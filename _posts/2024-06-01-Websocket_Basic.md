# WebSocket

작성일시: 2024년 6월 1일 오후 1:49
종류: WebSocket
복습: No

## 목차

## Websocket

- 기존의 단방향 HTTP 프로토코로가 호환되어 양방향 통신을 제공하기 위해 개발된 프로토콜
- 일반 socket과 달리 HTTTP 80 포트를 사용하므로 방화벽에 제약이 없으며 통상 Websocket으로 불린다.
- 접속까지는 HTTP프로토콜을 이용하고 그 이후 통신은 자체적인 Websocket프로토콜로 통신하게 된다.

### 하나의 세션 안에서 수행되는 채팅 만들기

1. 라이브러리 추가
    
    ```java
    implementation 'org.springframework.boot:spring-boot-starter-web'
    	implementation 'org.springframework.boot:spring-boot-starter-websocket'
    	compileOnly 'org.projectlombok:lombok'
    	annotationProcessor 'org.projectlombok:lombok'
    	testImplementation 'org.springframework.boot:spring-boot-starter-test'
    	testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
    ```
    
2. ChatHandler
- socket통신은 서버와 클라이언트가 1:N으로 관계를 맺는다.
- 따라서 한 서버에 여러 클라이언트가 접속할 수 있으며 서버에는 여러 클라이언트가 발송한 메시지를 받아 처리해줄 Handler의 작성이 필요하다.
- 아래 Handler는 TextWebSocketHandler를 상속받아 사용한다.
    
    ```java
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.web.socket.TextMessage;
    import org.springframework.web.socket.WebSocketSession;
    import org.springframework.web.socket.handler.TextWebSocketHandler;
    
    @Component
    public class WebSockChatHandler extends TextWebSocketHandler {
        // WebSockChatHandler 클래스는 Spring의 TextWebSocketHandler를 확장하여 웹소켓 텍스트 메시지를 처리합니다.
    
        private static final Logger log = LoggerFactory.getLogger(WebSockChatHandler.class);
        // log는 로깅을 위한 Logger 객체입니다. LoggerFactory.getLogger()를 사용하여 WebSockChatHandler 클래스에 대한 Logger를 생성합니다.
    
        @Override
        protected void handleTextMessage(WebSocketSession session, TextMessage message) throws Exception {
            // handleTextMessage 메서드는 WebSocketSession과 TextMessage를 매개변수로 받습니다.
            // WebSocketSession은 클라이언트와의 웹소켓 세션을 나타내며, TextMessage는 수신된 텍스트 메시지를 나타냅니다.
    
            String payload = message.getPayload();
            // message.getPayload()를 호출하여 수신된 메시지의 페이로드(내용)를 문자열로 가져옵니다.
    
            log.info("payload = {}", payload);
            // 수신된 메시지의 페이로드를 로그로 출력합니다.
            // {}는 플레이스홀더로, payload 값이 로그 메시지에 삽입됩니다.
    
            TextMessage textMessage = new TextMessage("Welcome");
            // 클라이언트에게 보낼 텍스트 메시지를 생성합니다. 여기서는 "Welcome"이라는 간단한 문자열을 메시지로 설정합니다.
    
            session.sendMessage(textMessage);
            // session.sendMessage()를 호출하여 생성된 텍스트 메시지를 클라이언트에게 전송합니다.
        }
    }
    
    ```
    
1. WebSocketConfig 작성
    - 위에서 만든 handler를 사용하여 websocket을 활성화하기 위한 config파일을 작성
    - @EnableWebSocket을 선언하여 websocket활성화
    - Websocket에 접속하기 위한 endpoint는 /ws/chat으로 설정하고 도메인이 다른 서버에서도 접속 가능하도록 cors를 설정
    - 이제 클라이언트가 ws://localhost/ws/chat으로 커넥션을 연결하고 메시지 통신을 할 수 있다.

### 테스트


![Untitled](https://github.com/Sunro1994/Websocket/assets/132982907/571a2c6c-4926-4b3c-8e6e-88e0b9943532)


### 위 방식의 문제점

- 위에서 만든 프로젝트는 연결된 클라이언트 끼리만 메시지 통신이 가능하다. 따라서 채팅방이 하나뿐인 채팅 서버이다. 여러개의 채팅방을 만들어서 해당 채팅방에 입장한 클라이언트들간에 메시지를 교환하려면 고도화가 필요하다.
- 채팅방에 입장시 클ㄹ라이언트들의 websocket session정보를 채팅방에 맵핑시켜서 보관하고 있으면 서버에 전달된 메시지를 특정 방의 websocket세션으로 보낼 수 있으므로 개별 채팅방을 구현할 수 있다.

## 채팅 메시지 구현

- 채팅 메시지를 주고받기 위한 DTO를 만든다.
- 상황에 따라 채팅방 입장, 채팅방에 메시지 보내기 두 가지 상황이 있으므로, ENTER, TALK enum을 선언한다.
- 나머지 멤버 필드는 채팅방 구별  id,  메시지를 보낸 사람, 메시지로 구성한다.
    
    ```java
    import lombok.Data;
    
    @Data
    public class ChatMessage {
        // ChatMessage 클래스는 웹소켓을 통해 주고받는 채팅 메시지를 나타냅니다.
    
        // 메시지 타입: 입장, 채팅
        public enum MessageType{
            ENTER, TALK
        }
        // MessageType 열거형을 정의합니다. 이 열거형은 메시지 타입을 나타내며, "ENTER"와 "TALK" 값을 가질 수 있습니다.
    
        private MessageType type;
        // type 필드는 메시지의 타입을 나타냅니다. MessageType 열거형 타입입니다.
    
        private String roomId;
        // roomId 필드는 메시지가 전송되는 채팅방의 ID를 나타냅니다. String 타입입니다.
    
        private String sender;
        // sender 필드는 메시지를 보낸 사용자의 이름을 나타냅니다. String 타입입니다.
    
        private String message;
        // message 필드는 실제 메시지 내용을 나타냅니다. String 타입입니다
    }
    
    ```
    

## 채팅방 구현

- 채팅방을 구현하기 위해 DTO를 하나 만든다.
- 채팅바엥 입장한 클라이언트들의 정보를 가지고 있어야 하므로 WeboskcetSession 정보 리스트를 멤버 필드로 갖는다.
- 나머지 멤버 필드로 채팅방 id, 채팅방 이름을 추가한다.
- 채팅방에서는 입장, 대화하기 기능이 있으므로 handleAction을 통해 분기처리 한다.
- 입장 시에는 ChatRoom 의 session 정보에 클라이언트의 session리스트를 추가해놓았다가 채팅에 메시지가 도착할 경우 채팅룸의 모든 session에 미시지를 발송하면 채팅이 완성된다.
    
    ```java
    import com.sunro.basicWebsocket.Websocket.service.ChatService;
    import lombok.Builder;
    import lombok.Getter;
    import org.springframework.web.socket.WebSocketSession;
    
    import java.util.HashSet;
    import java.util.Set;
    
    @Getter
    public class ChatRoom {
    
        private String roomId;
        private String name;
        private Set<WebSocketSession> sessions = new HashSet<>();
    
        @Builder
        public ChatRoom(String roomId, String name) {
            this.roomId = roomId;
            this.name = name;
        }
    
        public void handlerActions(WebSocketSession session, ChatMessage chatMessage, ChatService chatService) {
            if (chatMessage.getType().equals(ChatMessage.MessageType.ENTER)) {
                sessions.add(session);
                chatMessage.setMessage(chatMessage.getSender()+"님이 입장하셨습니다.");
            }
            sendMessage(chatMessage,chatService);
    
        }
    
        public <T> void sendMessage(T message, ChatService chatService) {
            sessions.parallelStream().forEach(session -> chatService.sendMessgae(session,message));
        }
    }
    
    ```
    

## 채팅 서비스 구현

- 채팅방을 생성, 조회하고 하나의 세션에 메시지를 발송하는 서비스를 아래와 같이 구현한다.
- 채팅방 Map은 서버에 생성된 모든 채팅방의 정보를 모아둔 구조체
- 채팅룸의 정보 저장은 빠른 구현을 위해 일단 외부 저장소를 이용하지 않고 HashMap에 저장
- 채팅방 조회, 생성, 발송기능을 구현
    
    ```java
    import com.fasterxml.jackson.databind.ObjectMapper;
    import com.sunro.basicWebsocket.Websocket.dto.ChatRoom;
    import jakarta.annotation.PostConstruct;
    import lombok.RequiredArgsConstructor;
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.stereotype.Service;
    import org.springframework.web.socket.TextMessage;
    import org.springframework.web.socket.WebSocketSession;
    
    import java.io.IOException;
    import java.util.*;
    
    @Service
    @RequiredArgsConstructor
    @Slf4j
    public class ChatService {
    
        private final ObjectMapper objectMapper;
        private Map<String, ChatRoom> chatRooms;
    
        @PostConstruct
        public void init() {
            chatRooms = new HashMap<>();
        }
    
        public List<ChatRoom> findAllRoom() {
            return new ArrayList<>(chatRooms.values());
        }
    
        public ChatRoom findRoomById(String roomId) {
            return chatRooms.get(roomId);
        }
    
        public ChatRoom createRoom(String name) {
            String roomId = UUID.randomUUID().toString();
            ChatRoom chatRoom = ChatRoom.builder()
                    .roomId(roomId)
                    .name(name)
                    .build();
            chatRooms.put(roomId, chatRoom);
            return chatRoom;
        }
    
        public <T> void sendMessgae(WebSocketSession session, T message) {
            try {
                session.sendMessage(new TextMessage(objectMapper.writeValueAsString(message)));
            } catch (IOException e) {
                log.error(e.getMessage(), e);
            }
        }
    }
    
    ```
    

## 채팅 컨트롤러 구현

- 채팅방의 생성 및 조회는 REST API로 구현
    
    ```java
    import com.sunro.basicWebsocket.Websocket.dto.ChatRoom;
    import com.sunro.basicWebsocket.Websocket.service.ChatService;
    import lombok.RequiredArgsConstructor;
    import org.springframework.web.bind.annotation.*;
    
    import java.util.List;
    
    @RequiredArgsConstructor
    @RestController
    @RequestMapping("/chat")
    public class ChatController {
    
        private final ChatService chatService;
    
        @PostMapping
        public ChatRoom createRoom(@RequestParam String name) {
            return chatService.createRoom(name);
        }
    
        @GetMapping
        public List<ChatRoom> findAllRoom() {
            return chatService.findAllRoom();
        }
    }
    
    ```
    

## WebsocketHandler수정

- 웹 소켓 클라이언트로부터 채팅 메시지를 전달받아 채팅 메시지 객체로 변환
- 전달받은 메시지에 담긴 채팅방  Id로 발송 대상 채팅방 정보를 조회
- 해당 채팅방에 입장해있는 모든 클라이언트들에게 타입에 따른 메시지 발송


```java

import com.fasterxml.jackson.databind.ObjectMapper;
import com.sunro.basicWebsocket.Websocket.dto.ChatMessage;
import com.sunro.basicWebsocket.Websocket.dto.ChatRoom;
import com.sunro.basicWebsocket.Websocket.service.ChatService;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.context.annotation.Configuration;
import org.springframework.stereotype.Component;
import org.springframework.web.socket.TextMessage;
import org.springframework.web.socket.WebSocketSession;
import org.springframework.web.socket.handler.TextWebSocketHandler;

@Component
@RequiredArgsConstructor
@Slf4j
public class WebSockChatHandler extends TextWebSocketHandler {
    // WebSockChatHandler 클래스는 Spring의 TextWebSocketHandler를 확장하여 웹소켓 텍스트 메시지를 처리합니다.

        private final ObjectMapper objectMapper;
        private final ChatService chatService;

    @Override
    protected void handleTextMessage(WebSocketSession session, TextMessage message) throws Exception {

        // handleTextMessage 메서드는 WebSocketSession과 TextMessage를 매개변수로 받습니다.
        // WebSocketSession은 클라이언트와의 웹소켓 세션을 나타내며, TextMessage는 수신된 텍스트 메시지를 나타냅니다.

        String payload = message.getPayload();
        // message.getPayload()를 호출하여 수신된 메시지의 페이로드(내용)를 문자열로 가져옵니다.

        log.info("payload = {}", payload);
        // 수신된 메시지의 페이로드를 로그로 출력합니다.
        // {}는 플레이스홀더로, payload 값이 로그 메시지에 삽입됩니다.

        /**
         *  이전 로직은 단일 세션에서 수행되는 코드였으나, 여러 세션에서 채팅이 가능하도록 변경하여 이 코드를 사용하지 않습니다.
         TextMessage textMessage = new TextMessage("Welcome");
         클라이언트에게 보낼 텍스트 메시지를 생성합니다. 여기서는 "Welcome"이라는 간단한 문자열을 메시지로 설정합니다.

         session.sendMessage(textMessage);
         session.sendMessage()를 호출하여 생성된 텍스트 메시지를 클라이언트에게 전송합니다.
         */

        ChatMessage chatMessage = objectMapper.readValue(payload,ChatMessage.class);
        ChatRoom chatRoom = chatService.findRoomById(chatMessage.getRoomId());
        chatRoom.handlerActions(session,chatMessage,chatService);
    }
}



```

## 테스트

1. 채팅방 생성
    - room ID를 저장하고 있다가 나중에 입력해주자
    
![Untitled 1](https://github.com/Sunro1994/Websocket/assets/132982907/dbba171e-1b1e-481e-9b18-727233c18f14)

    
2. 채팅방 입장
    - roomId에 위 roomId를 적고 필요한 값들을 넣어서 전달해주자
    - 예시
        
        {
        "type":"ENTER",
        "roomId":"[본인이 받은 roomId]",
        "sender":"happydaddy",
        "message":""
        }
        
![Untitled 2](https://github.com/Sunro1994/Websocket/assets/132982907/215eef69-239e-4de4-a84a-189fc7febe22)