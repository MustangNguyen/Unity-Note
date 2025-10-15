## So Sánh: `(tile as CustomTile).TileID` vs `(CustomTile)tile.TileID`

### **1. `(tile as CustomTile).TileID`**

#### Cách Hoạt Động:

- Sử dụng toán tử `as` để **ép kiểu an toàn** đối tượng `tile` sang kiểu `CustomTile`.
- Nếu `tile` không phải là đối tượng kiểu `CustomTile`, kết quả của phép ép kiểu sẽ là `null`.
- Truy cập `TileID` chỉ hoạt động nếu phép ép kiểu thành công (tức là `tile` thực sự là `CustomTile`).

#### **Ưu Điểm:**

- An toàn: Tránh ném ra ngoại lệ `InvalidCastException` nếu `tile` không phải là `CustomTile`.
- Phù hợp khi bạn không chắc chắn về kiểu của `tile`.

#### **Nhược Điểm:**

- Cần kiểm tra `null` nếu `tile` không phải là `CustomTile`, nếu không sẽ gây ra lỗi `NullReferenceException`.

#### **Ví Dụ:**

```csharp
CustomTile customTile = tile as CustomTile;
if (customTile != null)
{
    Console.WriteLine(customTile.TileID);
}
else
{
    Console.WriteLine("tile is not a CustomTile.");
}
```

---

### **2. `(CustomTile)tile.TileID`**

#### Cách Hoạt Động:

- Sử dụng toán tử `(CustomTile)` để **ép kiểu mạnh** đối tượng `tile` sang kiểu `CustomTile`.
- Nếu `tile` không phải là đối tượng kiểu `CustomTile`, **ngoại lệ `InvalidCastException` sẽ được ném ra**.

#### **Ưu Điểm:**

- Hiệu quả hơn trong các trường hợp bạn **chắc chắn** `tile` luôn là một đối tượng kiểu `CustomTile`.
- Tránh việc cần kiểm tra `null`.

#### **Nhược Điểm:**

- Không an toàn: Nếu `tile` không phải là `CustomTile`, chương trình sẽ bị dừng do ngoại lệ.

#### **Ví Dụ:**

```csharp
try
{
    CustomTile customTile = (CustomTile)tile;
    Console.WriteLine(customTile.TileID);
}
catch (InvalidCastException)
{
    Console.WriteLine("tile is not a CustomTile.");
}
```

---

### **So Sánh Tổng Quát**

| Cú Pháp                       | Khi Nào Dùng                             | Rủi Ro                                        |
| ----------------------------- | ---------------------------------------- | --------------------------------------------- |
| `(tile as CustomTile).TileID` | Khi bạn không chắc chắn kiểu của `tile`  | Cần kiểm tra `null`.                          |
| `(CustomTile)tile.TileID`     | Khi bạn chắc chắn `tile` là `CustomTile` | Ngoại lệ nếu `tile` không phải kiểu mong đợi. |

---

### **Khi Nào Sử Dụng Cái Nào?**

1. **Dùng `(tile as CustomTile).TileID`**:
    
    - Khi bạn muốn kiểm tra kiểu một cách an toàn.
    - Khi kiểu của `tile` có thể không phải là `CustomTile`.
2. **Dùng `(CustomTile)tile.TileID`**:
    
    - Khi bạn **chắc chắn** `tile` luôn là kiểu `CustomTile` (ví dụ, kiểm tra kiểu trước hoặc trong môi trường được kiểm soát).

Nếu không chắc chắn, hãy chọn cách an toàn với toán tử `as` để tránh lỗi không mong muốn.