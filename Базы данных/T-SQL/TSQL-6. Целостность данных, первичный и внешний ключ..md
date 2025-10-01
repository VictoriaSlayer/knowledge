## Целостность данных
Целостность данных [[БД от ВК 1. Введение]] - непротиворечивость хранящейся в БД информации, соответствие внутренней логике, структуре и всем явно заданным правилам.
Возраст не может быть отрицательным;
В таблице продаж не может быть продукта которого не существует;
Двум сотрудникам не может быть присвоен одинаковый табельный номер;

Целостность данных обеспечивается с помощью ограничений(constraint) и триггеров.

### Категории целостности данных

#### Доменная целостность (domain integrity)

*Тип данных* - когда мы задаем определенному столбцу тип данных мы уже задаем ограничения для столбца.
*Домен* - множество допустимых значений столбца. С помощью типов данных, CHECK, NOT NULL, FOREIGN KEY, DEFAULT мы можем обеспечить доменную целостность. 
*Check* - доп. ограничения которые должны выполниться, чтобы данные считались валидными и были занесены в этот столбец.
*Not null* - возможность заносить null значения.
*Foreign key* - ограничение относится к ссылочной целостности 
*Default* - значение в столбце по умолчанию, если нет никаких данных

##### Примеры

if object_id ('employees') is not null
drop table employees
-- проверка существования указанного объекта

create table employees(
	id int identity not null,
	fname varchar(50),
	lname varchar(50),
	phone varchar(15) constraint CK_Employees_phone check(phone like '([0-9][0-9][0-9]) [0-9][0-9][0-9]-[0-9][0-9]-[0-9][0-9]'),
	salary decimal (10,4),
	bonus decimal (10,4),
	sex varchar (6) check (sex in ('male', 'female', 'm', 'f')),
	-- если будут пытаться вводить какие либо другие поля, то будет выдавать ошибку
	StartDate date constraint DF_emplyees_StartDate default getdate()
	-- default - значение по умолчанию, 
	-- однако при указанном default НЕОБХОДИМО указывать порядок следования столбцов, чтобы не выбивало ошибку
	)

-- alter table employees
-- drop constraint CK__employees__sex__7849DB76


-- Constraint - название ограничения, формат названия ограничение_таблица_столбец
Ограничения существуют различных типов, они могут относиться как к доменной целостности, так и к целостности сущностей, наверняка и ко многим другим (просто автор не упоминает)
--check - условие для ограничения

insert into employees
values
(
'alex', 'po', '(000) 100-20-30', 1000, 2000
)
можно вставлять данные только в таком формате

##### Добавить новое ограничение
Alter table employees
with nocheck
-- no check - отключает проверку по уже существующим данным в таблице, иначе может выдавать ошибку
add constraint ck_employees_bonus 
check (bonus <= salary * 0.1)

В таблицу можно добавлять несколько ограничений Check,  Unique и т.д.

##### Удалить ограничение
alter table employees
drop constraint CK__employees__sex__7849DB76

необходимо указывать название ограничения которое находится в папке Constraints

##### Дефолт ограничение для уже существующих столбцов
alter table employees
with nocheck
add constraint df_employees_defaultBonus default 0 for bonus
-- добавили ограничение - дефолтное значение по умолчанию для уже существующих столбцов
-- дефолтные ограничения отменить нельзя

#### Целостность сущностей (entity integrity)
Определяет строку как уникальную сущность в конкретной таблице. 
Primary Key, Unique - задают правило, что в столбце не может быть повторяющихся данных, двух одинаковых сущностей.
Unique - как правило используется для ID.

##### Primary key
Ограничение первичного ключа - поле или набор полей(составной primary key), которые уникально идентифицируют каждую запись в таблице.
Primary key - один на таблицу. Однако он может быть составным - несколько столбцов для него.
В столбце не должно быть повторяющихся данных.
Столбец не может принимать NULL значения.
В таблице не может быть нескольких Primary Key значений.
Отключить Primary Key и Unique нельзя, т.к. если отключить их и вставить дублирующие данные, и потом включить, то нарушится сама логика этих ограничений, ограничения можно только удалить.
![[Pasted image 20240623000758.png]]

unique - поле помеченное этим словом не может принимать дублирующиеся данные.

###### Примеры

Единичный Primary Key
CREATE TABLE employees
(
    id int not null constraint PK_employees_id primary key,
    fname varchar(50),
	lname varchar(50),
	phone char(15)
);

Множественные ограничения записывают как правило в конце таблицы
Множественный primary key
CREATE TABLE employees
(
    id int not null,
    fname varchar(50) not null,
	lname varchar(50) not null,
	phone char(15),
	constraint pk_employees_FName_LName Primary key (FName, LName)
	-- при составном primary key столбцы не могут принимать null значения, вставить в таблицу двух сотрудников с одинаковыми именем фамилией мы не сможем
);

##### Unique
Unique - как правило используется для ID.
Unique может быть несколько (по аналогии с Primary Key), а Primary Key один.
###### Примеры
create table employees (
id int not null,
fname varchar(50),
lname varchar(50),
phone varchar(15) constraint UQ_Employees_Phone unique 
--nonclustered (что это значит я хз) ограничение unique видим в папке keys
)

###### Кластеры
Clustered, Nonclustered - относится к ключам/индексам и необходимы когда БД начинает разрастаться и там появляется большое количество данных. Clustered, nonclustered влияет на быстроту получения данных из таблицы.
Primary key - clustered
Unique key - nonclustered

https://habr.com/ru/articles/141767/
https://learn.microsoft.com/ru-ru/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?view=sql-server-ver16
##### Foreign key
Используется для принудительного установления связи между данными двух таблиц.
Ограничение Foreign Key должно быть связано с ограничениями Primary Key или Unique.

Грубо говоря в значения foreign key могут попадать только те значения, которые уже существуют в primary key. Иначе будет выбивать ошибку, т.к. foreign key жестко завязано на primary key. 

#### Ссылочная целостность (Referential integrity)
Сохраняет определенные связи между таблицами при добавлении или удалении строк. Гарантирует согласованность значений ключей во всех таблицах. 
Foreign Key.

![[Pasted image 20240627055810.png]]
Особенности с удалением таблиц и столбцов
Нельзя удалить таблицу на которую есть ограничения foreign key из другой таблицы. Исходя из картинки мы можем удалить таблицу Orders, но не можем удалить таблицу Employees. Удалив Orders удалятся все ограничения Foreign key и потом можно удалить будет таблицу Employees.
Также нельзя удалить в таблице столбцы, на которых стоят ограничения.
##### Пример №1
if OBJECT_ID('employees') is not null
drop table employees

CREATE TABLE dbo.employees
(
	id int not null identity primary key,
	fname varchar(50) not null,
	lname varchar(50) not null,
	phone char(15)
);

if OBJECT_ID('orders') is not null
drop table orders

CREATE TABLE dbo.orders
(
	id int not null identity primary key
	,[date] date -- default getdate()
	,totalsum decimal (10,4) --default(0)
	,employeeId int Constraint FK_Orders_Employees Foreign Key
	References Employees(Id)
	-- Foreign Key может ссылаться только на поле Primary Key или Unique
	-- В этом весь смысл иначе мы можем ссылаться на несколько записей, что тогда ломает целостность данных
	-- в данном виде условия - мы не сможем удалять записи из таблицы Employees, т.к. они будут в таблице Orders

	-- On Delete No Action также значения по умолчанию которые явно не прописаны
	-- On Update No Action также значения по умолчанию которые явно не прописаны
);

insert employees values
('Robert', 'Wu', '(000) 100-20-30')
,('Sarah', 'Lee', '(001) 100-20-30')
,('Mary', 'Wee', '(002) 100-20-30')

insert orders(employeeId)
values(4) -- мы не можем вставить ID 4, т.к. в employees всего 3 сотрудника
-- также в данном случае в orders в id таблица начнется со 2-го id, т.к. identity перескочил, когда мы не смогли вставлять
![[Pasted image 20240627064119.png]]

insert orders(employeeId)
values(1), (3), (2)  -- мы можем вставить 1-е значение, т.к. в employees есть первый айдишник

##### Пример №2 отключение ограничения через NoCheck
alter table orders
nocheck constraint fk_orders_employees
-- теперь можем добавлять значения без проверки связи
-- ключ не удаляли, а просто отключили на время

alter table orders
check constraint fk_orders_employees

##### Пример №3 добавляем заново удаленное ограничение NoCheck
alter table orders
with nocheck
add constraint FK_Orders_Employees Foreign Key (employeeid)
--указываем столбец которому задаем ограничение
references employees(id)
--указываем столбец на который ссылаемся в качестве primary key


#### Удаление данных в таблице
В предыдущих примерах манипулировали со структурой таблицы. Однако в большинстве своем необходимо манипулировать данными в самой таблице. Команды On delete no action, on update no action как раз за это отвечают.

#### Типы связей между таблицами
![[Pasted image 20240628235542.png]]
Ранее было расмотрено 1 ко многим. Чтобы обеспечить связь 1 к 1 необходимо обеспечивать Primary Key или Unique.
Принято считать, что для каждой таблицы должно быть свое поле ID.
##### Пример 1 к 1
-- Создаем связь 1 к 1
CREATE TABLE dbo.Products
(
    Id int NOT NULL Identity primary key,
	name varchar(30)
);
go

CREATE TABLE dbo.ProductDescr
(
    Id int Unique foreign key references products(id),
	-- Вариант №1 Id повешена на поле, которое хранит уникальные значения
	color varchar(30),
	[weight] decimal(10,4)
);
go

CREATE TABLE dbo.Stock
(
    Id int NOT NULL identity primary key,
    qty int,
	ProductId int unique foreign key references Products(id)
	-- Вариант №2 ProductId повешена на поле, которое хранит уникальные значения, foreignKey, т.к. PK может быть только один
);
go

![[Pasted image 20240629183028.png]]
Более подробно про все типы связей после 60 минуты.
##### Self Referencing
Self Referencing - в таблице данные ссылаются сами на себя. Например подчиненные ссылаются на начальника и т.д.

-- Self Referencing
alter table employees
add employeeid int foreign key references employees(id)
go

##### Связи таблиц созданные за урок
![[Pasted image 20240629184717.png]]

##### Как удалять строки из таблиц, если они связаны и есть внешние ключи?
Как удалить сотрудника из таблицы Employees если эти сотрудники есть в таблице Orders и связаны Foreign Key.
По умолчанию будет выдавать ошибку, т.к. есть ограничение внешнего ключа и по дефолту стоит on delete:no action, on update:no action

Помимо no action есть 3 настройки для on delete, on update:
- set default - Пример создали таблицу с on delete no action по умолчанию. Потом решили изменить ограничение на Set Default. После того, как мы поудаляем часть значений, в другой таблице, которая была связана с нашей через Foreign Key, в столбце который был связан появятся либо Default значения которые мы выбрали по умолчанию (конкретно пример не привел), либо Null значения; ![[Pasted image 20240629190426.png]] 
- set null - будут null значения;
- cascade - каскадное удаление, при удалении записи из родительской таблицы, удаляется все записи из дочерней таблицы;
Как правило используют set default, set null, cascade, а не значение по умолчанию.

#### Пользовательские ограничения (User-defined integrity)
Обеспечиваются за счет триггеров. 