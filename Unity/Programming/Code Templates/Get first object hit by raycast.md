``` CS
private void Update()

{

	GameObject hitObject = GetFirstObjectHitByRaycast();

	if (hitObject != null)

	{



	}

}



/// <summary>

/// Casts a ray from the main camera through the screen position of the first touch or mouse click,

/// and returns the first GameObject hit by the ray, or null if nothing is hit.

/// </summary>

/// <param name="layerMask">Optional: LayerMask to filter which objects can be hit by the raycast.</param>

/// <param name="maxDistance">Optional: Maximum distance for the raycast (default: 100f).</param>

/// <returns>The first GameObject hit by the ray, or null if nothing is hit.</returns>

public GameObject GetFirstObjectHitByRaycast(LayerMask? layerMask = null, float maxDistance = 200f)

{

	Vector3 screenPosition;



	// Handle both touch (mobile) and mouse (PC)

	if (Input.touchCount > 0)

	{

		// Use the first touch

		Touch touch = Input.GetTouch(0);

		if (touch.phase != TouchPhase.Began) return null;

		screenPosition = touch.position;

	}

	else if (Input.GetMouseButtonDown(0))

	{

		screenPosition = Input.mousePosition;

	}

	else

	{

		return null;

	}



	Ray ray = Camera.main.ScreenPointToRay(screenPosition);

	RaycastHit hit;



	bool isHit = layerMask.HasValue

		? Physics.Raycast(ray, out hit, maxDistance, layerMask.Value)

		: Physics.Raycast(ray, out hit, maxDistance);



	if (isHit)

	{

		Debug.Log($"[Raycast] Raycast hit: {hit.collider.gameObject.name}", hit.collider.gameObject);

		return hit.collider.gameObject;

	}



	return null;

}
```