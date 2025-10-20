---
title: "Bài 10 - Xử lý Bất đồng bộ trong JavaScript – Hiểu rõ Callback, Promise và Async/Await"
date: 2025-10-21
draft: false
tags: ["JavaScript", "Lập trình web", "Bất đồng bộ", "Async Await"]
categories: ["JavaScript"]
---

- JavaScript là ngôn ngữ **đơn luồng (single-threaded)**, nghĩa là nó chỉ thực hiện **một tác vụ tại một thời điểm**.  
- Vậy làm sao JavaScript có thể xử lý được những tác vụ **mất thời gian** như tải dữ liệu từ server, đọc file, hay đợi người dùng thao tác mà **không làm treo trình duyệt**?

👉 Câu trả lời nằm ở **cơ chế xử lý bất đồng bộ (asynchronous programming)**.

---

## ⚙️ 1. Cơ chế Event Loop

- Khi chạy JavaScript, các tác vụ được đưa vào **Call Stack**, và các công việc bất đồng bộ (như `setTimeout`, `fetch`, hay `event listener`) sẽ được **Web APIs** xử lý, sau đó đẩy về **Callback Queue** khi hoàn tất.  
- **Event Loop** liên tục kiểm tra xem Stack có rảnh không để đẩy callback vào thực thi.

📸 *Minh họa cơ chế Event Loop:*
![Event Loop JavaScript](/images/js-event-loop.png)

---

## 🧩 2. Callback – Cách cổ điển để xử lý bất đồng bộ

```javascript
console.log("Bắt đầu tải dữ liệu...");

setTimeout(() => {
  console.log("✅ Dữ liệu đã được tải!");
}, 2000);

console.log("Đang chờ dữ liệu...");
```
🧠 Kết quả:
```java
Bắt đầu tải dữ liệu...
Đang chờ dữ liệu...
✅ Dữ liệu đã được tải!
```
> Callback giúp xử lý bất đồng bộ, nhưng nếu lồng nhiều callback sẽ dẫn đến callback hell.

📉 Minh họa Callback Hell:
![Event Loop JavaScript](/images/call-back-hell.png)

---

## 🚀 3. Promise – Giải pháp hiện đại hơn
Promise đại diện cho một giá trị sẽ có trong tương lai (thành công hoặc thất bại).
```java
function fetchData() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve("✅ Dữ liệu tải thành công!");
    }, 2000);
  });
}

console.log("Bắt đầu...");

fetchData()
  .then((result) => console.log(result))
  .catch((error) => console.error("Lỗi:", error))
  .finally(() => console.log("Hoàn tất"));
```

#### 🧩 Kết quả:
```java
Bắt đầu...
✅ Dữ liệu tải thành công!
Hoàn tất
```

---

## ⏳ 4. Async / Await – Cú pháp hiện đại, dễ đọc
async và await giúp viết code bất đồng bộ trông như đồng bộ, dễ hiểu và dễ debug hơn.

```java
async function getData() {
  console.log("⏳ Đang tải...");
  const data = await new Promise((resolve) =>
    setTimeout(() => resolve("✅ Tải xong dữ liệu!"), 2000)
  );
  console.log(data);
}

getData();
console.log("➡️ Hàm vẫn tiếp tục chạy...");
```

Kết quả:
```java
⏳ Đang tải...
➡️ Hàm vẫn tiếp tục chạy...
✅ Tải xong dữ liệu!
```

---

## 🌐 5. Ví dụ thực tế: Gọi API từ Server

```java
async function getUserData() {
  try {
    const response = await fetch("https://jsonplaceholder.typicode.com/users/1");
    const user = await response.json();
    console.log("👤 Tên người dùng:", user.name);
  } catch (error) {
    console.error("⚠️ Lỗi:", error);
  }
}

getUserData();
```


---

## 🧠 6. So sánh 3 cách tiếp cận

| Cách            | Ưu điểm                    | Nhược điểm                    |
| --------------- | -------------------------- | ----------------------------- |
| **Callback**    | Dễ hiểu, cơ bản            | Gây “callback hell”           |
| **Promise**     | Dễ quản lý chuỗi công việc | Cú pháp `.then()` hơi rườm rà |
| **Async/Await** | Đọc dễ như code đồng bộ    | Phải chạy trong hàm `async`   |

---

## ⚙️ 7. Một ví dụ kết hợp: Promise + Async/Await
```java
function downloadFile(fileName) {
  return new Promise((resolve) => {
    setTimeout(() => resolve(`📦 ${fileName} tải xong!`), 1500);
  });
}

async function downloadAll() {
  const files = ["data1.json", "data2.json", "data3.json"];
  for (const file of files) {
    const msg = await downloadFile(file);
    console.log(msg);
  }
  console.log("🎉 Tất cả file đã tải hoàn tất!");
}

downloadAll();
```

---

## 💡 8. Tổng kết

- Callback là nền tảng cho mọi cơ chế bất đồng bộ.

- Promise giúp code gọn gàng hơn.

- Async/Await mang lại trải nghiệm gần như lập trình đồng bộ.

- Luôn sử dụng try...catch trong async để xử lý lỗi.

---

## 🧭 9. Ứng dụng trong thực tế

| Ứng dụng           | Mô tả                              |
| ------------------ | ---------------------------------- |
| 🌐 Gọi API         | Lấy dữ liệu từ backend             |
| 🎮 Game online     | Chờ dữ liệu từ server hoặc đối thủ |
| 📷 Ứng dụng camera | Chờ phản hồi từ thiết bị           |
| 📦 Tải dữ liệu lớn | Quản lý tiến trình tải file        |

---

✍️ Tác giả: Nguyễn Thanh Phương

📅 Cập nhật: October 20, 2025

---