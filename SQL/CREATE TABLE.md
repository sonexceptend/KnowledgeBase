# Запрос на создание простой таблицы

```
CREATE TABLE book(
    book_id INT PRIMARY KEY AUTO_INCREMENT, -- уникальный ключ, автодополнение на +1
    title VARCHAR(50),-- строка длиной 50 символов
    author VARCHAR(30),-- строка длиной 30 создание
    price DECIMAL(8, 2),-- вещественное число длиной 8 знаков, из них 2 знака после запятой
    amount INT-- целое число
);
```
