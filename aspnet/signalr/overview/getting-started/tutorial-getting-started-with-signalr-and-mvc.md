---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
title: Учебник. Начало работы с SignalR 2 и MVC 5 | Документация Майкрософт
author: pfletcher
description: Этом руководстве показано, как использовать ASP.NET SignalR 2 для создания приложения разговора в режиме реального времени. Будет добавить в приложение MVC 5 SignalR и создание представления "Разговор"...
ms.author: riande
ms.date: 06/10/2014
ms.assetid: 80bfe5fb-bdfc-41fe-ac43-2132e5d69fac
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
msc.type: authoredcontent
ms.openlocfilehash: 568f82daa67f33736c2bf7a45a3e1339f265c487
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287526"
---
<a name="tutorial-getting-started-with-signalr-2-and-mvc-5"></a>Учебник. Начало работы с SignalR 2 и MVC 5
====================
по [Флетчера Патрик](https://github.com/pfletcher), [Teebken Тим](https://github.com/timlt)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

[Скачать завершенный проект](http://code.msdn.microsoft.com/Getting-Started-with-c366b2f3)

> Этом руководстве показано, как использовать ASP.NET SignalR 2 для создания приложения разговора в режиме реального времени. Следует добавить SignalR в приложение MVC 5 и создать представления чата для отправки и отображения сообщений.
>
> ## <a name="software-versions-used-in-the-tutorial"></a>Версии программного обеспечения, используемые в этом руководстве
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - MVC 5
> - SignalR версии 2
>
>
>
> ## <a name="using-visual-studio-2012-with-this-tutorial"></a>С помощью Visual Studio 2012 с помощью этого руководства
>
>
> Чтобы использовать Visual Studio 2012 с этим руководством, сделайте следующее:
>
> - Обновление вашей [диспетчера пакетов](http://docs.nuget.org/docs/start-here/installing-nuget) до последней версии.
> - Установка [установщик веб-платформы](https://www.microsoft.com/web/downloads/platform.aspx).
> - В установщик веб-платформы, найдите и установите **ASP.NET и Web Tools 2013.1 для Visual Studio 2012**. Это будет установки Visual Studio шаблоны для SignalR классов, таких как **центр**.
> - Некоторые шаблоны (такие как **класс запуска OWIN**) будут недоступны; для этого используйте файл класса.
>
>
> ## <a name="tutorial-versions"></a>Учебника по версии
>
> Сведения о более ранних версий SignalR, см. в разделе [более старых версий SignalR](../older-versions/index.md).
>
> ## <a name="questions-and-comments"></a>Вопросы и комментарии
>
> Оставьте свои отзывы на том, как вам понравилось, и этот учебник и что можно улучшить в комментариях в нижней части страницы. Если у вас есть вопросы, которые не имеют отношения к руководству, их можно разместить [форум по ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) или [StackOverflow.com](http://stackoverflow.com/).


## <a name="overview"></a>Обзор

В этом руководстве описываются разработку в режиме реального времени веб-приложений ASP.NET SignalR 2 и ASP.NET MVC 5. В этом руководстве используется тот же код приложения чата, как [учебнике SignalR Приступая к работе](tutorial-getting-started-with-signalr.md), но показано, как добавить его в приложение MVC 5.

В этом разделе вы узнаете, как следующие задачи разработки SignalR:

- Библиотека SignalR вы добавляете в приложение MVC 5.
- Создание концентратора и классы startup OWIN для отправки содержимого клиентам.
- С помощью библиотеки jQuery SignalR на веб-странице для отправки сообщений и отобразить обновления от концентратора.

На следующем снимке экрана показано приложение завершенного чата, работающих в браузере.

![Экземпляры чата](tutorial-getting-started-with-signalr-and-mvc/_static/image1.png)

Разделы:

- [Настройка проекта](#setup)
- [Запуск образца](#run)
- [Изучите код](#code)
- [Следующие шаги](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a>Настройка проекта

Предварительные требования

- Visual Studio 2013. Если у вас нет Visual Studio, см. в разделе [загрузок ASP.NET](https://www.asp.net/downloads) для получения бесплатной Visual Studio 2013 Express средство разработки.

В этом разделе показано, как создать приложение ASP.NET MVC 5, добавить библиотеку SignalR и создание приложения чата.

1. В Visual Studio создайте приложение ASP.NET на C#, ориентированном на .NET Framework 4.5, назовите его SignalRChat и нажмите кнопку ОК.

    ![Создание веб-](tutorial-getting-started-with-signalr-and-mvc/_static/image2.png)
2. В `New ASP.NET Project` диалоговое окно, а затем выберите **MVC**и нажмите кнопку **изменить способ проверки подлинности**.

    ![Создание веб-](tutorial-getting-started-with-signalr-and-mvc/_static/image3.png)
3. Выберите **без проверки подлинности** в **изменить способ проверки подлинности** окна и нажмите кнопку **ОК**.

    ![Выберите без проверки подлинности](tutorial-getting-started-with-signalr-and-mvc/_static/image4.png)

    > [!NOTE]
    > Если выбрать поставщик проверки подлинности для приложения, `Startup.cs` класса будут создаваться автоматически; не нужно будет создать свой собственный `Startup.cs` класс на шаге 10 ниже.
4. Нажмите кнопку **ОК** в **новый проект ASP.NET** диалоговое окно.
5. Откройте **Сервис > Диспетчер пакетов NuGet > консоль диспетчера пакетов** и выполните следующую команду. Этот шаг добавляет в проект набор файлов сценариев и ссылки на сборки, обеспечивающие функциональные возможности SignalR.

    `install-package Microsoft.AspNet.SignalR`
6. В **обозревателе решений**, разверните папку «скрипты». Обратите внимание на то, что библиотеки скрипта для SignalR были добавлены в проект.

    ![Папка скриптов](tutorial-getting-started-with-signalr-and-mvc/_static/image5.png)
7. В **обозревателе решений**, щелкните правой кнопкой мыши проект, выберите **Add | Новая папка**, и добавьте новую папку с именем **концентраторов**.
8. Щелкните правой кнопкой мыши **концентраторов** папку, нажмите кнопку **Add | Новый элемент**выберите **Visual C# | Web | SignalR** узел в **установленные** области выберите **класс концентратора SignalR (v2)** в центральной области и создать новый концентратор с именем **ChatHub.cs**. Этот класс будет использоваться в качестве концентратора сервера SignalR, которое отправляет сообщения на всех клиентах.

    ![Создание нового концентратора](tutorial-getting-started-with-signalr-and-mvc/_static/image6.png)
9. Замените код в **ChatHub** класса следующим кодом.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample1.cs)]
10. Создайте новый класс с именем Startup.cs. Измените содержимое файла следующим образом.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample2.cs)]
11. Изменить `HomeController` класс найден в **Controllers/HomeController.cs** и добавьте следующий метод к классу. Этот метод возвращает **Chat** представление, которое вы создадите на более позднем этапе.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample3.cs)]
12. Щелкните правой кнопкой мыши **Views/Home** и выберите **добавить... | Представление**.
13. В **Добавление представления** диалоговом окне имя нового представления **Chat**.

    ![Добавление представления](tutorial-getting-started-with-signalr-and-mvc/_static/image7.png)
14. Замените содержимое файла **Chat.cshtml** следующим кодом.

    > [!IMPORTANT]
    > При добавлении SignalR и других библиотек сценариев в проект Visual Studio, диспетчер пакетов может установить версию файла скрипта SignalR, более свежие, чем версия, приведенные в этом разделе. Убедитесь, что ссылку на сценарий в коде совпадает с версией библиотеки сценариев, которые установлены в вашем проекте.

    [!code-cshtml[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample4.cshtml)]
15. **Сохранить все** для проекта.

<a id="run"></a>

## <a name="run-the-sample"></a>Запуск образца

1. Нажмите клавишу F5, чтобы запустить проект в режиме отладки.
2. В адресной строке браузера добавьте **/home/чата** на URL-адрес страницы по умолчанию для проекта. Чат страница загружается в экземпляре обозревателя и предлагает ввести имя пользователя.

    ![Введите имя пользователя](tutorial-getting-started-with-signalr-and-mvc/_static/image8.png)
3. Введите имя пользователя.
4. Скопируйте URL-адрес в адресной строке браузера и использовать его для открытия двух экземпляров дополнительные браузера. В каждом экземпляре обозревателя введите уникальное имя пользователя.
5. В каждом экземпляре браузера добавьте комментарий и нажмите кнопку **отправки**. Комментарии должны отображаться в все экземпляры браузера.

    > [!NOTE]
    > Это приложение на простом чате не ведет контекста обсуждения на сервере. Концентратор осуществляет широковещательную передачу комментарии для всех текущих пользователей. Пользователи, беседа позже будет видеть сообщения добавлены с момента их присоединения к.
6. На следующем снимке экрана показано приложение чата, работающих в браузере.

    ![Браузеры чата](tutorial-getting-started-with-signalr-and-mvc/_static/image9.png)
7. В **обозревателе решений**, проверять **документы скриптов** узел для запущенного приложения. Этот узел является видимым в режиме отладки, если вы используете Internet Explorer как браузер. Файл скрипта с именем **концентраторов** , библиотека SignalR динамически создает во время выполнения. Этот файл управляет обменом данных между jQuery-сценарий и код на стороне сервера. Если вы используете другой браузер Internet Explorer, также можно использовать динамическую **концентраторов** файл, перейдя к нему напрямую, например http://mywebsite/signalr/hubs.

<a id="code"></a>

## <a name="examine-the-code"></a>Изучите код

Приложение чата SignalR демонстрирует две основные задачи разработки SignalR: создание концентратора в качестве основной координации объекта на сервере и с помощью библиотеки jQuery SignalR для отправки и получения сообщений.

### <a name="signalr-hubs"></a>Концентраторы SignalR

В следующем образце кода **ChatHub** класс является производным от **Microsoft.AspNet.SignalR.Hub** класса. Наследование от **концентратора** класс является эффективным методом для построения приложения SignalR. Можно создавать открытые методы в классе концентратора и затем получить доступ к этих методов, вызывая их с помощью сценариев на веб-странице.

В коде чата, клиенты вызывают **ChatHub.Send** метод для отправки нового сообщения. Концентратор в свою очередь отправляет сообщение всем клиентам, вызвав **Clients.All.addNewMessageToPage**.

**Отправки** метод демонстрирует несколько вещей понятия:

- Объявления открытых методов концентратора, таким образом, клиенты могут вызывать их.
- Используйте **Microsoft.AspNet.SignalR.Hub.Clients** свойство для доступа к всех клиентов, подключенных к этому концентратору.
- Вызов функции на стороне клиента (например, `addNewMessageToPage` функции) для обновления клиентов.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample5.cs)]

### <a name="signalr-and-jquery"></a>SignalR и jQuery

**Chat.cshtml** файл представления в примере кода показано, как использовать библиотеку jQuery SignalR для взаимодействия с центром SignalR. Основные задачи в коде создается ссылка на автоматически созданный прокси-сервер для центра, объявление функции, который сервер может обратиться к содержимому Push-уведомлений для клиентов и подключения для отправки сообщений в концентратор.

Следующий код объявляет ссылку на прокси-сервера концентратора.

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample6.js)]

> [!NOTE]
> В JavaScript ссылку на класс сервера и его членах находится в верхний регистр. Ссылается на пример кода C# **ChatHub** класс в JavaScript в виде **chatHub**. Если требуется сослаться `ChatHub` класс в jQuery с обычной Pascal регистр, как это делается в C#, измените файл ChatHub.cs классов. Добавить `using` инструкцию, чтобы ссылаться на `Microsoft.AspNet.SignalR.Hubs` пространства имен. Затем добавьте `HubName` атрибут `ChatHub` классов, например `[HubName("ChatHub")]`. Наконец, обновите jQuery справочной информации для `ChatHub` класса.


Ниже показано, как создать функцию обратного вызова в скрипте. Класс концентратора на сервере вызывает эту функцию для отправки обновлений содержимого для каждого клиента. Дополнительного обращения к `htmlEncode` функция отображает способ HTML кодирования содержимое сообщения перед его отображением на странице, чтобы предотвратить внедрение скрипта.

[!code-html[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample7.html)]

Ниже показано, как открыть соединение с концентратором. Код запускает подключение, а затем передает его функции, чтобы обрабатывать событие щелчка на **отправки** кнопку на данной странице чата.

> [!NOTE]
> Такой подход гарантирует, что соединение установлено, перед выполнением обработчика событий.


[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample8.js)]

<a id="next"></a>

## <a name="next-steps"></a>Следующие шаги

Вы узнали, что SignalR — это платформа для построения в режиме реального времени веб-приложений. Вы также узнали несколько задач разработки SignalR: Добавление в приложение ASP.NET SignalR, как создать класс концентратора и как отправлять и получать сообщения из концентратора.

Пошаговое руководство о том, как развернуть пример приложения SignalR в Azure, см. в разделе [с помощью SignalR с веб-приложениями в службе приложений Azure](../deployment/using-signalr-with-azure-web-sites.md). Подробные сведения о развертывании веб-проекта Visual Studio для веб-сайта Windows Azure см. в разделе [создать веб-приложение ASP.NET в службе приложений Azure](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).

Для более сложных концепций разработки SignalR см. на следующих узлах SignalR исходный код и ресурсы:

- [Проект SignalR](http://signalr.net)
- [SignalR Github и примерами](https://github.com/SignalR/SignalR)
- [Вики-сайте SignalR](https://github.com/SignalR/SignalR/wiki)
