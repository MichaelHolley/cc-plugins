# Claude Code Plugins

Personal Claude Code marketplace - skills, MCPs, and agents for shipping code faster.

Supported in any harness with marketplace like Claude-Code, oh-my-pi, ...

## Install options

The skills install gets only my own skills from [skills/](skills/). The marketplace install gets the same skills as the `dev-workflows` plugin, plus a set of third-party skills that you can install one by one.

### Skills install

Install all my skills in [skills/](skills/):

```sh
npx skills add michaelholley/cc-plugins/skills
```

or selected skills with:

```sh
npx skills add michaelholley/cc-plugins/skills --skill='the-skill-name'
```

### Claude Marketplace install

Gets the same skills as the `dev-workflows` plugin. It also lists a set of third-party skills that point to their original repos. Browse them with `/plugin` after adding the marketplace.

Add the marketplace, then install the plugins you want:

```sh
/plugin marketplace add MichaelHolley/cc-plugins
/plugin install dev-workflows@michaelholley
```
