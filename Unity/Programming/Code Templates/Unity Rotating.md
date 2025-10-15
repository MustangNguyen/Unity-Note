# 📌 Các Cách Để Quay Vật Thể Trong Unity  

## 1️⃣ Sử dụng `Transform.Rotate()`  
Quay vật thể theo từng khung hình.  
```csharp
void Update()
{
    transform.Rotate(0, 100 * Time.deltaTime, 0); // Quay quanh trục Y
}
```
✅ **Dễ dùng**  
❌ **Khó kiểm soát góc chính xác**  

---

## 2️⃣ Sử dụng `Transform.eulerAngles`  
Thiết lập góc quay trực tiếp.  
```csharp
void Start()
{
    transform.eulerAngles = new Vector3(0, 90, 0); // Quay 90 độ quanh Y
}
```
✅ **Trực quan**  
❌ **Có thể gặp Gimbal Lock**  

---

## 3️⃣ Sử dụng `Transform.rotation (Quaternion)`  
Dùng quaternion để đặt góc quay.  
```csharp
void Start()
{
    transform.rotation = Quaternion.Euler(0, 90, 0);
}
```
✅ **Tránh Gimbal Lock**  
❌ **Khó hiểu hơn Euler Angles**  

---

## 4️⃣ Sử dụng `Quaternion.RotateTowards()`  
Quay từ từ về một góc cụ thể.  
```csharp
void Update()
{
    Quaternion targetRotation = Quaternion.Euler(0, 180, 0);
    transform.rotation = Quaternion.RotateTowards(transform.rotation, targetRotation, 50 * Time.deltaTime);
}
```
✅ **Kiểm soát tốc độ quay**  
❌ **Không quay ngay lập tức**  

---

## 5️⃣ Sử dụng `Quaternion.Lerp()` hoặc `Quaternion.Slerp()`  
Quay mượt mà theo thời gian.  
```csharp
void Update()
{
    Quaternion targetRotation = Quaternion.Euler(0, 180, 0);
    transform.rotation = Quaternion.Lerp(transform.rotation, targetRotation, Time.deltaTime * 2);
}
```
✅ **Quay mượt, hiệu ứng đẹp**  
❌ **Cần kiểm soát thời gian tốt**  

---

## 6️⃣ Sử dụng Rigidbody để quay bằng vật lý  
Dùng `angularVelocity`:  
```csharp
Rigidbody rb;
void Start()
{
    rb = GetComponent<Rigidbody>();
    rb.angularVelocity = new Vector3(0, 1, 0);
}
```
Hoặc dùng `AddTorque()`:  
```csharp
void Start()
{
    rb = GetComponent<Rigidbody>();
    rb.AddTorque(Vector3.up * 10, ForceMode.Impulse);
}
```
✅ **Phù hợp với game vật lý**  
❌ **Khó kiểm soát góc chính xác**  

---

## 7️⃣ Quay theo hướng của một đối tượng khác (`LookAt`)  
```csharp
public Transform target;
void Update()
{
    transform.LookAt(target);
}
```
✅ **Dùng cho AI, camera**  
❌ **Không kiểm soát được trục cụ thể**  

---

## 8️⃣ Sử dụng Animation hoặc Tweening  
- Dùng Animator hoặc **DOTween**:  
```csharp
using DG.Tweening;
void Start()
{
    transform.DORotate(new Vector3(0, 180, 0), 2f);
}
```
✅ **Quay mềm mại, dễ kiểm soát**  
❌ **Cần cài DOTween/LeanTween**  

---

## 🔥 Kết Luận  
| **Trường hợp**         | **Giải pháp**                 |
|------------------------|------------------------------|
| **Quay liên tục**      | `Rotate()`, `angularVelocity` |
| **Quay đến góc cụ thể** | `eulerAngles`, `rotation`    |
| **Quay theo thời gian** | `RotateTowards()`, `Lerp()`  |
| **Quay vật lý**        | `Rigidbody`                  |
| **Quay theo hướng**    | `LookAt()`                   |
| **Tweening/Animation** | DOTween, Animator            |

---

📝 **Ghi chú:**  
- Nếu cần **quay liên tục**, hãy dùng `Rotate()`.  
- Nếu muốn **quay đến góc cố định**, hãy dùng `eulerAngles` hoặc `rotation`.  
- Nếu muốn **quay từ từ** thì dùng `RotateTowards()`, `Lerp()`, hoặc `Slerp()`.  
- Nếu cần **quay theo hướng**, hãy dùng `LookAt()`.  
- Nếu cần hiệu ứng đẹp, hãy dùng **Tweening hoặc Animator**.  