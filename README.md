
# AI Translation Quality Benchmark
LLM vs. MT translation quality, scored with an MQM-style typology, plus an audit of an LLM judge.

On 60 English→Russian segments, an LLM judge agreed with my pass/fail verdicts 90% of the time but caught only 3 of the 21 segments I failed.

## The question

Can general-purpose LLMs match a dedicated MT engine on English→Russian? And can an LLM judge stand in for a human quality reviewer?

## Setup

- **Data:** 60 segments (1,370 source words) from FLORES+ (devtest split), CC BY-SA 4.0. No system saw the references.
- **Locale:** en → ru-RU
- **Systems**, all run October 1, 2026:
  - Claude Sonnet 5.5, desktop app, incognito chat
  - ChatGPT GPT-5.6, web app, incognito chat
  - DeepL free web translator, default settings
- **Controls:** same prompt for both LLMs, segment IDs kept, raw outputs frozen, scored in shuffled order.
- **Scoring:** I scored all 180 outputs myself, with 15+ years in localization quality. I used an MQM-style typology with five categories: Accuracy, Fluency, Style, Terminology, and Locale convention. Severity weights are minor 1, major 5, critical 25. A segment fails if it has any major or critical error.

## Results

| System | Errors per 100 words | Critical | Major | Minor |
| --- | --- | --- | --- | --- |
| ChatGPT (GPT-5.6) | 1.61 | 0 | 8 | 14 |
| Claude (Sonnet 5.5) | 1.82 | 1 | 5 | 19 |
| DeepL (free web) | 1.82 | 0 | 7 | 18 |

The three systems scored close together. Weighted by severity, the penalty per 100 words was DeepL 3.87, ChatGPT 3.94, and Claude 5.04. Claude's higher score comes from one critical error: a sentence cut off mid-clause.

## Where the LLM judge was wrong

The judge was Claude Sonnet 5.5, run October 3, 2026, using the prompt in `judge_prompt.md`.

- **Verdict agreement:** 90%, with a penalty correlation of 0.61. Inflated, since most segments pass.
- **Recall on failures:** 3 of 21 (14%).
- **Severity:** the judge flagged 26 errors and none were critical. I flagged 72, including 1 critical and 20 major. The judge caught the truncated sentence but rated it major.
- **What it missed:** a plural where the source is singular ("женщины" for "the woman"), tense agreement, "animal pests" rendered as "вредные животные", a company name that was translated instead of kept, and miles and feet left unconverted.
- **Preference flags:** 4, all minor.
- **What it caught that I missed:** 2 errors: a dropped "so far" and Jimmy Wales's surname rendered as "Уэльс".
- **Pattern:** the judge doesn't produce false alarms. It under-flags errors and under-rates their severity, especially meaning shifts and locale conventions.

## What this means in practice

An LLM judge works as a second reviewer that adds findings. It never failed a segment I passed. It doesn't work as a gate: at 14% recall, a PASS from the judge tells you little. A human should own severity calls and the Accuracy and Locale convention categories, which is where the judge missed the most.

## Limitations

- One scorer, so there's no inter-annotator agreement.
- 60 segments and one language pair.
- The judge is the same model as one of the systems it scored.
- FLORES+ is public, so some systems may have seen it in training.
- These results come from consumer chat interfaces as of the run dates. The models behind them can change without notice.

## Files in this repo

- `judge_prompt.md`: the judge prompt
- `raw_outputs.csv`: source segments and the outputs from all three systems
- `scoring.csv`: my segment-level scores
- `judge_scores.csv` and `judge_errors.csv`: the judge's verdicts and the errors it flagged
- `compare.csv`: my scores and the judge's side by side, with a cause for each gap
