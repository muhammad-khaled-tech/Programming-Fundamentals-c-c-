```javascript
const ea = ExcalidrawAutomate;
ea.reset();

// دالة الانتظار
const sleep = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

async function run() {
    // 1. Root Node
    const rootId = ea.addEllipse(400, 100, 80, 80);
    ea.style.strokeColor = "black";
    ea.style.backgroundColor = "#ffc9c9";
    ea.style.fillStyle = "solid";
    
    // Show Root
    await ea.addElementsToView(true, true);
    ea.addText(425, 125, "Root");
    await ea.addElementsToView(true, true);
    await sleep(500);

    // 2. Left Child
    const leftId = ea.addEllipse(300, 250, 80, 80);
    ea.style.backgroundColor = "#a5d8ff";
    
    // Show Left
    await ea.addElementsToView(true, true);
    ea.addText(325, 275, "Left");
    await ea.addElementsToView(true, true);
    await sleep(500);

    // 3. Draw Arrow
    ea.connectObjects(rootId, "bottom", leftId, "top", {
        endArrowHead: "triangle"
    });
    await ea.addElementsToView(true, true);
}

await run();
```
