# Lambdas và Closures trong C#

## Lambdas là gì?
- **Lambda** trong C# là cách viết ngắn gọn để định nghĩa một hàm nặc danh (anonymous function).
- Ví dụ:
```csharp
  Func<int, int> doubleNumber = x => x * 2;
  Console.WriteLine(doubleNumber(5)); // Output: 10
```
Ở đây:
- `x => x * 2` là một lambda nhận một tham số `x` và trả về `x * 2`.
---
## Closures là gì?

- **Closure** trong C# là một lambda "nhớ" các biến từ phạm vi mà nó được tạo ra, ngay cả khi phạm vi đó đã kết thúc.
	
- Ví dụ:
```CS
Func<int, int> MakeMultiplier(int factor)
{
    return x => x * factor; // 'factor' được nhớ bởi lambda
}

var multiplyBy2 = MakeMultiplier(2);
Console.WriteLine(multiplyBy2(5)); // Output: 10

```
Ở đây:
- `x => x * factor` là một lambda nhớ biến `factor`, ngay cả khi hàm `MakeMultiplier` đã kết thúc.
---
## Vấn đề với Lambdas và Closures trong Vòng Lặp

Khi sử dụng lambda hoặc closures trong vòng lặp, có thể xảy ra hành vi không mong muốn.
### Ví dụ:
```cs
var actions = new List<Action>();

for (int i = 0; i < 3; i++)
{
    actions.Add(() => Console.WriteLine(i));
}

foreach (var action in actions)
{
    action();
}
```

**Kết quả mong đợi**:
```
0 
1 
2
```

**Kết quả thực tế**:

```
3 
3 
3
```
### Tại sao?

- Lambda `() => Console.WriteLine(i)` không lưu giá trị hiện tại của `i`. Thay vào đó, nó lưu **tham chiếu** đến biến `i`.
- Khi vòng lặp kết thúc, `i` có giá trị cuối cùng là `3`. Tất cả các lambda đều sử dụng giá trị này.

---
## Cách Sửa

### Giải pháp 1: Tạo Biến Cục Bộ

Tạo một biến cục bộ bên trong vòng lặp để giữ giá trị của `i`.

```cs
var actions = new List<Action>();

for (int i = 0; i < 3; i++)
{
    int localI = i; // Tạo biến cục bộ
    actions.Add(() => Console.WriteLine(localI));
}

foreach (var action in actions)
{
    action();
}
```

### Giải pháp 2: Sử dụng Closure Tường Minh

Bao bọc lambda trong một hàm riêng để đảm bảo mỗi lần gọi lambda sẽ sử dụng giá trị hiện tại của `i`.

```cs
var actions = new List<Action>();

Action CreateAction(int value)
{
    return () => Console.WriteLine(value);
}

for (int i = 0; i < 3; i++)
{
    actions.Add(CreateAction(i));
}

foreach (var action in actions)
{
    action();
}
```
