# Development Advisor Skill - Complete Guide

**The comprehensive guide to using the Development Advisor skill effectively.**

---

## Table of Contents

1. [What It Is](#what-it-is)
2. [Use Cases](#use-cases)
3. [How to Use](#how-to-use)
4. [Real Examples](#real-examples)
5. [Best Practices](#best-practices)
6. [FAQ](#faq)

---

## What It Is

The Development Advisor is a Claude Code skill that helps you:

- **Restart after breaks** — Get complete project context in 2 minutes
- **Make decisions** — Strategic cost-benefit analysis
- **Plan refactors** — Pre-flight checklist and readiness assessment
- **Debug strategically** — Error pattern analysis and recommendations
- **Manage sessions** — Guidance on when to fork, resume, or restart

---

## Use Cases

### Use Case 1: Restart After a Break ⭐ PRIMARY

**When:** You reopen your project after hours/days away

**How:**
```bash
/advisor "Help me get restarted"
```

**What You Get:**
- What you were working on
- Where you left off (commits, blockers)
- Project context (architecture, testing)
- How to resume (resume vs fork vs fresh)
- Immediate next steps

### Use Case 2: Decision Support

**When:** Unsure whether to fork, patch, refactor, etc.

**How:**
```bash
/advisor "Should I patch this or fix root cause?"
/advisor "Should I fork before the big refactor?"
```

**What You Get:**
- Cost-benefit analysis
- Option comparison
- Risk assessment
- Strategic recommendation

### Use Case 3: Pre-Refactor Assessment

**When:** Planning major refactoring work

**How:**
```bash
/advisor "Ready to refactor the auth module?"
```

**What You Get:**
- Readiness assessment
- Test coverage gaps
- Pre-refactor checklist
- Risk mitigation strategies

### Use Case 4: Stuck Debugging

**When:** Repeated errors across modules

**How:**
```bash
/advisor "Same error in 4 modules, pattern or isolated?"
```

**What You Get:**
- Error pattern analysis
- Root cause hypothesis
- Decision path (fix vs patch vs delegate)

---

## How to Use

### Basic Usage

```bash
# Simple status check
/advisor

# With a specific question
/advisor "Your question here"

# Restart after break (most common)
/advisor "Help me get restarted"
```

### Asking Better Questions

**Good:**
```
/advisor "Same error in 4 modules"
```

**Better:**
```
/advisor "Same validation error in 4 modules. Context 74%. Should I patch or fix root cause?"
```

**Best:**
```
/advisor "Validation error across auth, profile, settings modules. Repeated 4 times. Context 74%. Should I patch individually or find root cause? Deadline is Friday."
```

---

## Real Examples

### Example 1: Restart Scenario
```
Q: /advisor "Help me get restarted"

A: You've been away 2 days. Working on JWT auth (90% done).
   Last commit: "feat: token refresh"
   Blocker: Redis staging not ready (workaround: Docker)
   Recommendation: /resume [session-id]
   Next: Code review, tests, merge
```

### Example 2: Decision Scenario
```
Q: /advisor "Same error 4 turns, patch or fix root?"

A: Error Pattern: Shared validator issue
   Root cause fix: 2.5 hours, permanent solution
   Keep patching: 3 hours, 30+ hours future debugging
   Recommendation: Fork & fix root cause
   Commands: [specific grep and fix commands]
```

### Example 3: Architecture Scenario
```
Q: /advisor "Ready for auth refactor?"

A: Architecture: Follow existing patterns ✓
   Test coverage: 60% (need 85%+)
   Status: YELLOW LIGHT
   Checklist:
     - Add critical tests (1 week)
     - Design architecture (3-5 days)
     - Implement in phases (2-3 weeks)
     - Validate (1 week)
   Confidence: 85% (with preparations)
```

---

## Best Practices

### Practice 1: Use at Natural Break Points
- End of work session
- Start of work session
- Finished a feature
- Hit a blocker
- Before major decisions

### Practice 2: Ask Specific Questions
- Include context (error details, timeline, constraints)
- Mention what you've already tried
- State what decision you're facing

### Practice 3: Act on Recommendations
```
Get advice → Read reasoning → Copy command → Execute → Move forward
```

### Practice 4: Save Decisions
When making major choices, note:
- What decision you made
- Why (from advisor's reasoning)
- When
- Outcome (later)

### Practice 5: Learn from Reasoning
Don't just follow commands—understand the decision-making patterns. This teaches you strategic thinking.

---

## FAQ

**Q: How much does it cost?**
A: ~20-50K tokens per call. Usually worth it. Prevents wasted hours.

**Q: Can I use it offline?**
A: No, requires Claude Code + internet.

**Q: Does it work with all languages?**
A: Yes—Python, JS, Go, Rust, Java, etc.

**Q: Is it free?**
A: Yes! Community edition. Share freely.

**Q: Can I modify it?**
A: Yes! Contribute improvements back.

**Q: What if the advice is wrong?**
A: It's guidance, not commands. Evaluate and decide for yourself.

**Q: How long does it take to learn?**
A: 5 min to install, 2 min to use, 20 min to master.

---

## Integration with Claude Code

The advisor works alongside Claude Code's tools:
- `/context` — Visual context grid
- `/usage` — Cost breakdown
- `/advisor` — Strategic guidance ← You are here
- `/recap` — Quick summary
- `/checkpoint` — Save state

---

## Next Steps

1. **Install:** Follow Installation-Guide.md
2. **Try it:** `/advisor "Help me get restarted"`
3. **Share:** With your school/team
4. **Master it:** Use on real projects
5. **Improve it:** Contribute back to community

---

**Ready to transform your workflow?**

→ See Installation-Guide.md to get started
