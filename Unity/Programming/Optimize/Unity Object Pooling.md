## Object Pool Optimization with UnityEngine.Pool
### Pool Initialization

- Use `Dictionary<GameObject, ObjectPool<Object>>` for efficient pooling.
- Initialize `ObjectPool` after the manager is loaded.

### Custom Pool Handler
- Create a class inheriting from `INetworkPrefabInstanceHandler`.
- Implement `Instantiate()` and `Destroy()` for managing objects in the pool.

```CSharp
class PooledPrefabInstanceHandler : INetworkPrefabInstanceHandler
{
    GameObject m_Prefab;
    NetworkObjectPool m_Pool;

    public PooledPrefabInstanceHandler(GameObject prefab, NetworkObjectPool pool)
    {
        m_Prefab = prefab;
        m_Pool = pool;
    }

    NetworkObject INetworkPrefabInstanceHandler.Instantiate(ulong ownerClientId, Vector3 position, Quaternion rotation)
    {
        return m_Pool.GetNetworkObject(m_Prefab, position, rotation);
    }

    void INetworkPrefabInstanceHandler.Destroy(NetworkObject networkObject)
    {
        m_Pool.ReturnNetworkObject(networkObject, m_Prefab);
    }
}

