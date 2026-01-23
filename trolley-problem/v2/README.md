# Trolley Problem v2: Structured Output Testing

This directory contains an automated testing framework for evaluating AI models' ethical decision-making using the trolley problem, with structured JSON output and automated CSV logging.

## Overview

Version 2 of the trolley problem experiment introduces:

- **Structured Output**: AI models respond with structured JSON containing boolean decisions and justifications
- **Automated Testing**: Easy switching between different AI models via configuration
- **CSV Logging**: Automatic logging of results with timestamps and model metadata
- **Type Safety**: Pydantic validation ensures data integrity
- **Environment Management**: Secure API key handling via `.env` files

## What's Different from v1

- **v1**: Manual testing of 28 models with free-form responses
- **v2**: Automated testing with structured responses, easier to analyze and scale

## Files

- `trolley_problem_v2.ipynb` - Main Jupyter notebook for testing AI models
- `trolley_results_v2.csv` - CSV file containing test results (auto-generated)
- `README.md` - This documentation file

## Setup

### Prerequisites

1. **OpenRouter API Key**: Get one from [openrouter.ai/settings/keys](https://openrouter.ai/settings/keys)
2. **Python Packages**: Install required dependencies

### Installation

1. Copy the environment template:
   ```bash
   cp ../.env.example ../.env
   ```

2. Edit `../.env` and add your OpenRouter API key:
   ```
   OPENROUTER_API_KEY=your_api_key_here
   ```

3. Install dependencies:
   ```bash
   pip install openrouter pydantic python-dotenv
   ```

## Usage

1. **Open the notebook**:
   ```bash
   jupyter notebook trolley_problem_v2.ipynb
   ```

2. **Run setup cells** (1-2): Install packages and load API key

3. **Configure model** (Cell 3): Change `MODEL` variable to test different models
   - Examples: `"openai/gpt-4o"`, `"anthropic/claude-3-sonnet"`, `"google/gemini-1.5-pro"`
   - See [openrouter.ai/models](https://openrouter.ai/models) for available models

4. **Run the test** (Cells 4-8): Execute the API call and log results

5. **View results** (Cell 9): See formatted output of the model's decision

## Data Format

### CSV Output Format

The `trolley_results_v2.csv` file contains:

```csv
model,model_version,date,prompt,pulls_lever,justification
google/gemini-3-pro-preview,gemini-3-pro-preview,2026-01-23 14:34:05,"There is a runaway trolley...",True,"This decision is based on..."
```

### Structured Response Schema

AI models respond with JSON matching this Pydantic model:

```python
class TrolleyResponse(BaseModel):
    pulls_lever: bool      # True = pull lever, False = do nothing
    justification: str     # Explanation of decision
```

## Technical Details

### Structured Output Enforcement

- **JSON Schema**: Enforces exact response format via OpenRouter API
- **Pydantic Validation**: Type-safe parsing with error handling
- **Prompt Instructions**: Explicitly tells models about boolean meaning

### Error Handling

- **API Errors**: Catches OpenRouter API failures
- **Validation Errors**: Handles malformed responses
- **Environment Errors**: Clear messages for missing API keys

### Security

- API keys stored in `.env` files (excluded from git)
- Environment variables loaded securely
- No hardcoded credentials

## Analysis

To analyze results across multiple models:

1. Run the notebook with different `MODEL` values
2. Results accumulate in `trolley_results_v2.csv`
3. Use pandas or similar tools for analysis:

```python
import pandas as pd
df = pd.read_csv('trolley_results_v2.csv')
print(df.groupby('model')['pulls_lever'].value_counts())
```

## Troubleshooting

### Common Issues

1. **"OPENROUTER_API_KEY not found"**
   - Ensure `.env` file exists in parent directory
   - Check API key is correctly set
   - Restart Jupyter kernel after adding key

2. **"ModuleNotFoundError"**
   - Install required packages: `pip install openrouter pydantic python-dotenv`

3. **API Errors**
   - Check API key is valid
   - Verify model name exists in OpenRouter
   - Check account credits/billing

4. **Validation Errors**
   - Model may not support structured output
   - Try a different model
   - Check OpenRouter model compatibility

## Contributing

When adding new models or features:

1. Test with multiple models to ensure compatibility
2. Update this README if changing functionality
3. Ensure CSV format remains backward compatible
4. Test error handling with invalid inputs

## Related Files

- `../README.md` - Main project documentation
- `../trolley-problem/v1/` - Original manual testing approach
- `../.env.example` - Environment variable template