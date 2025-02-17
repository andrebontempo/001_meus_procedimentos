André... eu que passo o nome  e e-mail para a Flora. 
Pra gente testar eu acho que vc que tem que me passar  o endereço da Flora no GCP... não?
Esse é o código que estou utilizando na Intranet para exibir a Flora. (Homologação)
```


</script><script src="https://unpkg.com/blip-chat-widget@1.6.*" type="text/javascript"></script><script type="text/javascript">
AUI().use('array-extras', function(A) {(function() {
        Liferay.Service(
            [
                {
                    '$user[userId,emailAddress,fullName] = /user/get-user-by-id': {
                        userId: Liferay.ThemeDisplay.getUserId()
                    }
                }
            ],
            window.onload = function(obj) {
                var blipClient = new BlipChat();
                console.log("fullName: ", obj[0].fullName);
                console.log("email: ", obj[0].emailAddress);
                blipClient
                .withAppKey('ZmxvcmF2MjoxNmM1YmQ3Zi04MGZlLTQzYTItYjJjYS1lZDE4Mzc1YzE1NmQ=')
                .withButton({"color":"#09184f","icon":"https://transfer.sede.embrapa.br/transfer/cst/chatbot/flora2024/Flora_Widget_003.png"})
                .withAccount({
                    fullName: obj[0].fullName,
                    email: obj[0].emailAddress,
                    extras:{
                        idtopdesk: '{idTopdesk}'
                    }
                })
                .build();
            }
        );
    })();
});
</script>

```

## oi

```
Veja o código para embutir na página do GCP:
<link rel="stylesheet" href="https://www.gstatic.com/dialogflow-console/fast/df-messenger/prod/v1/themes/df-messenger-default.css">
<script src="https://www.gstatic.com/dialogflow-console/fast/df-messenger/prod/v1/df-messenger.js"></script>
<df-messenger
  location="us-central1"
  project-id="pilotoflora"
  agent-id="41d6b681-51e9-429c-8bec-27107662192e"
  language-code="pt-br"
  max-query-length="-1">
  <df-messenger-chat-bubble
   chat-title="Flora - 2025 - 005 - Logon">
  </df-messenger-chat-bubble>
</df-messenger>
<style>
  df-messenger {
    z-index: 999;
    position: fixed;
    --df-messenger-font-color: #000;
    --df-messenger-font-family: Google Sans;
    --df-messenger-chat-background: #f3f6fc;
    --df-messenger-message-user-background: #d3e3fd;
    --df-messenger-message-bot-background: #fff;
    bottom: 16px;
    right: 16px;
  }
</style>
```

Este código incorpora o **Dialogflow Messenger**, um chatbot integrado ao **Google Dialogflow CX**, dentro de uma página HTML. Vou explicar detalhadamente cada parte:

---

## **1️⃣ Inclusão de CSS - Aparência do Chatbot**
```html
<link rel="stylesheet" href="https://www.gstatic.com/dialogflow-console/fast/df-messenger/prod/v1/themes/df-messenger-default.css">
```
### 📌 O que faz?  
Carrega um **arquivo CSS externo** fornecido pelo Google, que define a aparência padrão do **Dialogflow Messenger**.  

- Define cores, fontes e estilos do chatbot.  
- Garante que o chat tenha um visual padronizado.  
- Sem esse link, o chat ficaria sem estilos apropriados.

---

## **2️⃣ Inclusão do Script do Chatbot**
```html
<script src="https://www.gstatic.com/dialogflow-console/fast/df-messenger/prod/v1/df-messenger.js"></script>
```
### 📌 O que faz?  
- Importa o **código JavaScript do Google Dialogflow Messenger**, que permite o funcionamento do chatbot na página.  
- Esse script contém a lógica necessária para conectar o chatbot ao **Dialogflow CX**.  
- Sem ele, o chatbot não apareceria na página.

---

## **3️⃣ Configuração do Chatbot - `<df-messenger>`**
```html
<df-messenger
  location="us-central1"
  project-id="pilotoflora"
  agent-id="41d6b681-51e9-429c-8bec-27107662192e"
  language-code="pt-br"
  max-query-length="-1">
```
### 📌 O que faz?  
Esse elemento define o chatbot **Dialogflow Messenger** na página e suas configurações:

| **Atributo**       | **Descrição** |
|--------------------|--------------|
| `location="us-central1"` | Define a **região do servidor** onde o agente Dialogflow CX está hospedado. |
| `project-id="pilotoflora"` | O **ID do projeto** no Google Cloud. Ele identifica o chatbot na plataforma do Dialogflow. |
| `agent-id="41d6b681-51e9-429c-8bec-27107662192e"` | O **ID único do agente Dialogflow CX** vinculado ao chatbot. |
| `language-code="pt-br"` | Define o **idioma** do chatbot para **português do Brasil**. |
| `max-query-length="-1"` | Define o **tamanho máximo da entrada do usuário**. O valor **"-1"** significa **sem limite de caracteres**. |

---

## **4️⃣ Configuração do Balão de Chat - `<df-messenger-chat-bubble>`**
```html
  <df-messenger-chat-bubble
   chat-title="Flora - 2025 - 005 - Logon">
  </df-messenger-chat-bubble>
```
### 📌 O que faz?  
- Define a **interface do balão de chat** do chatbot.  
- O atributo `chat-title="Flora - 2025 - 005 - Logon"` define o **título** que aparece na interface do chat.  
- Isso indica que o chatbot pode ser chamado "Flora - 2025 - 005 - Logon".

---

## **5️⃣ Personalização do Estilo do Chatbot (`<style>`)**
```html
<style>
  df-messenger {
    z-index: 999;
    position: fixed;
    --df-messenger-font-color: #000;
    --df-messenger-font-family: Google Sans;
    --df-messenger-chat-background: #f3f6fc;
    --df-messenger-message-user-background: #d3e3fd;
    --df-messenger-message-bot-background: #fff;
    bottom: 16px;
    right: 16px;
  }
</style>
```
### 📌 O que faz?  
Esse bloco de **CSS personalizado** ajusta a aparência e o posicionamento do chatbot.

| **Propriedade**  | **Descrição** |
|-----------------|--------------|
| `z-index: 999;` | Garante que o chatbot fique acima de outros elementos da página. |
| `position: fixed;` | Faz o chatbot ficar fixo na tela, mesmo se o usuário rolar a página. |
| `bottom: 16px;` | Define a distância de 16 pixels **do rodapé** da página. |
| `right: 16px;` | Define a distância de 16 pixels **da lateral direita** da tela. |
| `--df-messenger-font-color: #000;` | Define a **cor do texto** do chatbot como preto. |
| `--df-messenger-font-family: Google Sans;` | Define a **fonte** do texto do chatbot como *Google Sans*. |
| `--df-messenger-chat-background: #f3f6fc;` | Define a **cor de fundo do chat** como um tom claro de azul. |
| `--df-messenger-message-user-background: #d3e3fd;` | Define a **cor das mensagens do usuário**. |
| `--df-messenger-message-bot-background: #fff;` | Define a **cor das mensagens do bot** (branco). |

---

## **📌 Como esse código funciona na página?**
1. O **CSS e o JavaScript** do Google são carregados para exibir o chatbot corretamente.  
2. O `<df-messenger>` é renderizado, **conectando-se ao agente Dialogflow CX** especificado.  
3. O `<df-messenger-chat-bubble>` exibe o **balão de chat**, onde os usuários podem interagir.  
4. O chatbot responde usando o fluxo do **Dialogflow CX** conforme as mensagens do usuário.  
5. O estilo do chatbot é personalizado via **CSS**, garantindo um visual agradável.

---

## **📌 Resultado Final**
Esse código embute um chatbot **Dialogflow CX** na página, aparecendo como um **balão de chat fixo no canto inferior direito** da tela.

Se precisar adicionar mais funcionalidades, como **enviar variáveis da página para o bot**, me avise! 🚀

Ótimo! Para enviar variáveis da página (como `name` e `email`) para o chatbot do **Dialogflow CX**, você pode utilizar a API do `df-messenger` e enviar **eventos personalizados com parâmetros** para o bot.  

---  

# **📌 Como enviar variáveis da página para o Dialogflow CX**  

### **1️⃣ Capturar as variáveis na página**
Se os valores `name` e `email` estão disponíveis na página dentro de **inputs ocultos, localStorage, sessionStorage ou variáveis globais**, primeiro precisamos capturá-los com JavaScript.

```html
<!-- Campos ocultos para armazenar nome e e-mail -->
<input type="hidden" id="name" value="João da Silva">
<input type="hidden" id="email" value="joao@email.com">
```

Agora, vamos capturar esses valores com JavaScript:

```javascript
const userName = document.getElementById("name").value;
const userEmail = document.getElementById("email").value;
console.log("Nome:", userName, "Email:", userEmail);
```

Se os valores estiverem armazenados em `localStorage`, use:

```javascript
const userName = localStorage.getItem("name") || "Usuário";
const userEmail = localStorage.getItem("email") || "email@exemplo.com";
```

---

### **2️⃣ Esperar o carregamento do `df-messenger` e enviar as variáveis**
O `df-messenger` precisa ser carregado antes de enviarmos os dados. Podemos fazer isso com o evento **`dfMessengerLoaded`**.

```javascript
window.addEventListener("dfMessengerLoaded", function () {
    console.log("Chatbot carregado!");

    // Obtém a referência ao chatbot
    const dfMessenger = document.querySelector("df-messenger");

    // Envia um evento personalizado com as variáveis
    dfMessenger.renderCustomEvent("user-info", {
        UserName: userName,
        UserEmail: userEmail
    });

    console.log("Dados enviados para o bot:", userName, userEmail);
});
```

---

### **3️⃣ Criar um `Event Handler` no Dialogflow CX**
Agora, no **Dialogflow CX**, precisamos configurar o evento `"user-info"` para receber os dados da página.

#### **Passos no Dialogflow CX:**
1. Vá para o seu **Agent (Agente)** no Dialogflow CX.  
2. Acesse o **fluxo** onde deseja capturar os dados.  
3. Clique na **Página (State)** onde deseja processar os dados.  
4. Vá até **Event Handlers** e clique em **Adicionar Evento**.  
5. No campo **Nome do Evento**, insira:  
   ```plaintext
   user-info
   ```
6. No campo **Parâmetros**, adicione as variáveis:  
   - **UserName**  
   - **UserEmail**  
7. Na **resposta do bot**, personalize a mensagem usando os valores capturados:  
   ```plaintext
   Olá, ${session.params.UserName}! Seu e-mail é ${session.params.UserEmail}. Como posso te ajudar?
   ```
8. **Salve e teste** no simulador.

---

# **📌 Como funciona esse código?**
1. O chatbot **carrega** na página.  
2. Assim que estiver pronto, o evento **"dfMessengerLoaded"** dispara.  
3. O código captura as variáveis `name` e `email` da página.  
4. O evento `"user-info"` é enviado para o **Dialogflow CX**, passando `UserName` e `UserEmail`.  
5. O Dialogflow responde ao usuário usando esses valores.  

---

## **📌 Teste e Debug**
1. Abra o console do navegador (F12 → Console).  
2. Veja se os valores **Name e Email** aparecem corretamente.  
3. Digite algo no chatbot e veja se ele responde com seu nome e e-mail.  

---

# **📌 Melhorias possíveis**
✅ Enviar mais variáveis (ex: `userRole`, `lastLogin` etc.).  
✅ Salvar os dados no **Contexto do Dialogflow** para uso futuro.  
✅ Personalizar ainda mais as respostas baseadas no usuário.  

Isso resolve o que você precisa? 🚀 Se precisar de mais ajustes, me avise!

Aqui está o **código completo**, incluindo o chatbot do **Dialogflow CX** e o **script para capturar e enviar variáveis da página** (`name` e `email`).  

---

## **📌 Código Completo**
```html
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Chatbot Dialogflow CX</title>

    <!-- Importa o CSS padrão do Dialogflow Messenger -->
    <link rel="stylesheet" href="https://www.gstatic.com/dialogflow-console/fast/df-messenger/prod/v1/themes/df-messenger-default.css">
</head>
<body>

    <!-- Campos ocultos com os dados do usuário -->
    <input type="hidden" id="name" value="João da Silva">
    <input type="hidden" id="email" value="joao@email.com">

    <!-- Chatbot Dialogflow CX -->
    <df-messenger
        location="us-central1"
        project-id="pilotoflora"
        agent-id="41d6b681-51e9-429c-8bec-27107662192e"
        language-code="pt-br"
        max-query-length="-1">
        <df-messenger-chat-bubble chat-title="Flora - 2025 - 005 - Logon">
        </df-messenger-chat-bubble>
    </df-messenger>

    <!-- Importa o JavaScript do Dialogflow Messenger -->
    <script src="https://www.gstatic.com/dialogflow-console/fast/df-messenger/prod/v1/df-messenger.js"></script>

    <!-- Script para capturar variáveis da página e enviá-las para o chatbot -->
    <script>
        window.addEventListener("dfMessengerLoaded", function () {
            console.log("Chatbot carregado!");

            // Captura os valores dos campos da página
            const userName = document.getElementById("name").value || "Usuário";
            const userEmail = document.getElementById("email").value || "email@exemplo.com";

            console.log("Nome capturado:", userName, "Email capturado:", userEmail);

            // Obtém o chatbot
            const dfMessenger = document.querySelector("df-messenger");

            // Envia um evento para o Dialogflow CX com os dados do usuário
            dfMessenger.renderCustomEvent("user-info", {
                UserName: userName,
                UserEmail: userEmail
            });

            console.log("Dados enviados para o chatbot:", userName, userEmail);
        });
    </script>

    <!-- Estilos personalizados para o chatbot -->
    <style>
        df-messenger {
            z-index: 999;
            position: fixed;
            --df-messenger-font-color: #000;
            --df-messenger-font-family: Google Sans;
            --df-messenger-chat-background: #f3f6fc;
            --df-messenger-message-user-background: #d3e3fd;
            --df-messenger-message-bot-background: #fff;
            bottom: 16px;
            right: 16px;
        }
    </style>

</body>
</html>
```

---

## **📌 Como esse código funciona?**
1️⃣ **Captura as variáveis `name` e `email` da página.**  
2️⃣ **Aguarda o chatbot carregar (`dfMessengerLoaded`).**  
3️⃣ **Envia um evento personalizado `"user-info"` para o Dialogflow CX** com os dados.  
4️⃣ **O chatbot recebe os valores e pode responder com `UserName` e `UserEmail`.**  

---

## **📌 Como configurar no Dialogflow CX?**
1. **Criar um `Event Handler` no CX**  
   - Vá até seu **fluxo** no Dialogflow CX.  
   - Selecione a **Página (State)** onde deseja receber os dados.  
   - Em **Event Handlers**, clique em **Adicionar Evento**.  
   - Nome do evento:  
     ```plaintext
     user-info
     ```
   - Adicione os parâmetros:  
     - **UserName**  
     - **UserEmail**  

2. **Usar as variáveis na resposta do chatbot**  
   No campo de resposta do agente, use:

   ```plaintext
   Olá, ${session.params.UserName}! Recebemos seu e-mail: ${session.params.UserEmail}. Como posso te ajudar?
   ```

---

## **📌 Teste e Debug**
1️⃣ **Abra a página no navegador.**  
2️⃣ **Abra o console do navegador (F12 → Console).**  
3️⃣ **Verifique se os dados `UserName` e `UserEmail` foram capturados corretamente.**  
4️⃣ **Interaja com o chatbot e veja se ele responde com seu nome e e-mail.**  

---

Isso resolve o que você precisa? 🚀 Se precisar de mais ajustes, me avise! 😊

