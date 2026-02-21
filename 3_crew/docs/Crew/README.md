# CrewAI Documentation Index

This documentation covers CrewAI multi-agent framework concepts extracted from the course transcript. Files are organized into healthcare-specific and general technical topics.

## Healthcare-Specific Topics (3.2/)

These topics have been adapted for Smart Healthcare AI Platform use cases:

1. **HC_CrewAI_Debate_System.md** - Multi-agent debate system for healthcare decision making
2. **HC_Multi_Agent_Research_System.md** - Medical research automation with specialized agents
3. **HC_Structured_Outputs_Pydantic.md** - Type-safe data extraction for patient records
4. **HC_Custom_Tools_Development.md** - Building custom tools for EHR integration
5. **HC_Memory_Systems_RAG_SQL.md** - Patient history and context management
6. **HC_Code_Execution_Agents.md** - Analytics and statistical computation agents
7. **HC_Engineering_Team_Multi_Agent.md** - Building healthcare software with AI teams

## General Technical Topics (General/)

These are framework concepts applicable across domains:

1. **CrewAI_Setup_and_Project_Structure.md** - Getting started with CrewAI
2. **Sequential_vs_Hierarchical_Process.md** - Workflow execution modes
3. **Web_Search_Integration_SerperDev.md** - Real-time web search for agents
4. **Context_Management_Between_Tasks.md** - Data flow between tasks
5. **Docker_Safe_Code_Execution.md** - Secure code execution environment

## Quick Start Guide

### 5-Step CrewAI Project Creation

1. **Create Project**: `crew create crew project_name`
2. **Define Agents**: Edit `config/agents.yaml` with roles, goals, backstories
3. **Define Tasks**: Edit `config/tasks.yaml` with descriptions, outputs, context
4. **Configure Crew**: Update `crew.py` with decorators and crew setup
5. **Run**: Set inputs in `main.py` and execute `crew run`

### Key Concepts

- **Agent**: Autonomous unit with role, goal, backstory, LLM, tools, memory
- **Task**: Assignment with description, expected output, assigned agent
- **Crew**: Team of agents and tasks with execution process
- **Context**: Output from one task passed to another
- **Tools**: Functions agents can call (web search, custom actions)
- **Memory**: Short-term (RAG), long-term (SQL), entity (RAG)

### Healthcare Platform Integration

CrewAI agents can integrate with:
- **EHR Systems**: Custom tools for patient data access
- **Medical Databases**: PubMed, FDA, clinical trials
- **Notification Systems**: Alerts for critical values
- **Analytics Services**: Statistical analysis and reporting
- **Decision Support**: Multi-perspective clinical decisions

## Learning Path

### Beginner
1. Start with CrewAI_Setup_and_Project_Structure.md
2. Build simple debate system (HC_CrewAI_Debate_System.md)
3. Learn context management (Context_Management_Between_Tasks.md)

### Intermediate
4. Add web search (Web_Search_Integration_SerperDev.md)
5. Implement structured outputs (HC_Structured_Outputs_Pydantic.md)
6. Build custom tools (HC_Custom_Tools_Development.md)

### Advanced
7. Implement memory systems (HC_Memory_Systems_RAG_SQL.md)
8. Enable code execution (HC_Code_Execution_Agents.md)
9. Build complete engineering team (HC_Engineering_Team_Multi_Agent.md)

## Interview Preparation

Each document includes:
- Simple explanations for quick understanding
- Real-world use cases and problem-solving
- Practical examples with code
- Interview Q&A sections
- Quick revision summaries

## Labs and Hands-On Practice

All healthcare-specific documents include:
- Lab objectives
- Step-by-step implementation
- Expected outcomes
- Healthcare-specific scenarios

## Additional Resources

- CrewAI Documentation: https://docs.crewai.com
- SerperDev API: https://serper.dev
- Docker Desktop: https://docker.com
- Pydantic Documentation: https://docs.pydantic.dev

## Notes

- All examples use minimal code for clarity
- Healthcare examples are simplified for learning
- Production systems require additional security, validation, and compliance measures
- HIPAA compliance considerations should be added for real healthcare deployments
