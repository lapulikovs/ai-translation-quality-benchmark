# AI Translation Quality Benchmark

On 60 English→Russian segments, an LLM judge agreed with my pass/fail verdicts 90% of the time and caught two errors I missed. Most of our disagreements were about severity, which a style guide should narrow.

## The question

Can an LLM judge stand in for a human quality reviewer on English→Russian MT?

## Setup

- **Data:** 60 segments (1,370 source words) from FLORES+ (devtest split), CC BY-SA 4.0. No system saw the references.
- **Locale:** en → ru-RU
- **Systems**, all run October 1, 2026:
  - Claude Sonnet 5.5, desktop app, incognito chat
  - ChatGPT GPT-5.6, web app, incognito chat
  - DeepL free web translator, default settings
- **Controls:** same prompt for both LLMs, segment IDs kept, raw outputs frozen, shuffled scoring order.
- **Scoring:** I scored all 180 outputs myself, with 15+ years in localization quality. MQM-style typology: Accuracy, Fluency, Style, Terminology, Locale convention. Weights: minor 1, major 5, critical 25. Any major or critical error fails the segment.

## Results

| System | Errors per 100 words | Critical | Major | Minor |
| --- | --- | --- | --- | --- |
| ChatGPT (GPT-5.6) | 1.61 | 0 | 8 | 14 |
| Claude (Sonnet 5.5) | 1.82 | 1 | 5 | 19 |
| DeepL (free web) | 1.82 | 0 | 7 | 18 |

The three systems scored close together. Severity-weighted penalty per 100 words: DeepL 3.87, ChatGPT 3.94, Claude 5.04 (driven by one truncated sentence).

## Where the judge and I disagreed

The judge was Claude Sonnet 5.5, run October 3, 2026, using the prompt in `judge_prompt.md`.

- **Verdict agreement:** 90% (penalty correlation 0.61).
- **Recall on failures:** 3 of 21 (14%).
- **Severity:** the judge flagged 26 errors and none were critical. I flagged 72, including 1 critical and 20 major. The judge caught the truncated sentence but rated it major.
- **What it missed:** number agreement ("женщины" for "the woman"), tense, a mistranslated term ("animal pests"), a translated company name.
- **Preference flags:** 4, all minor.
- **What it caught that I missed:** 2 errors: a dropped "so far" and Jimmy Wales's surname rendered as "Уэльс".
- **Pattern:** the judge never failed a segment I passed, but it was sometimes strict on wording. Of the 26 segments where our scores differed, 11 came down to severity, mainly over unit conversion. The judge also missed 9 meaning errors outright.

## What this means in practice

An LLM reviewer is useful today as a second pair of eyes. It flags real issues, including ones a human misses, and when it fails a segment it is right.

Most of the gap is a guidelines problem. Neither the systems nor the judge had a style guide or a locale brief. Without guidelines, any reviewer, human or AI, is guessing at severity. My next step is to give the judge a style guide and measure how much of the severity gap closes.

One thing guidelines won't fix is the meaning errors the judge missed, such as number agreement and tense. Until its recall improves, a human should still review Accuracy, and a PASS from the judge shouldn't be enough to ship.

## Limitations

- One scorer, so there's no inter-annotator agreement.
- 60 segments and one language pair.
- No style guide or locale brief for the systems or the judge, so severity on context-dependent issues (units, register) is a judgment call.
- The judge is the same model as one of the systems it scored.
- FLORES+ is public, so some systems may have seen it in training.
- Consumer chat interfaces; results hold as of the run dates.

## Files in this repo

- `judge_prompt.md`: the judge prompt
- `raw_outputs.csv`: sources and all three systems' outputs
- `scoring.csv`: my segment-level scores
- `judge_scores.csv` and `judge_errors.csv`: the judge's verdicts and the errors it flagged
- `compare.csv`: my scores and the judge's side by side, with a cause for each gap
- `compare.csv`: my scores and the judge's side by side, with a cause for each gap
