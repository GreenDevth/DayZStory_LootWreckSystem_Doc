# DayZStory Loot Wreck System

English / ไทย

A configurable system allowing players to search predefined wreck objects and receive randomized loot with cooldowns, glove injury risk, item state randomization, and category–based loot pools.

ระบบค้นหา (Search) ซากวัตถุ (Wreck) ที่ตั้งค่าได้: สุ่มไอเท็ม, คูลดาวน์, ความเสี่ยงบาดเจ็บมือ, ปรับ Health/Quantity ของไอเท็ม และเลือกหมวดหมู่ของลูท

---

## Features / คุณสมบัติ

- Configurable search duration & success chance / กำหนดเวลาและโอกาสสำเร็จ
- Per-wreck loot categories / หมวดหมู่ลูทต่อจุด
- Global cooldown per wreck position / คูลดาวน์ต่อจุด
- Glove injury & durability system / ระบบบาดเจ็บมือและความทนทานถุงมือ
- Health & quantity randomization for spawned items / สุ่มสภาพ (Health/Quantity)
- Attachment & additional item spawning / สปาวน์อุปกรณ์เสริม
- Clean JSON config (with optional comments for docs) / ไฟล์ตั้งค่า JSON

---

## File Locations (Typical) / ตำแหน่งไฟล์

- Config: `profiles/DayZStory_LootWreckSystem/Settings.json`
- Source (example): `Scripts/4_World/.../ActionSearchForWreck.c`, `PlayerBase.c`
- README: place at mod root / วางไฟล์ README ที่โฟลเดอร์หลักของม็อด

---

## Quick Start / เริ่มใช้งานเร็ว

1. Copy the mod into your server & keys folder.  
2. Start server once to generate `Settings.json` (if not present).  
3. Edit wreck entries + loot pools.  
4. Restart server.  
5. In game: Approach configured wreck, use “Search” action.  

ไทย:

1. วางม็อดในเซิร์ฟเวอร์  
2. รันครั้งแรกให้ไฟล์ Settings.json สร้าง  
3. แก้ไขรายชื่อ Wreck และ Loot  
4. รีสตาร์ท  
5. เข้าเกม กดค้นหา (Search)

---

## Core Config Fields (Settings.json)

| Field | EN Description | TH คำอธิบาย |
|------|----------------|-------------|
| configVersion | Schema version | เวอร์ชันสคีมา |
| SearchDuration | Seconds to perform search | เวลาค้นหา (วินาที) |
| SearchChance | Success chance (0–1 or >1 = percent) | โอกาสสำเร็จ (0–1 หรือ >1 = %) |
| CooldownSeconds | Seconds before same wreck usable | เวลาคูลดาวน์ (วินาที) |
| SearchingSoundSet | Sound set while searching | ชุดเสียงระหว่างค้นหา |
| FailMessage | Message on failure / cooldown | ข้อความเมื่อค้นหาล้มเหลว |
| SuccessMessage | Message on success | ข้อความเมื่อสำเร็จ |
| Health / Quantity | Fixed value if >=0 and range disabled | ค่าคงที่ถ้า >=0 และไม่ใช้ช่วง |
| HealthMin/Max | Bounds when range enabled | ช่วงสุ่ม Health |
| QuantityMin/Max | Bounds when range enabled | ช่วงสุ่ม Quantity |
| UseHealthRange | 1=use min/max | 1=ใช้ช่วง |
| UseQuantityRange | 1=use min/max | 1=ใช้ช่วง |
| HealthIsPercent | Treat values as % of max | ใช้เป็นเปอร์เซ็นต์ |
| QuantityIsPercent | Treat quantity as % | ใช้เป็นเปอร์เซ็นต์ |
| ApplyStateToAttachments | Apply state to attachments | ใช้กับอุปกรณ์แนบ |
| ApplyStateToAdditional | Apply state to extra items | ใช้กับไอเท็มเสริม |
| GloveInjuryChanceNoGloves | Injury chance no gloves | โอกาสบาดเจ็บ (ไม่มีถุงมือ) |
| GloveBreakChance | Instant ruin chance | โอกาสพังทันที |
| GloveDamageFraction | Durability fraction loss | สัดส่วนลดความทนทาน |
| GloveInjuryHealthLoss | Health lost on injury | พลังชีวิตที่เสีย |
| Wreck[] | Wreck definitions array | รายการจุด Wreck |
| Loots[] | Loot pool definitions | การตั้งค่าลูทหมวดต่างๆ |
| m_Type | Optional class/type label | คลาส/ประเภท (ทางเลือก) |
| m_Model | Model/type used for ID & cooldown | โมเดล/ชนิดวัตถุ |
| m_Position | World X,Y,Z for uniqueness | พิกัดในโลก |
| m_LootTypes | Category key (e.g. Weapons) | หมวดหมู่ลูท |

---

## Glove Risk System / ระบบความเสี่ยงถุงมือ

Logic:

1. No gloves: Roll GloveInjuryChanceNoGloves → if success: lose GloveInjuryHealthLoss HP + possible bleeding.
2. With gloves: Roll GloveBreakChance → ruin gloves OR reduce durability by GloveDamageFraction * max (never auto-ruins below 1 HP unless break triggers).

ไทย:

1. ไม่มีถุงมือ: ทอย GloveInjuryChanceNoGloves ถ้าติด ลด HP และสุ่มเลือดออก
2. มีถุงมือ: ทอย GloveBreakChance ถ้าติด พังเลย ไม่ติด ลดทนทานตาม GloveDamageFraction

Disable a part: set chance to 0.

---

## Cooldown / คูลดาวน์

- Triggered after every search (success or fail).
- Key = combination of model + position.
- Remaining time shown (rounded up minutes).
- Set `CooldownSeconds = 0` to disable.

---

## Wreck Entry Example / ตัวอย่าง Wreck

```json
{
  "m_Type": "Land_Wreck_sed01_aban2_white",
  "m_Model": "wreck_sed01_aban2_w",
  "m_Position": [13919.99, 10.28, 8317.87],
  "m_LootTypes": "Tools"
}
```

Tips:

- Keep positions exact (avoid rounding drift).
- Use consistent model names.

---

## Loot Pool Structure / โครงสร้างหมวดลูท

Each category (Weapons, Armour, Misc, Food, Tools, Medic, Ammo) contains items:

```json
{
  "m_ClassName": "MakarovIJ70",
  "m_Magazine": "Mag_IJ70_8Rnd",
  "m_Attachments": ["PistolSuppressor"],
  "m_AdditionalItems": ["Battery9V"],
  "m_Conditions": []
}
```

- Leave empty arrays if unused.
- `m_Conditions` reserved for future filters.

---

## Item State Application / การตั้งค่าสภาพไอเท็ม

Flow:

1. Decide health value (fixed or range).
2. If HealthIsPercent = 1 convert % → absolute.
3. Apply to spawned main item (+ attachments/additional if enabled).
Same for quantity.

---

## Example Minimal Config / ตัวอย่างย่อ

```json
{
  "configVersion": "1.2",
  "SearchDuration": 2.5,
  "SearchChance": 50,
  "CooldownSeconds": 900,
  "SearchingSoundSet": "LootWreck_SoundSet",
  "FailMessage": "Nothing here.",
  "SuccessMessage": "You found loot!",
  "UseHealthRange": 1,
  "HealthMin": 60,
  "HealthMax": 90,
  "HealthIsPercent": 1,
  "UseQuantityRange": 1,
  "QuantityMin": 30,
  "QuantityMax": 80,
  "QuantityIsPercent": 1,
  "GloveInjuryChanceNoGloves": 0.25,
  "GloveBreakChance": 0.05,
  "GloveDamageFraction": 0.30,
  "GloveInjuryHealthLoss": 5,
  "Wreck": [
    {
      "m_Type": "ExampleSedan",
      "m_Model": "wreck_sed02_aban2_",
      "m_Position": [9308.39, 2.96, 13667.82],
      "m_LootTypes": "Weapons"
    }
  ],
  "Loots": [
    {
      "minItems": 1,
      "maxItems": 1,
      "Weapons": [
        {
          "m_ClassName": "SKS",
          "m_Magazine": "Mag_SKS_10Rnd",
          "m_Attachments": [],
          "m_AdditionalItems": ["Ammo_762x39"],
          "m_Conditions": []
        }
      ]
    }
  ]
}
```

Note: SearchChance > 1 treated as % (50 → 50%).

---

## Balancing Tips / เคล็ดลับปรับสมดุล

EN:

- Increase CooldownSeconds for rarer loot.
- Lower SearchChance instead of shrinking item lists to keep variety.
- ApplyStateToAttachments=0 for pristine attachments if you only want main item degraded.

TH:

- เพิ่ม CooldownSeconds ถ้าอยากให้ลูทยาก
- ลด SearchChance เพื่อรักษาความหลากหลาย
- ปิด ApplyStateToAttachments ถ้าอยากให้อุปกรณ์แนบใหม่เสมอ

---

## Troubleshooting / การแก้ปัญหา

| Issue | Cause | Fix |
|-------|-------|-----|
| No Search action | Not in Wreck list / wrong model | Verify m_Model matches world object |
| Always fail | SearchChance too low | Raise to e.g. 0.4 (or 40) |
| Gloves never break | GloveBreakChance=0 or too low | Increase to 0.05–0.1 |
| Items all same health | UseHealthRange=0 or Health >=0 | Set UseHealthRange=1 & Health=-1 |

---

## Future Ideas / แนวคิดเพิ่ม

- Per-category cooldown modifiers
- Time-of-day chance multipliers
- Conditional m_Conditions logic
- Logging analytics

---

## License / ใบอนุญาต

Add license statement here (MIT, GPL, custom). ระบุสัญญาอนุญาตที่นี่

---

## Credits / ผู้พัฒนา

Author / ผู้พัฒนา: (Add names)
Contributions welcome (เปิดรับ Pull Request)

Enjoy scavenging! / ขอให้ค้นหาลูทสนุก
