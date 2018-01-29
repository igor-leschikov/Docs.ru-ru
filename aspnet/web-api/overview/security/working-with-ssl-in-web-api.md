---
uid: web-api/overview/security/working-with-ssl-in-web-api
title: "Работа с SSL в веб-API | Документы Microsoft"
author: MikeWasson
description: "Показано, как использовать протокол SSL с веб-API ASP.NET, в том числе с использованием SSL-сертификатов клиента."
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/12/2012
ms.topic: article
ms.assetid: 97f6164f-59cf-45c0-b820-e4aa29b45396
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/security/working-with-ssl-in-web-api
msc.type: authoredcontent
ms.openlocfilehash: 127b336cb628e55bd59481ecb1c4df83960dc25b
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2018
---
<a name="working-with-ssl-in-web-api"></a>Работа с SSL в веб-API
====================
по [Mike Wasson](https://github.com/MikeWasson)

Некоторые распространенные схемы проверки подлинности не являются безопасными через простой HTTP. В частности Обычная проверка подлинности и проверки подлинности форм отправлять незашифрованные учетные данные. Для обеспечения безопасности, эти схемы проверки подлинности *должен* использовать протокол SSL. Кроме того SSL-сертификатов клиента может использоваться для проверки подлинности клиентов.

## <a name="enabling-ssl-on-the-server"></a>При включении SSL на сервере

Настройка SSL в IIS 7 или более поздней версии:

- Создать или получить сертификат. Для тестирования, можно создать самозаверяющий сертификат.
- Добавьте HTTPS-привязки.

Дополнительные сведения см. в разделе [как выполнить настройку SSL в службах IIS 7](https://www.iis.net/learn/manage/configuring-security/how-to-set-up-ssl-on-iis).

Для локального тестирования можно включить SSL в IIS Express из Visual Studio. В окне свойств задайте **протокол SSL включен** для **True**. Обратите внимание на значение **URL-адрес SSL**; использовать этот URL-адрес для тестирования подключения по протоколу HTTPS.

![](working-with-ssl-in-web-api/_static/image1.png)

### <a name="enforcing-ssl-in-a-web-api-controller"></a>Применение протокола SSL в контроллер Web API

Если у вас есть привязку HTTP и HTTPS, клиенты могут использовать HTTP для доступа к сайту. Можно разрешить некоторые ресурсы с использованием HTTP, а для других ресурсов требуется SSL. В этом случае используйте фильтр действий обязательного использования протокола SSL к защищенным ресурсам. В следующем коде показано фильтр проверки подлинности веб-API, который проверяет наличие SSL:

[!code-csharp[Main](working-with-ssl-in-web-api/samples/sample1.cs)]

Добавьте этот фильтр действия веб-API, которые требуется SSL:

[!code-csharp[Main](working-with-ssl-in-web-api/samples/sample2.cs)]

## <a name="ssl-client-certificates"></a>SSL-сертификатов клиента

SSL обеспечивает проверку подлинности с помощью сертификатов инфраструктуры открытого ключа. Сервер должен предоставить сертификат, который проверяет подлинность сервера на клиент. Менее часто клиент предоставлял сертификат на сервере, но это один из вариантов проверки подлинности клиентов. Чтобы использовать клиентские сертификаты с протоколом SSL, необходимо способ распространения сертификаты для пользователей. Для многих типов приложений это не будет эффективное взаимодействие с пользователем, но в некоторых средах (например, предприятие) может быть нецелесообразно.

| Преимущества | Недостатки |
| --- | --- |
| -Учетные данные сертификата надежнее, чем имя пользователя и пароль. -SSL предоставляет полный безопасный канал с проверкой подлинности, целостность сообщений и шифрования сообщений. | -Необходимо получить и управлять сертификатами PKI. -Платформа клиента должна поддерживать SSL-сертификатов клиента. |

Чтобы настроить службы IIS, чтобы принимать сертификаты клиентов, откройте диспетчер служб IIS и выполните следующие действия:

1. Щелкните узел в древовидном представлении.
2. Дважды щелкните **параметры SSL** функцию в средней области.
3. В разделе **клиентских сертификатов**, выберите один из следующих вариантов: 

    - **Принять**: IIS будет принять сертификат клиента, но не требуется.
    - **Требуется**: требуется сертификат клиента. (Чтобы включить этот параметр, необходимо также выбрать «Требовать SSL»)

В файле ApplicationHost.config, можно также задать следующие параметры:

[!code-xml[Main](working-with-ssl-in-web-api/samples/sample3.xml)]

**SslNegotiateCert** флаг означает IIS будет принять сертификат клиента, но не требуются (эквивалент параметра «Принять» в диспетчере служб IIS). Чтобы требуется сертификат, установите **для SslRequireCert** флаг. Для тестирования, можно также задать эти параметры в IIS Express, в локальном узле applicationhost. Файл конфигурации, расположенный в «Documents\IISExpress\config».

### <a name="creating-a-client-certificate-for-testing"></a>Создание сертификата клиента для проверки

В целях тестирования можно использовать [MakeCert.exe](https://msdn.microsoft.com/library/bfsktky3.aspx) Создание сертификата клиента. Сначала создайте тестовым корневым центром.

[!code-console[Main](working-with-ssl-in-web-api/samples/sample4.cmd)]

Makecert предложит ввести пароль для закрытого ключа.

Добавьте сертификат в тест, который хранения сервера «доверенные корневые центры сертификации», как показано ниже:

1. Откройте консоль MMC.
2. В разделе **файл**выберите **добавить или удалить оснастку**.
3. Выберите **учетная запись компьютера**.
4. Выберите **локального компьютера** и завершите работу мастера.
5. В области навигации разверните узел «Доверенные корневые центры сертификации».
6. На **действия** последовательно выберите пункты **все задачи**, а затем нажмите кнопку **импорта** для запуска мастера импорта сертификатов.
7. Перейдите к файлу сертификата TempCA.cer.
8. Нажмите кнопку **откройте**, нажмите кнопку **Далее** и завершите работу мастера. (Вам будет предложено повторно ввести пароль.)

Теперь создайте сертификат клиента, который подписывается первый сертификат:

[!code-console[Main](working-with-ssl-in-web-api/samples/sample5.cmd)]

### <a name="using-client-certificates-in-web-api"></a>Использование сертификатов клиентов в веб-API

На стороне сервера можно получить сертификат клиента, вызвав [GetClientCertificate](https://msdn.microsoft.com/library/system.net.http.httprequestmessageextensions.getclientcertificate.aspx) в сообщении запроса. Метод возвращает значение null, если нет сертификата клиента. В противном случае он возвращает **X509Certificate2** экземпляра. Этот объект можно используйте для получения сведений из сертификатов, такие как издатель и субъект. Затем эти сведения можно использовать для проверки подлинности или авторизации.

[!code-csharp[Main](working-with-ssl-in-web-api/samples/sample6.cs)]