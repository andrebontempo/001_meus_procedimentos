Para garantir que **somente o Chrony** cuide da sincronização de tempo no seu **Linux Mint**, siga os passos abaixo para desativar outros serviços NTP e garantir que o Chrony esteja ativo e configurado corretamente.

---

## **1. Parar e Desativar Outros Serviços de NTP**
Seu sistema pode estar rodando o `systemd-timesyncd` ou o `ntpd`. Vamos desativá-los:

### **1.1. Parar o `systemd-timesyncd` (se estiver rodando)**
```bash
sudo systemctl stop systemd-timesyncd
sudo systemctl disable systemd-timesyncd
```
Para evitar que ele seja iniciado automaticamente:
```bash
sudo systemctl mask systemd-timesyncd
```
Isso impede que ele seja ativado por outros serviços.

---

### **1.2. Remover o `ntpd` (se estiver instalado)**
Se você usava o `ntpd`, remova-o para evitar conflitos:
```bash
sudo apt remove --purge ntp -y
```

---

## **2. Configurar o Chrony para Controlar o Tempo**
### **2.1. Garantir que o Chrony esteja instalado**
Se o Chrony não estiver instalado, instale com:
```bash
sudo apt install chrony -y
```

### **2.2. Editar o arquivo de configuração**
Abra o arquivo `/etc/chrony/chrony.conf`:
```bash
sudo nano /etc/chrony/chrony.conf
```
- **Adicione seus servidores NTP do Active Directory**, por exemplo:
  ```
  server dc1.empresa.br iburst
  server dc2.empresa.br iburst
  server time.windows.com iburst
  ```
  (Substitua pelos servidores NTP do seu AD)

- **Remova ou comente os servidores padrão do Ubuntu**:
  ```bash
  # pool ntp.ubuntu.com        iburst maxsources 4
  # pool 0.ubuntu.pool.ntp.org iburst maxsources 1
  # pool 1.ubuntu.pool.ntp.org iburst maxsources 1
  # pool 2.ubuntu.pool.ntp.org iburst maxsources 2
  ```

- **Salvar e sair:**  
  Pressione `Ctrl + X`, depois `Y` e `Enter`.

---

## **3. Ativar e Reiniciar o Chrony**
Agora, certifique-se de que o **Chrony** está rodando e será iniciado automaticamente:

```bash
sudo systemctl restart chronyd
sudo systemctl enable chronyd
```

Verifique o status:
```bash
sudo systemctl status chronyd
```
A saída deve mostrar algo como **"active (running)"**.

---

## **4. Testar a Sincronização**
Verifique se o Chrony está sincronizando corretamente com os servidores do Active Directory:
```bash
chronyc sources -v
```
Saída esperada:
```
MS Name/IP address    Stratum Poll Reach LastRx Last sample
===============================================================================
^* dc1.empresa.br          2   10   377   45    -0.123  0.045
^+ dc2.empresa.br          2   10   377   47    -0.150  0.050
```
- **`*`** indica o servidor principal em uso.
- **`+`** mostra servidores alternativos.

Caso o Chrony ainda não esteja sincronizado corretamente, force a sincronização imediata:
```bash
sudo chronyc makestep
```

---

## **Conclusão**
Agora, **somente o Chrony** estará gerenciando a sincronização de tempo no seu Linux Mint. 🚀
