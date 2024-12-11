---
Date: 2024-07-27
---
---
Kubernetes 中，安全性是一個非常重要的方面，因為它涉及到保護叢集的整體運行和其中的應用程式。
### 1. 認證（Authentication）
認證是確定用戶或應用程式的身份。Kubernetes 支持多種認證方式，包括：

- **憑證（Certificates）**：常見於內部通信和 kubelet 認證。
- **OAuth Tokens**：集成第三方身份提供商（如 Google、GitHub 等）。
- **靜態 Token 文件**：簡單但不推薦用於生產環境。
- **OpenID Connect（OIDC）**：常見於集成外部身份管理系統。

### 2. 授權（Authorization）
授權是確定已認證的用戶或應用程式可以執行哪些操作。Kubernetes 支持多種授權方式，包括：

- **RBAC（Role-Based Access Control）**：基於角色的訪問控制，是 Kubernetes 中最常用的授權方式。通過定義角色（Roles）和角色綁定（RoleBindings），可以精細控制用戶和應用程式的訪問權限。
- **ABAC（Attribute-Based Access Control）**：基於屬性的訪問控制。
- **Webhook**：將授權決策委託給外部服務。

### 3. 網絡策略（Network Policies）
網絡策略用於控制 Pod 之間以及 Pod 與外部之間的網絡通信。通過配置網絡策略，可以定義哪些 Pod 可以相互通信，哪些 Pod 可以訪問外部服務。