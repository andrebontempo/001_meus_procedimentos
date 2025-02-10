# Instalação e Configuração do MariaDB no Debian 12

Este guia detalha o processo para instalar e configurar o MySQL no Debian 12, criando um usuário administrador para gerenciar o banco de dados.

## Passo 1: Atualizar os Pacotes do Sistema
Antes de começar, certifique-se de que todos os pacotes do sistema estejam atualizados:
```bash
apt install sudo
```
```bash
usermod -aG sudo andre
```
```bash
apt update && sudo apt upgrade -y
```
Agora faça a conexão via SSH, isto facilita as configurações pois usa o terminal local.
```bash
ssh andre@<servidor>
```
## Passo 2: Instalar o MySQL Server
Instale o pacote do MySQL Server usando o gerenciador de pacotes APT:
PARA MARIADB
```bash
apt install mariadb-server -y
```
OU PARA MySQL
```bash
sudo apt install mysql-server -y
```

> **Nota:** O Debian 12 utiliza os repositórios oficiais do MySQL. O pacote instalado será a versão mais estável disponível.

## Passo 3: Verificar o Status do MySQL
Certifique-se de que o serviço mariadb está ativo e configurado para iniciar automaticamente com o sistema:
```bash
systemctl status mariadb
```
Se não estiver ativo, inicie e habilite o serviço:
```bash
systemctl start mariadb
systemctl enable mariadb
```
## Passo 4: Configurar o mariaDB com `mysql_secure_installation`
Execute o script de configuração inicial para melhorar a segurança do MySQL:
```bash
mysql_secure_installation
```
### Durante o processo:
1. Configure ou altere a senha do usuário `root`.
2. Responda `Y` (Yes) para:
   - Remover usuários anônimos.
   - Restringir o acesso remoto ao usuário `root`.
   - Remover o banco de dados de teste.
   - Recarregar as tabelas de privilégios.

## Passo 5: Acessar o MySQL como Root
Acesse o console do MySQL com o usuário `root` senha `1234`:
```bash
mysql -u root -p
```
Insira a senha configurada no passo anterior.

## Passo 6: Criar um Usuário Administrador
No console do MySQL, crie um novo usuário com permissões administrativas completas, inclusive para acesso REMOTO:
```sql
CREATE USER 'andredb'@'%' IDENTIFIED BY 'andredb1234';
GRANT ALL PRIVILEGES ON *.* TO 'andredb'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
EXIT;
```
> **Substitua** `adm_user` pelo nome desejado para o usuário e `sua_senha_segura` por uma senha forte.

## Passo 7: Configurar o Acesso Remoto

Primeiro, se não tiver, fixe o IP do servidor

```bash
nano /etc/network/interfaces
```
```bash
auto eth0
iface eth0 inet static
    address 192.168.0.69
    netmask 255.255.255.0
    gateway 192.168.0.1
    dns-nameservers 8.8.8.8 8.8.4.4
```

Para permitir o acesso remoto ao MySQL, edite o arquivo de configuração:
No maria DB é este o arquivo:
```bash
nano /etc/mysql/mariadb.conf.d/50-server.cnf
```
ou, dependendo da sua instalação:
```bash
nano /etc/my.cnf
```
Para o MySQL
```bash
nano /etc/mysql/mysql.conf.d/mysqld.cnf
```
Encontre a linha abaixo e comente-a (adicione `#` no início):
ou
Encontre a linha abaixo e substitua para 0.0.0.0
```ini
bind-address = 127.0.0.1
```
Para conceder acesso remoto para um usuário:
```bash
GRANT ALL PRIVILEGES ON *.* TO 'andredb'@'%' IDENTIFIED BY 'andredb1234';
FLUSH PRIVILEGES;
```
Para mostrar permissões de um usuário
```bash
SHOW GRANTS FOR 'andredb'@'%';
```
Reinicie o serviço mariaDB para aplicar as alterações:
```bash
systemctl restart mariadb
```
Certifique-se de que as regras do firewall permitem conexões à porta padrão do MariaDB (3306):
```bash
ufw allow 3306
```
## Passo 8: Testar o Acesso Remoto
De outro computador na mesma rede ou com acesso ao servidor, teste o acesso remoto ao MariaDB:
```bash
mysql -u andredb -p -h 192.168.0.69
```
> **Nota:** Substitua `<IP_DO_SERVIDOR>` pelo endereço IP do servidor MySQL. 
Se o acesso funcionar, o MariaDB está corretamente configurado para conexões remotas.

#### c) **Abrir Porta no Firewall**
1. Permita a porta 3306 (padrão do MariaDB):
```bash
ufw allow 3306/tcp
```
#### b) **Usando um Cliente GUI**
Você pode usar ferramentas como **DBeaver**, **MySQL Workbench** ou **HeidiSQL**:
1. Configure uma nova conexão com as seguintes informações:
   - **Host**: 127.0.0.1
   - **Port**: 3307 (ou outra porta local usada no túnel)
   - **Username**: Usuário do MariaDB.
   - **Password**: Senha do MariaDB.

---

### 4. **Testes Adicionais**

#### a) **Ping na Porta do MariaDB**
No servidor local, teste se o MariaDB está acessível:
```bash
telnet 127.0.0.1 3306
```

#### b) **Teste de Firewall**
Garanta que a porta está acessível externamente:
```bash
nc -zv ip-do-servidor 3306
```
#### c) **Logs do MariaDB**
Se houver problemas, verifique os logs:
```bash
sudo tail -f /var/log/mysql/error.log
```
---
### 5. **Boas Práticas de Segurança**
- Use autenticação SSH por chave para evitar senhas no túnel SSH.
- Configure um firewall para permitir conexões apenas de IPs autorizados.
- Utilize SSL para proteger conexões diretas ao MariaDB (opcional).
Esses passos devem garantir acesso remoto ao servidor MariaDB via SSH com testes eficazes de conectividade.
ATUALIZADO


