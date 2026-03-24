# Project Details

This document provides a comprehensive breakdown of the versions, APIs, tools, frameworks, and data used in the Multi-Agent Trip Planner project.

## 📦 Frameworks & Libraries (Versions)

The following core libraries and frameworks are utilized in the project (as defined in `requirements.txt`):

- **Flask (`>=3.0.0`)**: The primary web framework used to serve the application, handle routing (`/`, `/plan`, `/result`), and render the UI.
- **python-dotenv (`>=1.0.0`)**: Used to load environment variables from the `.env` file (like API keys and Flask configurations).
- **LangChain (`>=0.2.0`) & langchain-core (`>=0.2.0`)**: Included to provide an interface for future LLM integration and advanced AI agent capabilities.
- **langchain-google-genai (`>=1.0.0`)**: Google GenAI integration for LangChain, allowing the system to interface with Google's Gemini models.
- **Pydantic (`>=2.0.0`)**: Utilized for rigorous data validation and schema definitions within the agent pipeline.

*Note: The project is designed to be easily adaptable to agentic frameworks like **CrewAI** in the future.*

## 🔌 APIs Used

- **Google Gemini API**: Configured via the `GOOGLE_API_KEY` in the `.env` environment file. It is prepared for use with the `langchain-google-genai` library to provide dynamic language model capabilities to the agents (though the base pipeline operates on local dummy data to ensure reliability).

## 🧰 Custom Tools (Agent Tools Layer)

The system relies on a dedicated tools layer instead of monolithic scripts. These tools are used by the agents to compute and retrieve information:

1. **`RecommendationTool`**
   - **Purpose**: Fetches destination data, filters available places based on user preferences, and validates if a destination is supported.
2. **`ItineraryTool`**
   - **Purpose**: Takes the recommended places and evenly distributes them across the user's requested number of days, handling overflows (cycling places) if there are more days than places.
3. **`BudgetTool`**
   - **Purpose**: Computes the estimated total cost of the trip (Days × Avg Cost per Day) and cross-checks it against the user's specified budget to determine budget feasibility.

## 🗄️ Data Storage

The application currently relies on a structured, local JSON dataset rather than an external database. 

- **File Source**: `data/destinations.json`
- **Structure**: A dictionary mapping destinations to their available places and average daily costs.
- **Supported Destinations & Details**:
  - **Goa**: ₹3,000 avg/day (Places: Baga Beach, Anjuna Beach, Fort Aguada, etc.)
  - **Manali**: ₹2,500 avg/day (Places: Solang Valley, Rohtang Pass, Hadimba Temple, etc.)
  - **Jaipur**: ₹2,000 avg/day (Places: Amber Fort, Hawa Mahal, City Palace, etc.)
  - **Kerala**: ₹3,500 avg/day (Places: Alleppey Backwaters, Munnar Hills, Kovalam Beach, etc.)
  - **Ladakh**: ₹4,000 avg/day (Places: Pangong Lake, Nubra Valley, Leh Palace, etc.)

## 🏗️ Multi-Agent Architecture

The core logic operates on a "Pure Python" Multi-Agent pipeline communicating via **Shared Memory**:

1. **`UserAgent`**: Collects and validates inputs.
2. **`RecommendationAgent`**: Uses the `RecommendationTool` to curate places.
3. **`PlannerAgent`**: Uses the `ItineraryTool` to build a day-by-day plan.
4. **`BudgetAgent`**: Uses the `BudgetTool` to analyze financial feasibility.
