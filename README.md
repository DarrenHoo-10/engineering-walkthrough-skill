# Engineering Walkthrough Skill

Reusable Codex skill for producing source-backed engineering walkthroughs of complex codebases and business workflows.

The skill guides Codex to:

- align a concrete document outline with the user before deep tracing;
- identify the main state axis, entrypoints, storage tables, async boundaries, locks, idempotency, and configuration;
- produce durable Markdown walkthrough documents with code evidence;
- draw business swimlane diagrams, architecture diagrams, state diagrams, and ER diagrams;
- sync diagrams to Feishu/Lark whiteboards when publishing is explicitly requested.

## Repository Layout

```text
engineering-walkthrough-skill/
|-- README.md
|-- LICENSE
`-- engineering-walkthrough/
    |-- SKILL.md
    `-- agents/
        `-- openai.yaml
```

The actual Codex skill is the `engineering-walkthrough/` directory. The repository-level README and LICENSE are for open-source distribution and should not be copied inside the skill folder.

## Install

Clone this repository, then copy the skill directory into your Codex skills directory.

Windows PowerShell:

```powershell
git clone https://github.com/DarrenHoo-10/engineering-walkthrough-skill.git
Copy-Item -Recurse -Force .\engineering-walkthrough-skill\engineering-walkthrough "$env:USERPROFILE\.codex\skills\engineering-walkthrough"
```

macOS/Linux:

```bash
git clone https://github.com/DarrenHoo-10/engineering-walkthrough-skill.git
mkdir -p ~/.codex/skills
cp -R engineering-walkthrough-skill/engineering-walkthrough ~/.codex/skills/
```

Restart Codex after installation so the skill list refreshes.

## Use

Ask Codex to use the skill explicitly:

```text
Use $engineering-walkthrough to trace this repository's order lifecycle and write a Markdown walkthrough.
```

Typical requests:

- "Use $engineering-walkthrough to walk through a BPMN process from deployment to completion."
- "Use $engineering-walkthrough to document the full lifecycle of this business object."
- "Use $engineering-walkthrough and sync the finished document to Feishu."

## Validate

If you have Codex's built-in `skill-creator` validation script available:

```bash
python ~/.codex/skills/.system/skill-creator/scripts/quick_validate.py ./engineering-walkthrough
```

On Windows, the equivalent path is usually:

```powershell
python "$env:USERPROFILE\.codex\skills\.system\skill-creator\scripts\quick_validate.py" .\engineering-walkthrough
```

## License

MIT. See [LICENSE](LICENSE).
