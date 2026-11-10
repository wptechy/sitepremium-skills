# Sitepremium agent skills

Seven skills that tell your own agent (Claude Code, Codex, Cursor, ...) how to work on a Sitepremium site through the
Sitepremium MCP server (`/mcp` on the API). The platform runs no AI model: the agent is yours, on your subscription; the
server validates, renders and stores what the agent writes, always into the site draft.

| Skill         | What it does                                                                                                                                            |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `sp-build`    | Builds the theme: home page, the rest of the pages, one page, one template; clone fidelity loop (`check_fidelity`). Parameter `scope`: `HOME` or `ALL`. |
| `sp-edit`     | Makes a requested change to an existing theme and nothing else.                                                                                         |
| `sp-propose`  | Proposes visual improvements for a page or template, each with its rationale and a ready theme change.                                                  |
| `sp-seo`      | Writes SEO titles and meta descriptions for the SEO queue.                                                                                              |
| `sp-analyze`  | Hands in an analysis report (Setup, Analiza).                                                                                                           |
| `sp-research` | Hands in a brand research report, every fact with sources (Setup, Brand).                                                                               |
| `sp-plan`     | Proposes the page structure (Setup, Structura).                                                                                                         |

Every skill follows the same flow: context (brief) -> plan -> execution -> verification -> report, with explicit stops for human
decisions. Release and rollback happen only at the explicit request of the person; the skills never do them on their own.

## Layout

```
agent-skills/
  .claude-plugin/marketplace.json        Claude Code marketplace (one plugin)
  plugins/sitepremium/
    .claude-plugin/plugin.json
    skills/<name>/SKILL.md               the skills (frontmatter: name + description)
```

The `SKILL.md` files are the single source. The API serves the same text as one MCP prompt per skill (`sp-build`, ...), for
agents that do not have the skills installed (`apps/api/src/mcp/skills.ts` reads these files). A test
(`apps/api/src/agent-skills.test.ts`) checks the frontmatter, the required sections, the shared theme rules, and that every tool a
skill names exists on the MCP server.

This directory is not a pnpm workspace package. `apps/coming-soon` is untouched.

## Install

1. Connect the agent to the MCP server (Profile creates your token), for example for Claude Code:
   `claude mcp add --transport http sitepremium <api-url>/mcp --header "Authorization: Bearer <token>"`.
2. Skills:
   - **Claude Code:** `/plugin marketplace add <owner>/<public-repo>` then `/plugin install sitepremium@sitepremium`
     (from a local checkout: `/plugin marketplace add ./agent-skills`).
   - **Codex:** copy (or symlink) the folders in `plugins/sitepremium/skills/` into `~/.agents/skills/` (user) or
     `.agents/skills/` (repository). Codex reads the same `SKILL.md` format (frontmatter `name` + `description`). Start a new session
     and invoke a skill with `$sp-build`, or let Codex pick it from the description.
   - **Cursor, Claude Desktop, Gemini CLI and other agents without skills:** use the MCP prompts of the server (`sp-build`, ...):
     each prompt returns the text of its skill.

## Public mirror (needs Owner approval)

The source of truth is this directory in the monorepo. For distribution it is mirrored to a public GitHub repository whose root
is the content of `agent-skills/` (so `.claude-plugin/marketplace.json` sits at the root). The first push to the public repository
happens only with the Owner's approval; nothing is published automatically. Mirror step, once approved:

```bash
git subtree split --prefix=agent-skills -b skills-mirror
git push <public-repo-url> skills-mirror:main
```

Every change to a skill is a normal pull request in the monorepo; the mirror is updated from `main` afterwards.

## Local exports

Archives and WordPress dumps are not uploaded as such. The skills tell the agent to process them on the person's machine and to
upload only what the site publishes (images and files through `upload_media`, content through the content tools). There is no
separate mechanism.
