using UnityEngine;

public class CarController2D : MonoBehaviour
{
    [Header("Kinematics (SI Units)")]
    [Tooltip("ความเร่ง (m/s^2)")]
    public float acceleration = 0f;     // a
    [Tooltip("ความเร็วปัจจุบัน (m/s) - อ่านอย่างเดียว")]
    public float velocity = 0f;         // v (read-only for inspector)
    [Tooltip("ระยะทางรวม (m)")]
    public float distance = 0f;         // s
    [Tooltip("เวลาที่วิ่ง (s)")]
    public float elapsedTime = 0f;      // t

    [Header("Movement")]
    public Vector2 direction = Vector2.right; // ทิศทางวิ่ง (ขวา)
    public float maxSpeed = 0f;               // 0 = ไม่จำกัด
    public float linearDrag = 0.0f;           // ค่าต้านแบบง่าย (0 = ไม่มี)

    [Header("Run State")]
    public bool isRunning = false;

    private Vector3 startPos;

    void Awake()
    {
        direction = direction.normalized;
        startPos = transform.position;
    }

    void FixedUpdate()
{
    if (!isRunning) return;

    float dt = Time.fixedDeltaTime;

    // อัปเดตความเร็ว
    velocity += acceleration * dt;

    // อัปเดตตำแหน่ง
    Vector3 delta = (Vector3)(direction.normalized * velocity * dt);
    transform.position += delta;

    // เก็บสถิติ
    distance += delta.magnitude;
    elapsedTime += dt;
}


    // เรียกจาก Slider หรือปุ่ม
    public void SetAcceleration(float a) => acceleration = a;
    public void StartRun() => isRunning = true;
    public void PauseRun() => isRunning = false;

    public void ResetRun()
    {
        isRunning = false;
        velocity = 0f;
        distance = 0f;
        elapsedTime = 0f;
        transform.position = startPos;
    }
}
