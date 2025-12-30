```CS

using UnityEngine;
using UnityEngine.UI;

/// <summary>
/// Scale RectTransform của Image sao cho cover parent (giữ nguyên tỷ lệ).
/// Image sẽ được scale cho đến khi cả width và height >= parent.
/// Phần thừa sẽ bị cắt ra ngoài (cần parent có mask hoặc overflow hidden).
/// </summary>
[ExecuteAlways]
[RequireComponent(typeof(RectTransform))]
public class ImageCoverFitter : MonoBehaviour
{
    #region Fields
    [Header("References")]
    [SerializeField] private Graphic targetGraphic;
    
    [Header("Settings")]
    [Tooltip("Tự động cập nhật khi sprite thay đổi")]
    [SerializeField] private bool autoRefresh = true;
    
    [Tooltip("Cập nhật liên tục mỗi frame (dùng khi parent size thay đổi runtime)")]
    [SerializeField] private bool continuousUpdate = false;
    
    private RectTransform m_rectTransform;
    private RectTransform m_parentRectTransform;
    private Sprite m_lastSprite;
    private Texture m_lastTexture;
    private Vector2 m_lastParentSize;
    #endregion

    #region Unity Lifecycle
    private void Awake()
    {
        CacheReferences();
    }

    private void OnEnable()
    {
        Refresh();
    }

    private void Update()
    {
        if (!autoRefresh && !continuousUpdate) return;
        
        bool needsRefresh = false;
        
        // Check sprite/texture changed
        if (autoRefresh && HasVisualChanged())
        {
            needsRefresh = true;
        }
        
        // Check parent size changed
        if (continuousUpdate && m_parentRectTransform != null)
        {
            Vector2 parentSize = m_parentRectTransform.rect.size;
            if (parentSize != m_lastParentSize)
            {
                m_lastParentSize = parentSize;
                needsRefresh = true;
            }
        }
        
        if (needsRefresh)
        {
            Refresh();
        }
    }
    
#if UNITY_EDITOR
    private void OnValidate()
    {
        if (!Application.isPlaying)
        {
            UnityEditor.EditorApplication.delayCall += () =>
            {
                if (this != null)
                {
                    CacheReferences();
                    Refresh();
                }
            };
        }
    }
#endif
    #endregion

    #region Public Methods
    /// <summary>
    /// Refresh kích thước để cover parent
    /// </summary>
    public void Refresh()
    {
        CacheReferences();
        
        if (m_rectTransform == null || m_parentRectTransform == null)
        {
            return;
        }
        
        // Set anchor và pivot về top-center để neo trên đỉnh
        m_rectTransform.anchorMin = new Vector2(0.5f, 1f);
        m_rectTransform.anchorMax = new Vector2(0.5f, 1f);
        m_rectTransform.pivot = new Vector2(0.5f, 1f);
        m_rectTransform.anchoredPosition = Vector2.zero;
        
        Vector2 parentSize = m_parentRectTransform.rect.size;
        
        // Lấy kích thước gốc của image
        Vector2 imageSize = GetNativeSize();
        
        if (imageSize.x <= 0 || imageSize.y <= 0 || parentSize.x <= 0 || parentSize.y <= 0)
        {
            return;
        }
        
        // Tính tỷ lệ scale cần thiết để cover parent
        float scaleX = parentSize.x / imageSize.x;
        float scaleY = parentSize.y / imageSize.y;
        
        // Chọn scale lớn hơn để đảm bảo cover (không có khoảng trống)
        float scale = Mathf.Max(scaleX, scaleY);
        
        // Apply kích thước mới
        Vector2 newSize = imageSize * scale;
        
        m_rectTransform.sizeDelta = newSize;
        
        // Cache lại để so sánh
        m_lastParentSize = parentSize;
        CacheLastVisual();
    }

    /// <summary>
    /// Set target image và refresh
    /// </summary>
    public void SetTargetImage(UnityEngine.UI.Image image)
    {
        targetGraphic = image;
        Refresh();
    }

    /// <summary>
    /// Set target raw image và refresh
    /// </summary>
    public void SetTargetRawImage(RawImage rawImage)
    {
        targetGraphic = rawImage;
        Refresh();
    }

    /// <summary>
    /// Set target graphic (Image hoặc RawImage) và refresh
    /// </summary>
    public void SetTargetGraphic(Graphic graphic)
    {
        targetGraphic = graphic;
        Refresh();
    }
    #endregion

    #region Private Methods
    private void CacheReferences()
    {
        if (m_rectTransform == null)
        {
            m_rectTransform = GetComponent<RectTransform>();
        }
        
        if (m_parentRectTransform == null && transform.parent != null)
        {
            m_parentRectTransform = transform.parent as RectTransform;
        }
        
        if (targetGraphic == null)
        {
            targetGraphic = GetComponent<Graphic>();
        }
    }

    private Vector2 GetNativeSize()
    {
        if (targetGraphic is UnityEngine.UI.Image image && image.sprite != null)
        {
            Sprite sprite = image.sprite;
            return new Vector2(sprite.rect.width, sprite.rect.height);
        }

        if (targetGraphic is RawImage rawImage && rawImage.texture != null)
        {
            Texture texture = rawImage.texture;
            return new Vector2(texture.width, texture.height);
        }
        
        // Fallback: dùng kích thước hiện tại của RectTransform
        if (m_rectTransform != null)
        {
            Vector2 currentSize = m_rectTransform.rect.size;
            if (currentSize.x > 0 && currentSize.y > 0)
            {
                return currentSize;
            }
        }
        
        return Vector2.zero;
    }

    private bool HasVisualChanged()
    {
        if (targetGraphic is UnityEngine.UI.Image image)
        {
            if (image.sprite != m_lastSprite)
            {
                m_lastSprite = image.sprite;
                return true;
            }

            return false;
        }

        if (targetGraphic is RawImage rawImage)
        {
            if (rawImage.texture != m_lastTexture)
            {
                m_lastTexture = rawImage.texture;
                return true;
            }

            return false;
        }

        return false;
    }

    private void CacheLastVisual()
    {
        if (targetGraphic is UnityEngine.UI.Image image)
        {
            m_lastSprite = image.sprite;
            m_lastTexture = null;
        }
        else if (targetGraphic is RawImage rawImage)
        {
            m_lastTexture = rawImage.texture;
            m_lastSprite = null;
        }
        else
        {
            m_lastSprite = null;
            m_lastTexture = null;
        }
    }
    #endregion
}

```