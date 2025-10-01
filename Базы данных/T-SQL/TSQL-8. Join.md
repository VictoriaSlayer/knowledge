# Join
С помощью инструкции джоин можно собрать данные из нескольких таблиц в одну.  Данные по типу должны совпадать. Как правило маппят по Primary Key, Foreign Key.
![[Pasted image 20240804114122.png]]
Всю информацию можно было бы хранить в одной таблице, но это привело бы к избыточности данных, аномалии обновления и т.д.
## Inner Join
![[Pasted image 20240804114259.png]]
С помощью иннер джоин можно сделать выборку - выбираем из таблицы авторов и соединяем с таблицей книг и указываем по какому полю делаем соединение.
Будут соединены только поля имеющие общие значения.

select * from authors a
join books b
-- a, b псевдонимы для таблиц
on a.id = b.AuthorId
-- on команда по которой значения столбцов маппить друг с другом

## ФОРМУЛА
select (перечисление столбцов) 
from (название таблицы 1)
join (название таблицы 2)
on
(таблица 1.столбец 1 = таблица 2.столбец 2)
## Outer Join
Бывает 3 вида соединений:
- Left outer join
- Right outer join
- Full outer join

Left/Right показывает по какой таблице мы идем и по какой таблице мы будем маппить данные.
### Left outer join
![[Pasted image 20240804120957.png]]

Ключевое слово Outer можно опускать
Выберем все данные из первой таблицы и подставим все значения из второй таблицы, а для тех столбцов, где мы не найдем значения, подставится NULL.
Теперь мы видим всех авторов, даже тех у кого нет книг.

Если поменять таблицы местами
![[Pasted image 20240804122742.png]]
select * from books b
left outer join authors a
on a.id = b.AuthorId

### Right Outer Join
![[Pasted image 20240804123043.png]]
Select * from books b
right join authors a
on a.id = b.authorId

Порядок столбцов можем указать через звездочку - *
### Соединить только NULL значения
Select * from authors AS a
left join books AS b
on a.id = b.authorid
where b.authorid is null
![[Pasted image 20240804125133.png]]

### Full outer join
![[Pasted image 20240804134754.png]]
Full join = Left join + Right join
Попадают все записи из всех таблиц.
По сути в Full join показывает информацию если бы все данные хранили бы в одной таблице.
Когда мы делаем full join мы идем по первой таблице, находим соответствия со второй таблицей, подставляем NULL где не находим совпадений. Идем по второй таблице, там где не нашли соответствия, подставляем NULL.

Select * from authors a
full join books b
on a.id = b.authorid

### Cross Join
Для каждой записи из первой таблицы подставить записи из второй таблицы - иначе перекрестное объединение/Декартово соединение.
![[Pasted image 20240806224021.png]]

аналог cross Join - select * from players, playerinfos
### Theta Join
Соединение по условию
Select * from authors a
join books b
on a.name > b.title
### Self Join
Объединение таблицы с самой собой
Select * from Authors a1
Join Authors a2
on a1.id = a2.id

  select * from authors a1
  join authors a2
  on a1.name = a2.name

# Практика
select p.rank, p.fname + ' ' + p.lname Name, pli.BirthDate, pli.Weight, pli.height
-- указываем порядок столбцов
-- p.fname + ' ' + p.lname Name - псевдоним для столбца с названием Name
from players p
join playerinfos pli
on p.id = pli.id

order by rank

![[Pasted image 20240811110602.png]]

## Вывод определенных столбцов
  select c.name, m.time, p.fname + ' ' + p.lname player1, ps.aces from matches m
    -- определяем какой порядок столбцов выводить из какой таблицы;
  -- не исключено, что в будущем еще в какой-то таблице появится столбец Name
  -- поэтому мы выводим название столбца через псевдоним;
  join PlayerStats ps
  on m.id = ps.matchid and ps.win = 1
  join players p
  on p.id = ps.id
  join courts c
  on c.id = m.courtid

## Конкатенация двух столбцов в одном
p.fname + ' ' + p.lname player1
-- вывести инфу из p.fname и p.lname под псевдонимом столбца Player 1

## Псевдоним таблицы
(
select c.name, m.time, p.fname + ' ' + p.lname player1, ps.aces from matches m
  join PlayerStats ps
  on m.id = ps.matchid and ps.win = 1
  join players p
  on p.id = ps.id
  join courts c
  on c.id = m.courtid
  ) winner
  -- обернув все в скобки и написав рядом Victoria мы создали псевдоним таблицы под названием winner с которым далее можем работать


## Сложный Join
SELECT Name, Date, Time, Winner, wA Aces, Loser, lA Aces FROM
(SELECT m.Id, ps.Aces wA, c.Name, m.Date, m.Time, p.FName + ' ' + p.LName Winner  
FROM Matches m
		JOIN PlayerStats ps
			ON m.Id = ps.MatchId AND ps.Win = 1
		JOIN Players p 
			ON p.Id = ps.PlayerId
		JOIN Courts c ON c.Id = m.CourtId) winner 
JOIN
(SELECT m.Id, ps.Aces lA, p.FName + ' ' + p.LName Loser  
FROM Matches m
		JOIN PlayerStats ps
			ON m.Id = ps.MatchId AND ps.Win = 0
		JOIN Players p 
			ON p.Id = ps.PlayerId) loser
ON winner.Id = loser.Id

