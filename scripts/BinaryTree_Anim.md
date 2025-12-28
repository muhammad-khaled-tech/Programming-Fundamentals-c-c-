```javascript
/* Excalidraw Script: Test 
*/
(async () => {
    const ea = ExcalidrawAutomate;
    ea.reset();
    
    // رسم نص بسيط للاختبار
    ea.addText(0, 0, "It Works! 🎉");
    
    // أهم سطر: الإضافة للرؤية
    await ea.addElementsToView(true, true);
})();
```