---
title: "Открытый веб-интерфейс для .NET (OWIN)"
author: ardalis
description: "Узнайте, как ASP.NET Core поддерживает открытие веб-интерфейса для .NET (OWIN), который позволяет веб-приложений, связано с веб-серверов."
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/owin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e819037e2ebd1566c778879516e20de8dc7603ea
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
---
# <a name="introduction-to-open-web-interface-for-net-owin"></a><span data-ttu-id="4cbcf-103">Общие сведения, чтобы открыть веб-интерфейс для .NET (OWIN)</span><span class="sxs-lookup"><span data-stu-id="4cbcf-103">Introduction to Open Web Interface for .NET (OWIN)</span></span>

<span data-ttu-id="4cbcf-104">По [Стив Смит](https://ardalis.com/) и [Рик Андерсон](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4cbcf-104">By [Steve Smith](https://ardalis.com/) and  [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4cbcf-105">ASP.NET Core поддерживает открытый веб-интерфейс для .NET (OWIN).</span><span class="sxs-lookup"><span data-stu-id="4cbcf-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="4cbcf-106">OWIN позволяет ослабить зависимость веб-приложений от веб-сервера.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="4cbcf-107">Он определяет стандартный способ для промежуточного по для использования в конвейере для обработки запросов и связанных ответов.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="4cbcf-108">Приложения ASP.NET Core и по промежуточного слоя могут взаимодействовать с по промежуточного слоя, серверов и приложений на основе OWIN.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="4cbcf-109">OWIN обеспечивает разделение уровень, позволяющий две платформы с разрозненными объектные модели для совместного использования.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="4cbcf-110">`Microsoft.AspNetCore.Owin` Пакет содержит две реализации адаптера:</span><span class="sxs-lookup"><span data-stu-id="4cbcf-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>
- <span data-ttu-id="4cbcf-111">ASP.NET Core для OWIN</span><span class="sxs-lookup"><span data-stu-id="4cbcf-111">ASP.NET Core to OWIN</span></span> 
- <span data-ttu-id="4cbcf-112">OWIN для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4cbcf-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="4cbcf-113">Это позволяет ASP.NET Core для размещения на основе OWIN server совместимую или для других совместимых компонентов OWIN для выполнения на основе ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host, or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

<span data-ttu-id="4cbcf-114">Примечание: С помощью этих адаптеров поставляется с затратами по производительности.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-114">Note: Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="4cbcf-115">Приложения, использующие только компоненты ASP.NET Core не следует использовать пакет Owin или адаптеров.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-115">Applications using only ASP.NET Core components should not use the Owin package or adapters.</span></span>

<span data-ttu-id="4cbcf-116">[Просмотреть или скачать образец кода](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([как скачивать](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4cbcf-116">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-pipeline"></a><span data-ttu-id="4cbcf-117">Под управлением OWIN по промежуточного слоя в конвейере ASP.NET</span><span class="sxs-lookup"><span data-stu-id="4cbcf-117">Running OWIN middleware in the ASP.NET pipeline</span></span>

<span data-ttu-id="4cbcf-118">Поддержка OWIN ASP.NET Core развертывается как часть `Microsoft.AspNetCore.Owin` пакета.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="4cbcf-119">Поддержка OWIN можно импортировать в проект при установке этого пакета.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="4cbcf-120">По промежуточного слоя OWIN соответствует [спецификации OWIN](http://owin.org/spec/spec/owin-1.0.0.html), что требует `Func<IDictionary<string, object>, Task>` задать интерфейс и определенные ключи (такие как `owin.ResponseBody`).</span><span class="sxs-lookup"><span data-stu-id="4cbcf-120">OWIN middleware conforms to the [OWIN specification](http://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="4cbcf-121">Следующий простой OWIN по промежуточного слоя отображается «Hello, World!»:</span><span class="sxs-lookup"><span data-stu-id="4cbcf-121">The following simple OWIN middleware displays "Hello World":</span></span>

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: http://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

<span data-ttu-id="4cbcf-122">Возвращает образец подписи `Task` и принимает `IDictionary<string, object>` согласно требованиям OWIN.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="4cbcf-123">Следующий код демонстрирует добавление `OwinHello` (показано выше) в конвейере ASP.NET с по промежуточного слоя `UseOwin` метода расширения.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="4cbcf-124">Можно настроить другие действия для выполнения в конвейер OWIN.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="4cbcf-125">Заголовки ответа может изменяться только до первой записи в поток ответа.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="4cbcf-126">Несколько вызовов `UseOwin` не рекомендуется по соображениям производительности.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="4cbcf-127">Компоненты OWIN будет работать наилучшим образом, если группируются вместе.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-127">OWIN components will operate best if grouped together.</span></span>

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        // do something before
        return OwinHello;
        // do something after
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="using-aspnet-hosting-on-an-owin-based-server"></a><span data-ttu-id="4cbcf-128">С помощью ASP.NET размещения на сервере под управлением OWIN</span><span class="sxs-lookup"><span data-stu-id="4cbcf-128">Using ASP.NET Hosting on an OWIN-based server</span></span>

<span data-ttu-id="4cbcf-129">Серверы на основе OWIN можно размещать приложения ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-129">OWIN-based servers can host ASP.NET applications.</span></span> <span data-ttu-id="4cbcf-130">Один сервер является [Nowin](https://github.com/Bobris/Nowin), веб-сервера .NET OWIN.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-130">One such server is [Nowin](https://github.com/Bobris/Nowin), a .NET OWIN web server.</span></span> <span data-ttu-id="4cbcf-131">В этом образце для данной статьи я включил проект, который ссылается на Nowin и использует ее для создания `IServer` для самостоятельного размещения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-131">In the sample for this article, I've included a project that references Nowin and uses it to create an `IServer` capable of self-hosting ASP.NET Core.</span></span>

[!code-csharp[Main](owin/sample/src/NowinSample/Program.cs?highlight=15)]

<span data-ttu-id="4cbcf-132">`IServer`— Это интерфейс, который требует `Features` свойство и `Start` метод.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-132">`IServer` is an interface that requires an `Features` property and a `Start` method.</span></span>

<span data-ttu-id="4cbcf-133">`Start`отвечает за настройки и запуска на сервер, который в этом случае выполняется с помощью ряда fluent вызовы API, которые установить синтаксический анализ адреса из IServerAddressesFeature.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-133">`Start` is responsible for configuring and starting the server, which in this case is done through a series of fluent API calls that set addresses parsed from the IServerAddressesFeature.</span></span> <span data-ttu-id="4cbcf-134">Обратите внимание, что конфигурация fluent `_builder` переменная указывает, что запросы будут обрабатываться `appFunc` определенные ранее в метод.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-134">Note that the fluent configuration of the `_builder` variable specifies that requests will be handled by the `appFunc` defined earlier in the method.</span></span> <span data-ttu-id="4cbcf-135">Это `Func` вызывается при каждом запросе для обработки входящих запросов.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-135">This `Func` is called on each request to process incoming requests.</span></span>

<span data-ttu-id="4cbcf-136">Также мы добавим `IWebHostBuilder` расширения облегчает добавление и настройка сервера Nowin.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-136">We'll also add an `IWebHostBuilder` extension to make it easy to add and configure the Nowin server.</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting.Server;
using Microsoft.Extensions.DependencyInjection;
using Nowin;
using NowinSample;

namespace Microsoft.AspNetCore.Hosting
{
    public static class NowinWebHostBuilderExtensions
    {
        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder)
        {
            return builder.ConfigureServices(services =>
            {
                services.AddSingleton<IServer, NowinServer>();
            });
        }

        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder, Action<ServerBuilder> configure)
        {
            builder.ConfigureServices(services =>
            {
                services.Configure(configure);
            });
            return builder.UseNowin();
        }
    }
}
```

<span data-ttu-id="4cbcf-137">Это место, все, что необходимые для запуска приложения ASP.NET с помощью этого пользовательского сервера для вызова модуля *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="4cbcf-137">With this in place, all that's required to run an ASP.NET application using this custom server to call the extension in *Program.cs*:</span></span>

```csharp

using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;

namespace NowinSample
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = new WebHostBuilder()
                .UseNowin()
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseIISIntegration()
                .UseStartup<Startup>()
                .Build();

            host.Run();
        }
    }
}

```

<span data-ttu-id="4cbcf-138">Дополнительные сведения о ASP.NET [серверов](servers/index.md).</span><span class="sxs-lookup"><span data-stu-id="4cbcf-138">Learn more about ASP.NET [Servers](servers/index.md).</span></span>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="4cbcf-139">Запустить ASP.NET Core на сервере под управлением OWIN и использовать его поддержка WebSockets</span><span class="sxs-lookup"><span data-stu-id="4cbcf-139">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="4cbcf-140">Будет доступен другой пример серверы на базе OWIN функций ASP.NET Core имеет доступ к функциям, например WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-140">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="4cbcf-141">Веб-сервера .NET OWIN, используемый в предыдущем примере реализована поддержка веб-сокеты встроенным, который может использоваться приложением ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-141">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="4cbcf-142">В приведенном ниже примере показан простой веб-приложения, который поддерживает веб-сокеты и возвращает обратно все данные отправляются на сервер через WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-142">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

<span data-ttu-id="4cbcf-143">Это [пример](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/owin/sample) настраивается с помощью одного `NowinServer` как предыдущего - единственное различие заключается в том, как приложение настроено в его `Configure` метод.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-143">This [sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/owin/sample) is configured using the same `NowinServer` as the previous one - the only difference is in how the application is configured in its `Configure` method.</span></span> <span data-ttu-id="4cbcf-144">Тест, с помощью [клиент простой websocket](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) демонстрирует приложение:</span><span class="sxs-lookup"><span data-stu-id="4cbcf-144">A test using [a simple websocket client](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) demonstrates  the application:</span></span>

![Веб-сокета тестового клиента](owin/_static/websocket-test.png)

## <a name="owin-environment"></a><span data-ttu-id="4cbcf-146">Среда OWIN</span><span class="sxs-lookup"><span data-stu-id="4cbcf-146">OWIN environment</span></span>

<span data-ttu-id="4cbcf-147">Можно создать среду OWIN с помощью `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-147">You can construct a OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="4cbcf-148">Ключи OWIN</span><span class="sxs-lookup"><span data-stu-id="4cbcf-148">OWIN keys</span></span>

<span data-ttu-id="4cbcf-149">Зависит от OWIN `IDictionary<string,object>` объекта для передачи сведений в HTTP-запроса или ответа exchange.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-149">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="4cbcf-150">ASP.NET Core реализует перечисленные ниже ключи.</span><span class="sxs-lookup"><span data-stu-id="4cbcf-150">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="4cbcf-151">В разделе [основной спецификации, расширения](http://owin.org/#spec), и [OWIN ключ рекомендации и общие ключи](http://owin.org/spec/spec/CommonKeys.html).</span><span class="sxs-lookup"><span data-stu-id="4cbcf-151">See the [primary specification, extensions](http://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](http://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="4cbcf-152">Данные запроса (версии 1.0.0 OWIN)</span><span class="sxs-lookup"><span data-stu-id="4cbcf-152">Request Data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="4cbcf-153">Ключ</span><span class="sxs-lookup"><span data-stu-id="4cbcf-153">Key</span></span>               | <span data-ttu-id="4cbcf-154">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="4cbcf-154">Value (type)</span></span> | <span data-ttu-id="4cbcf-155">Описание:</span><span class="sxs-lookup"><span data-stu-id="4cbcf-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="4cbcf-156">owin.RequestScheme</span><span class="sxs-lookup"><span data-stu-id="4cbcf-156">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="4cbcf-157">owin. RequestMethod</span><span class="sxs-lookup"><span data-stu-id="4cbcf-157">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="4cbcf-158">owin.RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="4cbcf-158">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="4cbcf-159">owin.RequestPath</span><span class="sxs-lookup"><span data-stu-id="4cbcf-159">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="4cbcf-160">owin.RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="4cbcf-160">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="4cbcf-161">owin.RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="4cbcf-161">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="4cbcf-162">owin. RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="4cbcf-162">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="4cbcf-163">owin.RequestBody</span><span class="sxs-lookup"><span data-stu-id="4cbcf-163">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="4cbcf-164">Данные запроса (OWIN v1.1.0)</span><span class="sxs-lookup"><span data-stu-id="4cbcf-164">Request Data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="4cbcf-165">Ключ</span><span class="sxs-lookup"><span data-stu-id="4cbcf-165">Key</span></span>               | <span data-ttu-id="4cbcf-166">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="4cbcf-166">Value (type)</span></span> | <span data-ttu-id="4cbcf-167">Описание:</span><span class="sxs-lookup"><span data-stu-id="4cbcf-167">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="4cbcf-168">owin. ИД запроса</span><span class="sxs-lookup"><span data-stu-id="4cbcf-168">owin.RequestId</span></span> | `String` | <span data-ttu-id="4cbcf-169">Optional</span><span class="sxs-lookup"><span data-stu-id="4cbcf-169">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="4cbcf-170">Данные ответа (версии 1.0.0 OWIN)</span><span class="sxs-lookup"><span data-stu-id="4cbcf-170">Response Data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="4cbcf-171">Ключ</span><span class="sxs-lookup"><span data-stu-id="4cbcf-171">Key</span></span>               | <span data-ttu-id="4cbcf-172">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="4cbcf-172">Value (type)</span></span> | <span data-ttu-id="4cbcf-173">Описание:</span><span class="sxs-lookup"><span data-stu-id="4cbcf-173">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="4cbcf-174">owin.ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="4cbcf-174">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="4cbcf-175">Optional</span><span class="sxs-lookup"><span data-stu-id="4cbcf-175">Optional</span></span> |
| <span data-ttu-id="4cbcf-176">owin.ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="4cbcf-176">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="4cbcf-177">Optional</span><span class="sxs-lookup"><span data-stu-id="4cbcf-177">Optional</span></span> |
| <span data-ttu-id="4cbcf-178">owin. ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="4cbcf-178">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="4cbcf-179">owin.ResponseBody</span><span class="sxs-lookup"><span data-stu-id="4cbcf-179">owin.ResponseBody</span></span> | `Stream`  | |


### <a name="other-data-owin-v100"></a><span data-ttu-id="4cbcf-180">Другие данные (версии 1.0.0 OWIN)</span><span class="sxs-lookup"><span data-stu-id="4cbcf-180">Other Data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="4cbcf-181">Ключ</span><span class="sxs-lookup"><span data-stu-id="4cbcf-181">Key</span></span>               | <span data-ttu-id="4cbcf-182">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="4cbcf-182">Value (type)</span></span> | <span data-ttu-id="4cbcf-183">Описание:</span><span class="sxs-lookup"><span data-stu-id="4cbcf-183">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="4cbcf-184">owin. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="4cbcf-184">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="4cbcf-185">owin. Версия</span><span class="sxs-lookup"><span data-stu-id="4cbcf-185">owin.Version</span></span>  | `String` | |   


### <a name="common-keys"></a><span data-ttu-id="4cbcf-186">Общие ключи</span><span class="sxs-lookup"><span data-stu-id="4cbcf-186">Common Keys</span></span>

| <span data-ttu-id="4cbcf-187">Ключ</span><span class="sxs-lookup"><span data-stu-id="4cbcf-187">Key</span></span>               | <span data-ttu-id="4cbcf-188">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="4cbcf-188">Value (type)</span></span> | <span data-ttu-id="4cbcf-189">Описание:</span><span class="sxs-lookup"><span data-stu-id="4cbcf-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="4cbcf-190">ssl.ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="4cbcf-190">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="4cbcf-191">ssl.LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="4cbcf-191">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="4cbcf-192">server.RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="4cbcf-192">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="4cbcf-193">server.RemotePort</span><span class="sxs-lookup"><span data-stu-id="4cbcf-193">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="4cbcf-194">server.LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="4cbcf-194">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="4cbcf-195">server.LocalPort</span><span class="sxs-lookup"><span data-stu-id="4cbcf-195">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="4cbcf-196">сервер. IsLocal</span><span class="sxs-lookup"><span data-stu-id="4cbcf-196">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="4cbcf-197">сервер. Псевдособытие</span><span class="sxs-lookup"><span data-stu-id="4cbcf-197">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |


### <a name="sendfiles-v030"></a><span data-ttu-id="4cbcf-198">SendFiles v0.3.0</span><span class="sxs-lookup"><span data-stu-id="4cbcf-198">SendFiles v0.3.0</span></span>

| <span data-ttu-id="4cbcf-199">Ключ</span><span class="sxs-lookup"><span data-stu-id="4cbcf-199">Key</span></span>               | <span data-ttu-id="4cbcf-200">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="4cbcf-200">Value (type)</span></span> | <span data-ttu-id="4cbcf-201">Описание:</span><span class="sxs-lookup"><span data-stu-id="4cbcf-201">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="4cbcf-202">sendfile.SendAsync</span><span class="sxs-lookup"><span data-stu-id="4cbcf-202">sendfile.SendAsync</span></span> | <span data-ttu-id="4cbcf-203">В разделе [сигнатура делегата](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="4cbcf-203">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="4cbcf-204">По запросу</span><span class="sxs-lookup"><span data-stu-id="4cbcf-204">Per Request</span></span> |


### <a name="opaque-v030"></a><span data-ttu-id="4cbcf-205">Непрозрачный v0.3.0</span><span class="sxs-lookup"><span data-stu-id="4cbcf-205">Opaque v0.3.0</span></span>

| <span data-ttu-id="4cbcf-206">Ключ</span><span class="sxs-lookup"><span data-stu-id="4cbcf-206">Key</span></span>               | <span data-ttu-id="4cbcf-207">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="4cbcf-207">Value (type)</span></span> | <span data-ttu-id="4cbcf-208">Описание:</span><span class="sxs-lookup"><span data-stu-id="4cbcf-208">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="4cbcf-209">opaque.Version</span><span class="sxs-lookup"><span data-stu-id="4cbcf-209">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="4cbcf-210">непрозрачный. Обновление</span><span class="sxs-lookup"><span data-stu-id="4cbcf-210">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="4cbcf-211">В разделе [сигнатура делегата](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="4cbcf-211">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="4cbcf-212">opaque.Stream</span><span class="sxs-lookup"><span data-stu-id="4cbcf-212">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="4cbcf-213">непрозрачный. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="4cbcf-213">opaque.CallCancelled</span></span> | `CancellationToken` |  |


### <a name="websocket-v030"></a><span data-ttu-id="4cbcf-214">WebSocket v0.3.0</span><span class="sxs-lookup"><span data-stu-id="4cbcf-214">WebSocket v0.3.0</span></span>

| <span data-ttu-id="4cbcf-215">Ключ</span><span class="sxs-lookup"><span data-stu-id="4cbcf-215">Key</span></span>               | <span data-ttu-id="4cbcf-216">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="4cbcf-216">Value (type)</span></span> | <span data-ttu-id="4cbcf-217">Описание:</span><span class="sxs-lookup"><span data-stu-id="4cbcf-217">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="4cbcf-218">WebSocket. Версия</span><span class="sxs-lookup"><span data-stu-id="4cbcf-218">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="4cbcf-219">websocket.Accept</span><span class="sxs-lookup"><span data-stu-id="4cbcf-219">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="4cbcf-220">В разделе [сигнатура делегата](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="4cbcf-220">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="4cbcf-221">websocket.AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="4cbcf-221">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="4cbcf-222">Non-spec</span><span class="sxs-lookup"><span data-stu-id="4cbcf-222">Non-spec</span></span> |
| <span data-ttu-id="4cbcf-223">WebSocket. Подпротокол</span><span class="sxs-lookup"><span data-stu-id="4cbcf-223">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="4cbcf-224">В разделе [RFC6455 раздел 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) шаг 5.5</span><span class="sxs-lookup"><span data-stu-id="4cbcf-224">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="4cbcf-225">websocket.SendAsync</span><span class="sxs-lookup"><span data-stu-id="4cbcf-225">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="4cbcf-226">В разделе [сигнатура делегата](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="4cbcf-226">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="4cbcf-227">websocket.ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="4cbcf-227">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="4cbcf-228">В разделе [сигнатура делегата](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="4cbcf-228">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="4cbcf-229">websocket.CloseAsync</span><span class="sxs-lookup"><span data-stu-id="4cbcf-229">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="4cbcf-230">В разделе [сигнатура делегата](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="4cbcf-230">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="4cbcf-231">WebSocket. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="4cbcf-231">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="4cbcf-232">websocket.ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="4cbcf-232">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="4cbcf-233">Optional</span><span class="sxs-lookup"><span data-stu-id="4cbcf-233">Optional</span></span> |
| <span data-ttu-id="4cbcf-234">websocket.ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="4cbcf-234">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="4cbcf-235">Optional</span><span class="sxs-lookup"><span data-stu-id="4cbcf-235">Optional</span></span> |


## <a name="additional-resources"></a><span data-ttu-id="4cbcf-236">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="4cbcf-236">Additional Resources</span></span>

* [<span data-ttu-id="4cbcf-237">ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="4cbcf-237">Middleware</span></span>](middleware.md)

* [<span data-ttu-id="4cbcf-238">Серверы</span><span class="sxs-lookup"><span data-stu-id="4cbcf-238">Servers</span></span>](servers/index.md)