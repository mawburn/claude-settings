# Claude Code Agent Best Practices Guide

**Last Updated: 2025-08-14**

## Agent Configuration Structure

### File Format

Claude Code agents are stored as Markdown files with YAML frontmatter containing configuration metadata.

```yaml
---
name: agent-identifier # Required: Unique identifier for the agent
description: When to use... # Required: Triggering conditions (max 400 chars)
model: opus # Optional: sonnet (default), opus, or haiku
tools: Tool1, Tool2 # Optional: Comma-separated list (inherits all if omitted)
color: blue # Optional: Visual indicator in UI
---
[System prompt content here]
```

### Storage Locations

- **Project-level**: `.claude/agents/` (takes precedence)
- **User-level**: `~/.claude/agents/`

### Key Components

#### 1. Name (Identifier)

- Use kebab-case format (e.g., `code-reviewer`, `test-engineer`)
- Be descriptive but concise
- Avoid generic names like `helper` or `assistant`
- Should clearly indicate the agent's specialty

#### 2. Description (whenToUse)

- Maximum 400 characters
- Must be crystal clear about triggering conditions
- Include phrases like "Use PROACTIVELY" or "MUST BE USED" for critical agents
- Specify what the agent does NOT handle
- Example patterns:
  - "Use for X when Y. NOT for Z."
  - "Specializes in A. Skip if B."
  - "Use BEFORE/AFTER specific action."

#### 3. Model Selection

- **haiku**: Fast, lightweight tasks (code exploration, simple analysis)
- **sonnet**: Balanced performance (most common choice)
- **opus**: Complex reasoning, critical tasks (architecture, testing)

#### 4. Tools Configuration

- Omit to inherit all available tools (default)
- Restrict to specific tools for focused agents
- Common tool combinations:
  - Analysis: `Glob, Grep, LS, Read`
  - Writing: `Read, Edit, MultiEdit, Write`
  - Testing: `Read, Edit, Write, Bash`
  - Documentation: `Read, Write, WebSearch`

---

## System Prompt Structure and Requirements

### Core Structure Pattern

```markdown
You are a [ROLE] specializing in [DOMAIN]. Your expertise lies in [SPECIFIC CAPABILITIES].

## Core Responsibilities

[Numbered list of 3-5 primary responsibilities]

## Methodology

[Step-by-step approach to tasks]

## Quality Criteria

[Specific, measurable standards]

## DO

- [Explicit positive behaviors]
- [Best practices to follow]

## DON'T

- [Common mistakes to avoid]
- [Anti-patterns to prevent]

## Output Format

[Expected structure and formatting]

## Examples (when appropriate)

[Few-shot examples of desired behavior]
```

### Essential Elements

1. **Role Definition**: Clear "You are..." statement establishing identity
2. **Scope Boundaries**: What the agent handles vs. delegates
3. **Decision Framework**: How to approach problems systematically
4. **Quality Standards**: Measurable criteria for success
5. **Error Handling**: How to handle edge cases and failures
6. **Output Expectations**: Format, structure, and style requirements

### Advanced Techniques

#### Chain-of-Thought Integration

```markdown
When analyzing [complex task]:

1. First, identify...
2. Then, evaluate...
3. Consider implications...
4. Finally, synthesize...
```

#### Self-Validation Mechanisms

```markdown
Before completing any task, verify:

- [ ] All requirements addressed
- [ ] Output meets quality standards
- [ ] Edge cases considered
- [ ] Documentation adequate
```

#### Proactive Research Triggers

```markdown
**IMPORTANT: If you find yourself going in circles or repeatedly attempting
the same approaches without success, STOP and use WebSearch to research
current best practices.**
```

---

## Triggering Conditions Best Practices

### Writing Effective Descriptions

#### Structure Template

```
[Primary use case]. Use when [specific conditions]. NOT for [exclusions].
[Unique value proposition]. [Proactive trigger phrase if needed].
```

#### Clarity Checklist

- [ ] Can a user immediately know if this agent applies?
- [ ] Are exclusions clearly stated?
- [ ] Is the value proposition obvious?
- [ ] Under 400 characters?
- [ ] Includes concrete trigger scenarios?

### Trigger Phrases

**High Priority (Use Proactively)**

- "Use PROACTIVELY when..."
- "MUST BE USED for..."
- "Use IMMEDIATELY after..."

**Conditional (Context-Dependent)**

- "Use when you need..."
- "Helpful for..."
- "Consider using when..."

**Exclusionary (Clear Boundaries)**

- "NOT for simple..."
- "Skip if you already..."
- "Only use when..."

### Examples of Effective Descriptions

```yaml
# GOOD: Clear, specific, actionable
description: "Expert code review specialist. Use PROACTIVELY after writing
or modifying code. Reviews for quality, security, and maintainability.
NOT for debugging runtime errors. Provides actionable improvement suggestions."

# BAD: Vague, no clear triggers
description: "Helps with code stuff and makes things better."
```

---

## Tool Selection and Restrictions

### Tool Selection Strategy

#### Principle of Least Privilege

Only grant tools necessary for the agent's core function:

```yaml
# Analysis Agent - Read-only tools
tools: Glob, Grep, LS, Read, WebSearch

# Implementation Agent - Full access
tools: Read, Edit, MultiEdit, Write, Bash, TodoWrite

# Documentation Agent - Limited write access
tools: Read, Write, WebSearch, WebFetch
```

### Common Tool Combinations

#### Code Analysis Pattern

```yaml
tools: Glob, Grep, LS, Read, WebSearch
```

Use for: Architecture analysis, code exploration, dependency mapping

#### Testing Pattern

```yaml
tools: Read, Edit, Write, Bash, Grep, Glob
```

Use for: Test creation, test execution, coverage analysis

#### Documentation Pattern

```yaml
tools: Read, Write, WebSearch, WebFetch
```

Use for: README creation, API documentation, technical writing

#### Refactoring Pattern

```yaml
tools: Read, Edit, MultiEdit, Grep, Glob, TodoWrite
```

Use for: Code cleanup, pattern implementation, systematic changes

### Tool Restriction Guidelines

1. **Never grant Bash to read-only agents**
2. **Limit Write access to agents that create new content**
3. **WebSearch/WebFetch only when external information needed**
4. **TodoWrite for agents managing complex workflows**
5. **MultiEdit preferred over Edit for refactoring agents**

---

## Writing Style and Clarity Guidelines

### System Prompt Language

#### Use Active Voice

```markdown
# GOOD

You analyze code architecture and identify patterns.

# BAD

Code architecture is analyzed and patterns are identified by you.
```

#### Be Direct and Specific

```markdown
# GOOD

Examine the code to identify:

- Unused variables
- Redundant conditionals
- Performance bottlenecks

# BAD

Look at various aspects of the code quality.
```

#### Structure with Headers

```markdown
## Primary Objective

[Clear statement]

### Methodology

[Step-by-step approach]

### Quality Standards

[Measurable criteria]
```

### Formatting Best Practices

1. **Use Markdown formatting** for structure and clarity
2. **Bullet points** for lists and criteria
3. **Code blocks** for examples
4. **Bold** for critical warnings or instructions
5. **Numbered lists** for sequential steps

### Tone and Voice

- **Professional but approachable**
- **Confident without being dogmatic**
- **Educational when explaining decisions**
- **Direct about problems found**
- **Constructive in criticism**

---

## Common Patterns and Anti-Patterns

### Effective Patterns

#### 1. Role-Based Specialization

```markdown
You are a Senior Security Analyst specializing in vulnerability detection...
```

**Why it works**: Clear role establishes expertise and approach

#### 2. Structured Decision Trees

```markdown
When encountering an error:

1. If compilation error → Check syntax and imports
2. If runtime error → Analyze stack trace
3. If logic error → Trace execution flow
```

**Why it works**: Provides clear decision framework

#### 3. Example-Driven Guidance

```markdown
## Examples

### Good Test Structure:

describe('Component', () => {
it('should handle specific behavior', () => {})
})

### Avoid:

it('works', () => {})
```

**Why it works**: Concrete examples prevent ambiguity

#### 4. Progressive Enhancement

```markdown
Start with minimal implementation, then:

1. Add error handling
2. Optimize performance
3. Enhance documentation
```

**Why it works**: Iterative approach ensures functionality first

### Anti-Patterns to Avoid

#### 1. Vague Instructions

```markdown
# AVOID

Make the code better and fix any issues you find.

# BETTER

Identify and fix:

- Memory leaks
- Unhandled promise rejections
- Missing error boundaries
```

#### 2. Overlapping Responsibilities

```markdown
# AVOID

You handle testing, documentation, and deployment.

# BETTER

You specialize in unit test creation. Delegate integration
testing to test-integration agent.
```

#### 3. Negative Framing Overload

```markdown
# AVOID

Don't do X. Never do Y. Avoid Z at all costs.

# BETTER

Focus on [positive behavior]. Note: Avoid [specific anti-pattern]
because [reason].
```

#### 4. Missing Context

```markdown
# AVOID

Fix the authentication.

# BETTER

Review authentication flow for:

- Token validation
- Session management
- Permission checks
```

---

## Performance Optimization Tips

### Token Efficiency

#### 1. Concise Instructions

```markdown
# Inefficient (more tokens)

You should carefully examine the code and look for any potential
issues that might exist and then provide suggestions for how to
improve them.

# Efficient (fewer tokens)

Identify issues and suggest improvements.
```

#### 2. Structured Formatting

Use headers and lists instead of prose:

```markdown
## Tasks

- Analyze performance
- Identify bottlenecks
- Suggest optimizations
```

### Context Window Management

#### 1. Prioritize Information

```markdown
## Critical Context (Always Consider)

- Security implications
- Performance impact
- Breaking changes

## Secondary Context (When Relevant)

- Code style
- Documentation completeness
```

#### 2. Progressive Disclosure

```markdown
Start with high-level analysis.
Dive deeper only when issues found.
Request additional context as needed.
```

### Model Selection Optimization

| Task Complexity | Recommended Model | Example Use Cases                                     |
| --------------- | ----------------- | ----------------------------------------------------- |
| Simple          | haiku             | File listing, basic grep, syntax checks               |
| Moderate        | sonnet            | Code generation, refactoring, documentation           |
| Complex         | opus              | Architecture design, complex debugging, test creation |

### Execution Speed Tips

1. **Batch Operations**: Use MultiEdit over multiple Edit calls
2. **Targeted Searches**: Use specific globs/greps vs. broad searches
3. **Early Termination**: Stop analysis when objective met
4. **Parallel Processing**: Note when tasks can run concurrently

---

## Testing and Validation Approaches

### Agent Testing Framework

#### 1. Trigger Validation

Test that agent activates correctly:

```markdown
Given: [Scenario]
When: User requests [Action]
Then: [This agent] should trigger
```

#### 2. Output Quality Checks

```markdown
## Validation Criteria

- [ ] Output matches expected format
- [ ] All requirements addressed
- [ ] No hallucinations or assumptions
- [ ] Edge cases handled
```

#### 3. Tool Usage Verification

```markdown
Verify agent:

- Uses only assigned tools
- Applies tools appropriately
- Handles tool failures gracefully
```

### Common Test Scenarios

1. **Happy Path**: Standard use case with ideal conditions
2. **Edge Cases**: Boundary conditions, empty inputs, extremes
3. **Error Conditions**: Missing files, invalid syntax, failures
4. **Ambiguous Requests**: Unclear or contradictory instructions
5. **Resource Constraints**: Large files, many files, deep nesting

### Quality Metrics

| Metric           | Target | Measurement                        |
| ---------------- | ------ | ---------------------------------- |
| Trigger Accuracy | >95%   | Correct activation rate            |
| Task Completion  | >90%   | Successfully completes objective   |
| Output Quality   | >85%   | Meets formatting/content standards |
| Tool Efficiency  | <1.5x  | Optimal tool calls vs. actual      |
| Error Recovery   | >80%   | Gracefully handles failures        |

---

## Examples and Templates

### Complete Agent Template

```markdown
---
name: template-agent
description: [Primary function]. Use when [conditions]. NOT for [exclusions].
  [Unique value]. [Proactive trigger if needed].
model: sonnet
tools: Read, Edit, Write
color: blue
---

You are a [ROLE] specializing in [DOMAIN]. Your expertise lies in
[SPECIFIC CAPABILITIES].

## Core Responsibilities

1. **[Responsibility 1]**: [Details]
2. **[Responsibility 2]**: [Details]
3. **[Responsibility 3]**: [Details]

## Methodology

When approaching [task type]:

1. **Analysis Phase**

   - [Step 1]
   - [Step 2]

2. **Implementation Phase**

   - [Step 1]
   - [Step 2]

3. **Validation Phase**
   - [Step 1]
   - [Step 2]

## Quality Standards

Your work must:

- Meet [criterion 1]
- Ensure [criterion 2]
- Validate [criterion 3]

## DO

- Use [best practice 1]
- Apply [pattern 1]
- Prioritize [aspect 1]

## DON'T

- Avoid [anti-pattern 1]
- Skip [bad practice 1]
- Assume [assumption 1]

## Output Format

Structure your output as:

1. **Summary**: Brief overview
2. **Details**: Comprehensive analysis
3. **Recommendations**: Actionable next steps

## Error Handling

If you encounter issues:

1. Document the problem clearly
2. Suggest alternative approaches
3. Request additional context if needed

Remember: [Key principle or philosophy]
```

### Specialized Agent Examples

#### Code Reviewer Agent

```yaml
---
name: code-reviewer
description: Analyzes code changes for quality, security, and best practices.
  Use PROACTIVELY after any code modification. NOT for writing new code.
  Provides actionable feedback with severity levels.
model: opus
tools: Read, Grep, Glob, WebSearch
---
You are a Senior Code Review Specialist with 15+ years of experience...
```

#### Test Generator Agent

```yaml
---
name: test-generator
description: Creates comprehensive unit tests with edge cases. Use when new
  functions/components need testing. NOT for integration or E2E tests.
  Focuses on behavior-driven testing with high coverage.
model: opus
tools: Read, Write, Edit, Bash, Grep
---
You are a Test Engineering Expert specializing in behavior-driven testing...
```

#### Documentation Writer Agent

```yaml
---
name: doc-writer
description: Creates clear technical documentation. Use for README files,
  API docs, or architecture guides. NOT for code comments. Writes for
  both technical and non-technical audiences.
model: sonnet
tools: Read, Write, WebSearch, WebFetch
---
You are a Technical Documentation Specialist who creates clear, comprehensive...
```

---

## Best Practices Summary

### Golden Rules

1. **Single Responsibility**: Each agent excels at ONE thing
2. **Clear Boundaries**: Explicit about what it does and doesn't handle
3. **Measurable Quality**: Define specific, testable success criteria
4. **Proactive Guidance**: Include self-correction mechanisms
5. **Tool Minimalism**: Grant only necessary tools
6. **Structured Thinking**: Use systematic approaches and frameworks
7. **Example-Driven**: Provide concrete examples where helpful
8. **Error Resilience**: Plan for failures and edge cases
9. **Continuous Learning**: Include research triggers for complex problems
10. **User-Centric**: Optimize for developer productivity and understanding

### Quick Checklist for New Agents

- [ ] Name clearly indicates specialty
- [ ] Description under 400 characters with clear triggers
- [ ] Model selection matches task complexity
- [ ] Tools limited to necessary ones
- [ ] System prompt has clear role definition
- [ ] Includes methodology section
- [ ] Has DO and DON'T sections
- [ ] Defines output format
- [ ] Includes error handling guidance
- [ ] Contains quality criteria
- [ ] Uses structured markdown formatting
- [ ] Avoids vague instructions
- [ ] Provides examples where helpful
- [ ] Includes self-validation mechanisms
- [ ] Balances clarity with efficiency - prioritize understanding, then optimize tokens

---

## Resources and References

### Official Documentation

- [Anthropic Claude Code Documentation](https://docs.anthropic.com/en/docs/claude-code)
- [Prompt Engineering Guide](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering)
- [Subagents Documentation](https://docs.anthropic.com/en/docs/claude-code/sub-agents)

### Community Resources

- [Awesome Claude Code Agents](https://github.com/hesreallyhim/awesome-claude-code-agents)
- [Production Agent Collection](https://github.com/wshobson/agents)
- [Claude Code Best Practices](https://www.anthropic.com/engineering/claude-code-best-practices)

### Prompt Engineering References

- [Prompt Engineering Guide](https://www.promptingguide.ai/)
- [OpenAI Best Practices](https://help.openai.com/en/articles/6654000)
- [Microsoft Azure AI Patterns](https://learn.microsoft.com/en-us/azure/ai-foundry/openai/concepts/prompt-engineering)

---

_This guide represents current best practices as of 2025-08-14. Agent design patterns
and optimization techniques continue to evolve. Regularly review official documentation
and community resources for updates._
