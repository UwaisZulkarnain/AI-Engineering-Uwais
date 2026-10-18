# Week 1 Project Scope: WaTT

| Field | Details |
|---|---|
| **Project Name** | WaTT |
| **Domain** | Household energy and consumer utilities: domestic electricity billing in Peninsular Malaysia (TNB) |
| **Problem Statement** | Since July 2025, a domestic TNB bill is built from several parts: energy, capacity and network charges, a retail charge, service tax, and an Automatic Fuel Adjustment (AFA) that changes every month. On top of that, relief rules shift at short notice. The exemption threshold moved from 600 kWh to 800 kWh for September to December 2026 and reverts on 1 January 2027. When a bill rises, most households cannot tell whether the cause is their usage, the AFA, or crossing a threshold. The answer exists, but it is spread across tariff schedules, press releases and the bill itself. WaTT is a bilingual (BM and English) assistant that answers "why did my bill change?" by grounding every explanation in official tariff documents and computing every figure with a deterministic calculator, so the model explains but never guesses numbers. |

## Evidence of the Problem

**Evidence 1:** AFA flipped from rebates to surcharges in 2026. Customers received rebates for nine months (August 2025 to April 2026); surcharges began in May 2026 after generation costs rose following the West Asia crisis, reaching +3.67 sen/kWh in September 2026. TNB issued a press release specifically to address bill concerns.
Source: TNB press release, "Addressing Electricity Bill Concerns: AFA Mechanism Ensures Transparency and Fair Cost Sharing Since July 2025", 15 September 2026. https://www.tnb.com.my/assets/press_releases/20260915_23_bi.pdf

**Evidence 2:** The rules move faster than people can follow. On 15 September 2026, TNB stated the exemption threshold as 600 kWh. Two days later, the government raised it to 800 kWh for September to December 2026, with credits applied retroactively on October bills. By 15 October 2026, about 8 million Peninsular households were covered, while households above 800 kWh remain fully exposed to AFA. National media ran explainers asking why bills were suddenly higher.
Sources:
- Bernama, TNB bill adjustment for users up to 800 kWh, 2 October 2026. https://www.bernama.com/bm/news.php?id=2614501
- The Vibes, DPM on 8 million households protected, 15 October 2026. https://www.thevibes.com/articles/news/128374/global-fuel-shock-drives-electricity-costs-but-8-million-malaysian-households-remain-protected-dpm-ii
- Malay Mail, "Why is my TNB electricity bill suddenly high? The AFA fuel surcharge explained", 21 July 2026. https://www.malaymail.com/news/malaysia/2026/07/21/why-is-my-tnb-electricity-bill-suddenly-high-the-afa-fuel-surcharge-explained/227856

## Initial Related Work

**1. TNB itemised bill format (since July 2025)**
Source: Malay Mail, "New TNB itemised electricity bills will help consumers better understand, manage energy use", 1 July 2025. https://www.malaymail.com/news/malaysia/2025/07/01/new-tnb-itemised-electricity-bills-will-help-consumers-better-understand-manage-energy-use/182364
Key limitation: The bill lists each charge but does not explain why the total changed from last month, and cannot answer follow up questions.

**2. Third party bill calculators (e.g. Paul Tan TNB Bill Calculator)**
Source: https://paultan.org/tnb-calculator/
Key limitation: Computes an estimate, but the user must find and enter the month's AFA rate manually. It excludes the Time of Use (ToU) tariff, does not explain what AFA is, and gives no clear last updated date.

**3. RAG chatbot for electricity complaints at PLN, Indonesia (Putro et al., 2025)**
Source: I. P. H. Putro et al., "Retrieval Augmented Generation (RAG) Chatbot for Handling Customer Complaints in the Energy Sector", *Jurnal Infomedia*, 10(2), 105 to 111, 2025. https://e-jurnal.pnl.ac.id/infomedia/article/view/7169
Key limitation: Shows that retrieval in a local language works for a utility (92% of answers rated helpful), but it is grounded in past complaint records rather than tariff rules. It was evaluated with BLEU and ROUGE, which measure wording overlap and cannot catch a wrong ringgit amount.

**4. General purpose AI assistants (ChatGPT, Claude)**
Source: Own observation, Week 1 Lab Part A (Claude.ai, Figure A1.3).
Key limitation: Their knowledge has a fixed cutoff (Claude reported about June 2026), so without live search they cannot know the September 2026 threshold change. They also compute arithmetic inside the model, with no guarantee the numbers are right.

## Engineering Gap

Each existing approach solves one part of the problem. Calculators compute but do not explain. Bills itemise but do not show causes. General chatbots explain but go out of date and can get numbers wrong. Utility RAG chatbots prove the pattern but do not handle rules that change by date or verify amounts.

**Gap:** No accessible tool combines retrieval over official tariff documents filtered by billing period, verified arithmetic, and plain BM and English explanations of month to month bill changes.

## Initial Project Objectives

- **O1:** To design a bilingual, document grounded assistant that explains Peninsular Malaysia domestic electricity bills using official tariff documents tagged with their effective dates.
- **O2:** To develop the assistant with date aware retrieval, a deterministic bill calculator tool, and guardrails for source citation, applicable period and personal data.
- **O3:** To evaluate the assistant on calculation accuracy against reference bills, groundedness of explanations, BM and English answer parity, and response latency.

## Target Users

Domestic TNB customers in Peninsular Malaysia, especially households near the 600 or 800 kWh threshold, whose bills swing the most when rules change. Secondary users are family members who read and manage bills for elderly parents.

## Data Sources

| # | Document | URL |
|---|---|---|
| 1 | Suruhanjaya Tenaga media statement and RP4 tariff schedule (June 2025): tariff components and rates | https://www.st.gov.my/en/contents/files/highlights/nocategory/2025-06-20/FINAL2_Kenyataan%20Media%20-%20Jadual%20Tarif%20RP4_Finale%20(1).pdf |
| 2 | TNB press releases on AFA rates and bill relief (e.g. 15 September 2026, 2 October 2026) | https://www.tnb.com.my/assets/press_releases/20260915_23_bi.pdf and https://www.tnb.com.my/assets/press_releases/20261002_25_bi.pdf |
| 3 | TNB bill format and service tax pages | https://www.tnb.com.my/residential/billing/understand-your-bill/current-bill-format and https://www.tnb.com.my/sst |

Documents are downloaded for local course use only. The repository stores a source list (URL, retrieval date, effective date), not copies of the PDFs.

## Comparison with EduAdvisor

**Similar to EduAdvisor**
- Document grounded question answering over official PDFs
- Retrieval with source citations
- Guardrails on scope
- Evaluated on groundedness and latency

**Different from EduAdvisor**
- **Numbers, not just text:** answers include ringgit amounts, so a calculator tool must produce them. Retrieval alone is not enough.
- **Rules change with time:** AFA changes monthly and thresholds are temporary, so retrieval must filter by billing month. A correct rule for the wrong month is a wrong answer.
- **Bilingual:** answers in BM and English, with answer quality measured in both languages.
- **Wider user group:** the general public, many with low technical or financial literacy, rather than university students.

## Responsible AI Risks

**Risk 1: Wrong period.** The system applies a rule from the wrong month, for example the 800 kWh relief after it reverts on 1 January 2027. Households then dispute a correct bill or budget for the wrong amount.
Control: tag every document with its effective dates and state the applicable period in every answer.

**Risk 2: Invented numbers.** The model states a charge or total it computed itself. A wrong figure stated confidently misleads users who cannot check it.
Control: all amounts come from the calculator tool, and the working is shown.

**Risk 3: Unequal quality across languages.** BM answers are less accurate or less clear than English ones, so the households most likely to need help get worse help.
Control: run the same test set in both languages and report the gap.

**Risk 4: Personal data.** Users paste or upload bills containing name, address and account number, which falls under the PDPA.
Control: do not store uploads, redact identifiers before processing, and warn users before upload.

**Risk 5: Mistaken authority.** Users treat WaTT as an official TNB channel and rely on it in a billing dispute.
Control: a clear "not affiliated with TNB" notice, and disputes are always referred to official TNB channels.
