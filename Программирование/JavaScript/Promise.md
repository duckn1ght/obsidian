**Promise** (обещание) - это объект который позволяет удобнее писать асинхронный код. Стандартная структура Promise:
```jsx
let promise = new Promise((resolve, reject) => {
		if(true) resolve() // В случае успеха асинхронной операции, вызвать resolve
		else reject() // В случае ошибки вызвать reject
});

promise
	.then() // resolve
	.catch() // reject
```

**resolve** - функция, которая будет вызываться при успехе операции. Функций resolve может быть несколько
**reject** - функция, которая будет вызвана при ошибке операции. Их также может быть несколько (но зачем?)
С помощью Promise можно удобно решить задачу с созданием директории и файла внутри:
```jsx
const createFileInDirPromise = new Promise((res, rej) => {
    let dirPath = path.join(__dirname, 'dir2') // Путь до директории
    fs.mkdir(dirPath, (err) => { // Создание директории
        if (err) rej(err); // В случае ошибки вызвать reject
        else res(dirPath); // В случае успеха вызывть resolve
    });
});

createFileInDirPromise
		// resolve выполняет создание директории
    .then(dirPath => { fs.writeFile(path.join(dirPath, 'file2.txt'), 
		'text', err => (err) ? console.log(err) : null) }) 
		// reject выводит ошибку в консоль
    .catch(err => console.log(err)); 
```
