# Team Huntrix Final Presentation Script

**Talk length:** approximately 9 to 10 minutes  
**Format:** 12 slides, four presenters  
**Deck:** `Huntrix_Final_Presentation.pptx`

The paper follows the ACL/EMNLP author-year citation convention. The presentation uses short numbered source markers so citations remain readable on screen; the full source map is at the end of this script.

## Presenter allocation

| Presenter | Slides | Approximate time |
|---|---:|---:|
| Touhidul Alam Seyam | 1-3 | 2 min 35 sec |
| MD. Abtahee Kabir | 4-6 | 2 min 50 sec |
| Joyeta Barua Moni | 7-9 | 2 min 50 sec |
| Noore Tamanna Orny | 10-12 | 2 min 40 sec |

The individual slide targets add up to 10 minutes and 5 seconds. In rehearsal, aim for 9 minutes and 30 seconds to leave room for a brief pause during handoffs.

## Slide 1: Private rank 1 came from changing the decision path

**Presenter:** Touhidul Alam Seyam  
**Target:** 45 seconds

Good morning. We are Team Huntrix. Our final Phase 1 system scored 0.982 on the private leaderboard and finished first. The public score was 0.967. The main reason was not a larger model or a large fine-tune. We changed which method was allowed to answer each kind of question. Exact cases went through deterministic evidence. Only the uncertain remainder went to the language model. This talk explains that decision, what failed before it, and how we packaged the result for Phase 2.

## Slide 2: One binary label hid many different reasoning tasks

**Presenter:** Touhidul Alam Seyam  
**Target:** 50 seconds

The label looked simple: zero for hallucinated and one for faithful. The rows were not simple. Some asked whether a response followed a supplied passage. Others needed arithmetic, option parsing, Bengali morphology, idiom meaning, legal sections, or a historical fact. One generic judge prompt had to switch between all of these without being told which evidence standard to use. It gave us a useful baseline, but it also created a ceiling. A response could sound reasonable while answering the wrong relation. That pushed us toward task routing.

## Slide 3: The breakthrough was a selective cascade

**Presenter:** Touhidul Alam Seyam  
**Target:** 60 seconds

Our fix was a selective cascade. We normalize the row, identify its task family, and try only the rules that belong to that family. Exact lineage can decide an exact source match. Structured solvers can decide a parsed number or option. Context checks can decide a contradiction inside the supplied passage. Typed evidence can decide an idiom or legal relation. Each stage can also return nothing. That matters. If the source is only similar, or the relation is unclear, we do not force a label. The row reaches the judge.

**Handoff:** Abtahee will show how much work that router removed and how we controlled the remaining evidence.

## Slide 4: Deterministic routing handled 93.7% of Phase 1

**Presenter:** MD. Abtahee Kabir  
**Target:** 50 seconds

The final router resolved 2,358 of the 2,516 Phase 1 rows before LLM inference. That is 93.72 percent. The judge saw 158 unique rows, or 6.28 percent. Earlier in development the uncertain queue was 620, so the deterministic layers removed almost three quarters of the expensive tail. This did more than save time. It also reduced the number of rows exposed to prompt variation. One detail: 158 is a row count. Some no-context rows received two or three views, so the number of model requests was higher than 158.

## Slide 5: Evidence could decide only when the relation matched

**Presenter:** MD. Abtahee Kabir  
**Target:** 60 seconds

Coverage alone would have been dangerous, so we imposed an evidence order. A contradiction inside the supplied context came first. Exact source lineage followed. Structured solvers needed the value, entity, and requested predicate to agree. Idiom, legal, and public fact records were typed by relation. A year near the right entity was not enough if the prompt asked about a different event. Fuzzy retrieval had the weakest role. It could surface a candidate, but it could not decide against stronger evidence. If two equally trusted sources disagreed, the router abstained. That policy sometimes increased the judge queue, but it prevented confident relation swaps.

## Slide 6: Qwen judged 158 rows, not the whole test set

**Presenter:** MD. Abtahee Kabir  
**Target:** 60 seconds

The fallback used Qwen3.6 27B in a 16.332 GiB quantized checkpoint. Context rows received one entailment view. No-context rows received up to three short views: a factual check, strict exam grading, and a check that was deliberately cautious about faithful labels. A no-context response needed unanimous positive votes to receive label one. The first zero ended evaluation early. We disabled thinking and constrained generation to one binary token. Row order, prompt order, seeds, and batch boundaries were fixed. That made the judge fast enough to be a tail component rather than the whole system.

**Handoff:** Joyeta will show how this architecture changed the score and what we learned from the failed experiments.

## Slide 7: Routing produced the largest score jump

**Presenter:** Joyeta Barua Moni  
**Target:** 55 seconds

This score history changed how we thought about the problem. The early character and embedding baseline scored 0.657. Gemma reached 0.742, and a Qwen judge reached 0.797. The largest single jump came at v6, when source and structured routing raised the score to 0.903. After that, progress was smaller and more forensic. We tightened provenance, repaired relation checks, and added legal and idiom evidence. v85 reached 0.966. v87 is the small dip on the right. We reverted it. v91 finished at 0.967 publicly.

## Slide 8: Several reasonable ideas made the score worse

**Presenter:** Joyeta Barua Moni  
**Target:** 60 seconds

Some of the most useful experiments were failures. Prompt voting looked decent on the released set and scored only 0.783 publicly. The compact TF-IDF verifier improved out of fold but struggled with cultural facts that had no context. Raw QA retrieval often found a passage about the right entity but the wrong relation. v87 was the clearest warning. We changed two labels using facts that looked plausible, and the public score fell from 0.966 to 0.965. We reverted those edits. From that point, a source was not enough. Every accepted record also needed the exact predicate the question asked for.

## Slide 9: The final result held across local, public, and private views

**Presenter:** Joyeta Barua Moni  
**Target:** 55 seconds

We kept the evaluation views separate. On the 299 released labels, the full cascade reached 0.9933 macro F1. The deterministic subset covered 225 of those rows without an error. The public leaderboard score was 0.967 at rank five. The final private score was 0.982 at rank one. That private result tells us the conservative policy generalized better than the public slice suggested. It does not tell us that every individual v91 rule will survive Phase 2. The source mix is broader, so we treat the private score as evidence, not a guarantee.

**Handoff:** Orny will cover reproducibility, runtime, and the Phase 2 plan.

## Slide 10: Three full runs reproduced the same CSV under 10 minutes

**Presenter:** Noore Tamanna Orny  
**Target:** 55 seconds

We ran the frozen notebook three times on Kaggle with two T4 GPUs. The complete 2,516-row runs took 440.87, 527.03, and 593.43 seconds. All three produced the same CSV bytes and the same SHA-256 hash as the scored submission. We reported 0.17 hours, or 612 seconds, as a conservative form value. A 5,000-row same-mix test took 14.60 minutes. We also estimated the pessimistic case where every row needs all three judge views. That stays around 5.2 to 5.5 hours, below the nine-hour limit.

## Slide 11: Phase 2 widens the source mix, so abstention matters more

**Presenter:** Noore Tamanna Orny  
**Target:** 60 seconds

Phase 2 changes the source distribution. The organizer listed Common Crawl, recent newspapers, Wikipedia, Banglapedia, government service pages, Bangladesh law, NCTB textbooks, and literature. Exact Phase 1 lineage will naturally cover less. The router handles this by failing closed. It first tries context clauses, structured solvers, and exact entity-relation checks. If those checks fail, the row skips lineage and reaches the same fixed judge. Lower deterministic coverage will increase runtime, but it should not make approximate retrieval more powerful. Our all-uncertain timing gives us room for that shift. Accuracy is still the open question, which is why abstention remains important.

## Slide 12: Be exact when possible. Abstain when necessary.

**Presenter:** Noore Tamanna Orny  
**Target:** 45 seconds

We will close with three lessons. First, this benchmark is a mixture of tasks, and those tasks need different evidence standards. Second, a related passage is not enough. The entity and the requested relation must match. Third, selective judging made the system faster and steadier because the LLM handled only the unresolved tail. That combination produced a private score of 0.982, reproduced the scored CSV offline, and stayed within the Phase 2 runtime limit. We are ready for your questions.

## Likely questions

### Why did you not fine-tune a classifier or the judge?

The released set had only 299 labeled rows, while Phase 2 deliberately changes the source distribution. Fine-tuning on that small set would make it easy to learn Phase 1 artifacts. Routing gave us a larger gain, preserved an interpretable abstention path, and kept inference inside the Kaggle limit.

### Why Qwen3.6-27B?

It is an open multilingual model that handled Bengali factual and entailment prompts well in our local checks. The 16.332 GiB Q4_K_P checkpoint fits the two-T4 environment, and the short constrained output keeps the fallback practical.

### Did exact-source matching amount to manually labeling the test set?

No. We did not manually assign labels to test rows, use hidden labels, or call paid or closed APIs. The rules apply typed relations to public or competition-permitted evidence. A source match is allowed to decide only when the entity, value, and requested predicate agree; otherwise the system abstains.

### Did the judge process 158 requests?

It processed 158 unique rows. Context rows use one view. Some no-context rows use two or three short views, with early exit on the first zero, so the request count is higher than the row count.

### How does the system handle Phase 2 sources it has never seen?

Unknown sources do not receive approximate deterministic labels. Context checks and general structured solvers still run, but lineage rules abstain unless their exact conditions are met. The unresolved row then reaches the fixed LLM judge.

### Why are the public and private scores different?

The public leaderboard evaluated only part of the test set. The private score came from the remaining held-out portion. We report both aggregate scores, but we do not infer row-level labels or error patterns from the leaderboard.

## Source map

1. Bengali LLM Hallucination Detection Challenge, data description, leaderboard, and rules: <https://www.kaggle.com/competitions/bengali-hallucination>
2. Organizer Phase 2 source preview: <https://www.kaggle.com/competitions/bengali-hallucination/discussion/719835>
3. *BenHalluEval: A Multi-Task Hallucination Evaluation Framework for Large Language Models on Bengali*: <https://arxiv.org/abs/2605.31483>
4. *When Words Don't Mean What They Say: Figurative Understanding in Bengali Idioms*: <https://arxiv.org/abs/2602.12921>
5. Qwen3.6-27B model card: <https://huggingface.co/Qwen/Qwen3.6-27B-FP8>
6. llama.cpp: <https://github.com/ggml-org/llama.cpp>
7. Team Huntrix experiment log, frozen manifest, deterministic trace, Kaggle runtime records, and final leaderboard records in this repository.

