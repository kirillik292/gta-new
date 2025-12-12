MiniGTA3D/
│
├── Assets/
│   ├── Scripts/
│   │   ├── Player/
│   │   │   ├── PlayerController.cs
│   │   │   ├── PlayerCamera.cs
│   │   │   └── PlayerGun.cs
│   │   ├── Car/
│   │   │   └── CarController.cs
│   │   ├── Game/
│   │   │   └── GameManager.cs
│   ├── Models/
│   │   ├── SimpleCar.fbx
│   │   ├── Player.fbx
│   │   └── Buildings/
│   ├── Materials/
│   ├── Scenes/
│   │   └── City.unity
│   └── Prefabs/
│       ├── Player.prefab
│       └── Car.prefab
│
├── ProjectSettings/
├── Packages/
└── .gitignore
using UnityEngine;

public class PlayerController : MonoBehaviour
{
    public float speed = 6f;
    public float runSpeed = 10f;
    public float jumpForce = 5f;

    private Rigidbody rb;
    private bool isGrounded;

    void Start()
    {
        rb = GetComponent<Rigidbody>();
    }

    void Update()
    {
        float h = Input.GetAxis("Horizontal");
        float v = Input.GetAxis("Vertical");

        Vector3 dir = new Vector3(h, 0, v).normalized;

        float currentSpeed = Input.GetKey(KeyCode.LeftShift) ? runSpeed : speed;

        if (dir.magnitude > 0)
        {
            Vector3 move = transform.TransformDirection(dir) * currentSpeed;
            rb.MovePosition(rb.position + move * Time.deltaTime);
        }

        if (Input.GetKeyDown(KeyCode.Space) && isGrounded)
        {
            rb.AddForce(Vector3.up * jumpForce, ForceMode.Impulse);
        }
    }

    private void OnCollisionStay()
    {
        isGrounded = true;
    }

    private void OnCollisionExit()
    {
        isGrounded = false;
    }
}
using UnityEngine;

public class PlayerCamera : MonoBehaviour
{
    public Transform target;
    public float distance = 5f;
    public float height = 2f;
    public float sensitivity = 2f;

    private float rotX;
    private float rotY;

    void LateUpdate()
    {
        rotX += Input.GetAxis("Mouse X") * sensitivity;
        rotY -= Input.GetAxis("Mouse Y") * sensitivity;
        rotY = Mathf.Clamp(rotY, -20, 60);

        Quaternion rotation = Quaternion.Euler(rotY, rotX, 0);
        Vector3 position = target.position - rotation * Vector3.forward * distance + Vector3.up * height;

        transform.position = position;
        transform.LookAt(target);
    }
}
using UnityEngine;

public class CarController : MonoBehaviour
{
    public float speed = 1500f;
    public float turnSpeed = 5f;

    public WheelCollider FL;
    public WheelCollider FR;
    public WheelCollider RL;
    public WheelCollider RR;

    void FixedUpdate()
    {
        float h = Input.GetAxis("Horizontal");
        float v = Input.GetAxis("Vertical");

        FL.steerAngle = h * 30f;
        FR.steerAngle = h * 30f;

        RL.motorTorque = v * speed * Time.deltaTime;
        RR.motorTorque = v * speed * Time.deltaTime;
    }
}
using UnityEngine;

public class PlayerGun : MonoBehaviour
{
    public GameObject bulletPrefab;
    public Transform shootPoint;
    public float force = 3000f;

    void Update()
    {
        if (Input.GetMouseButtonDown(0))
        {
            GameObject b = Instantiate(bulletPrefab, shootPoint.position, shootPoint.rotation);
            b.GetComponent<Rigidbody>().AddForce(shootPoint.forward * force);
            Destroy(b, 3);
        }
    }
}
Buildings/
├── CubeHouse.prefab
├── RoadStraight.prefab
├── RoadTurn.prefab
└── Lamp.prefab
