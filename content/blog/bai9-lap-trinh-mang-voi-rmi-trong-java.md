---
title: "Bài 9 - Lập trình mạng trong Java với RMI (Remote Method Invocation)"
date: 2025-10-30
draft: false
tags: ["Java", "RMI", "Distributed Systems", "Networking"]
categories: ["Java"]
---

- Khi lập trình mạng, ngoài việc truyền dữ liệu giữa Client và Server, đôi khi bạn muốn **gọi trực tiếp một hàm** trên máy khác như thể nó nằm trong cùng chương trình.  
👉 Đó chính là lúc **RMI (Remote Method Invocation)** phát huy sức mạnh!

---

## 🧠 1. Giới thiệu về RMI

**RMI (Remote Method Invocation)** là cơ chế cho phép một chương trình Java **gọi phương thức của đối tượng trên máy khác** qua mạng, giống như gọi hàm cục bộ.

📸 *Minh họa kiến trúc RMI:*
![RMI Architecture Diagram](/images/java-rmi-architecture.png)

### 💡 Ưu điểm của RMI:
- Trừu tượng hóa toàn bộ giao tiếp mạng.  
- Dễ dàng chia sẻ logic giữa Client – Server.  
- Tích hợp tốt với Java Object Serialization.

---

## ⚙️ 2. Cấu trúc chương trình RMI
📁 RMIExample/

┣ 📄 Hello.java ← Giao diện Remote

┣ 📄 HelloImpl.java ← Cài đặt giao diện

┣ 📄 Server.java ← Khởi chạy RMI Server

┣ 📄 Client.java ← Gọi phương thức từ xa


---

## 🧩 3. Tạo giao diện Remote (Hello.java)

```java
import java.rmi.Remote;
import java.rmi.RemoteException;

public interface Hello extends Remote {
    String sayHello(String name) throws RemoteException;
}
```

> 🧠 Mọi interface RMI đều phải:
- Kế thừa java.rmi.Remote
- Mỗi phương thức ném ra RemoteException

--- 

## ⚙️ 4. Cài đặt giao diện (HelloImpl.java)

```java
import java.rmi.server.UnicastRemoteObject;
import java.rmi.RemoteException;

public class HelloImpl extends UnicastRemoteObject implements Hello {
    protected HelloImpl() throws RemoteException {
        super();
    }

    @Override
    public String sayHello(String name) throws RemoteException {
        return "Xin chào, " + name + "! 👋 Chúc bạn một ngày tốt lành!";
    }
}
```

---

## 🖥️ 5. Tạo Server (Server.java)
```java
import java.rmi.registry.LocateRegistry;
import java.rmi.registry.Registry;

public class Server {
    public static void main(String[] args) {
        try {
            Hello helloObj = new HelloImpl();

            // Tạo registry tại port 1099
            Registry registry = LocateRegistry.createRegistry(1099);
            registry.rebind("HelloService", helloObj);

            System.out.println("🚀 Server đã khởi động và chờ client gọi phương thức...");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```
> 📌 Registry hoạt động như “danh bạ” lưu trữ các đối tượng có thể gọi từ xa.

---

## 💻 6. Tạo Client (Client.java)
```java
import java.rmi.registry.LocateRegistry;
import java.rmi.registry.Registry;

public class Client {
    public static void main(String[] args) {
        try {
            // Kết nối đến registry tại localhost:1099
            Registry registry = LocateRegistry.getRegistry("localhost", 1099);

            // Lấy đối tượng từ xa
            Hello hello = (Hello) registry.lookup("HelloService");

            // Gọi phương thức từ xa
            String response = hello.sayHello("Phương");
            System.out.println("📩 Phản hồi từ Server: " + response);

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

---

## ⚙️ 7. Cách chạy chương trình

### 🧱 Bước 1: Biên dịch tất cả file
```java
javac *.java
```

### 🧰 Bước 2: Khởi động RMI Registry

#### Trong terminal:
```java
rmiregistry
```
> (Giữ cửa sổ này mở!)

### 🚀 Bước 3: Chạy Server
```java
java Server
```

### 💻 Bước 4: Chạy Client
```java
java Client
```

### Kết quả hiển thị:
```java
📩 Phản hồi từ Server: Xin chào, Phương! 👋 Chúc bạn một ngày tốt lành!
```

---

## 🧠 8. Giải thích quy trình hoạt động

| Bước | Mô tả                                              |
| ---- | -------------------------------------------------- |
| 1️⃣  | Client gọi phương thức `sayHello()`                |
| 2️⃣  | Stub chuyển yêu cầu thành gói dữ liệu gửi qua mạng |
| 3️⃣  | Skeleton phía Server nhận và gọi hàm thật          |
| 4️⃣  | Kết quả trả ngược lại qua Stub                     |
| 5️⃣  | Client nhận kết quả như gọi hàm cục bộ             |

---

## 💡 9. Mở rộng & Ứng dụng

| Ứng dụng                 | Mô tả                                              |
| ------------------------ | -------------------------------------------------- |
| 💬 Chat RMI              | Mỗi client có thể gửi/nhận tin nhắn từ server      |
| 🗂 Quản lý cơ sở dữ liệu | Server thực thi câu lệnh SQL từ Client             |
| 🎮 Multiplayer Game      | Các người chơi gọi hàm cập nhật trạng thái chung   |
| ☁️ Dịch vụ phân tán      | Xây dựng microservice đơn giản giữa nhiều máy Java |

---

## 🧩 10. Ưu điểm & Nhược điểm của RMI

| Ưu điểm                     | Nhược điểm                                                   |
| --------------------------- | ------------------------------------------------------------ |
| Dễ sử dụng, hướng đối tượng | Chỉ hỗ trợ Java ↔ Java                                       |
| Giao tiếp tự nhiên qua hàm  | Cần thiết lập bảo mật RMI khi triển khai thật                |
| Phù hợp với hệ thống nội bộ | Không phổ biến bằng REST/Socket trong môi trường đa ngôn ngữ |

---

## 🏁 11. Tổng kết

Sau bài này, bạn đã học được:

- Cách sử dụng RMI để gọi hàm từ xa giữa hai chương trình Java.

- Hiểu quy trình hoạt động của Stub & Skeleton trong truyền dữ liệu.

- Có nền tảng để phát triển ứng dụng phân tán hoặc microservice bằng Java.

---

# 📘 Tổng kết chuỗi “Lập trình mạng trong Java”

| Bài | Chủ đề                     |
| --- | -------------------------- |
| 1️⃣ | Giới thiệu về Java         |
| 2️⃣ | Kết nối mạng với Socket    |
| 3️⃣ | Lập trình đa luồng         |
| 4️⃣ | Client – Server cơ bản     |
| 5️⃣ | Chat nhiều người           |
| 6️⃣ | Multithreading nâng cao    |
| 7️⃣ | Truyền dữ liệu có cấu trúc |
| 8️⃣ | Gửi và nhận file           |
| 9️⃣ | **RMI – Gọi hàm từ xa** ✅  |

--- 

✍️ Tác giả: Nguyễn Thanh Phương

📅 Cập nhật: October 20, 2025

---
