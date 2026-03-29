# 📌 Uso de Pre-Commit Hooks para Detecção de Secrets e Validação de Terraform

## 📋 **Resumo**

Esta documentação explica como utilizar **pre-commit hooks** para:

* 🔐 **Detectar segredos** (API keys, senhas e outras informações
  sensíveis) antes que sejam enviadas para o repositório
* 🌱 **Validar formatação e realizar linting de segurança** em código
  Terraform

### 🔗 Documentação de Referência

* [**Yelp/detect-secrets**](https://github.com/Yelp/detect-secrets) –
  ferramenta para detectar e prevenir segredos em código
* [**pre-commit-terraform**](https://github.com/antonbabenko/pre-commit-terraform)
  – hooks relacionados ao Terraform
* [**pre-commit**](https://github.com/pre-commit/pre-commit) –
  framework para gerenciar hooks multi-linguagem

## 🚀 Instalação

> **Observação:** Instruções válidas para ambientes **Linux/WSL**.
> Para outros sistemas operacionais, consulte sua documentação.

Execute os seguintes comandos para configurar o ambiente local:

```bash
pip install detect-secrets
pip install pre-commit
curl -s https://raw.githubusercontent.com/terraform-linters/tflint/master/install_linux.sh \
  | bash
```

> O comando acima instala o **tflint**, usado na validação de Terraform.

## 🗂️ Configuração do Repositório

1. Clone o repositório desejado
2. Acesse a pasta do repositório
3. Crie os arquivos `.pre-commit-config.yaml` e `scan/.tflint.hcl`
   utilizando os exemplos abaixo
4. Instale os hooks:

```bash
pre-commit install
```

## 🧩 Arquivos de Configuração

### `.pre-commit-config.yaml`

```yaml
---
repos:
  - repo: https://github.com/Yelp/detect-secrets
    rev: v1.5.0
    hooks:
      - id: detect-secrets
        exclude: package.lock.json
  
  - repo: https://github.com/antonbabenko/pre-commit-terraform
    rev: v1.99.1
    hooks:
      - id: terraform_fmt
        args:
          - --args=-recursive
      - id: terraform_tflint
        args:
          - --args=--call-module-type=none
          - --args=--config=__GIT_WORKING_DIR__/scan/.tflint.hcl
```

### `scan/.tflint.hcl`

```hcl
config {
  call_module_type    = "all"
  force               = true
  disabled_by_default = false
}

plugin "terraform" {
  enabled = true
  preset  = "all"
}
```

## 🔍 Como Funciona

Após a configuração, **toda vez que você rodar**:

```bash
git commit
```

os hooks serão executados automaticamente, verificando:

* Se você está tentando subir **segredos vazados**
* Se o seu código Terraform está **formatado corretamente**
* Se há **problemas de segurança** no Terraform

👉 Isso garante que **erros e segredos nunca sejam enviados** para o
repositório por acidente.

### 📥 **Primeira Execução**

Na primeira vez que você fizer um `git commit`, o **pre-commit** irá:

* Baixar todos os hooks necessários
* Configurar o `detect-secrets`
* Configurar os hooks de Terraform (`terraform_fmt` e `tflint`)
* Rodar as verificações iniciais

➡️ **Essa primeira execução pode demorar um pouco**, pois envolve fazer
download e preparar o ambiente.

### ⚡ **Execuções seguintes**

A partir do segundo commit:

* Os hooks **já estarão instalados**
* **Nenhum novo download será necessário**
* Apenas as verificações serão executadas

➡️ **Isso torna as próximas execuções muito mais rápidas**, geralmente
levando apenas alguns segundos.

## 🛠️ Testes Manuais

Você pode rodar os hooks manualmente com:

### Rodar todos os hooks

```bash
pre-commit run --all-files
```

### Rodar hooks específicos

```bash
pre-commit run detect-secrets --all-files
pre-commit run terraform_fmt --all-files
pre-commit run terraform_tflint --all-files
```
