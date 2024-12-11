---
Date: 2024-10-27
---
首先原本在你的專案就有container了，然後你可以在本地創建一個`.devcontainer/devcontainer.json`的設定檔案，讓你的容器可以連線進去。
### Example
**docker-compose.yml**

```yml
version: '3'

services:
  nextjs-app:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    volumes:
      - .:/app
      - /app/node_modules
      - /app/.next
    environment:
      - NODE_ENV=development
    command: npm run dev
```

**.devcontainer/devcontainer.json**

```json
{
  "name": "Next.js 开发环境",
  "dockerComposeFile": ["../docker-compose.yml"],
  "service": "nextjs-app",
  "workspaceFolder": "/app",
  "settings": {
    "terminal.integrated.shell.linux": "/bin/bash"
  },
  "extensions": [
    "dbaeumer.vscode-eslint",
    "esbenp.prettier-vscode",
    "dsznajder.es7-react-js-snippets",
    "bradlc.vscode-tailwindcss"
  ],
  "postCreateCommand": "npm install",
  "remoteUser": "node"
}

```

這樣設定都完成了，你可以在`VSCode`中輸入 `command+shift+p` 輸入 `Remote-Containers: Open Folder in Container`。

之後就選擇你的專案路徑，就大功告成！！🎉🎉🎉
