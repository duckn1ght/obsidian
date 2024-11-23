Для более удобной работы с базой данных можно использовать ORM систему, которая позволяет использовать команды на ЯП, вместо SQL-запросов для взаимодействия с БД.

Одной из популярных ORM является Sequalize. Для начала работы с ней, необходимо установить ее пакет:
```jsx
npm i sequalize
```

Т.к. мы будем использовать sequalize с mysql, то нам понадобится пакет mysql2:
```jsx
npm i mysql2
```

Для взаимодействия с БД, необходимо подключиться к ней. Создадим папку db, а в ней db.js:
```jsx
const Sequalize = require('sequelize');

const sqlize = new Sequalize('[Название БД]', '[Логин]', '[Пароль]', {
    dialect: "[тип БД (mysql)]",
    host: '[ip адрес]',
    port: '[порт]',
    logging: false // нужно ли выводить SQL запросы в консоль?
})

sqlize.authenticate()
    .then(() => {
        console.log('Успешно подключено к базе данных');
    })
    .catch(err => {
        console.error('Ошибка подключения к базе данных:', err);
    });

module.exports = {
    sqlize: sqlize,
}
```
## Модель
Для того, чтобы хранить данные, нужно создать модель данных. По сути, модель данных является таблицей в БД. В папке db создадим файл Users.js:
```jsx
// Импортируем sequelize для указания типов данных
const Sequelize = require('sequelize'); 

// Экспортируем функцию для определения модели. Первый аргумент это название таблицы
module.exports = db => db.define('users', {
    name: {
        type: Sequelize.STRING
    },
    email: {
        type: Sequelize.STRING
    },
    password: {
        type: Sequelize.STRING
    }
}, {
    timestamps: false // Отключаем временные метки в таблице
})
```

Теперь в файле db.js:
```jsx
const Sequelize = require('sequelize');

const db = new Sequelize('module_b', 'root', '', {
    dialect: 'mysql',
    host: '127.0.0.1',
    port: '3307',
    logging: false
})

db.authenticate()
    .then(() => {
        console.log('Подключение к БД успешно!');
    })
    .catch(err => {
        console.error('Ошибка подключения к БД:', err);
    })

const Users = require('./Users.js')(db); // Импортируем модель передавая в функцию db

// Синхронизируем таблицы с БД. После этого будут созданы все необходимые таблицы
db.sync().then((err) => { 
    console.log('Синхронизация успешна!');
}, (err) => {
    console.log('Ошибка при синхронизации таблиц: ', err);
});

// Экспортируем Users для последующих взаимодействий
module.exports = {
    db: db,
    users: Users
}
```
## Запросы

Для добавления запросов к Users создадим файл Controller.js в /db/Users:
```jsx
const db = require('../db.js');

async function getUsers() {
    try {
        return await db.users.findAll();
    } catch (err) {
        console.error('Ошибка при получении пользователей: ', err);
        throw err;
    }
}

async function getUserById(userId) {
    try {
        const user = await db.users.findOne({ where: { id: userId } });
        if (!user) {
            console.error('Пользователь не найден');
            return;
        }
        return user;
    } catch (err) {
        console.error('Ошибка поиска пользователя: ', err);
        throw err;
    }
}

async function createUser(userData) {
    try {
        return await db.users.create(userData);
    } catch (err) {
        console.error('Ошибка при создании пользователя: ', err);
        throw err;
    }
}

async function updateUserById(newData, userId) {
    try {
        const user = await getUserById(userId);
        if (!user) {
            console.error('Пользователь не найден');
            return;
        }
        await db.users.update(newData, { where: { id: userId } });
        return await getUserById(userId);
    } catch (err) {
        console.error('Ошибка обновления пользователя: ', err);
        throw err;
    }
}

async function deleteUserById(userId) {
    try {
        const deletedRows = await db.users.destroy({ where: { id: userId } });
        if (deletedRows === 0) throw new Error('Пользователь не найден');
    } catch (err) {
        console.error('Ошибка удаления пользователя: ', err);
        throw err;
    }
}

module.exports = {
    getUsers,
    createUser,
    getUserById,
    updateUserById,
    deleteUserById
};

```
Очень важно использовать тут экземпляр db и ссылаться на users через него, иначе ни черта работать не будет. Также лучше использовать trycatch чтобы проще выводить ошибки.

Для использования данных функций, необходимо создать для них пути. Создадим файл routes.js:
```jsx
const UsersController = require('./db/Users/Controller');

module.exports = function routes(app) {
    app.get('/users', async (req, res) => {
        const users = await UsersController.getUsers();
        res.send(users);
    });

    app.post('/users/create', async (req, res) => {
        const newUser = await UsersController.createUser(req.body);
        res.send(newUser);
    });

    app.get('/users/:id', async (req, res) => {
        const findUser = await UsersController.getUserById(req.params.id);
        res.send(findUser);
    });

    app.get('/users/delete/:id', async (req, res) => {
        const deletedUser = await UsersController.deleteUserById(req.params.id);
        res.sendStatus(200);
    });

    app.post('/users/:id', async (req, res) => {
        const updatedUser = await UsersController.updateUserById(req.body, req.params.id);
        res.send(updatedUser);
    });
}
```

