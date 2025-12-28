```javascript
// استدعاء الأداة من النافذة الرئيسية لضمان وجودها
const ea = window.ExcalidrawAutomate; 

// لو الأداة مش موجودة، وقف الكود عشان ميعملش خطأ
if (!ea) {
    console.error("ExcalidrawAutomate not found!");
} else {
    ea.reset();

    // دالة الانتظار
    const sleep = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

    // الدالة الرئيسية
    async function run() {
        
        // 1. Root Node
        // بنرسم الدايرة الأول
        const rootId = ea.addEllipse(400, 100, 80, 80);
        ea.style.strokeColor = "black";
        ea.style.backgroundColor = "#ffc9c9";
        ea.style.fillStyle = "solid";
        
        // نظهرها
        await ea.addElementsToView(true, true);
        
        // نكتب جواها (خطوة منفصلة لضمان الظهور)
        ea.addText(425, 125, "Root");
        await ea.addElementsToView(true, true);
        await sleep(500);

        // 2. Left Node
        const leftId = ea.addEllipse(300, 250, 80, 80);
        ea.style.backgroundColor = "#a5d8ff";
        
        await ea.addElementsToView(true, true);
        ea.addText(325, 275, "Left");
        await ea.addElementsToView(true, true);
        await sleep(500);

        // 3. Connect Arrow
        ea.connectObjects(rootId, "bottom", leftId, "top", {
            endArrowHead: "triangle"
        });
        await ea.addElementsToView(true, true);
    }

    // تشغيل
    await run();
}