## Types

uint8	Беззнаковые 8-битные целые числа от 0 до 255
uint16	Беззнаковые 16-битные целые числа 	от 0 до 65535
uint32	Беззнаковые 32-битные целые числа 	от 0 до 4294967295
uint64	Беззнаковые 64-битные целые числа 	от 0 до 18446744073709551615
int8	Знаковые 8-битные целые числа 	от -128 до 127
int16	Знаковые 16-битные целые числа 	от -32768 до 32767
int32	Знаковые 32-битные целые числа 	от -2147483648 до 2147483647
int64	Знаковые 64-битные целые числа 	от -9223372036854775808 до 9223372036854775807

Также существует 3 машинно-зависимых целочисленных типа: uint, int и uintptr. Они машинно-зависимы, потому что их размер зависит от архитектуры используемого компьютера:
- int: представляет целое число со знаком, которое в зависимости от платформы может занимать либо 4 байта, либо 8 байт. То есть соответствовать либо int32, либо int64.
- uint: представляет целое число только без знака, которое, аналогично типу int, в зависимости от платформы может занимать либо 4 байта, либо 8 байт. То есть соответствовать либо uint32, либо uint64.

Когда объявляется переменная, она автоматически содержит значение по умолчанию для своего типа: 0 для int, 0.0 для float, false для bool, пустая строка для string, nil для указателя и т.д.


## String

```go
fmt.Println(    
        // Содержится ли подстрока в строке    
        strings.Contains("test", "es"), 
        // результат: true

        // Кол-во подстрок в строке
        strings.Count("test", "t"),
        // результат: 2

        // Начинается ли строка с префикса       
        strings.HasPrefix("test", "te"), 
        // результат: true

        // Заканчивается ли строка суффиксом
        strings.HasSuffix("test", "st"), 
        // результат: true

        // Возвращает начальный индекс подстроки в строке, а при отсутствии вхождения возвращает -1
        strings.Index("test", "e"), 
        // результат: 1

        // объединяет массив строк через символ
        strings.Join([]string{"hello","world"}, "-"),
        // результат: "hello-world"

        // Повторяет строку n раз подряд
        strings.Repeat("a", 5), 
        // результат: "aaaaa"

        // Функция Replace заменяет любое вхождение old в вашей строке на new
        // Если значение n равно -1, то будут заменены все вхождения.
        // Общий вид: func Replace(s, old, new string, n int) string
        // Пример:
        strings.Replace("blanotblanot", "not", "***", 	-1),
        // результат: "bla***bla***"
 
        // Разбивает строку согласно разделителю
        strings.Split("a-b-c-d-e", "-"), 
        // результат: []string{"a","b","c","d","e"}

        // Возвращает строку c нижним регистром
        strings.ToLower("TEST"), 
        // результат: "test"

        // Возвращает строку c верхним регистром
        strings.ToUpper("test"), 
        // результат: "TEST"

        // Возвращает строку с вырезанным набором
        strings.Trim("tetstet", "te"),
        // результат: s
    )
	
	// функции ниже принимают на вход тип rune


    // проверка символа на цифру
	fmt.Println(unicode.IsDigit('1')) // true
    // проверка символа на букву
	fmt.Println(unicode.IsLetter('a')) // true 
    // проверка символа на нижний регистр
	fmt.Println(unicode.IsLower('A')) // false
    // проверка символа на верхний регистр
	fmt.Println(unicode.IsUpper('A')) // true
    // проверка символа на пробел 
    // пробел это не только ' ', но и:
    //  '\t', '\n', '\v', '\f', '\r' - подробнее читайте в документации
	fmt.Println(unicode.IsSpace('\t')) // true 

    // С помощью функции Is можно проверять на кастомный RangeTable:
    // например, проверка на латиницу:
 	fmt.Println(unicode.Is(unicode.Latin, 'ы')) // false


    // функции преобразований
	fmt.Println(string(unicode.ToLower('F'))) // f
	fmt.Println(string(unicode.ToUpper('f'))) // F
```

### Time

Конвертирование строк в структуру Time

```go
// func Parse(layout, value string) (Time, error)
// парсит дату и время в строковом представлении
firstTime, err := time.Parse("2006/01/02 15-04", "2020/05/15 17-45")
if err != nil {
	panic(err)
}

// LoadLocation находит временную зону в справочнике IANA
// https://www.iana.org/time-zones
loc, err := time.LoadLocation("Asia/Yekaterinburg")
if err != nil {
	panic(err)
}

// func ParseInLocation(layout, value string, loc *Location) (Time, error)
// парсит дату и время в строковом представлении с отдельным указанием временной зоны
secondTime, err := time.ParseInLocation("Jan 2 06 03:04:05pm", "May 15 20 05:45:10pm", loc)
if err != nil {
	panic(err)
}

fmt.Println(firstTime.Format("02-01-2006 15:04:05"))  // 15-05-2020 17:45:00
fmt.Println(secondTime.Format("02-01-2006 15:04:05")) // 15-05-2020 17:45:10
```

## Структуры

### Array

```go
var array [5]int // создаем массив целых чисел
```

### Slice

Объявление слайса 

```go
var slice []int // не задается размер
slice := []int{1,2,3,4,5} // объявили и проинициализировали. slice = [1 2 3 4 5] 
slice := make([]byte, 5) // объявили и проинициализировали. slice = [0 0 0 0 0] 
```

Для добавления элемента в slice используется функция append: 

```go
package main

import "fmt"

func main() {
    slice := []int{1, 2, 3, 4, 5}
    fmt.Println(slice) // вывод: [1 2 3 4 5]
    slice = append(slice, 6)
    fmt.Println(slice) // вывод: [1 2 3 4 5 6]
	
	slice = make([]int, 5) // будет создан слайс из 5 элементов со значениями по умолчанию 0 для int-а
    fmt.Println(slice) // вывод: [0 0 0 0 0]
}
```

Считывание целой строки с пробелами и перевод ее в массив рун

```go
	var s string
	myscanner := bufio.NewScanner(os.Stdin)
	myscanner.Scan()
	s = myscanner.Text()
	
	sr := []rune(s)
```


### Map

Объявление мапы лучше делать так, что бы на nil не полуяить ссылку

```go
// с помощью встроенной функции make:
m1 := make(map[int]int)

// с помощью использования литерала отображения:
m2 := map[int]int{
    // Пары ключ:значение указываются при необходимости
    12: 2,
    1:  5,
}
```

```go
// добавление с помощью встроенной функции make:
m := make(map[int]int)
m[12] = 3

// Удаление осуществляется с помощью встроенной функции delete:
m := map[int]int{
	12: 2,
	1:  5,
}

delete(m, 12) // Удаление элемента по ключу 12
fmt.Println(m) // map[1:5]

// проверка на существование ключа

m := map[int]int{
	1: 10,
}

if value, inMap := m[1]; inMap {
	fmt.Println(value) // 10
}

if value, inMap := m[2]; inMap {
	fmt.Println(value) // Условие не выполняется
}

// перебор

for key, value := range mapName {
    fmt.Println(key, value)
}
```

## Файлы и директории

###  io/ioutil

```go
dataForFile := []byte("Тестовая строка, предназначенная для записи в файл")

// Создаем новый файл и записываем в него данные dataForFile
if err := ioutil.WriteFile("test.txt", dataForFile, 0600); err != nil {
	...
}

// Читаем данные из того же файла
dataFromFile, err := ioutil.ReadFile("test.txt")
if err != nil {
	...
}

// Сравниваем исходные данные с записанными в файл и прочитанными из него
fmt.Printf("dataForFile == dataFromFile: %v\n", bytes.Equal(dataFromFile, dataForFile))

// Изучаем содержимое директории
filesFromDir, err := ioutil.ReadDir(".")
if err != nil {
	...
}

for _, file := range filesFromDir {
	// Проходим по всем найденным файлам и печатаем их имя и размер
	fmt.Printf("name: %s, size: %d\n", file.Name(), file.Size())
}

// Output:
// dataForFile == dataFromFile: true
// name: main.go, size: 727
// name: test.txt, size: 93
```

### os

Переименование и удаление файлов делается через функции Rename и Remove:

```go
// создаем файл
os.Create("text.txt")
// переименовываем файл
os.Rename("text.txt", "new_text.txt")
// удаляем файл
os.Remove("new_text.txt")
// кстати, os позволяет работать не только с файлами
// выходим из программы:
os.Exit(0)
```

Так же мы можем получать информацию файлов и сравнивать их:

```go
file1, _ := os.Create("text.txt")
file2, _ := os.Create("text.txt")
info1, _ := file1.Stat() // функция Stat возвращает информацию о файле и ошибку
info2, _ := file2.Stat()
fmt.Println(os.SameFile(info1, info2)) // true

// вот что мы можем получить из FileInfo:
// A FileInfo describes a file and is returned by Stat and Lstat.
type FileInfo interface {
	Name() string       // base name of the file
	Size() int64        // length in bytes for regular files; system-dependent for others
	Mode() FileMode     // file mode bits
	ModTime() time.Time // modification time
	IsDir() bool        // abbreviation for Mode().IsDir()
	Sys() interface{}   // underlying data source (can return nil)
}
```

### bufio

bufio.Reader и примеры работы:

```go
file, err := os.Open("test.txt")
if err != nil {
	...
}
defer file.Close()

rd := bufio.NewReader(file)

buf := make([]byte, 10)
n, err := rd.Read(buf) // читаем в buf 10 байт из ранее открытого файла
if err != nil && err != io.EOF {
	// io.EOF не совсем ошибка - это состояние, указывающее, что файл прочитан до конца
	...
}
fmt.Printf("прочитано %d байт: %s\n", n, buf) // прочитано 10 байт: bufio ...

s, err := rd.ReadString('\n') // читаем данные до разрыва абзаца ('\n')
fmt.Printf("%s\n", s)         // ... здесь будет строка
```

bufio.Writer создан для записи в объекты, удовлетворяющие интерфейсу io.Writer, но предоставляет ряд более высокоуровневых методов, в частности метод WriteString(s string):

```go
file, err := os.Create("test.txt")
if err != nil {
	...
}
defer file.Close()

w := bufio.NewWriter(file)
n, err := w.WriteString("Запишем строку")
if err != nil {
	...
}
fmt.Printf("Записано %d байт\n", n) // Записано 27 байт

// bufio.Writer имеет собственный буфер, чтобы быть уверенным, что данные точно записаны,
// вызываем метод Flush()
w.Flush()
```

bufio.Scanner создан для построчного чтения данных. Создается он функцией NewScanner(r io.Reader), посмотрим, как работает этот тип:

```go
file, err := os.Open("test.txt")
if err != nil {
	panic(err)
}
defer file.Close()

s := bufio.NewScanner(file)

// Я заранее записал в файл 5 цифр, каждую на новой строке
for s.Scan() { // возвращает true, пока файл не будет прочитан до конца
	fmt.Printf("%s\n", s.Text()) // s.Text() содержит данные, считанные на данной итерации
}

// 1
// 2
// 3
// 4
// 5
```

### path и path/filepath

```go
package main

import (
	"fmt"
	"os"
	"path/filepath"
)

func walkFunc(path string, info os.FileInfo, err error) error {
	if err != nil {
		return err // Если по какой-то причине мы получили ошибку, проигнорируем эту итерацию
	}

	if info.IsDir() {
		return nil // Проигнорируем директории
	}

	fmt.Printf("Name: %s\tSize: %d byte\tPath: %s\n", info.Name(), info.Size(), path)
	return nil
}

func main() {
	const root = "./test" // Файлы моей программы находятся в другой директории

	if err := filepath.Walk(root, walkFunc); err != nil {
		fmt.Printf("Какая-то ошибка: %v\n", err)
	}

	// Name: file1     Size: 6 byte    Path: test/dir1/file1
	// Name: file2     Size: 6 byte    Path: test/dir1/file2
	// Name: file3     Size: 6 byte    Path: test/dir2/file3
	// Name: file4     Size: 6 byte    Path: test/dir3/file4
	// Name: file5     Size: 6 byte    Path: test/dir3/file5
	// Name: file6     Size: 6 byte    Path: test/dir3/file6
}
```

## Json

### Кодирование

JSON в стандартной библиотеке Go реализовано в пакете encoding/json.

```go

type myStruct struct {
	Name   string
	Age    int
	Status bool
	Values []int
}

s := myStruct{
	Name:   "John Connor",
	Age:    35,
	Status: true,
	Values: []int{15, 11, 37},
}

// Функция Marshal принимает аргумент типа interface{} (в нашем случае это структура)
// и возвращает байтовый срез с данными, кодированными в формат JSON.
data, err := json.Marshal(s)
if err != nil {
	fmt.Println(err)
	return
}

fmt.Printf("%s", data) // {"Name":"John Connor","Age":35,"Status":true,"Values":[15,11,37]}

data, err := json.MarshalIndent(s, "", "\t")
if err != nil {
	fmt.Println(err)
	return
}

fmt.Printf("%s", data)

//{
//	"Name": "John Connor",
//	"Age": 35,
//	"Status": true,
//	"Values": [
//		15,
//		11,
//		37
//	]
//}


```

### Декодирование

```go
data := []byte(`{"Name":"John Connor","Age":35,"Status":true,"Values":[15,11,37]}`)

type myStruct struct {
	Name   string
	Age    int
	Status bool
	Values []int
}

var s myStruct

if err := json.Unmarshal(data, &s); err != nil {
	fmt.Println(err)
	return
}

fmt.Printf("%v", s) // {John Connor 35 true [15 11 37]}
```

### Проверка json на правильность

Мы можем проверить является ли срез байтов форматом json:
 
```go
type user struct {
	Name     string
	Email    string
	Status   bool
	Language []byte
}

m := user{Name: "John Connor", Email: "test email"}

data, _ := json.Marshal(m)

data = bytes.Trim(data, "{") // испортим json удалив '{'

// функция json.Valid возвращает bool, true - если json правильный
if !json.Valid(data) {
	fmt.Println("invalid json!") // вывод: invalid json!
}

fmt.Printf("%s", data) // вывод: "Name":"John Connor","Email":"test email","Status":false,"Language":null}
```

### Аннотирование структур

Для этого мы можем использовать специальные аннотации - тэги для полей нашей структуры.

Продемонстрировать используемые теги проще всего на примере:

```go
// в общем виде аннотация выглядит так: `json:"используемое_имя,опция,опция"`

type myStruct struct {
	// при кодировании / декодировании будет использовано имя name, а не Name
	Name string `json:"name"`
	
	// при кодировании / декодировании будет использовано то же имя (Age),
	// но если значение поля равно 0 (пустое значение: false, nil, пустой слайс и пр.),
	// то при кодировании оно будет опущено
	Age int `json:",omitempty"`
	
	// при кодировании / декодировании поле всегда игнорируется
	Status bool `json:"-"`
}

m := myStruct{Name: "John Connor", Age: 0, Status: true}

data, err := json.Marshal(m)
if err != nil {
	fmt.Println(err)
	return
}
```



