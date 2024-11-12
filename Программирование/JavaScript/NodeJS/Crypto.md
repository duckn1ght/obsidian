Crypto - стандартная библиотека NodeJS, созданная для шифровая и дешифрования паролей.
```js
const crypto = require('crypto');

// Генерируем соль
function generateSalt() {
    return crypto.randomBytes(16).toString('hex');
}

// Хешируем пароль
function hashPwd(password, salt) {
    return new Promise((resolve, reject) => {
        crypto.pbkdf2(password, salt, 10000, 64, 'sha512', (err, key) => {
            if (err) reject(err);
            resolve(key.toString('hex'));
        });
    })
}

// Проверяем пароли
async function checkPassword(password, hashedPassword, salt) {
    const hashedPasswordCheck = await hashPwd(password, salt);
    return hashedPassword === hashedPasswordCheck;
}

module.exports = {
    generateSalt,
    hashPwd,
    checkPassword
}
```