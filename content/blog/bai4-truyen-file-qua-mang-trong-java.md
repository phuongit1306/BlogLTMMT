---
title: "Bài 4 - Truyền file qua mạng trong Java bằng Socket"
date: 2025-10-08
draft: false
tags: ["Java", "Socket", "Truyền file", "Lập trình mạng"]
categories: ["Java"]
---

- Trong các ứng dụng mạng, việc **truyền file (file transfer)** là một trong những chức năng quan trọng nhất.  
- Java hỗ trợ thao tác này rất tốt thông qua **Socket**, giúp chúng ta gửi và nhận dữ liệu dạng nhị phân giữa **Client và Server**.

---

## ⚙️ 1. Tổng quan về truyền file

Khi truyền file qua mạng bằng Socket:
- **Client** đọc nội dung file → gửi qua luồng **OutputStream**.  
- **Server** nhận dữ liệu qua **InputStream** → ghi lại thành file.  

Quá trình này tương tự như **copy file giữa hai máy tính**, chỉ khác là sử dụng **giao thức TCP**.

📸 *Minh họa luồng dữ liệu giữa Client và Server:*
![File Transfer Diagram](../images/file-transfer-diagram.png)

---

## 🧩 2. Mô hình hoạt động

| Thành phần | Mô tả |
|-------------|--------|
| `ServerSocket` | Tạo cổng lắng nghe (ví dụ 6000) |
| `Socket` | Kết nối giữa hai máy |
| `FileInputStream` | Đọc dữ liệu từ file (Client side) |
| `FileOutputStream` | Ghi dữ liệu ra file (Server side) |
| `Buffered` streams | Tăng tốc độ truyền dữ liệu |

---

## 🖥️ 3. Code ví dụ: Truyền file đơn giản giữa Client và Server

### 🧠 Server nhận file

```java
import java.io.*;
import java.net.*;

public class FileReceiver {
    public static void main(String[] args) {
        int port = 6000;

        try (ServerSocket serverSocket = new ServerSocket(port)) {
            System.out.println("📡 Server đang lắng nghe trên cổng " + port + "...");

            Socket socket = serverSocket.accept();
            System.out.println("✅ Client đã kết nối: " + socket.getInetAddress());

            // Tạo luồng để nhận dữ liệu từ client
            DataInputStream dis = new DataInputStream(socket.getInputStream());
            String fileName = dis.readUTF(); // nhận tên file
            long fileSize = dis.readLong();  // nhận kích thước file

            System.out.println("Đang nhận file: " + fileName + " (" + fileSize + " bytes)");

            // Tạo thư mục lưu file nếu chưa có
            File dir = new File("received_files");
            if (!dir.exists()) dir.mkdir();

            FileOutputStream fos = new FileOutputStream("received_files/" + fileName);

            byte[] buffer = new byte[4096];
            int read;
            long total = 0;

            while ((read = dis.read(buffer)) > 0) {
                fos.write(buffer, 0, read);
                total += read;
                if (total >= fileSize) break;
            }

            System.out.println("📁 Đã nhận xong file: " + fileName);
            fos.close();
            dis.close();
            socket.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
#### 🖥️ Client gửi file
```java
import java.io.*;
import java.net.*;

public class FileSender {
    public static void main(String[] args) {
        String host = "localhost";
        int port = 6000;
        String filePath = "C:/Users/ntp34/Desktop/test.txt"; // Đường dẫn file bạn muốn gửi

        try (Socket socket = new Socket(host, port)) {
            System.out.println("🔗 Đã kết nối tới server.");

            File file = new File(filePath);
            FileInputStream fis = new FileInputStream(file);
            DataOutputStream dos = new DataOutputStream(socket.getOutputStream());

            // Gửi thông tin file trước
            dos.writeUTF(file.getName());
            dos.writeLong(file.length());

            System.out.println("📤 Đang gửi file: " + file.getName());

            byte[] buffer = new byte[4096];
            int read;
            while ((read = fis.read(buffer)) > 0) {
                dos.write(buffer, 0, read);
            }

            System.out.println("✅ File đã được gửi thành công!");
            fis.close();
            dos.close();
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