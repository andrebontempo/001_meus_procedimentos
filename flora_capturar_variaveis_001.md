Ótima abordagem! Você pode capturar as variáveis **UserName** e **EmailName** da página HTML e enviá-las ao **Dialogflow Messenger** usando a API de eventos do `df-messenger`. Dessa forma, o agente pode usá-las no webhook de abertura de chamados no **TOPdesk**.  

---

## **📌 Como Capturar as Variáveis do Usuário e Enviar ao Dialogflow Messenger?**
### **1️⃣ Certifique-se de que a Página HTML Contém os Dados do Usuário**
Se o usuário já estiver autenticado na página, os valores podem ser armazenados de várias formas:  
✅ **No HTML (`input hidden`)**  
✅ **Em `localStorage` ou `sessionStorage`**  
✅ **No JavaScript global**  

### **Exemplo de Como os Dados Podem Estar na Página**
```html
<input type="hidden" id="UserName" value="João da Silva">
<input type="hidden" id="EmailName" value="joao.silva@email.com">
```
Ou podem estar no `localStorage`:
```javascript
localStorage.setItem("UserName", "João da Silva");
localStorage.setItem("EmailName", "joao.silva@email.com");
```

---

### **2️⃣ Capturar os Dados e Enviar ao Dialogflow Messenger**
Agora, você precisa modificar o script do **Dialogflow Messenger** para capturar essas variáveis e enviá-las como um **evento** para o chatbot.

```html
<script>
    window.addEventListener("dfMessengerLoaded", function () {
        console.log("✅ Chatbot carregado!");

        // Captura os valores do usuário
        const userName = document.getElementById("UserName")?.value || localStorage.getItem("UserName") || "Usuário Desconhecido";
        const userEmail = document.getElementById("EmailName")?.value || localStorage.getItem("EmailName") || "email@desconhecido.com";

        console.log("📩 Capturado - Nome:", userName, "| Email:", userEmail);

        // Obtém referência ao chatbot
        const dfMessenger = document.querySelector("df-messenger");

        // Envia um evento para o Dialogflow com os dados do usuário
        dfMessenger.renderCustomEvent("user-login", {
            UserName: userName,
            UserEmail: userEmail
        });

        console.log("🚀 Dados enviados ao Dialogflow:", userName, userEmail);
    });
</script>
```

📌 **O que este código faz?**  
1. **Espera o carregamento do chatbot (`dfMessengerLoaded`)**.  
2. **Captura o nome e e-mail do usuário** de `<input>` ou `localStorage`.  
3. **Envia um evento `user-login` ao Dialogflow Messenger** contendo `UserName` e `UserEmail`.  
4. **Exibe logs no console** para facilitar a depuração.  

---

## **📌 3️⃣ Criar um `Event Handler` no Dialogflow CX para Capturar os Dados**
Agora, você precisa configurar o **Dialogflow CX** para receber esses dados.

### **Passos no Dialogflow CX**
1. Vá até seu **Agent (Agente)** no Dialogflow CX.  
2. Acesse o **fluxo principal**.  
3. Clique em **Adicionar Event Handler**.  
4. No campo **Nome do Evento**, insira:  
   ```plaintext
   user-login
   ```
5. No campo **Parâmetros**, adicione:  
   - **UserName**  
   - **UserEmail**  
6. Na resposta do bot, personalize a mensagem usando os valores capturados:  
   ```plaintext
   Olá, ${session.params.UserName}! Seu e-mail é ${session.params.UserEmail}. Como posso te ajudar?
   ```

📌 **Agora o agente do Dialogflow CX terá os dados do usuário automaticamente!**  

---

## **📌 4️⃣ Como Usar Esses Dados no Webhook?**
Agora que o Dialogflow CX recebe os dados do usuário, podemos enviá-los para o webhook do **TOPdesk**.

No webhook, os dados serão passados na requisição JSON:
```json
{
  "sessionInfo": {
    "parameters": {
      "UserName": "João da Silva",
      "UserEmail": "joao.silva@email.com"
    }
  }
}
```

### **Código para Capturar os Dados no Webhook do TOPdesk**
```javascript
exports.dialogflowWebhook = async (req, res) => {
    console.log("🚀 Webhook chamado pelo Dialogflow");

    // Captura os dados do usuário enviados pelo Dialogflow
    const userName = req.body.sessionInfo.parameters.UserName;
    const userEmail = req.body.sessionInfo.parameters.UserEmail;

    console.log(`👤 Usuário: ${userName} | 📩 Email: ${userEmail}`);

    if (!userEmail) {
        return res.json({ fulfillmentText: "Erro: e-mail não informado." });
    }

    // Agora podemos usar o userEmail para buscar o ID do usuário no TOPdesk...
};
```

---

## **📌 Resumo**
✅ **Captura os dados do usuário na página HTML**.  
✅ **Envia esses dados para o Dialogflow Messenger como um evento**.  
✅ **O Dialogflow CX armazena os dados e os usa nas interações**.  
✅ **O Webhook do TOPdesk recebe esses dados e os processa**.  

🚀 **Agora seu agente do Dialogflow sabe quem está falando antes mesmo do usuário digitar qualquer coisa!** 🔥 Se precisar de ajustes, me avise! 😊
