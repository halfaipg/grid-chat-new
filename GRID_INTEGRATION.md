# Grid Models Integration for Open WebUI

This document explains how the AI Power Grid models integration works in Open WebUI.

## Overview

The Grid models integration allows Open WebUI to dynamically load and use models from the AI Power Grid API. This integration:

1. Fetches available models from the Grid API in real-time
2. Integrates them into Open WebUI's model picker dropdown
3. Handles chat completions through the Grid API
4. Provides caching for performance

## Architecture

### Components

1. **Grid Models Provider** (`backend/open_webui/routers/grid_models.py`)
   - Fetches models and workers from Grid API
   - Handles caching (30-second cache duration)
   - Provides chat completion endpoint

2. **Model Loading Integration** (`backend/open_webui/utils/models.py`)
   - Integrates Grid models into Open WebUI's model system
   - Fetches models alongside OpenAI and Ollama models

3. **Chat Completion Routing** (`backend/open_webui/utils/chat.py`)
   - Routes Grid model requests to the Grid API
   - Handles response formatting

### API Endpoints

- `GET /api/v1/models/grid` - Get available Grid models
- `GET /api/v1/models/grid/refresh` - Force refresh Grid models cache
- `GET /api/v1/models/grid/workers` - Get available Grid workers
- `POST /api/v1/chat/completions/grid` - Handle Grid chat completions

## Configuration

The Grid API is configured with:
- **API Key**: `SRjE7PuHTW1SG4rd_AqYGg`
- **Base URL**: `https://api.aipowergrid.io`
- **Cache Duration**: 30 seconds

## Model Format

Grid models are formatted as:
```json
{
  "id": "grid_model_name",
  "name": "model_name",
  "object": "model",
  "created": 1234567890,
  "owned_by": "grid",
  "connection_type": "remote",
  "tags": [],
  "meta": {
    "description": "Grid model: model_name",
    "queue_length": 0.0,
    "eta": 0,
    "performance": "0",
    "worker_count": 1
  }
}
```

## Usage

1. **Model Selection**: Grid models appear in the model picker dropdown with the prefix "grid_"
2. **Chat Completion**: When a Grid model is selected, chat requests are automatically routed to the Grid API
3. **Response Format**: Responses are formatted to be compatible with Open WebUI's frontend

## Testing

Run the test script to verify the integration:
```bash
python3 test_grid_integration.py
```

## Error Handling

- Network errors are logged and cached models are returned if available
- API errors are handled gracefully with appropriate HTTP status codes
- Timeout handling for long-running generations (5 minutes max)

## Performance

- Models are cached for 30 seconds to reduce API calls
- Async/await pattern for non-blocking operations
- Connection pooling with aiohttp

## Security

- API key is embedded in the backend (should be moved to environment variables in production)
- No sensitive data is exposed to the frontend
- Request validation and sanitization

## Future Improvements

1. **Environment Variables**: Move API key to environment variables
2. **Streaming Support**: Add streaming response support
3. **Model Configuration**: Allow per-model parameter configuration
4. **Error Recovery**: Implement retry logic for failed requests
5. **Monitoring**: Add metrics and monitoring for Grid API usage 