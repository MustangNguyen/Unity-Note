To make a Serialize Field can read but not able to adjust.
### ReadOnlyAttribute.cs

``` CS
using UnityEngine;

public class ReadOnlyAttribute : PropertyAttribute {}

```

### ReadOnlyDrawer.cs
*Need to put into Editor folder.*

``` CS
using UnityEditor;
using UnityEngine;

[CustomPropertyDrawer(typeof(ReadOnlyAttribute))]
public class ReadOnlyDrawer : PropertyDrawer
{
    public override void OnGUI(Rect position, SerializedProperty property, GUIContent label)
    {
        GUI.enabled = false; // Tạm thời disable
        EditorGUI.PropertyField(position, property, label, true);
        GUI.enabled = true;  // Bật lại sau khi vẽ xong
    }

    public override float GetPropertyHeight(SerializedProperty property, GUIContent label)
    {
        return EditorGUI.GetPropertyHeight(property, label, true);
    }
}

```