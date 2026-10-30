# Effective Advocacy Project

Ask about your advocacy campaigns from Claude or ChatGPT: who the key actors
are, what an organisation or person has said, and how members of the European
Parliament, the Assemblée nationale and the Sénat vote, with their amendments
and roll calls. Answers come from your campaign's data, with links to the
sources and interactive panels.

The plugin is for organisations working with the
[Effective Advocacy Project](https://effectiveadvocacyproject.org). You need
an EAP portal account with access to at least one campaign. The assistant sees
exactly the campaigns your account can open on the portal, and nothing else.

## What the plugin contains

- **A connector**, `eap`, to the EAP server at
  `https://app.effectiveadvocacyproject.org/mcp`. After you add the plugin,
  open its **Connectors** tab, click **Connect**, and sign in with your EAP
  portal account. The plugin holds no credentials: sign-in is handled by your
  assistant and the EAP portal.
- **A skill**, `eap-advocacy`, that tells the assistant which EAP tool answers
  which question and how to cite its sources.
- The EAP icon.

The plugin runs nothing on your computer.

## What is sent, and where

Each question the assistant answers with EAP sends a tool call (the tool's
name and its arguments, such as a campaign or a member's name) to
`app.effectiveadvocacyproject.org`, and nowhere else. EAP records which tools
are used and one sentence summing up the purpose of each request — never
your conversations. Two tools send a note to the EAP team: `send_feedback`
(your own feedback, once you agree to send it, or a short report when a tool
fails or gives a result that looks wrong) and `rate_assessment` (when you say
one of EAP's assessments is right or wrong).

- Privacy policy: <https://app.effectiveadvocacyproject.org/privacy>
- Terms: <https://app.effectiveadvocacyproject.org/terms>
- Help: <https://app.effectiveadvocacyproject.org/help/claude>
- Contact: info@effectiveadvocacyproject.org

## Example questions

- "Who in the National Assembly votes with the campaign?"
- "What has the agriculture minister said since September?"
- "Which amendments deal with transition periods?"

This repository is generated from the EAP code base for each release.
