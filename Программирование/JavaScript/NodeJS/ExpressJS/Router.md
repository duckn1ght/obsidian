Для создания маршрутизации в ExpressJS используется объект router.

Создадим файл **router.js:**
```jsx
const express = require("express");
const router = express.Router();

router.get("/", (req, res) => {
    res.send("Hello World");
});

router.get("/test", (req, res) => {
    res.send("Hello Test");
})

module.exports = router;
```
В нем создается объект Router, определяются два маршрута и экспортируется созданный объект

Теперь в файле index.js подключим маршрутизатор:
```jsx
const router = require("./router");
app.use("/", router);
```

