+++
title = "Security as Intent Alignment: Lessons from Vitalik's Framework"
date = 2026-03-19T12:00:00+08:00
draft = false
math = false
+++

Vitalik Buterin recently shared a concise yet profound framework for thinking about security. At its core, the argument is deceptively simple: **security is about minimizing the divergence between the user's intent and the actual behavior of the system**. But the implications ripple far beyond what that one sentence might suggest.

This post distills and extends the key ideas from his thread.

## I. Security and UX Are the Same Problem

Under this definition, "user experience" and "security" collapse into a single spectrum. Both ask the same question: *does the system do what the user actually wanted?*

The distinction lies in where on the risk curve you focus:

- **UX** handles the common case — frictionless, intuitive interactions.
- **Security** handles the tail risk — situations where the divergence is large, and especially where that divergence is *adversarially induced*.

This framing is powerful because it dissolves the false trade-off between security and usability. A system that forces the user through 10 confirmation dialogs is not "more secure" — it simply shifts the failure mode from technical exploit to user fatigue. Real security makes low-risk actions easy and dangerous actions hard.

## II. Perfect Security Is Impossible — And Not Because of Bugs

The deeper insight is *why* perfection is unattainable. It's not because software has bugs, or because humans make mistakes. It's because **the user's intent is a fundamentally complex object that the user themselves cannot fully articulate**.

Consider a seemingly trivial intent: *"I want to send 1 ETH to Bob."*

- "Bob" is a meatspace entity that cannot be mathematically defined. You can approximate Bob with a public key or an ENS name, but the mapping from the real Bob to that identifier is itself a trust assumption.
- "ETH" seems unambiguous — until a contentious hard fork splits the chain, and "which ETH" becomes subjective.
- The user has intuitions about these things — what we loosely call "common sense" — but common sense resists formalization.

The problem compounds with more abstract goals. "Preserve my privacy" sounds clear enough, but:

- Is encrypting message content sufficient? What about metadata — who talks to whom, when, and how often?
- What constitutes a "trivial" privacy leak versus a "catastrophic" one?
- The answer depends on threat models the user may not even be aware of.

Anyone familiar with early AI alignment research — particularly Yudkowsky's work — will recognize this pattern: **robustly specifying goals is itself one of the hardest problems**. Security, in this light, is a special case of the alignment problem.

## III. The Core Pattern: Redundant, Multi-Angle Specification of Intent

If intent can never be perfectly captured by a single input, the natural solution is **redundancy from multiple angles**. Good security designs share a common structure: the user (or developer) specifies their intention in *multiple, overlapping ways*, and the system only acts when these specifications are consistent with each other.

Vitalik offers a catalog of examples:

| Mechanism | Angle 1 | Angle 2 |
|---|---|---|
| **Type systems** | The code (what the program does) | Type annotations (what shape data has at each step) |
| **Formal verification** | The code | Mathematical properties the code must satisfy |
| **Transaction simulations** | The action the user wants to take | The simulated on-chain consequences |
| **Post-assertions** | The transaction's actions | The expected effects declared in the same tx |
| **Multisig / social recovery** | Key A | Key B, Key C, ... |
| **Spending limits & confirmations** | The action | A re-confirmation that the action is intentionally unusual |

The pattern: there is no perfection, only **risk reduction through redundancy**. And the redundancy is most valuable when different specifications approach the user's intent from genuinely different angles — action vs. expected consequence, economic bound vs. social confirmation, code vs. mathematical property.

## IV. Where LLMs Fit In

This framework also provides a principled way to think about LLMs in security systems.

A generic LLM functions as a rough simulation of human common sense — a "shadow" of how a typical person would interpret a situation. A user-fine-tuned LLM is a more personalized shadow, capable of distinguishing what is normal vs. unusual *for that specific user*.

The key constraint: **LLMs should never be the sole determiner of intent**. But they are a valid *additional angle* — and a particularly valuable one, because the way an LLM approximates intent is fundamentally different from traditional explicit encodings (type checks, assertions, formal proofs). That difference is precisely what maximizes the value of the redundancy.

In other words, LLMs are useful in security not because they are reliable (they are not), but because they are *differently unreliable* from every other mechanism.

## V. The Anti-Pattern: More Clicks ≠ More Security

A direct corollary of the intent-alignment framing is that security does **not** mean making the user do more work for everything. If every action requires three confirmations, users learn to click through blindly, and the confirmation degrades from signal to noise.

The correct design principle:

- **Low-risk actions** should be easy, ideally automated.
- **High-risk or unusual actions** should require explicit, deliberate re-specification.

The challenge is calibrating the boundary — and that calibration itself is a function of the user's intent, which brings us full circle.

## VI. Takeaway

Security, once framed as intent alignment, stops being a checklist of countermeasures and becomes a design philosophy:

1. Accept that intent is complex and never fully capturable.
2. Build systems that require the user to express intent from multiple independent angles.
3. Only act when those angles converge.
4. Make the cost of interaction proportional to the risk of the action.
5. Treat every new "angle" — including imperfect ones like LLMs — as valuable, so long as it is *differently imperfect* from the others.

The most secure system is not the one with the most walls. It's the one that best understands what you're trying to do — and catches the cases where its understanding diverges from yours.
