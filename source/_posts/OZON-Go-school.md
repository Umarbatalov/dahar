---
title: OZON Go school
date: 2020-05-30 16:32:46
tags:
---

# В этой статье представлены условия и решения задач, которые необходимо было выполнить для поступления в OZON Go school. 

Материалы, которые понадобились в ходе выполнения задач:

1. [XOR](https://medium.com/@AndriiHeonia/xor-4b295f8c56f4)
2. [JOIN](https://blog.codinghorror.com/a-visual-explanation-of-sql-joins/)
3. [Как прочитать большой файл средствами PHP (не грохнув при этом сервак)](https://habr.com/ru/post/345024/)

Собственно сами задачи.

## A. Уникальное число

|                      |                                      |
|:---------------------|:------------------------------------:|
|Ограничение времени   |1 секунда                             |
|Ограничение памяти    |64Mb                                  |
|Ввод                  |стандартный ввод или input-201.txt    |
|Вывод                 |стандартный вывод или input-201.a.txt |
  
На вход программе подается большое количество целых чисел. Все числа, кроме одного, имеют пару, причем может быть
несколько одинаковых пар. Найдите число без пары. Формат ввода stdin десятеричные числа по одному на каждой строке
Формат вывода stdout десятеричное число.

*Пример*  

| Ввод | Вывод |
|:-----|:-----:|
|  1   |   3   |
|  2   |       |
|  2   |       |
|  1   |       |
|  2   |       |
|  3   |       |
|  2   |       |

Решение

```php
// Первое решение.
$f = file('input-201.txt');
$acc = [];

foreach ($f as $num) {
    isset($acc[$num]) ? ++$acc[$num] : $acc[$num] = 1;

    if (isset($acc[$num]) && $acc[$num] % 2 === 0) {
        unset($acc[$num]);
    }
}

file_put_contents('input-201.a.txt', (int)array_key_first($acc));

// Второе решение.
$f = file('input-201.txt');
$acc = 0;

foreach ($f as $num) {
    $acc ^= (int)$num;
}

file_put_contents('input-201.a.txt', $acc);
```

## B. Теги

|                      |                                      |
|:---------------------|:------------------------------------:|
|Ограничение времени   |1 секунда                             |
|Ограничение памяти    |64Mb                                  |
|Ввод                  |стандартный ввод или input.txt        |
|Вывод                 |стандартный вывод или output.txt      |

В базе данных имеется таблица с товарами goods (id INTEGER, name TEXT), таблица с тегами tags (id INTEGER, name
TEXT) и таблица связки товаров и тегов tags_goods (tag_id INTEGER, goods_id INTEGER, UNIQUE (tag_id, goods_id)).

Выведите id и названия всех товаров, которые имеют все возможные теги в этой базе.

БД - SQLite3. В качестве языка решения выберите make2.

Формат ввода - SQL-запрос.

*Решение*

Вариант №1
```sql
explain
select g.id as id, g.name as name
from goods g
         inner join tags_goods tg on g.id = tg.goods_id
         inner join tags t
group by g.id, g.name
having sum(tg.tag_id) = sum(t.id);
```

Вариант №2
```sql
explain
select g.id as id, g.name as name
from goods g
         inner join tags_goods tg on g.id = tg.goods_id
         inner join tags t
group by g.id, g.name
having count(distinct tg.tag_id) = count(distinct t.id);
```

## D. Сложение чисел

|                      |                        |
|:---------------------|:----------------------:|
|Ограничение времени   |1 секунда               |
|Ограничение памяти    |64Mb                    |
|Ввод                  |стандартный ввод        |
|Вывод                 |стандартный вывод       |

Даны два неотрицательных числа A и B (числа могут содержать до 1000 цифр). Вам нужно вычислить их сумму.

*Формат ввода*
Первая строка ввода содержит числа A и B, разделенные пробелом

*Формат вывода*
Результат сложения двух чисел нужно вывести на отдельной строке.

*Пример 1*

| Ввод | Вывод |
|:-----|:-----:|
|  1 2 |   3   |

*Пример 2*

| Ввод  | Вывод |
|:------|:-----:|
| 199 1 |  200  |

*Решение*

```php
$stdin = explode(' ', file_get_contents('php://stdin'));

$a = trim($stdin[0]);
$b = trim($stdin[1]);

$padLength = max(strlen($a), strlen($b));

$a = str_pad($a, $padLength, '0', STR_PAD_LEFT);
$b = str_pad($b, $padLength, '0', STR_PAD_LEFT);

$acc = 0;
$res = '';

for ($i = $padLength - 1; $i >= 0; $i--) {
    $sum = (int)$a[$i] + (int)$b[$i] + $acc;

    if ($sum > 9) {
        $acc = 1;
        $sum %= 10;
    } else {
        $acc = 0;
    }

    $res = $sum . $res;
}

file_put_contents('php://stdout', $acc > 0 ? $acc . $res : $res);
```

## F. Сумма двух

|                      |                        |
|:---------------------|:----------------------:|
|Ограничение времени   |1.5 секунд              |
|Ограничение памяти    |64Mb                    |
|Ввод                  |input.txt               |
|Вывод                 |output.txt              |

Дано целое положительное число "target". Также дана последовательность из целых положительных чисел. Необходимо
записать в выходной файл "1", если в последовательности есть два числа сумма, которых равна значению "target" или
"0" если таких нет.

*Формат ввода*

5
1 7 3 4 7 9 4

10
1 5 11 14 5 3

*Формат вывода*
1

*Примечания*
Все числа используемы в задаче находятся в диапазоне 0 < N < 999999999

*Решение*

```php
$f = fopen('input.txt', 'rb');

$t = (int)fgets($f);
// Будет читать до 200 байт.
$buff = 200;
$acc = [];
$res = '0';

// Будем читать строку по 200 байт и до символа пробела.
while ($nums = explode(' ', stream_get_line($f, $buff, ' '))) {
    if (feof($f)){
        break;
    }

    $el = (int)$nums[0];

    if ($el < $t) {
        if (isset($acc[$t-$el])) {
            $res = '1';
            break;
        }

        $acc[$el] = 0;
    }
}

fclose($f);

file_put_contents('output.txt', $res);
```
