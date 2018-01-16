---
uid: mvc/overview/older-versions-1/models-data/validating-with-the-idataerrorinfo-interface-cs
title: "Проверка с помощью IDataErrorInfo-интерфейс (C#) | Документы Microsoft"
author: StephenWalther
description: "Стивен Вальтер показано, как для отображения пользовательской проверки ошибок, реализовав интерфейс IDataErrorInfo в классе модели."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/02/2009
ms.topic: article
ms.assetid: 4733b9f1-9999-48fb-8b73-6038fbcc5ecb
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/models-data/validating-with-the-idataerrorinfo-interface-cs
msc.type: authoredcontent
ms.openlocfilehash: c04088c576481e4a91676d7e6962c03b56e7a8a4
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2017
---
<a name="validating-with-the-idataerrorinfo-interface-c"></a><span data-ttu-id="7578a-103">Проверка с помощью IDataErrorInfo-интерфейс (C#)</span><span class="sxs-lookup"><span data-stu-id="7578a-103">Validating with the IDataErrorInfo Interface (C#)</span></span>
====================
<span data-ttu-id="7578a-104">по [Стивен Вальтер](https://github.com/StephenWalther)</span><span class="sxs-lookup"><span data-stu-id="7578a-104">by [Stephen Walther](https://github.com/StephenWalther)</span></span>

> <span data-ttu-id="7578a-105">Стивен Вальтер показано, как для отображения пользовательской проверки ошибок, реализовав интерфейс IDataErrorInfo в классе модели.</span><span class="sxs-lookup"><span data-stu-id="7578a-105">Stephen Walther shows you how to display custom validation error messages by implementing the IDataErrorInfo interface in a model class.</span></span>


<span data-ttu-id="7578a-106">Целью данного учебника является объяснить одним из подходов к проверке в приложении ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="7578a-106">The goal of this tutorial is to explain one approach to performing validation in an ASP.NET MVC application.</span></span> <span data-ttu-id="7578a-107">Вы научитесь допустить Отправка HTML-формы без указания значения для обязательных полей формы.</span><span class="sxs-lookup"><span data-stu-id="7578a-107">You learn how to prevent someone from submitting an HTML form without providing values for required form fields.</span></span> <span data-ttu-id="7578a-108">В этом учебнике вы узнаете, как выполнять проверку с помощью интерфейса IErrorDataInfo.</span><span class="sxs-lookup"><span data-stu-id="7578a-108">In this tutorial, you learn how to perform validation by using the IErrorDataInfo interface.</span></span>

## <a name="assumptions"></a><span data-ttu-id="7578a-109">Допущения</span><span class="sxs-lookup"><span data-stu-id="7578a-109">Assumptions</span></span>

<span data-ttu-id="7578a-110">В этом учебнике используется MoviesDB базы данных и таблицы базы данных фильмов.</span><span class="sxs-lookup"><span data-stu-id="7578a-110">In this tutorial, I'll use the MoviesDB database and the Movies database table.</span></span> <span data-ttu-id="7578a-111">Эта таблица содержит следующие столбцы:</span><span class="sxs-lookup"><span data-stu-id="7578a-111">This table has the following columns:</span></span>

<a id="0.5_table01"></a>


| <span data-ttu-id="7578a-112">**Имя столбца**</span><span class="sxs-lookup"><span data-stu-id="7578a-112">**Column Name**</span></span> | <span data-ttu-id="7578a-113">**Тип данных**</span><span class="sxs-lookup"><span data-stu-id="7578a-113">**Data Type**</span></span> | <span data-ttu-id="7578a-114">**Разрешить значения NULL**</span><span class="sxs-lookup"><span data-stu-id="7578a-114">**Allow Nulls**</span></span> |
| --- | --- | --- |
| <span data-ttu-id="7578a-115">Идентификатор</span><span class="sxs-lookup"><span data-stu-id="7578a-115">Id</span></span> | <span data-ttu-id="7578a-116">Int</span><span class="sxs-lookup"><span data-stu-id="7578a-116">Int</span></span> | <span data-ttu-id="7578a-117">False</span><span class="sxs-lookup"><span data-stu-id="7578a-117">False</span></span> |
| <span data-ttu-id="7578a-118">Заголовок</span><span class="sxs-lookup"><span data-stu-id="7578a-118">Title</span></span> | <span data-ttu-id="7578a-119">Nvarchar(100)</span><span class="sxs-lookup"><span data-stu-id="7578a-119">Nvarchar(100)</span></span> | <span data-ttu-id="7578a-120">False</span><span class="sxs-lookup"><span data-stu-id="7578a-120">False</span></span> |
| <span data-ttu-id="7578a-121">Директор</span><span class="sxs-lookup"><span data-stu-id="7578a-121">Director</span></span> | <span data-ttu-id="7578a-122">Nvarchar(100)</span><span class="sxs-lookup"><span data-stu-id="7578a-122">Nvarchar(100)</span></span> | <span data-ttu-id="7578a-123">False</span><span class="sxs-lookup"><span data-stu-id="7578a-123">False</span></span> |
| <span data-ttu-id="7578a-124">DateReleased</span><span class="sxs-lookup"><span data-stu-id="7578a-124">DateReleased</span></span> | <span data-ttu-id="7578a-125">DateTime</span><span class="sxs-lookup"><span data-stu-id="7578a-125">DateTime</span></span> | <span data-ttu-id="7578a-126">False</span><span class="sxs-lookup"><span data-stu-id="7578a-126">False</span></span> |


<span data-ttu-id="7578a-127">В этом учебнике. я использую Microsoft Entity Framework для создания классов модели моей базы данных.</span><span class="sxs-lookup"><span data-stu-id="7578a-127">In this tutorial, I use the Microsoft Entity Framework to generate my database model classes.</span></span> <span data-ttu-id="7578a-128">На рисунке 1 отображается фильма класс, созданный платформой Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="7578a-128">The Movie class generated by the Entity Framework is displayed in Figure 1.</span></span>


<span data-ttu-id="7578a-129">[![Сущность фильма](validating-with-the-idataerrorinfo-interface-cs/_static/image1.jpg)](validating-with-the-idataerrorinfo-interface-cs/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="7578a-129">[![The Movie entity](validating-with-the-idataerrorinfo-interface-cs/_static/image1.jpg)](validating-with-the-idataerrorinfo-interface-cs/_static/image1.png)</span></span>

<span data-ttu-id="7578a-130">**На рисунке 01**: фильма сущности ([Просмотр полноразмерное изображение](validating-with-the-idataerrorinfo-interface-cs/_static/image2.png))</span><span class="sxs-lookup"><span data-stu-id="7578a-130">**Figure 01**: The Movie entity([Click to view full-size image](validating-with-the-idataerrorinfo-interface-cs/_static/image2.png))</span></span>


> [!NOTE] 
> 
> <span data-ttu-id="7578a-131">Дополнительные сведения об использовании платформы Entity Framework для создания классов модели базы данных, видят Мои учебника под названием Creating Model Classes с платформой Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="7578a-131">To learn more about using the Entity Framework to generate your database model classes, see my tutorial entitled Creating Model Classes with the Entity Framework.</span></span>


## <a name="the-controller-class"></a><span data-ttu-id="7578a-132">Класс контроллера</span><span class="sxs-lookup"><span data-stu-id="7578a-132">The Controller Class</span></span>

<span data-ttu-id="7578a-133">Мы используем контроллера Home в список фильмов и создание новых фильмов.</span><span class="sxs-lookup"><span data-stu-id="7578a-133">We use the Home controller to list movies and create new movies.</span></span> <span data-ttu-id="7578a-134">В список 1 содержится код для этого класса.</span><span class="sxs-lookup"><span data-stu-id="7578a-134">The code for this class is contained in Listing 1.</span></span>

<span data-ttu-id="7578a-135">**Листинг 1 - Controllers\HomeController.cs**</span><span class="sxs-lookup"><span data-stu-id="7578a-135">**Listing 1 - Controllers\HomeController.cs**</span></span>

[!code-csharp[Main](validating-with-the-idataerrorinfo-interface-cs/samples/sample1.cs)]

<span data-ttu-id="7578a-136">Класс контроллера Home в листинге 1 содержит два действия Create().</span><span class="sxs-lookup"><span data-stu-id="7578a-136">The Home controller class in Listing 1 contains two Create() actions.</span></span> <span data-ttu-id="7578a-137">Первое действие отображает HTML-формы для создания новых фильмов.</span><span class="sxs-lookup"><span data-stu-id="7578a-137">The first action displays the HTML form for creating a new movie.</span></span> <span data-ttu-id="7578a-138">Второе действие Create() выполняет фактическое вставки новых фильма в базу данных.</span><span class="sxs-lookup"><span data-stu-id="7578a-138">The second Create() action performs the actual insert of the new movie into the database.</span></span> <span data-ttu-id="7578a-139">Второе действие Create() вызывается при отправке формы, отображаемого элементом первое действие Create() на сервер.</span><span class="sxs-lookup"><span data-stu-id="7578a-139">The second Create() action is invoked when the form displayed by the first Create() action is submitted to the server.</span></span>

<span data-ttu-id="7578a-140">Обратите внимание, что второе действие Create() содержит следующие строки кода:</span><span class="sxs-lookup"><span data-stu-id="7578a-140">Notice that the second Create() action contains the following lines of code:</span></span>

[!code-csharp[Main](validating-with-the-idataerrorinfo-interface-cs/samples/sample2.cs)]

<span data-ttu-id="7578a-141">Свойство IsValid возвращает значение false, если имеется ошибка проверки.</span><span class="sxs-lookup"><span data-stu-id="7578a-141">The IsValid property returns false when there is a validation error.</span></span> <span data-ttu-id="7578a-142">В этом случае отображается повторно создать представление, содержащее форма HTML для создания фильма.</span><span class="sxs-lookup"><span data-stu-id="7578a-142">In that case, the Create view that contains the HTML form for creating a movie is redisplayed.</span></span>

## <a name="creating-a-partial-class"></a><span data-ttu-id="7578a-143">Создание разделяемого класса</span><span class="sxs-lookup"><span data-stu-id="7578a-143">Creating a Partial Class</span></span>

<span data-ttu-id="7578a-144">Класс фильма создается платформой Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="7578a-144">The Movie class is generated by the Entity Framework.</span></span> <span data-ttu-id="7578a-145">Можно просмотреть код для класса фильм, если расширение файла MoviesDBModel.edmx в окне обозревателя решений и откройте в редакторе кода файл MoviesDBModel.Designer.cs (см. рис. 2).</span><span class="sxs-lookup"><span data-stu-id="7578a-145">You can see the code for the Movie class if you expand the MoviesDBModel.edmx file in the Solution Explorer window and open the MoviesDBModel.Designer.cs file in the Code Editor (see Figure 2).</span></span>


<span data-ttu-id="7578a-146">[![Код для сущности фильма](validating-with-the-idataerrorinfo-interface-cs/_static/image2.jpg)](validating-with-the-idataerrorinfo-interface-cs/_static/image3.png)</span><span class="sxs-lookup"><span data-stu-id="7578a-146">[![The code for the Movie entity](validating-with-the-idataerrorinfo-interface-cs/_static/image2.jpg)](validating-with-the-idataerrorinfo-interface-cs/_static/image3.png)</span></span>

<span data-ttu-id="7578a-147">**На рисунке 02**: код для сущности фильма ([Просмотр полноразмерное изображение](validating-with-the-idataerrorinfo-interface-cs/_static/image4.png))</span><span class="sxs-lookup"><span data-stu-id="7578a-147">**Figure 02**: The code for the Movie entity([Click to view full-size image](validating-with-the-idataerrorinfo-interface-cs/_static/image4.png))</span></span>


<span data-ttu-id="7578a-148">Класс фильма является частичным классом.</span><span class="sxs-lookup"><span data-stu-id="7578a-148">The Movie class is a partial class.</span></span> <span data-ttu-id="7578a-149">Это означает, что можно добавить еще один разделяемый класс с тем же именем, расширяющие функциональные возможности класса фильма.</span><span class="sxs-lookup"><span data-stu-id="7578a-149">That means that we can add another partial class with the same name to extend the functionality of the Movie class.</span></span> <span data-ttu-id="7578a-150">Мы добавим логику проверки в новый разделяемый класс.</span><span class="sxs-lookup"><span data-stu-id="7578a-150">We'll add our validation logic to the new partial class.</span></span>

<span data-ttu-id="7578a-151">Добавьте класс в списке 2 в папке «модели».</span><span class="sxs-lookup"><span data-stu-id="7578a-151">Add the class in Listing 2 to the Models folder.</span></span>

<span data-ttu-id="7578a-152">**Листинг 2 - Models\Movie.cs**</span><span class="sxs-lookup"><span data-stu-id="7578a-152">**Listing 2 - Models\Movie.cs**</span></span>

[!code-csharp[Main](validating-with-the-idataerrorinfo-interface-cs/samples/sample3.cs)]

<span data-ttu-id="7578a-153">Обратите внимание, что класс в списке 2 включает *частичного* модификатор.</span><span class="sxs-lookup"><span data-stu-id="7578a-153">Notice that the class in Listing 2 includes the *partial* modifier.</span></span> <span data-ttu-id="7578a-154">Любые методы или свойства, добавленные к этому классу становятся частью фильма класс, созданный платформой Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="7578a-154">Any methods or properties that you add to this class become part of the Movie class generated by the Entity Framework.</span></span>

## <a name="adding-onchanging-and-onchanged-partial-methods"></a><span data-ttu-id="7578a-155">Добавление OnChanging и OnChanged частичных методов</span><span class="sxs-lookup"><span data-stu-id="7578a-155">Adding OnChanging and OnChanged Partial Methods</span></span>

<span data-ttu-id="7578a-156">Если Entity Framework создает класс сущностей, Entity Framework автоматически добавляет разделяемые методы в класс.</span><span class="sxs-lookup"><span data-stu-id="7578a-156">When the Entity Framework generates an entity class, the Entity Framework adds partial methods to the class automatically.</span></span> <span data-ttu-id="7578a-157">Entity Framework создает OnChanging и OnChanged разделяемые методы, которые соответствуют каждому свойству класса.</span><span class="sxs-lookup"><span data-stu-id="7578a-157">The Entity Framework generates OnChanging and OnChanged partial methods that correspond to each property of the class.</span></span>

<span data-ttu-id="7578a-158">В случае класса фильма Entity Framework создает следующие методы:</span><span class="sxs-lookup"><span data-stu-id="7578a-158">In the case of the Movie class, the Entity Framework creates the following methods:</span></span>

- <span data-ttu-id="7578a-159">OnIdChanging</span><span class="sxs-lookup"><span data-stu-id="7578a-159">OnIdChanging</span></span>
- <span data-ttu-id="7578a-160">OnIdChanged</span><span class="sxs-lookup"><span data-stu-id="7578a-160">OnIdChanged</span></span>
- <span data-ttu-id="7578a-161">OnTitleChanging</span><span class="sxs-lookup"><span data-stu-id="7578a-161">OnTitleChanging</span></span>
- <span data-ttu-id="7578a-162">OnTitleChanged</span><span class="sxs-lookup"><span data-stu-id="7578a-162">OnTitleChanged</span></span>
- <span data-ttu-id="7578a-163">OnDirectorChanging</span><span class="sxs-lookup"><span data-stu-id="7578a-163">OnDirectorChanging</span></span>
- <span data-ttu-id="7578a-164">OnDirectorChanged</span><span class="sxs-lookup"><span data-stu-id="7578a-164">OnDirectorChanged</span></span>
- <span data-ttu-id="7578a-165">OnDateReleasedChanging</span><span class="sxs-lookup"><span data-stu-id="7578a-165">OnDateReleasedChanging</span></span>
- <span data-ttu-id="7578a-166">OnDateReleasedChanged</span><span class="sxs-lookup"><span data-stu-id="7578a-166">OnDateReleasedChanged</span></span>

<span data-ttu-id="7578a-167">Метод OnChanging вызывается правой перед изменением соответствующего свойства.</span><span class="sxs-lookup"><span data-stu-id="7578a-167">The OnChanging method is called right before the corresponding property is changed.</span></span> <span data-ttu-id="7578a-168">Метод OnChanged вызывается справа после изменения свойства.</span><span class="sxs-lookup"><span data-stu-id="7578a-168">The OnChanged method is called right after the property is changed.</span></span>

<span data-ttu-id="7578a-169">Можно воспользоваться преимуществами этих разделяемые методы для добавления логики проверки класс фильма.</span><span class="sxs-lookup"><span data-stu-id="7578a-169">You can take advantage of these partial methods to add validation logic to the Movie class.</span></span> <span data-ttu-id="7578a-170">Обновления класса фильма в списке 3 проверяет, что свойства Title и директора назначены непустых значений.</span><span class="sxs-lookup"><span data-stu-id="7578a-170">The update Movie class in Listing 3 verifies that the Title and Director properties are assigned nonempty values.</span></span>

> [!NOTE] 
> 
> <span data-ttu-id="7578a-171">Разделяемый метод — это метод, определенный в классе, который не является обязательным для реализации.</span><span class="sxs-lookup"><span data-stu-id="7578a-171">A partial method is a method defined in a class that you are not required to implement.</span></span> <span data-ttu-id="7578a-172">Если не реализовать частичный метод компилятор удаляет сигнатуру метода, и все вызовы метода, поэтому являются не во время выполнения затраты, связанные с разделяемого метода.</span><span class="sxs-lookup"><span data-stu-id="7578a-172">If you don't implement a partial method then the compiler removes the method signature and all calls to the method so there are no run-time costs associated with the partial method.</span></span> <span data-ttu-id="7578a-173">В редакторе кода Visual Studio можно добавить разделяемого метода, введя ключевое слово *частичного* за которыми следует пробел, чтобы просмотреть список частичными репликами для реализации.</span><span class="sxs-lookup"><span data-stu-id="7578a-173">In the Visual Studio Code Editor, you can add a partial method by typing the keyword *partial* followed by a space to view a list of partials to implement.</span></span>


<span data-ttu-id="7578a-174">**Листинг 3 - Models\Movie.cs**</span><span class="sxs-lookup"><span data-stu-id="7578a-174">**Listing 3 - Models\Movie.cs**</span></span>

[!code-csharp[Main](validating-with-the-idataerrorinfo-interface-cs/samples/sample4.cs)]

<span data-ttu-id="7578a-175">Например, при попытке присвоить свойству заголовка пустая строка, то сообщение об ошибке присваивается словарь с именем \_ошибок.</span><span class="sxs-lookup"><span data-stu-id="7578a-175">For example, if you attempt to assign an empty string to the Title property, then an error message is assigned to a Dictionary named \_errors.</span></span>

<span data-ttu-id="7578a-176">На этом этапе ничего не фактически произойдет, если назначить пустую строку для свойства Title и коллекцию ошибок добавляется к закрытому \_ошибки поля.</span><span class="sxs-lookup"><span data-stu-id="7578a-176">At this point, nothing actually happens when you assign an empty string to the Title property and an error is added to the private \_errors field.</span></span> <span data-ttu-id="7578a-177">Нам нужно реализовать интерфейс IDataErrorInfo для предоставления этих ошибок проверки для платформы ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="7578a-177">We need to implement the IDataErrorInfo interface to expose these validation errors to the ASP.NET MVC framework.</span></span>

## <a name="implementing-the-idataerrorinfo-interface"></a><span data-ttu-id="7578a-178">Реализация IDataErrorInfo-интерфейс</span><span class="sxs-lookup"><span data-stu-id="7578a-178">Implementing the IDataErrorInfo Interface</span></span>

<span data-ttu-id="7578a-179">IDataErrorInfo-интерфейс был частью .NET framework, начиная с первой версии.</span><span class="sxs-lookup"><span data-stu-id="7578a-179">The IDataErrorInfo interface has been part of the .NET framework since the first version.</span></span> <span data-ttu-id="7578a-180">Этот интерфейс является очень простым:</span><span class="sxs-lookup"><span data-stu-id="7578a-180">This interface is a very simple interface:</span></span>

[!code-csharp[Main](validating-with-the-idataerrorinfo-interface-cs/samples/sample5.cs)]

<span data-ttu-id="7578a-181">Если класс реализует интерфейс IDataErrorInfo, платформа ASP.NET MVC будет использовать этот интерфейс, при создании экземпляра класса.</span><span class="sxs-lookup"><span data-stu-id="7578a-181">If a class implements the IDataErrorInfo interface, the ASP.NET MVC framework will use this interface when creating an instance of the class.</span></span> <span data-ttu-id="7578a-182">Например контроллер Home действие Create() принимает экземпляр класса фильма:</span><span class="sxs-lookup"><span data-stu-id="7578a-182">For example, the Home controller Create() action accepts an instance of the Movie class:</span></span>

[!code-csharp[Main](validating-with-the-idataerrorinfo-interface-cs/samples/sample6.cs)]

<span data-ttu-id="7578a-183">Платформа ASP.NET MVC создает экземпляр фильм, переданные в действие Create() с помощью связывателя модели (DefaultModelBinder).</span><span class="sxs-lookup"><span data-stu-id="7578a-183">The ASP.NET MVC framework creates the instance of the Movie passed to the Create() action by using a model binder (the DefaultModelBinder).</span></span> <span data-ttu-id="7578a-184">Связыватель модели отвечает за создание экземпляра объекта фильма путем привязки к экземпляру объекта фильма поля формы HTML.</span><span class="sxs-lookup"><span data-stu-id="7578a-184">The model binder is responsible for creating an instance of the Movie object by binding the HTML form fields to an instance of the Movie object.</span></span>

<span data-ttu-id="7578a-185">DefaultModelBinder обнаруживает ли класс реализует интерфейс IDataErrorInfo.</span><span class="sxs-lookup"><span data-stu-id="7578a-185">The DefaultModelBinder detects whether or not a class implements the IDataErrorInfo interface.</span></span> <span data-ttu-id="7578a-186">Если класс реализует этот интерфейс связывателя модели вызывает IDataErrorInfo.this индексатора для каждого свойства класса.</span><span class="sxs-lookup"><span data-stu-id="7578a-186">If a class implements this interface then the model binder invokes the IDataErrorInfo.this indexer for each property of the class.</span></span> <span data-ttu-id="7578a-187">Если индексатор возвращает сообщение об ошибке связывателя модели добавляет это сообщение об ошибке для моделирования состояние автоматически.</span><span class="sxs-lookup"><span data-stu-id="7578a-187">If the indexer returns an error message then the model binder adds this error message to model state automatically.</span></span>

<span data-ttu-id="7578a-188">DefaultModelBinder также проверяет свойство IDataErrorInfo.Error.</span><span class="sxs-lookup"><span data-stu-id="7578a-188">The DefaultModelBinder also checks the IDataErrorInfo.Error property.</span></span> <span data-ttu-id="7578a-189">Это свойство предназначено для представления ошибки проверки конкретных не свойства, связанные с классом.</span><span class="sxs-lookup"><span data-stu-id="7578a-189">This property is intended to represent non-property specific validation errors associated with the class.</span></span> <span data-ttu-id="7578a-190">Например можно применить правило проверки, зависит от значений нескольких свойств класса фильма.</span><span class="sxs-lookup"><span data-stu-id="7578a-190">For example, you might want to enforce a validation rule that depends on the values of multiple properties of the Movie class.</span></span> <span data-ttu-id="7578a-191">В этом случае вернет ошибку проверки из свойства ошибки.</span><span class="sxs-lookup"><span data-stu-id="7578a-191">In that case, you would return a validation error from the Error property.</span></span>

<span data-ttu-id="7578a-192">Обновленный класс фильма в листинге 4 реализует интерфейс IDataErrorInfo.</span><span class="sxs-lookup"><span data-stu-id="7578a-192">The updated Movie class in Listing 4 implements the IDataErrorInfo interface.</span></span>

<span data-ttu-id="7578a-193">**Листинг 4 - Models\Movie.cs (реализует IDataErrorInfo)**</span><span class="sxs-lookup"><span data-stu-id="7578a-193">**Listing 4 - Models\Movie.cs (implements IDataErrorInfo)**</span></span>

[!code-csharp[Main](validating-with-the-idataerrorinfo-interface-cs/samples/sample7.cs)]

<span data-ttu-id="7578a-194">В листинге 4 проверяет свойство индексатора \_коллекцию ошибок, содержит ли ключ, который соответствует имени свойства, переданный индексатору.</span><span class="sxs-lookup"><span data-stu-id="7578a-194">In Listing 4, the indexer property checks the \_errors collection to see if it contains a key that corresponds to the property name passed to the indexer.</span></span> <span data-ttu-id="7578a-195">Если ошибки проверки, не связанный со свойством, возвращается пустая строка.</span><span class="sxs-lookup"><span data-stu-id="7578a-195">If there is no validation error associated with the property then an empty string is returned.</span></span>

<span data-ttu-id="7578a-196">Не требуется каким-либо образом с помощью измененного класса фильма контроллер Home.</span><span class="sxs-lookup"><span data-stu-id="7578a-196">You don't need to modify the Home controller in any way to use the modified Movie class.</span></span> <span data-ttu-id="7578a-197">Страницы, отображаемой на рис. 3 показано, что произойдет, если значение не указано для заголовка или директора поля формы.</span><span class="sxs-lookup"><span data-stu-id="7578a-197">The page displayed in Figure 3 illustrates what happens when no value is entered for the Title or Director form fields.</span></span>


<span data-ttu-id="7578a-198">[![Автоматическое создание методов действий](validating-with-the-idataerrorinfo-interface-cs/_static/image3.jpg)](validating-with-the-idataerrorinfo-interface-cs/_static/image5.png)</span><span class="sxs-lookup"><span data-stu-id="7578a-198">[![Creating action methods automatically](validating-with-the-idataerrorinfo-interface-cs/_static/image3.jpg)](validating-with-the-idataerrorinfo-interface-cs/_static/image5.png)</span></span>

<span data-ttu-id="7578a-199">**На рисунке 03**: форма с отсутствующими значениями ([Просмотр полноразмерное изображение](validating-with-the-idataerrorinfo-interface-cs/_static/image6.png))</span><span class="sxs-lookup"><span data-stu-id="7578a-199">**Figure 03**: A form with missing values ([Click to view full-size image](validating-with-the-idataerrorinfo-interface-cs/_static/image6.png))</span></span>


<span data-ttu-id="7578a-200">Обратите внимание, что DateReleased проверить автоматически.</span><span class="sxs-lookup"><span data-stu-id="7578a-200">Notice that the DateReleased value is validated automatically.</span></span> <span data-ttu-id="7578a-201">Так как свойство DateReleased не может принимать значение NULL, DefaultModelBinder приводит к возникновению ошибки проверки для этого свойства автоматически при не имеет значение.</span><span class="sxs-lookup"><span data-stu-id="7578a-201">Because the DateReleased property does not accept NULL values, the DefaultModelBinder generates a validation error for this property automatically when it does not have a value.</span></span> <span data-ttu-id="7578a-202">Если вы хотите изменить сообщение об ошибке для свойства DateReleased необходимо создать настраиваемый связыватель модели.</span><span class="sxs-lookup"><span data-stu-id="7578a-202">If you want to modify the error message for the DateReleased property then you need to create a custom model binder.</span></span>

## <a name="summary"></a><span data-ttu-id="7578a-203">Сводка</span><span class="sxs-lookup"><span data-stu-id="7578a-203">Summary</span></span>

<span data-ttu-id="7578a-204">В этом учебнике вы узнали, как использовать интерфейс IDataErrorInfo для формирования сообщений об ошибках проверки.</span><span class="sxs-lookup"><span data-stu-id="7578a-204">In this tutorial, you learned how to use the IDataErrorInfo interface to generate validation error messages.</span></span> <span data-ttu-id="7578a-205">Во-первых мы создали разделяемый класс фильм, который расширяет функциональность в разделяемый класс фильм, созданные платформой Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="7578a-205">First, we created a partial Movie class that extends the functionality of the partial Movie class generated by the Entity Framework.</span></span> <span data-ttu-id="7578a-206">Далее мы добавили логику проверки в фильма класса OnTitleChanging() и OnDirectorChanging() разделяемые методы.</span><span class="sxs-lookup"><span data-stu-id="7578a-206">Next, we added validation logic to the Movie class OnTitleChanging() and OnDirectorChanging() partial methods.</span></span> <span data-ttu-id="7578a-207">Наконец мы реализовали IDataErrorInfo-интерфейс для предоставления этих сообщений проверки с платформой ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="7578a-207">Finally, we implemented the IDataErrorInfo interface in order to expose these validation messages to the ASP.NET MVC framework.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="7578a-208">[Назад](performing-simple-validation-cs.md)
[Вперед](validating-with-a-service-layer-cs.md)</span><span class="sxs-lookup"><span data-stu-id="7578a-208">[Previous](performing-simple-validation-cs.md)
[Next](validating-with-a-service-layer-cs.md)</span></span>