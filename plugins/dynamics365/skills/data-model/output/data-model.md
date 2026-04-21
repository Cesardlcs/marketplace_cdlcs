 @startuml BSH_BU_Inheritance
title BSH Contact Center — Business Unit Inheritance

top to bottom direction

skinparam {
  BackgroundColor #FFFFFF
  ArrowColor #2C5F8A
  ArrowThickness 2
  NoteBackgroundColor #FFFDE7
  NoteBorderColor #F9A825
  NoteFontSize 12
  ClassFontSize 13
  ClassBorderColor #888888
  Shadowing false
  RoundCorner 8
  ClassAttributeIconSize 0
}

' ── STEP 0: BU TYPES (top) ────────────────────────────────

class "Global BU\n(e.g. BSH Global)" as GlobalBU #C7D2FE
class "Regional BU\n(e.g. BSH ES, BSH DE …)" as RegionalBU #A5B4FC

GlobalBU <-- RegionalBU : child of

' ── STEP 1: BU SOURCE ─────────────────────────────────────

class "Channel Instance" as CI #E9D5F5 {
  owningbusinessunit = **Regional BU**
  --
  contactpoint: URL / phone / email
}

note right of CI
  The Channel Instance (chat widget,
  phone number, mailbox) determines
  which Regional BU is used.
end note

RegionalBU <.. CI : carries

' ── STEP 2–6: INHERITANCE CHAIN ───────────────────────────

class "Contact" as C #D1ECF1 {
  owningbusinessunit
  --
  Guest      → Regional BU  ← from Channel Instance
  OneAccount → Global BU
}

class "Account" as A #D1ECF1 {
  owningbusinessunit
  --
  Inherited from Contact
}

class "Customer Asset" as CA #D1ECF1 {
  owningbusinessunit
  --
  Inherited from Account
}

class "Functional Location" as FL #D1ECF1 {
  owningbusinessunit
  --
  Inherited from Customer Asset
}

class "Case" as CS #D1ECF1 {
  owningbusinessunit
  --
  Guest (via Contact) → Regional BU
  Guest (via Asset)   → Regional BU
  OneAccount          → Global BU
}

' ── CLEAN VERTICAL CHAIN ──────────────────────────────────

CI -down-> C  : "① BU assigned to Contact"
C  -down-> A  : "② Account inherits from Contact"
A  -down-> CA : "③ Customer Asset inherits from Account"
CA -down-> FL : "④ Functional Location inherits from Asset"
CA -down-> CS : "⑤ Case inherits from Asset"
C  -down-> CS : "   or from Contact"

' ── LEGEND ────────────────────────────────────────────────

legend bottom right
  |= Color |= Meaning |
  |<back:#C7D2FE>   </back>| Global BU |
  |<back:#A5B4FC>   </back>| Regional BU (per country) |
  |<back:#E9D5F5>   </back>| BU source (Channel Instance) |
  |<back:#D1ECF1>   </back>| Entity — owningBU is inherited |
  | ——> solid  | BU assignment / inheritance |
  | ···> dashed | carries / references |
end legend

@enduml