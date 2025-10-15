# Unity SerializeField & UnityEvent
## SerializeField Attribute
The `[SerializeField]` attribute allows Unity to serialize private fields, making them visible and editable in the Inspector while maintaining proper encapsulation.

### Basic Usage
```csharp
[SerializeField] private float m_Speed = 5f;
[SerializeField] private GameObject m_Target;
```

### Benefits
- Maintains encapsulation (private fields)
- Inspector visibility
- Serialization for saving/loading
- Better control over public access

### Common Use Cases
```csharp
public class Example : MonoBehaviour
{
    [SerializeField] private float m_Health = 100f;  // Basic type
    [SerializeField] private Vector3 m_SpawnPoint;   // Unity struct
    [SerializeField] private GameObject m_Prefab;    // Unity Object
    [SerializeField] private List<string> m_Tags;    // Collections
}
```

## UnityEvent System
UnityEvents are serializable events that can be configured in the Inspector and connected to methods at runtime.

### Basic UnityEvent
```csharp
[SerializeField] private UnityEvent m_OnGameStart;
[SerializeField] private UnityEvent<int> m_OnScoreChanged;
```

### Common UnityEvent Types
```csharp
using UnityEngine.Events;

public class EventExample : MonoBehaviour
{
    // No parameters
    [SerializeField] private UnityEvent m_BasicEvent;
    
    // Single parameter
    [SerializeField] private UnityEvent<float> m_FloatEvent;
    [SerializeField] private UnityEvent<string> m_StringEvent;
    
    // Multiple parameters (up to 4)
    [SerializeField] private UnityEvent<int, string> m_MultiParamEvent;
}
```

### Invoking Events
```csharp
public class EventManager : MonoBehaviour
{
    [SerializeField] private UnityEvent<int> m_OnScoreChanged;
    
    private void UpdateScore(int _newScore)
    {
        m_OnScoreChanged?.Invoke(_newScore);
    }
}
```

## Best Practices
1. **Naming Convention**
   - Use m_ prefix for private serialized fields
   - PascalCase for public properties

2. **Performance Considerations**
   - UnityEvents are slower than C# events
   - Use C# events for performance-critical code
   - UnityEvents are better for designer-friendly configurations

3. **Inspector Organization**
```csharp
[SerializeField, Header("Core Settings")]
private float m_Speed;

[SerializeField, Tooltip("Maximum health of the player")]
private float m_MaxHealth;

[SerializeField, Range(0f, 1f)]
private float m_Volume;
```

## Common Pitfalls
- Arrays of UnityEvents aren't serialized properly
- Avoid serializing complex custom classes without [Serializable] attribute
- Remember to check for null before invoking events

## Example Implementation
```csharp
using UnityEngine;
using UnityEngine.Events;

public class PlayerController : MonoBehaviour
{
    #region Serialized Fields
    [SerializeField, Header("Movement")]
    private float m_MoveSpeed = 5f;
    
    [SerializeField, Header("Events")]
    private UnityEvent<float> m_OnHealthChanged;
    private UnityEvent m_OnPlayerDeath;
    #endregion
    
    #region Private Fields
    private float m_CurrentHealth;
    #endregion
    
    public void TakeDamage(float _damage)
    {
        m_CurrentHealth -= _damage;
        m_OnHealthChanged?.Invoke(m_CurrentHealth);
        
        if (m_CurrentHealth <= 0)
        {
            m_OnPlayerDeath?.Invoke();
        }
    }
}
```

## Related Unity Documentation
- [SerializeField Documentation](https://docs.unity3d.com/ScriptReference/SerializeField.html)
- [UnityEvent Documentation](https://docs.unity3d.com/ScriptReference/Events.UnityEvent.html)

---
Tags: #unity #csharp #programming #gamedev #serialization #events
