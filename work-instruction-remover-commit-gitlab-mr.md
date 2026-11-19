# Work Instruction: Remover um commit de um Merge Request no GitLab

## Objetivo

Remover um commit enviado por engano para uma branch que já possui um Merge Request aberto no GitLab.

> O Merge Request representa a diferença entre a branch de origem e a branch de destino. Ao remover o commit da branch remota, o GitLab atualiza o MR automaticamente.

## Pré-requisitos

- Estar na branch usada pelo Merge Request.
- Ter o repositório atualizado localmente.
- Confirmar que nenhum colega enviou alterações novas para a mesma branch.
- Ter permissão para executar `force push` na branch.

## 1. Confirmar a branch atual

```bash
git branch --show-current
```

Se necessário, acesse a branch correta:

```bash
git switch <nome-da-branch>
```

## 2. Atualizar as referências remotas

```bash
git fetch origin
```

## 3. Verificar os últimos commits

```bash
git log --oneline --decorate -10
```

Exemplo:

```text
abc1234 commit enviado por engano
def5678 alteração correta do MR
```

## Cenário A: o commit incorreto é o último commit

### Opção recomendada: salvar o commit antes de removê-lo

Crie uma branch de backup apontando para o estado atual:

```bash
git branch backup-commit-removido
```

Depois, remova o último commit da branch do MR:

```bash
git reset --hard HEAD~1
```

Atualize a branch remota:

```bash
git push --force-with-lease origin HEAD
```

O GitLab atualizará o Merge Request automaticamente.

## Cenário B: o commit incorreto não é o último

Identifique quantos commits precisam aparecer no rebase. Por exemplo, para revisar os últimos cinco commits:

```bash
git rebase -i HEAD~5
```

O editor mostrará algo parecido com:

```text
pick def5678 alteração correta do MR
pick abc1234 commit enviado por engano
pick ghi9012 outra alteração correta
```

Altere `pick` para `drop` no commit que deve ser removido:

```text
pick def5678 alteração correta do MR
drop abc1234 commit enviado por engano
pick ghi9012 outra alteração correta
```

Salve e feche o editor. Depois, atualize a branch remota:

```bash
git push --force-with-lease origin HEAD
```

## Cenário C: remover as alterações, mas manter o histórico do commit

Se a branch for compartilhada ou não permitir `force push`, use `git revert`:

```bash
git revert <hash-do-commit>
git push origin HEAD
```

Isso cria um novo commit que desfaz as alterações. O commit original continuará aparecendo no histórico e no MR.

## Validação

Confirme o histórico local:

```bash
git log --oneline --decorate -10
```

Compare a branch local com a branch remota:

```bash
git status
git log --oneline origin/$(git branch --show-current)..HEAD
git log --oneline HEAD..origin/$(git branch --show-current)
```

Depois, abra o Merge Request no GitLab e valide:

- O commit incorreto não aparece mais na lista de commits.
- As alterações corretas continuam no diff.
- A pipeline foi executada novamente, se aplicável.

## Recuperação em caso de erro

O Git normalmente mantém os commits removidos temporariamente no `reflog`:

```bash
git reflog
```

Localize o hash anterior e restaure a branch:

```bash
git reset --hard <hash-anterior>
git push --force-with-lease origin HEAD
```

Se a branch de backup foi criada, também é possível restaurar a partir dela:

```bash
git reset --hard backup-commit-removido
git push --force-with-lease origin HEAD
```

## Cuidados importantes

- Use `--force-with-lease`, não `--force`. Ele evita sobrescrever mudanças remotas que não existem localmente.
- Não execute `git reset --hard` com arquivos locais não salvos. Esse comando descarta alterações não commitadas.
- Em branch compartilhada, avise os demais colaboradores antes de reescrever o histórico.
- Se a branch for protegida no GitLab, o `force push` pode estar bloqueado. Nesse caso, use `git revert` ou ajuste a branch conforme o processo do projeto.

## Resumo rápido

Para remover o último commit, preservando um backup:

```bash
git branch backup-commit-removido
git reset --hard HEAD~1
git push --force-with-lease origin HEAD
```
