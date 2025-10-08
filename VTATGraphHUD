using UnityEngine;
using System.Collections.Generic;

public class VTATGraphHUD : MonoBehaviour
{
    [Header("Refs")]
    public CarController2D car;
    public LineRenderer vLine;   // กราฟ v–t
    public LineRenderer aLine;   // กราฟ a–t (อยู่ครึ่งล่าง)

    [Header("Frame / Box")]
    public SpriteRenderer frame;        // สี่เหลี่ยมกรอบพื้นหลัง (ไม่บังคับ)
    public Vector2 boxSize = new Vector2(8f, 4f);  // กว้าง×สูง (หน่วยโลก)
    public float innerPadding = 0.2f;             // ระยะขอบในกล่อง

    [Header("Ranges")]
    public float timeWindow = 10f;      // ย้อนหลังกี่วินาที
    public Vector2 vRange = new Vector2(0f, 20f); // y-min/max ของ v
    public Vector2 aRange = new Vector2(-5f, 5f); // y-min/max ของ a

    [Header("Anchor (ติดมุมจอ)")]
    public bool anchorToViewport = true;
    [Range(0f,1f)] public float anchorX = 0.85f;  // 0=ซ้าย, 1=ขวา
    [Range(0f,1f)] public float anchorY = 0.85f;  // 0=ล่าง, 1=บน
    public Vector2 worldPadding = new Vector2(0.2f, 0.2f); // เผื่อจากมุมจอ

    readonly List<Vector2> vPts = new List<Vector2>();
    readonly List<Vector2> aPts = new List<Vector2>();

    void Awake()
    {
        SetupLR(vLine);
        SetupLR(aLine);
    }

    void SetupLR(LineRenderer lr)
    {
        if (!lr) return;
        lr.positionCount = 0;
        lr.widthMultiplier = 0.05f;
        if (!lr.sharedMaterial) lr.sharedMaterial = new Material(Shader.Find("Sprites/Default"));
        lr.useWorldSpace = true;
        lr.numCornerVertices = 2;
        lr.numCapVertices = 2;
    }

    void LateUpdate()
    {
        if (!car) return;

        // 1) หาตำแหน่ง origin (มุมซ้ายล่างของกล่อง)
        Vector3 origin;
        if (anchorToViewport && Camera.main)
        {
            // ตำแหน่งมุมขวาบนของกล่องตาม anchor แล้วถอยลง-ซ้ายด้วยขนาดกล่องครึ่งหนึ่ง
            var topRight = Camera.main.ViewportToWorldPoint(new Vector3(anchorX, anchorY, 0f));
            topRight.z = 0f;
            origin = topRight - new Vector3(boxSize.x, boxSize.y, 0f) + new Vector3(worldPadding.x, worldPadding.y, 0f);
        }
        else
        {
            origin = transform.position; // ใช้ตำแหน่งวัตถุเป็น origin
        }

        // 2) อัปเดตกึ่งกล่อง (v ครึ่งบน, a ครึ่งล่าง)
        float halfH = boxSize.y * 0.5f;
        Rect vRect = new Rect(origin.x + innerPadding, origin.y + halfH + innerPadding,
                              boxSize.x - innerPadding*2, halfH - innerPadding*2);
        Rect aRect = new Rect(origin.x + innerPadding, origin.y + innerPadding,
                              boxSize.x - innerPadding*2, halfH - innerPadding*2);

        // 3) เก็บจุดใหม่
        float t = car.elapsedTime;
        vPts.Add(new Vector2(t, car.velocity));
        aPts.Add(new Vector2(t, car.acceleration));

        // ตัดข้อมูลเก่า
        float t0 = t - timeWindow;
        vPts.RemoveAll(p => p.x < t0);
        aPts.RemoveAll(p => p.x < t0);

        // 4) ลงเส้น v–t
        if (vLine)
        {
            vLine.positionCount = vPts.Count;
            for (int i = 0; i < vPts.Count; i++)
            {
                float x01 = Mathf.InverseLerp(t0, t0 + timeWindow, vPts[i].x);
                float y01 = Mathf.InverseLerp(vRange.x, vRange.y, vPts[i].y);
                Vector3 p = new Vector3(
                    Mathf.Lerp(vRect.xMin, vRect.xMax, x01),
                    Mathf.Lerp(vRect.yMin, vRect.yMax, Mathf.Clamp01(y01)),
                    0f);
                vLine.SetPosition(i, p);
            }
        }

        // 5) ลงเส้น a–t
        if (aLine)
        {
            aLine.positionCount = aPts.Count;
            for (int i = 0; i < aPts.Count; i++)
            {
                float x01 = Mathf.InverseLerp(t0, t0 + timeWindow, aPts[i].x);
                float y01 = Mathf.InverseLerp(aRange.x, aRange.y, aPts[i].y);
                Vector3 p = new Vector3(
                    Mathf.Lerp(aRect.xMin, aRect.xMax, x01),
                    Mathf.Lerp(aRect.yMin, aRect.yMax, Mathf.Clamp01(y01)),
                    0f);
                aLine.SetPosition(i, p);
            }
        }

        // 6) ปรับกรอบพื้นหลัง (ถ้ามี)
        if (frame)
        {
            frame.transform.position = origin + new Vector3(boxSize.x * 0.5f, boxSize.y * 0.5f, 0f);
            frame.transform.localScale = new Vector3(boxSize.x, boxSize.y, 1f);
        }
    }
}

