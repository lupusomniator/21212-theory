# Оглавление
1. [А нужно ли оно нам?](#intro)
2. [Malloc()](#mal)
3. [Calloc()](#cal)
4. [Realloc()](#real)
5. [Free()](#free)

# Динамическое выделение памяти и с чем это едят

## А нужно ли оно нам? <a name="intro"></a>

При создании массива с фиксированными размерами под него выделяется определенная память. Например, пусть у нас будет массив с пятью элементами:

```c++
double array[5] = {1.1, 2.2, 3.3, 4.4, 5.5};
```
Для такого массива выделяется память 5 * sizeof(double) байт. Таким образом, мы точно знаем, сколько в массиве элементов и сколько он занимает памяти. Однако это не всегда удобно. 
1) Работа с массивом данных неизвестной величины
2) Недостаток памяти в стеке
3) Придумайте сами

В таких ситуациях необходимо выделять память динамически, для чего используется ряд функций, которые определены в заголовочном файле `stdlib.h`



## `Malloc()` <a name="mal"></a>
```c++
void *malloc(
   size_t size
);
```
## Параметры
`size` - размер выделяемой памяти в байтах

## Возвращаемое значение

- Функция возвращает указатель `void` на выделенное пространство или значение `NULL`, если памяти недостаточно. 
- Чтобы вернуть указатель на тип, отличный от `void`, используйте приведение типов для возвращаемого значения. 
- Дисковое пространство, на который указывает возвращаемое значение, будет гарантированно соответствовать требованиям к выравниванию для хранения объектов любого типа, если таковые требования не превышают базовые. 
> В Visual C++ в качестве базовых устанавливаются требования к выравниванию для типа double или 8 байтов. В коде, ориентированном на 64-разрядные платформы, он составляет 16 байт.

 ## `Calloc()`<a name="cal"></a>
```c++
void *calloc(
   size_t number,
   size_t size
);
```
## Параметры
`number` - число элементов.
`size` - длина каждого элемента в байтах.

## Возвращаемое значение
- Функция возвращает указатель `void` на выделенное пространство или значение `NULL`, если памяти недостаточно. 
- Чтобы вернуть указатель на тип, отличный от `void`, используйте приведение типов для возвращаемого значения. 
- Дисковое пространство, на который указывает возвращаемое значение, будет гарантированно соответствовать требованиям к выравниванию для хранения объектов любого типа, если таковые требования не превышают базовые. 
>Функция `calloc()` выделяет место для хранения массива числовых элементов, каждый из которых имеет Размер в байтах. Каждый элемент инициализируется значением 0.

 ## `Realloc()`<a name="real"></a>
```c++
void *realloc(
   void *memblock,
   size_t size
);
```
## Параметры
`memblock` - указатель на ранее выделенный блок памяти.
`size` - новый размер в байтах.

## Возвращаемое значение
- Функция возвращает указатель `void` в перераспределенном (и, возможно, перемещенном) блоке памяти.
- Если доступной памяти недостаточно, чтобы расширить блок до заданного размера, исходный блок останется без изменений и будет возвращено значение `NULL`.
- Если `size` равен нулю, то блок, на который указывает `memblock`, освобождается; возвращается значение `NULL`, `memblock` по-прежнему указывает на освобожденный блок.
- Дисковое пространство, на который указывает возвращаемое значение, будет гарантированно соответствовать требованиям к выравниванию для хранения объектов любого типа, если таковые требования не превышают базовые. 
- Если параметр `memblock` имеет значение `NULL`, функция `realloc()` ведет себя так же, как и функция [`malloc()`](#mal)
> Функция `realloc()` не была обновлена для реализации поведения C17, так как новое поведение несовместимо с операционной системой Windows.

## `Free()` <a name="free"></a>
```c++
void free(
   void *memblock
);
```
## Параметры
`memblock` - ранее выделенный блок памяти, который требуется освободить.

## Действие функции

Функция `free()` освобождает блок памяти (`memblock`), который был выделен ранее вызовом функции `calloc()`, `malloc()` или `realloc()`. Число освобожденных байтов эквивалентно количеству байтов, запрошенному при выделении блока (или выделении заново, если использовалась функция realloc). Если `memblock` имеет значение `NULL`, указатель не обрабатывается и функция `free()` немедленно возвращает управление.
> Попытка освободить недопустимый указатель (указатель на блок памяти, не выделенный с помощью `calloc()` , `malloc()` или `realloc()` ) может повлиять на последующие запросы на выделение и привести к ошибкам.