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
ms.openlocfilehash: 88b0ac838dfa8e097a30cc6cef591377e4a95ad8
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079598"
---
<span data-ttu-id="33d53-103">Из предыдущего модуля вы узнали, как с помощью бессерверной функции упростить безопасную отправку изображений из веб-приложения в хранилище BLOB-объектов.</span><span class="sxs-lookup"><span data-stu-id="33d53-103">In the previous module, you saw how a serverless function can facilitate the secure uploading of images to Blob storage from a web application.</span></span> <span data-ttu-id="33d53-104">В этом модуле вы создадите другую бессерверную функцию, которая позволяет просматривать изображения и создавать из них эскизы.</span><span class="sxs-lookup"><span data-stu-id="33d53-104">In this module, you create another serverless function to watch for uploaded images and create thumbnails from them.</span></span>

## <a name="create-a-blob-storage-container-for-thumbnails"></a><span data-ttu-id="33d53-105">Создание контейнера хранилища BLOB-объектов для эскизов</span><span class="sxs-lookup"><span data-stu-id="33d53-105">Create a blob storage container for thumbnails</span></span>

<span data-ttu-id="33d53-106">Полноразмерные изображения хранятся в контейнере с именем **images**.</span><span class="sxs-lookup"><span data-stu-id="33d53-106">The full size images are stored in a container named **images**.</span></span> <span data-ttu-id="33d53-107">Для хранения эскизов этих изображений вам потребуется другой контейнер.</span><span class="sxs-lookup"><span data-stu-id="33d53-107">You need another container to store thumbnails of those images.</span></span>

1. <span data-ttu-id="33d53-108">Убедитесь, что окно Cloud Shell (Bash) открыто.</span><span class="sxs-lookup"><span data-stu-id="33d53-108">Ensure you are still signed in to the Cloud Shell (bash).</span></span>  <span data-ttu-id="33d53-109">Если вы уже вышли, выберите **Enter focus mode** (Перейти в режим фокусировки), чтобы открыть окно Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="33d53-109">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="33d53-110">Создайте в своей учетной записи хранения контейнер с именем **thumbnails** с общим доступом ко всем большим двоичным объектам.</span><span class="sxs-lookup"><span data-stu-id="33d53-110">Create a new container named **thumbnails** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n thumbnails --account-name <storage account name> --public-access blob
    ```


## <a name="create-a-blob-triggered-serverless-function"></a><span data-ttu-id="33d53-111">Создание бессерверной функции, активируемой большим двоичным объектом</span><span class="sxs-lookup"><span data-stu-id="33d53-111">Create a blob-triggered serverless function</span></span>

<span data-ttu-id="33d53-112">Триггер определяет способ вызова функции.</span><span class="sxs-lookup"><span data-stu-id="33d53-112">A trigger defines how a function is invoked.</span></span> <span data-ttu-id="33d53-113">Функция, которую вы создадите, использует триггер больших двоичных объектов: она автоматически вызывается при отправке большого двоичного объекта (файл изображения) в контейнер **images**.</span><span class="sxs-lookup"><span data-stu-id="33d53-113">The function you create next uses a blob trigger: the function is automatically invoked when a blob (image file) is uploaded to the **images** container.</span></span> <span data-ttu-id="33d53-114">У функции должен быть один триггер.</span><span class="sxs-lookup"><span data-stu-id="33d53-114">A function must have one trigger.</span></span> <span data-ttu-id="33d53-115">С триггерами могут быть связанны данные, которые обычно являются полезными и активируют функцию.</span><span class="sxs-lookup"><span data-stu-id="33d53-115">Triggers have associated data, which is usually the payload that triggered the function.</span></span>

<span data-ttu-id="33d53-116">Привязки определяют, как функция считывает или записывает данные в Azure или сторонних службах.</span><span class="sxs-lookup"><span data-stu-id="33d53-116">Bindings define how a function reads or writes data in Azure or third-party services.</span></span> <span data-ttu-id="33d53-117">Для функции создается эскиз изображения, при отправке которого она активируется. Этот эскиз сохраняется в контейнер *thumbnails*.</span><span class="sxs-lookup"><span data-stu-id="33d53-117">This function creates a thumbnail version of the image that triggers it and saves the thumbnail in a *thumbnails* container.</span></span>

1. <span data-ttu-id="33d53-118">Откройте приложение-функцию на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="33d53-118">Open your function app in the Azure Portal.</span></span>

1. <span data-ttu-id="33d53-119">В окне приложения-функции наведите указатель мыши на элемент **Функции** в области навигации слева и нажмите кнопку **+**, чтобы приступить к созданию бессерверной функции.</span><span class="sxs-lookup"><span data-stu-id="33d53-119">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span> <span data-ttu-id="33d53-120">Когда появится страница быстрого запуска, щелкните **Пользовательская функция**, чтобы просмотреть список шаблонов функций.</span><span class="sxs-lookup"><span data-stu-id="33d53-120">If a quickstart page appears, click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="33d53-121">Найдите и выберите шаблон **BlobTrigger**.</span><span class="sxs-lookup"><span data-stu-id="33d53-121">Find the **BlobTrigger** template and select it.</span></span>

1. <span data-ttu-id="33d53-122">Введите указанные ниже значения, чтобы создать функцию, которая создает эскизы при отправке изображений.</span><span class="sxs-lookup"><span data-stu-id="33d53-122">Use these values to create a function that creates thumbnails as images are uploaded.</span></span>

    | <span data-ttu-id="33d53-123">Параметр</span><span class="sxs-lookup"><span data-stu-id="33d53-123">Setting</span></span>      |  <span data-ttu-id="33d53-124">Рекомендуемое значение</span><span class="sxs-lookup"><span data-stu-id="33d53-124">Suggested value</span></span>   | <span data-ttu-id="33d53-125">ОПИСАНИЕ</span><span class="sxs-lookup"><span data-stu-id="33d53-125">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="33d53-126">**Язык**</span><span class="sxs-lookup"><span data-stu-id="33d53-126">**Language**</span></span> | <span data-ttu-id="33d53-127">C# или JavaScript</span><span class="sxs-lookup"><span data-stu-id="33d53-127">C# or JavaScript</span></span> | <span data-ttu-id="33d53-128">Выберите предпочитаемый язык.</span><span class="sxs-lookup"><span data-stu-id="33d53-128">Choose your preferred language.</span></span> |
    | <span data-ttu-id="33d53-129">**Имя функции**</span><span class="sxs-lookup"><span data-stu-id="33d53-129">**Name your function**</span></span> | <span data-ttu-id="33d53-130">ResizeImage</span><span class="sxs-lookup"><span data-stu-id="33d53-130">ResizeImage</span></span> | <span data-ttu-id="33d53-131">Введите это имя именно так, как показано, чтобы приложение смогло обнаружить функцию.</span><span class="sxs-lookup"><span data-stu-id="33d53-131">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="33d53-132">**Путь**</span><span class="sxs-lookup"><span data-stu-id="33d53-132">**Path**</span></span> | <span data-ttu-id="33d53-133">images/{name}</span><span class="sxs-lookup"><span data-stu-id="33d53-133">images/{name}</span></span> | <span data-ttu-id="33d53-134">Функция выполняется при появлении файла в контейнере **images**.</span><span class="sxs-lookup"><span data-stu-id="33d53-134">Execute the function when a file appears in the **images** container.</span></span> |
    | <span data-ttu-id="33d53-135">**Сведения об учетной записи хранения**</span><span class="sxs-lookup"><span data-stu-id="33d53-135">**Storage account information**</span></span> | <span data-ttu-id="33d53-136">AZURE_STORAGE_CONNECTION_STRING</span><span class="sxs-lookup"><span data-stu-id="33d53-136">AZURE_STORAGE_CONNECTION_STRING</span></span> | <span data-ttu-id="33d53-137">Используйте переменную среды, созданную ранее вместе со строкой подключения.</span><span class="sxs-lookup"><span data-stu-id="33d53-137">Use the environment variable previously created with the connection string.</span></span> |

    ![Ввод параметров для новой функции](media/functions-first-serverless-web-app/3-new-function.png)

1. <span data-ttu-id="33d53-139">Щелкните **Создать**, чтобы создать функцию.</span><span class="sxs-lookup"><span data-stu-id="33d53-139">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="33d53-140">Когда функция будет создана, щелкните **Интегрировать**, чтобы просмотреть триггер, а также входные и выходные привязки функции.</span><span class="sxs-lookup"><span data-stu-id="33d53-140">When the function is created, click **Integrate** to view its trigger, input, and output bindings.</span></span>

1. <span data-ttu-id="33d53-141">Чтобы создать выходную привязку, щелкните **Новое выходное значение**.</span><span class="sxs-lookup"><span data-stu-id="33d53-141">Click **New output** to create a new output binding.</span></span>

    ![Выбор элемента "Новое выходное значение" на вкладке "Интегрировать"](media/functions-first-serverless-web-app/3-new-output.jpg)

1. <span data-ttu-id="33d53-143">Выберите **Хранилище BLOB-объектов Azure** и щелкните **Выбрать**.</span><span class="sxs-lookup"><span data-stu-id="33d53-143">Select **Azure Blob Storage** and click **Select**.</span></span> <span data-ttu-id="33d53-144">Возможно, потребуется прокрутить экран вниз, чтобы отобразилась кнопка **Выбрать**.</span><span class="sxs-lookup"><span data-stu-id="33d53-144">You may have to scroll down to see the **Select** button.</span></span>

    ![Выбор элемента "Хранилище BLOB-объектов Azure"](media/functions-first-serverless-web-app/3-storage-output.jpg)

1. <span data-ttu-id="33d53-146">Введите указанные ниже значения.</span><span class="sxs-lookup"><span data-stu-id="33d53-146">Enter the following values.</span></span>

    | <span data-ttu-id="33d53-147">Параметр</span><span class="sxs-lookup"><span data-stu-id="33d53-147">Setting</span></span>      |  <span data-ttu-id="33d53-148">Рекомендуемое значение</span><span class="sxs-lookup"><span data-stu-id="33d53-148">Suggested value</span></span>   | <span data-ttu-id="33d53-149">ОПИСАНИЕ</span><span class="sxs-lookup"><span data-stu-id="33d53-149">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="33d53-150">**Имя параметра большого двоичного объекта**</span><span class="sxs-lookup"><span data-stu-id="33d53-150">**Blob parameter name**</span></span> | <span data-ttu-id="33d53-151">thumbnail</span><span class="sxs-lookup"><span data-stu-id="33d53-151">thumbnail</span></span> | <span data-ttu-id="33d53-152">Функция использует параметр с таким именем для записи эскиза.</span><span class="sxs-lookup"><span data-stu-id="33d53-152">The function uses the parameter with this name to write the thumbnail.</span></span> |
    | <span data-ttu-id="33d53-153">**Использовать возвращаемое значение функции**</span><span class="sxs-lookup"><span data-stu-id="33d53-153">**Use function return value**</span></span> | <span data-ttu-id="33d53-154">Нет </span><span class="sxs-lookup"><span data-stu-id="33d53-154">No</span></span> |  |
    | <span data-ttu-id="33d53-155">**Путь**</span><span class="sxs-lookup"><span data-stu-id="33d53-155">**Path**</span></span> | <span data-ttu-id="33d53-156">thumbnails/{name}</span><span class="sxs-lookup"><span data-stu-id="33d53-156">thumbnails/{name}</span></span> | <span data-ttu-id="33d53-157">Эскизы записываются в контейнер с именем **thumbnails**.</span><span class="sxs-lookup"><span data-stu-id="33d53-157">The thumbnails are written to a container named **thumbnails**.</span></span> |
    | <span data-ttu-id="33d53-158">**Подключение к учетной записи хранения**</span><span class="sxs-lookup"><span data-stu-id="33d53-158">**Storage account connection**</span></span> | <span data-ttu-id="33d53-159">AZURE_STORAGE_CONNECTION_STRING</span><span class="sxs-lookup"><span data-stu-id="33d53-159">AZURE_STORAGE_CONNECTION_STRING</span></span> | <span data-ttu-id="33d53-160">Используйте переменную среды, созданную ранее вместе со строкой подключения.</span><span class="sxs-lookup"><span data-stu-id="33d53-160">Use the environment variable previously created with the connection string.</span></span> |

    ![Ввод параметров для выходной привязки BLOB-объекта](media/functions-first-serverless-web-app/3-blob-output.png)

1. <span data-ttu-id="33d53-162">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="33d53-162">**JavaScript**</span></span>

    1. <span data-ttu-id="33d53-163">(JavaScript) Щелкните **Расширенный редактор** в правом верхнем углу окна, чтобы открыть документ JSON с привязками.</span><span class="sxs-lookup"><span data-stu-id="33d53-163">(JavaScript) Click on **Advanced editor** in the top right corner of the window to reveal the JSON representing the bindings.</span></span>

    1. <span data-ttu-id="33d53-164">(JavaScript) В разделе привязки `blobTrigger` добавьте свойство с именем `dataType` и значением `binary`.</span><span class="sxs-lookup"><span data-stu-id="33d53-164">(JavaScript) In the `blobTrigger` binding, add a property named `dataType` with a value of `binary`.</span></span> <span data-ttu-id="33d53-165">Таким образом вы зададите конфигурацию для привязки, чтобы отправлять содержимое большого двоичного объекта в функцию в виде двоичных данных.</span><span class="sxs-lookup"><span data-stu-id="33d53-165">This configures the binding to pass the blob contents to the function as binary data.</span></span>

    ```json
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "images/{name}",
      "connection": "AZURE_STORAGE_CONNECTION_STRING",
      "dataType": "binary"
    }
    ```

1. <span data-ttu-id="33d53-166">Нажмите кнопку **Сохранить**, чтобы создать привязку.</span><span class="sxs-lookup"><span data-stu-id="33d53-166">Click **Save** to create the new binding.</span></span>

1. <span data-ttu-id="33d53-167">**C#**</span><span class="sxs-lookup"><span data-stu-id="33d53-167">**C#**</span></span>

    1. <span data-ttu-id="33d53-168">(C#) Щелкните имя функции **ResizeImage** в области навигации слева, чтобы открыть исходный код функции.</span><span class="sxs-lookup"><span data-stu-id="33d53-168">(C#) Select the **ResizeImage** function name on the left navigation to open the function's source code.</span></span>

    1. <span data-ttu-id="33d53-169">(C#) Для создания эскизов функции требуется пакет NuGet с именем **ImageResizer**.</span><span class="sxs-lookup"><span data-stu-id="33d53-169">(C#) The function requires a NuGet package called **ImageResizer** to generate the thumbnails.</span></span> <span data-ttu-id="33d53-170">Пакеты NuGet добавляются в функции C# с помощью файла **project.json**.</span><span class="sxs-lookup"><span data-stu-id="33d53-170">NuGet packages are added to C# functions using a **project.json** file.</span></span> <span data-ttu-id="33d53-171">Чтобы создать такой файл, щелкните **Просмотреть файлы** справа. Откроются файлы, из которых состоит функция.</span><span class="sxs-lookup"><span data-stu-id="33d53-171">To create the file, click **View Files** on the right to reveal the files that make up the function.</span></span>
    
    1. <span data-ttu-id="33d53-172">(C#) Чтобы добавить новый файл с именем **project.json**, нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="33d53-172">(C#) Click **Add** to add a new file named **project.json**.</span></span>
    
    1. <span data-ttu-id="33d53-173">(C#) Скопируйте содержимое из репозитория [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) в только что созданный файл.</span><span class="sxs-lookup"><span data-stu-id="33d53-173">(C#) Copy the contents of [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) into the newly created file.</span></span> <span data-ttu-id="33d53-174">Сохраните файл.</span><span class="sxs-lookup"><span data-stu-id="33d53-174">Save the file.</span></span> <span data-ttu-id="33d53-175">Пакеты автоматически восстанавливаются при обновлении файла.</span><span class="sxs-lookup"><span data-stu-id="33d53-175">Packages are automatically restored when the file is updated.</span></span>
    
        ![Файл project.json с указанным пакетом ImageResizer](media/functions-first-serverless-web-app/3-project-json.png)
    
    1. <span data-ttu-id="33d53-177">(C#) Выберите файл **run.csx** в разделе **Просмотреть файлы** и замените его содержимое содержимым из репозитория [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).</span><span class="sxs-lookup"><span data-stu-id="33d53-177">(C#) Select **run.csx** under **View Files** and replace its content with the content in [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).</span></span>

1. <span data-ttu-id="33d53-178">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="33d53-178">**JavaScript**</span></span> 

    1. <span data-ttu-id="33d53-179">(JavaScript) Чтобы изменить размер фотографии, этой функции требуется пакет `jimp` из npm.</span><span class="sxs-lookup"><span data-stu-id="33d53-179">(JavaScript) This function requires the `jimp` package from npm to resize the photo.</span></span> <span data-ttu-id="33d53-180">Чтобы установить пакет npm, щелкните имя приложения-функции в области навигации слева и выберите **Функции платформы**.</span><span class="sxs-lookup"><span data-stu-id="33d53-180">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="33d53-181">(JavaScript) Чтобы открыть окно консоли, щелкните **Консоль**.</span><span class="sxs-lookup"><span data-stu-id="33d53-181">(JavaScript) Click **Console** to reveal a console window.</span></span>

    1. <span data-ttu-id="33d53-182">(JavaScript) В консоли выполните команду `npm install jimp`.</span><span class="sxs-lookup"><span data-stu-id="33d53-182">(JavaScript) Run the command `npm install jimp` in the console.</span></span> <span data-ttu-id="33d53-183">Выполнение операции может занять одну-две минуты.</span><span class="sxs-lookup"><span data-stu-id="33d53-183">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="33d53-184">(JavaScript) Щелкните имя функции **ResizeImage** в области навигации слева, чтобы открыть окно с ее кодом. Замените содержимое файла **index.js** содержимым из репозитория [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).</span><span class="sxs-lookup"><span data-stu-id="33d53-184">(JavaScript) Click on the **ResizeImage** function name in the left navigation to reveal the function, replace all of **index.js** with the content of [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).</span></span>

1. <span data-ttu-id="33d53-185">Щелкните **Журналы** под окном кода, чтобы развернуть панель журналов.</span><span class="sxs-lookup"><span data-stu-id="33d53-185">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="33d53-186">Выберите команду **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="33d53-186">Click **Save**.</span></span> <span data-ttu-id="33d53-187">Просмотрите записи на панели журналов и проверьте, успешно ли сохранена функция и нет ли ошибок.</span><span class="sxs-lookup"><span data-stu-id="33d53-187">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="33d53-188">Тестирование бессерверной функции</span><span class="sxs-lookup"><span data-stu-id="33d53-188">Test the serverless function</span></span>

1. <span data-ttu-id="33d53-189">Откройте приложение в браузере.</span><span class="sxs-lookup"><span data-stu-id="33d53-189">Open the application in a browser.</span></span> <span data-ttu-id="33d53-190">Выберите файл изображения и отправьте его.</span><span class="sxs-lookup"><span data-stu-id="33d53-190">Select an image file and upload it.</span></span> <span data-ttu-id="33d53-191">Отправка завершится, но, так как мы еще не добавили возможность выводить изображения, в приложении не отобразится отправленная фотография.</span><span class="sxs-lookup"><span data-stu-id="33d53-191">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span>

1. <span data-ttu-id="33d53-192">В Cloud Shell проверьте, отправлено ли изображение в контейнер **images**.</span><span class="sxs-lookup"><span data-stu-id="33d53-192">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="33d53-193">Убедитесь, что эскиз создан в контейнере с именем **thumbnails**.</span><span class="sxs-lookup"><span data-stu-id="33d53-193">Confirm the thumbnail was created in a container named **thumbnails**.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c thumbnails -o table
    ```

1. <span data-ttu-id="33d53-194">Получите URL-адрес эскиза.</span><span class="sxs-lookup"><span data-stu-id="33d53-194">Obtain the thumbnail's URL.</span></span>

    ```azurecli
    az storage blob url --account-name <storage account name> -c thumbnails -n <filename> --output tsv
    ```

    <span data-ttu-id="33d53-195">Откройте URL-адрес в браузере и убедитесь, что эскиз создан правильно.</span><span class="sxs-lookup"><span data-stu-id="33d53-195">Open the URL in a browser and confirm the thumbnail was properly created.</span></span>

1. <span data-ttu-id="33d53-196">Прежде чем перейти к следующему руководству, удалите все файлы в контейнерах **images** и **thumbnails**.</span><span class="sxs-lookup"><span data-stu-id="33d53-196">Before moving on to the next tutorial, delete all files in the **images** and **thumbnails** containers.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```
    ```azurecli
    az storage blob delete-batch -s thumbnails --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="33d53-197">Сводка</span><span class="sxs-lookup"><span data-stu-id="33d53-197">Summary</span></span>

<span data-ttu-id="33d53-198">В этом модуле вы создали бессерверную функцию, которая создает эскиз при отправке изображения в контейнер хранилища BLOB-объектов.</span><span class="sxs-lookup"><span data-stu-id="33d53-198">In this module, you created a serverless function to create a thumbnail whenever an image is uploaded to a Blob storage container.</span></span> <span data-ttu-id="33d53-199">Далее вы узнаете, как использовать Azure Cosmos DB для хранения и вывода списка метаданных изображения.</span><span class="sxs-lookup"><span data-stu-id="33d53-199">Next, you learn how to use Azure Cosmos DB to store and list image metadata.</span></span>
