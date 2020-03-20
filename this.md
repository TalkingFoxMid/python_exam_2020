# 1. Функции. Декораторы. Модуль functools.

## **Функция [(PEP 3107)](https://www.python.org/dev/peps/pep-3107/)** - объект, у которого определен метод __call__ - он вызовется при применении оператора “круглые скобки”. В именах функций могут быть только маленькие буквы, слова разделяются _.

**Именные функции** создаются при помощи инструкции def.
```python
def named_function():
	return;
```

Используется ключевое слово <span style="color:orange">return</span> для возврата значения из функции. Если <span style="color:orange">return</span> нет, то возвращается <span style="color:orange">None</span>. Благодаря механизму [кортежей](https://www.notion.so/2-tuple-list-set-frozenset-dict-Comprehensions-copy-collect-28acfb6e0c05480e9c7001ad1c0d242e#352411804efd4875a7c5c176ca754731), функции в питоне могут возвращать несколько объектов.

Функции могут иметь:

> ● Произвольное число аргументов
> ● Позиционные и именованные аргументы
> ● Обязательные и необязательные аргументы

**Позиционные аргументы** – сопоставление: первый переданный аргумент – это первый аргумент, который принимает функция и т.д. *** - распаковывается список или любая другая последовательность. *Args* - tuple. **** - словарь. *Kwargs* – словарь.

Аргументы по умолчанию, а также именованные аргументы всегда должны перечисляться в конце.

```python
>>> def f(a, b, c):
		print(a, b, c)
>>> f(1, 2, b = 3)
TypeError: f() got multiple values for argument 'b'

>>> def f(*args, b):
		print(args, b)
>>> f(1, 2, 3)
TypeError: f() missing 1 required keyword-only argument: 'b'

>>> f(1, 2, 3, b=3)
(1, 2, 3) 3

>>> def f(*args, **kwargs, c):
		print(args, kwargs, c)
SyntaxError: invalid syntax

def f(c, x = []):
	x.append(c)
	return x

print(f(2)) # [2]
print(f(3)) # [2, 3]
print(f(4)) # [2, 3, 4]

def f(a, b, c = 3):
	pass
f(1, b=4, c=6) # но не f(1, 4, b=6) - выкинет исключение!
```


Попытка очистить словарь приводит к странному поведению: словарь очищается, но некоторые функции (__call__, __type__, e.t.c.) продолжают работать. Все остальные же кидают RunTimeError.

Так как функции являются самостоятельными объектами, их можно сохранять в переменные. Функции можно передавать в качестве аргументов в другие функции. Например, встроенные функции map и filter принимают в качестве первого параметра функцию. Можно также возвращать функции из других функций. Например объявленные внутри возвращающей функции или полученные ей в качестве аргумента.

Функции, как и классы, могут иметь атрибуты:
```python

def a():
	pass
```

```python

	a.b = 1
	a.c = a
```

**Анонимные функции [(PEP 3107)](https://www.python.org/dev/peps/pep-3107/)**. Для их создания используется ключевое слово: lambda. Содержат одно выражение и выполняются быстрее. Их не обязательно присваивать переменной.
```python
f = lambda x, y: x + y # x, y - параметры, x + y - возвращаемое значение
```

Использование без переменной:
```python
	(lambda x, y: x + y)(1, 2) # Возвращает сумму чисел 1 и 2
```
У функции есть в частности следующие поля:
- **__name__** **:** **<class 'str'>** — хранит имя функции (при сохранении функции в переменную, имя останется неизменным). 
  У лямбда-функций **__name__**  == ’<lambda>’
- **__module__** **: <class 'str'>** — хранит название модуля, в котором определена функция.
- **__doc__** **:** **<class 'NoneType'>** — возвращает [docstring](https://www.notion.so/1-functools-e65f2510041b4046862b1a319b789ff8#f53ede852f344352af4cca6ac2ad4e45) функции (краткая документации по функции в тройных кавычках ''' text '''). По умолчанию поле **__doc__** == None.
- **__code__ : <class 'code'>** — объект кода для функции, через который можно получить доступ к байт коду самой функции и прочим полезным вещам (константам, флагам функции (который показывают ее тип (генераторная, асинхронная)) и прочее)

Функции объявляют, чтобы:

- избежать дублирования кода
- выделить логические блоки кода для повторного использования
- улучшить читаемость кода
- повысить управляемость кода (удобно для нахождения ошибок)

**Docstring [(PEP 257)](https://www.python.org/dev/peps/pep-0257/)** — это такая строковая переменная, которая идет сразу за объявлением модуля, функции, класса, метода. Таким образом питон предоставляет удобный способ добавления документации. Существует много средств для автоматического генерирования документации, которые используют докстринг. Докстринг очень похож на комментарий, но заключается в тройные кавычки. Все функции должны иметь докстринг, который содержит описание работы этой функции. Комментарии же обычно пытаются объяснить эту работу.

Вывести документацию: module_name.func_name.__doc__

При помощи **pydoc имя_файла** можно автоматически увидеть документацию, которая благодаря docstring была создана к нашему файлу.

**pydoc -w имя_файла создаст** html страницу с документацией.

**Декоратор [(PEP 318)](https://www.python.org/dev/peps/pep-0318/)** - это функция, которая принимает на вход функцию и возвращает новую функцию. Декоратор в питоне представляет собой функцию, которая может изменить поведение другой функции. Это возможно, потому что функции в питоне являются объектами, и их можно передавать в качестве аргументов и возвращать в качестве результатов другой функции.
```python=
@bold
def hello(who):
	print("Hello " + who)
```

При вызове функции hello(who) она сначала передается как аргумент декоратору bold, который должен вернуть некоторую другую функцию, которая должна принять аргумент who, предназначенный для оригинальной функции hello.

Задача, которая ставится при создании функции-декоратора - написать функцию, которая принимает одну функцию, а возвращает другую.
```python=
def bold(fun hello):
	def inner(who):
		print("<b>")
		fun hello(who)
		print("</b>")
	return inner
```

Если бы перед def hello(who) не было декоратора, то аналогичный результат можно было получить следующим кодом:
```python=
	hello = bold(hello) # bold заменяет ссылку на hello, подставляет туда функцию inner
	hello("Igor")
```

Одновременно можно применять несколько декораторов. Например, было бы неплохо выделять результат функции hello ещё и тегами <i> и </i>. Для этого нужно определить еще один декоратор italic. Важно соблюдать порядок добавления декораторов к функции.
```python=
def italic(fun_hello):
	def inner(who):
		print("<i>")
		fun hello(who)
		print("</i>")
	return inner
@italic
@bold
def hello(who):
print ("Hello " + who)
```

Декоратор с параметром, который перед вызовом функции будет засыпать на одну секунду
```python=
def sleep(seconds):
	"""принимает количество секунд, на которое мы будем засыпать, то есть по сути
sleep - это некая фабрика декораторов, он сам будет производить декораторы"""
	def decorator(f):
		def decorated(*args, **kwargs):  # заменит исходную функцию f
			time.sleep(seconds)
			return f(*args, **kwargs)
		decorated.__name__ = f.__name__  # при запуске будет сохраняться имя функции
		return decorated
return decorator
```


```python=
@sleep(1)
def printer():
	print("home sweet home")
	
	
# Конструкция аналогична следующей:
def printer():
	print("home sweet home")
printer = sleep(1)(printer)


def count(f):
	def decorated(*args, **kwargs):
		decorated.count += 1
		print(f'{f.__name__} вызывалась уже {decorated.count} раз')
		return f(*args, **kwargs)
		decorated.count = 0
		decorated.__name__ = f.__name__  # при запуске будет сохраняться имя функции
	return decorated
```

Декораторы могут применяться к классам [(PEP 3129)](https://www.python.org/dev/peps/pep-3129/):
```python=
import functools
def singleton(cls):
	"""Make a class a Singleton class (only one instance)"""
	@functools.wraps(cls)
	def wrapper_singleton(*args, **kwargs):
		if not wrapper_singleton.instance:
			wrapper_singleton.instance = cls(*args, **kwargs)
		return wrapper_singleton.instance
	wrapper_singleton.instance = None
	return wrapper_singleton
	
	
@singleton
class TheOne:
	pass
```

Это аналог переопределения метода __init__ в исходном классе.

При создании экземпляра TheOne будет вызвана функция, созданная в декораторе

Так как класс сам по себе является функцией, а именно класс, как конструктор новых объектов, то можно создать для этого класса функцию-обёртку, которая внутри себя будет производить определённого рода действия, а затем возвращать новый экземпляр этого класса.
```python=
TheOne = singleton(TheOne)
one = TheOne() # вызов функции wrapper_singleton
```

Классы сами могут быть декораторами, для этого в __init__ класса первым аргументом должна приниматься функция, а класс должен реализовывать метод __call__. Кроме этого надо переопределить метод __get__, так как он отвечает за поиск методов и использовать декоратор wraps, чтобы сохранить метаинформацию о функции:
```python=
import types
from functools import wraps

class DecoratorClass:
	def __init__(self, func):
		wraps(func)(self)
		print("init is called")

	def __call__(self, *args, **kwargs):
		print("call is called")
		return self.__wrapped__(*args, **kwargs)

	def __get__(self, instance, cls):
		if instance is None:
			return self
		else:
			return types.MethodType(self, instance)
```

```python=
@DecoratorClass
def test_function():
	print("func is called")
```

Реализация декораторов classmethod и staticmethod
```python=
class ClassMethod(object):
	"Emulate PyClassMethod_Type() in Objects/funcobject.c"
	
	def __init__(self, f):
		self.f = f

	def __get__(self, obj, klass=None):
		if klass is None:
			klass = type(obj)
		def newfunc(*args):
			return self.f(klass, *args)
		return newfunc

class StaticMethod(object):
"Emulate PyStaticMethod_Type() in Objects/funcobject.c"

def __init__(self, f):
self.f = f

def __get__(self, obj, objtype=None):
return self.f
```

### Модуль functools [(python-library: functools)](https://docs.python.org/3/library/functools.html).

Он содержит некоторые функции и декораторы.

**cmp_to_key()** — превращает функцию сравнения в key-функцию. 
Используется с инструментами, принимающие key-функции (sorted(), min(), max(), heapq.nlargest(), heapq.nsmallest(), itertools.groupby()). 
Эта функция в основном используется в качестве переходного инструмента для программ, преобразованных из Python2, которые поддерживали использование функций сравнения.
Функция сравнения - функция, принимающая два аргумента, сравнивающая их и возвращает отрицательное число, если первый аргумент меньше, нуль, если равен и положительное число, если больше второго. 
Key-функция - функция, принимает один аргумент и возвращает другое значение, определяющее положение аргумента при сортировке.

Пример:
```python=
import functools

def comparator(x, y):
	return 1 if x > y else -1

iterable = ["aa", "aaa", "a"]
print(sorted(iterable, key=lambda x: len(x)))
print(sorted(iterable, key=functools.cmp_to_key(comparator)))
```

@functools.**lru_cache**(maxsize=128, typed=False) — декоратор, который сохраняет результаты maxsize последних вызовов. Это может сэкономить время при дорогих вычислениях, если функция периодически вызывается с теми же аргументами.

Поскольку в качестве кэша используется словарь, все аргументы должны быть хешируемыми.

Если maxsize установлен в None, кэш может возрастать бесконечно. Также функция наиболее эффективна, если maxsize это степень двойки.

Если typed == True, аргументы функции с разными типами будут кэшироваться отдельно. Например, f(3) и f(3.0) будут считаться разными вызовами, возвращающие, возможно, различный результат.

Чтобы помочь измерить эффективность кэширования и отрегулировать размер кэша, обернутая функция дополняется функцией cache_info(), возвращающая namedtuple, показывающий попадания в кэш, промахи, максимальный размер и текущий размер. В многопоточном окружении количество попаданий и промахов приблизительно.

Также имеется функция cache_clear() для очистки кэша.

Оригинальная функция доступна через атрибут __wrapped__.

```python
>>> from functools import lru_cache
>>> # Числа Фибоначчи (попробуйте убрать lrucache) :)
>>> @lru_cache(maxsize=None)
... def fib(n):
... 	if n < 2:
...		return n
...	return fib(n-1) + fib(n-2)
>>>
>>> print([fib(n) for n in range(100)])
[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377, 610, ...]
>>> print(fib.cache_info())
// Cache Info(hits=196, misses=100, max size=None, cursize=100)
```
**partial** - принимает функцию и возвращает функцию с зафиксированными некоторыми параметрами.
Например, есть функция add(a, b).
Тогда inc(b), которая будет эквивалентна add(1, b) - это просто partial(add, 1).
```python
>>> from functools import partial
>>> basetwo = partial(int, base=2)
>>> basetwo.__doc__ = 'Convert base 2 string to an int.'
>>> print(basetwo('10010'))
18
```

**reduce** - лучше пояснить на примере.
reduce(func, [1, 2, 3]) выдаст результат выражения func(func(1, 2), 3).
Если передать необязательный параметр initial - он будет приписываться в начале к последовательности.
reduce(func, [1, 2, 3], -1) выдаст результат func(func(func(-1, 1), 2), 3)

Похожа на aggregate в шарпе

**total ordering** - декоратор для класса, который определяет все функции сравнения, если определены необходимые(всегда мечтал).
Например, если определены функции lt и eq(обязательна), то можно из этого определить le, ge, gt. Вот он это как раз и делает.

**wraps** - копирует всю информацию об оборачиваемой функции (ее имя, из какого она модуля, её документацию и т.п.) в функцию-обертку. Так, если в примере ниже мы напишем log("Test")(func_name).__name__, то получим 'func_name'. Но, если убрать этот декоратор, то получим 'wrapper'. Удобная функция для вызова partial(update_wrapper, ...) как декоратора при определении функции-оболочки.
```python
import functools

	def log(arg):
		def decorator(func):
			@functools.wraps(func) #очень важная строчка!!!
			def wrapper(*args, **kwargs):
				print(str(arg))
				msg = "Called {} with {} and {}".format(func.__name__, args, kwargs)
				print(msg)
				rv = func(*args, **kwargs)
				print("{}({}) -> {}".format(func.__name__, str(args) + ", " + str(kwargs), str(rv)))
				return rv
			return wrapper
		return decorator
```
```python=
import time
def timer(f):
	def tmp(*args, **kwargs):
		t = time.time()
		res = f(*args, **kwargs)
		print ("Время выполнения функции: %f" % (time.time()-t))
		return res
	return tmp

@timer
def func(x, y):
	return x + y
```

Декораторы также могут быть и с параметрами:
```python=
#decorator with params
class log:
	def __init__(self, prefix):
		self.prefix = prefix

	def __call__(self, func):
		@functools.wraps(func)
		def wrapper(*args, **kwargs): 
			rv = func(*args, **kwargs)
			print("{} '{} called with'".format(self.prefix, func.__name__), args, kwargs, '->', rv)
		return rv
	return wrapper

@log('Огородников оценит')
def f(x: int, y: int) -> int:
	return x + 2*y
```

### Замыкание в лямбда-функции:
```python=
arr = []
for i in range(10):
	arr.append(lambda x: i + x)
print(arr[2](2))
# ожидаем 4, выведет 11 (9 + 2) <- замыкание, во всех лямбдах i = 9 (последнее значение i)

# решение проблемы
arr = []
for i in range(10):
	arr.append(lambda x, i = i: i + x)
print(arr[2](2)) # ожидаем 4, выведет 4
```

[https://python-scripts.com/import-functools](https://python-scripts.com/import-functools)

[https://pythonworld.ru/moduli/modul-functools.html](https://pythonworld.ru/moduli/modul-functools.html)