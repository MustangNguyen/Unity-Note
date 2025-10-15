# 📌 C# MethodInfo Cheat Sheet
``` csharp
public object Invoke(object obj, object[] parameters);
```
## 📖 Giới thiệu

`MethodInfo` thuộc namespace `System.Reflection`, cho phép lấy thông tin về phương thức trong runtime, bao gồm tên, tham số, kiểu trả về và thực thi phương thức.

## 🔹 1. Lấy MethodInfo từ một class

```csharp
using System;
using System.Reflection;

class Example
{
    public void SayHello() => Console.WriteLine("Hello, World!");
}

class Program
{
    static void Main()
    {
        MethodInfo method = typeof(Example).GetMethod("SayHello");
        Console.WriteLine(method.Name); // Output: SayHello
    }
}
```

## 🔹 2. Gọi phương thức bằng MethodInfo

```csharp
Example obj = new Example();
MethodInfo method = typeof(Example).GetMethod("SayHello");
method.Invoke(obj, null); // Output: Hello, World!
```

## 🔹 3. Lấy danh sách phương thức của một class

```csharp
MethodInfo[] methods = typeof(Example).GetMethods();
foreach (var method in methods)
{
    Console.WriteLine(method.Name);
}
```

## 🔹 4. Lấy thông tin tham số của một phương thức

```csharp
class Example
{
    public void Greet(string name, int age) {}
}

MethodInfo method = typeof(Example).GetMethod("Greet");
ParameterInfo[] parameters = method.GetParameters();
foreach (var param in parameters)
{
    Console.WriteLine($"{param.Name}: {param.ParameterType}");
}
```

## 🔹 5. Kiểm tra kiểu trả về của phương thức

```csharp
MethodInfo method = typeof(Example).GetMethod("Greet");
Console.WriteLine(method.ReturnType); // Output: System.Void
```

## 🔹 6. Gọi phương thức có tham số

```csharp
Example obj = new Example();
MethodInfo method = typeof(Example).GetMethod("Greet");
method.Invoke(obj, new object[] { "Alice", 25 });
```

## 🔹 7. Lấy phương thức static

```csharp
class StaticExample
{
    public static void StaticMethod() => Console.WriteLine("Static Method Called");
}

MethodInfo method = typeof(StaticExample).GetMethod("StaticMethod");
method.Invoke(null, null); // Không cần đối tượng
```

## 🔹 8. Lấy phương thức generic

```csharp
class GenericExample
{
    public void Print<T>(T value) => Console.WriteLine(value);
}

MethodInfo method = typeof(GenericExample).GetMethod("Print");
MethodInfo genericMethod = method.MakeGenericMethod(typeof(int));
genericMethod.Invoke(new GenericExample(), new object[] { 42 });
```

## 🔹 9. Lấy phương thức có BindingFlags (Private, Static...)

```csharp
MethodInfo privateMethod = typeof(Example).GetMethod("PrivateMethod", BindingFlags.NonPublic | BindingFlags.Instance);
```

## 🎯 Lưu ý

- `GetMethod("Tên phương thức")` chỉ lấy phương thức **public** mặc định.
- Dùng `BindingFlags` nếu muốn lấy phương thức `private`, `static`.
- `Invoke()` có thể ném `TargetInvocationException` nếu có lỗi bên trong phương thức.

---

📌 **Ứng dụng thực tế**:

- **Tạo Plugin hệ thống** (Gọi phương thức từ DLL bên ngoài).
- **Lập trình hướng dữ liệu** (Gọi phương thức dựa trên JSON, Database).
- **Debugging và Unit Testing** (Tự động kiểm tra phương thức bằng Reflection).