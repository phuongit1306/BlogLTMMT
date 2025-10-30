---
title: "Bài 7 - Lập trình mạng với UDP trong Java"
date: 2025-10-23
draft: false
tags: ["Java", "UDP", "Datagram", "Networking"]
categories: ["Java"]
---

- Trong các bài trước, chúng ta đã làm quen với **Socket TCP**, giúp tạo kết nối ổn định giữa Client và Server.  
- Tuy nhiên, trong nhiều ứng dụng như **trò chơi trực tuyến, stream video/audio**, ta cần **tốc độ cao hơn**, dù có thể **mất một ít dữ liệu**.

Đó chính là lúc giao thức **UDP (User Datagram Protocol)** được sử dụng ⚡

---

## 🧠 1. Tổng quan về UDP

| Đặc điểm | Mô tả |
|-----------|--------|
| **Không kết nối (Connectionless)** | Không cần thiết lập kết nối giữa client & server |
| **Nhanh hơn TCP** | Không có xác nhận gói tin nên tốc độ cao hơn |
| **Không đảm bảo dữ liệu đến** | Gói tin có thể bị mất, trễ hoặc đến sai thứ tự |
| **Ứng dụng phổ biến** | Video call, voice chat, game online, livestream |

📸 *Minh họa hoạt động UDP:*
![Java UDP Diagram](../images/java-udp-diagram.png)

---

## 🧩 2. Các lớp chính trong Java UDP

| Lớp | Mô tả |
|------|--------|
| `DatagramSocket` | Dùng để gửi hoặc nhận gói tin UDP |
| `DatagramPacket` | Đại diện cho một gói dữ liệu được gửi/nhận |
| `InetAddress` | Đại diện cho địa chỉ IP của máy chủ hoặc client |

---

## 💻 3. Mã nguồn ví dụ: Gửi & nhận dữ liệu UDP

Dưới đây là ví dụ về **Server** và **Client** giao tiếp qua UDP.

---

### 🖥️ UDPServer.java

```java
import java.net.*;

public class UDPServer {
    public static void main(String[] args) {
        try {
            DatagramSocket socket = new DatagramSocket(9876);
            byte[] receiveData = new byte[1024];
            byte[] sendData;

            System.out.println("💬 UDP Server đang chờ gói tin...");

            while (true) {
                // Nhận dữ liệu từ Client
                DatagramPacket receivePacket = new DatagramPacket(receiveData, receiveData.length);
                socket.receive(receivePacket);

                String message = new String(receivePacket.getData(), 0, receivePacket.getLength());
                System.out.println("📩 Nhận từ Client: " + message);

                // Gửi phản hồi lại cho Client
                InetAddress clientAddress = receivePacket.getAddress();
                int clientPort = receivePacket.getPort();

                String response = "Server nhận được: " + message;
                sendData = response.getBytes();

                DatagramPacket sendPacket = new DatagramPacket(sendData, sendData.length, clientAddress, clientPort);
                socket.send(sendPacket);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

### 💻 UDPClient.java

```java
import java.net.*;
import java.util.Scanner;

public class UDPClient {
    public static void main(String[] args) {
        try {
            DatagramSocket socket = new DatagramSocket();
            InetAddress serverAddress = InetAddress.getByName("localhost");
            Scanner scanner = new Scanner(System.in);

            while (true) {
                System.out.print("Nhập tin nhắn gửi đến server: ");
                String message = scanner.nextLine();

                byte[] sendData = message.getBytes();
                DatagramPacket sendPacket = new DatagramPacket(sendData, sendData.length, serverAddress, 9876);
                socket.send(sendPacket);

                // Nhận phản hồi từ server
                byte[] receiveData = new byte[1024];
                DatagramPacket receivePacket = new DatagramPacket(receiveData, receiveData.length);
                socket.receive(receivePacket);

                String response = new String(receivePacket.getData(), 0, receivePacket.getLength());
                System.out.println("💻 Phản hồi từ server: " + response);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```
---
## ⚙️ 4. Cách chạy chương trình

#### Bước 1 – Chạy Server:

```java
javac UDPServer.java
java UDPServer
```

#### Bước 2 – Chạy Client:
```java
javac UDPClient.java
java UDPClient
```
#### 📟 Kết quả ví dụ:
```java
💬 UDP Server đang chờ gói tin...
📩 Nhận từ Client: Xin chào Server!
```

#### Và ở phía Client:
```java
Nhập tin nhắn gửi đến server: Xin chào Server!
💻 Phản hồi từ server: Server nhận được: Xin chào Server!
```

---

## 🔍 5. So sánh TCP và UDP

| Tiêu chí            | TCP                           | UDP                         |
| ------------------- | ----------------------------- | --------------------------- |
| **Kết nối**         | Có kết nối (connection-based) | Không kết nối               |
| **Đảm bảo dữ liệu** | Có (retransmit nếu lỗi)       | Không đảm bảo               |
| **Tốc độ**          | Chậm hơn                      | Nhanh hơn                   |
| **Ứng dụng**        | Web, email, FTP               | Game, video call, streaming |

---

## 🪄 6. Mở rộng thêm

| Tính năng            | Gợi ý                                                          |
| -------------------- | -------------------------------------------------------------- |
| 🌐 Gửi file qua UDP  | Chia file thành nhiều gói nhỏ rồi gửi tuần tự                  |
| 🧠 Reliable UDP      | Tự xây cơ chế kiểm tra gói mất và gửi lại                      |
| 🕹️ Game Multiplayer | Dùng UDP để gửi dữ liệu vị trí người chơi trong thời gian thực |
| 🔒 Bảo mật           | Mã hóa gói tin bằng AES trước khi gửi                          |

---

## 🏁 7. Kết luận

- UDP nhanh, nhẹ, phù hợp cho các ứng dụng yêu cầu tốc độ cao hơn độ chính xác tuyệt đối.

- Tuy nhiên, cần tự xử lý lỗi và kiểm tra dữ liệu nếu muốn đảm bảo an toàn.

- Java hỗ trợ UDP cực kỳ dễ dàng thông qua DatagramSocket và DatagramPacket.

---

✍️ Tác giả: Nguyễn Thanh Phương

📅 Cập nhật: October 20, 2025

---