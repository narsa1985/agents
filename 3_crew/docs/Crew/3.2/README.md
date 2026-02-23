# Healthcare CrewAI Documentation - Complete Guide

## Overview

This folder contains 7 comprehensive guides for implementing CrewAI multi-agent systems in healthcare. Each document includes complete, working code examples that you can run immediately.

## 📚 Documents

### 1. HC_CrewAI_Debate_System.md
**Build a clinical decision support system using debate agents**
- Complete project with 3 agents (clinical advocate, financial analyst, medical director)
- Real scenario: Evaluate telemedicine platform adoption
- Full YAML configs, Python code, and multiple use cases
- **Run time**: 2-3 minutes
- **Complexity**: Beginner

### 2. HC_Multi_Agent_Research_System.md
**Automate medical research with web search**
- 2-agent system (researcher + analyst) with SerperDev integration
- Real scenario: Research diabetes treatment options
- Includes PubMed API extension example
- **Run time**: 3-5 minutes
- **Complexity**: Beginner-Intermediate

### 3. HC_Structured_Outputs_Pydantic.md
**Extract structured patient data from clinical notes**
- Patient intake system with complex nested Pydantic models
- Real scenario: Convert unstructured notes to JSON for EHR
- Includes vitals, symptoms, medications, allergies
- **Run time**: 1-2 minutes
- **Complexity**: Intermediate

### 4. HC_Custom_Tools_Development.md
**Build custom tools for EHR integration and alerts**
- 4 custom tools: drug interaction checker, lab alerts, medication history, vitals monitor
- Real scenario: Clinical pharmacist safety checks
- Includes Pushover notification integration
- **Run time**: 2-3 minutes
- **Complexity**: Intermediate-Advanced

### 5. HC_Memory_Systems_RAG_SQL.md
**Maintain patient context across multiple visits**
- 3 memory types: short-term (recent visits), long-term (chronic conditions), entity (doctors/meds)
- Real scenario: 3 patient visits over 3 months
- Demonstrates continuity of care
- **Run time**: 5-7 minutes (3 visits)
- **Complexity**: Advanced

### 6. HC_Code_Execution_Agents.md
**Perform clinical analytics with code-writing agents**
- Agent writes Python code to analyze lab results
- Real scenarios: Glucose analysis, BP monitoring, CHADS2 scores
- Includes Docker setup for safe execution
- **Run time**: 3-5 minutes
- **Complexity**: Advanced

### 7. HC_Engineering_Team_Multi_Agent.md
**Build complete healthcare applications with AI team**
- 4-agent team: architect, backend dev, frontend dev, tester
- Real scenario: Patient appointment scheduling system
- Generates working app with UI and tests
- **Run time**: 5-10 minutes
- **Complexity**: Advanced

## 🚀 Quick Start

### Prerequisites
```bash
# Install CrewAI
pip install crewai crewai-tools

# For specific features
pip install gradio  # For UIs
pip install chromadb  # For memory systems

# Install Docker (for code execution)
# Download from https://docker.com
```

### Environment Setup
Create `.env` file in each project:
```bash
OPENAI_API_KEY=your_key_here
ANTHROPIC_API_KEY=your_key_here  # Optional
SERPER_API_KEY=your_key_here  # For web search
PUSHOVER_USER_KEY=your_key_here  # For notifications
PUSHOVER_API_TOKEN=your_key_here
```

### Run Your First Project
```bash
# 1. Create project (example: debate system)
crew create crew healthcare_debate
cd healthcare_debate

# 2. Copy code from HC_CrewAI_Debate_System.md
#    - agents.yaml
#    - tasks.yaml
#    - crew.py
#    - main.py

# 3. Run
crew run
```

## 📖 Learning Path

### Beginner (Start Here)
1. **HC_CrewAI_Debate_System.md** - Learn basic multi-agent setup
2. **HC_Multi_Agent_Research_System.md** - Add tools and context flow
3. **HC_Structured_Outputs_Pydantic.md** - Master data validation

### Intermediate
4. **HC_Custom_Tools_Development.md** - Build custom integrations
5. **HC_Memory_Systems_RAG_SQL.md** - Add persistent memory

### Advanced
6. **HC_Code_Execution_Agents.md** - Enable code generation
7. **HC_Engineering_Team_Multi_Agent.md** - Build complete systems

## 🏥 Healthcare Use Cases by Document

### Clinical Decision Support
- **Debate System**: Treatment option evaluation, equipment purchases
- **Research System**: Evidence-based medicine, drug research

### Patient Care
- **Structured Outputs**: Patient intake, data extraction
- **Memory Systems**: Continuity of care, patient history
- **Custom Tools**: Medication safety, lab alerts

### Analytics & Reporting
- **Code Execution**: Lab analysis, quality metrics, risk scores
- **Engineering Team**: Custom analytics dashboards

### System Development
- **Engineering Team**: Appointment systems, EHR modules, patient portals

## 🔑 Key Concepts

### Agents
- Defined in `agents.yaml`
- Have role, goal, backstory, model
- Can have tools and memory

### Tasks
- Defined in `tasks.yaml`
- Have description, expected output, agent
- Can have context (from previous tasks)

### Tools
- Built-in: SerperDevTool (web search)
- Custom: Inherit from BaseTool
- Enable real-world actions

### Memory
- Short-term: Recent interactions (vector DB)
- Long-term: Permanent facts (SQL)
- Entity: People/places/things (vector DB)

### Code Execution
- Agents can write and run Python code
- Docker provides safe isolation
- Perfect for analytics and calculations

## 📊 Comparison Matrix

| Feature | Debate | Research | Structured | Tools | Memory | Code Exec | Eng Team |
|---------|--------|----------|------------|-------|--------|-----------|----------|
| Agents | 3 | 2 | 1 | 2 | 2 | 2 | 4 |
| Tools | ❌ | ✅ | ❌ | ✅✅✅✅ | ❌ | ❌ | ❌ |
| Memory | ❌ | ❌ | ❌ | ❌ | ✅✅✅ | ❌ | ❌ |
| Code Exec | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ | ✅ |
| Pydantic | ❌ | ❌ | ✅ | ✅ | ❌ | ❌ | ❌ |
| Complexity | ⭐ | ⭐⭐ | ⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |

## 🛠️ Common Issues & Solutions

### Issue: "No module named 'crewai'"
```bash
pip install crewai crewai-tools
```

### Issue: "OPENAI_API_KEY not found"
Create `.env` file in project root with your API key

### Issue: "Docker not found" (Code Execution)
Install Docker Desktop from https://docker.com

### Issue: "SerperDev API error" (Research)
Sign up for free API key at https://serper.dev

### Issue: Memory files not persisting
Check that `memory=True` is set in both crew and agents

## 📝 Code Templates

### Basic Agent (agents.yaml)
```yaml
agent_name:
  role: Role description
  goal: What agent should achieve with {variable}
  backstory: Context and expertise
  model: gpt-4o-mini
```

### Basic Task (tasks.yaml)
```yaml
task_name:
  description: What to do with {variable}
  expected_output: What format/content
  agent: agent_name
  context: [previous_task]  # Optional
  output_file: output/result.md  # Optional
```

### Agent with Tools (crew.py)
```python
@agent
def agent_name(self) -> Agent:
    return Agent(
        config=self.agents_config['agent_name'],
        tools=[SerperDevTool(), CustomTool()],
        memory=True,  # Optional
        allow_code_execution=True,  # Optional
        code_execution_mode="safe",  # Optional
        verbose=True
    )
```

## 🎯 Best Practices

### 1. Start Simple
- Begin with 2-3 agents
- Add features incrementally
- Test each component

### 2. Clear Instructions
- Be specific in task descriptions
- Use examples in prompts
- Define expected outputs clearly

### 3. Error Handling
- Set timeouts for code execution
- Implement retry logic
- Validate inputs with Pydantic

### 4. Security
- Use Docker for code execution
- Validate all inputs
- Implement access controls
- Audit log all actions

### 5. Healthcare Compliance
- Encrypt sensitive data
- Implement HIPAA controls
- Audit all patient data access
- Use secure API connections

## 📚 Additional Resources

- **CrewAI Docs**: https://docs.crewai.com
- **SerperDev**: https://serper.dev
- **Pydantic**: https://docs.pydantic.dev
- **Gradio**: https://gradio.app
- **Docker**: https://docker.com

## 🤝 Contributing

Found an issue or have an improvement?
- Test the code examples
- Report bugs with specific error messages
- Suggest healthcare use cases
- Share your implementations

## 📄 License

These examples are for educational purposes. Ensure HIPAA compliance and proper security measures before using in production healthcare environments.

---

**Last Updated**: February 2026
**CrewAI Version**: Latest
**Python Version**: 3.8+
