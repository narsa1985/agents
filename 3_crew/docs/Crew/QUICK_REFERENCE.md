# CrewAI Quick Reference Card

## 5-Step Project Creation

```bash
# 1. Create
crew create crew project_name

# 2. Define Agents (config/agents.yaml)
agent_name:
  role: Role description
  goal: Objective
  backstory: Context
  model: gpt-4o-mini

# 3. Define Tasks (config/tasks.yaml)
task_name:
  description: What to do
  expected_output: Format
  agent: agent_name
  context: [previous_task]

# 4. Configure Crew (crew.py)
@agent
def agent_name(self) -> Agent:
    return Agent(config=self.agents_config['agent_name'])

@task
def task_name(self) -> Task:
    return Task(config=self.tasks_config['task_name'])

@crew
def crew(self) -> Crew:
    return Crew(agents=self.agents, tasks=self.tasks, process=Process.sequential)

# 5. Run
crew run
```

## Common Patterns

### Web Search Agent
```python
from crewai_tools import SerperDevTool

@agent
def researcher(self) -> Agent:
    return Agent(
        config=self.agents_config['researcher'],
        tools=[SerperDevTool()]
    )
```

### Structured Output
```python
from pydantic import BaseModel, Field

class OutputSchema(BaseModel):
    field: str = Field(description="Description")

@task
def task_name(self) -> Task:
    return Task(
        config=self.tasks_config['task_name'],
        output_pydantic=OutputSchema
    )
```

### Code Execution
```python
@agent
def coder(self) -> Agent:
    return Agent(
        config=self.agents_config['coder'],
        allow_code_execution=True,
        code_execution_mode="safe"
    )
```

### Memory
```python
from crewai.memory import ShortTermMemory, LongTermMemory, EntityMemory
from crewai.memory.storage.rag_storage import RAGStorage

short_term = ShortTermMemory(
    storage=RAGStorage(provider="openai", model="text-embedding-3-small")
)

@crew
def crew(self) -> Crew:
    return Crew(
        agents=self.agents,
        tasks=self.tasks,
        memory=True,
        short_term_memory=short_term
    )
```

### Custom Tool
```python
from crewai.tools import BaseTool
from pydantic import BaseModel, Field

class ToolInput(BaseModel):
    param: str = Field(description="Parameter description")

class CustomTool(BaseTool):
    name = "tool_name"
    description = "What the tool does"
    args_schema = ToolInput
    
    def _run(self, param: str) -> str:
        # Tool logic here
        return result
```

## Environment Variables

```bash
# .env file
OPENAI_API_KEY=your_key
ANTHROPIC_API_KEY=your_key
SERPER_API_KEY=your_key
```

## Process Types

```python
# Sequential (tasks run in order)
process=Process.sequential

# Hierarchical (manager assigns tasks)
process=Process.hierarchical
manager_llm="gpt-4"
```

## Context Flow

```yaml
task1:
  agent: agent1
  
task2:
  agent: agent2
  context: [task1]  # Gets task1 output

task3:
  agent: agent3
  context: [task1, task2]  # Gets both outputs
```

## Healthcare Examples

### Patient Analysis
```yaml
researcher:
  role: Medical Researcher
  goal: Research {condition} treatments

analyst:
  role: Clinical Analyst
  goal: Analyze treatment options
```

### Drug Interaction Check
```python
class DrugInteractionTool(BaseTool):
    name = "check_drug_interaction"
    
    def _run(self, drug1: str, drug2: str) -> str:
        return check_interaction_api(drug1, drug2)
```

### Patient Record Extraction
```python
class PatientInfo(BaseModel):
    name: str
    age: int
    chief_complaint: str

@task
def extract_info(self) -> Task:
    return Task(
        description="Extract patient information",
        output_pydantic=PatientInfo
    )
```

## Common Commands

```bash
# Create project
crew create crew project_name

# Run project
crew run

# Install dependencies
pip install crewai crewai-tools

# Install Docker (for safe code execution)
# Download from docker.com
```

## Debugging Tips

1. Set `verbose=True` in agents and crew
2. Check output files in `output/` directory
3. Verify API keys in `.env`
4. Review task descriptions for clarity
5. Check context flow between tasks
6. Examine error logs

## Model Options

```yaml
# OpenAI
model: gpt-4o-mini
model: gpt-4

# Anthropic
model: claude-3-7-sonnet-latest

# DeepSeek
model: deepseek-chat

# Local (with Ollama)
model: llama3.2
```

## File Structure

```
project_name/
├── .env
├── src/
│   └── project_name/
│       ├── config/
│       │   ├── agents.yaml
│       │   └── tasks.yaml
│       ├── tools/
│       │   └── custom_tool.py
│       ├── crew.py
│       └── main.py
└── output/
    └── results.md
```

## Interview Cheat Sheet

**Q: What is CrewAI?**
A: Framework for building multi-agent AI systems where specialized agents collaborate on tasks.

**Q: Sequential vs Hierarchical?**
A: Sequential = fixed order; Hierarchical = manager assigns tasks dynamically.

**Q: How to pass data between tasks?**
A: Use `context: [previous_task]` in task definition.

**Q: Why use Pydantic outputs?**
A: Ensures type safety, validation, and consistent data format.

**Q: How to make agents safe?**
A: Use Docker with `code_execution_mode="safe"`.

## Quick Links

- Docs: https://docs.crewai.com
- SerperDev: https://serper.dev
- Docker: https://docker.com
- Pydantic: https://docs.pydantic.dev
