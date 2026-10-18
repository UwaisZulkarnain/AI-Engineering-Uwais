# WaTT

> **WaTT** explains Malaysian household electricity bills in BM and English. It grounds every answer in official tariff documents and computes every figure with a verified calculator.

*Independent student project for SAIA3113 AI Engineering, UTM. Not affiliated with or endorsed by Tenaga Nasional Berhad (TNB).*

## Problem Statement

Since July 2025, a domestic TNB bill combines energy, capacity, network and retail charges, service tax, and an Automatic Fuel Adjustment (AFA) that changes every month. On top of that, relief thresholds change at short notice. When a bill rises, households cannot easily tell whether the cause is usage, the AFA, or crossing a threshold.

General chatbots go out of date and can get the arithmetic wrong, while calculators give a number without explaining it. WaTT combines retrieval over dated official documents with a deterministic calculator, so it can explain the change correctly and show its working. The AI handles what rules cannot: free form questions in BM, English or a mix of both, matched to the right rule for the right billing month.

## Evidence of the Problem

- **AFA flipped from rebates to surcharges in 2026.** Customers had nine months of rebates, then surcharges from May 2026 after the West Asia crisis pushed up fuel costs, reaching +3.67 sen/kWh in September 2026. TNB issued a press release to address bill concerns ([TNB, 15 Sep 2026](https://www.tnb.com.my/assets/press_releases/20260915_23_bi.pdf)).
- **The relief rules move faster than people can follow.** The exemption threshold was 600 kWh on 15 September 2026, then raised to 800 kWh two days later for September to December 2026 ([Bernama, 2 Oct 2026](https://www.bernama.com/bm/news.php?id=2614501)). About 8 million households are now covered, while those above the threshold stay exposed ([The Vibes, 15 Oct 2026](https://www.thevibes.com/articles/news/128374/global-fuel-shock-drives-electricity-costs-but-8-million-malaysian-households-remain-protected-dpm-ii)).

## Initial Related Work / Existing Approaches

- **TNB itemised bill (since July 2025):** lists each charge but does not explain why the total changed or answer questions ([Malay Mail, 1 Jul 2025](https://www.malaymail.com/news/malaysia/2025/07/01/new-tnb-itemised-electricity-bills-will-help-consumers-better-understand-manage-energy-use/182364)).
- **Third party bill calculators:** compute an estimate, but the user must look up the month's AFA rate. ToU is excluded and nothing is explained ([Paul Tan TNB Bill Calculator](https://paultan.org/tnb-calculator/)).
- **RAG chatbot for PLN complaints (Putro et al., 2025):** shows retrieval in a local language works for a utility. However, it is grounded in complaint records rather than tariff rules, and BLEU and ROUGE scores cannot catch a wrong amount ([Jurnal Infomedia](https://e-jurnal.pnl.ac.id/infomedia/article/view/7169)).
- **General purpose AI assistants:** have a fixed knowledge cutoff and compute numbers inside the model. In our Week 1 lab, Claude reported a cutoff of about June 2026, before the September threshold change.

## Engineering Gap / Limitation

No accessible tool combines three things:
- retrieval over official tariff documents filtered by billing period
- verified arithmetic
- plain BM and English explanations of month to month bill changes

## Project Objectives

- **O1:** To design a bilingual, document grounded assistant that explains Peninsular Malaysia domestic electricity bills using official tariff documents tagged with their effective dates.
- **O2:** To develop the assistant with date aware retrieval, a deterministic bill calculator tool, and guardrails for source citation, applicable period and personal data.
- **O3:** To evaluate the assistant on calculation accuracy against reference bills, groundedness, BM and English answer parity, and latency.

## Target Users

- **Primary:** Domestic TNB customers in Peninsular Malaysia, especially households near the 600 or 800 kWh threshold.
- **Secondary:** Family members who manage bills for elderly parents.

## Data Sources

- **Suruhanjaya Tenaga RP4 tariff schedule and media statement (June 2025):** [st.gov.my](https://www.st.gov.my/en/contents/files/highlights/nocategory/2025-06-20/FINAL2_Kenyataan%20Media%20-%20Jadual%20Tarif%20RP4_Finale%20(1).pdf)
- **TNB press releases on AFA and bill relief:** [15 Sep 2026](https://www.tnb.com.my/assets/press_releases/20260915_23_bi.pdf), [2 Oct 2026](https://www.tnb.com.my/assets/press_releases/20261002_25_bi.pdf)
- **TNB bill format and service tax pages:** [bill format](https://www.tnb.com.my/residential/billing/understand-your-bill/current-bill-format), [SST](https://www.tnb.com.my/sst)

Source PDFs are not committed. `data/` will hold a source list with URL, retrieval date and effective date.

## Repository Structure

```
docs/         lab reports, scope, system maps
data/         source list and processed data (no raw copyrighted PDFs)
src/          application code
notebooks/    Colab experiments
evaluation/   test sets and results
prompts/      system and task prompts
```

## Weekly Progress

| Week | Focus | Deliverable | Status |
|------|-------|-------------|--------|
| W1 | System map + project scope | README + docs | ✅ |
