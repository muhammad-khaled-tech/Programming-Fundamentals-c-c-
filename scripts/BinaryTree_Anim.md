```javascript
// Excalidraw Script
// ملاحظة: المتغير ea موجود جاهز مش محتاجين نعرفه

ea.reset();

// 1. ارسم نود للاختبار
ea.addText(0, 0, "اشتغلت يا وحش! 🦁");

// 2. اظهرها على الشاشة (أهم سطر)
await ea.addElementsToView(true, true);

new Notice("Script Finished Success!")
```