---
title: "Bài 6 - Xây dựng Chat Server nhiều người dùng (Multi-Client Chat)"
date: 2025-10-10
draft: false
tags: ["Java", "Socket", "Multithreading", "Chat", "Networking"]
categories: ["Java"]
---

- Sau khi đã tạo thành công **chat hai chiều giữa 1 client và 1 server** ở bài trước, chúng ta sẽ tiếp tục phát triển thành **chat nhiều người dùng cùng lúc**.  
- Ứng dụng này giúp **nhiều client có thể trò chuyện trong cùng một phòng chat** giống như Messenger group hoặc Discord 🔥

---

## 🧩 1. Ý tưởng hệ thống

### ⚙️ Cấu trúc hoạt động

| Thành phần | Mô tả |
|-------------|--------|
| **Server** | Chờ nhiều client kết nối, mỗi client có một luồng riêng để giao tiếp |
| **Client** | Kết nối đến server, gửi và nhận tin nhắn |
| **Broadcast** | Server gửi tin nhắn từ 1 client đến tất cả client khác |

📸 *Sơ đồ hoạt động:*
![Java Multi Chat Diagram](../images/java-multi-chat-diagram.png)

---

## 🧱 2. Kiến trúc chương trình

Chúng ta chia ứng dụng thành 3 file:

1. **ChatServer.java** – Quản lý kết nối và gửi tin nhắn đến tất cả người dùng  
2. **ClientHandler.java** – Xử lý từng client trên một luồng riêng  
3. **ChatClient.java** – Giao diện phía người dùng

---

## 🖥️ 3. Mã nguồn chi tiết

### 🧠 ChatServer.java
```java
import java.io.*;
import java.net.*;
import java.util.*;

public class ChatServer {
    private static final int PORT = 5000;
    private static Set<ClientHandler> clientHandlers = new HashSet<>();

    public static void main(String[] args) {
        System.out.println("💬 Server đang chạy trên cổng " + PORT);

        try (ServerSocket serverSocket = new ServerSocket(PORT)) {
            while (true) {
                Socket clientSocket = serverSocket.accept();
                System.out.println("✅ Client mới kết nối: " + clientSocket);

                ClientHandler clientHandler = new ClientHandler(clientSocket, clientHandlers);
                clientHandlers.add(clientHandler);

                new Thread(clientHandler).start();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
⚙️ ClientHandler.java

```java
import java.io.*;
import java.net.*;
import java.util.*;

public class ClientHandler implements Runnable {
    private Socket socket;
    private Set<ClientHandler> clientHandlers;
    private PrintWriter out;
    private String clientName;

    public ClientHandler(Socket socket, Set<ClientHandler> clientHandlers) {
        this.socket = socket;
        this.clientHandlers = clientHandlers;
    }

    @Override
    public void run() {
        try {
            BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            out = new PrintWriter(socket.getOutputStream(), true);

            out.println("Nhập tên của bạn:");
            clientName = in.readLine();
            broadcast("🔔 " + clientName + " đã tham gia phòng chat!");

            String message;
            while ((message = in.readLine()) != null) {
                if (message.equalsIgnoreCase("exit")) {
                    broadcast("🚪 " + clientName + " đã rời khỏi phòng.");
                    break;
                }
                broadcast("💬 " + clientName + ": " + message);
            }
        } catch (IOException e) {
            System.out.println("❌ Lỗi kết nối với " + clientName);
        } finally {
            try {
                socket.close();
                clientHandlers.remove(this);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    private void broadcast(String message) {
        System.out.println(message);
        for (ClientHandler client : clientHandlers) {
            if (client != this) {
                client.out.println(message);
            }
        }
    }
}
```
💻 ChatClient.java

```java
import java.io.*;
import java.net.*;

public class ChatClient {
    public static void main(String[] args) {
        String host = "localhost";
        int port = 5000;

        try (Socket socket = new Socket(host, port)) {
            System.out.println("🔗 Đã kết nối đến server!");

            BufferedReader input = new BufferedReader(new InputStreamReader(System.in));
            BufferedReader reader = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            PrintWriter writer = new PrintWriter(socket.getOutputStream(), true);

            // Luồng nhận tin nhắn
            new Thread(() -> {
                try {
                    String response;
                    while ((response = reader.readLine()) != null) {
                        System.out.println(response);
                    }
                } catch (IOException e) {
                    System.out.println("❌ Mất kết nối đến server!");
                }
            }).start();

            // Gửi tin nhắn
            String userInput;
            while ((userInput = input.readLine()) != null) {
                writer.println(userInput);
                if (userInput.equalsIgnoreCase("exit")) {
                    break;
                }
            }
        } catch (IOException e) {
            System.out.println("❌ Không thể kết nối đến server!");
        }
    }
}
```
---
## 🚀 4. Cách chạy chương trình

⚙️ Bước 1 – Chạy Server

```java
javac ChatServer.java ClientHandler.java
java ChatServer
```
⚙️ Bước 2 – Mở 2 cửa sổ Terminal khác và chạy Client

```java
javac ChatClient.java
java ChatClient
```

💡 Kết quả:
```java
💬 Server đang chạy trên cổng 5000
✅ Client mới kết nối: /127.0.0.1
✅ Client mới kết nối: /127.0.0.1
🔔 Nam đã tham gia phòng chat!
🔔 Phương đã tham gia phòng chat!
💬 Nam: Xin chào cả nhóm!
💬 Phương: Hello Nam 😄
```
---

## 🧠 5. Giải thích hoạt động

| Thành phần      | Chức năng                                    |
| --------------- | -------------------------------------------- |
| `ChatServer`    | Tạo socket server, quản lý danh sách client  |
| `ClientHandler` | Xử lý giao tiếp từng client trên luồng riêng |
| `broadcast()`   | Gửi tin nhắn đến tất cả client khác          |
| `exit`          | Client thoát khỏi phòng chat                 |

---

## 🪄 6. Mở rộng thêm

| Tính năng              | Mô tả                                                   |
| ---------------------- | ------------------------------------------------------- |
| ✅ Danh sách người dùng | Gửi danh sách người online đến các client               |
| 💬 Phòng chat riêng    | Tạo nhiều phòng khác nhau                               |
| 🖼️ Giao diện          | Dùng **JavaFX / Swing** để hiển thị cửa sổ chat đẹp hơn |
| 🔐 Bảo mật             | Mã hóa dữ liệu bằng AES hoặc SSL Socket                 |

## 🏁 7. Tổng kết

Bằng cách kết hợp:

Socket để giao tiếp mạng

Multithreading để xử lý nhiều client

Broadcast message để chia sẻ thông tin

👉 Bạn đã tạo thành công Chat Server nhiều người dùng — nền tảng cho các ứng dụng chat thực tế.

---
✍️ Tác giả: Nguyễn Thanh Phương

📅 Cập nhật: October 10, 2025

---