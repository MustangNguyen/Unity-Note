## 1. Tóm tắt
`RuntimeInitializeOnLoadMethodAttribute` là **một attribute** trong Unity cho phép đánh dấu phương thức **tĩnh** (`static`) để Unity tự động gọi khi game bắt đầu chạy.  
Phương thức này có thể được gọi **trước hoặc sau khi scene đầu tiên được load**, tùy thuộc vào `RuntimeInitializeLoadType`.

Thường dùng để:
- Khởi tạo hệ thống (logging, analytics, dependency injection…)
- Đăng ký sự kiện toàn cục
- Thiết lập config cho project ngay khi khởi động

---

## 2. Cú pháp cơ bản
```csharp
using UnityEngine;

public class MyInitializer
{
    [RuntimeInitializeOnLoadMethod]
    static void OnRuntimeMethodLoad()
    {
        Debug.Log("Game started!");
    }
}
```

## 3. Tham số thời điểm chạy

|LoadType|Thời điểm chạy|Ghi chú|
|---|---|---|
|`AfterSceneLoad` _(mặc định)_|Sau khi scene load xong, trước `Start`|Phù hợp cho setup cần dữ liệu scene|
|`BeforeSceneLoad`|Trước khi scene load|Dùng cho setup toàn cục, không phụ thuộc scene|
|`AfterAssembliesLoaded`|Ngay sau khi các assembly được load vào, trước khi scene load|Rất sớm, có thể dùng để đăng ký service|
|`BeforeSplashScreen`|Ngay sau khi Unity khởi động, trước splash screen|Dùng cho init siêu sớm (vd: anti-cheat)|
|`SubsystemRegistration`|Trước mọi thứ, thường dùng để đăng ký subsystem|Dùng nội bộ, rất hiếm khi cần|
## 4. Timeline khởi tạo

- **SubsystemRegistration** → Rất sớm, trước tất cả.
- **BeforeSplashScreen** → Trước khi hiển thị splash screen.
- **AfterAssembliesLoaded** → Sau khi load assembly, trước scene.
- **BeforeSceneLoad** → Trước khi scene load.
- Scene load → gọi `Awake` / `OnEnable`.
- **AfterSceneLoad** → Sau khi scene load, trước `Start`.
- Gọi `Start`.

## 5. Lưu ý khi sử dụng

- Phương thức phải **static** và không có tham số.
- Nếu cần sắp xếp nhiều init khác nhau, có thể kết hợp với `RuntimeInitializeLoadType` để đảm bảo thứ tự.
- Thường dùng chung với singleton hoặc hệ thống quản lý toàn cục.