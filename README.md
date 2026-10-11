# pradeep-skills

A Claude Code plugin marketplace of personal skills.

## Install

```
/plugin marketplace add pradeep-stellar/pradeep-skills
/plugin install command-line-prog-creator@pradeep-skills
```


## Plugins

| Plugin | Skill | Description |
| --- | --- | --- |
| `command-line-prog-creator` | `command-line-prog-creator` | Design and build well-behaved command-line programs.via [Mark Watson](https://mark-watson.blogspot.com/2026/10/skillmd-file-to-convert-large-python.html) |

## Layout

```
.claude-plugin/marketplace.json        # marketplace catalog
plugins/<plugin>/
  .claude-plugin/plugin.json           # plugin manifest
  skills/<skill>/SKILL.md              # the skill
```

## Adding a skill

1. Create `plugins/<name>/.claude-plugin/plugin.json` and `plugins/<name>/skills/<name>/SKILL.md`.
2. Add an entry to `plugins` in `.claude-plugin/marketplace.json`.
3. Update the table above.
