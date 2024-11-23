## Содержание:
[[$ ExpressJS]]
[[$ NestJS]]
[[Crypto]]
[[ejs]]

---
## Введение
Чтобы инициализировать проект на nodejs необходимо ввести команду
```bash
npm init
```
После этого в папке с проектом появиться файл **package.json**, который позволит устанавливать пакеты в проект.
## Path
Для работы с путями в проекте, используется объект path из стандартной библиотеки. Пример с использованием методом **join:**
```jsx
const path = require('path');

let newPath = path.join('user', 'todos', 'todo:1');

console.log(newPath);
// Вывод: user\\todos\\todo:1
```
Метод join формирует путь из переданных аргументов. Использование это метода обуславливается тем, что в Windows и Linux пути имеют разный формат.
Существуют две стандартные переменные: __dirname и __filename, которые выводят абсолютный путь до директории и файла соответственно. Для того чтобы указать путь на директорию ниже, можно использовать аргумент ‘..’. Пример:

```jsx
const path = require('path');

// Создаем переменную для вывода пути до директории
let newPath = path.join(__dirname);

console.log(newPath);

// Обновляем переменную, чтобы спуститься на директорию ниже
newPath = path.join(__dirname, '..');

console.log(newPath);

// Вывод:
// C:\\Users\\user\\Documents\\nodejs_projects\\first
// C:\\Users\\user\\Documents\\nodejs_projects
```
Метод parse() позволяет парсить путь:
```jsx
const path = require('path');

// Создаем переменную для вывода пути до директории
let newPath = path.join(__filename);
// Вывод парсинга пути
console.log(path.parse(newPath))

// Вывод:
// {
//     root: 'C:\\\\',
//     dir: 'C:\\\\Users\\\\user\\\\Documents\\\\nodejs_projects\\\\first',
//     base: 'index.js',
//     ext: '.js',
//     name: 'index'
// }
```
## URL
Для работы с url-адрессами используется стандартный класс URL:
```jsx
let urlPath = new URL('<https://localhost:3000/user:2>');

console.log(urlPath);

// Вывод:
// URL {
//     href: '<https://localhost:3000/user:2>',
//     origin: '<https://localhost:3000>',
//     protocol: 'https:',
//     username: '',
//     password: '',
//     host: 'localhost:3000',
//     hostname: 'localhost',
//     port: '3000',
//     pathname: '/user:2',
//     search: '',
//     searchParams: URLSearchParams {},
// }  
```
## Файловая система
Для работы с файловой системой используется библиотека fs:
```jsx
const fs = require('fs');
const path = require('path');
// Создание директорий с помощью асинхронной функции mkdir
fs.mkdir(path.join(__dirname, 'dir1', 'dir2'), // Путь для директорий
{recursive: true}, // Параметр для рекурсивного создания папок
(err) => { // Колбек 
    if(err) {
      console.log(err);
      return;  
    }
    console.log('Папки созданы');
});

```

Предположим, что нам необходимо создать директорию, а внутри нее файл:
```jsx
const fs = require('fs');
const path = require('path');

fs.mkd(path.join(__dirname, 'dir1', 'dir2'), {recursive: true}, (err) => {
    if(err) {
      console.log(err);
      return;  
    }
    console.log('Папки созданы');
});

fs.writeFile(path.join(__dirname, 'dir1', 'dir2', 'text.txt'), 'hello txt', (err) => {
    if (err) {
        console.log(err);
    }
})
```

Данный код выведет ошибку, потому что функция создания папки выполняется асинхронно. Значит, что функция создания файла, выполняется в то же время, когда функция создания папки, то есть тогда, когда папка еще не создана. Для решения этой проблемы необходимо вызвать синхронную функцию для создания папки:
```jsx
const fs = require('fs');
const path = require('path');

fs.mkdirSync(path.join(__dirname, 'dir1', 'dir2'), {recursive: true});

fs.writeFile(path.join(__dirname, 'dir1', 'dir2', 'text.txt'), 'hello txt', (err) => {
    if (err) {
        console.log(err);
    }
})
```
Функция writeFile создает или перезаписывает файл с указанным содержимым. Чтобы добавить запись в конец, используется метод **appendFile()**.