DML. INSERT и UPDATE – Telegraph

DML. INSERT и UPDATE
====================

[Дорогу осилит идущий](https://t.me/ViamSupervadetVadens)July 22, 2023

DML. INSERT и UPDATE

=======================

[Дорогу осилит идущий](https://t.me/ViamSupervadetVadens)

На несколько уроков отвлечемся от извлечения данных и познакомимся с другими, не менее важными возможностями DML: вставка и обновление (в этом уроке), а также удаление данных из таблицы (в следующем).

### Оператор INSERT

Казалось бы, со вставкой данных в таблицу мы уже знакомились в рамках Урока 84 ([https://telegra.ph/DML-INSERT-07-02](/DML-INSERT-07-02)).

Но теперь, умея использовать подзапросы, мы можем использовать новый для нас способ – вставка данных на базе данных из другой таблицы.

Рассмотрим ситуацию, когда в нашей системе потребовалось ввести разделение пассажиров по половому признаку и хранить пассажиров каждого пола по отдельности. Почему такая потребность возникла – история умалчивает.

Итак, создадим две новые таблицы (к DDL мы еще не приступили, поэтому просто копируем запросы):

create table passenger\_male (
id                   bigserial,
first\_name           varchar(100),
last\_name            varchar(100),
birth\_date           date,
last\_purchase        timestamp,
favorite\_airports    text\[\],
created              timestamp      default now()
);

create table passenger\_female (
id                   bigserial,
first\_name           varchar(100),
last\_name            varchar(100),
birth\_date           date,
last\_purchase        timestamp,
favorite\_airports    text\[\],
created              timestamp      default now()
);

Вы можете заметить, что исчезла колонка, отвечающая за пол – она теперь избыточна. Зато добавилась колонка _created_ – для обозначения даты создания записи.

Таблицы есть, осталось их наполнить. Поскольку переносить данные в ручном режиме – задача трудоемкая и неблагодарная, сделаем это в автоматическом режиме (на примере _passenger\_male_):

insert into passenger\_male
(id, first\_name, last\_name, birth\_date, last\_purchase,
favorite\_airports)
select id, first\_name, last\_name, birth\_date, last\_purchase,
favorite\_airports
from passenger where male;

И вот таблица _passenger\_male_ уже заполнения пассажирами-мужчинами.

Разумеется, мы не обязаны переносить все в первозданном виде. В _SELECT_ могут быть любые выражения, которые будут лишь частично использовать значения записей из оригинальной таблицы пассажиров, если это необходимо. В данном случае SELECT-запрос не будет иметь каких-либо ограничений, кроме того, что набор колонок по количеству и типу данных должен совпадать с указанным в _INSERT_.

К слову, без явного указания колонок в _INSERT_ тоже можно, но, тогда придется явно указывать время создания записи (значение для новой колонки). Заодно в примере ниже приведем все имена и фамилии к верхнему регистру (таблицу лучше пересоздать перед выполнением примера ниже. Или очистить, если сможете):

insert into passenger\_male
select id, upper(first\_name), upper(last\_name), birth\_date,
last\_purchase, favorite\_airports, now()
from passenger where male;

### Оператор UPDATE

Кроме вставки новых значений вполне вероятны ситуации, когда требуется обновить уже существующие значения – как правило, установить новые значения для ряда колонок всем записям (или части записей) в таблице. Для этих целей и существует оператор **_UPDATE_**.

С особенностями использования разберемся на базе примеров.

update passenger set first\_name = upper(first\_name);

Запрос на обновление начинается с оператора _UPDATE_, после которого указывается название таблицы, записи которой будут обновлены. Далее используется оператор **_SET_**, за которым следуют обновляемые колонки и присваиваемые им значения.

В данном случае мы обновляем все записи таблицы, присваивая им текущее значение колонки, но переведенное в верхний регистр.

Также может быть использован и какой-то заданный литерал:

update passenger set first\_name = 'sthName';

Или значение другой колонки (или какое-то иное выражение):

update passenger set first\_name = concat(first\_name, ' ', last\_name);

Все то же самое актуально и для обновления нескольких колонок за раз:

update passenger set
first\_name = upper(first\_name),
last\_name = upper(last\_name);

Но большинство задач, все же, не подразумевают обновления всех записей таблицы, требуя обновить лишь часть, отфильтрованную по каким-то признакам:

update passenger set last\_name = 'Ivanoff'
where lower(last\_name) = 'Ivanov';

В данном случае, всем пассажирам с фамилией '_ivanov_' (записанной в любом регистре) будет установлена фамилия '_Ivanoff_'.

WHERE-условие будет общим для всех обновляемых колонок, если их несколько.

Так же могут быть использованы подзапросы (как в SET-, так и в WHERE- блоках):

update passenger set birth\_date = (
select min (birth\_date) from passenger
)
where male;

update passenger set last\_name = 'Ivanoff'
where lower(last\_name) = 'ivanov'
and birth\_date = (select min(birth\_date) from passenger);



Как видите, синтаксически в операции обновления нет особенных сложностей – в целом, все действия достаточно логичны и имеют массу общих элементов с уже изученными SELECT-запросами. Это не отменяет того, что отдельные операции обновления могут стать целым квестом, но это зависит от конкретной БД, задачи и навыков исполнителя.

С теорией на сегодня все!

![](/file/59a67801c88aff16b15de.png)

Переходим к практике:

> Напоминаю, что практика в рамках данных статей остается формальной. Для закрепления навыков работы с SQL рекомендую использовать специализированные сервисы или любые иные источники задач.

### Задача 1

Заполните таблицу _passenger\_female_ пассажирами-женщинами из таблицы _passenger_.

### Задача 2

Обновите все записи в таблице _passenger\_female_, установив им датой создания – _01.01.2023_.

### Задача 3

Смените имя всем пассажирам-женщинам с _Katerina_ на _Ekaterina_. Записи с другим значением имени оставьте без изменений.



Если что-то непонятно или не получается – welcome в комменты к посту или в лс:)

Канал: [https://t.me/ViamSupervadetVadens](https://t.me/ViamSupervadetVadens)

Мой тг: [https://t.me/ironicMotherfucker](https://t.me/ironicMotherfucker)

_Дорогу осилит идущий!_

EditPublish

Report content on this page

Report Page
-----------

Violence Child Abuse  Copyright  Illegal Drugs  Personal Details  Other

Please submit your DMCA takedown request to [\[email protected\]](/cdn-cgi/l/email-protection#96f2fbf5f7d6e2f3faf3f1e4f7fbb8f9e4f1a9e5e3f4fcf3f5e2abc4f3e6f9e4e2b3a4a6e2f9b3a4a6c2f3faf3f1e4f7e6feb3a4a6e6f7f1f3b3a4a6b3a4a4d2dbdab8b3a4a6dfd8c5d3c4c2b3a4a6b3d2a6b3d4aeb3a4a6c3c6d2d7c2d3b3a4a4b0f4f9f2efabc4f3e6f9e4e2f3f2b3a4a6e6f7f1f3b3a5d7b3a4a6fee2e2e6e5b3a5d7b3a4d0b3a4d0e2f3faf3f1e4f7b8e6feb3a4d0d2dbdabbdfd8c5d3c4c2bbffbbc3c6d2d7c2d3bba6a1bba4a4b3a6d7b3a6d7b3a6d7)

Cancel Report

var T={"apiUrl":"https:\\/\\/edit.telegra.ph","datetime":1690042157,"pageId":"64ddfec4d5b4a23517d9c","editable":true};(function(){var b=document.querySelector('time');if(b&&T.datetime){var a=new Date(1E3\*T.datetime),d='January February March April May June July August September October November December'.split(' ')\[a.getMonth()\],c=a.getDate();b.innerText=d+' '+(10>c?'0':'')+c+', '+a.getFullYear()}})();