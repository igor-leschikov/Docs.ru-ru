---
uid: web-forms/overview/data-access/paging-and-sorting-with-the-datalist-and-repeater/paging-report-data-in-a-datalist-or-repeater-control-cs
title: "Разбивка на страницы данных отчета в DataList или управления повторителем (C#) | Документы Microsoft"
author: rick-anderson
description: "При повторителя ни DataList предложение автоматическое разбиение по страницам или поддержку сортировки этого учебника показано, как добавить поддержку разбиения на страницы в DataList или повторителя..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 11/13/2006
ms.topic: article
ms.assetid: e8e0809b-25c4-4c3b-8d12-9a17048148ae
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/paging-and-sorting-with-the-datalist-and-repeater/paging-report-data-in-a-datalist-or-repeater-control-cs
msc.type: authoredcontent
ms.openlocfilehash: 4952adff752ec834b8be5f190181be98a034ccfd
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2018
---
<a name="paging-report-data-in-a-datalist-or-repeater-control-c"></a>Разбиение на страницы данных отчета в DataList или управления повторителем (C#)
====================
по [Скотт Митчелл](https://twitter.com/ScottOnWriting)

[Загрузить пример приложения](http://download.microsoft.com/download/4/a/7/4a7a3b18-d80e-4014-8e53-a6a2427f0d93/ASPNET_Data_Tutorial_44_CS.exe) или [скачать PDF](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/datatutorial44cs1.pdf)

> Хотя повторителя ни DataList предложения, автоматическое разбиение по страницам или сортировки поддержки этого учебника показано, как добавьте поддержка разбиения на страницы в DataList или повторителя, что позволяет гораздо более гибкие разбиения на страницы и данных отображения интерфейсов.


## <a name="introduction"></a>Вступление

Разбиение по страницам и сортировка – две часто встречающиеся функции отображения данных в приложение электронной. Например при поиске книг по ASP.NET в Интернет-магазине, могут существовать сотни книг, но отчет с результатами поиска только десять совпадениями на каждой странице. Кроме того можно сортировать результаты по title, цена, количество страниц, имя автора и т. д. Как уже говорилось в [разбиение по страницам и сортировка данных отчета](../paging-and-sorting/paging-and-sorting-report-data-cs.md) учебник, элементы управления GridView, DetailsView и FormView, имеют встроенную поддержку разбиения на страницы, может быть включена в деления флажок. GridView также включает поддержку сортировки.

К сожалению DataList ни повторителя обеспечивают автоматическое разбиение по страницам или поддержки сортировки. В этом учебнике мы рассмотрим, как добавить поддержку разбиения на страницы в DataList или повторителя. Мы вручную необходимо создать интерфейс разбиения на страницы, отобразить соответствующую страницу записей и запомнить странице во время обратной передачи. А это требует больше времени и кода по сравнению с GridView, DetailsView и FormView, DataList и повторителя позволяют гораздо более гибкие разбиения на страницы и данных отображения интерфейсов.

> [!NOTE]
> Этот учебник посвящен исключительно разбиения на страницы. В следующем уроке мы будем рассмотрим добавление возможности сортировки.


## <a name="step-1-adding-the-paging-and-sorting-tutorial-web-pages"></a>Шаг 1: Добавление разбиением по страницам и сортировка учебника веб-страницы

Прежде чем начать этого учебника, позволяют s сначала занять некоторое время для добавления страниц ASP.NET, которые потребуются для этого учебника и другая. Начните с создания новой папки в проект с именем `PagingSortingDataListRepeater`. Добавьте пять страниц ASP.NET в этой папке, в которых настроен на использование главной страницы `Site.master`:

- `Default.aspx`
- `Paging.aspx`
- `Sorting.aspx`
- `SortingWithDefaultPaging.aspx`
- `SortingWithCustomPaging.aspx`


![Создание папки PagingSortingDataListRepeater и добавление страницы учебника по ASP.NET](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image1.png)

**Рис. 1**: создание `PagingSortingDataListRepeater` папки и добавления страниц учебника по ASP.NET


Затем откройте `Default.aspx` страницы и перетащите `SectionLevelTutorialListing.ascx` пользовательского элемента управления с `UserControls` папку в область конструктора. Этот пользовательский элемент управления, который мы создали [главные страницы и переходов](../introduction/master-pages-and-site-navigation-cs.md) учебник, перечисляет карты узла и отображает руководства, имеющиеся в текущем разделе в виде маркированного списка.


[![Добавление Default.aspx SectionLevelTutorialListing.ascx пользовательского элемента управления](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image3.png)](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image2.png)

**На рисунке 2**: добавление `SectionLevelTutorialListing.ascx` пользовательского элемента управления на `Default.aspx` ([Просмотр полноразмерное изображение](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image4.png))


Для выполнения в маркированном списке отображались разбиением по страницам и сортировка учебники, которые будут созданы, необходимо добавить их в карту узла. Откройте `Web.sitemap` файл и добавьте следующую разметку после редактирования и удаления разметкой DataList узел карты узла:


[!code-xml[Main](paging-report-data-in-a-datalist-or-repeater-control-cs/samples/sample1.xml)]


![Обновление карты сайта для включения новых страниц ASP.NET](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image5.png)

**Рис. 3**: обновление карты сайта для включения новых страниц ASP.NET


## <a name="a-review-of-paging"></a>Просмотрите разбиения на страницы

В предыдущих уроках мы рассмотрели просматривать страницы данных в элементах управления GridView, DetailsView и FormView. Эти три элемента управления предоставляют простой формы разбиения на страницы вызывается *разбиения на страницы по умолчанию* , может быть реализован просто можно установить параметр включения постраничного просмотра в смарт-теге элемента управления s. С разбиением на страницы по умолчанию, при каждом запросе страницы данных, либо на первой странице посетите или при переходе на другую страницу данных GridView, DetailsView, или повторно запрашивает управления FormView *все* данных из ObjectDataSource. Он затем фрагменты определенного набора записей для отображения заданным Запрошенный индекс страницы и число записей, отображаемых на каждой странице. Мы рассмотрели разбиения на страницы по умолчанию, подробно [разбиение по страницам и сортировка данных отчета](../paging-and-sorting/paging-and-sorting-report-data-cs.md) учебника.

Поскольку по умолчанию подкачки повторно запрашивает все записи для каждой страницы, он не имеет смысла при разбиении на страницы с большими объемами данных. Например представьте постраничный просмотр 50 000 записей, а размер страницы равен 10. Каждый раз, когда пользователь переходит на новую страницу, все 50 000 записей необходимо извлечь из базы данных, несмотря на то, что отображаются только десять из них.

*Пользовательское разбиение по страницам* решает разбиения возможны проблемы с производительностью, перехватывая нужного подмножества записей для отображения на запрошенную страницу. При реализации пользовательского разбиения на страницы, нам необходимо написать SQL-запрос, возвращающий эффективность правильным набором записей. Мы узнали, как создать запрос с помощью SQL Server 2005 s новый [ `ROW_NUMBER()` ключевое слово](http://www.4guysfromrolla.com/webtech/010406-1.shtml) в [эффективно разбивка на страницы через большие объемы данных](../paging-and-sorting/efficiently-paging-through-large-amounts-of-data-cs.md) учебника.

Для реализации постраничного просмотра по умолчанию для элементов управления DataList или повторителя, можно использовать [ `PagedDataSource` класса](https://msdn.microsoft.com/library/system.web.ui.webcontrols.pageddatasource.aspx) как оболочка `ProductsDataTable` страниц, содержимое. `PagedDataSource` Класс имеет `DataSource` свойство, которое может быть присвоен любой перечисляемый объект и [ `PageSize` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.pageddatasource.pagesize.aspx) и [ `CurrentPageIndex` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.pageddatasource.currentpageindex.aspx) свойств, которые указывают, сколько записей следует отображать на каждой странице и индекса текущей страницы. После задания этих свойств `PagedDataSource` можно использовать в качестве источника данных каких-либо данных веб-элемента управления. `PagedDataSource`, При перечислении, будет возвращать только соответствующие подмножество записей из его внутренним `DataSource` на основе `PageSize` и `CurrentPageIndex` свойства. На рисунке 4 показаны функциональные возможности `PagedDataSource` класса.


![Заключает в оболочку PagedDataSource перечислимый объект выгружаемой интерфейса](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image6.png)

**Рис. 4**: `PagedDataSource` упаковывает перечислимый объект выгружаемой интерфейса


`PagedDataSource` Объект может быть создан и настроен непосредственно из уровня бизнес-логики и привязан к DataList или повторителя через ObjectDataSource или можно создать и настроить непосредственно в класс фонового кода ASP.NET страницы s. Если используется второй подход, мы отказ от использования ObjectDataSource и вместо привязки разбитых на страницы данных в DataList или повторителя программным.

`PagedDataSource` Также имеет свойства для поддержки пользовательское разбиение по страницам. Тем не менее, мы можно обойти с помощью `PagedDataSource` для пользовательских подкачки, так как мы уже есть методы уровень бизнес-ЛОГИКИ `ProductsBLL` классов, предназначенных для пользовательское разбиение по страницам, который возвращает точное записи для отображения.

В этом учебнике мы рассмотрим реализации подкачки по умолчанию в элементе управления DataList, добавив новый метод `ProductsBLL` класс, который возвращает соответственно настроенного `PagedDataSource` объекта. В следующем уроке мы рассмотрим, как использовать пользовательское разбиение по страницам.

## <a name="step-2-adding-a-default-paging-method-in-the-business-logic-layer"></a>Шаг 2: Добавление метода разбиения на страницы по умолчанию в бизнес-логики

`ProductsBLL` Класс в настоящее время имеет метод, возвращающий сведения о продукте `GetProducts()` и один для возвращения определенного подмножества продуктов начального индекса `GetProductsPaged(startRowIndex, maximumRows)`. С разбиением на страницы по умолчанию, GridView, DetailsView и FormView элементы управления используют все `GetProducts()` метод для получения всех продуктов, но при использовании `PagedDataSource` внутренним образом для отображения только нужное подмножество записей. Чтобы реплицировать эта функциональность с элементами управления DataList и повторителя, можно создать новый метод в МЕТОДА, которая имитирует поведение.

Добавьте метод `ProductsBLL` класс с именем `GetProductsAsPagedDataSource` , принимает два входных параметра целое число:

- `pageIndex`индекс страницы для отображения, индексированных с нуля, и
- `pageSize`число записей, отображаемых на каждой странице.

`GetProductsAsPagedDataSource`Запускает, получая *все* записи из `GetProducts()`. Затем он создает `PagedDataSource` объекта, присвоив его `CurrentPageIndex` и `PageSize` свойства к значениям переданный `pageIndex` и `pageSize` параметров. Метод завершается, возвращая настройки `PagedDataSource`:


[!code-csharp[Main](paging-report-data-in-a-datalist-or-repeater-control-cs/samples/sample2.cs)]

## <a name="step-3-displaying-product-information-in-a-datalist-using-default-paging"></a>Шаг 3: Отображение сведений о продукте в элементе управления DataList при разбиении по умолчанию

С `GetProductsAsPagedDataSource` добавляемый метод `ProductsBLL` класса, теперь можно создать DataList или повторителя, предоставляющий разбиения на страницы по умолчанию. Сначала откройте `Paging.aspx` страницы в `PagingSortingDataListRepeater` папки и перетащите DataList из области элементов в конструктор параметр DataList s `ID` свойства `ProductsDefaultPaging`. Смарт-тег DataList s, создайте новый элемент управления ObjectDataSource с именем `ProductsDefaultPagingDataSource` и настроить его таким образом, чтобы он извлекает данные с помощью `GetProductsAsPagedDataSource` метод.


[![Создание ObjectDataSource и настройте его для использования () GetProductsAsPagedDataSource метод](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image8.png)](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image7.png)

**Рис. 5**: ObjectDataSource создайте и настройте его для использования `GetProductsAsPagedDataSource` `()` метод ([Просмотр полноразмерное изображение](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image9.png))


Установите раскрывающихся списков в UPDATE, INSERT и удаление вкладок (нет).


[![Установите раскрывающихся списков в UPDATE, INSERT и удаление вкладок (нет)](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image11.png)](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image10.png)

**Рис. 6**: установите раскрывающихся списков в UPDATE, INSERT и удаление вкладок (нет) ([Просмотр полноразмерное изображение](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image12.png))


Поскольку `GetProductsAsPagedDataSource` метод ожидает два входных параметра, мастер запросит нам источник значения этих параметров.

Индекс страницы и значения размера страниц необходимо запоминать. Они могут храниться в состоянии представления, сохраняются в строку запроса, хранящиеся в переменных сеанса или сохраненных с помощью какой-либо другой подход. В этом учебнике мы будем использовать строки запроса имеет преимущество, позволяя только определенную страницу данных, чтобы создать закладку.

В частности, используйте querystring поля pageIndex и pageSize для `pageIndex` и `pageSize` параметров, соответственно (см. рис. 7). Теперь пора установить значения по умолчанию для этих параметров, как значения строки запроса, выиграл t присутствовать при первом посещении этой страницы. Для `pageIndex`, значение по умолчанию равно 0 (которая отображается первая страница данных) и `pageSize` s значение по умолчанию для 4.


[![Использовать строку запроса в качестве источника для параметров pageIndex и pageSize](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image14.png)](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image13.png)

**Рис. 7**: использовать строку запроса в качестве источника для `pageIndex` и `pageSize` параметров ([Просмотр полноразмерное изображение](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image15.png))


После настройки ObjectDataSource, Visual Studio автоматически создает `ItemTemplate` для DataList. Настройка `ItemTemplate` так, чтобы отображались только название продукта s, категорию и поставщика. Также задать DataList s `RepeatColumns` равным 2, его `Width` до 100% и его `ItemStyle` s `Width` на 50%. Эти параметры ширины предоставит равные интервалы для двух столбцов.

После внесения этих изменений, разметка s DataList и ObjectDataSource должна выглядеть следующим образом:


[!code-aspx[Main](paging-report-data-in-a-datalist-or-repeater-control-cs/samples/sample3.aspx)]

> [!NOTE]
> Поскольку мы не выполняются никакие обновления или удаления функциональные возможности в этом учебнике, можно отключить состояние представления s DataList, чтобы уменьшить размер отображаемой страницы.


При первоначальном посещении этой страницы, с помощью браузера, ни `pageIndex` , ни `pageSize` предоставляются параметры строки запроса. Таким образом используются значения по умолчанию 0 и 4. Как показано на рис. 8, в результате DataList, в котором отображаются первые четыре продуктов.


[![Первая из четырех товаров, перечислены](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image17.png)](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image16.png)

**Рис. 8**: первая из четырех товаров, перечислены ([Просмотр полноразмерное изображение](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image18.png))


Без интерфейса постраничного просмотра, существует в настоящее время не просто s означает для пользователя перейти на вторую страницу данных. Мы создадим интерфейс разбиения по страницам в шаге 4. На данном этапе, разбиение на страницы могут выполняться только путем прямого указания критериев разбиения на страницы в строке запроса. Например, чтобы просмотреть на второй странице, измените URL-адрес в адресной строке браузера s из `Paging.aspx` для `Paging.aspx?pageIndex=2` и нажмите ВВОД. В результате данные, отображаемые на второй странице (см. рис. 9).


[![Откроется вторая страница данных](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image20.png)](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image19.png)

**Рис. 9**: отображения второй страницы данных ([Просмотр полноразмерное изображение](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image21.png))


## <a name="step-4-creating-the-paging-interface"></a>Шаг 4: Создание интерфейса постраничного просмотра

Существует множество разных интерфейсов разбиения на страницы, которые могут быть реализованы. Эти элементы управления GridView, DetailsView и FormView обеспечивают четыре различных интерфейсов для выбора одного из:

- **Далее, предыдущую** пользователи могут перемещать одной странице одновременно к следующему или предыдущему сообщению.
- **Далее, назад, первой, последней** помимо кнопки "Далее" и "Назад", этот интерфейс содержит первая и последняя кнопки для перехода к самой первой или последней странице.
- **Числовые** перечислены номера страниц в интерфейсе постраничного просмотра, позволяя пользователю быстро перейти к определенной странице.
- **Числовые, во-первых, последний** помимо номера страниц, включает кнопки для перехода к самой первой или последней странице.

DataList и повторителя мы отвечают за выбор при интерфейс разбиения по страницам и его реализации. Это включает в себя создание необходимых веб-элементов управления на странице и отображение запрошенную страницу, при нажатии кнопки интерфейса постраничного просмотра. Кроме того некоторые элементы управления интерфейса постраничного просмотра, может потребоваться отключить. Например при просмотре первой страницы данных с помощью следующего, назад, во-первых, последнего интерфейсом, первый и назад кнопками будет отключен.

В этом учебнике используйте позволяют s следующего, назад, во-первых, последнего интерфейса. Добавьте четыре кнопки веб-элементов управления на страницу и задайте их `ID` s, чтобы `FirstPage`, `PrevPage`, `NextPage`, и `LastPage`. Задать `Text` свойства &lt; &lt; сначала &lt; Prev, Далее &gt;и последний &gt; &gt; .


[!code-aspx[Main](paging-report-data-in-a-datalist-or-repeater-control-cs/samples/sample4.aspx)]

Создайте `Click` обработчик событий для каждого из этих кнопок. Позже мы добавим код, необходимый для отобразить запрошенную страницу.

## <a name="remembering-the-total-number-of-records-being-paged-through"></a>Обратите внимание, общее количество страниц

Независимо от выбранного интерфейса постраничного просмотра мы должны вычислений и запоминать, общее число страниц. Общее число строк (в сочетании с размером страницы) определяет, сколько всего страниц данных страниц, которое определяет, какие элементы управления интерфейса постраничного просмотра добавляются или включены. Далее, назад, первый последний интерфейс, который мы будем строить количество страниц используется двумя способами:

- Чтобы определить, является ли последняя страница просматривается в этом случае кнопок Далее и Дата последнего отключены.
- При нажатии кнопки последней, нам нужно whisk их на последнюю страницу, индекс которого является одним меньше страницы счетчика.

Количество страниц вычисляется как разделенное ceiling общее число строк на размер страницы. Например, если мы постраничного 79 записи четыре записей на каждой странице, то количество страниц является 20 (ceiling 79 / 4). При использовании числовых интерфейс разбиения по страницам, эти сведения сообщает о том, сколько кнопок с номерами страниц для отображения; Если наш интерфейса постраничного просмотра включает кнопок Далее или последней, количество страниц используется чтобы определить, когда отключение кнопок Далее или последнего.

Если интерфейс разбиения по страницам включает последней кнопки, крайне важно, общее число страниц, которые будут запомнены во время обратной передачи, чтобы при нажатии кнопки последней мы могли определить индекс последней страницы. Для этого создайте `TotalRowCount` свойства в классе ASP.NET страницы s кода, который сохраняет его значение состояния представления:


[!code-csharp[Main](paging-report-data-in-a-datalist-or-repeater-control-cs/samples/sample5.cs)]

В дополнение к `TotalRowCount`, прежде всего создать только для чтения свойства уровня страницы легко доступа к странице индекса, размер страницы и количества страниц:


[!code-csharp[Main](paging-report-data-in-a-datalist-or-repeater-control-cs/samples/sample6.cs)]

## <a name="determining-the-total-number-of-records-being-paged-through"></a>Определить общее число страниц

`PagedDataSource` Объект, возвращенный ObjectDataSource s `Select()` метод имеет внутри него *все* из записей продуктов, даже если только их подмножество, отображаются в DataList. `PagedDataSource` s [ `Count` свойство](https://msdn.microsoft.com/library/system.web.ui.webcontrols.pageddatasource.count.aspx) возвращает количество элементов, которые будут отображаться в DataList; [ `DataSourceCount` свойство](https://msdn.microsoft.com/library/system.web.ui.webcontrols.pageddatasource.datasourcecount.aspx) возвращает общее число элементов в `PagedDataSource`. Таким образом, нам нужно назначить ASP.NET страницы s `TotalRowCount` свойства значение из `PagedDataSource` s `DataSourceCount` свойство.

Чтобы сделать это, создайте обработчик событий для ObjectDataSource s `Selected` событий. В `Selected` обработчик событий, у нас есть доступ к возвращаемому значению ObjectDataSource s `Select()` метода в данном случае `PagedDataSource`.


[!code-csharp[Main](paging-report-data-in-a-datalist-or-repeater-control-cs/samples/sample7.cs)]

## <a name="displaying-the-requested-page-of-data"></a>Отображение запрошенной страницы данных

Когда пользователь нажимает одну из кнопок в интерфейсе постраничного просмотра, нам нужно отобразить запрошенную страницу данных. Поскольку параметры разбиения на страницы задаются с помощью строки запроса, чтобы отобразить запрошенную страницу данных используйте `Response.Redirect(url)` иметь браузер пользователя s повторно запросить `Paging.aspx` страницы с соответствующими параметрами разбиения на страницы. Например, для отображения второй страницы данных, будет перенаправлять пользователя для `Paging.aspx?pageIndex=1`.

Для этого создайте `RedirectUser(sendUserToPageIndex)` метод, который перенаправляет пользователя на `Paging.aspx?pageIndex=sendUserToPageIndex`. Затем вызовите этот метод с помощью четыре кнопки `Click` обработчики событий. В `FirstPage` `Click` обработчик событий, вызовите `RedirectUser(0)`, их отправки на первую страницу; в `PrevPage` `Click` обработчик событий, используйте `PageIndex - 1` как индекс страницы; и т. д.


[!code-csharp[Main](paging-report-data-in-a-datalist-or-repeater-control-cs/samples/sample8.cs)]

С `Click` завершить обработчики событий, DataList s записей можно передавать сообщения через, нажимая кнопки. Теперь пора попробуйте!

## <a name="disabling-paging-interface-controls"></a>Отключение подкачки элементы управления интерфейса

В настоящее время включены все четыре кнопки, независимо от того, просматриваемую страницу. Тем не менее мы хотим Отключение кнопки первой и назад при отображении первой страницы данных и кнопок Далее и Дата последнего при отображении на последней странице. `PagedDataSource` Объект, возвращаемый ObjectDataSource s `Select()` метод имеет свойства [ `IsFirstPage` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.pageddatasource.isfirstpage.aspx) и [ `IsLastPage` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.pageddatasource.islastpage.aspx) , можно проверить для определения, если просматривается первой или последней странице данных.

Добавьте следующий элемент управления ObjectDataSource s `Selected` обработчик событий:


[!code-csharp[Main](paging-report-data-in-a-datalist-or-repeater-control-cs/samples/sample9.cs)]

В результате этого добавления кнопки первой и Назад будет отключена при просмотре на первую страницу, пока кнопок Далее и последний будет отключена при просмотре на последней странице.

S позволяют выполнить интерфейс разбиения по страницам, сообщая пользователю, что страниц, они re просмотра в настоящее время и общее количество страниц существует. Добавьте веб-управления Label на страницу и задайте его `ID` свойства `CurrentPageNumber`. Задайте его `Text` свойство в ObjectDataSource s выбранные обработчика событий таким, что он включает текущую просматриваемую страницу (`PageIndex + 1`) и общее количество страниц (`PageCount`).


[!code-csharp[Main](paging-report-data-in-a-datalist-or-repeater-control-cs/samples/sample10.cs)]

На рисунке 10 показано `Paging.aspx` при первом посещении. Так как строка запроса не указаны, DataList по умолчанию — первые четыре продуктами; отключаются кнопки первой и назад. Нажав кнопку Далее отображаются следующие четыре записи (см. рис. 11); включены кнопки первой и назад.


[![Отображается первая страница данных](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image23.png)](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image22.png)

**Рис. 10**: отображается первая страница данных ([Просмотр полноразмерное изображение](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image24.png))


[![Откроется вторая страница данных](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image26.png)](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image25.png)

**Рис. 11**: отображения второй страницы данных ([Просмотр полноразмерное изображение](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image27.png))


> [!NOTE]
> Интерфейс разбиения по страницам можно улучшить, позволяя пользователю указать, сколько страниц для просмотра на одной странице. Например DropDownList может быть добавлен список размеров страницы 5, 10, 25, 50 и все. После выбора размера страницы, пользователь должен попасть на `Paging.aspx?pageIndex=0&pageSize=selectedPageSize`. Я предоставляю, реализацию этого компонента в качестве упражнения для модуля чтения.


## <a name="using-custom-paging"></a>С помощью пользовательское разбиение по страницам

Страницы DataList через свои данные, используя метод разбиения на страницы неэффективным по умолчанию. При разбиении на страницы с большими объемами данных, важно использовать пользовательское разбиение по страницам. Несмотря на то, что сведения о реализации немного отличаются, подходов к реализации пользовательское разбиение по страницам в элементе управления DataList совпадают с разбиением на страницы по умолчанию. Пользовательское разбиение по страницам, использовать `ProductBLL` класса s `GetProductsPaged` метод (вместо `GetProductsAsPagedDataSource`). Как было сказано в [эффективно разбивка на страницы через большие объемы данных](../paging-and-sorting/efficiently-paging-through-large-amounts-of-data-cs.md) учебнике `GetProductsPaged` должен быть передан в начало строки индекса и максимальное число возвращаемых строк. Эти параметры можно поддерживать через так же как querystring `pageIndex` и `pageSize` параметры, используемые по умолчанию разбивка на страницы.

Так как отсутствует s не `PagedDataSource` с пользовательское разбиение по страницам, необходимо использовать альтернативные методы определить общее число страниц с использованием и ли мы re отображение первой или последней странице данных. `TotalNumberOfProducts()` Метод `ProductsBLL` класса возвращает общее количество страниц продуктов. Чтобы определить при просмотре первой страницы данных, проверьте начальный индекс строки, если это значение равно нулю, то первая страница просматривается. Последняя страница просматривается, если индекс начальной строки, а также максимальное число строк, чтобы вернуть больше или равно общему числу страниц.

Мы изучим реализации пользовательское разбиение по страницам более подробно в следующем уроке.

## <a name="summary"></a>Сводка

Хотя DataList ни повторителя предлагает внешней поддержка разбиения на страницы поле найден в GridView, DetailsView и FormView элементы управления, такие функциональные возможности могут быть добавлены с минимальными усилиями. Самый простой способ реализации постраничного просмотра по умолчанию — заключить весь набор продуктов в в `PagedDataSource` и затем привязать `PagedDataSource` DataList или повторителя. В этом учебнике мы добавили `GetProductsAsPagedDataSource` метод `ProductsBLL` класса, чтобы вернуть `PagedDataSource`. `ProductsBLL` Класс уже содержит методы, необходимые для пользовательского разбиения `GetProductsPaged` и `TotalNumberOfProducts`.

А также получение либо точного набора записей для отображения пользовательское разбиение по страницам, либо все записи в `PagedDataSource` по умолчанию, необходимо также вручную добавить интерфейс разбиения по страницам. В этом учебнике мы создали следующий, предыдущий, во-первых, последнего интерфейс четыре кнопки веб-элементами управления. Кроме того был добавлен элемент управления метки, отображающий номер текущей страницы и общее число страниц.

В следующем уроке мы рассмотрим, как добавить поддержку сортировки в DataList и повторителя. Также будет показано, как создать DataList, который может быть одновременно, разбитых на страницы и сортировки (с примерами, с помощью экземпляра по умолчанию и пользовательское разбиение по страницам).

Программирование довольны!

## <a name="about-the-author"></a>Об авторе

[Скотт Митчелл](http://www.4guysfromrolla.com/ScottMitchell.shtml), автор семи ASP/ASP.NET и основателя из [4GuysFromRolla.com](http://www.4guysfromrolla.com), работает с веб-технологиями Майкрософт с 1998 года. Скотт — независимый консультант, trainer и записи. Его последняя книга — [ *диспетчерами учат самостоятельно ASP.NET 2.0 в течение 24 часов*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Он может быть достигнута по [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) или через его блог, который можно найти в [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Благодарности

Этот учебник ряд прошел проверку многие полезные рецензентов. Основными редакторами этого учебника были (Liz Shulok), Алексей Pespisa и Екатерина Leigh. Объясняются моих последующих статей для MSDN? Если Да, напишите мне по [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Вперед](sorting-data-in-a-datalist-or-repeater-control-cs.md)