# ✈️ Multi-Agent Group Trip Planner

> Turn *"We should go somewhere"* into *"Here’s the plan – yes or no?"*

An intelligent, multi-agent pipeline designed to eliminate group trip planning friction. Given a budget, travel dates, origin, candidate cities, and group preferences, this system generates a concrete, realistic itinerary complete with flight estimates, budget breakdowns, and curated local tips.

---

## 📌 Problem Statement

How many times have you and your friends proposed a trip in a WhatsApp group that never made it out of the chat?

> *"Let’s go to Lisbon!"*  
> *"I want Rome!"*  
> *"I’m broke..."*  
> *(Conversation dies)*

Planning group trips manually is painful:
1. Search flights $\rightarrow$ **10 open tabs**
2. Check hotels $\rightarrow$ **15 more tabs**
3. Look up activities $\rightarrow$ **Endless blog browsing**
4. Cross-check everything with budgets $\rightarrow$ **30–40 minutes wasted, idea abandoned.**

This project automates the entire discovery and planning phase, producing a clean, ready-to-share trip proposal in seconds.

---

## 🤖 Why Agents?

Instead of stuffing a single prompt with dozens of complex instructions, this system leverages a **multi-agent architecture**. The task is naturally modular, meaning each agent acts as a specialized worker:

1. **Flight Search Specialist:** Focuses purely on estimating round-trip flight costs.
2. **Budget & Selection Specialist:** Evaluates hotel, food, and activity costs to pick the best option within budget.
3. **Local Travel Researcher:** Scrapes travel blogs and guides for non-generic, high-value local tips.
4. **Report Writer:** Synthesizes structured data into a human-readable, chat-ready summary.

### Advantages of this approach:
- **Modularity:** Swap out individual components (e.g., replace web search flight estimation with a live Skyscanner API) without breaking the rest of the pipeline.
- **Maintainability:** Easier to debug, inspect intermediate steps, and extend functionality.
- **Reliability:** Structured JSON data passing between agents prevents hallucinated or malformed outputs.

---

## 🏗️ System Architecture

The project uses a sequential 4-stage pipeline (`SequentialAgent` / `FullTripPlanningPipeline`):

    ┌────────────────────────────────────────────────────────┐
    │                      User Input                        │
    │  (Origin, Dates, Candidates, Budget, Preferences)      │
    └───────────────────────────┬────────────────────────────┘
                                │
                                ▼
    ┌────────────────────────────────────────────────────────┐
    │ 1. Flight Price Comparison Agent                       │
    │    Estimates round-trip flight costs for all cities    │
    └───────────────────────────┬────────────────────────────┘
                                │
                                ▼
    ┌────────────────────────────────────────────────────────┐
    │ 2. Travel & Budgeting Agent                            │
    │    • BudgetEstimatorAgent: Estimates stays/food/apps   │
    │    • CitySelectionAgent (Python Tool):                 │
    │      Filters & picks cheapest destination within budget│
    └───────────────────────────┬────────────────────────────┘
                                │
                                ▼
    ┌────────────────────────────────────────────────────────┐
    │ 3. Content Insights Agent                              │
    │    Extracts 5 daily local blog tips per travel date    │
    └───────────────────────────┬────────────────────────────┘
                                │
                                ▼
    ┌────────────────────────────────────────────────────────┐
    │ 4. Travel Report Agent                                 │
    │    Merges budget + tips into final Markdown report     │
    └───────────────────────────┬────────────────────────────┘
                                │
                                ▼
    ┌────────────────────────────────────────────────────────┐
    │              Final Ready-to-Share Proposal             │
    └────────────────────────────────────────────────────────┘

---

## 🛠️ Pipeline Breakdown

### 1. Flight Price Comparison Agent
* **Input:** Origin city, candidate destinations, dates.
* **Process:** Performs web searches to estimate round-trip flight costs.
* **Output:** Standardized JSON array with flight estimates per destination.

### 2. Travel & Budgeting Agent
* **Sub-components:**
  * `BudgetEstimatorAgent`: Uses search tools to estimate accommodation per night, daily food/transport, and specific activity costs (e.g., wine tasting, museum entries).
  * `CitySelectionAgent`: Executes a custom Python tool function that compares total estimated costs against the budget per person and selects the cheapest qualifying destination (`is_within_budget: true`).

### 3. Content Insights Agent
* **Input:** Selected city, dates, and preferences.
* **Process:** Queries travel blogs and local guides to generate 5 daily practical tips tailored to the group's interests.

### 4. Travel Report Agent
* **Input:** Final budget JSON + daily tips JSON.
* **Process:** Formats and merges the outputs into a clean Markdown trip report ready to copy-paste into group chats.

---

## 🚀 Example Demo Output

### Prompt Input:
> *"Plan a 3-day trip from 2025-11-21 to 2025-11-23 departing from Luxembourg. Candidate cities are Lisbon, Rome, and Madrid. Total budget is 800 euros per person. Preferences: wine tasting, museums, sightseeing."*

### Final Generated Report:

🏁 **FINAL TRAVEL REPORT:**
✈️ **3-Day Trip to Lisbon (Nov 21–23, 2025)**

🏖️ **Trip Summary & Budget Overview**
* **Destination:** Lisbon 🇵🇹
* **Travel Dates:** 2025-11-21 to 2025-11-23
* **Total Trip Estimate:** €685.00
* **Flight Cost:** €150.00
* **Accommodation:** €100.00/night (€300.00 total)
* **Budget Status:** ✅ Within Budget (Limit: €800.00/person)

📅 **Daily Itinerary & Cost Breakdown**
* **Day 1 (Nov 21):** Wine Tasting Tour — 14:00 (€50.00) | Total Daily Est: €210.00
* **Day 2 (Nov 22):** Wine Tasting Tour — 14:00 (€50.00) | Total Daily Est: €210.00
* **Day 3 (Nov 23):** Museum Visit — 10:00 (€25.00) | Total Daily Est: €175.00

💡 **Must-Know Daily Tips**
* **Nov 21:**
  * Explore the historic Alfama district and visit the Lisbon Cathedral (Sé de Lisboa).
  * Ascend to Castelo de São Jorge for panoramic city views.
  * Ride the iconic Tram 28 through Baixa and Alfama.
  * Sunset at Miradouro da Senhora do Monte.
* **Nov 22:**
  * Visit Jerónimos Monastery and Belém Tower (UNESCO World Heritage).
  * Get fresh custard tarts at the original Pastéis de Belém bakery.
  * Visit the National Tile Museum (Museu Nacional do Azulejo).
  * Guided wine tasting at Lisbon Winery paired with local cheeses.
* **Nov 23:**
  * Day trip to Sintra: visit Pena Palace and Quinta da Regaleira.
  * Hike to the Moorish Castle for sweeping natural park views.
  * Enjoy lunch in Sintra historic center before returning to Lisbon.

---

## 🔮 Future Roadmap

If I had more time, here is how I would expand this project into a full production app:

- [ ] **Group Voting & Poll Integration:** Generate poll links automatically so friends can vote on candidate cities before generating the final itinerary.
- [ ] **Interactive Refinement:** Allow users to tweak prompts dynamically (*"Lower budget by €100"*, *"Make it more museum-heavy"*) and re-run only affected pipeline sub-agents.
- [ ] **Live API Refinement:** Replace estimated search data with real-time flight (e.g., Skyscanner, Amadeus) and hotel (e.g., Booking.com) APIs.
- [ ] **Export Formats:** One-click exports to shareable web viewable links or single-page PDF reports.

---

## ⚙️ Setup & Installation

**1. Clone the repository**
```bash
git clone [https://github.com/gi9494/fraud_detection_kaggle_dataset.git](https://github.com/gi9494/fraud_detection_kaggle_dataset.git)
cd fraud_detection_kaggle_dataset
