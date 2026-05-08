# Himeshaa Agents

The decentralized agent registry for [Himeshaa](https://github.com/vinkius-labs/himeshaa) — an open-source AI agent orchestration platform.

This repository is the single source of truth for all agent definitions. Himeshaa reads directly from this repository to bootstrap and update its agent catalog.

## How It Works

```
himeshaa-registry/
└── agents/
    ├── official/             ← Official agents maintained by the core team
    │   ├── security-analyst/
    │   │   └── agent.yaml
    │   ├── devops-engineer/
    │   │   └── agent.yaml
    │   └── ...
    └── community/            ← Community-contributed agents (via PR)
        └── your-agent/
            └── agent.yaml
```

Himeshaa syncs agents with a single command:

```bash
php artisan himeshaa:sync-agents
```

No manual configuration. No database seeders. The directory structure **is** the registry.

---

## Creating an Agent

Create a directory under `agents/community/` with a descriptive kebab-case name and an `agent.yaml` file inside it.

### Minimal Example

```yaml
name: My Custom Agent
version: "1.0.0"
icon: bot

identity:
  role: general
  description: >-
    A brief description of what this agent specializes in.
  prompt: >-
    You are an AI assistant that... (full system prompt here)

skills:
  - name: Primary Skill
    description: What this skill does.
    tags:
      - relevant
      - tags
    examples:
      - A concrete example of a task this skill handles.

guidelines:
  - Always respond in a professional tone
  - Provide actionable recommendations

constraints:
  - Never share sensitive data
  - Always confirm before destructive actions
```

### Full Specification

```yaml
# ─── Himeshaa Agent Definition v1.0 ───
# A2A-compatible persona definition.
# Directory name = unique identifier (slug).
# Icon reference: https://lucide.dev/icons
# Spec: github.com/vinkius-labs/himeshaa-registry

name: Security Analyst
version: "1.0.0"
icon: shield                          # Lucide icon (kebab-case)

# ─── IDENTITY ───
identity:
  role: security                      # Domain category
  description: >-
    Specialized in cybersecurity, threat detection,
    and vulnerability management
  prompt: >-
    You are a Security Analyst focused on protecting systems
    and data from security threats...

# ─── EXPERTISE ───
expertise:
  - Threat Intelligence Feeds
  - Vulnerability Assessment Frameworks
  - Compliance Standards (SOC2, ISO 27001)

# ─── SKILLS ───
# A2A-compatible. Skill IDs are auto-generated from name at sync time.
skills:
  - name: Threat Detection
    description: >-
      Identify and classify security threats using real-time
      intelligence feeds and behavioral analysis.
    tags:
      - threat
      - detection
    examples:
      - Set up real-time monitoring to detect zero-day vulnerabilities.

  - name: Incident Response
    description: >-
      Coordinate and execute incident response procedures
      to contain and remediate security breaches.
    tags:
      - incident
      - response
    examples:
      - Develop a playbook to streamline reactions under pressure.

# ─── GUIDELINES ───
guidelines:
  - Prioritize vulnerabilities based on risk and impact
  - Document all findings and remediation steps
  - Stay current on emerging threats

# ─── CONSTRAINTS ───
constraints:
  - Never execute destructive actions without confirmation
  - Do not expose credentials or secrets in output

# ─── OUTPUT ───
output:
  mode: text/markdown
  structure:
    - Threat assessment
    - Vulnerability findings
    - Remediation steps

# ─── SETTINGS ───
settings:
  type: configurable
  prompt_instructions:
    type: textarea
    label: Security Analyst Processing Instructions
    placeholder: >-
      Describe what actions to take for security tasks.
    description: >-
      Instructions for processing security events.

# ─── PLACEHOLDERS ───
placeholders:
  assignment_title: "E.g.: Perform Vulnerability Scan on Web Server"
  work_instructions: >-
    E.g.: Run a vulnerability scan, document findings,
    and deliver a prioritized remediation list.
```

---

## Field Reference

| Field | Required | Description |
|:---|:---:|:---|
| `name` | ✅ | Human-readable agent name |
| `version` | ✅ | Semantic version (`"1.0.0"`) |
| `icon` | ✅ | [Lucide](https://lucide.dev/icons) icon name (kebab-case) |
| `identity.role` | ✅ | Domain category (e.g. `security`, `devops`, `design`) |
| `identity.description` | ✅ | One-line description of the agent's expertise |
| `identity.prompt` | ✅ | Full LLM system prompt |
| `expertise` | | List of knowledge domains |
| `skills` | ✅ | A2A-compatible skill definitions |
| `skills[].name` | ✅ | Skill name (ID is auto-generated via slugify) |
| `skills[].description` | ✅ | What the skill does |
| `skills[].tags` | | Categorization tags |
| `skills[].examples` | | Concrete task examples |
| `guidelines` | | Behavioral directives for the agent |
| `constraints` | | Safety guardrails |
| `output.mode` | | MIME type (default: `text/markdown`) |
| `output.structure` | | Expected response sections |
| `settings` | | Runtime UI configuration |
| `placeholders` | | UX hints for task creation |

---

## Contributing

1. Fork this repository
2. Create your agent directory: `agents/community/your-agent-name/agent.yaml`
3. Follow the specification above
4. Open a Pull Request

### Rules

- **One agent per directory** — the directory name becomes the unique slug
- **No `id` fields** — IDs are auto-generated from names during sync
- **Icons must be from [Lucide](https://lucide.dev/icons)** — use kebab-case names
- **Version must be semantic** — `"1.0.0"`, `"1.1.0"`, etc.
- **Prompts must be complete** — the system prompt is the agent's personality

---

## A2A Interoperability

Agent definitions in this registry are designed to be compatible with the [Agent-to-Agent (A2A) Protocol](https://github.com/google/A2A). Skills map directly to A2A `AgentSkill` objects, and Himeshaa can serve each agent as an A2A-compliant Agent Card via `/.well-known/agent-card.json`.

---

## License

MIT — see [Himeshaa](https://github.com/vinkius-labs/himeshaa) for details.
