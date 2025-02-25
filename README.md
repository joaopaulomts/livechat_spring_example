# 💬 LiveChat Spring Example

Este é um projeto de chat em tempo real utilizando **Spring Boot**, **WebSockets** e **STOMP**. Ele permite a comunicação entre múltiplos usuários em um canal de chat ao vivo. 🚀

## 🚀 Tecnologias Utilizadas
- ☕ **Java 17+**
- 🔥 **Spring Boot**
- 🔌 **WebSockets**
- 📡 **STOMP**
- 🎨 **HTML, CSS e JavaScript** (Frontend Simples)

## 📂 Estrutura do Projeto
```
LIVECHAT_SPRING_EXAMPLE
|
├── src/main/java/com/joaopaulo/livechat_spring_example
│   ├── config
│   │   ├── WebSocketConfig.java  # Configuração do WebSocket e STOMP
│   ├── controller
│   │   ├── LiveChatController.java  # Controlador para receber e enviar mensagens
│   ├── domain
│   │   ├── ChatInput.java  # Representa a entrada de uma mensagem
│   │   ├── ChatOutput.java  # Representa a saída de uma mensagem
│   ├── LivechatSpringExampleApplication.java  # Classe principal
│
├── src/main/resources
│   ├── static
│   │   ├── app.js  # Lógica frontend para conectar-se ao WebSocket
│   │   ├── index.html  # Interface do usuário
│   │   ├── main.css  # Estilização
│   ├── templates
│   ├── application.properties  # Configuração da aplicação
│
├── pom.xml  # Dependências do projeto
```

## ⚙️ Como Funciona

O sistema permite que os usuários enviem mensagens, que são processadas e transmitidas para todos os participantes conectados ao WebSocket.

### 🔧 1. Configuração do WebSocket
A configuração do WebSocket está na classe `WebSocketConfig.java`:
```java
@Configuration
@EnableWebSocketMessageBroker
public class WebSocketConfig implements WebSocketMessageBrokerConfigurer {

    @Override
    public void configureMessageBroker(MessageBrokerRegistry registry) {
        registry.enableSimpleBroker("/topics");
        registry.setApplicationDestinationPrefixes("/app");
    }

    @Override
    public void registerStompEndpoints(StompEndpointRegistry registry) {
        registry.addEndpoint("/buildrun-livechat-websocket");
    }
}
```
- **`enableSimpleBroker("/topics")`**: Define o prefixo onde as mensagens serão publicadas.
- **`setApplicationDestinationPrefixes("/app")`**: Define o prefixo para os endpoints da aplicação.
- **`addEndpoint("/buildrun-livechat-websocket")`**: Registra um endpoint WebSocket.

### 📝 2. Controlador do Chat
O controlador `LiveChatController.java` recebe as mensagens e as distribui para os assinantes do tópico `/topics/livechat`.
```java
@Controller
public class LiveChatController {

    @MessageMapping("/new-message")
    @SendTo("/topics/livechat")
    public ChatOutput newMessage(ChatInput input) {
        return new ChatOutput(HtmlUtils.htmlEscape(input.user() + ": " + input.message()));
    }
}
```
- **`@MessageMapping("/new-message")`**: Indica que as mensagens enviadas para `/app/new-message` serão processadas aqui.
- **`@SendTo("/topics/livechat")`**: Todas as mensagens são enviadas para este tópico, permitindo que todos os assinantes as recebam.

### 📦 3. Modelos de Dados
#### ChatInput.java
```java
public record ChatInput(String user, String message) {}
```
#### ChatOutput.java
```java
public record ChatOutput(String content) {}
```

## ▶️ Como Executar o Projeto

1. 📥 Clone este repositório:
   ```sh
   git clone https://github.com/seu-usuario/livechat-spring-example.git
   ```
2. 📂 Acesse o diretório do projeto:
   ```sh
   cd livechat-spring-example
   ```
3. 🏗️ Compile e rode a aplicação:
   ```sh
   mvn spring-boot:run
   ```
4. 🌍 Abra o navegador e acesse: `http://localhost:8080`

## 🔗 Conectando ao WebSocket via JavaScript
O arquivo `app.js` contém a lógica para conectar ao WebSocket usando STOMP:
```js
const socket = new SockJS('/buildrun-livechat-websocket');
const stompClient = Stomp.over(socket);

stompClient.connect({}, () => {
    stompClient.subscribe('/topics/livechat', (message) => {
        console.log(JSON.parse(message.body).content);
    });
});
```

## 📌 Melhorias Futuras
- 🔐 Autenticação de usuários
- 📢 Suporte a salas privadas de chat
- 💾 Persistência das mensagens no banco de dados

## 📜 Licença
Este projeto está sob a licença **MIT**. Sinta-se à vontade para utilizá-lo e modificá-lo conforme necessário! 🚀

