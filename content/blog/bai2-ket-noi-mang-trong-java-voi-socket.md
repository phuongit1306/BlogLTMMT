---
title: "Bài 2 - Kết nối mạng trong Java với Socket"
date: 2025-10-06
draft: false
tags: ["Java", "Lập trình mạng", "Socket"]
categories: ["Java"]

---

- Khi lập trình ứng dụng mạng trong Java, **Socket** là một trong những công cụ cơ bản và mạnh mẽ nhất giúp các thiết bị có thể **giao tiếp với nhau qua mạng**.  
- Socket cho phép tạo ra **kết nối hai chiều (two-way communication)** giữa **Client** và **Server**.

---

## 🌐 Giới thiệu về Socket

Trong Java, một **Socket** là điểm cuối (endpoint) của quá trình giao tiếp giữa hai máy tính.  
Cụ thể:

- **ServerSocket**: được sử dụng ở phía **Server** để chờ và chấp nhận kết nối.  
- **Socket**: được sử dụng ở phía **Client** để **gửi và nhận dữ liệu** đến Server.

> 📘 Lưu ý: Java cung cấp đầy đủ thư viện để xử lý Socket qua gói `java.net`.

---

## ⚙️ Cấu trúc hoạt động của Socket

1. **Server** tạo một `ServerSocket` lắng nghe (listen) trên một cổng cụ thể.  
2. **Client** gửi yêu cầu kết nối đến địa chỉ IP + cổng của Server.  
3. Khi Server chấp nhận, hai bên sẽ tạo ra **kênh giao tiếp**.  
4. Dữ liệu được truyền qua **InputStream** và **OutputStream**.

Sơ đồ minh hoạ:

[Client] ⇄ [Socket Stream] ⇄ [ServerSocket]
---

## 💻 Ví dụ: Server và Client cơ bản trong Java

### 🖥️ Server (lắng nghe và phản hồi)

```java
import java.io.*;
import java.net.*;

public class SimpleServer {
    public static void main(String[] args) {
        try {
            ServerSocket serverSocket = new ServerSocket(5000);
            System.out.println("Server đang chờ kết nối...");

            Socket socket = serverSocket.accept();
            System.out.println("Client đã kết nối!");

            BufferedReader input = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            PrintWriter output = new PrintWriter(socket.getOutputStream(), true);

            String message = input.readLine();
            System.out.println("Client: " + message);

            output.println("Xin chào Client, tôi là Server!");

            socket.close();
            serverSocket.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

💬 Client (gửi tin nhắn đến Server)
```java
import java.io.*;
import java.net.*;

public class SimpleClient {
    public static void main(String[] args) {
        try {
            Socket socket = new Socket("localhost", 5000);

            BufferedReader input = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            PrintWriter output = new PrintWriter(socket.getOutputStream(), true);

            output.println("Xin chào Server!");

            String response = input.readLine();
            System.out.println("Server: " + response);

            socket.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```

---
✍️ Tác giả: Nguyễn Thanh Phương

📅 Cập nhật: October 19, 2025

---