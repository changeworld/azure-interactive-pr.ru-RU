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
ms.openlocfilehash: 56cfb4c2893977086309660f4f6941fd0d648913
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079695"
---
<span data-ttu-id="3d55b-103">Приложение, которое вы создаете, представляет собой галерею фотографий.</span><span class="sxs-lookup"><span data-stu-id="3d55b-103">The application that you're building is a photo gallery.</span></span> <span data-ttu-id="3d55b-104">В нем используется клиентский код JavaScript, чтобы вызывать API-интерфейсы для отправки и вывода изображений.</span><span class="sxs-lookup"><span data-stu-id="3d55b-104">It uses client-side JavaScript to call APIs to upload and display images.</span></span> <span data-ttu-id="3d55b-105">В этом модуле вы создадите API с помощью бессерверной функции, которая генерирует временный URL-адрес для отправки изображения.</span><span class="sxs-lookup"><span data-stu-id="3d55b-105">In this module, you create an API using a serverless function that generates a time-limited URL to upload an image.</span></span> <span data-ttu-id="3d55b-106">Веб-приложение использует сгенерированный URL-адрес для отправки изображения в хранилище BLOB-объектов с помощью [REST API хранилища BLOB-объектов](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).</span><span class="sxs-lookup"><span data-stu-id="3d55b-106">The web application uses the generated URL to upload an image to Blob storage using the [Blob storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).</span></span>

## <a name="create-a-blob-storage-container-for-images"></a><span data-ttu-id="3d55b-107">Создание контейнера хранилища BLOB-объектов для изображений</span><span class="sxs-lookup"><span data-stu-id="3d55b-107">Create a blob storage container for images</span></span>

<span data-ttu-id="3d55b-108">Приложению требуется отдельный контейнер с хранилищем для отправки и размещения изображений.</span><span class="sxs-lookup"><span data-stu-id="3d55b-108">The application requires a separate storage container to upload and host images.</span></span>

1. <span data-ttu-id="3d55b-109">Убедитесь, что окно Cloud Shell (Bash) открыто.</span><span class="sxs-lookup"><span data-stu-id="3d55b-109">Ensure you are still signed in to the Cloud Shell (bash).</span></span> <span data-ttu-id="3d55b-110">Если вы уже вышли, выберите **Enter focus mode** (Перейти в режим фокусировки), чтобы открыть окно Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="3d55b-110">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span>

1.  <span data-ttu-id="3d55b-111">Создайте контейнер с именем **images** в своей учетной записи хранения с общим доступом ко всем большим двоичным объектам.</span><span class="sxs-lookup"><span data-stu-id="3d55b-111">Create a new container named **images** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n images --account-name <storage account name> --public-access blob
    ```

## <a name="create-an-azure-function-app"></a><span data-ttu-id="3d55b-112">Создание приложения-функции Azure</span><span class="sxs-lookup"><span data-stu-id="3d55b-112">Create an Azure Function app</span></span>

<span data-ttu-id="3d55b-113">Решение "Функции Azure" — это служба для выполнения бессерверных функций.</span><span class="sxs-lookup"><span data-stu-id="3d55b-113">Azure Functions is a service for running serverless functions.</span></span> <span data-ttu-id="3d55b-114">Бессерверные функции могут активироваться (вызываться) событиями, такими как HTTP-запрос или создание большого двоичного объекта в контейнере хранилища.</span><span class="sxs-lookup"><span data-stu-id="3d55b-114">A serverless function can be triggered (called) by events such as an HTTP request or when a blob is created in a storage container.</span></span>

<span data-ttu-id="3d55b-115">Приложение-функция Azure — это контейнер для одной или нескольких бессерверных функций.</span><span class="sxs-lookup"><span data-stu-id="3d55b-115">An Azure Function app is a container for one or more serverless functions.</span></span>

1. <span data-ttu-id="3d55b-116">Создайте приложение-функцию Azure с уникальным именем в группе ресурсов **first-serverless-app**, которую вы создали ранее.</span><span class="sxs-lookup"><span data-stu-id="3d55b-116">Create a new Azure Function app with a unique name in the resource group you created earlier named **first-serverless-app**.</span></span> <span data-ttu-id="3d55b-117">Для приложений-функций требуется учетная запись хранения. При работе с этим руководством используйте существующую учетную запись хранения.</span><span class="sxs-lookup"><span data-stu-id="3d55b-117">Function apps require a Storage account; in this tutorial, you use the existing storage account.</span></span>

    ```azurecli
    az functionapp create -n <function app name> -g first-serverless-app -s <storage account name> -c westcentralus
    ```


## <a name="create-an-http-triggered-serverless-function"></a><span data-ttu-id="3d55b-118">Создание бессерверной функции, активируемой HTTP-запросом</span><span class="sxs-lookup"><span data-stu-id="3d55b-118">Create an HTTP-triggered serverless function</span></span>

<span data-ttu-id="3d55b-119">Веб-приложение коллекции фотографий выполняет HTTP-запрос к бессерверной функции, чтобы создать временный URL-адрес для безопасной отправки изображений в хранилище BLOB-объектов.</span><span class="sxs-lookup"><span data-stu-id="3d55b-119">The photo gallery web application makes an HTTP request to serverless function to generate a time-limited URL to securely upload an image to Blob storage.</span></span> <span data-ttu-id="3d55b-120">Функция активируется HTTP-запросом и использует пакет SDK для службы хранилища Azure, чтобы создать и вернуть защищенный URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="3d55b-120">The function is triggered by an HTTP request and uses the Azure Storage SDK to generate and return the secure URL.</span></span>

1. <span data-ttu-id="3d55b-121">Когда приложение-функция будет создано, найдите его на портале Azure с помощью поля поиска и щелкните, чтобы открыть.</span><span class="sxs-lookup"><span data-stu-id="3d55b-121">After the Function app is created, search for it in the Azure Portal using the Search box and click to open it.</span></span>

    ![Открытие приложения-функции](media/functions-first-serverless-web-app/2-search-function-app.png)

1. <span data-ttu-id="3d55b-123">В окне приложения-функции наведите указатель мыши на элемент **Функции** в области навигации слева и нажмите кнопку **+**, чтобы приступить к созданию бессерверной функции.</span><span class="sxs-lookup"><span data-stu-id="3d55b-123">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span>

    ![Создание функции](media/functions-first-serverless-web-app/2-new-function.png)

1. <span data-ttu-id="3d55b-125">Щелкните **Пользовательская функция**, чтобы просмотреть список шаблонов функций.</span><span class="sxs-lookup"><span data-stu-id="3d55b-125">Click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="3d55b-126">Найдите шаблон **HttpTrigger** и выберите язык (C# или JavaScript).</span><span class="sxs-lookup"><span data-stu-id="3d55b-126">Find the **HttpTrigger** template and click the language to use (C# or JavaScript).</span></span>

1. <span data-ttu-id="3d55b-127">Задайте указанные ниже значения, чтобы создать функцию, которая генерирует URL-адрес для отправки BLOB-объектов.</span><span class="sxs-lookup"><span data-stu-id="3d55b-127">Use these values to create a function that generates a blob upload URL.</span></span>

    | <span data-ttu-id="3d55b-128">Параметр</span><span class="sxs-lookup"><span data-stu-id="3d55b-128">Setting</span></span>      |  <span data-ttu-id="3d55b-129">Рекомендуемое значение</span><span class="sxs-lookup"><span data-stu-id="3d55b-129">Suggested value</span></span>   | <span data-ttu-id="3d55b-130">ОПИСАНИЕ</span><span class="sxs-lookup"><span data-stu-id="3d55b-130">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="3d55b-131">**Язык**</span><span class="sxs-lookup"><span data-stu-id="3d55b-131">**Language**</span></span> | <span data-ttu-id="3d55b-132">C# или JavaScript</span><span class="sxs-lookup"><span data-stu-id="3d55b-132">C# or JavaScript</span></span> | <span data-ttu-id="3d55b-133">Выберите нужный язык.</span><span class="sxs-lookup"><span data-stu-id="3d55b-133">Select the language you want to use.</span></span> |
    | <span data-ttu-id="3d55b-134">**Имя функции**</span><span class="sxs-lookup"><span data-stu-id="3d55b-134">**Name your function**</span></span> | <span data-ttu-id="3d55b-135">GetUploadUrl</span><span class="sxs-lookup"><span data-stu-id="3d55b-135">GetUploadUrl</span></span> | <span data-ttu-id="3d55b-136">Введите это имя именно так, как показано, чтобы приложение смогло обнаружить функцию.</span><span class="sxs-lookup"><span data-stu-id="3d55b-136">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="3d55b-137">**Уровень авторизации**</span><span class="sxs-lookup"><span data-stu-id="3d55b-137">**Authorization level**</span></span> | <span data-ttu-id="3d55b-138">Анонимные</span><span class="sxs-lookup"><span data-stu-id="3d55b-138">Anonymous</span></span> | <span data-ttu-id="3d55b-139">Предоставьте общий доступ к функции.</span><span class="sxs-lookup"><span data-stu-id="3d55b-139">Allow the function to be accessed publicly.</span></span> |

    ![Ввод параметров для новой функции, активируемой HTTP-запросом](media/functions-first-serverless-web-app/2-new-function-httptrigger.png)

1. <span data-ttu-id="3d55b-141">Щелкните **Создать**, чтобы создать функцию.</span><span class="sxs-lookup"><span data-stu-id="3d55b-141">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="3d55b-142">**C#**</span><span class="sxs-lookup"><span data-stu-id="3d55b-142">**C#**</span></span> 

    1. <span data-ttu-id="3d55b-143">Когда отобразится исходный код функции, замените содержимое файла **run.csx** содержимым из репозитория [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).</span><span class="sxs-lookup"><span data-stu-id="3d55b-143">When the function's source code appears, replace all of **run.csx** with the content of [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).</span></span>

1. <span data-ttu-id="3d55b-144">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="3d55b-144">**JavaScript**</span></span> 

    1. <span data-ttu-id="3d55b-145">(JavaScript) Для этой функции требуется пакет `azure-storage` из npm, чтобы сгенерировать токен подписанного URL-адреса (SAS), необходимый для создания защищенного URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="3d55b-145">(JavaScript) This function requires the `azure-storage` package from npm to generate the shared access signature (SAS) token required to build the secure URL.</span></span> <span data-ttu-id="3d55b-146">Чтобы установить пакет npm, щелкните имя приложения-функции в области навигации слева и выберите **Функции платформы**.</span><span class="sxs-lookup"><span data-stu-id="3d55b-146">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="3d55b-147">(JavaScript) Чтобы открыть окно консоли, щелкните **Консоль**.</span><span class="sxs-lookup"><span data-stu-id="3d55b-147">(JavaScript) Click **Console** to reveal a console window.</span></span>

        ![Открытие окна консоли](media/functions-first-serverless-web-app/2-open-console.jpg)

    1. <span data-ttu-id="3d55b-149">(JavaScript) Убедитесь, что в качестве текущего каталога используется **d:\home\site\wwwroot**, выполнив команду `cd d:\home\site\wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="3d55b-149">(JavaScript) Ensure the current directory is **d:\home\site\wwwroot** by running the command `cd d:\home\site\wwwroot`.</span></span>

    1. <span data-ttu-id="3d55b-150">(JavaScript) Выполните команду `npm init -y`, чтобы создать пустой файл **package.json**.</span><span class="sxs-lookup"><span data-stu-id="3d55b-150">(JavaScript) Run the command `npm init -y` to create an empty **package.json** file.</span></span>

    1. <span data-ttu-id="3d55b-151">(JavaScript) В консоли выполните команду `npm install --save azure-storage`, чтобы установить пакет и сохранить его в файле **package.json**.</span><span class="sxs-lookup"><span data-stu-id="3d55b-151">(JavaScript) Run the command `npm install --save azure-storage` in the console to install the package and save it in **package.json**.</span></span> <span data-ttu-id="3d55b-152">Выполнение операции может занять одну-две минуты.</span><span class="sxs-lookup"><span data-stu-id="3d55b-152">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="3d55b-153">(JavaScript) Щелкните имя функции (**GetUploadUrl**) в области навигации слева, чтобы открыть окно с ее кодом. Замените содержимое файла **index.js** содержимым из репозитория [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).</span><span class="sxs-lookup"><span data-stu-id="3d55b-153">(JavaScript) Click on the function name (**GetUploadUrl**) in the left navigation to reveal the function, replace all of **index.js** with the content of [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).</span></span>
    
        ![Файл index.js после обновления](media/functions-first-serverless-web-app/2-paste-js.jpg)

1. <span data-ttu-id="3d55b-155">Щелкните **Журналы** под окном кода, чтобы развернуть панель журналов.</span><span class="sxs-lookup"><span data-stu-id="3d55b-155">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="3d55b-156">Выберите команду **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="3d55b-156">Click **Save**.</span></span> <span data-ttu-id="3d55b-157">Просмотрите записи на панели журналов и проверьте, успешно ли скомпилирована функция.</span><span class="sxs-lookup"><span data-stu-id="3d55b-157">Check the logs panel to ensure the function is successfully compiled.</span></span>

<span data-ttu-id="3d55b-158">Функция создает так называемый подписанный URL-адрес (SAS), который используется для отправки файла в хранилище BLOB-объектов.</span><span class="sxs-lookup"><span data-stu-id="3d55b-158">The function generates what is called a shared access signature (SAS) URL that is used to upload a file to Blob storage.</span></span> <span data-ttu-id="3d55b-159">URL-адрес SAS действителен в течение короткого периода времени и позволяет отправить только один файл.</span><span class="sxs-lookup"><span data-stu-id="3d55b-159">The SAS URL is valid for a short period of time and only allows a single file to be uploaded.</span></span> <span data-ttu-id="3d55b-160">Дополнительные сведения об [использовании подписанных URL-адресов](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) см. в документации по хранилищу BLOB-объектов.</span><span class="sxs-lookup"><span data-stu-id="3d55b-160">Consult the Blob storage documentation for more information on [using shared access signatures](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).</span></span>


## <a name="add-an-environment-variable-for-the-storage-connection-string"></a><span data-ttu-id="3d55b-161">Добавление переменной среды для строки подключения к хранилищу</span><span class="sxs-lookup"><span data-stu-id="3d55b-161">Add an environment variable for the storage connection string</span></span>

<span data-ttu-id="3d55b-162">Чтобы только что созданная функция могла сгенерировать URL-адрес SAS, ей требуется строка подключения для учетной записи хранения.</span><span class="sxs-lookup"><span data-stu-id="3d55b-162">The function you just created requires a connection string for the Storage account so that it can generate the SAS URL.</span></span> <span data-ttu-id="3d55b-163">Чтобы не указывать строку подключения прямо в тексте функции, эту строку можно сохранить как параметр приложения.</span><span class="sxs-lookup"><span data-stu-id="3d55b-163">Instead of hardcoding the connection string in the function's body, it can be stored as an application setting.</span></span> <span data-ttu-id="3d55b-164">Параметры приложения доступны в виде переменных среды для всех функций в приложении-функции.</span><span class="sxs-lookup"><span data-stu-id="3d55b-164">Application settings are accessible as environment variables by all functions in the Function app.</span></span>

1. <span data-ttu-id="3d55b-165">В Cloud Shell запросите строку подключения для учетной записи хранения и сохраните ее в переменной bash с именем **STORAGE_CONNECTION_STRING**.</span><span class="sxs-lookup"><span data-stu-id="3d55b-165">In the Cloud Shell, query the Storage account connection string and save it to a bash variable named **STORAGE_CONNECTION_STRING**.</span></span>

    ```azurecli
    export STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n <storage account name> -g first-serverless-app --query "connectionString" --output tsv)
    ```

    <span data-ttu-id="3d55b-166">Убедитесь, что эта переменная успешно задана.</span><span class="sxs-lookup"><span data-stu-id="3d55b-166">Confirm the variable is set successfully.</span></span>

    ```azurecli
    echo $STORAGE_CONNECTION_STRING
    ```

1. <span data-ttu-id="3d55b-167">Создайте параметр приложения с именем **AZURE_STORAGE_CONNECTION_STRING**, используя значение, сохраненное на предыдущем шаге.</span><span class="sxs-lookup"><span data-stu-id="3d55b-167">Create a new application setting named **AZURE_STORAGE_CONNECTION_STRING** using the value saved from the previous step.</span></span>

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings AZURE_STORAGE_CONNECTION_STRING=$STORAGE_CONNECTION_STRING -o table
    ```

    <span data-ttu-id="3d55b-168">Убедитесь, что в выходных данных команды содержится новый параметр приложения с правильным значением.</span><span class="sxs-lookup"><span data-stu-id="3d55b-168">Confirm that the command's output contains the new application setting with the correct value.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="3d55b-169">Тестирование бессерверной функции</span><span class="sxs-lookup"><span data-stu-id="3d55b-169">Test the serverless function</span></span>

<span data-ttu-id="3d55b-170">На портале Azure предоставлены встроенные средства, которые позволяют не только создавать и редактировать функции, но и тестировать их.</span><span class="sxs-lookup"><span data-stu-id="3d55b-170">In addition to creating and editing functions, the Azure portal also provides an built-in tool for testing functions.</span></span>

1. <span data-ttu-id="3d55b-171">Чтобы проверить бессерверную функцию, которая активируется HTTP-запросом, щелкните вкладку **Тест** в правой части окна кода для развертывания области тестирования.</span><span class="sxs-lookup"><span data-stu-id="3d55b-171">To test the HTTP serverless function, click on the **Test** tab on the right of the code window to expand the test panel.</span></span>

1. <span data-ttu-id="3d55b-172">Измените **метод HTTP** на **GET**.</span><span class="sxs-lookup"><span data-stu-id="3d55b-172">Change the **Http method** to **GET**.</span></span>

1. <span data-ttu-id="3d55b-173">В разделе **Запрос** щелкните *Добавить параметр*\*, чтобы добавить следующий параметр:</span><span class="sxs-lookup"><span data-stu-id="3d55b-173">Under **Query**, click *Add parameter*\* and add the following parameter:</span></span>

    | <span data-ttu-id="3d55b-174">name</span><span class="sxs-lookup"><span data-stu-id="3d55b-174">name</span></span>      |  <span data-ttu-id="3d55b-175">value</span><span class="sxs-lookup"><span data-stu-id="3d55b-175">value</span></span>   | 
    | --- | --- |
    | <span data-ttu-id="3d55b-176">filename</span><span class="sxs-lookup"><span data-stu-id="3d55b-176">filename</span></span> | <span data-ttu-id="3d55b-177">image1.jpg</span><span class="sxs-lookup"><span data-stu-id="3d55b-177">image1.jpg</span></span> |

1. <span data-ttu-id="3d55b-178">В области тестирования нажмите кнопку **Запуск**, чтобы отправить HTTP-запрос к функции.</span><span class="sxs-lookup"><span data-stu-id="3d55b-178">Click **Run** in the test panel to send an HTTP request to the function.</span></span>

1. <span data-ttu-id="3d55b-179">Функция возвращает выходные данные с URL-адресом для отправки.</span><span class="sxs-lookup"><span data-stu-id="3d55b-179">The function returns an upload URL in the output.</span></span> <span data-ttu-id="3d55b-180">Сведения о выполнении функции отображаются в области журналов.</span><span class="sxs-lookup"><span data-stu-id="3d55b-180">The function execution appears in the logs panel.</span></span>

    ![Область журналов со сведениями о том, что функция выполнена успешно](media/functions-first-serverless-web-app/2-test-function.png)


## <a name="configure-cors-in-the-function-app"></a><span data-ttu-id="3d55b-182">Настройка CORS в приложении-функции</span><span class="sxs-lookup"><span data-stu-id="3d55b-182">Configure CORS in the function app</span></span>

<span data-ttu-id="3d55b-183">Так как внешний интерфейс приложения размещен в хранилище BLOB-объектов, для него используется не такое доменное имя, как для приложения-функции Azure.</span><span class="sxs-lookup"><span data-stu-id="3d55b-183">Because the app's frontend is hosted in Blob storage, it has a different domain name than the Azure Function app.</span></span> <span data-ttu-id="3d55b-184">Чтобы из клиентского кода JavaScript успешно вызывалась функция, которую вы только что создали, нужно настроить CORS (предоставление общего доступа к ресурсам независимо от источника) для приложения-функции.</span><span class="sxs-lookup"><span data-stu-id="3d55b-184">For the client-side JavaScript to successfully call the function you just created, the function app needs to be configured for cross-origin resource sharing (CORS).</span></span>

1. <span data-ttu-id="3d55b-185">На панели навигации слева в окне приложения-функции щелкните имя приложения-функции.</span><span class="sxs-lookup"><span data-stu-id="3d55b-185">In the left navigation bar of the function app window, click on the name of your function app.</span></span>

1. <span data-ttu-id="3d55b-186">Выберите **Функции платформы**, чтобы просмотреть список дополнительных функций.</span><span class="sxs-lookup"><span data-stu-id="3d55b-186">Click on **Platform features** to view a list of advanced features.</span></span>

1. <span data-ttu-id="3d55b-187">В разделе **API** щелкните **CORS**.</span><span class="sxs-lookup"><span data-stu-id="3d55b-187">Under **API**, click **CORS**.</span></span>

    ![Выбор варианта CORS](media/functions-first-serverless-web-app/2-open-cors.jpg)

1. <span data-ttu-id="3d55b-189">Добавьте допустимый источник для URL-адреса приложения из предыдущего модуля без символа **/** в конце (например, `https://firstserverlessweb.z4.web.core.windows.net`).</span><span class="sxs-lookup"><span data-stu-id="3d55b-189">Add an allow origin for the application URL from the previous module, omitting the trailing **/** (for example: `https://firstserverlessweb.z4.web.core.windows.net`).</span></span>

    ![Снимок экрана с параметрами CORS, на котором показано добавление URL-адреса бессерверного веб-приложения](media/functions-first-serverless-web-app/2-add-cors.png)

1. <span data-ttu-id="3d55b-191">Выберите команду **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="3d55b-191">Click **Save**.</span></span>

1. <span data-ttu-id="3d55b-192">C#</span><span class="sxs-lookup"><span data-stu-id="3d55b-192">C#</span></span>

   1. <span data-ttu-id="3d55b-193">(C#) Вернитесь к функции `GetUploadUrl` и выберите вкладку **Интегрировать**.</span><span class="sxs-lookup"><span data-stu-id="3d55b-193">(C#) Navigate back to the `GetUploadUrl` function, and then select the **Integrate** tab.</span></span>

   1. <span data-ttu-id="3d55b-194">(C#) В разделе "Выбранные методы HTTP" щелкните **OPTIONS**.</span><span class="sxs-lookup"><span data-stu-id="3d55b-194">(C#) Under Selected HTTP methods, select **OPTIONS**.</span></span>

      <span data-ttu-id="3d55b-195">Для методов **GET**, **POST** и **OPTIONS** должны быть установлены флажки.</span><span class="sxs-lookup"><span data-stu-id="3d55b-195">**GET**, **POST**, and **OPTIONS** should all be selected.</span></span> <span data-ttu-id="3d55b-196">Для CORS используется метод OPTIONS, который не выбран по умолчанию для функций C#.</span><span class="sxs-lookup"><span data-stu-id="3d55b-196">CORS uses the OPTIONS method, which is not selected by default for C# functions.</span></span>  

   1. <span data-ttu-id="3d55b-197">(C#) Щелкните **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="3d55b-197">(C#) Click **Save**.</span></span>

1. <span data-ttu-id="3d55b-198">Оставаясь на портале, перейдите к приложению-функции, выберите вкладку **Обзор** и щелкните **Перезапустить**, чтобы изменения для CORS вступили в силу.</span><span class="sxs-lookup"><span data-stu-id="3d55b-198">Still in the portal, navigate to the function app, select the **Overview** tab, and then click **Restart** to make sure that the changes for CORS take effect.</span></span>

## <a name="configure-cors-in-the-storage-account"></a><span data-ttu-id="3d55b-199">Настройка CORS в учетной записи хранения</span><span class="sxs-lookup"><span data-stu-id="3d55b-199">Configure CORS in the Storage account</span></span>

<span data-ttu-id="3d55b-200">Так как для отправки файлов приложение выполняет клиентские вызовы JavaScript к хранилищу BLOB-объектов, нужно также настроить учетную запись хранения для CORS.</span><span class="sxs-lookup"><span data-stu-id="3d55b-200">Because the app also makes client-side JavaScript calls to Blob Storage to upload files, you also have to configure the Storage account for CORS.</span></span>

1. <span data-ttu-id="3d55b-201">Чтобы разрешить отправку файлов в учетную запись хранения из всех источников, выполните приведенную ниже команду.</span><span class="sxs-lookup"><span data-stu-id="3d55b-201">Run the following command to allow all origins to upload files to the Storage account.</span></span>

    ```azurecli
    az storage cors add --methods OPTIONS PUT --origins '*' --exposed-headers '*' --allowed-headers '*' --services b --account-name <storage account name>
    ```


## <a name="modify-the-web-app-to-upload-images"></a><span data-ttu-id="3d55b-202">Изменение веб-приложения для отправки изображений</span><span class="sxs-lookup"><span data-stu-id="3d55b-202">Modify the web app to upload images</span></span>

<span data-ttu-id="3d55b-203">Веб-приложение извлекает параметры из файла с именем **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="3d55b-203">The web app retrieves settings from a file named **settings.js**.</span></span> <span data-ttu-id="3d55b-204">На следующих этапах вы с помощью Cloud Shell создадите файл, а затем укажете `window.apiBaseUrl` для URL-адреса приложения-функции и `window.blobBaseUrl` для URL-адреса конечной точки хранилища BLOB-объектов Azure.</span><span class="sxs-lookup"><span data-stu-id="3d55b-204">In the following steps, you create the file using Cloud Shell, then set `window.apiBaseUrl` to the URL of the Function app and `window.blobBaseUrl` to the URL of the Azure Blob Storage endpoint.</span></span>

1. <span data-ttu-id="3d55b-205">В Cloud Shell убедитесь, что в качестве текущего каталога используется папка **www/dist**.</span><span class="sxs-lookup"><span data-stu-id="3d55b-205">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="3d55b-206">Запросите URL-адрес приложения-функции и сохраните его в переменной bash с именем **FUNCTION_APP_URL**.</span><span class="sxs-lookup"><span data-stu-id="3d55b-206">Query the function app's URL and store it in a bash variable named **FUNCTION_APP_URL**.</span></span>

    ```azurecli
    export FUNCTION_APP_URL="https://"$(az functionapp show -n <function app name> -g first-serverless-app --query "defaultHostName" --output tsv)
    ```

    <span data-ttu-id="3d55b-207">Убедитесь, что переменная указана правильно.</span><span class="sxs-lookup"><span data-stu-id="3d55b-207">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $FUNCTION_APP_URL
    ```

1. <span data-ttu-id="3d55b-208">Чтобы задать базовый URI для вызовов API к своему приложению-функции, создайте файл **settings.js** и добавьте URL-адрес приложения-функции, как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="3d55b-208">To set the base URI of API calls to your function app, create **settings.js** and add the function app URL like the following.</span></span>

    `window.apiBaseUrl = 'https://fnapp@lab.GlobalLabInstanceId.azurewebsites.net'`

    <span data-ttu-id="3d55b-209">Чтобы внести изменения, можно выполнить указанную далее команду или использовать редактор командной строки, например VIM.</span><span class="sxs-lookup"><span data-stu-id="3d55b-209">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.apiBaseUrl = '$FUNCTION_APP_URL'" > settings.js
    ```

    <span data-ttu-id="3d55b-210">Убедитесь, что файл успешно записан.</span><span class="sxs-lookup"><span data-stu-id="3d55b-210">Confirm the file was successfully written.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="3d55b-211">Запросите базовый URL-адрес хранилища BLOB-объектов и сохраните его в переменной bash с именем **BLOB_BASE_URL**.</span><span class="sxs-lookup"><span data-stu-id="3d55b-211">Query the Blob Storage base URL and store it in a bash variable named **BLOB_BASE_URL**.</span></span>

    ```azurecli
    export BLOB_BASE_URL=$(az storage account show -n <storage account name> -g first-serverless-app --query primaryEndpoints.blob -o tsv | sed 's/\/$//')
    ```

    <span data-ttu-id="3d55b-212">Убедитесь, что переменная указана правильно.</span><span class="sxs-lookup"><span data-stu-id="3d55b-212">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $BLOB_BASE_URL
    ```

1. <span data-ttu-id="3d55b-213">Чтобы задать базовый URI для вызовов API к своему приложению-функции, добавьте URL-адрес хранилища в файл **settings.js**, как в приведенном ниже примере строки кода.</span><span class="sxs-lookup"><span data-stu-id="3d55b-213">To set the base URI of API calls to your function app, append the storage URL like the following line of code to **settings.js**.</span></span>

    `window.blobBaseUrl = 'https://mystorage.blob.core.windows.net'`

    <span data-ttu-id="3d55b-214">Чтобы внести изменения, можно выполнить указанную далее команду или использовать редактор командной строки, например VIM.</span><span class="sxs-lookup"><span data-stu-id="3d55b-214">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.blobBaseUrl = '$BLOB_BASE_URL'" >> settings.js
    ```

    <span data-ttu-id="3d55b-215">Убедитесь, что файл успешно записан и теперь содержит две строки.</span><span class="sxs-lookup"><span data-stu-id="3d55b-215">Confirm the file was successfully written and it now contains 2 lines.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="3d55b-216">Отправьте файл в хранилище BLOB-объектов.</span><span class="sxs-lookup"><span data-stu-id="3d55b-216">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-web-application"></a><span data-ttu-id="3d55b-217">Тестирование веб-приложения</span><span class="sxs-lookup"><span data-stu-id="3d55b-217">Test the web application</span></span>

<span data-ttu-id="3d55b-218">На этом этапе приложение коллекции может отправлять изображения в хранилище BLOB-объектов, но еще не может выводить изображения.</span><span class="sxs-lookup"><span data-stu-id="3d55b-218">At this point, the gallery application is able to upload an image to Blob storage, but it can't display images yet.</span></span> <span data-ttu-id="3d55b-219">Приложение попытается вызвать еще не существующую функцию `GetImages`, которую вы создадите в следующем модуле.</span><span class="sxs-lookup"><span data-stu-id="3d55b-219">It will try to call a `GetImages` function that doesn't exist yet because you create it in a later module.</span></span> <span data-ttu-id="3d55b-220">Этот вызов завершится ошибкой и веб-страница перестанет отвечать на запросы на этапе анализа, но выбранное изображение успешно отправится.</span><span class="sxs-lookup"><span data-stu-id="3d55b-220">That call will fail, and the web page will appear to be stuck on "Analyzing...", but the image you select will be successfully uploaded.</span></span>

<span data-ttu-id="3d55b-221">Убедитесь в том, что изображение успешно отправлено, проверив содержимое контейнера **images** на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="3d55b-221">You can verify an image is successfully uploaded by checking the contents of the **images** container in the Azure portal.</span></span>

1. <span data-ttu-id="3d55b-222">В окне браузера перейдите к приложению.</span><span class="sxs-lookup"><span data-stu-id="3d55b-222">In a browser window, browse to the application.</span></span> <span data-ttu-id="3d55b-223">Выберите файл изображения и отправьте его.</span><span class="sxs-lookup"><span data-stu-id="3d55b-223">Select an image file and upload it.</span></span> <span data-ttu-id="3d55b-224">Отправка завершится, но, так как мы еще не добавили возможность выводить изображения, в приложении не отобразится отправленная фотография</span><span class="sxs-lookup"><span data-stu-id="3d55b-224">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span> <span data-ttu-id="3d55b-225">(веб-страница перестанет отвечать на запросы на этапе анализа изображения; вы исправите это позже).</span><span class="sxs-lookup"><span data-stu-id="3d55b-225">(The web page appears to be stuck on "Analyzing image..."; you'll fix that later.)</span></span>

1. <span data-ttu-id="3d55b-226">В Cloud Shell проверьте, отправлено ли изображение в контейнер **images**.</span><span class="sxs-lookup"><span data-stu-id="3d55b-226">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="3d55b-227">Прежде чем перейти к следующему руководству, удалите все файлы в контейнере **images**.</span><span class="sxs-lookup"><span data-stu-id="3d55b-227">Before moving on to the next tutorial, delete all files in the **images** container.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="3d55b-228">Сводка</span><span class="sxs-lookup"><span data-stu-id="3d55b-228">Summary</span></span>

<span data-ttu-id="3d55b-229">В этом модуле вы создали приложение-функцию Azure и узнали, как с помощью бессерверных функций разрешить веб-приложению отправлять изображения в хранилище BLOB-объектов.</span><span class="sxs-lookup"><span data-stu-id="3d55b-229">In this module, you created an Azure Function app and learned how to use a serverless function to allow a web application to upload images to Blob storage.</span></span> <span data-ttu-id="3d55b-230">Далее вы научитесь создавать эскизы для отправленных изображений с помощью бессерверной функции, активируемой большим двоичным объектом.</span><span class="sxs-lookup"><span data-stu-id="3d55b-230">Next, you learn how to create thumbnails for the uploaded images using a Blob triggered serverless function.</span></span>
