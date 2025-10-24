# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Claude Quickstarts is a collection of production-ready starter applications demonstrating how to build with Claude. The repository contains full-stack applications (Next.js/TypeScript), Python demos (Docker/Streamlit), and educational agent implementations across different use cases.

## Repository Structure

### Full-Stack Applications (Next.js/TypeScript)

**`customer-support-agent/`** - Advanced customer support chat interface
- Next.js 14 with React and TypeScript
- Amazon Bedrock integration for RAG (knowledge base retrieval)
- Shadcn/ui components for highly customizable UI
- Real-time thinking & debug information display
- AWS Amplify deployment support
- Multiple UI variants (full, left sidebar only, right sidebar only, chat only)

**`financial-data-analyst/`** - Interactive financial data analysis with visualization
- Next.js 14 with React and TypeScript
- Multi-format file upload (CSV, PDF, images, text)
- Interactive chart generation (line, bar, area, pie, stacked charts)
- Recharts for data visualization
- Edge runtime with Anthropic SDK

### Python Applications

**`computer-use-demo/`** - Desktop computer control demonstration
- Python with Streamlit UI
- Docker-based environment
- Claude 3.5 Sonnet with computer use capabilities
- VNC server for remote desktop viewing
- Type checking with Pyright
- Comprehensive test suite

**`agents/`** - Minimal educational agent implementation
- Core agent loop implementation (<300 lines)
- Tool execution framework
- MCP (Model Context Protocol) server integration
- Reference implementation, not a production SDK
- Educational Jupyter notebook (`agent_demo.ipynb`)

## Development Environment Setup

### Prerequisites

**For Next.js Applications:**
- Node.js 18+
- npm or yarn

**For Python Applications:**
- Python 3.8+ (3.11+ recommended)
- Docker (for computer-use-demo)

### API Keys & Environment Variables

All projects require an Anthropic API key. Create environment files:

**Next.js projects** (`.env.local`):
```bash
ANTHROPIC_API_KEY=your_anthropic_api_key
```

**Python projects** (`.env`):
```bash
ANTHROPIC_API_KEY=your_anthropic_api_key
```

**Customer Support Agent** additionally requires:
```bash
BAWS_ACCESS_KEY_ID=your_aws_access_key
BAWS_SECRET_ACCESS_KEY=your_aws_secret_key
```
Note: The 'B' prefix is required because AWS Amplify doesn't allow environment variables starting with "AWS".

### Pre-commit Hooks

Install pre-commit hooks before making changes:

```bash
pip install pre-commit
pre-commit install
```

Pre-commit hooks run:
- YAML validation
- File ending fixes
- Trailing whitespace removal
- Ruff linting and formatting (Python projects only)
- Pyright type checking (computer-use-demo only)

## Common Development Commands

### Customer Support Agent

```bash
# Install dependencies
npm install

# Development servers
npm run dev           # Full UI with both sidebars
npm run dev:left      # Left sidebar only
npm run dev:right     # Right sidebar only
npm run dev:chat      # Chat interface only

# Production builds
npm run build         # Full UI
npm run build:left    # Left sidebar variant
npm run build:right   # Right sidebar variant
npm run build:chat    # Chat only variant

# Code quality
npm run lint          # ESLint
```

**UI Configuration:**
The sidebar configuration is controlled via environment variables:
- `NEXT_PUBLIC_INCLUDE_LEFT_SIDEBAR=true/false`
- `NEXT_PUBLIC_INCLUDE_RIGHT_SIDEBAR=true/false`

**Customization:**
- UI components: `components/ui/`
- Theme: `app/globals.css` and `styles/themes.js`
- Knowledge bases: Update `knowledgeBases` array in `ChatArea.tsx`
- Models: Update `models` array in `ChatArea.tsx`

### Financial Data Analyst

```bash
# Install dependencies
npm install

# Development
npm run dev           # Start dev server at localhost:3000

# Production
npm run build         # Build for production

# Code quality
npm run lint          # ESLint
```

**Supported File Formats:**
- Text/Code: .txt, .md, .html, .py, .csv
- PDF: Regular PDFs with text (not scanned documents)
- Images: Common formats for visual analysis

**Chart Types:**
Line, Bar, Multi-Bar, Area, Stacked Area, Pie charts based on data context.

### Computer Use Demo

```bash
# Setup environment
./setup.sh

# Build Docker image
docker build . -t computer-use-demo:local

# Run container
docker run \
  -e ANTHROPIC_API_KEY=$ANTHROPIC_API_KEY \
  -v $(pwd)/computer_use_demo:/home/computeruse/computer_use_demo/ \
  -v $HOME/.anthropic:/home/computeruse/.anthropic \
  -p 5900:5900 \
  -p 8501:8501 \
  -p 6080:6080 \
  -p 8080:8080 \
  -it computer-use-demo:local

# Code quality
ruff check .          # Lint Python code
ruff format .         # Format Python code
pyright               # Type checking

# Testing
pytest                                    # Run all tests
pytest tests/path_to_test.py::test_name -v  # Run specific test
```

**Ports:**
- 5900: VNC server
- 8501: Streamlit UI
- 6080: noVNC web interface
- 8080: HTTP server

### Agents (Educational Implementation)

```bash
# Install dependencies (from agents directory)
pip install anthropic mcp

# Set API key
export ANTHROPIC_API_KEY=your_api_key

# Run Jupyter notebook
jupyter notebook agent_demo.ipynb

# Use in Python
from agents.agent import Agent
from agents.tools.think import ThinkTool

agent = Agent(
    name="MyAgent",
    system="You are a helpful assistant.",
    tools=[ThinkTool()],
    mcp_servers=[...]
)
response = agent.run("Your query here")
```

## Code Standards

### TypeScript/Next.js Projects

- **Strict TypeScript mode** with proper interfaces and type annotations
- **Function components** with React hooks
- **ESLint** configuration for Next.js
- **Shadcn/ui** components library for UI
- **TailwindCSS** for styling
- **Edge runtime** where applicable for better performance

### Python Projects

**Code Style:**
- snake_case for functions/variables
- PascalCase for classes
- Type annotations for all parameters and returns
- Dataclasses and abstract base classes preferred
- Custom error types (e.g., ToolError for tool errors)

**Tools:**
- **ruff**: Linting and formatting
- **pyright**: Type checking (computer-use-demo)
- **pytest**: Testing framework
- **isort**: Import sorting with combine-as-imports

**Configuration:**
- Ruff config in `ruff.toml` or `pyproject.toml`
- Pyright config in `pyproject.toml`

## AWS Integration (Customer Support Agent)

### Amazon Bedrock Knowledge Base Setup

1. Go to AWS Console → Amazon Bedrock
2. Navigate to "Knowledge base" under "More"
3. Click "Create knowledge base"
4. Configure:
   - Name your knowledge base
   - Select data source (S3 recommended)
   - Choose embedding model (e.g., Titan Text Embeddings 2)
   - Select "Quick create a new vector store"
5. Get knowledge base ID from overview
6. Update `knowledgeBases` array in `ChatArea.tsx`:

```typescript
const knowledgeBases: KnowledgeBase[] = [
  { id: "your-knowledge-base-id", name: "Your KB Name" },
];
```

### AWS IAM Setup

1. Create IAM user with "AmazonBedrockFullAccess" policy
2. Create access key (Application running on AWS compute service)
3. Save Access Key ID and Secret Access Key
4. Add to `.env.local`

### AWS Amplify Deployment

1. AWS Console → Amplify → Create new app
2. Connect GitHub repository
3. Use provided `amplify.yml` configuration
4. Create/select service role with "AmazonBedrockFullAccess"
5. Add environment variables in Advanced settings
6. Deploy

**Service Role Configuration:**
After deployment, ensure the Amplify service role has "AmazonBedrockFullAccess" policy attached.

## Git Workflow

### Branch Naming
```bash
git checkout -b <type>/<description>
# Examples:
# feature/add-chart-type
# fix/authentication-bug
# docs/update-readme
```

### Commit Message Format
Use conventional commits:

```
<type>: <subject>

Types:
  feat     - New feature
  fix      - Bug fix
  docs     - Documentation
  style    - Formatting
  refactor - Code restructuring
  test     - Tests
  chore    - Maintenance
  ci       - CI/CD changes

Examples:
  feat: add pie chart visualization
  fix: resolve AWS Bedrock authentication issue
  docs: update setup instructions for Docker
```

## Testing & Quality Assurance

### Pre-commit Validation
Before every commit, hooks automatically:
1. Validate YAML files
2. Fix file endings
3. Remove trailing whitespace
4. Lint and format Python code (Python projects)
5. Run type checking (computer-use-demo)

### Manual Testing

**Next.js Applications:**
1. Run `npm run lint` before committing
2. Test all UI variants (if applicable)
3. Verify environment variables are properly set
4. Test with actual API keys in development

**Python Applications:**
1. Run `ruff check . --fix` before committing
2. Run `ruff format .` to ensure consistent formatting
3. Run `pyright` for type checking (computer-use-demo)
4. Run `pytest` to ensure tests pass
5. Test Docker builds and container runs (computer-use-demo)

## Common Patterns

### Adding a New Model to Next.js Applications

1. Update the `models` array in the chat component:
```typescript
const models: Model[] = [
  { id: "claude-3-haiku-20240307", name: "Claude 3 Haiku" },
  { id: "claude-3-5-sonnet-20241022", name: "Claude 3.5 Sonnet" },
  // Add new model here
];
```

2. Ensure the model is available in your Anthropic account
3. Update documentation if model has specific capabilities

### Adding a New Chart Type (Financial Data Analyst)

1. Define chart component in `components/` using Recharts
2. Update chart selection logic in API route
3. Add chart type to Claude's tool definitions
4. Test with appropriate data formats

### Adding a New Tool (Agents)

1. Create tool class in `agents/tools/`
2. Implement required methods:
   - `__init__()`: Initialize tool
   - `__call__()`: Execute tool logic
   - `definition`: Tool schema for Claude
3. Add tool to agent initialization:
```python
agent = Agent(tools=[YourNewTool(), ...])
```

### Adding MCP Server Integration (Agents)

1. Configure MCP server in agent initialization:
```python
agent = Agent(
    mcp_servers=[
        {
            "type": "stdio",
            "command": "python",
            "args": ["-m", "your_mcp_server"],
        }
    ]
)
```

2. Ensure MCP server is installed and accessible
3. Test tool availability after connection

## Project-Specific Architecture

### Customer Support Agent

**Key Components:**
- `app/`: Next.js app router pages
- `components/`: React components (UI and chat logic)
- `lib/`: Utility functions
- `styles/`: Theme configurations
- `tutorial/`: Documentation and screenshots

**Chat Flow:**
1. User sends message
2. System checks knowledge bases (Amazon Bedrock RAG)
3. Claude processes with context
4. Response includes sources and thinking process
5. UI displays formatted response

### Financial Data Analyst

**Key Components:**
- `app/`: Next.js app router with API routes
- `components/`: Chart components and UI elements
- `hooks/`: Custom React hooks
- `lib/`: PDF processing and utilities
- `types/`: TypeScript type definitions
- `utils/`: Helper functions

**Analysis Flow:**
1. User uploads file(s)
2. Files processed (text extraction, PDF parsing)
3. Claude analyzes data
4. Generates appropriate visualizations
5. Returns interactive charts and insights

### Computer Use Demo

**Key Components:**
- `computer_use_demo/`: Main application code
  - `loop.py`: Core agent loop
  - `tools/`: Computer control tools (bash, editor, computer)
  - `streamlit.py`: UI implementation
- `image/`: Docker image configuration
- `tests/`: Test suite

**Execution Flow:**
1. User provides instruction via Streamlit UI
2. Claude determines required tools
3. Tools execute on desktop environment
4. Screenshots captured for verification
5. Results displayed in UI

### Agents (Educational)

**Key Components:**
- `agent.py`: Core agent implementation (<300 lines)
- `tools/`: Tool implementations
  - Native tools (think, etc.)
  - MCP tool wrappers
- `utils/`: Message history and MCP connections
- `agent_demo.ipynb`: Interactive tutorial

**Agent Loop:**
1. Receive user input
2. Claude processes and selects tools
3. Execute tools (native or MCP)
4. Claude processes tool results
5. Generate response or continue loop
6. Return final answer

## Known Issues & Important Notes

### Customer Support Agent

1. **AWS Keys**: Must prefix with 'B' in Amplify environment variables
2. **Knowledge Base**: Must be in same AWS region as Bedrock access
3. **Model Availability**: Check region-specific Claude model availability
4. **Sidebar Variants**: Build separately for different UI configurations

### Financial Data Analyst

1. **PDF Support**: Only regular PDFs with text; scanned documents not supported
2. **File Size**: Large files may timeout; recommend <10MB
3. **Chart Selection**: Claude determines chart type based on data structure
4. **Image Analysis**: Works best with clear, high-contrast images

### Computer Use Demo

1. **Docker Required**: Must have Docker installed and running
2. **Resource Intensive**: Requires significant memory and CPU
3. **VNC Access**: May need VNC client for best experience
4. **Security**: Not intended for production use; runs with elevated permissions
5. **Beta Feature**: Computer use is a beta feature and may change

### Agents

1. **Educational Purpose**: This is a reference implementation, not production-ready
2. **Error Handling**: Minimal error handling; add robustly for production
3. **Rate Limiting**: No built-in rate limiting
4. **State Management**: Basic message history; extend for complex conversations
5. **MCP Servers**: Requires properly configured MCP servers to be running

## Resources

### Documentation
- [Claude API Documentation](https://docs.claude.com)
- [Next.js Documentation](https://nextjs.org/docs)
- [Amazon Bedrock Documentation](https://docs.aws.amazon.com/bedrock/)
- [Shadcn/ui Documentation](https://ui.shadcn.com)
- [Recharts Documentation](https://recharts.org)
- [MCP Documentation](https://modelcontextprotocol.io/)

### Related Resources
- [Claude Cookbooks](https://github.com/anthropics/claude-cookbooks) - Code snippets and guides
- [Claude API Fundamentals Course](https://github.com/anthropics/courses)
- [Building Effective Agents](https://www.anthropic.com/engineering/building-effective-agents)

### Support
- [Anthropic Support](https://support.anthropic.com)
- [Anthropic Discord](https://www.anthropic.com/discord)
- [GitHub Issues](https://github.com/anthropics/claude-quickstarts/issues)

## Important Disclaimers

**Production Readiness:**
- These quickstarts are prototypes provided "as-is"
- Not intended for production use without significant hardening
- No warranties or guarantees provided
- Use at your own risk

**Security:**
- Never commit API keys or secrets
- Use environment variables for sensitive data
- Review AWS IAM policies for least privilege
- Computer use demo should not be exposed to untrusted users

**Compliance:**
- Ensure compliance with data privacy regulations
- Review AWS and Anthropic terms of service
- Implement appropriate logging and monitoring for production
