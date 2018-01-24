---
title: "Ограничение времени существования полезных данных, защищенных"
author: rick-anderson
description: "В этом документе объясняется, как ограничить время существования защищенных полезных данных с помощью интерфейсов API защиты данных ASP.NET Core."
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 144097cd1551c1d0aece5df20ce01e14146a41d1
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
---
# <a name="limiting-the-lifetime-of-protected-payloads"></a><span data-ttu-id="7d600-103">Ограничение времени существования полезных данных, защищенных</span><span class="sxs-lookup"><span data-stu-id="7d600-103">Limiting the lifetime of protected payloads</span></span>

<span data-ttu-id="7d600-104">Существуют сценарии, где разработчик приложения хочет создать защищенный полезные данные, срок действия истекает через установленный период времени.</span><span class="sxs-lookup"><span data-stu-id="7d600-104">There are scenarios where the application developer wants to create a protected payload that expires after a set period of time.</span></span> <span data-ttu-id="7d600-105">Например защищенные полезных данных может представлять маркер сброса пароля, которая должна только действовать в течение одного часа.</span><span class="sxs-lookup"><span data-stu-id="7d600-105">For instance, the protected payload might represent a password reset token that should only be valid for one hour.</span></span> <span data-ttu-id="7d600-106">Возможна наверняка разработчикам создавать свои собственные формат полезных данных, который содержит внедренные срок действия, и дополнительно разработчикам может потребоваться это сделать, но для большинства разработчиков управление этих сроков действия может увеличиваться утомительным.</span><span class="sxs-lookup"><span data-stu-id="7d600-106">It is certainly possible for the developer to create their own payload format that contains an embedded expiration date, and advanced developers may wish to do this anyway, but for the majority of developers managing these expirations can grow tedious.</span></span>

<span data-ttu-id="7d600-107">Чтобы облегчить эту задачу для наших аудитория разработчиков, пакет [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) содержит API служебной программы для создания полезных данных, которые автоматически истекает после заданного периода времени.</span><span class="sxs-lookup"><span data-stu-id="7d600-107">To make this easier for our developer audience, the package [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contains utility APIs for creating payloads that automatically expire after a set period of time.</span></span> <span data-ttu-id="7d600-108">Эти API-интерфейсы зависания пределами `ITimeLimitedDataProtector` типа.</span><span class="sxs-lookup"><span data-stu-id="7d600-108">These APIs hang off of the `ITimeLimitedDataProtector` type.</span></span>

## <a name="api-usage"></a><span data-ttu-id="7d600-109">Использование API</span><span class="sxs-lookup"><span data-stu-id="7d600-109">API usage</span></span>

<span data-ttu-id="7d600-110">`ITimeLimitedDataProtector` Интерфейс является основной интерфейс для защиты и снятие защиты полезных данных ограниченной по времени и автоматическим истечением срока действия.</span><span class="sxs-lookup"><span data-stu-id="7d600-110">The `ITimeLimitedDataProtector` interface is the core interface for protecting and unprotecting time-limited / self-expiring payloads.</span></span> <span data-ttu-id="7d600-111">Для создания экземпляра `ITimeLimitedDataProtector`, вам потребуется сначала экземпляр обычной [IDataProtector](overview.md) создан с определенной цели.</span><span class="sxs-lookup"><span data-stu-id="7d600-111">To create an instance of an `ITimeLimitedDataProtector`, you'll first need an instance of a regular [IDataProtector](overview.md) constructed with a specific purpose.</span></span> <span data-ttu-id="7d600-112">Один раз `IDataProtector` экземпляр доступен, вызовите `IDataProtector.ToTimeLimitedDataProtector` метод расширения для получения предохранитель с возможностями встроенных истечение срока действия.</span><span class="sxs-lookup"><span data-stu-id="7d600-112">Once the `IDataProtector` instance is available, call the `IDataProtector.ToTimeLimitedDataProtector` extension method to get back a protector with built-in expiration capabilities.</span></span>

<span data-ttu-id="7d600-113">`ITimeLimitedDataProtector`предоставляет следующие методы API контактную зону и расширения:</span><span class="sxs-lookup"><span data-stu-id="7d600-113">`ITimeLimitedDataProtector` exposes the following API surface and extension methods:</span></span>

* <span data-ttu-id="7d600-114">CreateProtector (строка назначение): ITimeLimitedDataProtector - этот API, похожие на существующие `IDataProtectionProvider.CreateProtector` в том, что он может использоваться для создания [цели цепочки](purpose-strings.md) из предохранителя корневой ограниченной по времени.</span><span class="sxs-lookup"><span data-stu-id="7d600-114">CreateProtector(string purpose) : ITimeLimitedDataProtector - This API is similar to the existing `IDataProtectionProvider.CreateProtector` in that it can be used to create [purpose chains](purpose-strings.md) from a root time-limited protector.</span></span>

* <span data-ttu-id="7d600-115">Защита (byte [] открытого текста, истечение срока действия DateTimeOffset): byte]</span><span class="sxs-lookup"><span data-stu-id="7d600-115">Protect(byte[] plaintext, DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="7d600-116">Защита (открытого текста byte [], время существования TimeSpan): byte]</span><span class="sxs-lookup"><span data-stu-id="7d600-116">Protect(byte[] plaintext, TimeSpan lifetime) : byte[]</span></span>

* <span data-ttu-id="7d600-117">Защита (byte [] текстовая): byte]</span><span class="sxs-lookup"><span data-stu-id="7d600-117">Protect(byte[] plaintext) : byte[]</span></span>

* <span data-ttu-id="7d600-118">Защита (текстовая строка, истечение срока действия DateTimeOffset): строка</span><span class="sxs-lookup"><span data-stu-id="7d600-118">Protect(string plaintext, DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="7d600-119">Защита (обычный текст строки, время существования TimeSpan): строка</span><span class="sxs-lookup"><span data-stu-id="7d600-119">Protect(string plaintext, TimeSpan lifetime) : string</span></span>

* <span data-ttu-id="7d600-120">Защита (текстовая строка): строка</span><span class="sxs-lookup"><span data-stu-id="7d600-120">Protect(string plaintext) : string</span></span>

<span data-ttu-id="7d600-121">Помимо базовых `Protect` методов, принимающих только обычный текст, существуют новые перегрузки, которые разрешается указывать срок действия полезных данных.</span><span class="sxs-lookup"><span data-stu-id="7d600-121">In addition to the core `Protect` methods which take only the plaintext, there are new overloads which allow specifying the payload's expiration date.</span></span> <span data-ttu-id="7d600-122">Дата окончания срока действия может быть указан как абсолютная Дата (через `DateTimeOffset`) или как относительное время (из текущей системы времени через `TimeSpan`).</span><span class="sxs-lookup"><span data-stu-id="7d600-122">The expiration date can be specified as an absolute date (via a `DateTimeOffset`) or as a relative time (from the current system time, via a `TimeSpan`).</span></span> <span data-ttu-id="7d600-123">При вызове перегрузки, которая не принимает срока действия полезных данных предполагается никогда не истекает.</span><span class="sxs-lookup"><span data-stu-id="7d600-123">If an overload which doesn't take an expiration is called, the payload is assumed never to expire.</span></span>

* <span data-ttu-id="7d600-124">Снять защиту (byte [] protectedData, ожидания истечения срока действия DateTimeOffset): byte]</span><span class="sxs-lookup"><span data-stu-id="7d600-124">Unprotect(byte[] protectedData, out DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="7d600-125">Снять защиту (protectedData byte []): byte]</span><span class="sxs-lookup"><span data-stu-id="7d600-125">Unprotect(byte[] protectedData) : byte[]</span></span>

* <span data-ttu-id="7d600-126">Снять защиту (ожидания истечения срока действия DateTimeOffset, protectedData строка): строка</span><span class="sxs-lookup"><span data-stu-id="7d600-126">Unprotect(string protectedData, out DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="7d600-127">Снять защиту (строка protectedData): строка</span><span class="sxs-lookup"><span data-stu-id="7d600-127">Unprotect(string protectedData) : string</span></span>

<span data-ttu-id="7d600-128">`Unprotect` Методы возвращают незащищенных исходных данных.</span><span class="sxs-lookup"><span data-stu-id="7d600-128">The `Unprotect` methods return the original unprotected data.</span></span> <span data-ttu-id="7d600-129">Если полезные данные еще не истек, абсолютный срок действия возвращается как Необязательный выходной параметр вместе с исходной незащищенные данные.</span><span class="sxs-lookup"><span data-stu-id="7d600-129">If the payload hasn't yet expired, the absolute expiration is returned as an optional out parameter along with the original unprotected data.</span></span> <span data-ttu-id="7d600-130">Если истек срок действия полезных данных всех перегруженных версий метода Unprotect вызовет CryptographicException.</span><span class="sxs-lookup"><span data-stu-id="7d600-130">If the payload is expired, all overloads of the Unprotect method will throw CryptographicException.</span></span>

>[!WARNING]
> <span data-ttu-id="7d600-131">Не рекомендуется использовать эти API-интерфейсы для защиты полезных нагрузок, требующих сохраняемости долгосрочной или не определено.</span><span class="sxs-lookup"><span data-stu-id="7d600-131">It is not advised to use these APIs to protect payloads which require long-term or indefinite persistence.</span></span> <span data-ttu-id="7d600-132">«Я допускают для защищенных нагрузок быть невозможности восстановить после месяца?»</span><span class="sxs-lookup"><span data-stu-id="7d600-132">"Can I afford for the protected payloads to be permanently unrecoverable after a month?"</span></span> <span data-ttu-id="7d600-133">может служить хорошим правило; Если ответ не затем разработчики следует рассмотреть альтернативные API-интерфейсы.</span><span class="sxs-lookup"><span data-stu-id="7d600-133">can serve as a good rule of thumb; if the answer is no then developers should consider alternative APIs.</span></span>

<span data-ttu-id="7d600-134">Пример ниже, используется [пути кода не DI](../configuration/non-di-scenarios.md) для экземпляров системы защиты данных.</span><span class="sxs-lookup"><span data-stu-id="7d600-134">The sample below uses the [non-DI code paths](../configuration/non-di-scenarios.md) for instantiating the data protection system.</span></span> <span data-ttu-id="7d600-135">Чтобы запустить этот образец, убедитесь, сначала добавили ссылку на пакет Microsoft.AspNetCore.DataProtection.Extensions.</span><span class="sxs-lookup"><span data-stu-id="7d600-135">To run this sample, ensure that you have first added a reference to the Microsoft.AspNetCore.DataProtection.Extensions package.</span></span>

[!code-csharp[Main](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]