# Wazuh LLM Assistant

A natural language interface for Wazuh SIEM using LangChain and OpenAI function calling.

## Overview

This project implements an intelligent assistant that allows security analysts to query Wazuh SIEM data using natural language. The system converts user queries into structured function calls, executes them against the Wazuh OpenSearch backend, and returns human-readable security insights.

## Architecture

```
User Query → LLM Function Calling → Function Dispatcher → Wazuh Backend → Response Formatter → User
```

### Core Components

- **LangChain Agent**: Orchestrates conversation flow and tool usage
- **Function Tools**: 8 core security analysis functions with sub-actions
- **OpenSearch Client**: Direct integration with Wazuh's OpenSearch backend
- **FastAPI Server**: REST API for web interface integration
- **Pydantic Schemas**: Type-safe parameter validation

## Features

### 8 Core Security Intents

1. **analyze_alerts** - Alert analysis with ranking, filtering, counting, distribution
2. **investigate_entity** - Entity investigation (host, user, process, file)
3. **detect_threats** - MITRE ATT&CK techniques, tactics, threat actors
4. **map_relationships** - Entity relationships, access patterns, activity correlation
5. **find_anomalies** - Threshold, pattern, behavioral, trend detection
6. **trace_timeline** - Chronological event reconstruction
7. **check_vulnerabilities** - CVE checking and vulnerability assessment
8. **monitor_agents** - Agent status, health, and connectivity monitoring

### Example Queries

- "Show me the top 10 hosts with most alerts this week"
- "What alerts are there for user john.doe?"
- "Find T1055 process injection techniques detected recently"
- "Which users accessed host web-server-01 in the last 24 hours?"
- "Show me unusual login patterns from yesterday"
- "Check for Log4Shell vulnerabilities on our Windows hosts"
- "Which agents are disconnected right now?"

## Technology Stack

- **LangChain** - Agent orchestration and tool integration
- **OpenAI GPT-4** - Natural language understanding and function calling
- **OpenSearch Python Client** - Wazuh backend integration
- **FastAPI** - Async web framework
- **Pydantic** - Data validation and serialization
- **Redis** - Caching and session management
- **Structlog** - Structured logging
- **OpenTelemetry** - Observability and tracing

## Installation

### Prerequisites

- Python 3.9+
- OpenAI API key
- Access to Wazuh OpenSearch cluster
- Redis (optional, for caching)

### Setup

1. Clone the repository:
```bash
git clone https://github.com/yourusername/wazuh-llm-assistant.git
cd wazuh-llm-assistant
```

2. Install dependencies:
```bash
pip install -r requirements.txt
```

3. Configure environment variables:
```bash
cp .env.example .env
# Edit .env with your configuration
```

4. Start the service:
```bash
python main.py
```

## Configuration

### Environment Variables

```env
# OpenAI Configuration
OPENAI_API_KEY=your-openai-api-key

# OpenSearch Configuration
OPENSEARCH_HOST=your-opensearch-host
OPENSEARCH_PORT=9200
OPENSEARCH_USER=admin
OPENSEARCH_PASSWORD=your-password
OPENSEARCH_USE_SSL=true

# Redis Configuration (optional)
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_PASSWORD=

# Application Configuration
LOG_LEVEL=INFO
API_HOST=0.0.0.0
API_PORT=8000
```

## Usage

### REST API

Start the FastAPI server:
```bash
python main.py
```

Query the assistant:
```bash
curl -X POST "http://localhost:8000/query" \
  -H "Content-Type: application/json" \
  -d '{"query": "Show me critical alerts from the last hour"}'
```

### Python SDK

```python
from agent.wazuh_agent import WazuhSecurityAgent

agent = WazuhSecurityAgent(
    openai_api_key="your-api-key",
    opensearch_config={
        "host": "your-opensearch-host",
        "port": 9200,
        "auth": ("username", "password"),
        "use_ssl": True
    }
)

response = await agent.query("Show me the top 5 hosts with most alerts today")
print(response)
```

## Development

### Project Structure

```
wazuh-llm-assistant/
├── agent/                 # LangChain agent implementation
├── functions/             # Granular function modules
│   ├── analyze_alerts/    # Alert analysis functions
│   ├── investigate_entity/# Entity investigation functions
│   ├── map_relationships/ # Relationship mapping functions
│   ├── detect_threats/    # Threat detection functions
│   ├── find_anomalies/    # Anomaly detection functions
│   ├── trace_timeline/    # Timeline reconstruction functions
│   ├── check_vulnerabilities/ # Vulnerability checking functions
│   ├── monitor_agents/    # Agent monitoring functions
│   └── _shared/          # Shared utilities
├── tools/                # LangChain tool implementations
├── schemas/              # Pydantic schemas
├── tests/               # Test suite
└── docs/               # Documentation
```

### Adding New Functions

1. Create a new module in the appropriate `functions/` subdirectory
2. Implement the `execute()` function with proper parameters
3. Add the function to the corresponding LangChain tool
4. Update the schema if needed
5. Add tests

### Testing

Run the test suite:
```bash
pytest tests/
```

Run with coverage:
```bash
pytest --cov=. tests/
```

## Monitoring

### Observability

- **LangSmith**: Trace agent interactions and function calls
- **Structured Logging**: JSON-formatted logs with context
- **OpenTelemetry**: Distributed tracing support
- **Prometheus**: Metrics collection (optional)

### Health Checks

```bash
curl http://localhost:8000/health
```

## Security

### Best Practices

- Store API keys securely using environment variables
- Use HTTPS in production
- Implement rate limiting
- Validate all inputs through Pydantic schemas
- Monitor for unusual query patterns

### Authentication

The system supports JWT-based authentication. Configure your authentication provider in the environment variables.

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests
5. Submit a pull request

### Code Style

- Follow PEP 8
- Use type hints
- Write docstrings for all functions
- Add structured logging to new functions

## License

MIT License - see LICENSE file for details

## Support

For issues and questions:
- Create an issue on GitHub
- Check the documentation in `docs/`
- Review the example queries and use cases

## Roadmap

- [ ] Web interface for non-technical users
- [ ] Support for custom Wazuh rules
- [ ] Integration with external threat intelligence
- [ ] Multi-tenant support
- [ ] Advanced visualization capabilities
- [ ] Mobile app interface