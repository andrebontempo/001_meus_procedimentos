### Procedimento para Ingressar um Computador com Ubuntu 24.04 no Active Directory

Este guia descreve os passos necessários para ingressar um computador com **Ubuntu 24.04** em um domínio **Active Directory (AD)**. O procedimento utiliza o `realmd` e ferramentas relacionadas para facilitar a integração.

---

#### **Pré-requisitos**

1. **Credenciais de um usuário do Active Directory** com permissões para ingressar máquinas no domínio.
2. A máquina deve estar com o **nome do host configurado corretamente**.
   - Para verificar o nome:  
     ```bash
     hostnamectl
     ```
   - Para alterar o nome, use:  
     ```bash
     sudo hostnamectl set-hostname novo-hostname
     ```

3. Configuração de rede:  
   - O servidor DNS da rede deve apontar para o controlador de domínio do AD. Para verificar:  
     ```bash
     cat /etc/resolv.conf
     ```
   - Adicione o DNS do AD no arquivo `/etc/netplan/*.yaml` e aplique as configurações com:  
     ```bash
     sudo netplan apply
     ```

4. **Pacotes necessários instalados.** 

---

#### **1. Instalar os Pacotes Necessários**

Execute o seguinte comando para instalar os pacotes necessários:

```bash
sudo apt update && sudo apt install -y realmd sssd sssd-tools libnss-sss libpam-sss adcli krb5-user samba-common-bin oddjob oddjob-mkhomedir packagekit
```

Durante a instalação do pacote `krb5-user`, será solicitado que você insira o **domínio do Active Directory**. Informe o nome do domínio (exemplo: `empresa.local`).

---

#### **2. Descobrir o Domínio do Active Directory**

Execute o comando abaixo para verificar se o domínio está acessível:

```bash
realm discover dominio.local
```

Substitua `dominio.local` pelo nome do domínio da sua empresa (exemplo: `empresa.local`).  
A saída esperada deve incluir informações sobre o domínio, como:
```plaintext
empresa.local
  type: kerberos
  realm-name: EMPRESA.LOCAL
  domain-name: empresa.local
  configured: no
  server-software: active-directory
  client-software: sssd
  required-package: sssd-tools
```

Se a saída for válida, continue para o próximo passo.

---

#### **3. Ingressar no Domínio**

Use o comando `realm join` para ingressar no domínio. Substitua `dominio.local` pelo nome do domínio e forneça credenciais válidas:

```bash
sudo realm join --user=Administrador dominio.local
```

Após executar o comando, será solicitado que você insira a senha do usuário de domínio (`Administrador` no exemplo).

Se o ingresso for bem-sucedido, você verá uma mensagem como:  
```plaintext
Successfully enrolled machine in realm
```

---

#### **4. Verificar o Ingresso no Domínio**

Verifique se o computador está configurado no domínio:

```bash
realm list
```

A saída deve mostrar o domínio e informações sobre a configuração.

---

#### **5. Configurar o SSSD para Logins de Domínio**

Edite o arquivo `/etc/sssd/sssd.conf` para garantir que as configurações do SSSD estão corretas:

```bash
sudo nano /etc/sssd/sssd.conf
```

Certifique-se de que o conteúdo inclui as seguintes linhas (substitua `DOMINIO.LOCAL` pelo domínio correto):

```ini
[sssd]
services = nss, pam
domains = DOMINIO.LOCAL
config_file_version = 2

[domain/DOMINIO.LOCAL]
id_provider = ad
auth_provider = ad
chpass_provider = ad
access_provider = ad
cache_credentials = true
override_homedir = /home/%d/%u
default_shell = /bin/bash
use_fully_qualified_names = False
```

Após editar o arquivo, defina as permissões corretas:

```bash
sudo chmod 600 /etc/sssd/sssd.conf
```

Reinicie o serviço SSSD:

```bash
sudo systemctl restart sssd
```

---

#### **6. Configurar Criação Automática de Diretórios Home**

Certifique-se de que o módulo `oddjob-mkhomedir` está ativo para criar automaticamente o diretório home de usuários de domínio no primeiro login:

```bash
sudo pam-auth-update
```

Na interface exibida, certifique-se de que a opção **Create home directory on login** está marcada.

---

#### **7. Testar Login com Usuário de Domínio**

Agora, teste fazer login com um usuário do domínio. Você pode alternar para o usuário via terminal:

```bash
su - nome_usuario@dominio.local
```

Ou tente fazer login na interface gráfica usando as credenciais do domínio.

---

#### **8. Configurar Permissões de Acesso (Opcional)**

Por padrão, todos os usuários do domínio poderão acessar a máquina. Para restringir o acesso a um grupo específico do Active Directory:

```bash
sudo realm permit -g GrupoDoAD
```

Substitua `GrupoDoAD` pelo nome do grupo desejado.

---

#### **9. Desingressar do Domínio (Se Necessário)**

Se for necessário remover o computador do domínio, execute:

```bash
sudo realm leave dominio.local
```

---

#### **Conclusão**

Após seguir esses passos, o computador com Ubuntu 24.04 estará integrado ao domínio Active Directory, permitindo logins com contas do domínio e aplicando as políticas definidas.
