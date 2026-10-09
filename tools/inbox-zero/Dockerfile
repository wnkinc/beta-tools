# Upstream's prebuilt image, unmodified, plus the one optional package its
# experimental `claude-code` LLM provider loads at runtime (CLI_LLM_ENABLED=true).
# Version pinned to what upstream's docs/hosting/llm-setup.mdx names; it pulls in
# the Claude Agent SDK, which ships the Claude Code binary for this platform.
FROM ghcr.io/elie222/inbox-zero@sha256:194c82400dc0603d33f9fc605e2b1cbe26dc266deede09e1c48dee2cff2715b1

# Upstream turns Claude Code's sandbox on for tool calls, and the Agent SDK then
# refuses to start unless its dependencies are present. They're only exercised if
# Claude runs a shell command, which inbox-zero never allows (MCP tools only; the
# image has no shell Claude Code accepts), so Docker's default profile is fine.
RUN apk add --no-cache bubblewrap socat

# Installed in its own prefix (the app's node_modules is a pnpm standalone trace,
# not something npm can add to), then linked where the app's bare import resolves.
RUN npm install --prefix /opt/claude-provider --omit=dev --no-audit --no-fund \
      ai-sdk-provider-claude-code@4.3.1 zod@4 \
 && ln -s /opt/claude-provider/node_modules/ai-sdk-provider-claude-code \
      /app/node_modules/ai-sdk-provider-claude-code
