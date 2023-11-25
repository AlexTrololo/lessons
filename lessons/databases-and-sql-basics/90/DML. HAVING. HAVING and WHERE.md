DML. HAVING. HAVING и WHERE – Telegraph

DML. HAVING. HAVING и WHERE
===========================

[Дорогу осилит идущий](https://t.me/ViamSupervadetVadens)July 16, 2023

DML. HAVING. HAVING и WHERE

==============================

[Дорогу осилит идущий](https://t.me/ViamSupervadetVadens)

В рамках сегодняшнего урока мы завершим тему агрегации данных в SQL, рассмотрев механизм ограничения уже агрегированной выборки.

Суть довольно проста: когда мы агрегируем данные с группировкой по каким-то признакам, вполне вероятна ситуация, в которой нас интересует только часть групп.

Довольно наглядным будет пример с поиском дубликатов.

Скажем, мы хотим узнать, есть ли в нашем списке пассажиров тезки и если есть – какие у них имена. По сути, для этого достаточно посчитать, сколько носителей каждого из имен присутствует в таблице:

select count(\*), first\_name from passenger group by first\_name;

Но в результате будут не только имена тезок, но и имена, которые имеют лишь по одному носителю. Соответственно, нам нужна фильтрация результатов.

WHERE-условие для этой цели не подойдет – как выяснили в предыдущем уроке, _WHERE_ отрабатывает до агрегации данных, а нам нужна фильтрация самих результатов агрегации.

Для подобных целей существует оператор **_HAVING_**:

select count(\*), first\_name from passenger group by first\_name having count(\*) > 1;

Вуаля! В итоговой выборке присутствуют лишь имена с более чем двумя носителями.

Обычно этого достаточно, но мы можем пойти дальше – нам не требуется получать число носителей каждого имени, достаточно обычного перечисления этих имен:

select first\_name from passenger group by first\_name having count(\*) > 1;

Как видите, агрегатная функция исчезла из выражений _SELECT_. Но секция _GROUP BY_ остается – нам все еще требуется группировка для агрегатной функции в _HAVING_.

Также в _SELECT_ и _HAVING_ могут использоваться разные агрегатные функции – скажем, агрегация через _count()_ в _SELECT_ и ограничение по _max()_ в _HAVING_. Единственное ощутимое ограничение – агрегатная функция не может быть вызвана внутри другой агрегатной функции. Т.е. записать в _HAVING_ что-то вроде _max(count(\*))_ НЕ получится.

Еще один момент, который стоит помнить при работе с блоком _HAVING_ – все, что можно вынести из _HAVING_ в _WHERE_ – должно быть вынесено в _WHERE_. Из-за того, что _HAVING_ работает с результатами агрегации, расположение ограничений в этом блоке делает более тяжелым запрос целиком – ведь приходится агрегировать больший массив данных, чем был бы, перенеся мы HAVING-условия в _WHERE_.

Скажем, если вы хотите исключить из запроса выше всех Иванов – стоит добавить _first\_name != 'Ivan'_ в _WHERE_. Ведь нам нет необходимости знать число Иванов, чтобы исключить из выборки, следовательно, нет необходимости агрегировать Иванов.

И, по традиции, пример с взаимным расположением блоков в запросе:

select count(\*), male from passenger where last\_purchase is not null group by male having count(\*)> 1 order by male limit 1 offset 0;

С теорией на сегодня все!

![](/file/6d1b55deb7a663f5480c0.png)

Переходим к практике:

### Задача

Получите число пассажиров по длине имени. Нас интересуют только короткие имена (меньше 6 символов). Если пассажиров с заданной длиной имени меньше, чем два – игнорируйте их в результирующей выборке. Отсортируйте данные по количеству пассажиров.



Если что-то непонятно или не получается – welcome в комменты к посту или в лс:)

Канал: [https://t.me/ViamSupervadetVadens](https://t.me/ViamSupervadetVadens)

Мой тг: [https://t.me/ironicMotherfucker](https://t.me/ironicMotherfucker)

_Дорогу осилит идущий!_

EditPublish

Report content on this page

Report Page
-----------

Violence Child Abuse  Copyright  Illegal Drugs  Personal Details  Other

Please submit your DMCA takedown request to [\[email protected\]](/cdn-cgi/l/email-protection#e6828b8587a692838a838194878bc8899481d99593848c838592dbb48396899492c3d4d69289c3d4d6b2838a83819487968ec3d4d696878183c3d4d6c3d4d4a2abaac8c3d4d6aea7b0afa8a1c8c3d4d6aea7b0afa8a1c3d4d6c3a2d6c3a4dec3d4d6b1aea3b4a3c3d4d4c08489829fdbb483968994928382c3d4d696878183c3d5a7c3d4d68e92929695c3d5a7c3d4a0c3d4a092838a83819487c8968ec3d4a0a2abaacbaea7b0afa8a1cbaea7b0afa8a1cb8fcbb1aea3b4a3cbd6d1cbd7d0c3d6a7c3d6a7c3d6a7)

Cancel Report

var T={"apiUrl":"https:\\/\\/edit.telegra.ph","datetime":1689528930,"pageId":"4b904a765de46c01de18e","editable":true};(function(){var b=document.querySelector('time');if(b&&T.datetime){var a=new Date(1E3\*T.datetime),d='January February March April May June July August September October November December'.split(' ')\[a.getMonth()\],c=a.getDate();b.innerText=d+' '+(10>c?'0':'')+c+', '+a.getFullYear()}})();