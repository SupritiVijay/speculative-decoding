# speculative-decoding
Speculative decoding in LLMs

## Notes
1. Smaller draft model (q) --> suggests next k tokens or sequences
2. Larger target model (p) --> verifies these tokens and does a forward pass on only them in parallel
3. Criterion for accepting smaller model's suggestion is that p(x) >= q(x) --> which means that the smaller model should always be underconfident in its suggestion or equally confident, so that the larger model is more confident in the smaller model's suggestion
4. If p(x) < q(x), we reject this suggestion 1 - p(x)/q(x) times. Which means we accept it p(x)/q(x) times. Now, the formula is prob_acceptance * q(x) for this token being accepted. Since prob_acceptance = p(x)/q(x), this gets nullified and we are left with just p(x), which is what we wanted to emulate - the larger model's probability distribution.

Eg:
Smaller model generates 3 tokens after the phrase "happy birthday", the small model generates "to, you, buddy".
The large model now runs in parallel on:
- "happy birthday" → to verify "to"
- "happy birthday to" → to verify "you"
- "happy birthday to you" → to verify "buddy"
- "happy birthday to you buddy" → to generate the potential next token (this is instinctive)

If "you" gets rejected:
- It already has the probability distribution of the token for the input "Happy birthday to". During the "you" verification forward pass, it already has the probability distribution of the correct token (predicted by the larger model). That fits right in, and the process continues.
