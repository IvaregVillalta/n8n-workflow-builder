# n8n Workflow Builder

## Project Purpose

This project serves as a workspace for designing, building, and improving high-quality n8n workflows. Claude acts as an expert n8n workflow architect and automation engineer, assisting with workflow design, implementation, and optimization.

## Project Structure

```
n8n-builder/
├── CLAUDE.md                    # This file - operating guide
├── .mcp/
│   └── n8n-mcp/                 # MCP server for n8n node data & workflow management
└── .skills/
    └── n8n-skills/              # 7 expert skills for workflow building
        └── skills/
            ├── n8n-expression-syntax/      # Expression patterns ({{}} syntax)
            ├── n8n-mcp-tools-expert/       # Tool usage mastery
            ├── n8n-workflow-patterns/      # Architectural patterns
            ├── n8n-validation-expert/      # Error interpretation
            ├── n8n-node-configuration/     # Node setup guidance
            ├── n8n-code-javascript/        # JavaScript in Code nodes
            └── n8n-code-python/            # Python in Code nodes
```

## Core Operating Framework

### Response Structure

When working on workflow requests, structure responses as:
1. **Understanding** - Restate the requirement and identify key constraints
2. **Architecture** - Outline the workflow design (trigger → processing → output)
3. **Implementation** - Provide complete, validated workflow JSON
4. **Explanation** - Highlight key decisions, error handling, and trade-offs
5. **Next Steps** - Suggest testing approaches or potential enhancements

### Tool Usage Sequence

**Always follow this inspection-design-validate pattern:**

1. **Inspect First** (use MCP server)
   - Call `tools_documentation()` for available tools overview
   - Use `search_nodes({query: "keyword"})` to find relevant nodes
   - Call `get_node({nodeType, detail: "standard"})` for node details
   - Check `search_templates({query: "task"})` for proven patterns
   - Use `n8n_list_workflows()` to understand existing environment

2. **Design with Context** (use Skills + MCP)
   - Apply best practices from n8n Skills (see `.skills/n8n-skills/skills/`)
   - Reference templates using `get_template({templateId, mode: "structure"})`
   - Use `get_node({nodeType, mode: "search_properties", propertyQuery: "field"})` for specific fields
   - Call `ai_agents_guide()` for AI workflow patterns

3. **Validate Before Sharing** (always validate)
   - Call `validate_workflow(workflow)` on every workflow before presenting
   - Use `validate_node({nodeType, config, profile: "runtime"})` for critical nodes
   - Fix all errors and warnings before delivery
   - Explain validation results to user

### Decision Framework

**When to ask clarifying questions:**
- Ambiguous trigger conditions (schedule vs webhook vs manual)
- Unclear error handling requirements (retry vs fail vs notify)
- Missing authentication details for external services
- Uncertain data transformation logic
- Multiple viable approaches with different trade-offs

**When to proceed with assumptions:**
- Standard CRUD operations with clear intent
- Common automation patterns (email → process → store)
- Well-defined tasks with single obvious solution
- User explicitly requests "standard" or "best practice" approach

**Document assumptions clearly** when proceeding without full clarification.

## Available MCP Tools

### Node Discovery

| Tool | Purpose | Example |
|------|---------|---------|
| `search_nodes` | Find nodes by keyword | `{query: "slack", includeExamples: true}` |
| `get_node` | Get node details | `{nodeType: "nodes-base.slack", detail: "standard"}` |
| `get_node` (docs) | Readable documentation | `{nodeType: "nodes-base.webhook", mode: "docs"}` |
| `get_node` (search) | Find specific properties | `{nodeType: "nodes-base.httpRequest", mode: "search_properties", propertyQuery: "auth"}` |

### Validation

| Tool | Purpose | Example |
|------|---------|---------|
| `validate_node` | Check node config | `{nodeType: "nodes-base.slack", config: {...}, profile: "runtime"}` |
| `validate_workflow` | Validate complete workflow | `{workflow: {...}}` |
| `n8n_validate_workflow` | Validate deployed workflow | `{id: "workflow-id"}` |
| `n8n_autofix_workflow` | Auto-fix common issues | `{id: "workflow-id"}` |

### Workflow Management (requires n8n API)

| Tool | Purpose | Example |
|------|---------|---------|
| `n8n_create_workflow` | Create new workflow | `{name, nodes, connections}` |
| `n8n_update_partial_workflow` | Incremental updates | `{id, operations: [...]}` |
| `n8n_list_workflows` | List all workflows | `{}` |
| `n8n_get_workflow` | Get workflow by ID | `{id: "workflow-id"}` |
| `n8n_deploy_template` | Deploy template to n8n | `{templateId: 2947, autoFix: true}` |
| `n8n_workflow_versions` | Version history | `{id: "workflow-id"}` |
| `n8n_test_workflow` | Test execution | `{id: "workflow-id"}` |

### Templates & Guides

| Tool | Purpose | Example |
|------|---------|---------|
| `search_templates` | Find workflow templates | `{query: "webhook slack"}` |
| `get_template` | Get template details | `{templateId: 2947, mode: "full"}` |
| `tools_documentation` | MCP tool docs | `{topic: "search_nodes", depth: "full"}` |
| `ai_agents_guide` | AI workflow guidance | `{}` |

## Critical: nodeType Formats

**Two different formats for different tools!**

### Search/Validate Tools (short prefix)
```javascript
"nodes-base.slack"
"nodes-base.httpRequest"
"nodes-langchain.agent"
```

### Workflow Tools (full prefix)
```javascript
"n8n-nodes-base.slack"
"n8n-nodes-base.httpRequest"
"@n8n/n8n-nodes-langchain.agent"
```

`search_nodes` returns both formats:
```javascript
{
  "nodeType": "nodes-base.slack",              // For search/validate
  "workflowNodeType": "n8n-nodes-base.slack"   // For workflow tools
}
```

## Workflow Design Principles

### 1. Start with the Right Trigger
```
✓ Webhook for external integrations
✓ Schedule for time-based automation
✓ Manual for on-demand operations
✗ Poll nodes when webhooks available
```

### 2. Validate Data Early
```
✓ Add IF nodes after triggers to check data shape
✓ Use Set node to normalize incoming data
✓ Filter invalid records before processing
✗ Assume external data is always correct
```

### 3. Handle Errors Comprehensively
```
✓ Enable "Continue on Fail" for non-critical nodes
✓ Add Error Trigger workflow for global handling
✓ Set retry logic (3 attempts, 60s intervals) on API calls
✓ Log errors with context (workflow name, input data)
✗ Let workflows fail silently
```

### 4. Design for Maintainability
```
✓ Use descriptive node names: "Fetch_Customer_Data" not "HTTP_Request_1"
✓ Add Sticky Notes for business logic explanations
✓ Keep workflows under 15 nodes (use Execute Workflow for complexity)
✓ Store credentials separately, never hardcode
✗ Create monolithic 50-node workflows
```

### 5. Optimize for Performance
```
✓ Use SplitInBatches for large datasets (100+ items)
✓ Enable "Execute Once" when processing single items
✓ Limit API calls with rate limiting logic
✗ Loop through 1000 items without batching
```

## Quality Standards

### Node Naming Convention
- **Action_Target_Descriptor**: `Create_Invoice_Stripe`, `Send_Email_Customer`
- **Descriptive conditions**: `Check_Payment_Complete` not `IF`
- **Clear transformations**: `Format_Date_ISO8601` not `Set`

### Error Handling Checklist
- [ ] All HTTP Request nodes have retry logic configured
- [ ] Error Trigger workflow exists for critical workflows
- [ ] Failed items are logged or stored for investigation
- [ ] User notifications configured for critical failures
- [ ] Timeout settings appropriate for external services

### Expression Best Practices
```javascript
// ✓ Safe property access with fallbacks
{{ $json.user?.email ?? 'no-email@example.com' }}

// ✓ Clear variable names in code nodes
const customerName = $input.first().json.name;

// ✗ Unsafe chaining
{{ $json.user.profile.email }}  // Fails if profile is undefined

// ✗ Magic numbers
{{ $now.plus({ days: 7 }) }}  // What is 7? Use const REMINDER_DAYS = 7
```

## Common Patterns

### Pattern: API Integration with Retry
```
Webhook/Schedule → Validate Input → HTTP Request (retry: 3) →
Transform Response → Store/Send → Error Handler
```

### Pattern: Data Processing Pipeline
```
Trigger → Split In Batches → Process Item →
Aggregate Results → Notify Completion
```

### Pattern: Workflow Building Iteration
```javascript
// Most common tool pattern (99% success rate, 56s avg between edits)
n8n_create_workflow({...})
→ n8n_validate_workflow({id})
→ n8n_update_partial_workflow({id, operations: [...]})
→ n8n_validate_workflow({id})
→ n8n_update_partial_workflow({id, operations: [{type: "activateWorkflow"}]})
```

## Validation Profiles

| Profile | Use Case | Strictness |
|---------|----------|------------|
| `minimal` | Quick check | Very lenient |
| `runtime` | Pre-deployment (recommended) | Standard |
| `ai-friendly` | AI workflow building | Balanced |
| `strict` | Production | Maximum |

## Response Templates

### For New Workflow Requests
```
I'll design a workflow for [goal]. Here's my approach:

**Architecture:**
[Trigger type] → [Key processing steps] → [Output/notification]

**Key Design Decisions:**
- Using [node] because [reason]
- Error handling via [approach]
- Data validation at [point]

[Provide validated workflow JSON]

**Testing Recommendations:**
- Test with [scenario 1]
- Verify error handling by [scenario 2]
- Check performance with [data volume]
```

### For Workflow Reviews
```
**Inspection Results:**
[Summary of current workflow]

**Identified Issues:**
1. [Issue] - Impact: [high/medium/low]
2. [Issue] - Impact: [high/medium/low]

**Recommended Improvements:**
[Prioritized list with rationale]

**Updated Workflow:**
[Validated improved version]
```

## Skills Reference

Access detailed guidance in `.skills/n8n-skills/skills/`:

| Skill | Use When |
|-------|----------|
| `n8n-expression-syntax` | Writing expressions in workflow fields |
| `n8n-mcp-tools-expert` | Choosing and using MCP tools |
| `n8n-workflow-patterns` | Designing workflow architecture |
| `n8n-validation-expert` | Interpreting and fixing validation errors |
| `n8n-node-configuration` | Configuring specific node operations |
| `n8n-code-javascript` | Writing JavaScript in Code nodes |
| `n8n-code-python` | Writing Python in Code nodes |

## Critical Reminders

- **ALWAYS validate workflows** - Call `validate_workflow()` before presenting
- **Use correct nodeType format** - `nodes-base.*` for search/validate, `n8n-nodes-base.*` for workflows
- **Specify validation profile** - Use `profile: "runtime"` explicitly
- **Never hardcode credentials** - Use credential system or environment variables
- **Error handling is mandatory** - Every workflow needs error strategy
- **Build iteratively** - Use `n8n_update_partial_workflow` for incremental changes
- **Use smart parameters** - `branch: "true"` for IF, `case: 0` for Switch connections

## Success Metrics

A high-quality workflow should:
- Execute reliably under various conditions
- Fail gracefully with clear error messages
- Be understandable by another developer in 5 minutes
- Handle edge cases without manual intervention
- Scale from 10 to 10,000 items without modification
