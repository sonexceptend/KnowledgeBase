# Выборка данных

Общий шаблон:

```
	SELECT 'столбцы или * для выбора всех столбцов; обязательно'
	FROM 'таблица; обязательно'
	WHERE 'условие/фильтрация, например, city = 'Moscow'; необязательно'
	GROUP BY 'столбец, по которому хотим сгруппировать данные; необязательно'
	HAVING 'условие/фильтрация на уровне сгруппированных данных; необязательно'
	ORDER BY 'столбец, по которому хотим отсортировать вывод; необязательно'
```

Вычислимые поля

```
SELECT title, author, price, amount, 
     price * amount AS total 
FROM book
WHERE (author = 'Булгаков М.А.' OR author = 'Есенин С.А.') AND price > 600
ORDER BY author, amount DESC;
```
	
[link](https://stepik.org/lesson/297509/step/8?unit=279269)

# Операции

Приоритеты операций:

1. круглые скобки
2. умножение  (*),  деление (/)
3. сложение  (+), вычитание (-)
4. операторы сравнения (=, >, <, >=, <=, <>)
5. NOT
6. AND
7. OR

# BETWEEN и IN;

```
SELECT title, amount 
FROM book
WHERE amount BETWEEN 5 AND 14;

SELECT title, price 
FROM book
WHERE author IN ('Булгаков М.А.', 'Достоевский Ф.М.');
```

# DISTINCT

Чтобы отобрать уникальные элементы некоторого столбца используется ключевое слово DISTINCT, которое размещается сразу после SELECT.

```
SELECT DISTINCT author
FROM book;
```
