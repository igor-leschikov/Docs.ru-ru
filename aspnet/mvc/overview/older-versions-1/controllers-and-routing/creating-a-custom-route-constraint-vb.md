---
uid: mvc/overview/older-versions-1/controllers-and-routing/creating-a-custom-route-constraint-vb
title: "Создание ограничения маршрута Custom (Visual Basic) | Документы Microsoft"
author: StephenWalther
description: "Стивен Вальтер показано, как можно создать ограничение настраиваемый маршрут. Мы реализуем простой пользовательский ограничение, которое блокирует маршрут соответствует w..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/16/2009
ms.topic: article
ms.assetid: 892edb27-1cc2-4eaf-8314-dbc2efc6228a
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/controllers-and-routing/creating-a-custom-route-constraint-vb
msc.type: authoredcontent
ms.openlocfilehash: 41b04c1fea267e7ee9f8a0b1c2f0d4fe4bb96d15
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2017
---
<a name="creating-a-custom-route-constraint-vb"></a>Создание ограничения маршрута Custom (Visual Basic)
====================
по [Стивен Вальтер](https://github.com/StephenWalther)

> Стивен Вальтер показано, как можно создать ограничение настраиваемый маршрут. Мы реализовали простой пользовательский ограничение, которое предотвращает маршрут соответствует при запрос браузера с удаленного компьютера.


Целью данного учебника является демонстрация как можно создать ограничение настраиваемый маршрут. Настраиваемый маршрут ограничение позволяет предотвратить сравниваемыми, если совпадают некоторые настраиваемого условия маршрута.

В этом учебнике мы создания ограничения маршрута Localhost. Ограничения маршрута Localhost соответствует только запросы от локального компьютера. Удаленные запросы из через Интернет не соответствуют.

Реализовать ограничение настраиваемый маршрут, реализовав интерфейс IRouteConstraint. Это очень простой интерфейс, который описывает один метод:

[!code-vb[Main](creating-a-custom-route-constraint-vb/samples/sample1.vb)]

Метод возвращает логическое значение. Если возвращается значение False, маршрута, связанного с ограничением не соответствует запросу браузера.

Ограничение Localhost содержится в листинге 1.

**Листинг 1 - LocalhostConstraint.vb**

[!code-vb[Main](creating-a-custom-route-constraint-vb/samples/sample2.vb)]

Ограничение в список 1 использует свойство IsLocal класса HttpRequest. Это свойство возвращает значение true, если IP-адрес запроса либо 127.0.0.1 или если IP-адрес запроса будет таким же, как IP-адрес сервера.

Можно использовать пользовательские ограничения маршрута, определен в файле Global.asax. Файл Global.asax в списке 2 использует ограничение Localhost чтобы запретить запросе страницы администрирования, если они не выполняете запрос с локального сервера. Например запрос /Admin/DeleteAll завершится ошибкой при внесении с удаленного сервера.

**Листинг 2 - Global.asax**

[!code-vb[Main](creating-a-custom-route-constraint-vb/samples/sample3.vb)]

Ограничение Localhost используется в определении маршрута администратора. Запрос удаленной браузера, не будет соответствовать этот маршрут. Однако поймите, что тот же запрос может совпадать с других маршрутов, указанных в файле Global.asax. Важно понимать, что ограничение не позволяет сопоставить запрос на конкретный маршрут и не все маршруты, определенный в файле Global.asax.

Обратите внимание, что маршрут по умолчанию был закомментирован из файла Global.asax в списке 2. При включении маршрута по умолчанию маршрута по умолчанию будет соответствовать запросов для администрирования контроллера. В этом случае удаленные пользователи по-прежнему удалось вызвать действий администратора контроллера, несмотря на то, что их запросы не соответствует маршруту администратора.

>[!div class="step-by-step"]
[Назад](creating-a-route-constraint-vb.md)