---
uid: web-forms/overview/data-access/working-with-batched-data/wrapping-database-modifications-within-a-transaction-vb
title: "Перенос базы данных изменения в транзакции (Visual Basic) | Документы Microsoft"
author: rick-anderson
description: "Этот учебник является первым четырем, который отслеживает обновление, удаление и вставка пакеты данных. В этом учебнике показано, как разрешить транзакций базы данных..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/26/2007
ms.topic: article
ms.assetid: 7d821db5-6cbb-4b38-af14-198f9155fc82
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/working-with-batched-data/wrapping-database-modifications-within-a-transaction-vb
msc.type: authoredcontent
ms.openlocfilehash: f054445091edbc27263127fb3b7b851776ec617f
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2018
---
<a name="wrapping-database-modifications-within-a-transaction-vb"></a>Изменения базы данных оболочки в рамках транзакции (Visual Basic)
====================
по [Скотт Митчелл](https://twitter.com/ScottOnWriting)

[Загрузить код](http://download.microsoft.com/download/3/9/f/39f92b37-e92e-4ab3-909e-b4ef23d01aa3/ASPNET_Data_Tutorial_63_VB.zip) или [скачать PDF](wrapping-database-modifications-within-a-transaction-vb/_static/datatutorial63vb1.pdf)

> Этот учебник является первым четырем, который отслеживает обновление, удаление и вставка пакеты данных. В этом учебнике показано, как транзакции базы данных позволяют вносить изменения пакета должно быть выполнено как атомарную операцию, которая гарантирует, что все действия завершится успехом или ошибкой все шаги.


## <a name="introduction"></a>Вступление

Как мы видели, начиная с [Обзор Вставка, обновление и удаление данных](../editing-inserting-and-deleting-data/an-overview-of-inserting-updating-and-deleting-data-vb.md) учебнике GridView предоставляет встроенную поддержку для изменения уровня строк и удаления. С помощью нескольких щелчков мыши имеется возможность создать интерфейс изменения сложных данных без написания кода, при условии, что вы являетесь содержимого с помощью изменения и удаления на основе данных в ряду. Однако в некоторых случаях этого недостаточно, и нам необходимо предоставить пользователям возможность изменения или удаления пакета записей.

Например наиболее веб-клиенты электронной почты использование сетки списка каждое сообщение где каждая строка содержит флажок наряду с информацией по электронной почте s (тему, отправитель и т. д.). Этот интерфейс позволяет пользователю удалить несколько сообщений, пометив их и нажав кнопку Удалить выбранные сообщения. Интерфейс для редактирования пакета идеально подходит в ситуациях, где пользователи часто изменять многие различные записи. Вместо того, когда пользователь нажмет кнопку Изменить, их изменения и нажмите кнопку обновления для каждой записи, которую нужно изменить, интерфейс для редактирования пакета подготавливает к просмотру каждой строки с его интерфейс редактирования. Пользователь может быстро изменить набор строк, которые должны быть изменены и затем сохранить эти изменения, нажав кнопку "Обновить все". Этот набор учебников мы изучим, как создавать интерфейсы для вставки, редактирования и удаления пакетов данных.

При выполнении пакетных операций его сбой s, важно определить, будет ли возможно, для некоторых операций в пакете, для успешного выполнения хотя другим пользователям. Рассмотрите возможность удаления интерфейс — что должно происходить, если первый выбранную запись успешно удален, а вторая завершился неудачно, скажем, из-за нарушения ограничения внешнего ключа пакета? Сначала удалить запись s откатить или приемлемо для первой записи остаются удаленные?

Если требуется пакетную операцию следует рассматривать как [атомарной операции](http://en.wikipedia.org/wiki/Atomic_operation), один где либо все действия для успешного выполнения или сбой все действия, а затем уровень доступа к данным необходимо дополнить для поддержки [базы данных транзакции](http://en.wikipedia.org/wiki/Database_transaction). Транзакции базы данных обеспечения атомарности операций для набора `INSERT`, `UPDATE`, и `DELETE` — это функция, поддерживаемая все большинство современных баз данных и выполняются в рамках транзакции инструкции.

В этом учебнике мы рассмотрим способы расширения DAL для использования транзакций базы данных. Последующие учебники проверит реализации веб-страниц для пакетной вставки, обновления и удаления интерфейсов. S позволяют начать работу!

> [!NOTE]
> При изменении данных в пакетной транзакции, атомарности не всегда нужен. В некоторых случаях допустимо иметь некоторые успешного изменения данных и другим в том же пакете ошибкой, например, если удаление набора адресов электронной почты из веб сервера электронной почты клиента. Если отсутствует s обработать ошибки базы данных, середине удаления, его, вероятно, приемлемым, что эти записи, обработан без ошибок остаются удаленные s. В таких случаях DAL необходимо внести изменения для поддержки операций с базой данных. Существуют другие пакетной операции сценарии, тем не менее, где важна атомарность. Если клиент перемещается ее средств с одного банковского счета на другой, необходимо выполнить две операции: должно быть вычитается из первой учетной записи и затем добавляется второй денежные средства. Банк может намерений на первом шаге успешно, однако этот второй шаг неудачно, клиентам разумеется бы. Я рекомендую для работы этого учебника и реализовать расширенные возможности DAL для поддержки операций с базой данных, даже если вы не планируете их использование в пакетной вставки, обновления и удаления интерфейсы, которые мы будем строить в трех следующих учебников.


## <a name="an-overview-of-transactions"></a>Обзор операций

В большинстве баз данных включают поддержку *транзакции*, которые поддерживают несколько команд базы данных будут сгруппированы в одну логическую единицу работы. Команды базы данных, которые составляют транзакции гарантированно атомарной, это означает, что все команды завершится ошибкой или все успешно завершится.

В общем случае транзакции реализуются с помощью инструкций SQL, используя следующий шаблон:

1. Указывают на начало транзакции.
2. Выполните инструкции SQL, которые составляют транзакции.
3. Если имеется ошибка в одной из инструкций из шага 2, производится откат транзакции.
4. Если все инструкции шага 2 завершается без ошибок, зафиксируйте транзакцию.

Инструкций SQL, используемое для создания, фиксации и отката транзакции могут быть введены вручную, при написании скриптов SQL или создание хранимых процедур или через программный означает использование ADO.NET или классы в [ `System.Transactions` пространство имен](https://msdn.microsoft.com/library/system.transactions.aspx). В данном руководстве, мы рассмотрим только управление транзакциями, с помощью ADO.NET. В будущем учебнике мы рассмотрим способы использования хранимых процедур на уровне доступа к данным, после чего мы изучим инструкции SQL для создания, откат и фиксация транзакций. В то же время, обратитесь к [управление транзакции в хранимых процедур SQL Server](http://www.4guysfromrolla.com/webtech/080305-1.shtml) для получения дополнительной информации.

> [!NOTE]
> [ `TransactionScope` Класса](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx) в `System.Transactions` пространство имен позволяет разработчикам программным образом wrap ряд инструкций в пределах области транзакции и включает поддержку сложных операций с участием нескольких источников, таких как два разных баз данных или даже разнородных типов хранилищ данных, такие как базы данных Microsoft SQL Server, базы данных Oracle и веб-службы. Я решил использовать ADO.NET транзакции для этого учебника, а не хранить `TransactionScope` класса, так как более конкретные для транзакций базы данных и во многих случаях ADO.NET является гораздо меньше много ресурсов. Кроме того, в некоторых обстоятельствах `TransactionScope` класс использует координатор распределенных транзакций Майкрософт (MSDTC). Проблемы конфигурации, реализации и производительности окружающей MSDTC упрощает вместо специализированные и дополнительные темы и выходит за рамки этих учебников.


При работе с поставщиком SqlClient в ADO.NET транзакции инициируются путем вызова [ `SqlConnection` класса](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) s [ `BeginTransaction` метод](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.begintransaction.aspx), который возвращает [ `SqlTransaction` объекта](https://msdn.microsoft.com/library/system.data.sqlclient.sqltransaction.aspx). Инструкции изменения данных, размещенные в состав транзакции `try...catch` блока. При возникновении ошибки в инструкции `try` заблокировать выполнение передается `catch` блока, где можно выполнить откат транзакции через `SqlTransaction` объект s [ `Rollback` метод](https://msdn.microsoft.com/library/system.data.sqlclient.sqltransaction.rollback.aspx). Если все инструкции завершена успешно, вызов `SqlTransaction` объект s [ `Commit` метод](https://msdn.microsoft.com/library/system.data.sqlclient.sqltransaction.commit.aspx) в конце `try` блок фиксирует транзакцию. В следующем фрагменте кода показан этот шаблон. В разделе [поддержание согласованности базы данных с транзакциями](http://aspnet.4guysfromrolla.com/articles/072705-1.aspx) дополнительный синтаксис и примеры использования транзакций с помощью ADO.NET.


[!code-vb[Main](wrapping-database-modifications-within-a-transaction-vb/samples/sample1.vb)]

По умолчанию адаптеры таблиц TableAdapter типизированного набора данных не применять транзакции. Для обеспечения поддержки транзакций необходимо дополнить классы адаптеров таблиц для включения дополнительных методов, использующих выше шаблон для выполнения ряда инструкций изменения данных в области транзакции. В шаге 2 мы рассмотрим, как использовать разделяемые классы для добавления этих методов.

## <a name="step-1-creating-the-working-with-batched-data-web-pages"></a>Шаг 1: Создание рабочего с веб-страниц данных в пакетном режиме

Прежде чем начать, изучения того, как расширить DAL для поддержки операций с базой данных, позволяют s сначала занять некоторое время для создания веб-страниц ASP.NET, которые нам понадобится для этого учебника и три, выполните. Начните с добавления в новую папку с именем `BatchData` и добавьте следующие страницы ASP.NET, связывание с каждой страницы `Site.master` главной страницы.

- `Default.aspx`
- `Transactions.aspx`
- `BatchUpdate.aspx`
- `BatchDelete.aspx`
- `BatchInsert.aspx`


![Добавление страниц ASP.NET для руководств SqlDataSource](wrapping-database-modifications-within-a-transaction-vb/_static/image1.gif)

**Рис. 1**: Добавление страницы ASP.NET для руководств SqlDataSource


Как и в других папках, `Default.aspx` будет использовать `SectionLevelTutorialListing.ascx` пользовательский элемент управления, чтобы вывести список учебников внутри раздела. Таким образом, добавьте этот пользовательский элемент управления для `Default.aspx` , перетащив его из обозревателя решений на странице s представление конструктора.


[![Добавление Default.aspx SectionLevelTutorialListing.ascx пользовательского элемента управления](wrapping-database-modifications-within-a-transaction-vb/_static/image2.gif)](wrapping-database-modifications-within-a-transaction-vb/_static/image1.png)

**На рисунке 2**: добавление `SectionLevelTutorialListing.ascx` пользовательского элемента управления на `Default.aspx` ([Просмотр полноразмерное изображение](wrapping-database-modifications-within-a-transaction-vb/_static/image2.png))


И, наконец, добавьте эти четыре страницы как операции для `Web.sitemap` файла. В частности, добавьте следующую разметку после Настройка карты сайта `<siteMapNode>`:


[!code-xml[Main](wrapping-database-modifications-within-a-transaction-vb/samples/sample2.xml)]

После обновления `Web.sitemap`, занять некоторое время для просмотра учебников веб-сайт с помощью браузера. В левом меню теперь включает элементы для работы с учебниками пакетные данные.


![Карта сайта теперь включает операции для работы с учебниками пакетных данных](wrapping-database-modifications-within-a-transaction-vb/_static/image3.gif)

**Рис. 3**: карта сайта теперь включает операции для работы с учебниками пакетных данных


## <a name="step-2-updating-the-data-access-layer-to-support-database-transactions"></a>Шаг 2: Обновление уровня доступа к данным для поддержки транзакций базы данных

Как уже говорилось в первом руководстве, [Создание слой доступа к данным](../introduction/creating-a-data-access-layer-vb.md), типизированного набора данных в DAL состоит из таблиц данных и адаптеров таблиц. Данные хранятся DataTables TableAdapters предоставляют функциональные возможности для считывания данных из базы данных в таблицы данных, чтобы обновить базу данных изменения, внесенные в таблицы данных и т. д. Помните, что TableAdapters предоставляет два шаблона для обновления данных, которую я называют пакетного обновления и непосредственных DB. С шаблоном пакетное обновление TableAdapter передается DataSet, DataTable или коллекции из DataRow. Эти данные перечисляется и для каждого вставки, изменения или удаления строк, `InsertCommand`, `UpdateCommand`, или `DeleteCommand` выполняется. С шаблоном DB Direct TableAdapter вместо этого передается значений столбцов, необходимые для вставки, обновления или удаления одной записи. DB прямой метод шаблон затем использует эти значения, переданный для выполнения соответствующего `InsertCommand`, `UpdateCommand`, или `DeleteCommand` инструкции.

Независимо от обновления шаблон, используемый создаваемые автоматически методы адаптеры таблиц TableAdapter не применять транзакции. По умолчанию каждый insert, update или delete, выполненных TableAdapter рассматривается как дискретные одной операции. Для экземпляра представьте, что непосредственного шаблона DB используется каким-либо кодом МЕТОДА для вставки десять записей в базе данных. Этот код будет вызывать TableAdapter s `Insert` метод десять раз. Если первые пять вставки выполнена успешно, но шестой один вызвала исключение, первые пять записей, добавленных останется в базе данных. Аналогичным образом, если шаблон пакетного обновления используется для выполнения операций вставки, обновления и удаления для вставленных, изменения и удаленные строки в объект DataTable, если первый некоторых изменений выполнен успешно, но поздней произошла ошибка, эти изменения более ранних, завершена, будет оставаться в базе данных.

В некоторых сценариях требуется обеспечить атомарность последовательность изменений. Для этого мы вручную расширить TableAdapter, добавляя новые методы, которые выполняют `InsertCommand`, `UpdateCommand`, и `DeleteCommand` s в рамках транзакции. В [Создание слой доступа к данным](../introduction/creating-a-data-access-layer-vb.md) мы рассмотрели использование [разделяемые классы](http://en.wikipedia.org/wiki/Partial_type) для расширения функциональности DataTables в типизированный набор данных. Этот метод может также использоваться с адаптеры таблиц TableAdapter.

Типизированный набор данных `Northwind.xsd` находится в папке `App_Code` папки s `DAL` вложенную папку. Создать вложенную папку в `DAL` папку с именем `TransactionSupport` и добавьте новый файл класса с именем `ProductsTableAdapter.TransactionSupport.vb` (см. рис. 4). Этот файл будет содержать частичная реализация `ProductsTableAdapter` , включает методы для выполнения изменений данных, используя транзакцию.


![Добавьте папку с именем TransactionSupport и файл класса с именем ProductsTableAdapter.TransactionSupport.vb](wrapping-database-modifications-within-a-transaction-vb/_static/image4.gif)

**Рис. 4**: добавьте папку с именем `TransactionSupport` и один файл класса с именем`ProductsTableAdapter.TransactionSupport.vb`


Введите следующий код в `ProductsTableAdapter.TransactionSupport.vb` файла:


[!code-vb[Main](wrapping-database-modifications-within-a-transaction-vb/samples/sample3.vb)]

`Partial` Ключевое слово в объявлении класса указывает компилятору на то, что элементы, добавленные в, должны быть добавлены в `ProductsTableAdapter` класса в `NorthwindTableAdapters` пространства имен. Примечание `Imports System.Data.SqlClient` инструкции в верхней части файла. С момента настройки адаптера таблицы для использования поставщика SqlClient, внутренне используется [ `SqlDataAdapter` ](https://msdn.microsoft.com/library/system.data.sqlclient.sqldataadapter.aspx) объекта для выдачи команд в базу данных. Следовательно, необходимо использовать `SqlTransaction` класса, чтобы начать транзакцию и последующей его зафиксировать или выполнить откат. При использовании хранилища данных вместо Microsoft SQL Server, необходимо использовать соответствующий поставщик.

Эти методы предоставляют компоненты, необходимые для запуска отката и фиксации транзакции. Они помечены `Public`, позволяя использовать изнутри `ProductsTableAdapter`от другого класса в DAL и из другого слоя в архитектуре, таких как уровень бизнес-ЛОГИКИ. `BeginTransaction`Открывает внутренний s TableAdapter `SqlConnection` (при необходимости), начинает транзакцию и присваивает его `Transaction` свойство и прикрепляет транзакцию к внутренней `SqlDataAdapter` s `SqlCommand` объектов. `CommitTransaction`и `RollbackTransaction` вызвать `Transaction` объект s `Commit` и `Rollback` методы, соответственно, перед тем как закрыть внутренний `Connection` объекта.

## <a name="step-3-adding-methods-to-update-and-delete-data-under-the-umbrella-of-a-transaction"></a>Шаг 3: Добавление методов для обновления и удаления данных в рамках транзакции

С помощью этих методов завершения, мы повторно Готово для добавления методов для `ProductsDataTable` или МЕТОДА, выполнить ряд команд в рамках транзакции. Следующий метод использует шаблон пакета обновления для обновления `ProductsDataTable` экземпляра с помощью транзакции. Он запускает транзакцию путем вызова `BeginTransaction` метода, а затем использует `Try...Catch` блок для выдачи инструкции изменения данных. Если вызов `Adapter` объект s `Update` метод приводит к исключению, передаст выполнения `catch` блок, где будет выполнен откат транзакции и исключение создается повторно. Помните, что `Update` метод реализует шаблон пакетного обновления путем перечисления строк, предоставленного `ProductsDataTable` и выполнение требуемых `InsertCommand`, `UpdateCommand`, и `DeleteCommand` s. Если любой из этих команд приведет к ошибке, транзакция откатывается, Отмена предыдущего изменения, сделанные во время существования транзакции s. Следует `Update` инструкция исчерпана без ошибок, транзакция фиксируется в полном объеме.


[!code-vb[Main](wrapping-database-modifications-within-a-transaction-vb/samples/sample4.vb)]

Добавить `UpdateWithTransaction` метод `ProductsTableAdapter` класса с помощью разделяемый класс в `ProductsTableAdapter.TransactionSupport.vb`. Кроме того, этот метод может быть добавлен к s уровня бизнес-логики `ProductsBLL` класс несколько вспомогательных синтаксических изменений. А именно, ключевое слово `Me` в `Me.BeginTransaction()`, `Me.CommitTransaction()`, и `Me.RollbackTransaction()` пришлось бы заменить `Adapter` (Помните, что `Adapter` имя свойства в `ProductsBLL` типа `ProductsTableAdapter`).

`UpdateWithTransaction` Метод использует шаблон пакетного обновления, но ряд DB непосредственных вызовов также может использоваться в пределах области транзакции, как показано в следующем метод. `DeleteProductsWithTransaction` Метод принимает в качестве входных данных `List(Of T)` типа `Integer`, которые являются `ProductID` s для удаления. Запускает транзакцию путем вызова, метод `BeginTransaction` и затем в `Try` блокировать, выполняет итерацию предоставленного списка вызова непосредственного шаблона DB `Delete` метод для каждого `ProductID` значение. Если какие-либо вызовы `Delete` завершается ошибкой, управление передается `Catch` блок, где выполняется откат транзакции и исключение создается повторно. Если все вызовы `Delete` выполняются успешно, транзакция фиксируется. Этот метод, чтобы добавить `ProductsBLL` класса.


[!code-vb[Main](wrapping-database-modifications-within-a-transaction-vb/samples/sample5.vb)]

## <a name="applying-transactions-across-multiple-tableadapters"></a>Применение транзакций через несколько адаптеров таблиц

Код, связанные с транзакциями, проверяются в этом учебнике позволяет для нескольких инструкций на `ProductsTableAdapter` следует рассматривать как атомарную операцию. Но что делать, если несколько изменений в разных таблицах базы данных должны быть выполнены атомарным образом? Например при удалении категории, мы может сначала необходимо переназначить его текущие продукты, для некоторых других категорий. Следующие два действия переназначения продукты и удаление категории должна выполняться в виде атомарной операции. Но `ProductsTableAdapter` включает только методы изменения `Products` таблицы и `CategoriesTableAdapter` включает только методы изменения `Categories` таблицы. Так как транзакция может охватывать обе адаптеры таблиц TableAdapter

Один из вариантов — Добавление метода для `CategoriesTableAdapter` с именем `DeleteCategoryAndReassignProducts(categoryIDtoDelete, reassignToCategoryID)` . у этого метода вызовите хранимую процедуру, которая и переназначает продукты и удаляет категорию в пределах транзакции, определенной в хранимой процедуре. Мы рассмотрим способы begin, commit и rollback транзакции в хранимых процедурах в будущем учебника.

Другой вариант — создать вспомогательный класс в DAL, содержащий `DeleteCategoryAndReassignProducts(categoryIDtoDelete, reassignToCategoryID)` метод. Этот метод может создать экземпляр `CategoriesTableAdapter` и `ProductsTableAdapter` и сделать эти два адаптеры таблиц TableAdapter `Connection` свойства с тем же `SqlConnection` экземпляра. AT этой точки, либо один из двух адаптеров таблиц будет инициировать операцию вызовом `BeginTransaction`. Адаптеры таблиц TableAdapter методы для переназначения продукты и удаление категории будет вызываться в `Try...Catch` блок с транзакция фиксируется или откатывается назад при необходимости.

## <a name="step-4-adding-theupdatewithtransactionmethod-to-the-business-logic-layer"></a>Шаг 4: Добавление`UpdateWithTransaction`метод уровня бизнес-логики

На шаге 3 мы добавили `UpdateWithTransaction` метод `ProductsTableAdapter` в DAL. Мы должны добавьте соответствующий метод для МЕТОДА. Хотя уровень представления может вызвать непосредственно до DAL для вызова `UpdateWithTransaction` метода, в этих учебниках strived для определения многоуровневую архитектуру, которая изолирует DAL от уровня представления. Таким образом он behooves нам продолжить этот подход.

Откройте `ProductsBLL` и добавьте метод с именем `UpdateWithTransaction` который просто вызывает метод до соответствующего метода DAL. Теперь будет два новых метода в `ProductsBLL`: `UpdateWithTransaction`, который вы только что добавили, и `DeleteProductsWithTransaction`, который был добавлен на шаге 3.


[!code-vb[Main](wrapping-database-modifications-within-a-transaction-vb/samples/sample6.vb)]

> [!NOTE]
> Эти методы не включают `DataObjectMethodAttribute` атрибут, назначенный для большинства методов в `ProductsBLL` класса, так как мы будем вызов эти методы непосредственно из классов кода страницы ASP.NET. Помните, что `DataObjectMethodAttribute` используется для пометки, какие методы должны появиться в ObjectDataSource s Настройка источников данных, мастер какие вкладке (SELECT, UPDATE, INSERT или DELETE). Поскольку GridView не имеет встроенной поддержки пакета, изменение или удаление, мы еще вызывать эти методы программным образом, вместо написания кода декларативный подход.


## <a name="step-5-atomically-updating-database-data-from-the-presentation-layer"></a>Шаг 5: Атомарным образом обновление баз данных от уровня представления

Для демонстрации влияния транзакции при обновлении пакета записей, s позволяют создать пользовательский интерфейс, который содержит все продукты в GridView, а также кнопку веб-сайт управления, при щелчке переназначает продукты `CategoryID` значения. В частности, развивается переназначение категории, чтобы первый нескольким продуктам назначаются является допустимым `CategoryID` несуществующей присвоено значение, а другие представляют собой намеренно `CategoryID` значение. Если предпринимается попытка обновить базу данных с продуктом, `CategoryID` не соответствует существующей категории s `CategoryID`, произойдет нарушение ограничения внешнего ключа, и возникает исключение. Мы будет отображен в этом примере при, с использованием транзакции исключение, вызываемое из нарушение ограничения внешнего ключа приведет к предыдущим допустимым `CategoryID` изменений будет выполнен откат. При не используется транзакция, тем не менее, останутся изменения начальной категориям.

Сначала откройте `Transactions.aspx` страницы в `BatchData` папки и перетащите элемент управления GridView с панели элементов в конструктор. Задайте его `ID` для `Products` и его смарт-теге, привязать его к новой ObjectDataSource с именем `ProductsDataSource`. Настройка ObjectDataSource для извлечения данных из `ProductsBLL` класса s `GetProducts` метод. Это только для чтения GridView, поэтому устанавливается раскрывающихся списков в UPDATE, INSERT и удаление вкладок (нет) и нажмите "Готово".


[![Настройка ObjectDataSource можно использовать метод GetProducts класса ProductsBLL s](wrapping-database-modifications-within-a-transaction-vb/_static/image5.gif)](wrapping-database-modifications-within-a-transaction-vb/_static/image3.png)

**Рис. 5**: Настройка ObjectDataSource для использования `ProductsBLL` класса s `GetProducts` метод ([Просмотр полноразмерное изображение](wrapping-database-modifications-within-a-transaction-vb/_static/image4.png))


[![Установите раскрывающихся списков в UPDATE, INSERT и удаление вкладок (нет)](wrapping-database-modifications-within-a-transaction-vb/_static/image6.gif)](wrapping-database-modifications-within-a-transaction-vb/_static/image5.png)

**Рис. 6**: задать раскрывающихся списков в обновления, вставки и удаления вкладок (нет) ([Просмотр полноразмерное изображение](wrapping-database-modifications-within-a-transaction-vb/_static/image6.png))


После завершения работы мастера настройки источника данных Visual Studio создаст стояли и CheckBoxField для полей данных продукта. Удалите все эти поля за исключением `ProductID`, `ProductName`, `CategoryID`, и `CategoryName` и переименуйте `ProductName` и `CategoryName` стояли `HeaderText` свойства продукта и категории, соответственно. В смарт-теге установите флажок Включить разбиение на страницы. После внесения этих изменений, GridView и ObjectDataSource s декларативная разметка должна выглядеть следующим образом:


[!code-aspx[Main](wrapping-database-modifications-within-a-transaction-vb/samples/sample7.aspx)]

Добавьте три кнопки элементах над элементом управления GridView. Задайте первую кнопку s свойства Text обновить сетку второй s, чтобы изменить категории (с ТРАНЗАКЦИИ) и третий один s, чтобы изменить категории (без ТРАНЗАКЦИИ).


[!code-aspx[Main](wrapping-database-modifications-within-a-transaction-vb/samples/sample8.aspx)]

На этом этапе должен выглядеть примерно на показанную на рисунке 7 показано представление конструктора в Visual Studio.


[![Страница содержит элемент управления GridView и три кнопки веб-элементов управления](wrapping-database-modifications-within-a-transaction-vb/_static/image7.gif)](wrapping-database-modifications-within-a-transaction-vb/_static/image7.png)

**Рис. 7**: страница содержит GridView и три кнопки веб-элементов управления ([Просмотр полноразмерное изображение](wrapping-database-modifications-within-a-transaction-vb/_static/image8.png))


Создание обработчиков событий для каждого из трех кнопку s `Click` события и используйте следующий код:


[!code-vb[Main](wrapping-database-modifications-within-a-transaction-vb/samples/sample9.vb)]

Обновление кнопки s `Click` обработчик событий просто выполняет повторную привязку данных к GridView, вызвав `Products` GridView s `DataBind` метод.

Второй обработчик событий переназначает продукты `CategoryID` s и использует метод транзакции из МЕТОДА для выполнения базы данных обновляет в рамках транзакции. Обратите внимание, что каждый продукт s `CategoryID` произвольно присвоено то же значение, что его `ProductID`. Это будет работать для первого несколько продуктов, поскольку эти продукты имеют `ProductID` значения, которые происходят для сопоставления с допустимым `CategoryID` s. Один раз, но `ProductID` начало s, слишком большим, такое совпадение пересечение `ProductID` s и `CategoryID` s больше не применяется.

Третий `Click` обработчик событий обновляет продукты `CategoryID` s таким же образом, но отправляет обновление базы данных с помощью `ProductsTableAdapter` по умолчанию s `Update` метод. Это `Update` метод не переносится ряда команд в рамках транзакции, будет сохраняться, поэтому эти изменения будут сделаны перед первой ошибки Нарушение обнаружено ограничение внешнего ключа.

Чтобы продемонстрировать это, посетите эту страницу через браузер. Первоначально вы увидите первой страницы данных, как показано на рисунке 8. Затем щелкните кнопку Изменить категории (с ТРАНЗАКЦИИ). Это вызывает обратную передачу и попытка обновления всех продуктов `CategoryID` значений, но приведет к нарушению ограничения внешнего ключа (см. рис. 9).


[![Продукты отображаются в выгружаемой GridView](wrapping-database-modifications-within-a-transaction-vb/_static/image8.gif)](wrapping-database-modifications-within-a-transaction-vb/_static/image9.png)

**Рис. 8**: продукты отображаются в выгружаемой GridView ([Просмотр полноразмерное изображение](wrapping-database-modifications-within-a-transaction-vb/_static/image10.png))


[![Переназначение категорий приводит к нарушению ограничения внешнего ключа](wrapping-database-modifications-within-a-transaction-vb/_static/image9.gif)](wrapping-database-modifications-within-a-transaction-vb/_static/image11.png)

**Рис. 9**: переназначение результаты категорий в нарушение ограничения внешнего ключа ([Просмотр полноразмерное изображение](wrapping-database-modifications-within-a-transaction-vb/_static/image12.png))


Теперь попаданий кнопка "Назад" в браузере s, а затем нажмите кнопку Обновить сетку. При обновлении данных должны появиться точно такие же выходные данные, как показано на рисунке 8. То есть даже если некоторые из этих продуктов `CategoryID` s было изменено на допустимые значения и обновлен в базе данных, они были откатывается произошло нарушение ограничения внешнего ключа.

Теперь попробуйте щелкнуть кнопку Изменить категории (без ТРАНЗАКЦИИ). Это приведет к та же ошибка нарушения ограничения внешнего ключа (см. рис. 9), но это время этих продуктов, `CategoryID` значения были изменены для юридических значение не будет выполнен откат. Попадание кнопка "Назад" в браузере s и нажмите кнопку Обновить сетку. Как показано на рис. 10, `CategoryID` назначили s первых восьми продуктов. Например, на рисунке 8 изменений была `CategoryID` 1, но в на рисунке 10 ИТ s была переназначена на 2.


[![Некоторые продукты CategoryID значения обновлены во время другие были не удалось](wrapping-database-modifications-within-a-transaction-vb/_static/image10.gif)](wrapping-database-modifications-within-a-transaction-vb/_static/image13.png)

**Рис. 10**: некоторые продукты `CategoryID` значения обновлены во время другие были не удалось ([Просмотр полноразмерное изображение](wrapping-database-modifications-within-a-transaction-vb/_static/image14.png))


## <a name="summary"></a>Сводка

По умолчанию методы адаптера таблицы s не заключайте инструкции выполненного базы данных в пределах транзакции, но с минимальными усилиями можно добавить методы, которые будут созданы, фиксации и отката транзакции. В этом учебнике мы создали три таких методов в `ProductsTableAdapter` класса: `BeginTransaction`, `CommitTransaction`, и `RollbackTransaction`. Мы узнали, как использовать эти методы вместе с `Try...Catch` блока для выполнения ряда инструкций изменения данных atomic. В частности, мы создали `UpdateWithTransaction` метод в `ProductsTableAdapter`, которой использует шаблон пакетного обновления, чтобы выполнить необходимые изменения в строки указанный `ProductsDataTable`. Мы также добавили `DeleteProductsWithTransaction` метод `ProductsBLL` класса МЕТОДА, который принимает `List` из `ProductID` значения в качестве входных данных и вызывает метод шаблон DB Direct `Delete` для каждого `ProductID`. Оба метода начните с создания транзакции, а затем выполнить инструкции изменения данных в пределах `Try...Catch` блока. При возникновении исключения, откат транзакции, в противном случае он не будет выделен.

Шаг 5 показано влияние транзакций пакетных обновлений и пакетов обновлений, которые не поддерживаемые с использованием транзакции. В трех следующих руководствах будут основываться на фундаменте в этом учебнике и создавать пользовательские интерфейсы для выполнения пакета обновления, удаления и вставки.

Программирование довольны!

## <a name="further-reading"></a>Дополнительные сведения

Дополнительные сведения по темам, рассматриваемые в этом учебнике см. в следующих ресурсах:

- [Обеспечение согласованности базы данных с транзакциями](http://aspnet.4guysfromrolla.com/articles/072705-1.aspx)
- [Хранимые процедуры для управления транзакциями в SQL Server](http://www.4guysfromrolla.com/webtech/080305-1.shtml)
- [Транзакции, стало проще:`System.Transactions`](https://blogs.msdn.com/florinlazar/archive/2004/07/23/192239.aspx)
- [TransactionScope и адаптеров обработки данных](http://andyclymer.blogspot.com/2007/01/transactionscope-and-dataadapters.html)
- [С помощью операций с базой данных Oracle в .NET](http://www.oracle.com/technology/pub/articles/price_dbtrans_dotnet.html)

## <a name="about-the-author"></a>Об авторе

[Скотт Митчелл](http://www.4guysfromrolla.com/ScottMitchell.shtml), автор семи ASP/ASP.NET и основателя из [4GuysFromRolla.com](http://www.4guysfromrolla.com), работает с веб-технологиями Майкрософт с 1998 года. Скотт — независимый консультант, trainer и записи. Его последняя книга — [ *диспетчерами учат самостоятельно ASP.NET 2.0 в течение 24 часов*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Он может быть достигнута по [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) или через его блог, который можно найти в [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Благодарности

Этот учебник ряд прошел проверку многие полезные рецензентов. Основными редакторами этого учебника были Dave Gardner – Хилтон Гизнау и Мерфи Тереза д. Объясняются моих последующих статей для MSDN? Если Да, напишите мне по [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Назад](batch-inserting-cs.md)
[Вперед](batch-updating-vb.md)