# 7. Итераторы. Генераторы.
##
## Итераторы

**Итератор** - объект, представляющий последовательный доступ к данным из контейнера (память не тратится, так как промежуточные данные выдаются по мере необходимости при запросе). Контейнер поддерживает итерирование, если определён метод **__iter__**.

Итератор - объект, у которого определены два метода:

1. **\_\_iter__** - возвращает сам итератор
2. **\_\_next__** - возвращает очередной элемент или выбрасывает **StopIteration** (если элементы закончились)

Встроенные функции языка **iter**() и **next**() вызывают эти методы.

Итератор нельзя отмотать назад.

При итерировании по словарю будут возвращаться ключи

l = [1, 2, 3]

i = iter(l) # создание итератора. Можно применять к объектам, поддерживающим интерфейс итератора. iter(l) тоже самое что и l.__iter__().

Теперь можно использовать next:

next(i) # 1 тоже самое что и i.__next__()

next(i) # 2

Если произойдет выход за границу, то сгенерируется StopIteration.

Пример итератора для функции range.
```python
class Range:
    def __init__(self, start, end):
        self._start = start
        self._end = end
        self._current = start
    
    
    def __iter__(self):
        return self
    
    
    def __next__(self):
        if self._current < self._end:
            self._current += 1
            return self._current - 1
        else:
            raise StopIteration
```

Использование:
```python
r = Range(0, 3)
i = iter(r)
print(next(i)) # 0
print(next(i)) # 1
print(next(i)) # 2
print(next(i)) # StopIteration

```

##
## Устройство цикла for

    product = 1
    for i in [1, 2, 4, 8]:
       product *= i
    print(product) # 64

    product = 1
    i = iter([1, 2, 4, 8])
    while True:
        try:
            product *= next(i)
        except StopIteration:
            break
    print(product) # 64

For берёт итератор от переданной ему последовательности. Если эта последовательность была контейнером, то for получит итератор. Но for работает как с контейнерами, так и с итераторами, поскольку при попытке вызова iter от итератора, итератор возвращает сам себя. Затем вызывает next. Если перечисление закончилось цикл обрабатывает StopIteration и завершается.
## 
## Функция zip(*iterables)
Функция zip() принимает на вход итераторы, из которых собирает и возвращает новый итератор, возвращающий кортежи элементов входных итераторов с одинаковыми индексами, начиная с 0 и заканчивая индексом последнего элемента в итераторе с наименьшим числом элементов. (под числом элементов итератора подразумеваю то, сколько раз можно вызвать next(iterator) прежде чем произойдёт StopIteration)
```python
new_zip = zip([1,2,3,4],["dog","cat","wolf"])
print(next(new_zip)) # (1, 'dog')
print(next(new_zip)) # (2, 'cat')
print(next(new_zip)) # (3, 'wolf')
print(next(new_zip)) # StopIteration
```

> zip() может не принимать аргументов вовсе, но в таком случае он вернёт пустой итератор, next от которого выбросит StopIteration.

##
## Генераторы

**Генератор** - объект, у которого определены пять методов:

1. __iter__()
2. __next__()
3. send(arg)
4. throw(type [, val [, tb]])
5. close()

Генератор похож на итератор, только является функцией. При вызове этой функции в цикле, она в каждой новой итерации выдает следующее значение. Пишется эта функция с использованием оператора yield.

yield работает как return, с одной разницей, что между вызовами функций, все состояния и данные будут при следующем вызове функции такими, какими они были на момент предыдущего исполнения yield.

По сути это ключевое слово создает итератор, просто создание итератора упрощается за счет того, что методы __iter__() и __next__() создаются автоматически. При выходе из функции не по оператору yield автоматически генерируется StopIteration. Когда вызывается next(), начинается выполнение функции.
##
## **Генераторная функция**:
## **СЛОЖНАЯ ТЕМА.**
```python
def Range(start, end):
    current = start
    while current < end:
        yield current
        current += 1

    # Range(0, 5)  - возвращает генератор
for x in Range(0, 5): 
    print(x)
```

**yield** - возвращает значение, и указывает на место, с которого в следующий раз начнется функция

**yield from <expr>** - начиная с версии 3.3, эта конструкция - более короткий вариант for item in iterable: yield item

Эту конструкцию можно читать так: yield (вернуть значение) [<стоп мыслей>](https://2ch.hk/rf/) from (взять значение из) <expr>
```python
def gen(x):
    yield from range(x)
list(gen(5)) # [0, 1, 2, 3, 4]
```

**send() —**

Конструкция yield something способна возвращать значение. Если у генератора вызывается метод next, то yield something возвращает None. Если же вызывается метод send(value), то yield something возвращает value.

Как только вызывается первый next(generator), функция начинает выполняться с самого начала до того, как встретится первый yield something. Тогда генератор вернёт итеративное значение и остановится. То, что вернёт этот yield something — ещё не известно. 
В первом приближении допустимо считать, что send(None) и next() это одно и то же. Как только происходит send(value), yield, на котором функция была заморожена, возвращает value внутри этой функции, после чего функция продолжает своё выполнение, пока не встретится следующий yield, который вернёт итеративное значение.
```python
    def send_generator():
        while(True):
            a = yield 5
            print(a)
    g = send_generator()
    g.send(None) # Итератор выбросит 5
    g.send(99) # yield, выбросивший 5, присвоит переменной a значение 99
    					 # Затем функция продолжит выполнение, напечатает 99, перейдёт в 
    					 # новую итерацию цикла, после чего вызовет yield 5, выбросит 5 в 
    					 # итератор, после чего заморозится, ещё ничего не присвоив a.
    g.send(None)# Функция размораживается, переменной a присваивается значение 
    						# None, и так до тех пор пока она не выкинет 5 в итератор.
```

Рассмотрим генератор Range(start, end), реализующий метод send():
```python
def Range(start, end):
    current = start
    while(True):
        get = yield current
        current += 1
        if(get is not None):
            current = get
```

Заметим, что send(value) может быть вызван лишь в момент, когда генератор уже вернул итеративно некоторое значение и находится в замороженном состоянии, поэтому вызов generator.send(value) выводит:
TypeError: can't send non-None value to a just-started generator.
При чём generator.send(None) работает аналогично generator.next()

**close**() - возбуждает специальное GeneratorExit, чтобы вынудить завершить итерации.

**throw(typ, [, val [,tb]])** -вызывает исключение в генераторе в строке с yield, так мы можем сообщить о каких - то обстоятельствах в генератор и там их обработать.

yield from пробрасывает и эти исключения.

```python
def gen_items():
    for i, item in enumerate(["", "foo", "", "foo", "bad"]):
        if not item:
            continue
        try:
            yield item
        except Exception:
            raise Exception("error during index: %d" % i)
```
```python
# Stack trace includes only: "ValueError: bad value"
for item in gen_items():
    if item == "bad":
        raise ValueError("bad value")
```
```python
# Stack trace also includes: "Exception: error during index: 4"
gen = gen_items()
for item in gen:
    if item == "bad":
        gen.throw(ValueError, "bad value")
```

1. Если в генераторной функции встречается return, то он приводит к генерации исключения StopIteration, но куда девается значение, возвращаемое через return? Вот и ответ:

```python
def f():
    yield 1
    return 2

def g():
    return (yield from f())

def get_return(generator):
    while True:
        try:
            next(generator)
        except StopIteration as e:
            return e.value

print(get_return(f())) # 2
print(get_return(g())) # 2
```

> Более того, yield from это значение тоже "пробрасывает".

1. Что на самом деле происходит, когда вы пишите в коде yield from, можно узнать в [PEP 0380](https://www.python.org/dev/peps/pep-0380/#formal-semantics).

Создание генератора (**Генераторное выражение**):

a = (x ** 2 for x in range(4))

type(a) # <class 'generator'>

next(a) # 0

next(a) # 1

Вложенные генераторные выражения:

```python
g = (x * y for x in range(3) for y in range(4))
list(g) # [0, 0, 0, 0, 0, 1, 2, 3, 0, 2, 4, 6]
```

Разница между Comprehensions и генераторными выражениями:

В Comprehensions все элементы сохраняются в оперативную память (ест много памяти, нельзя описать бесконечные объекты)

Генераторные выражения при создании ничего не вычисляют -> эффективнее по памяти, позволяют описать бесконечные объекты

### 

### Модуль <itertools>

#### Itertools.count(start=0, step=1)
Генератор, возвращающий числа, начиная со start с шагом step.
```python
def count(start=0, step=1):
    # count(10) --> 10 11 12 13 14 ...
    # count(2.5, 0.5) -> 2.5 3.0 3.5 ...
    n = start
    while True:
        yield n
        n += step
```
#### Itertools.cycle(iterable)
Генератор, возвращающий по кругу элементы итератора.
Начинает с первого, доходит до последнего и начинает сначала.

*Замечание*: cycle от пустого итератора выкидывает StopIteration сразу после вызова \_\_next__()
```python
def cycle(iterable):
    # cycle('ABCD') --> A B C D A B C D A B C D ...
    saved = []
    for element in iterable:
        yield element
        saved.append(element)
    while saved:
        for element in saved:
              yield element
```
#### Itertools.repeat(object, times=None)
Генератор, способный возвратить object ровно number раз, после чего выкидывает StopIteration.

Если count = 0, выкидывает StopIteration после вызова первого \_\_next__().

```python
def repeat(object, times=None):
    # repeat(10, 3) --> 10 10 10
    if times is None:
        while True:
            yield object
    else:
        for i in range(times):
            yield object
```
#### Itertools.accumulate(any_iterable_object)
Первая итерация просто возвращает первый элемент вне зависимости от функции.

Последующие итерации возвращают f(past_yield,item) значение заданной в аргументе функции от двух элементов: прошлого возвращённого значения и нового элемента заданного итератора. (порядок важен)
```python
def accumulate(iterable, func=operator.add, *, initial=None):
    'Return running totals'
    # accumulate([1,2,3,4,5]) --> 1 3 6 10 15
    # accumulate([1,2,3,4,5], initial=100) --> 100 101 103 106 110 115
    # accumulate([1,2,3,4,5], operator.mul) --> 1 2 6 24 120
    it = iter(iterable)
    total = initial
    if initial is None:
        try:
            total = next(it)
        except StopIteration:
            return
    yield total
    for element in it:
        total = func(total, element)
        yield total
```
#### Itertools.chain(*iterables)
Принимает на вход неограниченное количество различных итераторов и возвращает их элементы по очереди. (сначала элементы первого итератора, затем второго...)
```python
def chain(*iterables):
    # chain('ABC', 'DEF') --> A B C D E F
    for it in iterables:
        for element in it:
            yield element
```
#### Itertools.chain.from_iterable(iterable)
Делает то же самое, но на вход возвращает итератор, возвращающий итераторы.
```python
    # chain.from_iterable(['ABC', 'DEF']) --> A B C D E F
    for it in iterables:
        for element in it:
            yield element
```
#### Itertools.compress(data, selectors)






В документации есть реализация каждого из этих методов:

[https://docs.python.org/3/library/itertools.html](https://docs.python.org/3/library/itertools.html)

### Модуль Operator.

Модуль operator содержит набор функций, соответствующих операторам Python.

Вот некоторые функции:

operator.add(a, b) # суммирует два значения
operator.ne(a, b) # то же, что и a!=b
operator.f # возвращает ссылку, по которой можно получить атрибут "id"

Казалось бы, зачем нужны они? А ответ довольно прост - функциональное программирование с использованием итераторов иногда требует создания небольших функций для простых выражений. Иногда они могут быть выражены как лямбда-функции, но некоторые операции не обязательно реализовывать самостоятельно. К тому же, оператор работает чуточку быстрее лямбды:

[Copy of Untitled](https://www.notion.so/97508c0175ec470aa424372efb10ed06)

![7/image39.png](7/image39.png)

Так же есть in-place операторы, которые изменяют входной параметр. Например:

[Copy of Untitled](https://www.notion.so/89234f536f4b48d8b5a4af4e0c04cb06)

С обычным методом add список s не изменился бы.

![7/image28.png](7/image28.png)

##