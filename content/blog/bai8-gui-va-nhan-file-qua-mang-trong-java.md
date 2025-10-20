---
title: "Bài 8 - Gửi và nhận file qua mạng trong Java (File Transfer)"
date: 2025-10-14
draft: false
tags: ["Java", "Socket", "File Transfer", "Networking"]
categories: ["Java"]
---

- Trong các bài trước, ta đã học cách gửi và nhận **chuỗi văn bản** giữa Client và Server.  
- Ở bài này, chúng ta sẽ tiến thêm một bước để **truyền tải file (ảnh, PDF, nhạc, video, …)** giữa hai máy tính qua **Socket TCP** 📁

---

## 🧠 1. Nguyên lý hoạt động

### 🔄 Luồng dữ liệu
1. **Client** mở một file cần gửi → đọc dữ liệu thành byte[]  
2. Gửi dữ liệu này qua **Socket OutputStream**  
3. **Server** nhận dữ liệu qua **InputStream** và ghi vào file đích  

📸 *Sơ đồ hoạt động:*
![Java File Transfer Diagram](../images/java-file-transfer-diagram.png)

---

## 🧩 2. Chuẩn bị thư mục

Trước khi chạy chương trình, hãy tạo cấu trúc đơn giản:

📁 FileTransfer/

┣ 📄 FileServer.java

┣ 📄 FileClient.java

┣ 📁 send_files/ ← Nơi chứa file Client sẽ gửi

┗ 📁 received_files/ ← Nơi Server lưu file nhận được


---

## 🖥️ 3. Mã nguồn chương trình

### ⚙️ FileServer.java

```java
import java.io.*;
import java.net.*;

public class FileServer {
    public static void main(String[] args) {
        int port = 5000;

        try (ServerSocket serverSocket = new ServerSocket(port)) {
            System.out.println("💬 Server đang chờ Client gửi file...");

            Socket socket = serverSocket.accept();
            System.out.println("✅ Client đã kết nối: " + socket.getInetAddress());

            DataInputStream dis = new DataInputStream(socket.getInputStream());
            String fileName = dis.readUTF();
            long fileSize = dis.readLong();

            File file = new File("received_files/" + fileName);
            FileOutputStream fos = new FileOutputStream(file);

            byte[] buffer = new byte[4096];
            int bytesRead;
            long totalRead = 0;

            while (totalRead < fileSize && (bytesRead = dis.read(buffer)) != -1) {
                fos.write(buffer, 0, bytesRead);
                totalRead += bytesRead;
                int progress = (int) ((totalRead * 100) / fileSize);
                System.out.print("\r📦 Đang tải: " + progress + "%");
            }

            System.out.println("\n✅ File " + fileName + " đã nhận thành công!");
            fos.close();
            dis.close();
            socket.close();

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

#### 💻 FileClient.java

```java
import java.io.*;
import java.net.*;

public class FileClient {
    public static void main(String[] args) {
        String host = "localhost";
        int port = 5000;

        try (Socket socket = new Socket(host, port)) {
            System.out.println("🔗 Đã kết nối đến server.");

            File file = new File("send_files/sample.jpg"); // Thay file bạn muốn gửi
            DataOutputStream dos = new DataOutputStream(socket.getOutputStream());
            FileInputStream fis = new FileInputStream(file);

            dos.writeUTF(file.getName());
            dos.writeLong(file.length());

            byte[] buffer = new byte[4096];
            int bytesRead;
            long totalSent = 0;

            while ((bytesRead = fis.read(buffer)) != -1) {
                dos.write(buffer, 0, bytesRead);
                totalSent += bytesRead;
                int progress = (int) ((totalSent * 100) / file.length());
                System.out.print("\r🚀 Đang gửi: " + progress + "%");
            }

            System.out.println("\n✅ Gửi file thành công!");
            fis.close();
            dos.close();

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

## ⚙️ 4. Cách chạy chương trình

#### 🧱 Bước 1: Chạy Server
```java
javac FileServer.java
java FileServer
```

#### 💻 Bước 2: Chạy Client
```java
javac FileClient.java
java FileClient
```

#### Khi chạy xong, bạn sẽ thấy:
```java
💬 Server đang chờ Client gửi file...
✅ Client đã kết nối: /127.0.0.1
📦 Đang tải: 100%
✅ File sample.jpg đã nhận thành công!
```

---

## 🧩 5. Giải thích mã nguồn

| Thành phần                             | Mô tả                                                   |
| -------------------------------------- | ------------------------------------------------------- |
| `DataInputStream` / `DataOutputStream` | Dễ dàng gửi/nhận kiểu dữ liệu nguyên thủy như UTF, long |
| `FileInputStream` / `FileOutputStream` | Đọc và ghi dữ liệu file                                 |
| `byte[] buffer`                        | Bộ nhớ tạm để gửi từng phần file                        |
| `progress`                             | Tính % tiến độ gửi/nhận                                 |

---

## 🧠 6. Nâng cấp thêm

| Tính năng              | Gợi ý triển khai                                        |
| ---------------------- | ------------------------------------------------------- |
| 🔄 Gửi nhiều file      | Cho phép chọn nhiều file hoặc gửi tuần tự               |
| 📂 Giao diện JavaFX    | Thêm thanh tiến trình (ProgressBar) hiển thị trạng thái |
| 🔐 Bảo mật             | Mã hóa file bằng AES trước khi gửi                      |
| 📶 Resume Upload       | Cho phép gửi lại từ vị trí dừng khi mạng gián đoạn      |
| 🌐 Truyền qua mạng LAN | Dùng IP thật của máy trong `socket.connect()`           |

---

## 🏁 7. Tổng kết

Qua bài này, bạn đã nắm:

- Cách gửi và nhận file qua mạng bằng Java Socket.

- Hiểu rõ cơ chế Input/Output Stream hoạt động với dữ liệu lớn.

- Biết cách mở rộng ứng dụng thành trình tải file hoặc chia sẻ dữ liệu nội bộ.

--- 

✍️ Tác giả: Nguyễn Thanh Phương

📅 Cập nhật: October 20, 2025

---