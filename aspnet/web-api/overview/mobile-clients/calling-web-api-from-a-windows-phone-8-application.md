---
uid: web-api/overview/mobile-clients/calling-web-api-from-a-windows-phone-8-application
title: Вызов веб-API из Windows Phone 8 приложения (C#) | Документация Майкрософт
author: rmcmurray
description: Создание полного сценария end-to-end, состоящий из приложения веб-API ASP.NET, которое содержит каталог книг в приложение Windows Phone 8.
ms.author: riande
ms.date: 10/09/2013
ms.assetid: b9775f41-352a-4f82-baa6-23e95b342e20
msc.legacyurl: /web-api/overview/mobile-clients/calling-web-api-from-a-windows-phone-8-application
msc.type: authoredcontent
ms.openlocfilehash: ca2b5f41f6c3bd38faacd1e15c4dee6f6210aff7
ms.sourcegitcommit: 45ac74e400f9f2b7dbded66297730f6f14a4eb25
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2018
ms.locfileid: "41837502"
---
<a name="calling-web-api-from-a-windows-phone-8-application-c"></a>Вызов веб-API из приложения Windows Phone 8 (C#)
====================
по [(Robert McMurray)](https://github.com/rmcmurray)

В этом руководстве вы узнаете, как создать полного сценария end-to-end, состоящий из приложения веб-API ASP.NET, которое содержит каталог книг в приложение Windows Phone 8.

### <a name="overview"></a>Обзор

Службы rESTful, например веб-API ASP.NET упрощают создание приложений на основе HTTP для разработчиков посредством абстрагирования архитектуры для серверных и клиентских приложений. Вместо того чтобы создавать собственный протокол на базе сокетов для обмена данными, веб-API разработчикам просто нужно опубликовать необходимые методы HTTP для своего приложения (например: GET, POST, PUT, DELETE), и разработчики клиентских приложений необходимо только методы HTTP, которые необходимы для своего приложения.

В этом руководстве end-to-end вы узнаете, как использовать веб-API для создания следующих проектов:

- В [первой части этого учебника](#STEP1), вы создадите приложение веб-API ASP.NET с поддержкой всех операций создания, чтения, обновления и удаления (CRUD) для управления каталогом книги. Это приложение будет использовать [пример XML-файла (books.xml)](https://msdn.microsoft.com/library/windows/desktop/ms762271.aspx) из MSDN.
- В [второй части этого учебника](#STEP2), вы создадите интерактивные приложения Windows Phone 8, которое получает данные из приложения веб-API.

#### <a name="prerequisites"></a>Предварительные требования

- Visual Studio 2013 с Windows Phone 8 установлен пакет SDK
- Windows 8 или более поздней на 64-разрядной системы с Hyper-V установлено
- Список дополнительных требований, см. в разделе *требования к системе* разделе [Windows Phone SDK 8.0](https://www.microsoft.com/download/details.aspx?id=35471) странице загрузки.

> [!NOTE]
> Если нужно проверить возможность подключения между веб-API и проекты Windows Phone 8 в локальной системе, необходимо будет следуйте инструкциям, приведенным в *[подключение к веб-приложений API в локальном эмуляторе Windows Phone 8 Компьютер](https://go.microsoft.com/fwlink/?LinkId=324014)* статью, чтобы настроить среду тестирования.


<a id="STEP1"></a>
### <a name="step-1-creating-the-web-api-bookstore-project"></a>Шаг 1: Создание веб-проекта API Bookstore

Первым шагом данного руководства end-to-end является создание проекта веб-API, который поддерживает все операции CRUD; Обратите внимание на то, что вы добавите в проект приложения Windows Phone с этим решением в [шаг 2](#STEP2) работы с этим руководством.

1. Откройте **Visual Studio 2013**.
2. Нажмите кнопку **файл**, затем **новый**, а затем **проекта**.
3. Когда **новый проект** диалоговое окно отображается, разверните узел **установленные**, затем **шаблоны**, затем **Visual C#**, а затем **Web**.


   | [![](calling-web-api-from-a-windows-phone-8-application/_static/image2.png)](calling-web-api-from-a-windows-phone-8-application/_static/image1.png) |
   |-----------------------------------------------------------------------------------------------------------------------------------------------------|
   |                                                                Щелкните изображение, чтобы развернуть                                                                |


4. Выделите **веб-приложение ASP.NET**, введите **BookStore** для имени проекта и нажмите кнопку **ОК**.
5. Когда **новый проект ASP.NET** отображается диалоговое окно, выберите пункт **веб-API** шаблона, а затем щелкните **ОК**.


   | [![](calling-web-api-from-a-windows-phone-8-application/_static/image4.png)](calling-web-api-from-a-windows-phone-8-application/_static/image3.png) |
   |-----------------------------------------------------------------------------------------------------------------------------------------------------|
   |                                                                Щелкните изображение, чтобы развернуть                                                                |


6. Когда откроется проект веб-API, удалите пример контроллера из проекта:

    1. Разверните **контроллеров** папку в обозревателе решений.
    2. Щелкните правой кнопкой мыши **ValuesController.cs** файла и нажмите кнопку **удалить**.
    3. Нажмите кнопку **ОК** при появлении запроса на подтверждение удаления.
7. Добавление данных XML-файл в проект веб-API; Этот файл содержит содержимое каталога книжного магазина:

   1. Щелкните правой кнопкой мыши **приложения\_данных** папку в обозревателе решений, затем нажмите кнопку **добавить**, а затем нажмите кнопку **новый элемент**.
   2. Когда **Добавление нового элемента** диалоговое окно, выделите **XML-файл** шаблона.
   3. Назовите файл **Books.xml**, а затем нажмите кнопку **добавить**.
   4. Когда **Books.xml** открывается файл, замените код в файле XML из примера **books.xml** файла на сайте MSDN: 

       [!code-xml[Main](calling-web-api-from-a-windows-phone-8-application/samples/sample1.xml)]
   5. Сохраните и закройте XML-файле.

8. Добавление bookstore модели в проект веб-API; Эта модель содержит логику создания, чтения, обновления и удаления (CRUD) для приложения книжного магазина:

   1. Щелкните правой кнопкой мыши **моделей** папку в обозревателе решений, затем нажмите кнопку **добавить**, а затем нажмите кнопку **класс**.
   2. При **Добавление нового элемента** диалоговое окно, назовите файл класса **BookDetails.cs**, а затем нажмите кнопку **добавить**.
   3. Когда **BookDetails.cs** открывается файл, замените код в файле следующим: 

       [!code-csharp[Main](calling-web-api-from-a-windows-phone-8-application/samples/sample2.cs)]
   4. Сохраните и закройте **BookDetails.cs** файла.

9. Добавьте в проект веб-API bookstore контроллера:

   1. Щелкните правой кнопкой мыши **контроллеров** папку в обозревателе решений, затем нажмите кнопку **добавить**, а затем нажмите кнопку **контроллера**.
   2. При **Добавление шаблона** диалоговое окно, выделите **контроллер Web API 2 — пустой**, а затем нажмите кнопку **добавить**.
   3. При **Добавление контроллера** диалоговое окно, назовите контроллер **BooksController**, а затем нажмите кнопку **добавить**.
   4. Когда **BooksController.cs** открывается файл, замените код в файле следующим: 

       [!code-csharp[Main](calling-web-api-from-a-windows-phone-8-application/samples/sample3.cs)]
   5. Сохраните и закройте **BooksController.cs** файла.

10. Постройте приложение веб-API для проверки ошибок.

<a id="STEP2"></a>
### <a name="step-2-adding-the-windows-phone-8-bookstore-catalog-project"></a>Шаг 2: Добавление проекта каталога книжный магазин Windows Phone 8

Следующий шаг в этом сценарии end-to-end — создать каталог приложения для Windows Phone 8. Это приложение будет использовать *Windows Phone с привязкой к данным приложения* шаблон для пользовательского интерфейса по умолчанию, который будет использовать приложение веб-API, который был создан в [шаг 1](#STEP1) этого учебника в качестве источника данных.

1. Щелкните правой кнопкой мыши **BookStore** решение в в обозревателе решений, затем нажмите кнопку **добавить**, а затем **новый проект**.
2. Когда **новый проект** диалоговое окно отображается, разверните **установленные**, затем **Visual C#**, а затем **Windows Phone**.
3. Выделите **Windows Phone с привязкой к данным приложения**, введите **BookCatalog** имя, а затем нажмите кнопку **ОК**.
4. Добавьте в пакет Json.NET NuGet **BookCatalog** проекта:

    1. Щелкните правой кнопкой мыши **ссылки** для **BookCatalog** проекта в обозревателе решений, а затем нажмите кнопку **управление пакетами NuGet**.
    2. Когда **управление пакетами NuGet** диалоговое окно отображается, разверните **Online** , при этом выделите **nuget.org**.
    3. Введите **Json.NET** при поиске поле и щелкните значок поиска.
    4. Выделите **Json.NET** в результаты поиска, а затем выберите **установить**.
    5. После завершения установки, нажмите кнопку **закрыть**.
5. Добавить **BookDetails** модели **BookCatalog** проекта; этот файл содержит общую модель класса книжного магазина:

   1. Щелкните правой кнопкой мыши **BookCatalog** проекта в обозревателе решений, а затем щелкните **добавить**, а затем нажмите кнопку **новую папку**.
   2. Назовите новую папку **моделей**.
   3. Щелкните правой кнопкой мыши **моделей** папку в обозревателе решений, затем нажмите кнопку **добавить**, а затем нажмите кнопку **класс**.
   4. При **Добавление нового элемента** диалоговое окно, назовите файл класса **BookDetails.cs**, а затем нажмите кнопку **добавить**.
   5. Когда **BookDetails.cs** открывается файл, замените код в файле следующим: 

       [!code-csharp[Main](calling-web-api-from-a-windows-phone-8-application/samples/sample4.cs)]
   6. Сохраните и закройте **BookDetails.cs** файла.

6. Обновление **MainViewModel.cs** класса для включения функции для взаимодействия с приложением BookStore веб-API:

   1. Разверните **ViewModels** папку в обозревателе решений, а затем дважды щелкните пункт **MainViewModel.cs** файла.
   2. Когда **MainViewModel.cs** файл открыт, замените код в файле следующим; Обратите внимание на то, что необходимо будет обновить значение `apiUrl` константы фактический URL-адрес веб-API: 

       [!code-csharp[Main](calling-web-api-from-a-windows-phone-8-application/samples/sample5.cs)]
   3. Сохраните и закройте **MainViewModel.cs** файла.

7. Обновление **MainPage.xaml** файл, чтобы настроить имя приложения:

   1. Дважды щелкните **MainPage.xaml** файл в обозревателе решений.
   2. Когда **MainPage.xaml** файл открыт, найдите следующие строки кода: 

       [!code-xml[Main](calling-web-api-from-a-windows-phone-8-application/samples/sample6.xml)]
   3. Замените эти строки следующим кодом: 

       [!code-xml[Main](calling-web-api-from-a-windows-phone-8-application/samples/sample7.xml)]
   4. Сохраните и закройте **MainPage.xaml** файла.

8. Обновление **DetailsPage.xaml** файл, чтобы настроить отображаемые элементы:

   1. Дважды щелкните **DetailsPage.xaml** файл в обозревателе решений.
   2. Когда **DetailsPage.xaml** файл открыт, найдите следующие строки кода: 

       [!code-xml[Main](calling-web-api-from-a-windows-phone-8-application/samples/sample8.xml)]
   3. Замените эти строки следующим кодом: 

       [!code-xml[Main](calling-web-api-from-a-windows-phone-8-application/samples/sample9.xml)]
   4. Сохраните и закройте **DetailsPage.xaml** файла.

9. Создание приложения Windows Phone на наличие ошибок.

### <a name="step-3-testing-the-end-to-end-solution"></a>Шаг 3: Тестирование решения End-to-End

Как упоминалось в *предварительные требования* раздела этого учебника, при тестировании сетевое подключение между веб-API и Windows Phone 8 проектов в локальной системе, вы должны следовать инструкциям *[ Подключение к веб-API приложения на локальном компьютере в эмуляторе Windows Phone 8](https://go.microsoft.com/fwlink/?LinkId=324014)* статью, чтобы настроить среду тестирования.

После настройки среды тестирования, необходимо установить как запускаемый проект приложения Windows Phone. Чтобы сделать это, выделите **BookCatalog** приложения в обозревателе решений и выберите пункт **Назначить запускаемым проектом**:

| [![](calling-web-api-from-a-windows-phone-8-application/_static/image6.png)](calling-web-api-from-a-windows-phone-8-application/_static/image5.png) |
| --- |
| Щелкните изображение, чтобы развернуть |

При нажатии клавиши F5, Visual Studio запустит обоих Windows Phone эмулятор, который будет отображаться &quot;Подождите&quot; сообщения во время извлечения данных приложением из веб-API:

| [![](calling-web-api-from-a-windows-phone-8-application/_static/image8.png)](calling-web-api-from-a-windows-phone-8-application/_static/image7.png) |
| --- |
| Щелкните изображение, чтобы развернуть |

Если все успешно, вы увидите, что отображается каталог:

| [![](calling-web-api-from-a-windows-phone-8-application/_static/image10.png)](calling-web-api-from-a-windows-phone-8-application/_static/image9.png) |
| --- |
| Щелкните изображение, чтобы развернуть |

Если коснуться любой название книги, приложение будет отображать название книги:

| [![](calling-web-api-from-a-windows-phone-8-application/_static/image12.png)](calling-web-api-from-a-windows-phone-8-application/_static/image11.png) |
| --- |
| Щелкните изображение, чтобы развернуть |

Если приложение не может обмениваться данными с веб-API, отображается сообщение об ошибке:

| [![](calling-web-api-from-a-windows-phone-8-application/_static/image14.png)](calling-web-api-from-a-windows-phone-8-application/_static/image13.png) |
| --- |
| Щелкните изображение, чтобы развернуть |

Если коснуться сообщение об ошибке, отобразится дополнительная информация об ошибке:


| [![](calling-web-api-from-a-windows-phone-8-application/_static/image16.png)](calling-web-api-from-a-windows-phone-8-application/_static/image15.png) |
|-------------------------------------------------------------------------------------------------------------------------------------------------------|
|                                                                 Щелкните изображение, чтобы развернуть                                                                 |

