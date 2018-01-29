---
title: "Непрерывное развертывание в Azure с помощью Visual Studio и Git"
author: rick-anderson
description: "Сведения о создании веб-приложения ASP.NET Core с помощью Visual Studio и его развертывании в службе приложений Azure с использованием Git для непрерывного развертывания."
ms.author: riande
manager: wpickett
ms.custom: mvc
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: c1d25b109bbf211eb476860ff77b649565960b62
ms.sourcegitcommit: 12e5194936b7e820efc5505a2d5d4f84e88eb5ef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2018
---
# <a name="continuous-deployment-to-azure-for-aspnet-core-with-visual-studio-and-git"></a>Непрерывное развертывание в Azure ASP.NET Core с Visual Studio и Git

Автор: [Эрик Рейтан](https://github.com/Erikre) (Erik Reitan)

Этого учебника показано, как создать веб-приложение ASP.NET Core с помощью Visual Studio и развернуть ее на службе приложений Azure из Visual Studio с помощью непрерывного развертывания.

См. также статью [Использование VSTS для сборки и публикации в веб-приложении Azure с помощью непрерывного развертывания](/vsts/build-release/archive/apps/aspnet/aspnet-4-ci-cd-azure-automatic), в которой показано, как настроить рабочий процесс непрерывной поставки для [службы приложений Azure](/azure/app-service/app-service-web-overview) с помощью Visual Studio Team Services. Azure непрерывной доставки в Team Services упрощает настройку конвейера высокого уровня надежности системы, для публикации обновлений для приложений, размещенных в службе приложений Azure. Конвейер можно настроить на портале Azure для создания, выполнения тестов, развертывания промежуточный слот и затем развернуть в рабочей среде.

> [!NOTE]
> Для работы с этим учебником требуется учетная запись Microsoft Azure. Чтобы получить учетную запись, [активировать преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) или [зарегистрироваться для получения бесплатной пробной версии](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Предварительные требования

В этом учебнике предполагается, что установлено следующее программное обеспечение:

* [Visual Studio](https://www.visualstudio.com)
* [Пакет SDK для .NET core](https://www.microsoft.com/net/download/core) (среда выполнения и средства)
* [Git](https://git-scm.com/downloads) для Windows.

## <a name="create-an-aspnet-core-web-app"></a>Создание веб-приложения ASP.NET Core

1. Запустите Visual Studio.

1. В меню **Файл** выберите пункт **Создать** > **Проект**.

1. Выберите шаблон проекта **Веб-приложение ASP.NET Core**. Он находится в разделе **Installed** (Установлено) > **Templates** (Шаблоны) > **Visual C#** > **.NET Core**. Задайте для проекта имя `SampleWebAppDemo`. Выберите параметр **Создать репозиторий Git** и нажмите кнопку **ОК**.

   ![Диалоговое окно создания нового проекта](azure-continuous-deployment/_static/01-new-project.png)

1. В диалоговом окне **New ASP.NET Core Project** (Новый проект ASP.NET Core) выберите **пустой** шаблон и щелкните **ОК**.

   ![Диалоговое окно "Новый проект ASP.NET"](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> Последний выпуск .NET Core является версия 2.0.

### <a name="running-the-web-app-locally"></a>Запуск веб-приложения в локальной системе

1. После того как Visual Studio завершит создание приложения, запустите его, выбрав **Отладка** > **Начать отладку**. В качестве альтернативы нажать **F5**.

   Инициализация Visual Studio и нового приложения может занять некоторое время. После завершения браузер показано выполняющееся приложение.

   ![Окно браузера с выполняющимся приложением, в котором выводится сообщение "Hello World!"](azure-continuous-deployment/_static/04-browser-runapp.png)

1. Ознакомившись с запущенной веб-приложения, закройте браузер и щелкните значок «Остановить отладку» на панели инструментов Visual Studio, чтобы остановить выполнение приложения.

## <a name="create-a-web-app-in-the-azure-portal"></a>Создание веб-приложения на портале Azure

Следующие шаги создания веб-приложения на портале Azure:

1. Войдите на [портал Azure](https://portal.azure.com).

1. Выберите **NEW** в верхней левой части портала интерфейса.

1. Выберите **Интернет + мобильные устройства** > **веб-приложение**.

   ![Портал Microsoft Azure > кнопка "Создать" > пункт "Интернет и мобильные устройства" в списке "Marketplace" > кнопка "Веб-приложение" в списке "Рекомендуемые приложения"](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. В колонке **Веб-приложение** в поле **Имя службы приложений** введите уникальное значение.

   ![Колонка "Веб-приложение"](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > **Имя приложения службы** имя должно быть уникальным. Портал применяет это правило, если указано имя. Если предоставить другое значение, замените это значение для каждого вхождения **SampleWebAppDemo** в этом учебнике.

   Кроме того, в колонке **Веб-приложение** выберите существующее **Расположение или план службы приложений** либо создайте собственный план или расположение. Если создается новый план, выберите ценовую категорию, расположение и другие параметры. Дополнительные сведения о планах службы приложений см. в разделе [исчерпывающий обзор планы службы приложений Azure](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).

1. Выберите **Создать**. Подготовка Azure и запустить веб-приложение.

   ![Портал Azure: колонка "Основное" для приложения SampleWebAppDemo01](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a>Включение публикации в Git для нового веб-приложения

Git — это система управления распределенных версии, который может использоваться для развертывания веб-приложение службы приложений Azure. Кода веб-приложения хранятся в локальном репозитории, а код развертывается в Azure с помощью принудительной отправки в удаленном репозитории.

1. Войдите на [портал Azure](https://portal.azure.com).

1. Выберите **службы приложений** для просмотра списка приложений служб, связанных с подпиской Azure.

1. Выберите веб-приложения, созданные в предыдущем разделе этого учебника.

1. В колонке **Deployment** (Развертывание) выберите **Deployment options** (Параметры развертывания) > **Выбор источника** > **Локальный репозиторий Git**.

   ![Колонка "Параметры" > колонка "Источник развертывания" > колонка "Выбор источника"](azure-continuous-deployment/_static/deployment-options.png)

1. Нажмите кнопку **ОК**.

1. Если учетные данные развертывания для публикации веб-приложение или другое приложение службы приложений ранее не задан, настройте их сейчас:

   * Выберите **параметры** > **учетные данные развертывания**. **Задать учетные данные развертывания** колонке отображается.
   * Создайте имя пользователя и пароль. Сохраните пароль для последующего использования при настройке Git.
   * Нажмите кнопку **Сохранить**.

1. В **веб-приложения** колонке выберите **параметры** > **свойства**. URL-адрес в удаленный репозиторий Git, для развертывания отображается в разделе **URL-адрес GIT**.

1. Скопируйте значение в поле **URL-адрес Git**, так как оно понадобится далее в этом руководстве.

   ![Портал Azure > колонка свойств приложения](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a>Опубликовать веб-приложения в службе приложений Azure

В этом разделе создайте локальный репозиторий Git с помощью Visual Studio и push из этого репозитория в Azure для развертывания веб-приложения. Для этого потребуется выполнить указанные ниже действия.

* Добавьте параметр удаленный репозиторий, используя значение URL-адрес GIT, чтобы можно было развернуть локальный репозиторий в Azure.
* Зафиксируйте изменения проекта.
* Отправьте изменения проекта из локального репозитория в удаленном репозитории в Azure.

1. В **обозревателе решений** щелкните правой кнопкой мыши элемент **Решение "SampleWebAppDemo"** и выберите пункт **Зафиксировать**. **Team Explorer** отображается.

   ![Вкладка "Подключение" в Team Explorer](azure-continuous-deployment/_static/10-team-explorer.png)

1. В **Team Explorer** щелкните значок **Главная** и выберите **Параметры** > **Параметры репозитория**.

1. В **удаленные элементы** раздел **параметров репозитория**выберите **добавить**. **Добавьте удаленный** диалоговое окно.

1. В качестве **имени** удаленного объекта укажите **Azure-SampleApp**.

1. Задайте значение **выборки** для **URL-адрес Git** , скопированные из Azure ранее в этом учебнике. Обратите внимание на то, что этот URL-адрес должен заканчиваться на **.git**.

   ![Диалоговое окно "Изменение удаленного объекта"](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > Кроме того, укажите удаленный репозиторий, из **командное окно** , открыв **командное окно**, изменения в каталог проекта и ввести команду. Пример
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. Щелкните значок **Главная** и выберите **Параметры** > **Глобальные параметры**. Убедитесь, что заданы имя и адрес электронной почты. Выберите **обновление** при необходимости.

1. Выберите **Главная** > **Изменения**, чтобы вернуться к представлению **Изменения**.

1. Введите сообщение фиксации, например **начальной Push #1** и выберите **фиксации**. Это действие создает *фиксации* локально.

   ![Вкладка "Подключение" в Team Explorer](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > В качестве альтернативы фиксации меняется с **командное окно** , открыв **командное окно**, изменения в каталог проекта и ввод команд git. Пример
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. Выберите **Главная** > **Синхронизировать** > **Действия** > **Открыть командную строку**. Откроется командная строка в каталоге проекта.

1. Введите в командном окне следующую команду:

   `git push -u Azure-SampleApp master`

1. Введите Azure **учетные данные развертывания** пароль, созданный ранее в Azure.

   Эта команда запускает процесс передачи файлов локального проекта в Azure. Выходные данные этой команды оканчивается сообщение о том, что развертывание успешно.

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > Если требуется совместная работа над проектом, распределенного запроса передает [GitHub](https://github.com) до отправки в Azure.
 
### <a name="verify-the-active-deployment"></a>Проверка активного развертывания

Проверьте успешность выполнения передачи приложения web от локальной среды в Azure.

В [портала Azure](https://portal.azure.com), выберите веб-приложения. Выберите **развертывания** > **варианты развертывания**.

![Портал Azure > колонка "Параметры" > колонка "Развертывания", содержащая успешное развертывание](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a>Запуск приложения в Azure

Теперь, когда веб-приложение развертывается в Azure, запустите приложение.

Это можно сделать двумя способами:

* На портале Azure найдите колонка веб приложения для веб-приложения. Выберите **Обзор** для просмотра приложения в браузере по умолчанию.
* Откройте браузер и введите URL-адрес для веб-приложения. Пример: `http://SampleWebAppDemo.azurewebsites.net`

## <a name="update-the-web-app-and-republish"></a>Обновить веб-приложение и повторно опубликуйте

После внесения изменений в локальный код, повторной публикации:

1. В **обозревателе решений** Visual Studio откройте файл *Startup.cs*.

1. В методе `Configure` измените метод `Response.WriteAsync`, как показано ниже.

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. Сохранить изменения в *файла Startup.cs*.

1. В **обозревателе решений** щелкните правой кнопкой мыши элемент **Решение "SampleWebAppDemo"** и выберите пункт **Зафиксировать**. **Team Explorer** отображается.

1. Например, введите сообщение фиксации `Update #2`.

1. Нажмите кнопку **Зафиксировать**, чтобы зафиксировать изменения проекта.

1. Выберите **Главная** > **Синхронизировать** > **Действия** > **Отправить**.

> [!NOTE]
> Кроме того, отправьте изменения из **командное окно** , открыв **командное окно**, изменения в каталог проекта и ввода команд git. Пример
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a>Просмотр обновленного веб-приложения в Azure

Просмотреть обновленные веб-приложения, выбрав **Обзор** из колонки веб приложения на портале Azure или откройте браузер и введите URL-адрес для веб-приложения. Пример: `http://SampleWebAppDemo.azurewebsites.net`

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Для создания и публикации в Azure веб-приложение с непрерывным развертыванием с помощью VSTS](/vsts/build-release/archive/apps/aspnet/aspnet-4-ci-cd-azure-automatic)
* [Проект Kudu](https://github.com/projectkudu/kudu/wiki)