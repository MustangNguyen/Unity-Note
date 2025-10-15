*Note: cần override image*
``` csharp
// Override hàm raycast để xác định vùng được phép bấm xuyên

    public override bool IsRaycastLocationValid(Vector2 screenPoint, Camera eventCamera)

    {

        Debug.Log($"IsRaycastLocationValid: screenPoint: {screenPoint}, eventCamera: {eventCamera}");

        // Convert điểm màn hình về local point trong RectTransform

        RectTransformUtility.ScreenPointToLocalPointInRectangle(rectTransform, screenPoint, eventCamera, out Vector2 localPoint);

  

        // Tính toán vị trí điểm local point so với vị trí offset

        Vector2 adjustedPoint = localPoint - m_OffsetPosition;

  

        // Dùng chiều cao của rect làm bán kính, điều chỉnh bởi hệ số bán kính

        float radius = rectTransform.rect.height * 0.5f * m_RadiusFactor;

  

        // Nếu điểm nằm TRONG hình tròn thì KHÔNG chặn (cho bấm xuyên)

        if (adjustedPoint.magnitude < radius)

        {

            // Debug.Log($"IsRaycastLocationValid: Point {localPoint} (Adjusted: {adjustedPoint}) is INSIDE radius {radius}. Returning FALSE.");

            return false;

        }

  

        // Còn lại thì chặn

        // Debug.Log($"IsRaycastLocationValid: Point {localPoint} (Adjusted: {adjustedPoint}) is OUTSIDE radius {radius}. Returning TRUE.");

        return true;

    }
```
