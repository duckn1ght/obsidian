Для реализации маршрутизации (навигации) в веб-приложении на React, лучше использовать ***React Router***.
1) Установим React Router:
```bash
yarn add react-router-dom
```
2) Создадим 2 компонента: ___HomePage.tsx___ и ___AuthPage.tsx___, и перепишем ___App.tsx____:
``` TypeScript
import { BrowserRouter, Route, Routes } from "react-router-dom";
import AuthPage from "./Pages/AuthPage";
import HomePage from "./Pages/HomePage";

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<AuthPage />} />
        <Route path="/home" element={<HomePage />} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```
Теперь при заходе в приложение, пользователя будет перенаправлять сразу по корневому пути ("/") к компоненту AuthPage