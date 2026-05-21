# Simple Text-to-SQL: Clinical Trials Chat

A lightweight chatbot for querying clinical trial data using plain English. Ask questions about trials without writing SQL—the AI handles the database queries for you.

## What It Does

This is a simplified version of a text-to-SQL system focused on quick natural language queries. Load your clinical trial data, ask questions in English, and get results back instantly through a chat interface.

## Key Features

- **Natural Language Queries**: Type questions like "Show top 5 sponsors" or "How many studies by phase?"
- **Pluggable LLM Support**: Use OpenAI, Gemini, Ollama, or Groq
- **Conversation Context**: Maintains chat history with automatic summarization for long conversations
- **User Feedback**: Rate helpful/unhelpful responses to improve results
- **Lightweight**: Uses in-memory SQLite for fast performance
- **Easy to Run**: Single notebook with minimal setup

## Quick Start

### 1. Install Dependencies

```bash
pip install pandas sqlalchemy llama-index llama-index-core
pip install llama-index-llms-openai llama-index-llms-gemini
pip install llama-index-llms-ollama llama-index-llms-groq
pip install llama-index-embeddings-huggingface
pip install gradio python-dotenv
```

### 2. Configure Environment

Create a `.env` file:
```
OPENAI_API_KEY=your_key
GEMINI_API_KEY=your_key
GROQ_API_KEY=your_key
HUGGINGFACE_API_KEY=your_key
```

### 3. Update Data Path

Change this line to point to your CSV:
```python
input_folder = r"G:\Your\Path\Here"
csv_path = os.path.join(input_folder, "aact_multiple_sponsors.csv")
```

### 4. Choose Your LLM

```python
LLM_PROVIDER = "groq"  # Options: "openai", "gemini", "ollama", "groq"
```

### 5. Run It

Execute all cells in the notebook. The Gradio interface will launch at `http://localhost:7860`

## How It Works

### Data Loading
- Reads CSV with pipe-delimited format
- Creates an in-memory SQLite database
- Extracts table schema for the LLM

### Query Processing
1. You ask a question in the chat
2. LLM reads the question + conversation context
3. LLM generates appropriate SQL query
4. Query executes against the database
5. Results displayed in the chat

### Conversation Management
- Keeps recent conversation history available
- After 10+ messages, summarizes older conversations
- Maintains context while keeping memory efficient
- Allows you to rate responses (thumbs up/down)

## Data Requirements

Your CSV should contain clinical trial data with columns like:
- `NCT_ID` or `studyid`: Unique trial identifier
- `brief_title`: Study name
- `enrollment`: Number of participants
- `phase`: Trial phase
- `sponsor_name`: Funding organization
- `overall_status`: Trial status (Active, Recruiting, Completed, etc.)
- `conditions`: Medical conditions studied

## Query Examples

```
"What is the total count of trials?"
"Top 5 sponsors with most studies?"
"Count of studies for Novartis by overall status"
"Status of study JZP598-302 and enrollment"
"How many recruiting vs completed trials?"
"Which conditions have the most studies?"
```

## Code Structure

### Core Components

**initialize_llm()**
- Abstracts LLM selection
- Handles API key loading
- Configurable model and temperature

**answer_query()**
- Main query processing function
- Manages conversation context
- Includes error handling

**Gradio Interface**
- Simple chat UI
- Clear button to reset
- Feedback mechanism (like/dislike)

## Differences from text2sql_agent_ai.ipynb

| Feature | Simple_Text2SQL | text2sql_agent_ai |
|---------|-----------------|-------------------|
| Chat Interface | ✅ Gradio | ✅ Gradio |
| Auto Visualization | ❌ | ✅ Charts |
| Data Insights | ❌ | ✅ LLM Analysis |
| Conversation History | ✅ With Summarization | ✅ | 
| Query Pipeline | Basic | Advanced Multi-threaded |
| Configuration | Simple | Comprehensive |
| File Size | ~2KB notebook | ~10KB notebook |

## Troubleshooting

**"API Key not found"**
- Check `.env` file is in the same directory
- Make sure you're using `load_dotenv(override=True)`

**"No results returned"**
- Check column names in your CSV match the schema
- Try a simpler query first (e.g., "Show me 5 rows")

**"Slow response time"**
- Check your LLM provider (Groq is fastest)
- Reduce conversation history with the Clear button
- Trim old rows from CSV if it's very large

## Tips for Best Results

- **Be specific**: "Top 10 active recruiting trials in Phase 3" beats "Show trials"
- **Reference exact values**: Use actual sponsor names or NCT IDs from your data
- **Clear chat periodically**: Keeps conversations fresh and focused
- **Start simple**: Test with basic queries before complex ones

## Performance Notes

- In-memory SQLite: Great for datasets < 2GB
- Best with 10K-100K rows of trial data
- Response time: 1-3 seconds depending on query complexity and LLM
- Works well for exploratory analysis and quick lookups

## Limitations

- No multi-table joins (single table only)
- Chart generation not supported (use text2sql_agent_ai for that)
- LLM accuracy depends on data quality
- Conversation summarization may lose fine details
- No persistent chat history (cleared when notebook restarts)

## Next Steps

- Extend with multiple tables using table retrieval
- Add result caching for repeated queries
- Export results to CSV/JSON
- Build dashboards with results
- Deploy as web service

## License

This project is provided as-is for research and educational purposes.
