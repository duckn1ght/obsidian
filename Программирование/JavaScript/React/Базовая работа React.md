В папке public есть файл **index.html**, который и передается в браузер. Этот файл **меняет** содержимое, чтобы показать ту или иную страницу. 
Чтобы менять содержимое **файла index.html**, в ***index.js*** (в папке src) находится следующий код:
```jsx
const root = ReactDOM.createRoot(document.getElementById('root'));

root.render(
    <App />
);
```
Первая строчка находит элемент с id ‘root’, создает на его основе корневой элемент и помещает его в переменную.
Вторая строчка передает то, что необходимо отобразить внутри корневого элемента.
В свою очередь, тэг `<App />` является ссылкой на файл с функцией, которая возвращает разметку HTML, которая будет отображаться внутри root файла:
```jsx
function App() {
  return (
    <div>
      <h1>Hello, world!</h1>
    </div>
  );
}

export default App;
```
