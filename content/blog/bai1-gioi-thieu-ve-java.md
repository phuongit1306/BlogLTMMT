---
title: "Bài 1 - Giới thiệu về Java"
date: 2025-10-05
draft: false
tags: ["Java", "Lập trình mạng"]
categories: ["Java"]
---

- Java là một ngôn ngữ lập trình hướng đối tượng mạnh mẽ, được sử dụng rộng rãi trong phát triển ứng dụng mạng.  
- Với Java, bạn có thể lập trình các ứng dụng server-client sử dụng **Socket**, **RMI**, và **Multithreading**.

📸 *Minh họa:*
![Features of Java](/images/dac-diem-cua-java.png)

---

## Tương tự C++, hướng đối tượng hoàn toàn
Trong quá trình tạo ra một ngôn ngữ mới phục vụ cho mục đích chạy được trên nhiều nền tảng, các kỹ sư của Sun MicroSystem muốn tạo ra một ngôn ngữ dễ học và quen thuộc với đa số người lập trình. Vì vậy họ đã sử dụng lại các cú pháp của C và C++.

Tuy nhiên, trong Java thao tác với con trỏ bị lược bỏ nhằm đảo bảo tính an toàn và dễ sử dụng hơn. Các thao tác overload, goto hay các cấu trúc như struct và union cũng được loại bỏ khỏi Java.

## Độc lập phần cứng và hệ điều hành
Một chương trình viết bằng ngôn ngữ Java có thể chạy tốt ở nhiều môi trường khác nhau. Gọi là khả năng “cross-platform”. Khả năng độc lập phần cứng và hệ điều hành được thể hiện ở 2 cấp độ là cấp độ mã nguồn và cấp độ nhị phân.

Ở cấp độ mã nguồn: Kiểu dữ liệu trong Java nhất quán cho tất cả các hệ điều hành và phần cứng khác nhau. Java có riêng một bộ thư viện để hỗ trợ vấn đề này. Chương trình viết bằng ngôn ngữ Java có thể biên dịch trên nhiều loại máy khác nhau mà không gặp lỗi.

Ở cấp độ nhị phân: Một mã biên dịch có thể chạy trên nhiều nền tảng khác nhau mà không cần dịch lại mã nguồn. Tuy nhiên cần có Java Virtual Machine để thông dịch đoạn mã này.

---

### 🧩 Một ví dụ về Socket Server:
```java
ServerSocket server = new ServerSocket(1234);
Socket client = server.accept();
System.out.println("Client connected!");
```

---
✍️ Tác giả: Nguyễn Thanh Phương

📅 Cập nhật: October 05, 2025

---