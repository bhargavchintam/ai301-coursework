I contributed a fix to SMG, an open-source gateway that prepares requests for AI models. My change puts a chat's opening instructions in the expected place and avoids adding an empty message when there are no instructions or tools.

I compared the generated prompts with a reference implementation and added tests for the affected cases. During automated review, an edge case surfaced: two empty text fields could still create a blank message. I revised the check and added a test for that exact input.

All 23 targeted tests passed, and my pull request merged on November 9.

This work connects to my interest in reliable AI systems. A small change becomes easier to trust when the failing behavior is visible, the tests cover awkward inputs, and review feedback leads to a concrete improvement.

https://github.com/smg-project/smg/pull/5353
