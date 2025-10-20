---
title: "Bài 3 - Lập trình đa luồng trong Java (Multithreading)"
date: 2025-10-07
draft: false
tags: ["Java", "Đa luồng", "Multithreading", "Socket"]
categories: ["Java"]
---

- Trong các ứng dụng mạng hoặc hệ thống có nhiều tác vụ chạy song song, **đa luồng (multithreading)** giúp chương trình hoạt động **hiệu quả, nhanh chóng và mượt mà hơn**.  
- Java hỗ trợ lập trình đa luồng rất mạnh mẽ thông qua **Thread class** và **Runnable interface**.

---

## ⚙️ 1. Giới thiệu về luồng (Thread)

Một **thread (luồng)** là **đơn vị thực thi nhỏ nhất** trong chương trình.  
Khi chương trình có nhiều luồng, các luồng có thể chạy **đồng thời (concurrently)**, chia sẻ tài nguyên chung như bộ nhớ hoặc dữ liệu.

> 💡 Ví dụ: Trong một server chat, mỗi người dùng được xử lý bởi **một thread riêng**, giúp tất cả có thể trò chuyện cùng lúc mà không bị gián đoạn.

---

## 🧩 2. Cách tạo Thread trong Java

### ✅ Cách 1: Kế thừa lớp `Thread`
```java
class MyThread extends Thread {
    public void run() {
        System.out.println("Luồng đang chạy: " + Thread.currentThread().getName());
    }

    public static void main(String[] args) {
        MyThread t1 = new MyThread();
        MyThread t2 = new MyThread();

        t1.start();
        t2.start();
    }
}
```
### ✅ Cách 2: Triển khai Runnable
```java
class MyThread extends Thread {
    public void run() {
        System.out.println("Luồng đang chạy: " + Thread.currentThread().getName());
    }

    public static void main(String[] args) {
        MyThread t1 = new MyThread();
        MyThread t2 = new MyThread();

        t1.start();
        t2.start();
    }
}
```
> ⚠️ Lưu ý: Không gọi run() trực tiếp! Hãy gọi start() để tạo luồng thực sự.
---
🚀 3. Ví dụ: Server xử lý nhiều Client cùng lúc
---
Trong các ứng dụng mạng, ví dụ như chat server, ta muốn mỗi Client được xử lý trên một luồng riêng biệt.
Dưới đây là ví dụ đầy đủ về Server và Client đa luồng.

### 🖥️ Server đa luồng (Multi-threaded Server)
```java
import java.io.*;
import java.net.*;

class ClientHandler extends Thread {
    private Socket socket;

    public ClientHandler(Socket socket) {
        this.socket = socket;
    }

    public void run() {
        try {
            BufferedReader input = new BufferedReader(
                new InputStreamReader(socket.getInputStream())
            );
            PrintWriter output = new PrintWriter(socket.getOutputStream(), true);

            // Nhận tên từ client
            String name = input.readLine();
            System.out.println("Client kết nối: " + name);
            output.println("Xin chào " + name + ", bạn đã kết nối thành công!");

            // Đọc tin nhắn từ client
            String message;
            while ((message = input.readLine()) != null) {
                if (message.equalsIgnoreCase("exit")) {
                    output.println("Tạm biệt " + name + "!");
                    break;
                }
                System.out.println(name + ": " + message);
                output.println("Server nhận: " + message.toUpperCase());
            }

            socket.close();
            System.out.println("Client " + name + " đã ngắt kết nối.");
        } catch (IOException e) {
            System.out.println("Lỗi khi xử lý client: " + e.getMessage());
        }
    }
}

public class MultiThreadedServer {
    public static void main(String[] args) {
        try {
            ServerSocket server = new ServerSocket(5000);
            System.out.println("Server đang chạy trên cổng 5000...");

            while (true) {
                Socket socket = server.accept();
                System.out.println("Client mới kết nối!");
                new ClientHandler(socket).start();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

💬 Client (gửi tên và tin nhắn đến Server)
```java
import java.io.*;
import java.net.*;
import java.util.Scanner;

public class MultiClient {
    public static void main(String[] args) {
        try {
            Socket socket = new Socket("localhost", 5000);
            BufferedReader input = new BufferedReader(
                new InputStreamReader(socket.getInputStream())
            );
            PrintWriter output = new PrintWriter(socket.getOutputStream(), true);
            Scanner scanner = new Scanner(System.in);

            System.out.print("Nhập tên của bạn: ");
            String name = scanner.nextLine();
            output.println(name);

            Thread listener = new Thread(() -> {
                try {
                    String line;
                    while ((line = input.readLine()) != null) {
                        System.out.println(">> " + line);
                    }
                } catch (IOException e) {
                    System.out.println("Ngắt kết nối khỏi server.");
                }
            });
            listener.start();

            String message;
            while (true) {
                message = scanner.nextLine();
                output.println(message);
                if (message.equalsIgnoreCase("exit")) break;
            }

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