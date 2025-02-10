#### Para permitir que suas estações **Ubuntu 24.04 LTS** e **Linux Mint Xia 22.1** executem `apt update` e `apt upgrade` passando pelo **proxy autenticado**, siga os passos abaixo:

---

## Configurar o Proxy no Sistema

### 1️⃣ **Configurar o Proxy no Sistema**

Edite o arquivo de configuração global do proxy:

```bash
sudo nano /etc/environment
```

Adicione as seguintes linhas, substituindo com seus dados:

```bash
http_proxy="http://usuario:senha@proxy.empresa.com:porta/"
https_proxy="http://usuario:senha@proxy.empresa.com:porta/"
ftp_proxy="http://usuario:senha@proxy.empresa.com:porta/"
no_proxy="127.0.0.1,localhost,.empresa.com"
```

Se seu proxy usa domínios, em vez de IPs, adapte o `no_proxy`.

Salve (`CTRL + X`, `Y`, `Enter`).

Carregue as configurações:

```bash
source /etc/environment
```

---

### 2️⃣ **Configurar o Proxy para o APT**

Crie um arquivo específico para o APT:

```bash
sudo nano /etc/apt/apt.conf.d/95proxies
```

Adicione:

```bash
Acquire::http::Proxy "http://usuario:senha@proxy.empresa.com:porta/";
Acquire::https::Proxy "http://usuario:senha@proxy.empresa.com:porta/";
```

Salve (`CTRL + X`, `Y`, `Enter`).

---

### 3️⃣ **Configurar o Proxy para o `wget` e `curl`**

Crie um arquivo de configuração para o `wget`:

```bash
sudo nano /etc/wgetrc
```

Adicione:

```bash
http_proxy = http://usuario:senha@proxy.empresa.com:porta/
https_proxy = http://usuario:senha@proxy.empresa.com:porta/
use_proxy = on
```

Para o `curl`, basta adicionar as variáveis ao **~/.bashrc**:

```bash
echo 'export http_proxy="http://usuario:senha@proxy.empresa.com:porta/"' >> ~/.bashrc
echo 'export https_proxy="http://usuario:senha@proxy.empresa.com:porta/"' >> ~/.bashrc
source ~/.bashrc
```

---

### 4️⃣ **Testar a Conexão**

Verifique se o `apt` consegue se conectar:

```bash
sudo apt update
```

Se houver problemas, tente testar diretamente:

```bash
wget -O- https://google.com
curl -I https://google.com
```

Se aparecer erro de autenticação, o problema pode estar na senha com caracteres especiais. Nesse caso, tente substituir **caracteres especiais** na senha usando a codificação URL (`%40` para `@`, `%21` para `!`, etc.).

---

### 5️⃣ **Caso Use Proxy com NTLM (Autenticação do AD)**

Se sua empresa usa um **proxy NTLM** (como Squid com autenticação AD), será necessário o **cntlm**:

```bash
sudo apt install cntlm
```

Edite o arquivo de configuração:

```bash
sudo nano /etc/cntlm.conf
```

Configure:

```ini
Username    seu_usuario
Domain      seu_dominio
Proxy       proxy.empresa.com:porta
Listen      3128
```

Gere a senha criptografada:

```bash
cntlm -M http://www.google.com/
```

Use a senha gerada no `cntlm.conf`, reinicie o serviço:

```bash
sudo systemctl restart cntlm
```

Configure o `apt` para usar `localhost:3128` como proxy:

```bash
echo 'Acquire::http::Proxy "http://localhost:3128/";' | sudo tee /etc/apt/apt.conf.d/95cntlm
```

---

### 🔥 **Conclusão**

Após seguir esses passos, suas estações Linux devem conseguir atualizar pacotes passando pelo proxy autenticado da empresa.

Se precisar de mais ajustes, me avise! 🚀

---

---

## **Socks**

O protocolo **SOCKS** só é necessário se o seu proxy corporativo **exclusivamente** suporta esse protocolo (como no caso de alguns proxies com SSH tunneling ou Tor). Porém, a maioria dos proxies empresariais trabalha com **HTTP(S)**, então geralmente não é necessário configurar o **SOCKS**.

Se o seu proxy suporta **SOCKS5**, você pode adicionar as seguintes linhas ao `/etc/environment`:

```bash
socks_proxy="socks5://usuario:senha@proxy.empresa.com:porta/"
```

### **Quando usar SOCKS?**

- Se sua empresa explicitamente exige **SOCKS5**.
- Se você usa um **proxy SSH (via `ssh -D` para tunelamento SOCKS)**.
- Se o proxy **não suporta diretamente HTTP/HTTPS**.

Caso tenha dúvidas, você pode testar se sua rede responde a **SOCKS5** rodando:

```bash
curl --proxy socks5://proxy.empresa.com:porta -I https://google.com
```

Se a resposta funcionar, então precisa configurar o **SOCKS5** no ambiente. Se não, **o uso de HTTP/HTTPS no `http_proxy` e `https_proxy` já é suficiente**.

Se precisar de mais alguma configuração específica, me avise! 🚀

---

## 📌 **Para que serve o `/etc/environment`?**

O arquivo **`/etc/environment`** é um arquivo de configuração do sistema no **Linux** usado para definir **variáveis de ambiente globais** que são carregadas automaticamente para todos os usuários e processos do sistema.

---

Ele é utilizado para definir **variáveis globais** do sistema, como:

- **Proxies** (`http_proxy`, `https_proxy`, `ftp_proxy`, etc.).
- **Caminhos de busca de executáveis** (`PATH`).
- **Localização e idioma** (`LANG`, `LC_ALL`).
- **Configurações de ambiente personalizadas**.

Diferente de outros arquivos como **`/etc/profile`** ou **`~/.bashrc`**, que são executados como scripts, o **`/etc/environment`** é simplesmente um arquivo de texto que contém declarações de variáveis no formato `NOME=VALOR`.

As variáveis definidas nesse arquivo são **carregadas automaticamente para todos os usuários no login**, independente do shell utilizado.

---

## 🛠️ **Como editar o `/etc/environment`?**

Você pode editar o arquivo com um editor de texto, como `nano`:

```bash
sudo nano /etc/environment
```

E adicionar ou modificar variáveis, por exemplo:

```ini
http_proxy="http://usuario:senha@proxy.empresa.com:porta/"
https_proxy="http://usuario:senha@proxy.empresa.com:porta/"
ftp_proxy="http://usuario:senha@proxy.empresa.com:porta/"
no_proxy="127.0.0.1,localhost,.empresa.com"
LANG="pt_BR.UTF-8"
```

Depois de editar, para carregar as alterações sem reiniciar o sistema, execute:

```bash
source /etc/environment
```

---

## 🔄 **Diferença entre `/etc/environment` e outros arquivos de configuração**

| Arquivo            | Carregamento                   | Abrangência                | Uso Principal                             |
| ------------------ | ------------------------------ | -------------------------- | ----------------------------------------- |
| `/etc/environment` | Carregado no login             | Global (todos os usuários) | Definir variáveis de ambiente permanentes |
| `/etc/profile`     | Executado no login             | Global (bash e sh)         | Configurações gerais de shell             |
| `/etc/bash.bashrc` | Executado ao abrir um terminal | Global (apenas Bash)       | Personalizações do shell                  |
| `~/.bashrc`        | Executado ao abrir um terminal | Usuário específico         | Configurações pessoais do Bash            |
| `~/.profile`       | Executado no login             | Usuário específico         | Inicialização do ambiente                 |

---

## ✅ **Quando usar `/etc/environment`?**

- **Quando precisa definir variáveis globais para todos os usuários e processos**.
- **Quando o shell não é garantido (ex: serviços, ambientes gráficos, jobs automáticos)**.
- **Para configurar variáveis de proxy em sistemas corporativos**.

Se precisar de mais detalhes, me avise! 🚀

---

As configurações de proxy feitas no ambiente gráfico não valem automaticamente para a linha de comando porque o **ambiente gráfico (GUI)** e o **terminal (CLI)** gerenciam variáveis de configuração de maneira diferente.

---

## 🔍 **Motivos principais**

### 1️⃣ **As configurações gráficas afetam apenas aplicativos GUI**

Quando você configura um proxy no **GNOME, KDE ou Cinnamon**, essa configuração é aplicada apenas aos aplicativos que seguem as definições do ambiente gráfico, como:
✅ Navegadores (Firefox, Chrome, Edge).
✅ Gerenciadores de arquivos (Nautilus, Dolphin, Nemo).
✅ Aplicativos gráficos que usam bibliotecas GTK/QT.

Porém, aplicativos de linha de comando como `wget`, `curl` e `apt` **não leem automaticamente essas configurações**.

---

### 2️⃣ **A Linha de Comando Não Usa Configurações do GNOME/KDE**

No ambiente gráfico, as configurações de proxy são salvas em arquivos como:
📁 **GNOME** → `~/.config/dconf/user`
📁 **KDE Plasma** → `~/.config/kioslaverc`

Já o **terminal (CLI)** não lê esses arquivos. Ele busca as variáveis de ambiente (`http_proxy`, `https_proxy`) que precisam ser definidas manualmente no `/etc/environment`, `~/.bashrc`, ou `/etc/apt/apt.conf.d/95proxies`.

---

### 3️⃣ **Os programas de linha de comando usam métodos diferentes**

Cada programa pode ter seu próprio sistema de configuração de proxy:

- `apt` → Usa arquivos dentro de `/etc/apt/apt.conf.d/`.
- `wget` → Lê `/etc/wgetrc` ou `~/.wgetrc`.
- `git` → Precisa de `git config --global http.proxy`.
- `curl` → Usa `http_proxy` e `https_proxy`.

Se um programa não encontrar as variáveis globais de proxy, ele ignora a configuração do ambiente gráfico.

---

### 4️⃣ **O Proxy Gráfico Pode Depender do "GSettings" ou "DConf"**

O ambiente gráfico pode usar **GSettings** para definir o proxy. Se um aplicativo da linha de comando não for compatível com GSettings, ele não vai reconhecer essas configurações.

Você pode testar isso rodando:

```bash
gsettings get org.gnome.system.proxy mode
```

Se o resultado for `manual`, o proxy está ativo na interface gráfica, mas não necessariamente na CLI.

---

## 🔧 **Como fazer o proxy do ambiente gráfico valer no terminal?**

### ✅ **Solução 1: Definir manualmente as variáveis de ambiente**

Abra um terminal e digite:

```bash
export http_proxy="http://usuario:senha@proxy.empresa.com:porta/"
export https_proxy="http://usuario:senha@proxy.empresa.com:porta/"
export ftp_proxy="http://usuario:senha@proxy.empresa.com:porta/"
export no_proxy="127.0.0.1,localhost,.empresa.com"
```

Se quiser que isso seja **permanente**, adicione essas linhas ao arquivo **`~/.bashrc`** ou **`/etc/environment`**.

```bash
echo 'export http_proxy="http://usuario:senha@proxy.empresa.com:porta/"' >> ~/.bashrc
echo 'export https_proxy="http://usuario:senha@proxy.empresa.com:porta/"' >> ~/.bashrc
source ~/.bashrc
```

---

### ✅ **Solução 2: Configurar o Proxy Específico para `apt`**

```bash
echo 'Acquire::http::Proxy "http://usuario:senha@proxy.empresa.com:porta/";' | sudo tee /etc/apt/apt.conf.d/95proxies
echo 'Acquire::https::Proxy "http://usuario:senha@proxy.empresa.com:porta/";' | sudo tee -a /etc/apt/apt.conf.d/95proxies
```

---

### ✅ **Solução 3: Fazer o Terminal Obedecer ao Proxy do GNOME**

Se estiver usando **GNOME ou Cinnamon**, você pode configurar o terminal para usar as mesmas configurações da interface gráfica:

```bash
gsettings get org.gnome.system.proxy mode
gsettings get org.gnome.system.proxy.http host
gsettings get org.gnome.system.proxy.http port
```

Se os valores estiverem configurados corretamente, faça um script para que o terminal use essas configurações:

```bash
export http_proxy="http://$(gsettings get org.gnome.system.proxy.http host):$(gsettings get org.gnome.system.proxy.http port)/"
export https_proxy="$http_proxy"
export ftp_proxy="$http_proxy"
export no_proxy="127.0.0.1,localhost"
```

Coloque esse script no **`~/.bashrc`** ou no `/etc/profile` para que seja carregado automaticamente.

---

## ✅ **Conclusão**

O motivo principal para o proxy do ambiente gráfico **não valer para a linha de comando** é que o **terminal não lê diretamente as configurações da GUI**. Para resolver isso, você precisa definir manualmente as variáveis de ambiente ou configurar cada programa de CLI separadamente.

Se precisar de mais detalhes ou ajustes, me avise! 🚀
