# Стресс-тестирование
Рано или поздно наступает момент, когда ваш код - идеальный. Но не работает.
В эти моменты замыленный глаз уже не в состоянии разыскать в тексте незначительные ошибки, приводящие к некорректной работе в ряде сценариев,
поэтому одним из разумных решений становится прибегнуть к стресс-тестам.

Стресс-тесты - это многократное сравнение вашего решения с правльным на автогенерируемыъ входных данных задачи.
"Правильным" решением зачастую выступают какой-то тупой и простой алгоритм, в котором очень сложно ошибиться при написании.
Обычно таковым является простой перебор: крайне неэффективный, но всегда простой алгоритм.

Для дальнейшего понимания происходящего, далее будем рассматривать [задачу из домашки](https://contest.yandex.ru/contest/35393/problems/):
```
Студентка Маша очень любит контролировать свои расходы и доходы, поэтому ведет статистику заработка каждый месяц.
Недавно она осознала, что она еще и большая сладкоежка и очень много ее денег уходит на шоколадки.
Чтобы как-то сдерживать свои порывы к сладкому, она придумала правило: покупать шоколадки только тогда, когда доход за несколько месяцев подряд равен определенному числу.
За такую гениальную идею Маша решила наградить себя сладеньким,
которое она могла бы купить в прошлые месяцы следуя придуманному правилу.
Зная статистику доходов и расходов Маши, помогите ей определить, сколько шоколадок нужно купить.
В первой строке записано два числа через пробел:

Входные данные:
  * K (-100 < K < 100): та сумма доходов за несколько месяцев подряд, за которую нужно награждать себя шоколадкой
  * N (N < 100000): количество месяцев, которые Маша ведет отчетность (да, она старовата)
  Далее в каждой из N строк щаписано чилсо P_j (-1000 < P_j < 1000)
Выходные данные:
  Программа должна вывести одно число - количество промежутков [I,J] таких, что (P_I + P_{I + 1} + ... + P_J) == K
```

## Генератор входных данных
Для эффективности тестирования входные данные должны быть в той или иной мере рандомизированы: это позволяет при большом количестве тестов покрывать самые неожиданные случаи с одной стороны,
а с другой - не сильно париться о том, как именно производить генерацию. Для того, чтобы у вас была возможность воспроизвести нагенерированные тесты, следует использовать детерминированную рандомизацию,
т.е. ту, в которой можно несложно задать зерно генерации (*seed*).

Один из наиболее удобных интерфейсов на C++ для этого предоставляет библиотека [testlib](https://github.com/MikeMirzayanov/testlib), которая к тому же достаточно хорошо задокументирована. 
Для ее использования достаточно скачать исходники, сконцентрированные в файле **"testlib.h"**, и положить их рядом с main-файлом будущего генератора.
Один `#include "testlib.h"` - и вы можете пользоваться всем многообразием функций этой библиотеки.

Для того, чтобы зарегистрировать seed, следует воспользоваться функцией `registerGen(int, char**, 1)`.
Первым аргументом функции является размер массива строк из второго аргумента. 
Массив же строк может быть в целом произвольным, но обычно им является набор входных аргументов программы, задаваемых как параметры функции main.

Ниже представлен пример генератора для задачи выше.
```c++
#include "testlib.h"
#include <iostream>
using namespace std;

int main(int argc, char* argv[]) {
    registerGen(argc, argv, 1); // задаем seed использая входные аргументы программы
    int neededSum = rnd.next(-100, 100); // генерируем 'K' из условия
    int numbersCount = atoi(argv[1]); // используем в качестве 'N' первый аргумент входной строки
    cout << neededSum << " " << numbersCount << endl; // выводим первую строку входных данных
    for (size_t i = 0; i < numbCount; ++i) {
        cout << rnd.next(-1000, 1000) << endl; // генерируем 'P_i' из условия
    }
}
```

Теперь, если собрать проект и запустить его из консоли с аргументам, получится автосгенерированный тест:
```bash
$ ./testgen.exe 10
24 10
-545
-632
667
733
-501
435
942
397
-507
258
```

## Генератор ответов
Генератор ответов - то самое тупое решение, которое легко написать и несложно проверить на валидность. Это то решение, с которым вы будете сверять ваше "идеальное-но-неработающее" решение, и оно может быть написано вообще говоря на любом языке. Для примера выше возьмем C++:
```c++
#include <iostream>
#include <vector>

using namespace std;

int main() {
	int sum;
	int count;

	vector<int> nums;
	for (int i = 0; i < count; ++i) {
		int num;
		cin >> num;
		nums.push_back(num);
	}

	int resultCount = 0;
	for (int i = 0; i < count; ++i) {
		int currentSum = 0;
		for (int j = i; j < count; ++j) {
			currentSum += nums[j];
			if (currentSum == sum) {
				resultCount++;
			}
		}
	}
	cout << resultCount;
}
```

Теперь, когда у нас есть генератор входных данных и генератор ответов, мы можем сформировать готовый тест.
Например, так:
```bash
$ ./testgen.exe 10 > test_10; ./answergen.exe < test_10 > test_10.answ
```

или вот так:
```bash
$ ./answergen.exe < $(./testgen.exe 10) > test_10.answ
```

## Запуск стресс-тестов
Для того, чтобы наконец начать стресс-тестирование, нужны две вещи:
1) Ваше решение, которое хотя-бы компилируется
2) Скрипт запуска тестов

С первым, хочется верить, справятся все. Скрипт же по-сути должен множество раз вызывать строчки выше с разнообразными (возможно случайными) аргументам для генератора и сравнивать результаты тупого решения с вашим.
Сравнение, например, можно организовать через команду `FC` на Windows или `diff` на Unix:
```bash
$ ./testgen.exe 10 > test_10; ./answergen.exe < test_10 > test_10.answ; ./my_solution.exe < test_10 > test_10.sol; FC test_10.answ test_10.sol
Comparing files test_10.answ and TEST_10.SOL
FC: no differences encountered
$ echo $?
0
```

Вот пример скрипта запуска на Python:
```python
import subprocess
from subprocess import PIPE, STDOUT

for i in range(10000):
    testInput = subprocess.run(
            ["./testgen.exe", str(i + 1000)], # аргумент генератора будет вариороваться от 1000 до 11000
            stdout=subprocess.PIPE
	)

    p1 = subprocess.Popen(["./answergen.exe"], stdout=PIPE, stdin=PIPE, stderr=STDOUT) 
    p2 = subprocess.Popen(["./my_solution.exe"], stdout=PIPE, stdin=PIPE, stderr=STDOUT)  

    answerRes = p1.communicate(input=testInput.stdout)[0].decode()
    solutionRes = p2.communicate(input=testInput.stdout)[0].decode()

    if answerRes != solutionRes:
        print("===== FOUND MISMATCH ======")
        print("Input:\n", testInput.stdout.decode())
        aLines, sLines = answerRes.splitlines(), solutionRes.splitlines()
        if len(aLines) != len(sLines):
            print("Outputs has different sizes: {} vs {}".format(len(aLines), len(sLines)))
        print("Output:")
        for aLine, sLine in zip(aLines, sLines):
            if aLine != sLine:
                print("MISMATCH HERE -> answer: {} | solution: {}".format(aLine, sLine))
            else:
                print(aLine)
        print("===========================")
        break
```


Сложив все нужные файлы в папку со скриптом, достаточно просто выполнить команду `$ python run_tests.py` - и вуаля, стресс-тестирование запустилось.
В случае со скриптом выше оно остановится, когда будет найдена хотя-бы одна ошибка.
