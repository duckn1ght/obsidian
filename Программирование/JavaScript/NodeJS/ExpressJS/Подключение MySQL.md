Для подключение к MySQL, необходим пакет mysql2.
Установим пакет:
```jsx
npm i mysql2
```

Далее создадим файл db.js в котором будет находится код для подключения к БД:
```jsx
const mysql = require('mysql2');

const connection = mysql.createConnection({
    host: '127.0.0.1',
    port:'3307',
    user: 'root',
    password: '',
    database: 'finance_tracker'
})

connection.connect((err) => {
    if (err) {
        console.log(err.stack);
        return;
    }
    console.log('Подключение к БД успешно!');
});

module.exports = connection;
```

В главном файле index.js подключим db.js:
```jsx
const db = require('./db/db.js');
```

После запуска сервера, увидим что подключение к БД успешно.

---
### SQL запросы

Для того, чтобы выполнить SQL-запрос, используется метод query объекта connection:
```jsx
function getAll() {
    db.query('SELECT * FROM users', (err, result) => {
        if (err) {
            console.log(err);
        } else {
            console.log(result);
        }
    })
}

getAll();
```

Данная функция выведет все данные из таблицы ‘users’ в консоль:
```jsx
[
  {
    id: 1,
    username: 'eugen',
    email: 'mail@mail.com',
    password: 'qwerty'
  }
]
```
---
[[Router]] | [[Sequalize]]
