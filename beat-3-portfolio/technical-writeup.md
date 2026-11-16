# Fixing how SMG renders instructions in Harmony chat prompts

## Overview

I contributed a Rust fix to SMG, an open-source gateway that prepares and routes requests to language models. Its gRPC chat path for gpt-oss placed a request's leading instructions differently from the vLLM reference and added an empty developer message to requests without instructions or tools. I changed the prompt builder, added four tests against reference prompts, and revised an empty-content edge case after automated review. My two-commit [pull request, SMG #5353](https://github.com/smg-project/smg/pull/5353), merged on November 9, 2026.

## What happened

### Turning the reported mismatch into failing tests

My work focuses on ML systems, including model serving. For this contribution, the observable behavior was the prompt prepared before generation. Harmony is the structured message format used by gpt-oss, so the placement and presence of messages are things a test can inspect directly.

[Issue #2830](https://github.com/smg-project/smg/issues/2830) described differences between SMG and vLLM. I addressed its first two cases. A client's leading system message appeared as another system message after the builder's own messages, whereas vLLM put that text in the developer message's instructions. A request without instructions or tools also received an empty developer message.

I added four tests comparing rendered prompts with vLLM's Harmony fixtures at revision `7b77ed58`. They covered requests without instructions or tools, leading system or developer instructions, instructions alongside tools, and tools without instructions. The tools-only case was a positive control: it already passed on main.

The following selected lines are from my [recorded baseline run](https://github.com/smg-project/smg/pull/5353), using main's builder at `d934ca87` with the new tests added. Assertion traces are omitted.

```text
$ cargo test --locked -p smg --lib routers::grpc::harmony::builder
test routers::grpc::harmony::builder::tests::a_chat_request_with_tools_keeps_its_developer_message ... ok
test routers::grpc::harmony::builder::tests::a_leading_system_or_developer_message_becomes_the_developer_instructions ... FAILED
test routers::grpc::harmony::builder::tests::leading_instructions_come_before_the_tools_in_one_developer_message ... FAILED
test routers::grpc::harmony::builder::tests::a_chat_request_without_instructions_or_tools_has_no_developer_message ... FAILED
test result: FAILED. 20 passed; 3 failed; 0 ignored; 0 measured; 4217 filtered out; finished in 0.11s
```

Three new tests failed, while the tools-only control passed. This gave me concrete expected and actual prompts to investigate. These checks ran on an Apple M4 with macOS 27.2 and Rust 1.98.0; they were prompt-rendering tests.

### Implementing the first fix

In [commit e6af46da](https://github.com/smg-project/smg/commit/e6af46da3c98ea1f1471b1d45ae42575c45d1339), I added `split_instructions` to extract a leading system or developer message. The builder passed those instructions to the shared developer-message construction code and converted the remaining messages as conversation content.

I also changed when the builder adds a developer message: it now requires instructions or a non-empty tool list. This preserved the message when it carried tool definitions and omitted it when it carried nothing. I kept the channel-list difference outside the patch because that was a separate issue with parser implications.

### Revising my own implementation after review

My first version still had a gap. CodeRabbit, the automated reviewer, [flagged the handling of empty text parts](https://github.com/smg-project/smg/pull/5353#discussion_r4505520129):

> Check the content before joining instruction parts.

Two empty text parts were joined with a space. My original check inspected the resulting string, so that single space counted as non-empty instructions and produced a blank developer message. An empty-string test alone did not cover this representation.

I changed the check to call `has_text()` before joining the parts and added a regression case containing two empty text parts. The actual patch in my [second commit, fa2eae6b](https://github.com/smg-project/smg/commit/fa2eae6b1aa6949931bd624ac18f1ccae7fdc574), shows both changes:

```diff
@@ -379,10 +379,7 @@ fn split_instructions(messages: &[ChatMessage]) -> (Option<String>, &[ChatMessag
         Some((
             ChatMessage::System { content, .. } | ChatMessage::Developer { content, .. },
             rest,
-        )) => {
-            let instructions = content.to_simple_string();
-            ((!instructions.is_empty()).then_some(instructions), rest)
-        }
+        )) => (content.has_text().then(|| content.to_simple_string()), rest),
         _ => (None, messages),
     }
 }
@@ -2068,6 +2065,17 @@ mod tests {
                 json!({"model": "gpt-oss-120b", "messages": [{"role": "system", "content": ""}, user]}),
                 without_commentary(SIMPLE_USER),
             ),
+            (
+                "a system message of empty text parts",
+                json!({"model": "gpt-oss-120b", "messages": [
+                    {"role": "system", "content": [
+                        {"type": "text", "text": ""},
+                        {"type": "text", "text": ""}
+                    ]},
+                    user
+                ]}),
+                without_commentary(SIMPLE_USER),
+            ),
             // A tool list, even an empty one, keeps the commentary channel.
             (
                 "an empty tool list",
```

I [replied in the review thread](https://github.com/smg-project/smg/pull/5353#discussion_r4510080534) that the new case failed before this change with a blank developer message. This was a second version of my own work, triggered by a specific review finding.

### Checking the revised behavior

The targeted tests passed after the changes. These selected lines come from the run recorded in the same PR:

```text
$ cargo test --locked -p smg --lib routers::grpc::harmony::builder
test routers::grpc::harmony::builder::tests::chat_tools_are_read_wherever_the_request_declares_them ... ok
test routers::grpc::harmony::builder::tests::leading_instructions_come_before_the_tools_in_one_developer_message ... ok
test routers::grpc::harmony::builder::tests::a_chat_request_with_tools_keeps_its_developer_message ... ok
test routers::grpc::harmony::builder::tests::a_leading_system_or_developer_message_becomes_the_developer_instructions ... ok
test routers::grpc::harmony::builder::tests::a_chat_request_without_instructions_or_tools_has_no_developer_message ... ok
test result: ok. 23 passed; 0 failed; 0 ignored; 0 measured; 4217 filtered out; finished in 0.08s
```

The checks established the expected rendering for the covered leading-instruction and empty-content cases, while retaining developer messages needed for tools. I also documented three library-test failures and an allocation-count integration failure from the wider local run that reproduced on main. The 23 passing targeted tests are the result relevant to this fix.

### The result that landed

The PR merged with both authored commits. The verified merge record is:

```text
PR: smg-project/smg#5353
Author: bhargavchintam
Merged: true
Merged at: 2026-11-09T15:22:24Z
Merge commit: fc664f714ca61245a6dec9c0d12fdc471c1e5be7
```

The accepted change makes SMG's covered chat cases handle leading instructions and empty developer messages consistently with the reference behavior. The empty-parts case illustrates why checking a final string can miss a problem introduced by the conversion itself. The review became more useful when I turned its finding into a test that would fail if that mistake returned.

## Limitations and what's next

I validated rendered prompts; I did not run live gpt-oss inference for this contribution or measure changes in answer quality or latency. The tests also do not establish complete parity with vLLM. For no-tools fixtures, the existing `commentary` channel difference was normalized in the comparison. Later system or developer messages and the broader multipart-text joining behavior remained outside this fix.

My next step would be a separate integration check with pinned model and tokenizer revisions and fixed generation settings. I would first compare the prepared prompts and token IDs, then evaluate instruction-following behavior on representative requests with and without tools. That would extend the evidence from prompt construction to the model-serving path.

I used AI assistance to organize and draft this account from the linked PR, review thread, commits, and recorded test results.
