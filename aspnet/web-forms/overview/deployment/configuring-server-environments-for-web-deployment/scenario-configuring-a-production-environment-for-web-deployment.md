---
uid: web-forms/overview/deployment/configuring-server-environments-for-web-deployment/scenario-configuring-a-production-environment-for-web-deployment
title: "Сценарий: Настройка рабочей среде для веб-развертывание | Документы Microsoft"
author: jrjlee
description: "В этом разделе описываются типичные веб-приложениях развертывания в рабочей среде и описание задачи, которые необходимо выполнить для настройки аналогичное..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: 2e861511-450e-4752-a61e-4a01933f9b6e
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/configuring-server-environments-for-web-deployment/scenario-configuring-a-production-environment-for-web-deployment
msc.type: authoredcontent
ms.openlocfilehash: cdd13f96ddf08ff86b01ef9de17ea82cf038ab28
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2018
---
<a name="scenario-configuring-a-production-environment-for-web-deployment"></a>Сценарий: Настройка рабочей среде для веб-развертывания
====================
по [Джейсон Lee](https://github.com/jrjlee)

[Скачать PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> В этом разделе описываются типичные веб-приложениях развертывания в рабочей среде и описание задачи, которые необходимо выполнить для настройки среды.


В рабочей среде является конечной точкой назначения для веб-приложения или веб-сайта. К этому моменту приложение прошла тестирование, будет развернута в промежуточной среде и готов к «ввести в эксплуатацию.» Характеристики рабочей среде могут существенно различаться по сути и назначения веб-содержимого, размер вашей организации, целевой аудитории и множество других факторов. В сценарии корпоративного уровня в рабочей среде может иметь следующие характеристики:

- Среда состоит из нескольких балансировки нагрузки веб-серверов и одного или нескольких серверов базы данных, часто с отказоустойчивой кластеризации и зеркальное отображение базы данных.
- Если Интернет-среде, скорее всего, изолированная от внутренней сети. Возможно, в другой подсети в сети периметра, возможно, на другом домене, и она может быть в совершенно другой сетевой инфраструктуры.
- Разработчики и учетных записей процесса сервера построения наверняка не имеет прав администратора на рабочих серверах.
- Внесения изменений в приложения развертываются на реже, чем теста или промежуточного развертывания.

> [!NOTE]
> Масштабирование развертывания базы данных по нескольким серверам выходит за рамки данного руководства. Дополнительные сведения об этой области, обратитесь к [электронной документации по SQL Server](https://technet.microsoft.com/library/ms130214.aspx).


Например, в нашем [сценарий учебника](../deploying-web-applications-in-enterprise-scenarios/enterprise-web-deployment-scenario-overview.md), Team Build сервера включает в себя определения сборок, которые позволяют пользователям построить решение диспетчера контактов и развернуть его в промежуточной среде, в один шаг. Когда приложение готово к развертыванию в рабочей среде, из-за ограничений, налагаемых требований к безопасности и сетевой инфраструктурой, администратор производственной среде необходимо вручную скопировать веб-пакета на рабочий веб-сервер и импорт его через диспетчер Internet Information Services (IIS).

![](scenario-configuring-a-production-environment-for-web-deployment/_static/image1.png)

## <a name="solution-overview"></a>Обзор решения

В этом сценарии можно вывести эти факты из Анализ требований к развертыванию:

- Из-за ограничений безопасности и конфигурации сети нельзя настроить в рабочую среду для поддержки одним щелчком или автоматического развертывания. Автономное развертывание является единственной допустимой подходом в этом сценарии.
- В рабочей среде включает в себя несколько веб-серверов, поэтому веб-фермы (WFF) можно использовать для создания фермы серверов. Используя этот подход, администратору необходимо лишь для импорта приложения на одном веб-сервере (сервере-источнике) и WFF будут реплицироваться на все другие серверы в рабочей среде развертывания.

В этих разделах содержатся сведения, необходимые для выполнения этих задач:

- [Создание фермы серверов с Web Farm Framework](configuring-a-database-server-for-web-deploy-publishing.md). Описывается, как создать и настроить ферму серверов с помощью WFF, таким образом, чтобы веб-платформы продуктов и компонентов, параметров конфигурации и веб-сайтов и приложений реплицируются по нескольким балансировки нагрузки веб-серверам.
- [Настройка веб-сервер для публикации (автономного развертывания) веб-развертывания](configuring-a-web-server-for-web-deploy-publishing-offline-deployment.md). В этом разделе описывается построение веб-сервера, которая позволяет администраторам Импорт и развертывание веб-пакеты вручную, начиная с Windows Server 2008 R2 чистую сборку.
- [Настройка сервера базы данных для публикации веб-развертывания](configuring-a-database-server-for-web-deploy-publishing.md). В этом разделе описывается настройка сервера базы данных для поддержки удаленного доступа и развертывания, начиная с установки по умолчанию SQL Server 2008 R2.

## <a name="further-reading"></a>Дополнительные сведения

Рекомендации по настройке среды тестирования типичного разработчика см. в разделе [сценарий: Настройка тестовой среды для веб-развертывания](scenario-configuring-a-test-environment-for-web-deployment.md). За инструкциями по настройке обычной промежуточной среде. в разделе [сценарий: Настройка в среде промежуточного хранения для развертывания веб-](scenario-configuring-a-staging-environment-for-web-deployment.md).

>[!div class="step-by-step"]
[Назад](scenario-configuring-a-staging-environment-for-web-deployment.md)
[Вперед](configuring-a-web-server-for-web-deploy-publishing-remote-agent.md)