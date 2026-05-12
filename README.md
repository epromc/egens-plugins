# eGens — Documentation

[![Modrinth](https://img.shields.io/badge/Modrinth-eGens-00AF5C?logo=modrinth&logoColor=white)](https://modrinth.com/plugin/egens)
[![Paper](https://img.shields.io/badge/Paper-1.21.x-1f7be8)](https://papermc.io/)
[![Folia](https://img.shields.io/badge/Folia-supported-7c5cff)](https://papermc.io/software/folia)
[![Java](https://img.shields.io/badge/Java-21-f89820?logo=openjdk&logoColor=white)](https://adoptium.net/temurin/releases/?version=21)
[![Donate](https://img.shields.io/badge/ko--fi-Tip%20the%20author-ff5e5b?logo=ko-fi&logoColor=white)](https://ko-fi.com/epromite/tip)

This repository is the **operator-facing documentation** for the
[eGens](https://modrinth.com/plugin/egens) plugin — a next-gen Gens /
Tycoon plugin for Paper and Folia servers running Minecraft 1.21.x.
The plugin source itself lives elsewhere and is not part of this
repository.

## Read the docs

The full table of contents is in [`docs/index.md`](docs/index.md).
Quick links:

- [Install](docs/getting-started/install.md)
- [Permissions](docs/getting-started/permissions.md)
- [Storage (MySQL / SQLite)](docs/getting-started/storage.md)
- [Commands](docs/commands/index.md)
- [Config reference](docs/config/config-yml.md)
- [Premade tier chains](docs/generators/premade-chains.md)
- [Drops registry](docs/generators/drops-registry.md)
- [Integrations](docs/integrations/vault.md)
- [Troubleshooting](docs/operations/troubleshooting.md)
- [FAQ](docs/operations/faq.md)

The same content is also mirrored to the
[GitHub Wiki](../../wiki) — the `/docs/` markdown is the source of
truth and the Wiki is auto-synced from `main` by
`.github/workflows/wiki-sync.yml`.

## Support development

eGens is built by [@epromite](https://github.com/epromite). If the
plugin saves you time on your server, a one-time tip on Ko-fi is the
single best way to keep it maintained:

[![Tip on Ko-fi](https://img.shields.io/badge/Tip%20on%20ko--fi-Support%20eGens-ff5e5b?logo=ko-fi&logoColor=white&style=for-the-badge)](https://ko-fi.com/epromite/tip)

## Need help?

- **Bug reports & feature requests** — open an issue on the plugin's
  Modrinth project page.
- **General chat** — join the eGens community Discord:
  <https://discord.gg/pM4atmCVS6>.

## License

The documentation in this repository is released under the
[MIT License](LICENSE). The eGens plugin itself is distributed under
its own license — see the plugin's download page for details.
