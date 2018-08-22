---
title: включение файла
description: включение файла
services: functions
author: tdykstra
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: 2202cdebe77668972372983a0e802d00edabf6dd
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079688"
---
<span data-ttu-id="88f7e-103">Azure Cosmos DB — это глобально распределенная бессерверная многомодельная база данных Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="88f7e-103">Azure Cosmos DB is Microsoft's serverless, globally distributed, multi-model database.</span></span> <span data-ttu-id="88f7e-104">Из этого модуля вы узнаете, как с помощью службы "Функции Azure" сохранять и извлекать метаданные изображений в виде документов JSON в Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="88f7e-104">In this module, you learn how to use Azure Functions to store and retrieve image metadata as JSON documents in Cosmos DB.</span></span>

## <a name="create-a-cosmos-db-account-database-and-collection"></a><span data-ttu-id="88f7e-105">Создание базы данных, коллекции и у четной записи Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="88f7e-105">Create a Cosmos DB account, database, and collection</span></span>

<span data-ttu-id="88f7e-106">Учетная запись Cosmos DB — это ресурс Azure, который содержит базы данных Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="88f7e-106">A Cosmos DB account is an Azure resource that contains Cosmos DB databases.</span></span>

1. <span data-ttu-id="88f7e-107">Убедитесь, что окно Cloud Shell открыто.</span><span class="sxs-lookup"><span data-stu-id="88f7e-107">Ensure you are still signed into the Cloud Shell.</span></span>  <span data-ttu-id="88f7e-108">Если вы уже вышли, выберите **Enter focus mode** (Перейти в режим фокусировки), чтобы открыть окно Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="88f7e-108">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="88f7e-109">Создайте учетную запись Cosmos DB с уникальным именем в той же группе ресурсов, где хранятся другие ресурсы, созданные при работе с этим руководством.</span><span class="sxs-lookup"><span data-stu-id="88f7e-109">Create a Cosmos DB account with a unique name in the same resource group as the other resources in this tutorial.</span></span>

    ```azurecli
    az cosmosdb create -g first-serverless-app -n <cosmos db account name>
    ```

1. <span data-ttu-id="88f7e-110">Создав учетную запись Cosmos DB, создайте в ней базу данных с именем **imagesdb**.</span><span class="sxs-lookup"><span data-stu-id="88f7e-110">After the Cosmos DB account is created, create a new database named **imagesdb** in the account.</span></span>

    ```azurecli
    az cosmosdb database create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb
    ```

1. <span data-ttu-id="88f7e-111">Когда база данных будет создана, создайте в ней коллекцию с именем **images** и пропускной способностью 400 единиц запросов (ЕЗ).</span><span class="sxs-lookup"><span data-stu-id="88f7e-111">After the database is created, create a new collection named **images** in the database with a throughput of 400 request units (RUs).</span></span>

    ```azurecli
    az cosmosdb collection create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb --collection-name images --throughput 400
    ```


## <a name="save-a-document-to-cosmos-db-when-a-thumbnail-is-created"></a><span data-ttu-id="88f7e-112">Сохранение документа в Cosmos DB при создании эскиза</span><span class="sxs-lookup"><span data-stu-id="88f7e-112">Save a document to Cosmos DB when a thumbnail is created</span></span>

<span data-ttu-id="88f7e-113">Выходная привязка Cosmos DB позволяет создавать документы в коллекции Cosmos DB из службы "Функции Azure".</span><span class="sxs-lookup"><span data-stu-id="88f7e-113">The Cosmos DB output binding lets you create documents in a Cosmos DB collection from Azure Functions.</span></span> <span data-ttu-id="88f7e-114">На следующих этапах вы настроите выходную привязку Cosmos DB в функции **ResizeImage** и измените функцию, чтобы она возвращала документ (объект) для сохранения.</span><span class="sxs-lookup"><span data-stu-id="88f7e-114">In the following steps, you configure a Cosmos DB output binding in the **ResizeImage** function and modify the function to return a document (object) to be saved.</span></span>

1. <span data-ttu-id="88f7e-115">Откройте приложение-функцию на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="88f7e-115">Open the function app in the Azure Portal.</span></span>

1. <span data-ttu-id="88f7e-116">В области навигации слева разверните узел функции **ResizeImage** и выберите **Интегрировать**.</span><span class="sxs-lookup"><span data-stu-id="88f7e-116">In the left hand navigation, expand the **ResizeImage** function, and then select **Integrate**.</span></span>

1. <span data-ttu-id="88f7e-117">В разделе **Выходные данные** щелкните **Новое выходное значение**.</span><span class="sxs-lookup"><span data-stu-id="88f7e-117">Under **Outputs**, click **New Output**.</span></span>

1. <span data-ttu-id="88f7e-118">Найдите и выберите элемент **Azure Cosmos DB**.</span><span class="sxs-lookup"><span data-stu-id="88f7e-118">Find the **Azure Cosmos DB** item and select it.</span></span> <span data-ttu-id="88f7e-119">Затем щелкните **Выбрать**.</span><span class="sxs-lookup"><span data-stu-id="88f7e-119">Then click **Select**.</span></span>

    ![Выбор элемента "Новое выходное значение"](media/functions-first-serverless-web-app/4-new-output.jpg)

1. <span data-ttu-id="88f7e-121">Заполните поля в разделе **Azure Cosmos DB output** (Выходные данные Azure Cosmos DB) указанными ниже значениями.</span><span class="sxs-lookup"><span data-stu-id="88f7e-121">Fill out the fields under **Azure Cosmos DB output** with the following values.</span></span>

    | <span data-ttu-id="88f7e-122">Параметр</span><span class="sxs-lookup"><span data-stu-id="88f7e-122">Setting</span></span>      |  <span data-ttu-id="88f7e-123">Рекомендуемое значение</span><span class="sxs-lookup"><span data-stu-id="88f7e-123">Suggested value</span></span>   | <span data-ttu-id="88f7e-124">ОПИСАНИЕ</span><span class="sxs-lookup"><span data-stu-id="88f7e-124">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="88f7e-125">**Имя параметра документа**</span><span class="sxs-lookup"><span data-stu-id="88f7e-125">**Document parameter name**</span></span> | <span data-ttu-id="88f7e-126">Выберите **Использовать возвращаемое значение функции**.</span><span class="sxs-lookup"><span data-stu-id="88f7e-126">Select **Use function return value**</span></span> | <span data-ttu-id="88f7e-127">В текстовом поле автоматически указывается значение **$return**.</span><span class="sxs-lookup"><span data-stu-id="88f7e-127">The value of the textbox is automatically set to **$return**.</span></span> |
    | <span data-ttu-id="88f7e-128">**Database name** (Имя базы данных)</span><span class="sxs-lookup"><span data-stu-id="88f7e-128">**Database name**</span></span> | <span data-ttu-id="88f7e-129">imagesdb</span><span class="sxs-lookup"><span data-stu-id="88f7e-129">imagesdb</span></span> | <span data-ttu-id="88f7e-130">Используйте имя созданной вами базы данных.</span><span class="sxs-lookup"><span data-stu-id="88f7e-130">Use the name of the database that you created.</span></span> |
    | <span data-ttu-id="88f7e-131">**Имя коллекции**</span><span class="sxs-lookup"><span data-stu-id="88f7e-131">**Collection name**</span></span> | <span data-ttu-id="88f7e-132">images</span><span class="sxs-lookup"><span data-stu-id="88f7e-132">images</span></span> | <span data-ttu-id="88f7e-133">Используйте имя созданной вами коллекции.</span><span class="sxs-lookup"><span data-stu-id="88f7e-133">Use the name of the collection that you created.</span></span> |

1. <span data-ttu-id="88f7e-134">Рядом с элементом **Подключение учетной записи Azure Cosmos DB** щелкните ссылку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="88f7e-134">Next to **Azure Cosmos DB account connection**, click **new**.</span></span> <span data-ttu-id="88f7e-135">Выберите учетную запись Cosmos DB, которую вы создали ранее.</span><span class="sxs-lookup"><span data-stu-id="88f7e-135">Select the Cosmos DB account you previously created.</span></span>

    ![Ввод параметров для выходной привязки Azure Cosmos DB](media/functions-first-serverless-web-app/4-cosmos-db-output.png)

1. <span data-ttu-id="88f7e-137">Нажмите кнопку **Сохранить**, чтобы создать выходную привязку Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="88f7e-137">Click **Save** to create the Cosmos DB output binding.</span></span>

1. <span data-ttu-id="88f7e-138">Щелкните имя функции **ResizeImage** слева, чтобы открыть функцию.</span><span class="sxs-lookup"><span data-stu-id="88f7e-138">Click on the **ResizeImage** function name on the left to open the function.</span></span>

1. <span data-ttu-id="88f7e-139">**C#**</span><span class="sxs-lookup"><span data-stu-id="88f7e-139">**C#**</span></span>

    1. <span data-ttu-id="88f7e-140">(C#) Измените тип возвращаемого значения функции с **void** на **object**.</span><span class="sxs-lookup"><span data-stu-id="88f7e-140">(C#) Change the return type of the function from **void** to **object**.</span></span>

    1. <span data-ttu-id="88f7e-141">(C#) В конец функции добавьте следующий блок кода, чтобы она возвращала сохраняемый документ:</span><span class="sxs-lookup"><span data-stu-id="88f7e-141">(C#) At the end of the function, add the following code block to return the document to be saved:</span></span>
    
        ```csharp
        return new {
            id = name,
            imgPath = "/images/" + name,
            thumbnailPath = "/thumbnails/" + name
        };
        ```
    
        ![Измененный файл run.csx для функции ResizeImages](media/functions-first-serverless-web-app/4-update-function.png)

1. <span data-ttu-id="88f7e-143">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="88f7e-143">**JavaScript**</span></span>

    1. <span data-ttu-id="88f7e-144">(JavaScript) Измените инструкцию `context.done()` в предложении `else`, чтобы возвращался документ, сохраняемый в Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="88f7e-144">(JavaScript) Change the `context.done()` statement in the `else` clause to return the document to be saved to Cosmos DB.</span></span>

    ```javascript
    if (error) {
        context.done(error);
    } else {
        context.bindings.thumbnail = stream;
        context.done(null, {
            id: context.bindingData.name,
            imgPath: "/images/" + context.bindingData.name,
            thumbnailPath: "/thumbnails/" + context.bindingData.name
        });
    }
    ```

1. <span data-ttu-id="88f7e-145">Щелкните **Журналы** под окном кода, чтобы развернуть панель журналов.</span><span class="sxs-lookup"><span data-stu-id="88f7e-145">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="88f7e-146">Выберите команду **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="88f7e-146">Click **Save**.</span></span> <span data-ttu-id="88f7e-147">Просмотрите записи на панели журналов и проверьте, успешно ли сохранена функция и нет ли ошибок.</span><span class="sxs-lookup"><span data-stu-id="88f7e-147">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="create-a-function-to-list-images-from-cosmos-db"></a><span data-ttu-id="88f7e-148">Создание функции для получения списка изображений из Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="88f7e-148">Create a function to list images from Cosmos DB</span></span>

<span data-ttu-id="88f7e-149">Чтобы получать метаданные изображения из Cosmos DB, веб-приложению требуется API.</span><span class="sxs-lookup"><span data-stu-id="88f7e-149">The web application requires an API to retrieve image metadata from Cosmos DB.</span></span> <span data-ttu-id="88f7e-150">На следующих этапах</span><span class="sxs-lookup"><span data-stu-id="88f7e-150">In the following steps.</span></span> <span data-ttu-id="88f7e-151">вы создадите функцию, активируемую HTTP-запросом, которая использует входную привязку Cosmos DB, чтобы запросить коллекцию базы данных.</span><span class="sxs-lookup"><span data-stu-id="88f7e-151">uou create an HTTP triggered function that uses a Cosmos DB input binding to query the database collection.</span></span>

1. <span data-ttu-id="88f7e-152">В приложении-функции наведите указатель мыши на элемент **Функции** слева и щелкните **+**, чтобы создать функцию.</span><span class="sxs-lookup"><span data-stu-id="88f7e-152">In your function app, hover over **Functions** on the left and click **+** to create a new function.</span></span>

1. <span data-ttu-id="88f7e-153">Найдите и выберите шаблон **HttpTrigger**.</span><span class="sxs-lookup"><span data-stu-id="88f7e-153">Find the **HttpTrigger** template and select it.</span></span>

1. <span data-ttu-id="88f7e-154">Задайте указанные ниже значения, чтобы создать функцию, которая генерирует URL-адрес для получения изображений.</span><span class="sxs-lookup"><span data-stu-id="88f7e-154">Use these values to create a function that generates a get images URL.</span></span>

    | <span data-ttu-id="88f7e-155">Параметр</span><span class="sxs-lookup"><span data-stu-id="88f7e-155">Setting</span></span>      |  <span data-ttu-id="88f7e-156">Рекомендуемое значение</span><span class="sxs-lookup"><span data-stu-id="88f7e-156">Suggested value</span></span>   | <span data-ttu-id="88f7e-157">ОПИСАНИЕ</span><span class="sxs-lookup"><span data-stu-id="88f7e-157">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="88f7e-158">**Имя функции**</span><span class="sxs-lookup"><span data-stu-id="88f7e-158">**Name your function**</span></span> | <span data-ttu-id="88f7e-159">GetImages</span><span class="sxs-lookup"><span data-stu-id="88f7e-159">GetImages</span></span> | <span data-ttu-id="88f7e-160">Введите это имя именно так, как показано, чтобы приложение смогло обнаружить функцию.</span><span class="sxs-lookup"><span data-stu-id="88f7e-160">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="88f7e-161">**Уровень авторизации**</span><span class="sxs-lookup"><span data-stu-id="88f7e-161">**Authorization level**</span></span> | <span data-ttu-id="88f7e-162">Анонимные</span><span class="sxs-lookup"><span data-stu-id="88f7e-162">Anonymous</span></span> | <span data-ttu-id="88f7e-163">Предоставьте общий доступ к функции.</span><span class="sxs-lookup"><span data-stu-id="88f7e-163">Allow the function to be accessed publicly.</span></span> |

1. <span data-ttu-id="88f7e-164">Нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="88f7e-164">Click **Create**.</span></span>

1. <span data-ttu-id="88f7e-165">Создав функцию, щелкните **Интегрировать** под ее именем в области навигации слева.</span><span class="sxs-lookup"><span data-stu-id="88f7e-165">When the new function is created, click **Integrate** under the function's name on the left navigation.</span></span>

1. <span data-ttu-id="88f7e-166">Выберите **Новое входное значение** и **Azure Cosmos DB**.</span><span class="sxs-lookup"><span data-stu-id="88f7e-166">Click **New Input** and select **Azure Cosmos DB**.</span></span> 

    ![Выбор элемента "Новое входное значение"](media/functions-first-serverless-web-app/4-new-input.jpg)

1. <span data-ttu-id="88f7e-168">Нажмите кнопку **Выбрать**.</span><span class="sxs-lookup"><span data-stu-id="88f7e-168">Click **Select**.</span></span>

1. <span data-ttu-id="88f7e-169">Введите следующие значения:</span><span class="sxs-lookup"><span data-stu-id="88f7e-169">Fill out the following values:</span></span>

    | <span data-ttu-id="88f7e-170">Параметр</span><span class="sxs-lookup"><span data-stu-id="88f7e-170">Setting</span></span>      |  <span data-ttu-id="88f7e-171">Рекомендуемое значение</span><span class="sxs-lookup"><span data-stu-id="88f7e-171">Suggested value</span></span>   | <span data-ttu-id="88f7e-172">ОПИСАНИЕ</span><span class="sxs-lookup"><span data-stu-id="88f7e-172">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="88f7e-173">**Имя параметра документа**</span><span class="sxs-lookup"><span data-stu-id="88f7e-173">**Document parameter name**</span></span> | <span data-ttu-id="88f7e-174">documents</span><span class="sxs-lookup"><span data-stu-id="88f7e-174">documents</span></span> | <span data-ttu-id="88f7e-175">Совпадает с именем параметра в функции.</span><span class="sxs-lookup"><span data-stu-id="88f7e-175">Matches parameter name in the function.</span></span> |
    | <span data-ttu-id="88f7e-176">**Database name** (Имя базы данных)</span><span class="sxs-lookup"><span data-stu-id="88f7e-176">**Database name**</span></span> | <span data-ttu-id="88f7e-177">imagesdb</span><span class="sxs-lookup"><span data-stu-id="88f7e-177">imagesdb</span></span> |  |
    | <span data-ttu-id="88f7e-178">**Имя коллекции**</span><span class="sxs-lookup"><span data-stu-id="88f7e-178">**Collection name**</span></span> | <span data-ttu-id="88f7e-179">images</span><span class="sxs-lookup"><span data-stu-id="88f7e-179">images</span></span> |  |
    | <span data-ttu-id="88f7e-180">**SQL query**</span><span class="sxs-lookup"><span data-stu-id="88f7e-180">**SQL query**</span></span> | <span data-ttu-id="88f7e-181">select \* from c order by c._ts desc</span><span class="sxs-lookup"><span data-stu-id="88f7e-181">select \* from c order by c._ts desc</span></span> | <span data-ttu-id="88f7e-182">Получение документов. Сначала вы получите документы, которые были созданы последними.</span><span class="sxs-lookup"><span data-stu-id="88f7e-182">Get documents, latest documents first.</span></span> |
    | <span data-ttu-id="88f7e-183">**Подключение учетной записи Azure Cosmos DB**</span><span class="sxs-lookup"><span data-stu-id="88f7e-183">**Azure Cosmos DB account connection**</span></span> | <span data-ttu-id="88f7e-184">Выберите существующую строку подключения.</span><span class="sxs-lookup"><span data-stu-id="88f7e-184">Select existing connection string</span></span> |  |

1. <span data-ttu-id="88f7e-185">Нажмите кнопку **Сохранить**, чтобы создать входную привязку.</span><span class="sxs-lookup"><span data-stu-id="88f7e-185">Click **Save** to create the input binding.</span></span>

1. <span data-ttu-id="88f7e-186">**C#**</span><span class="sxs-lookup"><span data-stu-id="88f7e-186">**C#**</span></span>

    1. <span data-ttu-id="88f7e-187">Щелкните имя функции, чтобы открыть окно кода, а затем замените содержимое файла **run.csx** содержимым из репозитория [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).</span><span class="sxs-lookup"><span data-stu-id="88f7e-187">Click the function's name to open the code window, and then replace all of **run.csx** with the content in [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).</span></span>

1. <span data-ttu-id="88f7e-188">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="88f7e-188">**JavaScript**</span></span>

    1. <span data-ttu-id="88f7e-189">Щелкните имя функции, чтобы открыть окно кода, а затем замените содержимое файла **index.js** содержимым из репозитория [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).</span><span class="sxs-lookup"><span data-stu-id="88f7e-189">Click the function's name to open the code window, and then replace all of **index.js** with the content in [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).</span></span>

1. <span data-ttu-id="88f7e-190">Щелкните **Журналы** под окном кода, чтобы развернуть панель журналов.</span><span class="sxs-lookup"><span data-stu-id="88f7e-190">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="88f7e-191">Выберите команду **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="88f7e-191">Click **Save**.</span></span> <span data-ttu-id="88f7e-192">Просмотрите записи на панели журналов и проверьте, успешно ли сохранена функция и нет ли ошибок.</span><span class="sxs-lookup"><span data-stu-id="88f7e-192">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-application"></a><span data-ttu-id="88f7e-193">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="88f7e-193">Test the application</span></span>

1. <span data-ttu-id="88f7e-194">Откройте приложение в браузере.</span><span class="sxs-lookup"><span data-stu-id="88f7e-194">Open the application in a browser.</span></span> <span data-ttu-id="88f7e-195">Выберите файл изображения и отправьте его.</span><span class="sxs-lookup"><span data-stu-id="88f7e-195">Select an image file and upload it.</span></span>

1. <span data-ttu-id="88f7e-196">Через несколько секунд на странице появится эскиз нового изображения.</span><span class="sxs-lookup"><span data-stu-id="88f7e-196">After a few seconds, the thumbnail of the new image appears on the page.</span></span>

1. <span data-ttu-id="88f7e-197">На портале Azure используйте поле поиска, чтобы найти учетную запись Cosmos DB по имени.</span><span class="sxs-lookup"><span data-stu-id="88f7e-197">In the Azure portal, use the Search box to search for your Cosmos DB account by name.</span></span> <span data-ttu-id="88f7e-198">Щелкните ее, чтобы открыть.</span><span class="sxs-lookup"><span data-stu-id="88f7e-198">Click it to open it.</span></span>

1. <span data-ttu-id="88f7e-199">Выберите **обозреватель данных** слева, чтобы просмотреть коллекции и документы.</span><span class="sxs-lookup"><span data-stu-id="88f7e-199">Click **Data Explorer** on the left to browse collections and documents.</span></span>

1. <span data-ttu-id="88f7e-200">В разделе базы данных **imagesdb** выберите коллекцию **images**.</span><span class="sxs-lookup"><span data-stu-id="88f7e-200">Under the **imagesdb** database, select the **images** collection.</span></span>

1. <span data-ttu-id="88f7e-201">Проверьте, создан ли документ для отправленного изображения.</span><span class="sxs-lookup"><span data-stu-id="88f7e-201">Confirm that a document was created for the uploaded image.</span></span>

    ![Обозреватель данных, в котором показан документ для отправленного изображения](media/functions-first-serverless-web-app/4-data-explorer.png)



## <a name="summary"></a><span data-ttu-id="88f7e-203">Сводка</span><span class="sxs-lookup"><span data-stu-id="88f7e-203">Summary</span></span>

<span data-ttu-id="88f7e-204">Из этого модуля вы узнали, как создать учетную запись, базу данных и коллекцию Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="88f7e-204">In this module, you learned how to create a Cosmos DB account, database, and collection.</span></span> <span data-ttu-id="88f7e-205">Также вы научились сохранять и извлекать метаданные изображения в коллекции Cosmos DB, используя привязки Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="88f7e-205">You also learned how to use the Cosmos DB bindings to save and retrieve image metadata in the Cosmos DB collection.</span></span> <span data-ttu-id="88f7e-206">Далее вы узнаете, как с помощью Microsoft Cognitive Services автоматически создавать подписи для каждого отправленного изображения.</span><span class="sxs-lookup"><span data-stu-id="88f7e-206">Next, you learn how to automatically generate a caption for each uploaded image using Microsoft Cognitive Services.</span></span>