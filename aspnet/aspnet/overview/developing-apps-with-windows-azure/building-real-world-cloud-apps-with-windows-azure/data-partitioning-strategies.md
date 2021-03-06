---
uid: aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-partitioning-strategies
title: (Создание реальных облачных приложений в Azure) стратегии секционирования данных | Документация Майкрософт
author: MikeWasson
description: Создание реальных облачных приложений в условиях электронная книга основана на презентацию, разработанная Скоттом Гатри. Здесь объясняется, 13 шаблонов и практических рекомендаций, которые он может...
ms.author: riande
ms.date: 06/12/2014
ms.assetid: 513837a7-cfea-4568-a4e9-1f5901245d24
msc.legacyurl: /aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-partitioning-strategies
msc.type: authoredcontent
ms.openlocfilehash: 07a80767ca2def26c0252037a3b8b990abcbe1b3
ms.sourcegitcommit: 7b4e3936feacb1a8fcea7802aab3e2ea9c8af5b4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/04/2018
ms.locfileid: "48578488"
---
<a name="data-partitioning-strategies-building-real-world-cloud-apps-with-azure"></a>(Создание реальных облачных приложений в Azure) стратегии секционирования данных
====================
по [Майк Уоссон](https://github.com/MikeWasson), [Рик Андерсон]((https://twitter.com/RickAndMSFT)), [том Дайкстра](https://github.com/tdykstra)

[Загрузить решение проект](http://code.msdn.microsoft.com/Fix-It-app-for-Building-cdd80df4) или [скачайте электронную книгу](http://blogs.msdn.com/b/microsoft_press/archive/2014/07/23/free-ebook-building-cloud-apps-with-microsoft-azure.aspx)

> **Создание реальных облачных приложений в условиях Azure** электронная книга основана на презентацию, разработанная Скоттом Гатри. Здесь объясняется 13 шаблоны и рекомендации, которые помогут вам быть в успешном развертывании веб-приложений для облака. Сведения об этой серии см. в разделе [первой главы](introduction.md).


Ранее мы увидели, насколько это просто масштабировать веб-уровня облачного приложения, путем добавления и удаления веб-серверов. Но если они все встретились то же хранилище данных, узкое место приложения перемещает из интерфейсной части к серверной части, и уровень данных, сложнее всего масштабировать. В этой главе мы рассмотрим способ внесения уровень данных масштабируемой путем секционирования данных на несколько реляционных баз данных, или объединения с другими параметрами хранилища данных реляционной базы данных хранилища.

Настройка схемы секционирования лучше done заранее по той же причине упоминалось: очень сложно изменить стратегии хранения данных, после запуска приложения. Если подумать жестких заранее различные подходы, их можно не «Twitter немного» Если приложение аварийно завершает работу или выйдет из строя в течение длительного времени при реорганизации данных и код доступа к данным приложения.

## <a name="the-three-vs-of-data-storage"></a>Три Vs хранилища данных

Чтобы определить, нужно ли вам стратегии секционирования, и что он должен быть, рассмотрим три вопроса о данных:

- Volume — объем данных будет вы в конечном счете хранить? Несколько гигабайт? Несколько сотен гигабайтов? ТБ? Петабайтов?
- Скорость – что такое частота, с которой ваши данные будут расти? Это внутреннего приложения, не создают большое количество данных? Внешние приложения, которое будет нужно отправлять клиентам, изображения и видеофайлы в?
- Множество — какой тип данных, будет хранить? Реляционные и изображений, пары "ключ значение", графы социальных сетей?

Если вы считаете, что вы собираетесь много тома, скорости или различных, необходимо учитывать, какого рода схему секционирования позволит лучше всего приложения, чтобы масштабировать более эффективно по мере увеличения их объема, и чтобы не превысить узкие места.

По сути, существует три подхода к секционирования:

- Вертикальное секционирование
- Горизонтальное секционирование
- Гибридное секционирование

## <a name="vertical-partitioning"></a>Вертикальное секционирование

Вертикальная portioning аналогичен разделение таблицы по столбцам: один набор столбцов переходит в одном хранилище данных и другой набор столбцов переходит в другое хранилище данных.

Например предположим, что мое приложение хранит данные о людях, включая образы:

![Таблица данных](data-partitioning-strategies/_static/image1.png)

После представления этих данных в виде таблицы и взглянуть на разные данные, вы увидите, что три столбца, в левой части имеют строковые данные, которые можно эффективно хранить реляционной базы данных, хотя два столбца в правой части являются по сути байтовые массивы c ome из файлов изображений. Существует возможность хранения данных файлов образа в реляционной базе данных, а со многими людьми делают это, так как они не хотят сохранить данные в файловой системе. Они могут отсутствовать файловой системы, который может хранить необходимые объемы данных или не можете управлять отдельной резервной копии и восстановление системы. Этот подход работает для локальных баз данных, а также для небольших объемов данных в облачных баз данных. В локальной среде может быть проще просто оставить берем на себя все, что администратор базы данных.

Но базы данных в облаке, — относительно дорогая операция хранилища, и большое количество образов может сделать размер базы данных, выходят за установленные пределы, в которых может работать эффективно. Эти проблемы можно решить, секционирование данных по вертикали, это означает, что выбор наиболее подходящего хранилища данных для каждого столбца в таблице данных. Что может быть лучшим в этом примере — поместить строковые данные в реляционную базу данных и изображений в хранилище BLOB-объектов.

![Вертикальное секционирование таблицы данных](data-partitioning-strategies/_static/image2.png)

Хранение образов в хранилище BLOB-объектов вместо базы данных является более удобным в облаке, чем в локальной среде, так как не нужно беспокоиться о настройке файловых серверов или управление резервного копирования и восстановления данных, хранящихся вне реляционной базы данных: все выполняется автоматически службой хранилища BLOB-объектов.

Этот подход секционирования была реализована в приложении Fix It, и мы рассмотрим код для этого в [Глава хранилища BLOB-объектов](unstructured-blob-storage.md). Без этой схемы секционирования и при условии, что размер образа среднее 3 мегабайт приложения ее исправить только смогли бы хранения около 40 задач перед достижением максимальный размер базы данных 150 ГБ. После удаления образов, базы данных можно хранить в 10 раз больше задач; Вы можете гораздо больше времени, до того, как можно подумать о реализации схеме горизонтального секционирования. И как приложение масштабируется, расходы увеличиваться медленнее, так как основная часть ваши потребности в хранении поступают в обеспечивает очень экономичное хранилище BLOB-объектов.

## <a name="horizontal-partitioning-sharding"></a>Горизонтальное секционирование (сегментирование)

Горизонтальная portioning аналогичен разделение таблицы по строкам: один набор строк переходит в одном хранилище данных и другой набор строк переходит в другое хранилище данных.

Учитывая тот же набор данных, другой вариант будет хранить разные интервалы, имена клиентов в разных базах данных.

![Горизонтальное секционирование таблицы данных](data-partitioning-strategies/_static/image3.png)

Нужно быть очень осторожными при схему сегментирования, чтобы убедиться в том, что данные распределяются равномерно избежание формирование активных областей. Этот простой пример, первой букве фамилии не удовлетворить это требование, так как многие имеют фамилии, начинающиеся с определенных распространенных символов. Ограничения на размер таблицы переход раньше, чем можно ожидать, так как некоторые базы данных будет очень большие, хотя большинство будет оставаться небольшими.

Вниз части горизонтального секционирования является то, что он может быть трудно сделать запросы во всех данных. В этом примере запроса пришлось бы нарисовать из до 26 разных баз данных для получения всех данных, хранящихся в приложении.

## <a name="hybrid-partitioning"></a>Гибридное секционирование

Вы можете объединить вертикальное и горизонтальное секционирование. Например в этом примере можно хранить изображения в хранилище BLOB-объектов и горизонтально разделять строковые данные.

![Секционированные гибридного таблицы данных](data-partitioning-strategies/_static/image4.png)

## <a name="partitioning-a-production-application"></a>Секционирование в рабочем приложении

Концептуально это легко увидеть, как будет работать схемы секционирования, но любые схемы секционирования, повышает сложность кода и предоставляет множество новых неполадок, которые имеют дело с. Если вы перемещаете образов в хранилище BLOB-объектов, что делать, если служба хранилища не работает? Порядок обработки большого двоичного объекта безопасности? Что произойдет, если база данных и больших двоичных объектов хранилища стать асинхронными? Если вы сегментирования, как будут обрабатываться запросы в пределах всех баз данных?

Осложнений решаемы, до тех пор, пока для них планируется, перед тем как перейти в рабочую среду. Многие пользователи не делал, которые хотите они имели более поздней версии. В среднем наша группа Customer Advisory Team (CAT) получает испуганный телефонные звонки примерно один раз месяц от клиентов, приложения которых стартует большими способом и они не делал это планирование. И они сказать примерно так: «Помогите! Все, что я помещаю в едином хранилище данных, и в течение 45 дней я собираюсь не останется свободного места на нем!» И если у вас есть масса бизнес-логики, встроенные в способы доступа к хранилищу данных, и у вас есть клиенты, которые работают с вашим приложением, не самое время перейти в день, пока выполняется перенос. Мы получим прохождения herculean усилия для секционирования клиентов свои данные в режиме реального времени без прерывания работы. Это очень интересных и весьма scary, а не что-либо вы хотите участвовать в Если этого можно избежать! Размышления об этом заранее и его интеграции в свое приложение сделает вашу жизнь намного проще, если приложение становится более поздней версии.

## <a name="summary"></a>Сводка

Схему секционирования действующие можно включить Облачное приложение Масштабирование до петабайт данных в облаке без узкие места. И не нужно заранее платить за большие машин или сложной инфраструктуры, как это делается при запуске приложения в центре обработки данных в локальной. В облаке вы можете можно постепенно добавлять емкость, как он нужен, и вы платите, только для так же, как вы используете при использовании его.

В [следующей главе](unstructured-blob-storage.md) мы увидим, как приложение Fix It реализует вертикальное секционирование, сохранение образов в хранилище BLOB-объектов.

## <a name="resources"></a>Ресурсы

Дополнительные сведения о стратегии секционирования см. следующие ресурсы.

Документация:

- [Рекомендации по проектированию крупномасштабных служб в облачных служб Windows Azure](https://msdn.microsoft.com/library/windowsazure/jj717232.aspx). Технический документ по (Mark simms) и Майкл Томасси.
- [Шаблоны и методики - конструктивные шаблоны облачных решений Майкрософт](https://msdn.microsoft.com/library/dn568099.aspx). См. в руководстве по секционированию данных, шаблон сегментирования.

Видеоролики:

- [FailSafe: Создание масштабируемых, надежных облачных служб](https://channel9.msdn.com/Series/FailSafe). Девять веб-трансляций, Ульрих Хоманн, Марк Меркьюри и (Mark simms). Отображает высокоуровневых концепциях и архитектурные принципы очень доступен и интересным способом, истории, извлеченных из консультационной группой Microsoft клиентов (CAT) опыт работы с реальными клиентами. См. в описании секционирования в серии 7.
- [Построение Big: Уроках, извлеченных из клиентов Windows Azure — часть I](https://channel9.msdn.com/Events/Build/2012/3-029). (Mark simms) обсуждаются схем секционирования, стратегии сегментирования, как реализовать сегментирования и федерации баз данных SQL, начиная с 19:49. Как и серия Failsafe, но переход в практические подробности.

Пример кода:

- [Основы облачных служб в Windows Azure](https://code.msdn.microsoft.com/Cloud-Service-Fundamentals-4ca72649). Образец приложения, который включает в себя сегментированной базы данных. Описание реализации схемы сегментирования, см. в разделе [DAL — сегментирования реляционной СУБД](https://blogs.msdn.com/b/windowsazure/archive/2013/09/05/dal-sharding-of-rdbms.aspx) в блоге Windows Azure.

> [!div class="step-by-step"]
> [Назад](data-storage-options.md)
> [Вперед](unstructured-blob-storage.md)
