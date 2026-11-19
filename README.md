# Ambiente de desenvolvimento

<div align="center">
  <a href="https://github.com/bfeliano/dev-environment">
    <img src="config/logo.png" width="80" height="80" alt="Repo Logo">
  </a>
  <p align="center">
    Este guia reúne os passos para configurar um computador Windows para
    desenvolvimento com Ubuntu no WSL.
  </p>
</div>

## Sobre o Projeto

Criei este repositório para disponibilizar um guia centralizado de configuração
do Windows para ambientes de desenvolvimento.

As instruções refletem minhas preferências e configurações pessoais.

Não me responsabilizo por eventuais problemas decorrentes do download ou da
instalação dos softwares recomendados. Siga estas instruções por sua conta e
risco e certifique-se de revisar e compreender cada etapa antes de prosseguir.

## Primeiros Passos

Aqui você encontrará os softwares, ferramentas e configurações que utilizo,
com foco no WSL (Windows Subsystem for Linux).

### Configuração do Windows

#### Instalar o WSL

Para instalar uma versão específica do Ubuntu, execute:

```powershell
wsl --install -d Ubuntu-24.04
```

Você também pode consultar outras distribuições Linux disponíveis para
instalação no WSL utilizando o comando abaixo:

```powershell
wsl --list --online
```

Recomendo especificar a versão do Ubuntu durante a instalação para garantir
um ambiente de desenvolvimento consistente e evitar mudanças inesperadas
na versão instalada.

#### Instalar os programas

Use Ctrl + Clique para abrir os sites em novas abas.

* [Brave Browser](https://brave.com/download/)
* [Visual Studio Code](https://code.visualstudio.com/download)

### Configurar o VS Code

1. Instale o tema [Monokai Pro](https://marketplace.visualstudio.com/items?itemName=monokai.theme-monokai-pro-vscode)
e selecione-o em `Preferences: Color Theme`.
2. Selecione o tema de ícones **Seti** em `Preferences: File Icon Theme`.
3. Instale as extensões:
   * [WSL](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl)
   * [GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)
   * [HashiCorp Terraform](https://marketplace.visualstudio.com/items?itemName=HashiCorp.terraform)
   * [Markdown Lint](https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint)
   * [GitHub Copilot](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot)
4. Abra `Preferences: Open User Settings (JSON)` e use estas configurações:

```json
{
    "editor.wordWrap": "on",
    "editor.formatOnSave": true,
    "[markdown]": {
        "editor.formatOnSave": false
    },
    "explorer.autoReveal": false,
    "workbench.colorTheme": "Monokai Pro",
    "claudeCode.preferredLocation": "panel",
    "extensions.supportAgentsWindow": {
        "monokai.theme-monokai-pro-vscode": true
    },
    "terminal.integrated.initialHint": false,
    "terminal.integrated.enableMultiLinePasteWarning": "never",
    "security.workspace.trust.untrustedFiles": "open",
    "security.allowedUNCHosts": [
        "wsl.localhost"
    ]
}
```

### Configuração do Ubuntu no WSL

**Execute os comandos abaixo no terminal do Ubuntu**, exceto quando estiver
indicado PowerShell.

### Atualizar o sistema

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install -y git curl ca-certificates unzip zsh
```

### Definir o Zsh como padrão

```bash
sudo chsh -s "$(command -v zsh)" "$USER"
getent passwd "$USER"
```

O resultado de `getent` deve terminar em `/usr/bin/zsh` ou `/bin/zsh`.

**É necessário encerrar e reiniciar o Ubuntu para aplicar a mudança.**
Feche o terminal do Ubuntu e execute no **PowerShell**:

```powershell
wsl --terminate Ubuntu-24.04
wsl
```

Confirme no terminal do Ubuntu:

```bash
ps -p $$ -o comm=
```

O resultado deve ser `zsh`.

### Instalar o Oh My Zsh

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

Configure os plugins `git` e `kubectl` no arquivo `~/.zshrc`:

```bash
sed -i 's/^plugins=(.*)/plugins=(git kubectl)/' ~/.zshrc
source ~/.zshrc
```

A configuração deve conter:

```zsh
plugins=(git kubectl)
```

### Instalar o kubectl

Os comandos abaixo instalam a versão estável do cliente Kubernetes para
a arquitetura do Ubuntu:

```bash
KUBECTL_VERSION="$(curl -fsSL https://dl.k8s.io/release/stable.txt)"
KUBECTL_ARCH="$(dpkg --print-architecture)"

curl -fLO "https://dl.k8s.io/release/${KUBECTL_VERSION}/bin/linux/${KUBECTL_ARCH}/kubectl"
curl -fLO "https://dl.k8s.io/release/${KUBECTL_VERSION}/bin/linux/${KUBECTL_ARCH}/kubectl.sha256"

echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
rm kubectl kubectl.sha256
kubectl version --client
```

A verificação de integridade deve mostrar `kubectl: OK`. Para acessar clusters,
configure as credenciais no `~/.kube/config`. A versão do `kubectl` deve estar
no máximo uma versão secundária acima ou abaixo da versão do cluster.

[Documentação oficial do kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/).

## 3. Outras ferramentas no WSL

### AWS CLI

Instale a AWS CLI v2 seguindo a [documentação oficial para Linux](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html).

```bash
aws --version
```

### Terraform

Instale o Terraform seguindo a [documentação oficial para Ubuntu/Debian](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli).

```bash
terraform version
```

### Node.js e npm

```bash
sudo apt install -y nodejs npm
node --version
npm --version
```

### EM DESENVOLVIMENTO! - Esta ainda não é a versão final deste documento
