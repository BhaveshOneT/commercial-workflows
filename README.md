# Commercial Workflows Plugin

Sales and commercial workflow automation for Claude Cowork. Human-in-the-loop by design — drafts and classifies, never sends or modifies CRM data.

## Skills

| Skill | Description |
|-------|-------------|
| `sales-email-response` | Analyze inbound sales email threads, classify intent (positive/neutral/negative), and draft context-aware replies |

## Installation

### Claude Cowork (ZIP Upload)

1. Download or clone this repository
2. ZIP the `commercial-workflows/` directory
3. Upload via Cowork plugin settings

### Claude Code (Marketplace)

```
/plugin marketplace add BhaveshOneT/onethousand-doc-plugins
/plugin install commercial-workflows@onethousand-doc-plugins
```

## Hard Boundaries

All skills in this plugin enforce:

- No automatic email sending
- No CRM record creation, modification, or deletion
- No data fabrication — missing data is flagged explicitly
- Human reviews and manually executes every action

## License

Proprietary — One Thousand GmbH
