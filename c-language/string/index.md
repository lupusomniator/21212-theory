# Строки. Длина строки. Инициализаторы строк.


Содержание:

1. Понятие строки в Си. Особенности
1. Ввод строки и вывод
1. Функции для строк
____

## 1. В Си строк нет.

Строка – это последовательность символов.

В памяти компьютера каждый символ представляется в виде последовательности 0 и 1, составленной по таблице кодировки ASCII. Это работает и в обратную сторону: из нулей и единиц можно получить символ.

Пример: “0” соответствует код 48 или в двоичной 110000. 

Технически, строка — это массив символов:

|H|E|L|L|O|\0|
| :-: | :-: | :-: | :-: | :-: | :-: |

Главным её отличием от простого массива является наличие в конце спец. символа “\0” который показывает, где заканчивается строка. В дальнейшем важно не забывать о его присутствии.
____
## 2. Ввод строки

Задать строку можно несколькими способами: прописать заранее или создать переменную (с заранее определенным максимальным кол-вом символов) и ввести ее с клавиатуры / из файла.

Стоит отметить, строки могут быть типа char или int (в зависимости от того, что мы собираемся в них хранить).
```c++
  char string[] = "hello";
```

В таком варианте введения строки компилятор сам определит, сколько необходимо выделить памяти.

```c++
  char string[] = {'h', 'e', 'l', 'l', 'o', '\0'};
```

Как и в прошлом случае переживать о нужном количестве памяти не следует, однако важно помнить при таком вводе строки о необходимости поставить в конец завершающий символ “\0”. Именно он и будет отличать просто массив от строки.

```c++
  char string[10];
```

Мы подготовили переменную string для записи в неё последовательности символов, ВАЖНО вписать мы можем только 9 (n-1) знаков т.к. последний по умолчанию отводится для “\0”.

Можно создавать указатели на строку: 

```c++
  char* stringLink = string;
```


Теперь мы готовы записать строку с клавиатуры, для этого воспользуемся функцией scanf() :

```c++
#include <stdio.h>

int main() {
	char string[100];
	scanf("%99s", string);
return 0;
}
```

“%99s”  - мы обезопасили себя от переполнения буфера, так как больше чем 99 символов он не сможет съесть; s – сообщает компилятору о том, что вводится строка.

Для подсчета фактической длины строки можно использовать конструкцию или готовую функцию (будет ниже)
```c++
int len = 0;
while (string[len] != '\0') {
	len++;
}
```

Вывод осуществляется благодаря функции 
```c++
printf(“%s”, string);
```
____
## 3. Основные функции стандартной библиотеки string.h



|Функция|Описание|
| :-: | :-: |
|char\* strcat(char\* s1, char\* s2)|присоединяет s2 к s1, возвращает s1|
|char\* strncat(char\* s1,  char\* s2, int n)|присоединяет не более n символов s2 к s1, завершает строку символом '\0', возвращает s1|
|char\* strсpy(char\* s1, char\* s2)|копирует строку s2 в строку s1, включая '\0', возвращает s1|
|char\* strncpy(char\* s1, char\* s2, int n)|Копирует не более n символов строки s2 в строку s1, возвращает s1|
|int strcmp(char\* s1, char\* s2)|Сравнивает s1 и s2, возвращает значение 0, если строки эквивалентны|
|int strncmp(char\* s1, char\* s2, int n)|сравнивает не более n символов строк s1 и s2, возвращает значение 0, если начальные n символов строк эквивалентны|
|int strlen(char\* s)|Возвращает кол-во символов в строке s|
|char\* strset(char\* s, char c)|Заполняет строку s символами, код которых равен значению с, возвращает указатель на строку s|
|char\* strnset(char\* s, char c, int n)|заменяет первые n символов строки s символами, код которых равен c, возвращает указатель на строку s|



<p align="center">
<img src="images/mem.png" widdth="300" height="600">
</p>



