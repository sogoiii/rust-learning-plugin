<assess-finding-skill>

<task>
Analyze and explain the following finding to help me understand it deeply:

**Finding:** $ARGUMENTS.finding
</task>

<process>
1. **Locate**: Find the code location(s) mentioned. If no file:line given, search for relevant code.

2. **Validate**: Read the actual code. Confirm whether the issue exists as described.
   - If the original analysis was wrong, say so clearly
   - If it's outdated (already fixed), note that

3. **Analyze**: Trace the execution flow. Understand:
   - What triggers this code path?
   - What's the current behavior?
   - What's the alleged problem?

4. **Visualize**: Create ASCII diagrams to explain. Use:
   - Flow charts for execution paths
   - Box diagrams for architecture
   - Tables for comparisons/tradeoffs
   - Timeline diagrams for async/concurrent issues

5. **Explain Simply**:
   - What happens now (current flow)
   - Why it might be a problem
   - What a fix would look like

6. **Assess**: Provide your judgment:
   | Aspect | Assessment |
   |--------|------------|
   | Issue valid? | Yes/No/Partially |
   | Severity | Critical/Medium/Low/Non-issue |
   | Fix complexity | Lines of code, risk |
   | Recommendation | Fix/Skip/Discuss |
</process>

<rules>
- ALWAYS read the code first—never trust the finding blindly
- Be honest if the finding is wrong or overstated
- Keep explanations concise but complete
- Diagrams should fit in ~60 char width
- Ask clarifying questions if the finding is ambiguous
</rules>

<success_criteria>
- Actual code is read and validated before explaining
- Finding is independently confirmed or refuted
- ASCII diagram or table is included to visualize the issue
- Assessment table with severity and recommendation is provided
- Explanation is honest about whether the finding is valid
</success_criteria>

</assess-finding-skill>
