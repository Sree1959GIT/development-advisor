# Installation Guide

## Quick Install (2 Minutes)

### Step 1: Create Directory
```bash
mkdir -p ~/.claude/skills/development-advisor
```

### Step 2: Copy Skill File
```bash
cp ../skill/SKILL.md ~/.claude/skills/development-advisor/
```

### Step 3: Restart Claude Code
- Close Claude Code completely
- Wait 2 seconds
- Reopen Claude Code

### Step 4: Test
Open any project and type:
```
/advisor
```

**If it works:** ✅ You're done!

## For Your School/Team

1. **Share this repository** with your group
2. **Each person** follows the Quick Install steps above
3. **Help each other** with troubleshooting

## Troubleshooting

### Issue: "Unknown command /advisor"

**Solution:**
```bash
# Verify file exists
ls ~/.claude/skills/development-advisor/SKILL.md

# Make sure you completely restarted Claude Code
# (not just the terminal, but the entire app)
```

### Issue: File not found

**Check path:**
```bash
# Create directory if missing
mkdir -p ~/.claude/skills/development-advisor

# Copy again
cp ../skill/SKILL.md ~/.claude/skills/development-advisor/
```

### Issue: Still not working

1. Delete the directory: `rm -rf ~/.claude/skills/development-advisor`
2. Start fresh with Step 1
3. Make sure you restart Claude Code (not just the project)

## Verify Installation

After restarting Claude Code, type in a project:
```
/advisor
```

You should get a structured response about your current session.

---

→ Ready? Next: See **Development-Advisor-Skill-Guide.md** for how to use it
