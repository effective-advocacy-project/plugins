# Effective Advocacy Project: plugins

This repository is a plugin marketplace for Claude and for ChatGPT / Codex. It
lists one plugin, **Effective Advocacy Project**, which lets your assistant
answer questions about your EAP advocacy campaigns: who the key actors are,
what an organisation or person has said, and how parliamentarians vote, with
links to the sources.

You need an EAP portal account with access to at least one campaign.

## Install in Claude

In claude.ai or Claude Desktop: **Customize → Plugins → Add from a
repository**, enter `effective-advocacy-project/plugins`, then install
**Effective Advocacy Project**. After installing, open the plugin's
**Connectors** tab, click **Connect** and sign in with your EAP portal account.

In Claude Code:

```
/plugin install effective-advocacy-project --marketplace effective-advocacy-project/plugins
```

## Install in Codex

```
codex plugin marketplace add effective-advocacy-project/plugins
codex plugin add effective-advocacy-project@effective-advocacy-project
```

Then connect the `eap` server and sign in with your EAP portal account.

The plugin itself lives at
<https://github.com/hubert-thieriot/effective-advocacy-project-plugin>.

- Help: <https://app.effectiveadvocacyproject.org/help/claude>
- Privacy policy: <https://app.effectiveadvocacyproject.org/privacy>
- Contact: info@effectiveadvocacyproject.org
