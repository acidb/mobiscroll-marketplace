# CLAUDE.md

`plugins/mobiscroll/` is the source of truth. The root `.mcp.json` and `skills/` are byte-identical mirrors so cursor.directory and skills.sh can find them — never edit them directly.

After changing anything under `plugins/mobiscroll/skills/` or `plugins/mobiscroll/.mcp.json`, resync the mirrors:

```bash
rm -rf skills && cp -r plugins/mobiscroll/skills skills && cp plugins/mobiscroll/.mcp.json .mcp.json
```
