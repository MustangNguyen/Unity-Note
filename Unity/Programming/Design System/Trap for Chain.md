## Thiết kế Trap cho Chain/Block

### Mục tiêu
- Đưa vào 2 trap:
  - **FreezeChain**: khóa di chuyển của 1 chain cho tới khi đủ số lần “bóng vào hố”.
  - **MysteryColor**: bóng dấu hỏi, chỉ lộ màu khi bóng liền kề biến mất.
- Dùng kiến trúc chung, gọn, dễ mở rộng, không phá vỡ logic màu hiện có.

### Phạm vi & Nguyên tắc
- **Scope trap**:
  - **Chain-scope**: tác động lên toàn bộ 1 chain (`RopeManager`).
  - **Block-scope**: tác động lên 1 bóng (`Block`).
- **Event-driven**: trap nghe sự kiện game: “bóng vào hố”, “đứt liên kết hàng xóm”.
- **Lifecycle rõ ràng**: Apply → Active → Remove; luôn unsubscribe event khi Remove/Destroy.
- **Hiển thị tách logic**: ẩn/hiện màu là UI; không thêm “Unknown” vào enum màu.

---

## Kiến trúc tổng quan

### Enum (khuyến nghị)
- `TrapScope { Chain, Block }`
- `TrapKind { FreezeChain, MysteryColor }`

### EventBus (đơn giản)
- `BallScored(Block b)` – bắn khi một bóng “vào hố”.
- `BlockNeighborUnlinked(Block neighbor, Block removed)` – bắn cho mỗi hàng xóm của bóng vừa bị gỡ khỏi chuỗi.

Ví dụ khung:
```csharp
using System;
using UnityEngine;

public static class GameEvents
{
	public static Action<Block> BallScored;
	public static Action<Block, Block> BlockNeighborUnlinked;
}
```

### Interface Trap (dùng lại cho mọi trap)
```csharp
public interface ITrap<in TTarget>
{
	bool IsActive { get; }
	void Apply(TTarget target);
	void Remove();
}
```

### API cần ở target
- `RopeManager`
  - `void SetMovementLocked(bool locked)` – khóa input/di chuyển chain.
- `Block`
  - `void SetRenderQuestionMark(bool on)` – đổi sprite/UI dấu hỏi.
  - `void SetRenderColor(BlockColor color)` – hiển thị màu thật (không đổi enum/logic bên trong nếu đã có).

---

## Trap 1: FreezeChain (đóng băng chain)

### Hành vi
- Khi Apply: khóa di chuyển chain.
- Nghe `BallScored` để tăng tiến độ “phá băng”.
- Khi đủ số lượng yêu cầu: mở khóa, Remove.

### Cấu hình
- `requiredCount`: số lần cần “bóng vào hố” để phá băng.
- Tùy chọn filter:
  - **Global**: mọi bóng vào hố đều tính.
  - **By-chain**: chỉ tính bóng thuộc chain này (cần hàm kiểm tra sở hữu/thuộc chuỗi).

### Mẫu triển khai gọn
```csharp
using UnityEngine;

public class ChainFreeze : MonoBehaviour, ITrap<RopeManager>
{
	[SerializeField] int requiredCount = 3;

	RopeManager targetChain;
	int progress;
	public bool IsActive { get; private set; }

	public void Apply(RopeManager target)
	{
		if (IsActive) return;
		targetChain = target;
		progress = 0;
		IsActive = true;

		targetChain.SetMovementLocked(true);
		GameEvents.BallScored += OnBallScored;

		Debug.Log($"[TRAP] Freeze applied need={requiredCount}");
	}

	void OnBallScored(Block scored)
	{
		if (!IsActive) return;

		// Optional filter: nếu cần chỉ tính bóng thuộc chain này
		// if (!targetChain.Contains(scored)) return;

		progress++;
		Debug.Log($"[TRAP] Freeze progress {progress}/{requiredCount}");

		if (progress >= requiredCount)
		{
			Remove();
		}
	}

	public void Remove()
	{
		if (!IsActive) return;

		IsActive = false;
		targetChain.SetMovementLocked(false);
		GameEvents.BallScored -= OnBallScored;

		Debug.Log("[TRAP] Freeze removed");
		Destroy(this);
	}
}
```

### Gợi ý stacking
- Cộng dồn `requiredCount` nếu Apply khi đang active.
- Hoặc “thay thế”: reset progress, giữ `requiredCount` mới nhất.

---

## Trap 2: MysteryColor (bóng dấu hỏi)

### Hành vi
- Khi Apply: ẩn màu thật, hiển thị dấu hỏi.
- Nghe `BlockNeighborUnlinked`; khi hàng xóm liền kề của chính bóng này biến mất → Reveal màu thật.
- Tự Remove sau khi lộ màu.

### Lưu dữ liệu
- `realColor`: màu thật của bóng.
- `hidden`: cờ đang ẩn.

### Mẫu triển khai gọn
```csharp
using UnityEngine;

public class MysteryColor : MonoBehaviour, ITrap<Block>
{
	Block target;
	BlockColor realColor;
	public bool IsActive { get; private set; }

	public void Apply(Block block)
	{
		if (IsActive) return;
		target = block;

		// Lưu lại màu thật (tùy codebase là lấy từ đâu)
		realColor = target.CurrentColor; // hoặc getter tương đương
		target.SetRenderQuestionMark(true);

		IsActive = true;
		GameEvents.BlockNeighborUnlinked += OnNeighborUnlinked;

		Debug.Log("[TRAP] Mystery applied");
	}

	void OnNeighborUnlinked(Block neighbor, Block removed)
	{
		if (!IsActive) return;
		if (neighbor != target) return;

		Reveal();
	}

	void Reveal()
	{
		if (!IsActive) return;

		target.SetRenderQuestionMark(false);
		target.SetRenderColor(realColor);

		Remove();
		Debug.Log($"[TRAP] Mystery revealed: {realColor}");
	}

	public void Remove()
	{
		if (!IsActive) return;

		IsActive = false;
		GameEvents.BlockNeighborUnlinked -= OnNeighborUnlinked;
		Destroy(this);
	}
}
```

---

## Điểm hook sự kiện trong game

### Khi “bóng vào hố”
- Bắn sự kiện sau khi xử lý score/loại bóng thành công:
```csharp
// nơi kết thúc logic ghi điểm cho 1 bóng
GameEvents.BallScored?.Invoke(scoredBlock);
```

### Khi “đứt liên kết hàng xóm”
- Sau khi remove 1 block khỏi chuỗi, bắn cho mỗi hàng xóm còn tồn tại:
```csharp
// giả sử removed có prev/next trước khi unlink
if (prev != null) GameEvents.BlockNeighborUnlinked?.Invoke(prev, removed);
if (next != null) GameEvents.BlockNeighborUnlinked?.Invoke(next, removed);
```

---

## Tích hợp tối thiểu

- `RopeManager`
  - Thêm `SetMovementLocked(bool locked)` để:
    - Chặn input kéo/thao tác.
    - Dừng cập nhật vị trí nếu có tween/physics custom.

- `Block`
  - Thêm/đảm bảo API hiển thị:
    - `void SetRenderQuestionMark(bool on)`
    - `void SetRenderColor(BlockColor color)`
  - Cung cấp getter màu hiện tại nếu cần: `BlockColor CurrentColor { get; }`

- Chỗ spawn trap:
  - Với FreezeChain: `chain.gameObject.AddComponent<ChainFreeze>().Apply(chain);`
  - Với MysteryColor: `block.gameObject.AddComponent<MysteryColor>().Apply(block);`

---

## Edge cases & Quy ước

- **Freeze vẫn active khi chain bị hủy?**
  - Remove trong `OnDestroy` hoặc kiểm tra null target.
- **MysteryColor bị rơi vào hố trước khi reveal**
  - Cho phép destroy bình thường; `OnDestroy` ensure unsubscribe event.
- **Nhiều trap cùng lúc**
  - Mỗi trap là 1 component độc lập; đồng thời được, miễn không mâu thuẫn API.
- **Hiệu năng**
  - Event nhẹ, không loop toàn bộ scene; chỉ subscribe khi active.
- **Debug**
  - Dùng tag `[TRAP]` cho tất cả log.

---

## Mở rộng trong tương lai
- Thêm `ITrapConfig` (ScriptableObject) để cấu hình `requiredCount`, hiệu ứng UI, âm thanh.
- Thêm các trigger khác: theo thời gian, theo combo, theo vị trí.
- Thêm hệ thống `TrapManager` trung gian để quản trap theo chain/block (thống kê, stacking policy).

---

- **KQ chính**: 1 lớp event nhẹ (`GameEvents`), 1 interface chung (`ITrap<T>`), 2 component trap độc lập (`ChainFreeze`, `MysteryColor`).
- **FreezeChain**: khóa di chuyển đến khi đủ số lần `BallScored`.
- **MysteryColor**: ẩn màu, lộ khi hàng xóm bị gỡ (vào hố hoặc phá chuỗi).
