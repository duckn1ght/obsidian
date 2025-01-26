Для подключения Tailwind к React (VIte) необходимо:
1) Установить библиотеки:
```node
yarn add -D tailwindcss postcss autoprefixer
```
2) Инициализировать tailwind в проекте, после чего будет создан файл __tailwind.config.js__:
```js
npx tailwindcss init -p
```
3) Заполнить список файлов в __tailwind.config.js__, который будет использовать tailwind: 
``` JavaScript
/** @type {import('tailwindcss').Config} */

export default {
  content: [
    "./src/**/*.{js,ts,jsx,tsx}", // Это укажет все React файлы в папке src
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```
4) Дополнить __index.css__:
``` CSS
@tailwind base;
@tailwind components;
@tailwind utilities;
```
# Подключение шрифтов в Tailwind
Чтобы использовать кастомные шрифты, достаточно подключить их в index.css и добавить в tailwind.config.js:
1. index.css:
```css
@import url('https://fonts.googleapis.com/css2?family=Nunito+Sans:ital,opsz,wght@0,6..12,200..1000;1,6..12,200..1000&display=swap');

@tailwind base;
@tailwind components;
@tailwind utilities;
```
2. tailwind.confing.js:
```css
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./src/**/*.{js,jsx}"
  ],
  theme: {
    extend: {
      fontFamily: {
      // Указываем название, которое будет носить шрифт и семейство подключенного шрифта
        fs: ['"Nunito Sans"'] // Двойные ковычки нужны для название с пробелами
      }
    },
  },
  plugins: [],
}

```
После этого можно использовать класс
```css
className='font-[название переменной шрифта]
```