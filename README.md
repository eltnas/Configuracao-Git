# Tutorial: Configuração de Chave SSH no GitHub

Este guia passo a passo aborda como gerar uma chave SSH na sua máquina local e vinculá-la à sua conta do GitHub para realizar operações como `push` e `pull` sem a necessidade de digitar senhas a cada ação.

---

## 1. Verificar chaves SSH existentes

Antes de criar uma chave nova, verifique se você já possui alguma chave gerada no seu sistema.

1. Abra o Terminal (no Linux/macOS) ou o Git Bash / PowerShell (no Windows).
2. Execute o comando:
   ```bash
   ls -al ~/.ssh
   ```
3. Verifique se existe algum arquivo listado como `id_ed25519.pub` ou `id_rsa.pub`. Se já possuir uma chave que deseja utilizar, você pode avançar para a **Etapa 3**.

---

## 2. Gerar uma nova chave SSH

O algoritmo **Ed25519** é o padrão recomendado atualmente por ser mais seguro e eficiente.

1. No terminal, execute o comando abaixo (substituindo pelo seu e-mail cadastrado no GitHub):
   ```bash
   ssh-keygen -t ed25519 -C "seu_email@exemplo.com"
   ```
2. Quando aparecer a mensagem `Enter file in which to save the key`, pressione **Enter** para aceitar a localização padrão (`~/.ssh/id_ed25519`).
3. Quando solicitado uma passphrase (`Enter passphrase`), você pode:
   * Digitar uma senha forte para proteção adicional da chave (recomendado).
   * Pressionar **Enter** duas vezes para deixar sem senha.

> **Verificação:** Execute `ls -al ~/.ssh` novamente e confirme se os arquivos `id_ed25519` (chave privada) e `id_ed25519.pub` (chave pública) foram criados.

---

## 3. Adicionar a chave SSH ao `ssh-agent`

Agora é necessário registrar a chave privada no agente SSH da sua máquina.

1. Inicie o `ssh-agent` em segundo plano:
   ```bash
   eval "$(ssh-agent -s)"
   ```
   > **Resposta esperada:** O terminal exibirá uma mensagem como `Agent pid 12345`.

2. Adicione sua chave privada ao agente:
   ```bash
   ssh-add ~/.ssh/id_ed25519
   ```
   > **Resposta esperada:** Se a chave possuir passphrase, ela será solicitada. Em seguida, surgirá a mensagem `Identity added`.

---

## 4. Copiar a chave pública

Você precisa copiar o conteúdo do arquivo **público** (`.pub`) para colar no GitHub.

* **No Linux (exibição direta no terminal):**
  ```bash
  cat ~/.ssh/id_ed25519.pub
  ```
  *(Selecione todo o texto exibido na tela, começando com `ssh-ed25519` até o seu e-mail, e copie).*

* **No Windows (Git Bash / PowerShell):**
  ```bash
  clip < ~/.ssh/id_ed25519.pub
  ```

* **No macOS:**
  ```bash
  pbcopy < ~/.ssh/id_ed25519.pub
  ```

---

## 5. Cadastrar a chave pública no GitHub

1. Acesse o site do [GitHub](https://github.com) e faça login na sua conta.
2. Clique na sua foto de perfil no canto superior direito e selecione **Settings** (Configurações).
3. Na barra lateral esquerda, clique em **SSH and GPG keys**.
4. Clique no botão verde **New SSH key**.
5. No campo **Title**, insira um nome descritivo para identificar a máquina (ex: *Notebook-Pessoal* ou *Desktop-Trabalho*).
6. Mantenha o campo **Key type** como *Authentication Key*.
7. No campo **Key**, cole a chave pública que você copiou na etapa anterior.
8. Clique em **Add SSH key** e confirme sua senha do GitHub, se solicitado.

---

## 6. Testar a conexão SSH

Para garantir que a chave foi configurada corretamente:

1. No terminal, execute:
   ```bash
   ssh -T git@github.com
   ```
2. Se for a primeira vez conectando a este host, você verá um aviso de autenticidade:
   ```text
   The authenticity of host 'github.com (...)' can't be established.
   ED25519 key fingerprint is SHA256:+DiYwhGOkaB3ILUlkE0M4D6588z87...
   Are you sure you want to continue connecting (yes/no/[fingerprint])?
   ```
3. Digite `yes` e pressione **Enter**.

> **Resultado Esperado:**
> `Hi username! You've successfully authenticated, but GitHub does not provide shell access.`