### Use cached `WaitForSeconds` objects to reduce garbage collection.

```CSharp
private WaitForSeconds waitTime = new WaitForSeconds(1.0f);

IEnumerator MyCoroutine()
{
    yield return waitTime;
    // Do something...
    yield return waitTime;
    // Do something else...
}
```

