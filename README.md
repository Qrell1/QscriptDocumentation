# QscriptDocumentation
Это Документация для языка QscriptNt она сильно связана с стандартной библиотекой qsrt

# QSRT

## Установка

```bash
qsr install <proj> qsrt
```

Подключение в коде:
```c
include "\qsrt\qsr.qh"; // В include точка с запятой обязательна!
```

## Инициализация

Перед использованием стандартной библиотеки необходимо инициализировать пул:

```c
// Константы
POOL_1mb     // 1 Мегабайт
POOL_4mb     // 4 Мегабайт
POOL_16mb    // 16 Мегабайт
POOL_64mb    // 64 Мегабайт
POOL_96mb    // 96 Мегабайт

// Инициализация
qsr.init(POOL_96mb)
```

---

## Работа с памятью

### malloc(размер)

Выделяет блок памяти указанного размера в байтах.

```c
int32* ptr = malloc(100)        // выделить 100 байт
int32* array = malloc(4 * 10)   // выделить массив из 10 int32
```

### mfree(указатель)

Освобождает ранее выделенный блок памяти.

```c
int32* data = malloc(64)
mfree(data)
```

### mcopy(источник, назначение, размер)

Копирует указанное количество байт из одного места в другое.

```c
int32* src = malloc(40)
int32* dst = malloc(40)
src[0] = 100
mcopy(src, dst, 40)  // скопировать 40 байт
```

---

## Ввод/вывод

### printq(значение)

Выводит значение в консоль без переноса строки.

```c
printq @string("Hello ")
printq @int32(42)
printq @string("\n")
```

### Console.endl()

Выводит перевод строки.

```c
printq @string("Line 1")
Console.endl()  // переходим на новую строку
printq @string("Line 2")
```

### read_lineq()

Читает строку из консоли (до нажатия Enter). Возвращает указатель на строку. **Строку нужно освободить через mfree()!**

```c
printq(@string("Enter your name: ")
string* name = read_lineq()
printq @string("Hello, " + name + "!\n")
mfree(name)  // не забываем освободить!
```

### Console.getch()

Читает один символ из консоли без ожидания Enter.

```c
printq @string("Press any key to continue...")
Console.getch()
```

---

## Работа со строками

### Операции со строками (ОСТОРОЖНО может утекать память)

```c
string* s1 = "Hello"
string* s2 = "World"
string* result = s1 + " " + s2  // конкатенация через +
printq(result)
mfree(result)
```

### ConvertStringToInt(строка)

Преобразует строку в целое число.

```c
string* numStr = "12345"
int32 value = ConvertStringToInt(numStr)
printq @int32(value)  // 12345
mfree(numStr)
```

### strif(строка1, строка2)

Сравнивает две строки. Возвращает 1 если равны, 0 если нет.

```c
if strif(name, "admin") == 1 {
    printq @string("Welcome, admin!\n")
}
```

### strof(строка, начало, количество)

Извлекает подстроку.

```c
string* text = "Hello World"
string* sub = strof(text, 0, 5)  // "Hello"
printq @string (sub)
mfree(sub)
```

### replace(строка, старая, новая)

Заменяет все вхождения подстроки.

```c
string* text = "Hello World"
string* result = replace(text, "World", "Q")
printq @string (result)  // "Hello Q"
mfree(result)
```

### int_to_str(число)

Преобразует число в строку (внутренняя функция, используется оператором `@int32`).

```c
string* strNum = @int32(42)  // "42"
printq @string (strNum)
mfree(strNum)
```

### strlen(строка)

Возвращает длину строки в символах.

```c
string* s = "Hello"
int32 len = strlen(s)  // 5
```

---

## Массивы

### Одномерные массивы

```c
int32* arr = malloc(4 * 5)  // массив из 5 элементов

arr[0] = 10
arr[1] = 20
arr[2] = 30

for (int32 i = 0; i < 5; i++) {
    arr[i] = i * 10;
}

mfree(arr)
```

### Двумерные массивы

```c
int32 rows = 10
int32 cols = 10
int32* matrix = malloc(4 * rows)

enumerator int32 i, rows {
    matrix[i] = malloc(4 * cols)
    enumerator int32 j, cols {
        matrix[i][j] = i * cols + j
    }
}

// Использование
printq @int32(matrix[5][3])  // 53

// Освобождение
enumerator int32 i, rows {
    mfree(matrix[i])
}
mfree(matrix)
```

---

## Циклы и управление

### iter (фиксированный цикл)

```c
// 10 итераций
iter 10 {
    printq @string("Hello\n")
}
```

### enumerator (цикл с переменной)

```c
enumerator int32 i, 10 {
    printq @string(@int i + " ")
}
// Вывод: 0 1 2 3 4 5 6 7 8 9
```

### while

```c
int32 i = 0;
while (i < 10) {
    printq @string(@int i + " ")
    i = i + 1
}
```

### for

```c
for (int32 i = 0; i < 10; i = i + 1) {
    printq @string(@int i + " ")
}
```

### break / continue

```c
iter 10 {
    if (i == 5) break      // выход из цикла
    if (i % 2 == 0) continue // пропуск чётных
    printq @string(@int i + " ")
}
```

---

## Условные операторы

### if / else

```c
int32 x = 10;

if (x > 5) {
    printq @string("x больше 5\n")
} else if (x == 5) {
    printq @string("x равен 5\n")
} else {
    printq @string("x меньше 5\n")
}
```

### Логические операторы

```c
if (x > 0 && x < 100) {
    printq @string("x в диапазоне (0, 100)\n")
}

if (x == 10 || x == 20) {
    printq @string("x равен 10 или 20\n")
}
```

---

## Работа с файлами

### File.Create(путь)

Создаёт новый файл. Возвращает структуру FileStream.

```c
FileStream* file = File.Create("test.txt")
if (file.handle != 0) {
    printq(@string("Файл создан\n"))
}
```

### File.Open(путь)

Открывает существующий файл для чтения.

```c
FileStream* file = File.Open("data.txt")
```

### File.ReadAll(file)

Читает содержимое файла в память.

```c
FileStream* file = File.Open("test.txt")
if (file.handle != 0) {
    File.ReadAll(file)
}
File.Close(file)
```

### File.Close(file)

Закрывает файл.

```c
File.Close(file)
```

### File.Delete(путь)

Удаляет файл.

```c
File.Delete("temp.txt")
```

### WriteFile(
  хендел файла
  строка,
  количество байт для записи,
  указатель на переммую куда будет записано количество записанных байт,
  0
)
```
FileStream* file = File.Create("test.txt")
if (file.handle != 0) {
    printq @string("Файл создан\n")
    int32 bytes_written
    WriteFile(file.handle, "Hello!", qsr.len_string("Hello!") * typeof.char, &bytes_written, 0)
}
```

---

## Системные функции

### qsr.getTick()

Возвращает количество миллисекунд с момента запуска системы.

```
int32 start = qsr.getTick()
int32 elapsed = qsr.getTick() - start
printq @string("Elapsed: " + @int elapsed + " ms\n"))
```

### qsr.exit(код)

Завершает программу с указанным кодом возврата.

```c
if (error) {
    qsr.exit(1)
}
```

### qsr.PoolPrint()

Выводит отладочную информацию о пуле памяти.

```c
qsr.PoolPrint()
// Вывод:
// PoolPtr = 7450656
// PoolTotalSize = 576
// PoolMaxSize = 16777216
// PoolItemCount = 12
```

---

## Примеры программ

### Пример 1: Калькулятор

```c
include "\qsrt\qsr.qh";

qsr.init(POOL_1mb)

printq @string("Calculator\n")
printq @string("Enter first number: ")
int32 a = ConvertStringToInt(read_lineq())

printq @string("Enter second number: ")
int32 b = ConvertStringToInt(read_lineq())

printq @string("Sum: " + @int (a + b)) Console.endl()
printq @string("Diff: " + @int (a - b)) Console.endl()
printq @string("Mul: " + @int (a * b)) Console.endl()
if (b != 0) {
    printq @string("Div: " + @int (a / b)) Console.endl()
}

Console.getch()
```

### Пример 2: Таблица умножения

```c
include "\qsrt\qsr.qh";

qsr.init(POOL_1mb);

enumerator int32 i, 10 {
    enumerator int32 j, 10 {
        printq @string(@int32 (i * j) + "\t")
    }
    Console.endl();
}

Console.getch();
```

### Пример 3: Приветствие

```c
include "\qsrt\qsr.qh";

qsr.init(POOL_1mb)

printq @string("What is your name? ")
string* name = read_lineq()

printq @string("Hello, " + name + "!\n")
printq @string("How old are you? ")
int32 age = ConvertStringToInt(read_lineq())

printq @string("You are " + @int age + " years old\n")

mfree(name)
Console.getch()
```

---



---


## Авторы

- **Qrell* — разработка и поддержка
