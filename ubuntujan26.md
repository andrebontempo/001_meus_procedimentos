Ingressar um computador com Ubuntu no Active Directory (AD) é uma tarefa comum em ambientes corporativos. Isso permite que o computador seja gerenciado pelas políticas do AD e que os usuários do AD façam login no sistema. Abaixo está um procedimento detalhado para realizar essa tarefa.

### Pré-requisitos
1. **Acesso de administrador** no computador Ubuntu.
2. **Credenciais de administrador** do Active Directory.
3. **Nome do domínio** do Active Directory.
4. **Servidor DNS** configurado corretamente para resolver o domínio do AD.

### Passo 1: Atualize o Sistema
Antes de começar, é uma boa prática atualizar o sistema para garantir que todos os pacotes estejam atualizados.

```bash
sudo apt update && sudo apt upgrade -y
```

### Passo 2: Instale os Pacotes Necessários
Você precisará instalar alguns pacotes para ingressar no AD. O pacote `realmd` e `sssd` são essenciais para isso.

```bash
sudo apt install realmd sssd sssd-tools libnss-sss libpam-sss adcli samba-common-bin
```

### Passo 3: Descobrir o Domínio
Antes de ingressar no domínio, você pode verificar se o domínio é descoberto corretamente.

```bash
realm discover seu.dominio.ad
```

Substitua `seu.dominio.ad` pelo nome do seu domínio Active Directory.

### Passo 4: Ingressar no Domínio
Agora, você pode ingressar no domínio usando o comando `realm join`.

```bash
sudo realm join -U administrador seu.dominio.ad
```

Substitua `administrador` pelo nome de usuário de um administrador do AD e `seu.dominio.ad` pelo nome do seu domínio.

### Passo 5: Verificar o Ingresso no Domínio
Após ingressar no domínio, você pode verificar se o computador foi adicionado corretamente.

```bash
realm list
```

Isso deve mostrar informações sobre o domínio ao qual o computador foi ingressado.

### Passo 6: Configurar o SSSD
O `sssd` (System Security Services Daemon) é responsável por gerenciar a autenticação e a autorização. Você pode precisar ajustar algumas configurações no arquivo de configuração do SSSD.

1. Abra o arquivo de configuração do SSSD:

   ```bash
   sudo nano /etc/sssd/sssd.conf
   ```

2. Certifique-se de que as seguintes linhas estão presentes e configuradas corretamente:

   ```ini
   [sssd]
   domains = seu.dominio.ad
   config_file_version = 2
   services = nss, pam

   [domain/seu.dominio.ad]
   ad_domain = seu.dominio.ad
   krb5_realm = SEU.DOMINIO.AD
   realmd_tags = manages-system joined-with-adcli
   cache_credentials = True
   id_provider = ad
   krb5_store_password_if_offline = True
   default_shell = /bin/bash
   ldap_id_mapping = True
   use_fully_qualified_names = False
   fallback_homedir = /home/%u
   access_provider = ad
   ```

3. Salve e feche o arquivo.

4. Reinicie o serviço SSSD:

   ```bash
   sudo systemctl restart sssd
   ```

### Passo 7: Permitir Logins de Usuários do AD
Por padrão, apenas usuários locais podem fazer login. Para permitir que usuários do AD façam login, você precisa ajustar as configurações do PAM.

1. Edite o arquivo de configuração do PAM:

   ```bash
   sudo nano /etc/pam.d/common-session
   ```

2. Adicione a seguinte linha no final do arquivo:

   ```bash
   session required pam_mkhomedir.so skel=/etc/skel/ umask=0022
   ```

3. Salve e feche o arquivo.

### Passo 8: Testar o Login
Agora, você pode testar o login de um usuário do AD.

1. Abra um novo terminal ou sessão.
2. Tente fazer login com um usuário do AD.

### Passo 9: Verificar o Computador no AD
Você pode verificar se o computador foi adicionado corretamente ao AD usando o `Active Directory Users and Computers` no servidor Windows.

### Passo 10: Configurações Adicionais (Opcional)
- **Permitir Acesso a Grupos Específicos**: Você pode configurar o acesso para grupos específicos do AD.

  ```bash
  sudo realm permit -g grupo_do_ad
  ```

- **Remover o Computador do Domínio**: Se precisar remover o computador do domínio, use o seguinte comando:

  ```bash
  sudo realm leave seu.dominio.ad
  ```

### Conclusão
Seguindo esses passos, você deve ser capaz de ingressar um computador Ubuntu no Active Directory com sucesso. Isso permitirá que os usuários do AD façam login no sistema e que o computador seja gerenciado pelas políticas do AD.
