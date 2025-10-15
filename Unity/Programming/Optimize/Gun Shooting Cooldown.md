## 🎯 Mục tiêu
Tối ưu hệ thống bắn súng trong Unity bằng cách kiểm soát thời gian giữa các lần bắn (cooldown) sử dụng `Time.time`.

---

## 🔁 Cooldown logic: `Time.time >= nextFireTime`

### ✅ Ưu điểm
- Đơn giản, hiệu quả
- Không lệ thuộc vào `Coroutine` hay `Invoke`
- Tối ưu hiệu suất cho bắn nhiều đạn nhanh (auto-fire)

---

## 📦 Biến cần thiết
```csharp
[SerializeField] private float fireRate = 0.2f; // thời gian chờ giữa các phát bắn
private float nextFireTime = 0f;
