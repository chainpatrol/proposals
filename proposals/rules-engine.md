# Rules Engine

## Problem

We need a way to define rules that can be used to detect scams and other malicious activities. The rules should be able to be defined in a way that is easy to understand and maintain.

## Solution

We propose a rules engine that allows us to define rules in TypeScript. The rules can then be compiled into a single JavaScript file that can be used in the browser.

```ts
import { rule, allow, block, args, screenshot } from "@chainpatrol/sdk";

export const VisualSimilarity = rule({
  name: "Check visual similarity",
  description: "Checks if the webpage is visually similar to the reference URL",
  handler: async (page) => {
    const referenceUrl = args.input.string("referenceUrl");
    const threshold = args.input.number("threshold", 0.9);

    const expectedScreenshot = await screenshot(referenceUrl);
    const actualScreenshot = await screenshot(page);

    const similarity = await expectedScreenshot.similarity(actualScreenshot);

    if (similarity < threshold) {
      return allow("Visual similarity is below threshold");
    } else {
      return block("Visual similarity is above threshold");
    }
  },
});
```
