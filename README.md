# Person Social Footprint Mapper MCP Server

[![Smithery](https://smithery.ai/badge/mambabuilt/mcp-person-social-footprint-mapper)](https://smithery.ai/servers/mambabuilt/mcp-person-social-footprint-mapper) [![Glama score](https://glama.ai/mcp/servers/mambalabsdev/mcp-person-social-footprint-mapper/badges/score.svg)](https://glama.ai/mcp/servers/mambalabsdev/mcp-person-social-footprint-mapper) [![MCP Registry](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fregistry.modelcontextprotocol.io%2Fv0%2Fservers%3Fsearch%3Dcom.mambabuilt%252Fmcp-person-social-footprint-mapper%26limit%3D1&query=%24.servers%5B0%5D._meta%5B%22io.modelcontextprotocol.registry%2Fofficial%22%5D.status&label=mcp%20registry&color=blue)](https://registry.modelcontextprotocol.io/v0/servers?search=com.mambabuilt/mcp-person-social-footprint-mapper&limit=1) [![npm version](https://img.shields.io/npm/v/@mambalabsdev/mcp-person-social-footprint-mapper)](https://www.npmjs.com/package/@mambalabsdev/mcp-person-social-footprint-mapper) [![npm downloads](https://img.shields.io/npm/dm/@mambalabsdev/mcp-person-social-footprint-mapper)](https://www.npmjs.com/package/@mambalabsdev/mcp-person-social-footprint-mapper) [![license](https://img.shields.io/github/license/mambalabsdev/mcp-person-social-footprint-mapper)](https://github.com/mambalabsdev/mcp-person-social-footprint-mapper/blob/main/LICENSE) [![mcpservers.org](https://img.shields.io/badge/mcpservers.org-listed-blue)](https://mcpservers.org/servers/mambalabsdev/mcp-person-social-footprint-mapper)

<a href="https://mcprush.com/to/person-social-footprint-mapper-mcp?ref=badge"><img width="380" height="200" src="https://mcprush.com/badge/person-social-footprint-mapper-mcp/card.svg" alt="Person Social Footprint Mapper MCP server" /></a>

An MCP server for the Mamba Labs Person Social Footprint Mapper actor on Apify. Give it a person's full name and the domain of the company they work at. Each run returns one row with that person's public GitHub, Bluesky, personal site, Substack, and X profiles, with a status and a match confidence per platform.

## What's Inside

- [What it does](#what-it-does)
- [Quick start](#quick-start)
- [Prerequisites](#prerequisites)
- [Example prompts](#example-prompts)
- [Tool](#tool)
- [Inputs](#inputs)
- [Pricing](#pricing)
- [Your keys](#your-keys)
- [Full actor documentation](#full-actor-documentation)
- [License](#license)

## What it does

Find a person's public profiles from a name and a company domain. You get one row per person with the GitHub profile, Bluesky profile, personal website, Substack newsletter, and X profile, each with public follower counts where the platform shows them, a status (`found`, `ambiguous`, `not_found`, `skipped`, `skipped_rate_limit`, `blocked`), and a confidence from 0 to 1.

A profile is `found` only at 0.8 or more, which needs evidence that ties it to the company or to another profile of the same person: a link to the company domain, the company's own team page listing it, the GitHub company field or organization membership, or two profiles that link to each other. A matching name alone is never enough, and a likely match that is not certain comes back `ambiguous` with no handle. No email address or phone number is ever returned, LinkedIn is never opened, and Substack is never read.

All of the work runs on Apify. This package is a thin client that starts the actor run, waits for it, and hands back the dataset rows.

## Quick start

You need Node.js 18 or newer and an Apify account with an API token.

Add this to your Claude Desktop config (`claude_desktop_config.json`), Cursor, or any MCP client:

```json
{
  "mcpServers": {
    "mamba-person-social-footprint-mapper": {
      "command": "npx",
      "args": [
        "-y",
        "@mambalabsdev/mcp-person-social-footprint-mapper"
      ],
      "env": {
        "APIFY_TOKEN": "your-apify-token"
      }
    }
  }
}
```

Restart the client. The `map_person_social_footprint` tool appears.

## Prerequisites

- Node.js 18 or newer.
- An Apify API token from [console.apify.com/account/integrations](https://console.apify.com/account/integrations), set as `APIFY_TOKEN`. The server lists its tool without a token; a call needs one.
- Optional: `GITHUB_TOKEN`, your own free GitHub token, for batches larger than a few dozen persons.
- Optional: `X_BEARER_TOKEN`, your own X API bearer token. Without it the X columns return `skipped`.

## Example prompts

- "Find Mitchell Hashimoto's public GitHub and Bluesky profiles. He founded hashicorp.com."
- "For these five founders and their company domains, which have a personal site or a newsletter?"
- "Does the CTO of hubspot.com have a public GitHub profile?"

## Tool

`map_person_social_footprint`. One tool. Example arguments:

```json
{
  "full_name": "Mitchell Hashimoto",
  "company_domain": "hashicorp.com"
}
```

## Inputs

Every buyer facing input of the actor. Omitted inputs take the actor's own defaults. Give `full_name` with `company_domain`, or `persons`. The two keys are read from this server's environment, never from tool arguments.

| Input | Type | Required | Description |
|---|---|---|---|
| `full_name` | string | no | First and last name, for example Mitchell Hashimoto. |
| `company_domain` | string | no | The company the person works at, for example hashicorp.com. |
| `persons` | array | no | Several persons, one per entry as `Full Name \| company.com`. Up to 200 per run. |
| `linkedin_url` | string | no | A weak hint from the words in the URL. LinkedIn is never opened. |
| `location` | string | no | City or country, a small boost when a profile agrees. |
| `platforms` | array | no | Any of github, bluesky, personal_site, substack, x. Omit for all five. |
| `suppress_profile_urls` | array | no | Profile URLs never to return. |
| `max_charge_usd` | string | no | Optional cost cap for the run. |
| `proxy_mode` | string | no | auto or direct. Default auto. |

## Pricing

Pay per event on Apify, billed to your Apify account. Prices in USD.

| Event | Free plan | Bronze | Silver | Gold |
|---|---|---|---|---|
| `person-mapped`, per person with at least one profile found | $0.005 | $0.00475 | $0.0045 | $0.00425 |
| `apify-actor-start`, per run start | $0.0002 | $0.0002 | $0.0002 | $0.0002 |

A person with nothing found, and blocked and error rows, are free. Mamba Labs pays the platform costs of the run, Apify proxy traffic included.

## Your keys

The actor stores no key. `GITHUB_TOKEN` and `X_BEARER_TOKEN` are optional and are your own: this server reads them from its environment and sends them in the run input of that run only. X bills each X lookup to your own X account. With no X key, X is never contacted.

## Responsible use

GitHub's Acceptable Use Policies do not allow its users' information to be used for spam, unsolicited email, or selling personal information, for example to recruiters, headhunters, and job boards. You are the data controller for the rows you collect under GDPR, CCPA, and applicable privacy laws.

## Full actor documentation

[apify.com/mambalabs/person-social-footprint-mapper](https://apify.com/mambalabs/person-social-footprint-mapper)

## License

MIT. See [LICENSE](LICENSE).
