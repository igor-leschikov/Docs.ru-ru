---
uid: web-forms/overview/data-access/working-with-batched-data/batch-inserting-vb
title: "Пакетной вставки (VB) | Документы Microsoft"
author: rick-anderson
description: "Дополнительные сведения о вставке нескольких записей базы данных в одной операции. В уровень пользовательского интерфейса, мы расширить GridView, чтобы разрешить пользователям вводить несколько n..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/26/2007
ms.topic: article
ms.assetid: 48e2a4ae-77ca-4208-a204-c38c690ffb59
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/working-with-batched-data/batch-inserting-vb
msc.type: authoredcontent
ms.openlocfilehash: 3f09b5fd86c1cc6641fb42a466b07da161c1dd35
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2018
---
<a name="batch-inserting-vb"></a>Пакетная вставка (Visual Basic)
====================
по [Скотт Митчелл](https://twitter.com/ScottOnWriting)

[Загрузить код](http://download.microsoft.com/download/3/9/f/39f92b37-e92e-4ab3-909e-b4ef23d01aa3/ASPNET_Data_Tutorial_66_VB.zip) или [скачать PDF](batch-inserting-vb/_static/datatutorial66vb1.pdf)

> Дополнительные сведения о вставке нескольких записей базы данных в одной операции. В уровень пользовательского интерфейса, мы расширить GridView, чтобы разрешить пользователям вводить несколько новых записей. В слое доступа к данным мы вокруг нескольких операций вставки в пределах транзакции обеспечивают выполнение всех операций вставки откат всех операций вставки.


## <a name="introduction"></a>Вступление

В [обновление пакета](batch-updating-vb.md) учебника мы рассматривали Настройка элемента управления GridView для представления интерфейса, где несколько записей были для редактирования. Пользователь, посетив страницу может внести ряд изменений, а затем, с одним нажатием кнопки, выполните пакетного обновления. В ситуациях, когда пользователи часто обновлять много записей за один раз, такой интерфейс можно сохранить бесчисленные щелчков мыши и клавиатуры для мыши контекстных переключений по сравнению с по умолчанию построчные возможности редактирования, которые сначала были изучены обратно в [ Общие сведения о вставке, обновление и удаление данных](../editing-inserting-and-deleting-data/an-overview-of-inserting-updating-and-deleting-data-vb.md) учебника.

Эта концепция могут также применяться при добавлении записей. Предположим, что здесь в компании Northwind Traders мы обычно получают отгрузок от поставщиков, которые содержат количество продуктов для определенной категории. Например мы получили отгрузки шесть разных чая и кофе продуктов из Tokyo Traders. Если пользователь вводит шесть одного продукта во время с помощью элемента управления DetailsView, их нужно снова и снова выберите многие одинаковые значения: необходимо будет выбрать одной категории (Напитки) одного поставщика (Tokyo Traders), то неподдерживаемых (значение Значение false) и тем же единицам на порядок значение (0). Эта запись повторяющихся данных не только много времени, но может привести к ошибкам.

С минимальными усилиями можно создать пакет, вставка интерфейс, позволяющий пользователю выбрать поставщика и категории один раз, ввести ряд названий продуктов и цен, а затем нажмите кнопку Добавление новых продуктов в базе данных (см. рис. 1). При добавлении каждого продукта, его `ProductName` и `UnitPrice` поля данных назначаются значения, введенные в текстовые поля, пока его `CategoryID` и `SupplierID` значения присваиваются значения с элементами управления DropDownList в верхнем fo формы. `Discontinued` И `UnitsOnOrder` значения присваиваются значения жестко `False` и 0 соответственно.


[![Интерфейс пакетной вставки](batch-inserting-vb/_static/image2.png)](batch-inserting-vb/_static/image1.png)

**Рис. 1**: пакетной вставки интерфейс ([Просмотр полноразмерное изображение](batch-inserting-vb/_static/image3.png))


В этом учебнике мы создадим страницу, которая реализует интерфейсам, приведенный на рисунке 1 пакета. Как с двух предыдущих занятий, мы будет переносить операции вставки в пределах транзакции, чтобы гарантировать атомарность. S позволяют начать работу!

## <a name="step-1-creating-the-display-interface"></a>Шаг 1: Создание интерфейса

Этот учебник состоит из одной страницы, разделенное на две области: область отображения и область вставки. Интерфейса, который мы создадим на этом шаге, показаны продукты в элементе управления GridView и включает кнопку с названием продукта отгрузки процесса. При нажатии этой кнопки интерфейса заменяется интерфейса вставки, как показано на рис. 1. Возвращает интерфейса после добавить продукты из отгрузки или нажатии кнопки "Отмена". Мы создадим интерфейса вставки на шаге 2.

При создании страницы, использующей два интерфейса, одновременно только одна из которых будет видима, каждый интерфейс обычно помещается в [веб-панель управления](http://www.w3schools.com/aspnet/control_panel.asp), который используется в качестве контейнера для других элементов управления. Таким образом нашу страницу будет иметь два элемента управления панели один для каждого интерфейса.

Сначала откройте `BatchInsert.aspx` страницы в `BatchData` папки и перетащите элемент управления из области элементов в конструктор (см. рис. 2). Задайте для свойства панели s `ID` свойства `DisplayInterface`. При добавлении панели в конструкторе, его `Height` и `Width` задано значение 50px и 125px, соответственно. Очистить значения этих свойств в окне свойств.


[![Перетащите элемент управления из области элементов в конструктор](batch-inserting-vb/_static/image5.png)](batch-inserting-vb/_static/image4.png)

**На рисунке 2**: перетащите элемент управления из области элементов в конструктор ([Просмотр полноразмерное изображение](batch-inserting-vb/_static/image6.png))


Затем перетащите элемент управления Button и GridView в панель. Настроить кнопку s `ID` свойства `ProcessShipment` и его `Text` свойства процесса отгрузки продукции. Набор GridView s `ID` свойства `ProductsGrid` и его смарт-теге, привязать его к новой ObjectDataSource с именем `ProductsDataSource`. Настройка ObjectDataSource для извлечения данных из `ProductsBLL` класса s `GetProducts` метод. Поскольку это GridView используется только для отображения данных, установите раскрывающихся списков в UPDATE, INSERT и удаление вкладок (нет). Нажмите кнопку Готово, чтобы завершить работу мастера настройки источника данных.


[![Просмотреть данные, возвращаемые из метода GetProducts класса ProductsBLL s](batch-inserting-vb/_static/image8.png)](batch-inserting-vb/_static/image7.png)

**Рис. 3**: отображения данных, возвращенных из `ProductsBLL` класса s `GetProducts` метод ([Просмотр полноразмерное изображение](batch-inserting-vb/_static/image9.png))


[![Установите раскрывающихся списков в UPDATE, INSERT и удаление вкладок (нет)](batch-inserting-vb/_static/image11.png)](batch-inserting-vb/_static/image10.png)

**Рис. 4**: задать раскрывающихся списков в обновления, вставки и удаления вкладок (нет) ([Просмотр полноразмерное изображение](batch-inserting-vb/_static/image12.png))


После завершения работы мастера ObjectDataSource Visual Studio добавит стояли и CheckBoxField для полей данных продукта. Удалите все, кроме `ProductName`, `CategoryName`, `SupplierName`, `UnitPrice`, и `Discontinued` поля. Вы можете внести любые изменения внешнего вида. Я решил форматирования `UnitPrice` поля в виде значения валюты, изменять расположение полей, а также некоторые поля `HeaderText` значения. Кроме того, настройте GridView, чтобы включить разбиение по страницам и сортировка поддержки, установив флажки включить разбиения на страницы и включить сортировку в GridView s смарт-тег.

После добавления элементов управления панели, кнопки, GridView и ObjectDataSource и настройка полей s GridView, на странице s должна выглядеть следующим образом:


[!code-aspx[Main](batch-inserting-vb/samples/sample1.aspx)]

Обратите внимание, что отображаются разметку для кнопки и GridView между открывающим и закрывающим `<asp:Panel>` тегов. Поскольку эти элементы управления находятся в пределах `DisplayInterface` панель, мы их можно скрыть, просто задав панели s `Visible` свойства `False`. Шаг 3 просматривает программного изменения панели s `Visible` щелкните свойство в ответ на нажатие кнопки, чтобы показать один интерфейс скрывая другой.

Теперь пора просмотрите ход работы через браузер. Как показано на рисунке 5, вы увидите кнопку отгрузки продукции процесса выше GridView, в которой перечислены продукты десять одновременно.


[![GridView перечислены продукты и предлагает сортировки и разбиения по страницам](batch-inserting-vb/_static/image14.png)](batch-inserting-vb/_static/image13.png)

**Рис. 5**: GridView перечислены продукты и обеспечивает сортировку и разбиение по страницам возможности ([Просмотр полноразмерное изображение](batch-inserting-vb/_static/image15.png))


## <a name="step-2-creating-the-inserting-interface"></a>Шаг 2: Создание интерфейса вставки

С помощью интерфейса завершена, мы готов для создания интерфейса вставки. В этом учебнике позволяют создать интерфейс для вставки, запрашивает значение single поставщика и категории, а затем позволяет пользователю ввести до пяти продуктов имена и значения цены единицы s. С помощью этого интерфейса пользователь может добавить одной до пяти новые продукты, все имеют те же категории и поставщика, но иметь уникальный код продукта названия и цены.

Пуск, перетащив панели из области элементов в конструктор, поместив ее под существующий `DisplayInterface` панель. Задать `ID` этого добавления панели `InsertingInterface` и задайте его `Visible` свойства `False`. Мы добавим код, который задает `InsertingInterface` панель s `Visible` свойства `True` на шаге 3. Также очистить панель s `Height` и `Width` значения свойств.

Теперь нам нужны для создания интерфейса вставки, показанном обратно на рис. 1. Этот интерфейс может создаваться с помощью разных методов HTML, но мы будем использовать ее очень просто: семь строк и четырех столбцов, таблица.

> [!NOTE]
> При вводе разметки HTML `<table>` элементов, я предпочитаю использовать представление источника. Хотя Visual Studio имеет средства для добавления `<table>` элементов с помощью конструктора, конструктор кажется все слишком пойти для вставки незапрошенный для `style` параметры в разметку. После создания `<table>` разметки, обычно возврата в конструкторе, чтобы добавить веб-элементов управления и задайте их свойства. При создании таблиц с предварительно определенный столбцами и строками предпочтительно в статическом формате HTML, а не [управления веб-таблица](https://msdn.microsoft.com/library/system.web.ui.webcontrols.table.aspx) так, как веб-элементов управления, размещенных в таблице веб-элемент управления может осуществляться только с помощью `FindControl("controlID")` шаблон. Таблица веб-элементов управления тем не менее, использовать для таблиц, динамически размера (то есть, строк или столбцов на основе некоторые базы данных или определяемый пользователем критериям), с момента веб-таблица, элемент управления может быть создан программным образом.


Введите следующую разметку в `<asp:Panel>` теги `InsertingInterface` панели:


[!code-html[Main](batch-inserting-vb/samples/sample2.html)]

Это `<table>` разметки не включает все веб-элементов управления, пока мы добавим те моментально. Обратите внимание, что каждый `<tr>` элемент содержит определенный параметр класс CSS: `BatchInsertHeaderRow` для строки заголовка, куда будут направлены поставщика и категории элементами управления DropDownList; `BatchInsertFooterRow` строкой нижнего колонтитула, куда будут направлены добавить продукты из отгрузки и Отмена кнопки; и чередующихся `BatchInsertRow` и `BatchInsertAlternatingRow` значения для строк, которые будут содержать продукта и единицы измерения по цене элемента управления TextBox. Я хранить созданные соответствующие классы CSS в `Styles.css` файл для предоставления интерфейса вставки интерфейсом, аналогичным интерфейсу GridView и DetailsView управляет, мы хранить, используемые в этих учебниках. Ниже приведены эти классы CSS.


[!code-css[Main](batch-inserting-vb/samples/sample3.css)]

Эта разметка введены возвращается в режим конструктора. Это `<table>` должно отображаться как семь строк и четырех столбцов, таблицы в конструкторе, как показано на рис. 6.


[![Вставка интерфейса состоит из четырех столбцов, семь строки таблицы](batch-inserting-vb/_static/image17.png)](batch-inserting-vb/_static/image16.png)

**Рис. 6**: вставка интерфейс состоит из четырех столбцов, Таблица 7 строкой ([Просмотр полноразмерное изображение](batch-inserting-vb/_static/image18.png))


Мы повторно теперь готов для добавления веб-элементов управления в интерфейсе вставки. Перетащите двумя элементами управления DropDownList из области элементов в соответствующие ячейки в таблице один поставщик и один для категории.

Задайте сведения о поставщике DropDownList s `ID` свойства `Suppliers` и привязать его к новой ObjectDataSource с именем `SuppliersDataSource`. Настройка нового ObjectDataSource для извлечения данных из `SuppliersBLL` класса s `GetSuppliers` метод и обновление набора вкладку s раскрывающегося списка (нет). Нажмите кнопку Готово, чтобы завершить работу мастера.


[![Настройка ObjectDataSource можно использовать метод GetSuppliers класса SuppliersBLL s](batch-inserting-vb/_static/image20.png)](batch-inserting-vb/_static/image19.png)

**Рис. 7**: Настройка ObjectDataSource для использования `SuppliersBLL` класса s `GetSuppliers` метод ([Просмотр полноразмерное изображение](batch-inserting-vb/_static/image21.png))


У `Suppliers` отображения DropDownList `CompanyName` поля данных и использование `SupplierID` поля данных в качестве его `ListItem` s значений.


[![Отобразить поле CompanyName данных и использовать в качестве значения SupplierID](batch-inserting-vb/_static/image23.png)](batch-inserting-vb/_static/image22.png)

**Рис. 8**: отображение `CompanyName` поля данных и использование `SupplierID` как значение ([Просмотр полноразмерное изображение](batch-inserting-vb/_static/image24.png))


Имя второй DropDownList `Categories` и привязать его к новой ObjectDataSource с именем `CategoriesDataSource`. Настройка `CategoriesDataSource` ObjectDataSource для использования `CategoriesBLL` класса s `GetCategories` метод; набор, в раскрывающемся списке перечислены вкладки UPDATE и DELETE (нет) и нажмите кнопку "Готово" для завершения работы мастера. Наконец, иметь отображения DropDownList `CategoryName` поля данных и использование `CategoryID` как значение.

После добавления и привязан к соответствующим образом настроенных ObjectDataSources этими двумя элементами управления DropDownList экран должен выглядеть аналогично рис. 9.


[![Строка заголовка теперь содержит поставщики и элементами управления DropDownList категорий](batch-inserting-vb/_static/image26.png)](batch-inserting-vb/_static/image25.png)

**Рис. 9**: заголовок строки теперь содержит `Suppliers` и `Categories` элементами управления DropDownList ([Просмотр полноразмерное изображение](batch-inserting-vb/_static/image27.png))


Теперь необходимо создать текстовые поля для сбора, имя и цену для каждого нового продукта. Перетащите элемент управления TextBox из области элементов в конструктор для каждой из пяти продуктов название и цену строк. Задать `ID` свойств текстовых полей к `ProductName1`, `UnitPrice1`, `ProductName2`, `UnitPrice2`, `ProductName3`, `UnitPrice3`, и т. д.

Добавить элемент управления CompareValidator после каждого цена текстовые поля, установка `ControlToValidate` свойство к соответствующему `ID`. Также задать `Operator` свойства `GreaterThanEqual`, `ValueToCompare` 0, и `Type` для `Currency`. Эти параметры указывают CompareValidator для убедитесь, что цена, если указано, значение валют, больше или равно нулю. Задать `Text` свойства \*, и `ErrorMessage` цену должен быть больше или равно нулю. Кроме того можно опустить любых символов валют.

> [!NOTE]
> Интерфейс для вставки не включает все элементы управления RequiredFieldValidator, даже если `ProductName` в `Products` таблицы базы данных не допускает `NULL` значения. Это, поскольку мы хотим позволить пользователю ввести до пяти продуктов. Например если пользователь для обеспечения продукта название и цену за единицу для первых трех строк, если оставить пустым, последних двух строк мы d просто добавьте три новых продуктов в системе. Поскольку `ProductName` — требуется, однако нам потребуется Программная проверка, чтобы убедиться, что если цена единицы вводится предоставление соответствующее значение имени продукта. Мы исследуем в этом выпуске эта проверка на шаге 4.


Если проверка ввода пользователя s, CompareValidator сообщает недопустимые данные, если значение содержит символ валюты. Добавьте $ перед каждой цена текстовые поля в качестве визуальная подсказка, которая указывает, что пользователю пропустить обозначение денежной единицы, при вводе цены.

И, наконец, добавьте элемент управления ValidationSummary в `InsertingInterface` панель параметров его `ShowMessageBox` свойства `True` и его `ShowSummary` свойства `False`. Эти параметры Если пользователь вводит значение цены Недопустимая единица измерения, будет отображаться звездочка рядом с нарушением. элементы управления TextBox и управления ValidationSummary отобразит messagebox клиентские, показано сообщение об ошибке, которую мы указали ранее.

На этом этапе экран должен выглядеть примерно на рис. 10.


[![Интерфейс для вставки теперь включает текстовые поля для продуктов, названия и цены](batch-inserting-vb/_static/image29.png)](batch-inserting-vb/_static/image28.png)

**Рис. 10**: вставка интерфейса теперь включает текстовые поля для продуктов названия и цены ([Просмотр полноразмерное изображение](batch-inserting-vb/_static/image30.png))


Далее нам нужно добавить добавить продукты из кнопок "отгрузки" и "Отмена" в строке нижнего колонтитула. Перетащите две кнопки элементы управления из области элементов в нижний колонтитул интерфейсе вставки параметра кнопки `ID` свойства `AddProducts` и `CancelButton` и `Text` свойства, чтобы добавить продукты из отгрузки и "Отмена", соответственно. Кроме того, задать `CancelButton` управления s `CausesValidation` свойства `false`.

Наконец необходимо добавить метки веб-элемент управления, который будет отображать сообщения о состоянии для двух интерфейсов. Например пользователь успешно добавляет новую отгрузку продуктов, мы хотим, чтобы вернуться к интерфейса и отображать сообщение с подтверждением. Если, однако пользователь предоставляет цену для нового продукта, но оставляет off название продукта, нам нужно отображать предупреждающее сообщение с момента `ProductName` поле является обязательным. Так как нам нужно это сообщение, отображаемое для обоих интерфейсов, поместите его в верхней части страницы без панелей.

Перетащите метку веб-элемент управления из панели элементов в верхнюю часть страницы в режиме конструктора. Задать `ID` свойства `StatusLabel`снимите out `Text` свойства, а также установите `Visible` и `EnableViewState` свойства `False`. Как было показано в предыдущих учебниках, установка `EnableViewState` свойства `False` позволяет программно изменять значения свойств подписи s и их автоматически восстановить их значения по умолчанию на последующую обратную передачу. Это упрощает код для отображения сообщения о состоянии в ответ на некоторое действие пользователя, исчезнет при последующих обратной передаче. Задайте `StatusLabel` управления s `CssClass` свойства для предупреждения, что имя класса CSS, определенного в `Styles.css` в больших, курсив, шрифт, который отображает текст.

Рис. 11 показана конструктора Visual Studio после метки был добавлен и настроен.


[![Поместить элемент управления StatusLabel выше двух элементов панели управления](batch-inserting-vb/_static/image32.png)](batch-inserting-vb/_static/image31.png)

**Рис. 11**: месте `StatusLabel` выше две панели элементов управления элемента управления ([Просмотр полноразмерное изображение](batch-inserting-vb/_static/image33.png))


## <a name="step-3-switching-between-the-display-and-inserting-interfaces"></a>Шаг 3: Переключение между отображением и вставка интерфейсов

На этом этапе завершена разметку для наших отображения и вставка интерфейсов, но мы re по-прежнему остается две задачи:

- Переключение между отображением и вставка интерфейсов
- Добавление продуктов в посылку в базу данных

В настоящее время интерфейса отображается, но будет отображаться интерфейса вставки. Это из-за `DisplayInterface` панель s `Visible` свойству `True` (значение по умолчанию), во время `InsertingInterface` панель s `Visible` свойству `False`. Для переключения между двумя интерфейсами, нам просто нужно переключить каждого элемента управления s `Visible` значение свойства.

Мы хотим переместить из интерфейса в интерфейсе вставки при нажатии кнопки процесса отгрузки продукции. Таким образом, создайте обработчик событий для кнопки s `Click` событие, которое содержит следующий код:


[!code-vb[Main](batch-inserting-vb/samples/sample4.vb)]

Этот код просто скрывает `DisplayInterface` панели и показывает `InsertingInterface` панель.

Создайте обработчики событий для добавления продуктов из отгрузки и кнопку Отмена элементов управления в интерфейсе вставки. При нажатии любой из этих кнопок, необходимо вернуться к интерфейса. Создание `Click` обработчики событий для обоих кнопки, элементы управления, чтобы они вызывают `ReturnToDisplayInterface`, метод, мы добавим моментально. Помимо скрытие `InsertingInterface` панели и отображение `DisplayInterface` панели `ReturnToDisplayInterface` метод необходимо вернуться к состоянию до редактирования веб-элементов управления. При этом задается элементами управления DropDownList `SelectedIndex` свойства 0 и очистке `Text` свойства элементов управления TextBox.

> [!NOTE]
> Рассмотрим, что может произойти, если мы не существовал t вернуться в состояние до редактирования перед возвратом интерфейса. Пользователь может отгрузки продукции процесса нажмите кнопку, введите продукты из отгрузки и нажмите кнопку Добавить продукты из отгрузки. Это следует добавить продукты и вернуть интерфейса пользователя. На этом этапе пользователь может потребоваться добавить другой посылкой. При нажатии кнопки процесса отгрузки продукции, возвращаемыми интерфейса вставки, но DropDownList выбранные параметры и значения в текстовое поле будет по-прежнему заполняться первоначальные значения.


[!code-vb[Main](batch-inserting-vb/samples/sample5.vb)]

Оба `Click` просто вызывает обработчики событий `ReturnToDisplayInterface` метода, несмотря на то, что мы вернемся к продуктам добавить из отгрузки `Click` обработчик событий в шаг 4 и добавить код для сохранения продукты. `ReturnToDisplayInterface`Запускает, возвращая `Suppliers` и `Categories` элементами управления DropDownList для их сначала параметров. Две константы `firstControlID` и `lastControlID` пометить начального и конечного значений индекса элемента управления, используемого для именования продукта название и цену за единицу текстовые поля в Вставка интерфейса и используются в границах `For` цикл, который задает `Text`свойства элементов управления TextBox обратно в пустую строку. Наконец, панелей `Visible` свойства сбрасываются, позволяющие интерфейса вставки является скрытым и интерфейса показано.

Теперь пора проверить эту страницу в браузере. При первом просмотре странице вы увидите интерфейса, как было показано на рис. 5. Нажмите кнопку процесса отгрузки продукции. Будет обратной передачи страницы, и вы увидите интерфейса вставки как показано на рис. 12. Щелкнув либо добавить продукты из отгрузки или "Отмена" снова интерфейса.

> [!NOTE]
> При просмотре интерфейса вставки, теперь пора проверить CompareValidators на цену за единицу текстовые поля. Вы увидите messagebox клиентские предупреждение при нажатии кнопки Добавить продукты из цен с значение меньше нуля или отгрузки кнопка с недопустимым денежных значений.


[![Интерфейс вставки отображается после нажатия кнопки отгрузки продукции процесса](batch-inserting-vb/_static/image35.png)](batch-inserting-vb/_static/image34.png)

**Рис. 12**: вставка интерфейс отображается после нажатия кнопки отгрузки продукции процесса ([Просмотр полноразмерное изображение](batch-inserting-vb/_static/image36.png))


## <a name="step-4-adding-the-products"></a>Шаг 4: Добавление продуктов

Этот учебник предназначен для сохранения продукты в базу данных в новый товар из s отгрузки кнопка остается `Click` обработчика событий. Это можно сделать путем создания `ProductsDataTable` и добавление `ProductsRow` для всех указанных имен продуктов. После этих `ProductsRow` , которые были добавлены мы выполним вызов `ProductsBLL` класса s `UpdateWithTransaction` передачи в метод `ProductsDataTable`. Помните, что `UpdateWithTransaction` метод, который был создан в [упаковки изменения базы данных в рамках транзакции](wrapping-database-modifications-within-a-transaction-vb.md) «учебник», передает `ProductsDataTable` для `ProductsTableAdapter` s `UpdateWithTransaction` метод. После этого запускается транзакции ADO.NET и проблемы TableAdatper `INSERT` инструкцию в базе данных для каждого добавленного `ProductsRow` в DataTable. Предположим, что все продукты добавляются без ошибок, транзакция фиксируется, в противном случае она откатывается назад.

Код для добавить продукты из кнопки отгрузки s `Click` обработчик событий также требуется выполнить проверку ошибок немного. Так как существуют не RequiredFieldValidators, используемых в интерфейсе вставки, пользователь может ввести цену для продукта во время его имя. Поскольку имя продукта s является обязательным, если таких условий развертывания необходимо предупредить пользователя и не продолжить выполнение операции вставки. Полный `Click` код обработчика событий следующим:


[!code-vb[Main](batch-inserting-vb/samples/sample6.vb)]

Обработчик события запускается, проверьте `Page.IsValid` свойство возвращает значение `True`. Если он возвращает `False`, это означает, что один или несколько CompareValidators указаны недопустимые данные; в этом случае мы не хотим попытки вставить введенное продуктов или мы получим исключение при попытке назначить пользователь ввел цена значение `ProductsRow` s `UnitPrice` свойство.

Далее, новый `ProductsDataTable` создается экземпляр (`products`). Объект `For` цикл используется для итерации по продукту название и цену за единицу текстовые поля и `Text` свойства считываются в локальных переменных `productName` и `unitPrice`. Если введенное пользователем значение цены единицы, но не имя соответствующего продукта `StatusLabel` отображает сообщение при указании цену единицы, вы должны также включать имя продукта и выходе из обработчика событий.

Если указано имя продукта, когда новый `ProductsRow` экземпляр создается с помощью `ProductsDataTable` s `NewProductsRow` метод. Этот новый `ProductsRow` экземпляра s `ProductName` свойству текущего продукта имя текстового поля при `SupplierID` и `CategoryID` свойства, назначенные для `SelectedValue` свойства элементами управления DropDownList в заголовке Вставка интерфейса s. Если пользователем введено значение цена продукта s, он не будет назначен `ProductsRow` экземпляра s `UnitPrice` свойство; в противном случае свойство влево без значения, что приведет к `NULL` значение для `UnitPrice` в базе данных. Наконец `Discontinued` и `UnitsOnOrder` свойства присваиваются значения жестко `False` и 0 соответственно.

После назначения свойства `ProductsRow` добавляется в экземпляр `ProductsDataTable`.

По завершении `For` цикла, мы проверяем, были ли добавлены все продукты. Пользователь может в конце концов, щелкнул добавить продукты из отгрузки перед вводом все названия продуктов или цены. Если имеется хотя бы один продукт в `ProductsDataTable`, `ProductsBLL` класса s `UpdateWithTransaction` вызывается метод. Далее, привязываются к `ProductsGrid` GridView для отображения продуктов, добавленный в интерфейса. `StatusLabel` Обновляется и отображает сообщение с подтверждением и `ReturnToDisplayInterface` вызывается, скрытие Вставка интерфейса и отображение интерфейса.

Если продукты не были введены, но сообщение, которое продукты не были добавлены отображается интерфейс для вставки. Введите имена продуктов, цены единицы в текстовых полях отображается.

Рисунок 13, 14 до 15 Показать Вставка и отображать интерфейсы в действии. На рис. 13 пользователь ввел значение цены единицы без соответствующего имени продукта. Рис. 14 показаны интерфейса после трех новых продуктов были успешно добавлены, а на рис. 15 показано двух продуктов, добавленный в GridView (третье — на предыдущей странице).


[![Название продукта-это требуется при вводе цена единицы](batch-inserting-vb/_static/image38.png)](batch-inserting-vb/_static/image37.png)

**Рис. 13**: название продукта —, необходимый при вводе цену единицы ([Просмотр полноразмерное изображение](batch-inserting-vb/_static/image39.png))


[![Для поставщика были добавлены три новые Veggies Mayumi s](batch-inserting-vb/_static/image41.png)](batch-inserting-vb/_static/image40.png)

**Рис. 14**: три новых Veggies были добавлены для поставщика Mayumi s ([Просмотр полноразмерное изображение](batch-inserting-vb/_static/image42.png))


[![Новые продукты можно найти на последней странице элемента управления GridView](batch-inserting-vb/_static/image44.png)](batch-inserting-vb/_static/image43.png)

**Рис. 15**: новых продуктов можно найти на последней странице GridView ([Просмотр полноразмерное изображение](batch-inserting-vb/_static/image45.png))


> [!NOTE]
> Вставка логику, используемую в этом учебнике пакета перенос операции вставки внутри области транзакции. Чтобы проверить это, намеренно вызвать ошибки уровня базы данных. Например, вместо того чтобы назначение нового `ProductsRow` экземпляра s `CategoryID` свойство для значения, выбранного в `Categories` DropDownList, назначить его значение, например `i * 5`. Здесь `i` является индексатором цикла и содержит значения в диапазоне от 1 до 5. Таким образом, при добавлении двух или более продуктов в пакете вставки первого продукта получит допустимое `CategoryID` будет иметь значение (5), но последующие продукты `CategoryID` значения, которые не соответствуют до `CategoryID` значения в `Categories` таблицы. В итоге получается, во время первого `INSERT` будет выполнена успешно, последующие условия, будут завершаться нарушение ограничения внешнего ключа. Так как Пакетная вставка является атомарной, первый `INSERT` будет выполнен откат, возвращение базы данных в состояние перед пакетной вставки процесс начала.


## <a name="summary"></a>Сводка

В этом и предыдущем два учебника мы создали интерфейсы, позволяющие для обновления, удаления, и вставлять пакеты данных, которые использовать поддержку транзакций, мы добавили в слое доступа к данным в [упаковки изменения базы данных в рамках транзакции](wrapping-database-modifications-within-a-transaction-vb.md) учебника. Для некоторых сценариев, такие интерфейсы пользователя пакетной обработки значительно повысить эффективность работы конечных пользователей в результате разбивки вниз на количество щелчков, обратных передач и переключение контекста мышь клавиатуры, сохраняя целостность базовых данных.

Этот учебник завершает внешнего вида в работе с данными в пакетном режиме. Следующий набор учебников более подробно рассматривается ряд сложных сценариев доступа к данным, включая использование хранимых процедур в методах s TableAdapter, Настройка параметров на уровне подключения и команды в DAL, шифрования строк подключения и многое другое!

Программирование довольны!

## <a name="about-the-author"></a>Об авторе

[Скотт Митчелл](http://www.4guysfromrolla.com/ScottMitchell.shtml), автор семи ASP/ASP.NET и основателя из [4GuysFromRolla.com](http://www.4guysfromrolla.com), работает с веб-технологиями Майкрософт с 1998 года. Скотт — независимый консультант, trainer и записи. Его последняя книга — [ *диспетчерами учат самостоятельно ASP.NET 2.0 в течение 24 часов*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Он может быть достигнута по [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) или через его блог, который можно найти в [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Благодарности

Этот учебник ряд прошел проверку многие полезные рецензентов. Привести рецензентов для этого учебника были – Хилтон Гизнау и S ren Алексей Lauritsen. Объясняются моих последующих статей для MSDN? Если Да, напишите мне по [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Назад](batch-deleting-vb.md)