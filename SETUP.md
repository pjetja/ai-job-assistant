# Setup Guide

## Requirements

- Claude Code CLI installed
- Google Chrome (for PDF generation)
- A text editor

## Installation

### 1. Clone the plugin

```bash
git clone https://github.com/pjetja/ai-job-assistant \
  ~/.claude/plugins/ai-job-assistant
```

### 2. Install commands

```bash
# Link commands to Claude Code commands directory
for f in ~/.claude/plugins/ai-job-assistant/commands/*.md; do
  ln -sf "$f" ~/.claude/commands/
done
```

### 3. Install skills

```bash
# Link skills to Claude Code skills directory
for f in ~/.claude/plugins/ai-job-assistant/skills/*.md; do
  ln -sf "$f" ~/.claude/skills/
done
```

### 4. Create your data directory

```bash
mkdir -p ~/JOB_ASSISTANT_DATA
cp ~/.claude/plugins/ai-job-assistant/config.template.md \
   ~/JOB_ASSISTANT_DATA/config.md
```

### 5. Edit your config

Open `~/JOB_ASSISTANT_DATA/config.md` and fill in:
- Your name and email
- The full path to `data_dir` (the directory you just created)
- The roles you want CVs for

### 6. (Optional) Set env var for custom data dir location

```bash
# Add to ~/.zshrc or ~/.bashrc
export AJA_DATA_DIR="/path/to/your/data/dir"
```

## Uninstall

```bash
# Remove command symlinks
for f in ~/.claude/plugins/ai-job-assistant/commands/*.md; do
  rm ~/.claude/commands/$(basename "$f")
done
# Remove skills symlinks
for f in ~/.claude/plugins/ai-job-assistant/skills/*.md; do
  rm ~/.claude/skills/$(basename "$f")
done
```
