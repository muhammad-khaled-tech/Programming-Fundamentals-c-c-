## [Day 7 - Part 1] Storage Classes, Unions & Advanced Types


---

## 1. 👁️ Scopes & The extern Keyword



قبل ما ندخل في العمق، لازم نفرق بين "مين شايف مين".

- **Block Scope:** أي حاجة جوه `{ }` (سواء فانكشن، if، for) بتموت بمجرد القوس ما يتقفل
    
- **Global Variable:** "ملطش للكل". بيتشاف في الفايل كله.
    

### The Multi-File Dilemma

لو قسمت الكود لـ `main.c` و `print.c`:

- **المشكلة:** لو عرفت `int x = 10` في `main.c` وحاولت تستخدمه في `print.c`، الكومبايلر هيضرب (Undefined Reference). ليه؟ لأن الـ Global Scope افتراضياً هو **File Scope** `[Source 2, 77]`.
    
- **الحل (extern):** لازم تروح لـ `print.c` وتقوله "يا عم الكومبايلر، فيه متغير اسمه `x` نوعه `int` موجود في مكان ما، متعملش واحد جديد، دور عليه".
    
    - `extern int x;` -> **Declaration Only** (متحجزوش مكان في الميموري، ده مجرد إخطار).
        

> [!danger] تحذير
> 
> ممنوع تعمل Initialization مع الـ extern (extern int x = 5 -> مصيبة/Compiler Error) [Source 3, 78].

Code snippet

```
graph TD
    subgraph "main.c"
    A["int x = 10; <br/> Definition & Initialization"]
    end

    subgraph "print.c"
    B["extern int x; <br/> Declaration only"]
    C["printf(x); <br/> Linker finds x in main.c"]
    end

    B -.->|Links to| A
```

---

## 2. 🦎 The static Keyword (The Chameleon)

`[Source 3, 4, 80, 81]`

كلمة `static` في لغة C ليها 3 معاني مختلفة تماماً حسب مكانها. دي "الحرباء" بتاعة اللغة.

### A. Static Global / Static Function (Private Access)

- **المكان:** قبل Global Variable أو Function.
    
- **التأثير:** بيخلي المتغير/الفانكشن **Private** للفايل ده بس.
    
- **الهدف:** الـ Encapsulation. عشان محدش من بره يعمل `extern` ويشوف المتغير بتاعك ويبوظه. لو حاولت تعمل extern لمتغير static، اللينكر هيقولك "مش شايفه" `[Source 4]`.
    

### B. Static Local (The Immortal Local)

- **المكان:** جوه الفانكشن (`static int count = 0;`).
    
- **التأثير:**
    
    - **Lifetime:** بيفضل عايش طول مدة البرنامج (بيتنقل من الـ **Stack** للـ **Data Segment**).
        
    - **Scope:** لسه Local (محدش يقدر يوصله غير الفانكشن دي).
        
- **Initialization:** بيحصل مرة واحدة فقط عند أول استدعاء. في المرات الجاية، بيفتكر قيمته القديمة `[Source 4, 12, 13]`.
    

**Code Reconstruction: Static Local Logic**

C

```
void counter() {
    static int cnt = 0; // Initialized ONCE
    cnt++;
    printf("%d ", cnt);
}

int main() {
    counter(); // Output: 1
    counter(); // Output: 2 (Remembers old value)
    counter(); // Output: 3
    // printf("%d", cnt); // ERROR: Scope is still local
}
```

---

## 3. 🛡️ Header Guards (#ifndef)

`[Source 6, 7, 86-89]`

لما المشروع يكبر، ممكن تعمل `#include "file.h"` مرتين بالغلط (مرة في main ومرة جوه file2.h). ده بيعمل **Redefinition Error**.

**الحل:** الـ Header Guards. باترن ثابت لازم تحفظه.

C

```
#ifndef PRINT_H_  // لو الكلمة دي مش متعرفة
#define PRINT_H_  // عرفها، ونفذ اللي تحت

void printFunction();
struct Data { int x; };

#endif // نهاية الشرط
```

- **أول مرة:** `PRINT_H_` مش متعرفة -> يدخل يعرفها وينسخ الكود.
    
- **تاني مرة:** `PRINT_H_` متعرفة -> الـ Preprocessor يشيل الكود كله ويسيب مكانه فراغ `[Source 7]`.
    

---

## 4. ⚡ Advanced Storage Classes (register & volatile)

`[Source 14-18]`

### A. register (Speed Request)

بتطلب من الكومبايلر يحط المتغير ده في **CPU Register** بدل الـ **RAM**.

- **الاستخدام:** لو عندك عداد Loop بيلف مليون مرة وعايز سرعة رهيبة.
    
- **تحذير:** ده مجرد "طلب". لو مفيش مكان في الريجسترات، الكومبايلر هيطنشه ويرميه في الرام `[Source 14]`.
    

### B. volatile (The Anti-Optimization)

دي أهم Keyword للـ Embedded Systems.

- **المشكلة:** الكومبايلر بيعمل Optimization. لو لقى كود زي:
    
    C
    
    ```
    int x = 0;
    while(x == 0); // Infinite Loop?
    ```
    
    الكومبايلر هيقول: "محدش بيغير x جوه اللوب، يبقى اللوب دي لا نهائية، أنا مش هروح أقرأ x من الميموري كل شوية، أنا هعتبرها بـ 0 وأخلص".
    
- **السيناريو:** في الـ Embedded، المتغير `x` ممكن يتغير من بره (عن طريق **ISR** أو هاردوير) `[Source 15-18]`.
    
- **الحل:** `volatile int x;` -> بتقول للكومبايلر: "يا محمد هنيدي (على رأي مينا)، ملكش دعوة! المتغير ده ممكن يتغير من ورا ظهرك، روح هاته من الميموري كل مرة غصب عنك" `[Source 16]`.
    

---

## 5. 📛 typedef vs #define (The Type Safety Trap)

`[Source 21-25]`

عشان نكتب كود Portable (يشتغل على أي ميكرو)، بنعرف أنواع زي `uint8` و `uint32`. نستخدم مين؟

- `#define`: مجرد Text Replacement (بحث واستبدال). غبي جداً.
    
- `typedef`: بتعمل اسم جديد لنوع داتا حقيقي. أذكى وآمن.
    

> [!danger] The Pointer Trap (سؤال امتحانات)
> 
> لو عايز تعرف 2 بوينترز في سطر واحد:
> 
> C
> 
> ```
> #define PTR_D char*
> typedef char* PTR_T;
> ```

> PTR_D p1, p2; // Preprocessor expands to: char* p1, p2;
> 
> // Result: p1 is Pointer, p2 is CHAR (Catastrophe!) [Source 25]

> PTR_T p3, p4; // Compiler treats PTR_T as a type.
> 
> // Result: p3 is Pointer, p4 is Pointer (Correct).

---

## 6. 📦 [[Unions]] (Shared Memory Magic)

`[Source 31-38, 159-163]`

الـ Union هو "علبة" بتشيل داتا أنواعها مختلفة، بس في نفس المكان في الميموري.

- **الحجم:** حجم الـ Union = حجم أكبر عنصر جواه (مش المجموع زي الـ Struct) `[Source 32]`.
    
- **الاستخدام:** توفير الميموري، أو التعامل مع الداتا بأشكال مختلفة (Type Punning).
    

### Hack: Endianness Check using Union

[Source 39, 184]

بدل ما تستخدم Pointers عشان تعرف إنت Little Endian ولا Big Endian، استخدم Union.

C

```
typedef union {
    uint32_t val;    // 4 Bytes (e.g., 0x00000001)
    uint8_t bytes[4]; // Array mapping the same 4 bytes
} EndianCheck;

EndianCheck u;
u.val = 1;

// If Little Endian: Memory is [01 00 00 00]
// u.bytes[0] will be 1.
if (u.bytes[0] == 1) printf("Little Endian");
```

---

## 7. 🤏 [[Bit Fields]] (Memory Squeezing)

`[Source 52, 222]`

لو عايز توفر في الميموري لأقصى درجة، وعايز متغير يشيل 3 بت بس (قيم من 0 لـ 7).

- **Syntax:** جوه الـ Struct.
    
    C
    
    ```
    struct Flags {
        uint8_t enable : 1; // Takes 1 bit
        uint8_t mode   : 3; // Takes 3 bits
        // Total is still padded to closest Byte/Word alignment
    };
    ```
    
- **الفائدة:** التعامل مع الـ Hardware Registers اللي بتبقى متقسمة بتات `[Source 53]`.
    
- **العيب:** مقدرش أعمل `&variable` (Address Of) لـ Bit Field، لأن أقل عنوان في الميموري هو Byte كامل `[Source 52]`.
    

---

## 8. 🔢 [[Enum]] (Killing Magic Numbers)

`[Source 56-62, 235]`

بدل ما نكتب 0 و 1 و 2 وننسى دول بتوع إيه، بنستخدم `enum`.

- **الميزة:** بتدي أسماء للأرقام (`GPIO_INPUT`, `GPIO_OUTPUT`).
    
- **Auto-Increment:** لو بدأت بـ `JAN=1`، اللي بعده FEB بيبقى 2 أوتوماتيك `[Source 57]`.
    
- **Type Safety:** في C، الـ Enum هو `int` في الآخر، وممكن ياخد أي رقم (حتى لو مش في الليستة). لكنه بيخلي الكود مقروء (Readable) `[Source 59]`.
    

The Config Function Pattern [Source 62, 63]

بدل ما تبعت 0 أو 1 للفانكشن، ابعت الـ Enum.

C

```
typedef enum { INPUT, OUTPUT } Direction;
void setPinDir(int pin, Direction dir) { ... }

// Call becomes readable:
setPinDir(5, OUTPUT); // Better than setPinDir(5, 1);
```

---

## 9. 🚀 Inline Functions vs Macros

`[Source 68-70]`

عايز سرعة الـ Macros بس أمان الفانكشن؟

- **Macro Function:** `#define SUM(x,y) (x+y)`. سريعة (Text replacement) بس مفهاش Type Checking وممكن تسبب بلاوي في الأولويات.
    
- **Inline Function:** `inline int sum(int x, int y)`.
    
    - بتقول للكومبايلر: "لو سمحت، خد كود الفانكشن ده وحطه مكان الاستدعاء (Copy-Paste) عشان نوفر الـ **Context Switching Overhead**".
        
    - في نفس الوقت، هي فانكشن حقيقية بـ Type Checking `[Source 69]`.
        

---

### 🔗 Connected Concepts

[[File Scope]]

[[Program Scope]]

[[Declaration vs Definition]]

[[Header Guards]]

[[Volatile Keyword]]

[[ISR (Interrupt Service Routine)]]

[[Union]]

[[Type Punning]]

[[Endianness]]

[[Bit Fields]]

[[Inline Function]]

[[Context Switching]]