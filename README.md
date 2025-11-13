# Examen-II-BienHecho

## Escena 1
```csharp
using UnityEngine;
using System;
public class MoverCubo : MonoBehaviour
{
    public delegate void GetReward();
    public delegate void GetSpecial();
    public event GetReward OnOneHundred;
	public Shield Special;
	public event GetSpecial SpecialShield;
    public int score;
	public float speed = 5f;
    public bool alcanzado = false;
	Rigidbody rb;
	void Awake()
	{
		rb = GetComponent<Rigidbody>();
		rb.freezeRotation = true;
	}
	void FixedUpdate()
	{
		float h = Input.GetAxis("Horizontal");
		float v = Input.GetAxis("Vertical");
		Vector3 move = new Vector3(h, 0f, v) * speed;
		Vector3 vel = new Vector3(move.x, rb.linearVelocity.y, move.z);
		rb.linearVelocity = vel;
	}
    private void OnCollisionEnter(Collision other)
    {
		Debug.Log($"[MoverCubo:{name}] OnCollisionEnter with {other.gameObject.name} tag={other.gameObject.tag} scoreBefore={score}");
		if (other.gameObject.CompareTag("shield1"))
		{
			if (other.gameObject.name == Special.name)
			{
				SpecialShield();
			}
			int reward = alcanzado ? 20 : 10;
			score += reward;
			Debug.Log($"[MoverCubo:{name}] Collected shield1 - reward={reward} scoreAfter={score}");
			other.gameObject.SetActive(false);
		}
		else if (other.gameObject.CompareTag("shield2"))
		{
			score += 20;
			Debug.Log($"[MoverCubo:{name}] Collected shield2 - reward=20 scoreAfter={score}");
			other.gameObject.SetActive(false);
		}
		if(score >= 100 && !alcanzado) {
			alcanzado = true;
			Debug.Log($"[MoverCubo:{name}] Score reached {score} - invoking accionRecompensa with {alcanzado}");
			OnOneHundred();
		}
	}
}
```

```csharp
using System;
using UnityEngine;

public class Guerrero : MonoBehaviour
{
	public MoverCubo cuboNotifier;
	public Rigidbody rb;
    public string myTag;
	public Shield special;
	public float speed = 10f;
	public bool huir = false;

	private void Start()
	{
		myTag = gameObject.tag;
		Debug.Log($"[My tag is :{myTag}");
		Collider col = GetComponent<Collider>();
		rb = GetComponent<Rigidbody>();
		cuboNotifier.OnOneHundred += AccionRecompensa;
	}
    private void AccionRecompensa()
    {
		Debug.Log($"[entro a  cubo.accionRecompensa");
        if (myTag == "guerrero1")
        {
            huir = true;
        }
    }
	public void FixedUpdate()
	{
		if (huir)
		{
			Vector3 dir = (rb.position - special.transform.position).normalized;
            rb.MovePosition(rb.position + dir * speed * Time.fixedDeltaTime);
		}
	}
}
```

```csharp
using UnityEngine;
using System;

public class Shield : MonoBehaviour
{
    public float speed = 5f;
    public Rigidbody rb;
    public MoverCubo cuboNotifier;
    public Transform shield;
    public Guerrero myGuerrero;
    public bool especial = false;
    public void Start()
    {
        rb = GetComponent<Rigidbody>();
        cuboNotifier.OnOneHundred += AccionRecompensa;
        cuboNotifier.SpecialShield += AccionEspecial;
    }
    void OnDestroy()
    {
        if (cuboNotifier != null)
        {
            Debug.Log($"[Shield:{name}] Unsubscribing from cubo.accionRecompensa");
            cuboNotifier.OnOneHundred -= AccionRecompensa;
        }
    }
    private void AccionEspecial()
    {
        especial = true;
    }
    private void AccionRecompensa()
    {
        if (CompareTag("shield1"))
        {
            transform.localScale *= 2f;
        }
    }
    public void FixedUpdate()
    {
        if (especial) {
        Vector3 dir = (myGuerrero.transform.position - rb.position).normalized;
        rb.MovePosition(rb.position + dir * speed * Time.fixedDeltaTime);
        }
    }
}

```

## Escena 2

```csharp
using UnityEngine;
using System;
public class MoverCubo : MonoBehaviour
{
    public delegate void GetReward();
    public delegate void GetSpecial();
    public event GetReward OnOneHundred;
	public Shield Special;
	public event GetSpecial SpecialShield;
    public int score;
	public float speed = 5f;
    public bool alcanzado = false;
	Rigidbody rb;
	void Awake()
	{
		rb = GetComponent<Rigidbody>();
		rb.freezeRotation = true;
	}
	void FixedUpdate()
	{
		float h = Input.GetAxis("Horizontal");
		float v = Input.GetAxis("Vertical");
		Vector3 move = new Vector3(h, 0f, v) * speed;
		Vector3 vel = new Vector3(move.x, rb.linearVelocity.y, move.z);
		rb.linearVelocity = vel;
	}
    private void OnCollisionEnter(Collision other)
    {
		Debug.Log($"[MoverCubo:{name}] OnCollisionEnter with {other.gameObject.name} tag={other.gameObject.tag} scoreBefore={score}");
		if (other.gameObject.CompareTag("shield1"))
		{
			if (other.gameObject.name == Special.name)
			{
				SpecialShield();
			}
			int reward = alcanzado ? 20 : 10;
			score += reward;
			Debug.Log($"[MoverCubo:{name}] Collected shield1 - reward={reward} scoreAfter={score}");
			other.gameObject.SetActive(false);
		}
		else if (other.gameObject.CompareTag("shield2"))
		{
			score += 20;
			Debug.Log($"[MoverCubo:{name}] Collected shield2 - reward=20 scoreAfter={score}");
			other.gameObject.SetActive(false);
		}
		if(score >= 100 && !alcanzado) {
			alcanzado = true;
			Debug.Log($"[MoverCubo:{name}] Score reached {score} - invoking accionRecompensa with {alcanzado}");
			OnOneHundred();
		}
	}
}
```

```csharp
using System;
using UnityEngine;

public class Guerrero : MonoBehaviour
{
	public MoverCubo cuboNotifier;
	public Rigidbody rb;
    public string myTag;
	public Shield special;
	public float speed = 10f;
	public bool huir = false;

	private void Start()
	{
		myTag = gameObject.tag;
		Debug.Log($"[My tag is :{myTag}");
		Collider col = GetComponent<Collider>();
		rb = GetComponent<Rigidbody>();
		cuboNotifier.OnOneHundred += AccionRecompensa;
	}
    private void AccionRecompensa()
    {
		Debug.Log($"[entro a  cubo.accionRecompensa");
        if (myTag == "guerrero1")
        {
            huir = false;
        }
    }
	public void FixedUpdate()
	{
		if (huir)
		{
			Vector3 dir = (rb.position - special.transform.position).normalized;
            rb.MovePosition(rb.position + dir * speed * Time.fixedDeltaTime);
		}
	}
}
```

```csharp
using UnityEngine;
using System;

public class Shield : MonoBehaviour
{
    public float speed = 5f;
    public Rigidbody rb;
    public MoverCubo cuboNotifier;
    public Transform shield;
    public Guerrero myGuerrero;
    public bool alinearse = false;
    public bool especial = false;
    public float alineacion = 2f;
    public Vector3 posicion ;
    public void Start()
    {
        posicion = myGuerrero.transform.position;
        posicion.x = posicion.x += alineacion;
        posicion.z = posicion.z += 2f;
        Debug.Log($"[Shield: a la posicion {posicion}] ");
        rb = GetComponent<Rigidbody>();
        cuboNotifier.OnOneHundred += AccionRecompensa;
        // cuboNotifier.SpecialShield += AccionEspecial;
    }
    void OnDestroy()
    {
        if (cuboNotifier != null)
        {
            Debug.Log($"[Shield:{name}] Unsubscribing from cubo.accionRecompensa");
            cuboNotifier.OnOneHundred -= AccionRecompensa;
        }
    }
    private void AccionEspecial()
    {
    }
    private void AccionRecompensa()
    {
        if (CompareTag("shield1"))
        {
            Debug.Log($"[Shield:{name}] alieandose");
            alinearse = true;
        }
    }
    public void FixedUpdate()
    {
        if (alinearse) {

            Vector3 dir = (posicion - rb.position).normalized;
            rb.MovePosition(rb.position + dir * speed * Time.fixedDeltaTime);
        }
    }
}


```
