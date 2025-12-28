```javascript
/*
  Excalidraw Script: Binary Tree Construction Animation 
  Author: Prof. Binary (Optimized)
*/

// تعريف الـ API
const ea = ExcalidrawAutomate; 
ea.reset(); // تنظيف أي حاجة قديمة في الذاكرة

// دالة الـ Sleep عشان الأنيميشن يبان
const sleep = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

async function drawTree() {
  
  // 1. Create Root Node
  // بنستخدم addEllipse بدل addElement عشان أضمن وأسهل
  const rootId = ea.addEllipse(400, 100, 80, 80); 
  ea.style.strokeColor = "#000000";
  ea.style.backgroundColor = "#ffc9c9";
  ea.style.fillStyle = "solid";
  
  // لازم نضيف التغييرات دي للرسمة عشان تتلون صح قبل ما نكتب النص
  // (خدعة عشان الستايل يطبق صح)
  const rootEl = ea.getElement(rootId); 
  rootEl.backgroundColor = "#ffc9c9";
  rootEl.fillStyle = "solid";

  ea.addText(425, 125, "Root");
  
  // اظهر اللي رسمناه لحد دلوقتي
  await ea.addElementsToView(true, true);
  await sleep(800);

  // 2. Create Left Child
  const leftId = ea.addEllipse(300, 250, 80, 80);
  // تظبيط الستايل
  const leftEl = ea.getElement(leftId);
  leftEl.backgroundColor = "#a5d8ff";
  leftEl.fillStyle = "solid";
  
  ea.addText(325, 275, "Left");
  
  await ea.addElementsToView(true, true);
  await sleep(800);

  // 3. Draw Pointer (Root -> Left)
  ea.connectObjects(rootId, "bottom", leftId, "top", {
    startArrowHead: null,
    endArrowHead: "triangle" // شكل السهم
  });
  
  await ea.addElementsToView(true, true);
  await sleep(800);

  // 4. Create Right Child
  const rightId = ea.addEllipse(500, 250, 80, 80);
  const rightEl = ea.getElement(rightId);
  rightEl.backgroundColor = "#a5d8ff";
  rightEl.fillStyle = "solid";

  ea.addText(520, 275, "Right");
  
  await ea.addElementsToView(true, true);
  await sleep(800);

  // 5. Draw Pointer (Root -> Right)
  ea.connectObjects(rootId, "bottom", rightId, "top", {
    startArrowHead: null,
    endArrowHead: "triangle"
  });
  
  await ea.addElementsToView(true, true);
  
  new Notice("Tree Built Successfully! 🌳");
}

// تنفيذ الدالة
await drawTree();
```