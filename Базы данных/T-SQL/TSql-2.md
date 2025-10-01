DDL - data definition language. Создание, изменение, удаление БД, таблицы. Identity.
DML - data manipulation language. Select, Insert, Update, Delete. Обработка данных.
Output.

DDL - язык описания данных. Предназначен для создания, удаления, изменения новых сущностей.

![[Pasted image 20240103131205.png]]

Create - создать новые сущности.
Alter - изменение определений существующих сущностей.
Drop - удаление существующих сущностей.

Create Table
Create Trigger
Create Procedure
Create View
Create Database
И т.д.

### DML сущности
Select - выборка инфы из одной или нескольких таблиц БД.
Insert - добавляет одну или несколько строк в таблицу или представление.
Update - изменяет существующие данные в таблице или представлении.
Delete - удаляет одну или несколько строк.
Bulk insert
Merge

### DDL создание
Create DATABASE Test - создать БД.
После создания БД всегда необходимо обновить окно, чтобы новая БД отображалась.
![[Pasted image 20240103133324.png]]

![[Pasted image 20240103133438.png]]

##### Для изменения сортировки БД по имени Test необходима следующая команда DDL:
Код:
Alter [объект изменения]  [имя объекта]  
[параметры изменений]
Пример:
Alter Database Test
Collate Cyrillic_general_ci_as

##### Удалить БД
Drop Database Test

##### Создание сущности в БД
Для создания сущности в БД необходимо перейти в саму БД, т.к. по умолчанию мы находимся в БД по имени Master
![[Pasted image 20240103134251.png]]
Сменить БД можно через выпадающий список или через команду Use:
Use Test

##### Создать таблицу
Create Table Students
Создать сниппет Ctrl + K + X

CREATE TABLE dbo.Sample_Table
(
    column_1 int NOT NULL Identity (1, 1),
    column_2 int NULL
);

Identity - показывает, что значения этого столбца будут генерироваться автоматически с 1-го значения с шагом в 1.
Null - показывает что мы допускаем Null значения. По умолчанию таблица допускает возможность указывать Null значения.

CREATE TABLE Students
(
    Id int NOT NULL Identity (1, 1),
    FirstName nvarchar(20) NULL,
	LastName nvarchar(20),
	Phone char(12),
	Email nvarchar(20)
);

приставка dbo в начале - название схемы data base owner, таким образом для больших бд можно устраивать структуру. Для чего это нужно непонятно.
###### Специфические названия столбцов
CREATE TABLE dbo.Sample_Table
(
    [имя_столбца в квадратных скобках] int NOT NULL Identity (1, 1),
    column_2 int NULL
);

##### Изменить таблицу

Alter [Тип сущности]  [Название сущности]
Alter Table Students

Изменить столбец Lname - увеличить длину символов до 30, пусть не поддерживает Null значения

Alter Table Students
Alter Column Lname nvarchar (30) Not Null

За раз можно поменять только один столбец!!!
##### Добавить столбец
Alter table Students
Add MiddleName nvarchar(30)

Колонку добавляем без слова Column!!!

##### Переименовать столбец
Use Имя_Бд
Exec sp_rename 'имя_таблицы.имя_колонки', 'новое_имя_колонки', 'COLUMN'
##### Удалить колонку
Alter Table Students
Drop Column MiddleName

При помощи подсказки Column мы подсказываем какую именно сущность мы удаляем.

# DML

#### Insert
##### Вставить строку
Вставить в таблицу Название_Таблицы
Перечисление Колонок // - это можно пропустить, тогда значения будут вставляться в столбцы по порядку, но это нежелательно
Значения
('значение 1', 'значение 2')

Мы не можем перечислять Колонки у которых стоит параметр Identity иначе будет ошибка!!!

Insert into Students
(FirstName, LastName, Phone, Email) 
Values
(N'Александр', N'Македонский', '(012)3456789', 'alex@mac.ru'),
(N'Диоген', N'Синопский', Null, 'alex@mac.ru'),

N добавляем в начале т.к. добавляем значения на русском языке

##### Выбрать первую тысячу строк
SELECT TOP (1000) [Id]
      ,[FirstName]
      ,[LastName]
      ,[Phone]
      ,[Email]
  FROM [Test].[dbo].[Students] - полный путь к таблице, когда мы находимся в определенной БД можно полный путь не указывать.

Все поля выделены квадратными скобками - это означает, что данный текст будет интерпретироваться как текст, а не зарезервированное слово.

##### Добавление значения в Айдишную Колонку
Для этого необходимо включить параметр Identity_Insert у колонки, т.к. по умолчанию он выключен и не позволяет этого делать:
Set Identity_insert Students On

Затем вставляем ряд
Insert into Students
(Id, FirstName, LastName, Phone, Email)
Values
(3, '', 'Сиракуза', '(012)3456789', 'assir@mac.ru')

И выключаем параметр Identity_insert:
Set Identity_insert Students Off

##### Вставить в столбец несколько значений
insert orders(employeeId)
values(1), (3), (2)
#### Select
Служит для выборки данных

Select * from students - выбрать все строки

Выбрать определенный набор столбцов
Select FirstName, LastName from Students

##### фильтр Where
Select FirstName, LastName from Students
Where id = 1

##### Вставить данные в одну таблицу из другой
Insert StudentPhones
	Select Id, LastName, Phone from Students 


#### Update
##### Изменение уже существующих данных
Изменение значений полей и т.д.

Изменить таблицу Студенты, поменять значение для колонки Phone на Null, где айдишник равен одному.

Update Students
	Set Phone = Null
	Where id = 1

Если не указывать фильтр Where, то обработаются все строки

Update Students
	Set Phone = Null

##### Удалить строку
Удалить строку где айдишник равен единице

Delete Students
	Where Id = 1

Удалить все строки
Delete Students

Truncate - более быстрая версия Delete без предложения Where. Эта инструкция удаляет все строки таблицы более быстро чем Delete, т.к. удаляет содержимое постранично, когда Delete удаляет построчно. 
Truncate Table - расширение Tsql. Также она сбрасывает индекс столбца, для которого указано Identity. То есть если мы удалим строки с айдишниками 5, 6 через Delete то след добавленные строки будут иметь айдишники 7, 8. Truncate же их сбросит.

##### Output
Дает возможность получить информацию по строкам. которые были добавлены, удалены или изменены в результате выполнения DML команд Insert, Delete, Merge, Update. Результаты выполненных операций соответствующих инструкций Output выводит в таблицах inserted и deleted.

Insert Students (FirstName, LastName, Phone, Email)
	Output inserted.* - выводим данные в таблице Inserted
	values
	('test First','test Second','123456','Test Email'),
	('test Third','test Fourth','123456','Test Email'),
	('test Fifth','test Sixth','123456','Test Email')

Inserted - таблица в которую попадают новые данные при вставке.

Deleted - таблица в которую попадают данные при удалении.

Delete Students
Output Deleted.id, Deleted.LastName - вывести столбец с айдишником 4 и связанный с ним столбец LastName
where Id = 4

##### Показать добавленные и удаленные значения с псевдонимами

Update Students
Set Phone = '(012)3456789'
Output inserted.id, Inserted.FirstName, Inserted.LastName, Inserted.Phone as [Новый телефон], 
	deleted.phone as "Удаленный телефон"
Where Id = 5

![[Pasted image 20240106233438.png]]
Inserted - колонка Новый телефон
Deleted - колонка Удаленный Телефон

##### Перенести удаленные данные из одной таблицы в другую
Их можно сохранить в таблицу и использовать в другой

Delete Students - удалить таблицу
Output deleted.id, deleted.LastName, deleted.Phone into StudentPhones - перенести данные в другую таблицу

При этом количество перенесенных столбцов по количеству должно совпадать.

#### Вопросы
Вопросы - как обойти ограничение по названию колонки в Mssql если слово используется в качестве команды?