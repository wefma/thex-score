FROM mcr.microsoft.com/devcontainers/typescript-node:22@sha256:27a2d19026714b1d8323c68a6e53ab29da4b59b463b6eb22971f6f6a4f77693f AS nodejs

RUN mkdir -p \
        /workspace/thex-score \
        /workspace/.pnpm-store \
        /home/node/.ssh \
        /home/node/.codex \
        /home/node/.vscode-server \
    && chown -R node:node \
        /workspace \
        /home/node/.ssh \
        /home/node/.codex \
        /home/node/.vscode-server \
    && printf '\nexport GPG_TTY=$(tty)\n' >> /etc/bash.bashrc

USER node