Код в процедурном стиле. Скрипт может состоять из одиночной инструкции и из нескольких инструкций, которые можно делить по пакетам/бэтчам. 
С помощью процедурных основ языка можно:
- сохранять промежуточные вычисления в локальные переменные;
- выполнять инструкции непоследовательно, с помощью циклов, условных конструкций и т.д.;
- обрабатывать ошибки с помощью try catch;
# Локальные переменные
Локальная переменная - объект содержащий некоторое значение определенного типа, хранится в оперативной памяти, доступен во время выполнения скрипта.

## Объявить переменную
declare  @название_переменной тип_переменной

## Присвоить переменной значения
### при объявлении
declare @age smallint = 2;
### с помощью инструкции SET
SET @name = 'John';
### c помощью SELECT
SELECT @age = 5;

Set и Select присваивают значение переменной, но не объявляют.
## Примеры
declare @name varchar (10);
-- в данной ситуации у нас объявлена пустая переменная. Мы можем проверить, что переменная пустая через функцию NULL:
print isnull (@name, 'null')

### Разные пакеты
declare @name varchar (10);
Set @name = 'John';
print @name
go
-- пакет закончился на команде go
set @name = 'John2';
print @name
-- два разных пакета, две разных переменных

Select @name = Lname from grandslamdb.dbo.players where id = 3

Select @name = Lname from grandslamdb.dbo.players
в данном случае будет присвоено последние из значений в столбце

## Вычисляемое присвоение
В случае если нам надо присвоить вычисляемое значение, то оформляем SELECT в скобки - через подзапрос, иначе он нам может выдать муть, а в этом варианте подстраховочно может выдать NULL:


SELECT @name = (SELECT LName FROM GrandSlamDB.dbo.Players 
							WHERE Id = 3)
PRINT @name
# Инструкции управления потоком
## ![[Pasted image 20240907184951.png]]BEGIN END
Проблематика:

IF @number >= 18
	PRINT('number is equal to or greater than 18')
ELSE
	PRINT(@number)
	PRINT('number is less than 18')
	-- последний PRINT не относится к конструкции IF ELSE, он самостоятелен. Чтобы от этого избавиться, как раз используют конструкции разграничители BEGIN ... END:
	
IF @number >= 18
	PRINT('number is equal to or greater than 18')
ELSE
BEGIN
	PRINT(@number)
	PRINT('number is less than 18')
END

## IF ... EXISTS/ NOT EXISTS
IF EXISTS (select 1 from sys.tables where object_id =  object_id ('courts'))
-- если запрос вернет TRUE
begin
print 'table courts already exists'
end

![[Pasted image 20240907191555.png]]

Пример с блоком Catch показывающем декомпозированную степень серьезности ошибки
![[Pasted image 20240907191834.png]]