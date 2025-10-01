### Встроенные функции
Rowset fuctions
Aggregate functions
Ranking functions
Scalar unctions

На курсе рассматриваются Aggregate fuctions и Scalar functions.

Некоторые функции начинаются с символов @@ по другому их называют глобальные переменные.
Для каждой БД функции хранятся в своей папке. Как свои, так и системные.
![[Pasted image 20240410081949.png]]

Скалярные функции возвращают одно значение.
Table-valued functions возвращают таблицу.
System functions - встроенные функции.
 
#### Команда GO
В предыдущих случаях сначала создавалась база данных, а затем в эту БД добавлялась таблица с помощью отдельных команд SQL. Но можно сразу совместить в одном скрипте несколько команд. В этом случае отдельные наборы команд называются пакетами (batch).

Каждый пакет состоит из одного или нескольких SQL-выражений, которые выполняются как одно целое. В качестве сигнала завершения пакета и выполнения его выражений служит команда GO.

Имеется несколько причин для разделения на batch:
- в случае если произойдет ошибка в одном из batch и он не сможет выполниться, другие смогут;
- раделитель пакетов. Одни выражения должны успешно выполниться до запуска других выражений. Например, при добавлении таблиц мы должны бы уверены, что была создана база данных, в которой мы собираемся создать таблицы.
![[Pasted image 20250504125840.png]]
В настройках программы ключевое слово *GO* можно назвать любым другим словом.

Options - Tools - Query Execution - Sql Server - Batch Separator
![[Pasted image 20240410084038.png]]

### System functions
#### @@ERROR
@@ERROR - возвращает информацию о последней возникшей сгенерированной ошибке.

Declare @myint int;
set @myint = 'drp';
go

select @@error
go

в колонку Result выводит номер последней возникшей ошибки - 245.
![[Pasted image 20240410084537.png]]
Если ошибок нет, то в колонке Result выведет 0.

#### Try/Catch

begin try
declare @myint int;
Set @myint = 1/0;
end try

begin catch
select
error_number() as ErrorNumber
,error_severity() as ErrorSeverity
,error_state() as ErrorState
,error_procedure() as ErrorProcedure
,error_line() as ErrorLine
,error_message() as ErrorMessage
end CATCH
![[Pasted image 20240413125817.png]]
Номер ошибки
степень серьезности состояния ошибки
степень серьезности состояния ошибки
никакой процедуры не было, поэтому выдало налл
Ошибку выдало на третьей линии
Описание ошибки

#### @@Identity
Функция Identity возвращает значение последнего вставленного идентификатора

@@Scope_Identity, @@Identity_Current - похожие функции

Insert into Flights
(FlightNumber, ScheduledDeparture, ScheduledArrival, DepartureAirport, ArrivalAirport, FlyStatus, AircraftCode, ActualArrival)
values
('zxc-124', '2023-25-06', '2023-13-06', 'departure', 'arrival', 'test', 'Code', 'Status')
go
Select @@IDENTITY
go

#### @@RowCount
Возвращает число строк затронутых при выполнении последней операции.
Сколько строк будет вставлено, изменено, обновлено

Update Flights
set FlightNumber = 'NewFlightNumber'
where FlightId = 5
go
Select @@ROWCOUNT
go

#### NEWID()
Создает уникальное значение типа uniqueidentifier
uniqueidentifier - это уникальный тип данных

Применяется как альтернатива Identity для присвоения уникальных значений.
Declare @myId uniqueidentifier
Set @myId = newid()
print @myid


#### ISNUMERIC
Определяет, имеет ли переданное выражение допустимый числовой тип (int, decimal и т.д.)
Возвращает 1/0.

select
ISNUMERIC (1),
ISNUMERIC (12),
ISNUMERIC ('12'),
ISNUMERIC ('21asd')
![[Pasted image 20240413135903.png]]

#### ISNull
Функция заменяет значение первого аргумента на второй, если в значении первого было Null.
Например в колонке значение равно Null, и в нашей выборке он будет показывать, что оно равно нулю.
![[Pasted image 20240413175551.png]]
Select Id, Lname, ISNull(salary, 0.0) as Salary
from Employees

#### Cast and convert
Преобразует один тип данных в другой
![[Pasted image 20240413175906.png]]
Explicit - явное приведение
Implicit - неявное

Select 10/3, 10./3,
cast (10 as decimal)/3,
convert (decimal, 10)/3
![[Pasted image 20240413180127.png]]

#### Try_функция
Try_cast, try_convert, try_parse - работают как предыдущие, но в случае невозможности приведения возвращают Null.

### Функции строк
#### ascii/unicode
возвращает код первого символа указанного символьного выражения
select ascii ('hello') [ascii],
UNICODE (N'Быть')  [Unicode]
![[Pasted image 20240413181415.png]]

#### char/nchar
Преобразовать код в букву
Select char(104) [char],
nchar(1073) [nchar]

#### Left, Right, Lower, Upper, Len, Reverse
left - вырезает N первых символов слева
right - вырезает N первых символов справа
Lower - приведение к нижнему регистру
Upper - приведение к верхнему регистру
Len - узнать длину без пробелов
Reverse - реверснуть

Select  left('absdefg', 2) [left],
right('asdfghj', 2) [right],
lower ('ASDFGHJ') [lower],
upper ('asdfghj') [upper],
len ('a s d f g h j') [len],
reverse('abcdefg') [reverse]

![[Pasted image 20240413182350.png]]

#### Stuff/substring
Удаление и замена символов в строке. 26 минута в ролике.

#### Ltrim/Rtrim
Удаляет все пробелы слева, справа, до начала символов

#### SPACE
добавляет определенное количество пробелов

#### Replicate
Создает строку из заданного количества указанных символов.

#### charindex
поиск в строке определенных символов, функция выводит индекс с которого начинается значение которое ищут.

#### replace
заменить значение

#### Concat
правильно конкатенирует строки с NULL значениями

### Математические функции

#### sqrt
найти корень
sqrt(144)

#### square
возвести в корень

#### power
power(2, 8)
возвести 2 в 8 степень

#### floor
floor (123.9) - округляет до наименьшего значения

#### round
округляет до определенного знака

#### pi
вычисляет число ПИ

### Дата и время
#### getdate
возвращает текущую дату и время
current_timestamp - аналог getdate

#### datename, datepart, day, month, year
Поступает задача выбрать сотрудников которые родились в декабре, но любого числа
используем функцию datepart

select id, lname, birthdate
from employees
where datepart(month, birthdate) = 12

первым аргументом подставляется другая функция - month, которая сигнализирует, о том, что в дате мы будем искать значения по месяцам, а второй аргумент как раз указывает какое именно число

#### datepart
парсит дату, в первом аргументе указываем какой период мы ищем, во втором аргументе число периода

#### day, month, year
возвращает день, месяц, год

#### datefromparts, datetimefromparts, timefromparts
составить дату из нескольких цифр
select datefromparts (2017,05,17),
datetimefromparts (2017, 05, 17, 04, 30, 12, 123),
timefromparts (04, 30, 12, 1234567, 7)
![[Pasted image 20240413192009.png]]

#### datediff
разница между датами
datediff (month, '20160901', '20161201')

## sys.tables
sys.tables - в базе которую создали системная таблица в которую вносятся все таблицы, которые мы создаем
![[Pasted image 20240413192950.png]]

Если в условии Not EXISTS вернется хотя бы одно значение, то BEGIN не сработает, а если не вернется, то сработает
![[Pasted image 20240413193224.png]]