```javascript
// Excalidraw Script
const ea = ExcalidrawAutomate;
ea.reset();

// دالة الانتظار
const sleep = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

async function run() {
    new Notice("🚀 Script Started!"); // رسالة تأكيد إن السكربت بدأ

    // 1. Root
    const rootId = ea.addEllipse(400, 100, 80, 80);
    ea.style.strokeColor = "black";
    ea.style.backgroundColor = "#ffc9c9";
    ea.style.fillStyle = "solid";
    ea.addText(425, 125, "Root");
    await ea.addElementsToView(true, true);
    await sleep(500);

    // 2. Left
    const leftId = ea.addEllipse(300, 250, 80, 80);
    ea.style.backgroundColor = "#a5d8ff";
    ea.addText(325, 275, "Left");
    await ea.addElementsToView(true, true);
    await sleep(500);

    // 3. Connect Root -> Left
    ea.connectObjects(rootId, "bottom", leftId, "top", { endArrowHead: "triangle" });
    await ea.addElementsToView(true, true);
    
    new Notice("✅ Done!");
}

await run();
```