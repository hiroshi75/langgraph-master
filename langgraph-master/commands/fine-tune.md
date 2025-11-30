---
name: fine-tune
description: Prompt-level optimization for LangGraph applications without changing graph structure
---

# LangGraph Fine-Tuning Command

Optimize prompts and parameters in your LangGraph application to improve accuracy, reduce costs, and enhance performance—without modifying the graph structure.

## Purpose

Optimize your LangGraph application according to the following objectives:

```
$ARGUMENTS
```

Unlike **arch-tune** which modifies graph structure, **fine-tune** focuses on:

- Prompt engineering (few-shot, chain-of-thought, structured output)
- Parameter tuning (temperature, max_tokens, model selection)
- Cost optimization (prompt caching, model downgrades where appropriate)
- Output quality improvements (format constraints, validation)

## Execution Flow

### Initialization: Task Registration

At the start of the fine-tune command, use the TodoWrite tool to register all Phases as tasks.

Update each Phase to `in_progress` at the start and `completed` upon completion.

### Phase 1: Preparation and Analysis

**Purpose**: Understand optimization targets and current state

**Execution Steps**:

1. **Load Objective Settings**

   - Check for `.langgraph-master/fine-tune.md`
   - If not exists, help user create optimization goals

2. **Identify Optimization Targets**

   - Use Serena MCP to find LLM-calling nodes
   - `find_symbol` to locate LLM clients (ChatOpenAI, ChatAnthropic, etc.)
   - `find_referencing_symbols` to identify prompt construction locations
   - Evaluate improvement potential for each node

3. **Create Target List**
   - Rank nodes by optimization potential
   - Document current prompt patterns

**Output**:

- List of optimization target nodes with priority ranking
- Current prompt analysis

### Phase 2: Baseline Evaluation

**Purpose**: Quantitatively measure current performance

**Execution Steps**:

1. **Prepare Evaluation Environment**

   - Check/create evaluation program in `.langgraph-master/evaluation/`
   - Prepare test cases covering edge cases
   - Define metrics (accuracy, latency, cost)

2. **Baseline Measurement**

   - Run 3-5 iterations for statistical reliability
   - Record all metrics with timestamps
   - Calculate mean, std dev, confidence intervals

3. **Analyze Results**
   - Identify underperforming areas
   - Document specific failure patterns
   - Prioritize improvement targets

**Output**:

- `baseline_results.json` - Detailed baseline metrics
- Identification of highest-impact optimization targets

### Phase 3: Iterative Improvement

**Purpose**: Data-driven incremental optimization

**Execution Steps**:

1. **Prioritize Improvements**

   - Select highest-impact target first
   - Consider effort vs. expected gain

2. **Implement Optimization**

   Apply techniques based on the issue:

   | Issue                | Technique                      | Expected Gain    |
   | -------------------- | ------------------------------ | ---------------- |
   | Low accuracy         | Few-shot examples              | +10-20% accuracy |
   | Inconsistent output  | Structured output format       | -90% parse error |
   | High cost            | Model downgrade, prompt cache  | -40-60% cost     |
   | Slow response        | Streaming, parallel processing | -30-50% latency  |
   | Poor edge case       | Chain-of-thought prompting     | +15-25% accuracy |

3. **Post-Improvement Evaluation**

   - Run same test cases (3-5 iterations)
   - Compare against baseline
   - Calculate statistical significance

4. **Decide Continuation**
   - If goal achieved: proceed to Phase 4
   - If not: repeat with next priority target
   - Max iterations: 5 (prevent over-optimization)

**Output**:

- Updated prompts/parameters in codebase
- Iteration results log

### Phase 4: Completion and Documentation

**Purpose**: Record achievements and finalize

**Execution Steps**:

1. **Create Final Report**

   ```markdown
   ## Fine-Tune Results

   ### Summary

   | Metric   | Baseline | Final  | Improvement |
   | -------- | -------- | ------ | ----------- |
   | Accuracy | X%       | Y%     | +Z%         |
   | Cost     | $X/1000  | $Y/1000| -Z%         |
   | Latency  | Xms      | Yms    | -Z%         |

   ### Changes Made

   1. Node X: Added few-shot examples (+15% accuracy)
   2. Node Y: Switched to structured output (-80% parse errors)

   ### Recommendations

   - Further improvements possible with...
   ```

2. **Git Commit**
   - Commit all changes with descriptive message
   - Tag if significant milestone

**Output**:

- `fine_tune_report.md` - Complete optimization report
- Git commit with all changes

## Optimization Techniques Reference

### High Impact (Apply First)

1. **Few-Shot Examples**: Add 2-3 examples for complex tasks
2. **Structured Output**: Use JSON mode or function calling
3. **Clear Instructions**: Be explicit about expected format

### Cost Reduction

1. **Prompt Caching**: Enable for repeated system prompts
2. **Model Selection**: Use smaller models for simple tasks
3. **Token Optimization**: Remove redundant instructions

### Quality Improvement

1. **Chain-of-Thought**: Add reasoning steps for complex logic
2. **Validation**: Add output validation and retry logic
3. **Temperature Tuning**: Lower for consistency, higher for creativity

## Important Notes

### Constraints

- **No Graph Structure Changes**: Do not add/remove nodes or edges
- **Preserve Data Flow**: Maintain state schema and node contracts
- **Single Variable Testing**: Change one thing at a time for clear attribution

### Evaluation Consistency

- Use identical test cases across all iterations
- Run sufficient iterations (3-5) for statistical validity
- Document all environment variables and conditions

### When to Use arch-tune Instead

If fine-tune cannot achieve goals after 3-5 iterations, consider:

- The graph structure itself may be the bottleneck
- Use `/arch-tune` to explore structural changes
- fine-tune optimizes within structure; arch-tune changes structure

## Usage Examples

### Basic Execution

```bash
# Improve accuracy
/langgraph-master:fine-tune "Improve classification accuracy to 95%"

# Reduce costs
/langgraph-master:fine-tune "Reduce API costs by 40%"

# Multiple goals
/langgraph-master:fine-tune "Improve accuracy to 90% while reducing latency by 30%"
```

### Example Workflow

```
User: /langgraph-master:fine-tune "Improve response quality"

Claude:
1. [Phase 1] Analyzing codebase... Found 3 LLM-calling nodes
2. [Phase 2] Running baseline... Accuracy: 72%, Latency: 1.2s
3. [Phase 3] Optimizing node 'generate_response'...
   - Added few-shot examples → Accuracy: 85% (+13%)
   - Added structured output → Parse errors: 0 (-100%)
4. [Phase 4] Final results: Accuracy 85%, Latency 1.1s
   Committed changes with report.
```

## Related Resources

- [fine-tune skill](../skills/fine-tune/SKILL.md) - Detailed skill documentation
- [workflow.md](../skills/fine-tune/workflow.md) - Step-by-step workflow
- [evaluation.md](../skills/fine-tune/evaluation.md) - Evaluation best practices
- [prompt_optimization.md](../skills/fine-tune/prompt_optimization.md) - Optimization techniques
- [arch-tune command](./arch-tune.md) - For graph structure changes
