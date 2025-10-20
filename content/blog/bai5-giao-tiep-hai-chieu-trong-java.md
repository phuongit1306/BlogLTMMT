---
title: "Bài 5 - Giao tiếp hai chiều trong Java bằng Socket (Chat Realtime)"
date: 2025-10-09
draft: false
tags: ["Java", "Socket", "Chat", "Realtime", "Đa luồng"]
categories: ["Java"]
---

Trong các bài trước, ta đã học cách:
- Kết nối Client ↔ Server bằng Socket.
- Gửi và nhận dữ liệu một chiều (Client gửi, Server nhận).
- Truyền file qua mạng.

👉 Trong bài này, chúng ta sẽ nâng cấp:  
**Cả hai bên đều có thể gửi & nhận tin nhắn cùng lúc** — tức là **giao tiếp hai chiều (bidirectional communication)**, như một ứng dụng chat cơ bản.

---

## 🧩 1. Kiến thức nền tảng

### ⚙️ Mô hình hoạt động

| Thành phần | Mô tả |
|-------------|--------|
| `ServerSocket` | Server lắng nghe yêu cầu kết nối |
| `Socket` | Cầu nối giữa 2 bên |
| `InputStream / OutputStream` | Dùng để đọc & gửi dữ liệu |
| `Thread` | Tạo luồng riêng cho việc **nhận tin nhắn** để tránh bị chặn chương trình |

📸 *Minh họa hoạt động chat song công:*
![Java Chat Diagram](../images/java-chat-diagram.png)

---

## 🧱 2. Ý tưởng

- Khi Client kết nối tới Server:
  - Server tạo một **luồng riêng** để nhận tin nhắn từ Client.
  - Ngược lại, Client cũng có luồng riêng để nhận tin từ Server.
- Người dùng có thể nhập tin nhắn (trong console) để gửi đi **mà không bị chặn** bởi việc nhận tin nhắn.

---

## 💬 3. Code ví dụ: Chat hai chiều đơn giản

### 🖥️ Server (ChatServer.java)

```java
import java.io.*;
import java.net.*;

public class ChatServer {
    public static void main(String[] args) {
        int port = 5000;

        try (ServerSocket serverSocket = new ServerSocket(port)) {
            System.out.println("💬 Server đang chạy, chờ Client kết nối...");

            Socket socket = serverSocket.accept();
            System.out.println("✅ Client đã kết nối: " + socket.getInetAddress());

            // Luồng nhận tin từ Client
            new Thread(() -> {
                try {
                    BufferedReader reader = new BufferedReader(new InputStreamReader(socket.getInputStream()));
                    String message;
                    while ((message = reader.readLine()) != null) {
                        System.out.println("👤 Client: " + message);
                    }
                } catch (IOException e) {
                    System.out.println("❌ Mất kết nối với Client!");
                }
            }).start();

            // Luồng gửi tin tới Client
            PrintWriter writer = new PrintWriter(socket.getOutputStream(), true);
            BufferedReader console = new BufferedReader(new InputStreamReader(System.in));

            String input;
            while ((input = console.readLine()) != null) {
                writer.println(input);
            }

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
#### 🖥️ Client (ChatClient.java)

```java
import java.io.*;
import java.net.*;

public class ChatClient {
    public static void main(String[] args) {
        String host = "localhost";
        int port = 5000;

        try (Socket socket = new Socket(host, port)) {
            System.out.println("🔗 Đã kết nối đến server!");

            // Luồng nhận tin từ Server
            new Thread(() -> {
                try {
                    BufferedReader reader = new BufferedReader(new InputStreamReader(socket.getInputStream()));
                    String message;
                    while ((message = reader.readLine()) != null) {
                        System.out.println("💻 Server: " + message);
                    }
                } catch (IOException e) {
                    System.out.println("❌ Mất kết nối với Server!");
                }
            }).start();

            // Luồng gửi tin đến Server
            PrintWriter writer = new PrintWriter(socket.getOutputStream(), true);
            BufferedReader console = new BufferedReader(new InputStreamReader(System.in));

            String input;
            while ((input = console.readLine()) != null) {
                writer.println(input);
            }

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
---
🧠 4. Giải thích
| Bước | Hoạt động                                                |
| ---- | -------------------------------------------------------- |
| 1️⃣  | Server tạo `ServerSocket` và chờ kết nối                 |
| 2️⃣  | Client kết nối đến Server                                |
| 3️⃣  | Mỗi bên tạo một luồng riêng để **nhận dữ liệu liên tục** |
| 4️⃣  | Người dùng nhập tin nhắn trên console → gửi đi           |
| 5️⃣  | Luồng nhận sẽ hiển thị tin nhắn từ bên kia               |

Nhờ có đa luồng, chương trình không bị "treo" khi chờ dữ liệu.

---
✍️ Tác giả: Nguyễn Thanh Phương

📅 Cập nhật: October 19, 2025

---