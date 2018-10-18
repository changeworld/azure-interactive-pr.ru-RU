---
title: включение файла
description: включение файла
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 10/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3779c2e130afa7ee8d5879f30a924e258b7a41e9
ms.sourcegitcommit: fdb43556b8dcf67cb39c18e532b5fab7ac53eaee
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49315982"
---
<span data-ttu-id="40838-103">Приложение, которое вы создаете, представляет собой галерею фотографий.</span><span class="sxs-lookup"><span data-stu-id="40838-103">The application that you're building is a photo gallery.</span></span> <span data-ttu-id="40838-104">В нем используется клиентский код JavaScript, чтобы вызывать API-интерфейсы для отправки и вывода изображений.</span><span class="sxs-lookup"><span data-stu-id="40838-104">It uses client-side JavaScript to call APIs to upload and display images.</span></span> <span data-ttu-id="40838-105">В этом модуле вы создадите API с помощью бессерверной функции, которая генерирует временный URL-адрес для отправки изображения.</span><span class="sxs-lookup"><span data-stu-id="40838-105">In this module, you create an API using a serverless function that generates a time-limited URL to upload an image.</span></span> <span data-ttu-id="40838-106">Веб-приложение использует сгенерированный URL-адрес для отправки изображения в хранилище BLOB-объектов с помощью [REST API хранилища BLOB-объектов](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).</span><span class="sxs-lookup"><span data-stu-id="40838-106">The web application uses the generated URL to upload an image to Blob storage using the [Blob storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).</span></span>

## <a name="create-a-blob-storage-container-for-images"></a><span data-ttu-id="40838-107">Создание контейнера хранилища BLOB-объектов для изображений</span><span class="sxs-lookup"><span data-stu-id="40838-107">Create a blob storage container for images</span></span>

<span data-ttu-id="40838-108">Приложению требуется отдельный контейнер с хранилищем для отправки и размещения изображений.</span><span class="sxs-lookup"><span data-stu-id="40838-108">The application requires a separate storage container to upload and host images.</span></span>

1. <span data-ttu-id="40838-109">Убедитесь, что окно Cloud Shell (Bash) открыто.</span><span class="sxs-lookup"><span data-stu-id="40838-109">Ensure you are still signed in to the Cloud Shell (bash).</span></span> <span data-ttu-id="40838-110">Если вы уже вышли, выберите **Enter focus mode** (Перейти в режим фокусировки), чтобы открыть окно Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="40838-110">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span>

1.  <span data-ttu-id="40838-111">Создайте контейнер с именем **images** в своей учетной записи хранения с общим доступом ко всем большим двоичным объектам.</span><span class="sxs-lookup"><span data-stu-id="40838-111">Create a new container named **images** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n images --account-name <storage account name> --public-access blob
    ```

## <a name="create-an-azure-function-app"></a><span data-ttu-id="40838-112">Создание приложения-функции Azure</span><span class="sxs-lookup"><span data-stu-id="40838-112">Create an Azure Function app</span></span>

<span data-ttu-id="40838-113">Решение "Функции Azure" — это служба для выполнения бессерверных функций.</span><span class="sxs-lookup"><span data-stu-id="40838-113">Azure Functions is a service for running serverless functions.</span></span> <span data-ttu-id="40838-114">Бессерверные функции могут активироваться (вызываться) событиями, такими как HTTP-запрос или создание большого двоичного объекта в контейнере хранилища.</span><span class="sxs-lookup"><span data-stu-id="40838-114">A serverless function can be triggered (called) by events such as an HTTP request or when a blob is created in a storage container.</span></span>

<span data-ttu-id="40838-115">Приложение-функция Azure — это контейнер для одной или нескольких бессерверных функций.</span><span class="sxs-lookup"><span data-stu-id="40838-115">An Azure Function app is a container for one or more serverless functions.</span></span>

<span data-ttu-id="40838-116">Создайте приложение-функцию Azure с уникальным именем в группе ресурсов **first-serverless-app**, которую вы создали ранее.</span><span class="sxs-lookup"><span data-stu-id="40838-116">Create a new Azure Function app with a unique name in the resource group you created earlier named **first-serverless-app**.</span></span> <span data-ttu-id="40838-117">Для приложений-функций требуется учетная запись хранения. При работе с этим руководством используйте существующую учетную запись хранения.</span><span class="sxs-lookup"><span data-stu-id="40838-117">Function apps require a Storage account; in this tutorial, you use the existing storage account.</span></span>

```azurecli
az functionapp create -n <function app name> -g first-serverless-app -s <storage account name> -c westcentralus
```

## <a name="configure-the-function-app"></a><span data-ttu-id="40838-118">Настройка приложения-функции</span><span class="sxs-lookup"><span data-stu-id="40838-118">Configure the function app</span></span>

<span data-ttu-id="40838-119">Для приложения-функции, используемого в этом руководстве, требуется среда выполнения Функций версии 1.x.</span><span class="sxs-lookup"><span data-stu-id="40838-119">The function app in this tutorial requires version 1.x of the Functions runtime.</span></span> <span data-ttu-id="40838-120">Задайте значение `~1` для параметра `FUNCTIONS_EXTENSION_VERSION`, чтобы закрепить приложение-функцию в последней версии 1.x.</span><span class="sxs-lookup"><span data-stu-id="40838-120">Setting the `FUNCTIONS_EXTENSION_VERSION` application setting to `~1` pins the function app to the latest 1.x version.</span></span> <span data-ttu-id="40838-121">Задайте параметры приложения с помощью команды [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set).</span><span class="sxs-lookup"><span data-stu-id="40838-121">Set application settings with the [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set) command.</span></span>

<span data-ttu-id="40838-122">В следующей команде Azure CLI <app_name> — это имя приложения-функции.</span><span class="sxs-lookup"><span data-stu-id="40838-122">In the following Azure CLI command, \`<app_name> is the name of your function app.</span></span>

```azurecli
az functionapp config appsettings set --name <function app name> --g first-serverless-app --settings FUNCTIONS_EXTENSION_VERSION=~1
```

## <a name="create-an-http-triggered-serverless-function"></a><span data-ttu-id="40838-123">Создание бессерверной функции, активируемой HTTP-запросом</span><span class="sxs-lookup"><span data-stu-id="40838-123">Create an HTTP-triggered serverless function</span></span>

<span data-ttu-id="40838-124">Веб-приложение коллекции фотографий выполняет HTTP-запрос к бессерверной функции, чтобы создать временный URL-адрес для безопасной отправки изображений в хранилище BLOB-объектов.</span><span class="sxs-lookup"><span data-stu-id="40838-124">The photo gallery web application makes an HTTP request to serverless function to generate a time-limited URL to securely upload an image to Blob storage.</span></span> <span data-ttu-id="40838-125">Функция активируется HTTP-запросом и использует пакет SDK для службы хранилища Azure, чтобы создать и вернуть защищенный URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="40838-125">The function is triggered by an HTTP request and uses the Azure Storage SDK to generate and return the secure URL.</span></span>

1. <span data-ttu-id="40838-126">Когда приложение-функция будет создано, найдите его на портале Azure с помощью поля поиска и щелкните, чтобы открыть.</span><span class="sxs-lookup"><span data-stu-id="40838-126">After the Function app is created, search for it in the Azure Portal using the Search box and click to open it.</span></span>

    ![Открытие приложения-функции](media/functions-first-serverless-web-app/2-search-function-app.png)

1. <span data-ttu-id="40838-128">В окне приложения-функции наведите указатель мыши на элемент **Функции** в области навигации слева и нажмите кнопку **+**, чтобы приступить к созданию бессерверной функции.</span><span class="sxs-lookup"><span data-stu-id="40838-128">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span>

    ![Создание функции](media/functions-first-serverless-web-app/2-new-function.png)

1. <span data-ttu-id="40838-130">Щелкните **Пользовательская функция**, чтобы просмотреть список шаблонов функций.</span><span class="sxs-lookup"><span data-stu-id="40838-130">Click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="40838-131">Найдите шаблон **HttpTrigger** и выберите язык (C# или JavaScript).</span><span class="sxs-lookup"><span data-stu-id="40838-131">Find the **HttpTrigger** template and click the language to use (C# or JavaScript).</span></span>

1. <span data-ttu-id="40838-132">Задайте указанные ниже значения, чтобы создать функцию, которая генерирует URL-адрес для отправки BLOB-объектов.</span><span class="sxs-lookup"><span data-stu-id="40838-132">Use these values to create a function that generates a blob upload URL.</span></span>

    | <span data-ttu-id="40838-133">Параметр</span><span class="sxs-lookup"><span data-stu-id="40838-133">Setting</span></span>      |  <span data-ttu-id="40838-134">Рекомендуемое значение</span><span class="sxs-lookup"><span data-stu-id="40838-134">Suggested value</span></span>   | <span data-ttu-id="40838-135">ОПИСАНИЕ</span><span class="sxs-lookup"><span data-stu-id="40838-135">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="40838-136">**Язык**</span><span class="sxs-lookup"><span data-stu-id="40838-136">**Language**</span></span> | <span data-ttu-id="40838-137">C# или JavaScript</span><span class="sxs-lookup"><span data-stu-id="40838-137">C# or JavaScript</span></span> | <span data-ttu-id="40838-138">Выберите нужный язык.</span><span class="sxs-lookup"><span data-stu-id="40838-138">Select the language you want to use.</span></span> |
    | <span data-ttu-id="40838-139">**Имя функции**</span><span class="sxs-lookup"><span data-stu-id="40838-139">**Name your function**</span></span> | <span data-ttu-id="40838-140">GetUploadUrl</span><span class="sxs-lookup"><span data-stu-id="40838-140">GetUploadUrl</span></span> | <span data-ttu-id="40838-141">Введите это имя именно так, как показано, чтобы приложение смогло обнаружить функцию.</span><span class="sxs-lookup"><span data-stu-id="40838-141">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="40838-142">**Уровень авторизации**</span><span class="sxs-lookup"><span data-stu-id="40838-142">**Authorization level**</span></span> | <span data-ttu-id="40838-143">Анонимные</span><span class="sxs-lookup"><span data-stu-id="40838-143">Anonymous</span></span> | <span data-ttu-id="40838-144">Предоставьте общий доступ к функции.</span><span class="sxs-lookup"><span data-stu-id="40838-144">Allow the function to be accessed publicly.</span></span> |

    ![Ввод параметров для новой функции, активируемой HTTP-запросом](media/functions-first-serverless-web-app/2-new-function-httptrigger.png)

1. <span data-ttu-id="40838-146">Щелкните **Создать**, чтобы создать функцию.</span><span class="sxs-lookup"><span data-stu-id="40838-146">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="40838-147">**C#**</span><span class="sxs-lookup"><span data-stu-id="40838-147">**C#**</span></span> 

    1. <span data-ttu-id="40838-148">Когда отобразится исходный код функции, замените содержимое файла **run.csx** содержимым из репозитория [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).</span><span class="sxs-lookup"><span data-stu-id="40838-148">When the function's source code appears, replace all of **run.csx** with the content of [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).</span></span>

1. <span data-ttu-id="40838-149">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="40838-149">**JavaScript**</span></span> 

    1. <span data-ttu-id="40838-150">(JavaScript) Для этой функции требуется пакет `azure-storage` из npm, чтобы сгенерировать токен подписанного URL-адреса (SAS), необходимый для создания защищенного URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="40838-150">(JavaScript) This function requires the `azure-storage` package from npm to generate the shared access signature (SAS) token required to build the secure URL.</span></span> <span data-ttu-id="40838-151">Чтобы установить пакет npm, щелкните имя приложения-функции в области навигации слева и выберите **Функции платформы**.</span><span class="sxs-lookup"><span data-stu-id="40838-151">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="40838-152">(JavaScript) Чтобы открыть окно консоли, щелкните **Консоль**.</span><span class="sxs-lookup"><span data-stu-id="40838-152">(JavaScript) Click **Console** to reveal a console window.</span></span>

        ![Открытие окна консоли](media/functions-first-serverless-web-app/2-open-console.jpg)

    1. <span data-ttu-id="40838-154">(JavaScript) Убедитесь, что в качестве текущего каталога используется **d:\home\site\wwwroot**, выполнив команду `cd d:\home\site\wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="40838-154">(JavaScript) Ensure the current directory is **d:\home\site\wwwroot** by running the command `cd d:\home\site\wwwroot`.</span></span>

    1. <span data-ttu-id="40838-155">(JavaScript) Выполните команду `npm init -y`, чтобы создать пустой файл **package.json**.</span><span class="sxs-lookup"><span data-stu-id="40838-155">(JavaScript) Run the command `npm init -y` to create an empty **package.json** file.</span></span>

    1. <span data-ttu-id="40838-156">(JavaScript) В консоли выполните команду `npm install --save azure-storage`, чтобы установить пакет и сохранить его в файле **package.json**.</span><span class="sxs-lookup"><span data-stu-id="40838-156">(JavaScript) Run the command `npm install --save azure-storage` in the console to install the package and save it in **package.json**.</span></span> <span data-ttu-id="40838-157">Выполнение операции может занять одну-две минуты.</span><span class="sxs-lookup"><span data-stu-id="40838-157">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="40838-158">(JavaScript) Щелкните имя функции (**GetUploadUrl**) в области навигации слева, чтобы открыть окно с ее кодом. Замените содержимое файла **index.js** содержимым из репозитория [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).</span><span class="sxs-lookup"><span data-stu-id="40838-158">(JavaScript) Click on the function name (**GetUploadUrl**) in the left navigation to reveal the function, replace all of **index.js** with the content of [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).</span></span>
    
        ![Файл index.js после обновления](media/functions-first-serverless-web-app/2-paste-js.jpg)

1. <span data-ttu-id="40838-160">Щелкните **Журналы** под окном кода, чтобы развернуть панель журналов.</span><span class="sxs-lookup"><span data-stu-id="40838-160">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="40838-161">Выберите команду **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="40838-161">Click **Save**.</span></span> <span data-ttu-id="40838-162">Просмотрите записи на панели журналов и проверьте, успешно ли скомпилирована функция.</span><span class="sxs-lookup"><span data-stu-id="40838-162">Check the logs panel to ensure the function is successfully compiled.</span></span>

<span data-ttu-id="40838-163">Функция создает так называемый подписанный URL-адрес (SAS), который используется для отправки файла в хранилище BLOB-объектов.</span><span class="sxs-lookup"><span data-stu-id="40838-163">The function generates what is called a shared access signature (SAS) URL that is used to upload a file to Blob storage.</span></span> <span data-ttu-id="40838-164">URL-адрес SAS действителен в течение короткого периода времени и позволяет отправить только один файл.</span><span class="sxs-lookup"><span data-stu-id="40838-164">The SAS URL is valid for a short period of time and only allows a single file to be uploaded.</span></span> <span data-ttu-id="40838-165">Дополнительные сведения об [использовании подписанных URL-адресов](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) см. в документации по хранилищу BLOB-объектов.</span><span class="sxs-lookup"><span data-stu-id="40838-165">Consult the Blob storage documentation for more information on [using shared access signatures](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).</span></span>


## <a name="add-an-environment-variable-for-the-storage-connection-string"></a><span data-ttu-id="40838-166">Добавление переменной среды для строки подключения к хранилищу</span><span class="sxs-lookup"><span data-stu-id="40838-166">Add an environment variable for the storage connection string</span></span>

<span data-ttu-id="40838-167">Чтобы только что созданная функция могла сгенерировать URL-адрес SAS, ей требуется строка подключения для учетной записи хранения.</span><span class="sxs-lookup"><span data-stu-id="40838-167">The function you just created requires a connection string for the Storage account so that it can generate the SAS URL.</span></span> <span data-ttu-id="40838-168">Чтобы не указывать строку подключения прямо в тексте функции, эту строку можно сохранить как параметр приложения.</span><span class="sxs-lookup"><span data-stu-id="40838-168">Instead of hardcoding the connection string in the function's body, it can be stored as an application setting.</span></span> <span data-ttu-id="40838-169">Параметры приложения доступны в виде переменных среды для всех функций в приложении-функции.</span><span class="sxs-lookup"><span data-stu-id="40838-169">Application settings are accessible as environment variables by all functions in the Function app.</span></span>

1. <span data-ttu-id="40838-170">В Cloud Shell запросите строку подключения для учетной записи хранения и сохраните ее в переменной bash с именем **STORAGE_CONNECTION_STRING**.</span><span class="sxs-lookup"><span data-stu-id="40838-170">In the Cloud Shell, query the Storage account connection string and save it to a bash variable named **STORAGE_CONNECTION_STRING**.</span></span>

    ```azurecli
    export STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n <storage account name> -g first-serverless-app --query "connectionString" --output tsv)
    ```

    <span data-ttu-id="40838-171">Убедитесь, что эта переменная успешно задана.</span><span class="sxs-lookup"><span data-stu-id="40838-171">Confirm the variable is set successfully.</span></span>

    ```azurecli
    echo $STORAGE_CONNECTION_STRING
    ```

1. <span data-ttu-id="40838-172">Создайте параметр приложения с именем **AZURE_STORAGE_CONNECTION_STRING**, используя значение, сохраненное на предыдущем шаге.</span><span class="sxs-lookup"><span data-stu-id="40838-172">Create a new application setting named **AZURE_STORAGE_CONNECTION_STRING** using the value saved from the previous step.</span></span>

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings AZURE_STORAGE_CONNECTION_STRING=$STORAGE_CONNECTION_STRING -o table
    ```

    <span data-ttu-id="40838-173">Убедитесь, что в выходных данных команды содержится новый параметр приложения с правильным значением.</span><span class="sxs-lookup"><span data-stu-id="40838-173">Confirm that the command's output contains the new application setting with the correct value.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="40838-174">Тестирование бессерверной функции</span><span class="sxs-lookup"><span data-stu-id="40838-174">Test the serverless function</span></span>

<span data-ttu-id="40838-175">На портале Azure предоставлены встроенные средства, которые позволяют не только создавать и редактировать функции, но и тестировать их.</span><span class="sxs-lookup"><span data-stu-id="40838-175">In addition to creating and editing functions, the Azure portal also provides an built-in tool for testing functions.</span></span>

1. <span data-ttu-id="40838-176">Чтобы проверить бессерверную функцию, которая активируется HTTP-запросом, щелкните вкладку **Тест** в правой части окна кода для развертывания области тестирования.</span><span class="sxs-lookup"><span data-stu-id="40838-176">To test the HTTP serverless function, click on the **Test** tab on the right of the code window to expand the test panel.</span></span>

1. <span data-ttu-id="40838-177">Измените **метод HTTP** на **GET**.</span><span class="sxs-lookup"><span data-stu-id="40838-177">Change the **Http method** to **GET**.</span></span>

1. <span data-ttu-id="40838-178">В разделе **Запрос** щелкните *Добавить параметр*\*, чтобы добавить следующий параметр:</span><span class="sxs-lookup"><span data-stu-id="40838-178">Under **Query**, click *Add parameter*\* and add the following parameter:</span></span>

    | <span data-ttu-id="40838-179">name</span><span class="sxs-lookup"><span data-stu-id="40838-179">name</span></span>      |  <span data-ttu-id="40838-180">value</span><span class="sxs-lookup"><span data-stu-id="40838-180">value</span></span>   | 
    | --- | --- |
    | <span data-ttu-id="40838-181">filename</span><span class="sxs-lookup"><span data-stu-id="40838-181">filename</span></span> | <span data-ttu-id="40838-182">image1.jpg</span><span class="sxs-lookup"><span data-stu-id="40838-182">image1.jpg</span></span> |

1. <span data-ttu-id="40838-183">В области тестирования нажмите кнопку **Запуск**, чтобы отправить HTTP-запрос к функции.</span><span class="sxs-lookup"><span data-stu-id="40838-183">Click **Run** in the test panel to send an HTTP request to the function.</span></span>

1. <span data-ttu-id="40838-184">Функция возвращает выходные данные с URL-адресом для отправки.</span><span class="sxs-lookup"><span data-stu-id="40838-184">The function returns an upload URL in the output.</span></span> <span data-ttu-id="40838-185">Сведения о выполнении функции отображаются в области журналов.</span><span class="sxs-lookup"><span data-stu-id="40838-185">The function execution appears in the logs panel.</span></span>

    ![Область журналов со сведениями о том, что функция выполнена успешно](media/functions-first-serverless-web-app/2-test-function.png)


## <a name="configure-cors-in-the-function-app"></a><span data-ttu-id="40838-187">Настройка CORS в приложении-функции</span><span class="sxs-lookup"><span data-stu-id="40838-187">Configure CORS in the function app</span></span>

<span data-ttu-id="40838-188">Так как внешний интерфейс приложения размещен в хранилище BLOB-объектов, для него используется не такое доменное имя, как для приложения-функции Azure.</span><span class="sxs-lookup"><span data-stu-id="40838-188">Because the app's frontend is hosted in Blob storage, it has a different domain name than the Azure Function app.</span></span> <span data-ttu-id="40838-189">Чтобы из клиентского кода JavaScript успешно вызывалась функция, которую вы только что создали, нужно настроить CORS (предоставление общего доступа к ресурсам независимо от источника) для приложения-функции.</span><span class="sxs-lookup"><span data-stu-id="40838-189">For the client-side JavaScript to successfully call the function you just created, the function app needs to be configured for cross-origin resource sharing (CORS).</span></span>

1. <span data-ttu-id="40838-190">На панели навигации слева в окне приложения-функции щелкните имя приложения-функции.</span><span class="sxs-lookup"><span data-stu-id="40838-190">In the left navigation bar of the function app window, click on the name of your function app.</span></span>

1. <span data-ttu-id="40838-191">Выберите **Функции платформы**, чтобы просмотреть список дополнительных функций.</span><span class="sxs-lookup"><span data-stu-id="40838-191">Click on **Platform features** to view a list of advanced features.</span></span>

1. <span data-ttu-id="40838-192">В разделе **API** щелкните **CORS**.</span><span class="sxs-lookup"><span data-stu-id="40838-192">Under **API**, click **CORS**.</span></span>

    ![Выбор варианта CORS](media/functions-first-serverless-web-app/2-open-cors.jpg)

1. <span data-ttu-id="40838-194">Добавьте допустимый источник для URL-адреса приложения из предыдущего модуля без символа **/** в конце (например, `https://firstserverlessweb.z4.web.core.windows.net`).</span><span class="sxs-lookup"><span data-stu-id="40838-194">Add an allow origin for the application URL from the previous module, omitting the trailing **/** (for example: `https://firstserverlessweb.z4.web.core.windows.net`).</span></span>

    ![Снимок экрана с параметрами CORS, на котором показано добавление URL-адреса бессерверного веб-приложения](media/functions-first-serverless-web-app/2-add-cors.png)

1. <span data-ttu-id="40838-196">Выберите команду **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="40838-196">Click **Save**.</span></span>

1. <span data-ttu-id="40838-197">C#</span><span class="sxs-lookup"><span data-stu-id="40838-197">C#</span></span>

   1. <span data-ttu-id="40838-198">(C#) Вернитесь к функции `GetUploadUrl` и выберите вкладку **Интегрировать**.</span><span class="sxs-lookup"><span data-stu-id="40838-198">(C#) Navigate back to the `GetUploadUrl` function, and then select the **Integrate** tab.</span></span>

   1. <span data-ttu-id="40838-199">(C#) В разделе "Выбранные методы HTTP" щелкните **OPTIONS**.</span><span class="sxs-lookup"><span data-stu-id="40838-199">(C#) Under Selected HTTP methods, select **OPTIONS**.</span></span>

      <span data-ttu-id="40838-200">Для методов **GET**, **POST** и **OPTIONS** должны быть установлены флажки.</span><span class="sxs-lookup"><span data-stu-id="40838-200">**GET**, **POST**, and **OPTIONS** should all be selected.</span></span> <span data-ttu-id="40838-201">Для CORS используется метод OPTIONS, который не выбран по умолчанию для функций C#.</span><span class="sxs-lookup"><span data-stu-id="40838-201">CORS uses the OPTIONS method, which is not selected by default for C# functions.</span></span>  

   1. <span data-ttu-id="40838-202">(C#) Щелкните **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="40838-202">(C#) Click **Save**.</span></span>

1. <span data-ttu-id="40838-203">Оставаясь на портале, перейдите к приложению-функции, выберите вкладку **Обзор** и щелкните **Перезапустить**, чтобы изменения для CORS вступили в силу.</span><span class="sxs-lookup"><span data-stu-id="40838-203">Still in the portal, navigate to the function app, select the **Overview** tab, and then click **Restart** to make sure that the changes for CORS take effect.</span></span>

## <a name="configure-cors-in-the-storage-account"></a><span data-ttu-id="40838-204">Настройка CORS в учетной записи хранения</span><span class="sxs-lookup"><span data-stu-id="40838-204">Configure CORS in the Storage account</span></span>

<span data-ttu-id="40838-205">Так как для отправки файлов приложение выполняет клиентские вызовы JavaScript к хранилищу BLOB-объектов, нужно также настроить учетную запись хранения для CORS.</span><span class="sxs-lookup"><span data-stu-id="40838-205">Because the app also makes client-side JavaScript calls to Blob Storage to upload files, you also have to configure the Storage account for CORS.</span></span>

1. <span data-ttu-id="40838-206">Чтобы разрешить отправку файлов в учетную запись хранения из всех источников, выполните приведенную ниже команду.</span><span class="sxs-lookup"><span data-stu-id="40838-206">Run the following command to allow all origins to upload files to the Storage account.</span></span>

    ```azurecli
    az storage cors add --methods OPTIONS PUT --origins '*' --exposed-headers '*' --allowed-headers '*' --services b --account-name <storage account name>
    ```


## <a name="modify-the-web-app-to-upload-images"></a><span data-ttu-id="40838-207">Изменение веб-приложения для отправки изображений</span><span class="sxs-lookup"><span data-stu-id="40838-207">Modify the web app to upload images</span></span>

<span data-ttu-id="40838-208">Веб-приложение извлекает параметры из файла с именем **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="40838-208">The web app retrieves settings from a file named **settings.js**.</span></span> <span data-ttu-id="40838-209">На следующих этапах вы с помощью Cloud Shell создадите файл, а затем укажете `window.apiBaseUrl` для URL-адреса приложения-функции и `window.blobBaseUrl` для URL-адреса конечной точки хранилища BLOB-объектов Azure.</span><span class="sxs-lookup"><span data-stu-id="40838-209">In the following steps, you create the file using Cloud Shell, then set `window.apiBaseUrl` to the URL of the Function app and `window.blobBaseUrl` to the URL of the Azure Blob Storage endpoint.</span></span>

1. <span data-ttu-id="40838-210">В Cloud Shell убедитесь, что в качестве текущего каталога используется папка **www/dist**.</span><span class="sxs-lookup"><span data-stu-id="40838-210">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="40838-211">Запросите URL-адрес приложения-функции и сохраните его в переменной bash с именем **FUNCTION_APP_URL**.</span><span class="sxs-lookup"><span data-stu-id="40838-211">Query the function app's URL and store it in a bash variable named **FUNCTION_APP_URL**.</span></span>

    ```azurecli
    export FUNCTION_APP_URL="https://"$(az functionapp show -n <function app name> -g first-serverless-app --query "defaultHostName" --output tsv)
    ```

    <span data-ttu-id="40838-212">Убедитесь, что переменная указана правильно.</span><span class="sxs-lookup"><span data-stu-id="40838-212">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $FUNCTION_APP_URL
    ```

1. <span data-ttu-id="40838-213">Чтобы задать базовый URI для вызовов API к своему приложению-функции, создайте файл **settings.js** и добавьте URL-адрес приложения-функции, как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="40838-213">To set the base URI of API calls to your function app, create **settings.js** and add the function app URL like the following.</span></span>

    `window.apiBaseUrl = 'https://fnapp@lab.GlobalLabInstanceId.azurewebsites.net'`

    <span data-ttu-id="40838-214">Чтобы внести изменения, можно выполнить указанную далее команду или использовать редактор командной строки, например VIM.</span><span class="sxs-lookup"><span data-stu-id="40838-214">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.apiBaseUrl = '$FUNCTION_APP_URL'" > settings.js
    ```

    <span data-ttu-id="40838-215">Убедитесь, что файл успешно записан.</span><span class="sxs-lookup"><span data-stu-id="40838-215">Confirm the file was successfully written.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="40838-216">Запросите базовый URL-адрес хранилища BLOB-объектов и сохраните его в переменной bash с именем **BLOB_BASE_URL**.</span><span class="sxs-lookup"><span data-stu-id="40838-216">Query the Blob Storage base URL and store it in a bash variable named **BLOB_BASE_URL**.</span></span>

    ```azurecli
    export BLOB_BASE_URL=$(az storage account show -n <storage account name> -g first-serverless-app --query primaryEndpoints.blob -o tsv | sed 's/\/$//')
    ```

    <span data-ttu-id="40838-217">Убедитесь, что переменная указана правильно.</span><span class="sxs-lookup"><span data-stu-id="40838-217">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $BLOB_BASE_URL
    ```

1. <span data-ttu-id="40838-218">Чтобы задать базовый URI для вызовов API к своему приложению-функции, добавьте URL-адрес хранилища в файл **settings.js**, как в приведенном ниже примере строки кода.</span><span class="sxs-lookup"><span data-stu-id="40838-218">To set the base URI of API calls to your function app, append the storage URL like the following line of code to **settings.js**.</span></span>

    `window.blobBaseUrl = 'https://mystorage.blob.core.windows.net'`

    <span data-ttu-id="40838-219">Чтобы внести изменения, можно выполнить указанную далее команду или использовать редактор командной строки, например VIM.</span><span class="sxs-lookup"><span data-stu-id="40838-219">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.blobBaseUrl = '$BLOB_BASE_URL'" >> settings.js
    ```

    <span data-ttu-id="40838-220">Убедитесь, что файл успешно записан и теперь содержит две строки.</span><span class="sxs-lookup"><span data-stu-id="40838-220">Confirm the file was successfully written and it now contains 2 lines.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="40838-221">Отправьте файл в хранилище BLOB-объектов.</span><span class="sxs-lookup"><span data-stu-id="40838-221">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-web-application"></a><span data-ttu-id="40838-222">Тестирование веб-приложения</span><span class="sxs-lookup"><span data-stu-id="40838-222">Test the web application</span></span>

<span data-ttu-id="40838-223">На этом этапе приложение коллекции может отправлять изображения в хранилище BLOB-объектов, но еще не может выводить изображения.</span><span class="sxs-lookup"><span data-stu-id="40838-223">At this point, the gallery application is able to upload an image to Blob storage, but it can't display images yet.</span></span> <span data-ttu-id="40838-224">Приложение попытается вызвать еще не существующую функцию `GetImages`, которую вы создадите в следующем модуле.</span><span class="sxs-lookup"><span data-stu-id="40838-224">It will try to call a `GetImages` function that doesn't exist yet because you create it in a later module.</span></span> <span data-ttu-id="40838-225">Этот вызов завершится ошибкой и веб-страница перестанет отвечать на запросы на этапе анализа, но выбранное изображение успешно отправится.</span><span class="sxs-lookup"><span data-stu-id="40838-225">That call will fail, and the web page will appear to be stuck on "Analyzing...", but the image you select will be successfully uploaded.</span></span>

<span data-ttu-id="40838-226">Убедитесь в том, что изображение успешно отправлено, проверив содержимое контейнера **images** на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="40838-226">You can verify an image is successfully uploaded by checking the contents of the **images** container in the Azure portal.</span></span>

1. <span data-ttu-id="40838-227">В окне браузера перейдите к приложению.</span><span class="sxs-lookup"><span data-stu-id="40838-227">In a browser window, browse to the application.</span></span> <span data-ttu-id="40838-228">Выберите файл изображения и отправьте его.</span><span class="sxs-lookup"><span data-stu-id="40838-228">Select an image file and upload it.</span></span> <span data-ttu-id="40838-229">Отправка завершится, но, так как мы еще не добавили возможность выводить изображения, в приложении не отобразится отправленная фотография</span><span class="sxs-lookup"><span data-stu-id="40838-229">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span> <span data-ttu-id="40838-230">(веб-страница перестанет отвечать на запросы на этапе анализа изображения; вы исправите это позже).</span><span class="sxs-lookup"><span data-stu-id="40838-230">(The web page appears to be stuck on "Analyzing image..."; you'll fix that later.)</span></span>

1. <span data-ttu-id="40838-231">В Cloud Shell проверьте, отправлено ли изображение в контейнер **images**.</span><span class="sxs-lookup"><span data-stu-id="40838-231">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="40838-232">Прежде чем перейти к следующему руководству, удалите все файлы в контейнере **images**.</span><span class="sxs-lookup"><span data-stu-id="40838-232">Before moving on to the next tutorial, delete all files in the **images** container.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="40838-233">Сводка</span><span class="sxs-lookup"><span data-stu-id="40838-233">Summary</span></span>

<span data-ttu-id="40838-234">В этом модуле вы создали приложение-функцию Azure и узнали, как с помощью бессерверных функций разрешить веб-приложению отправлять изображения в хранилище BLOB-объектов.</span><span class="sxs-lookup"><span data-stu-id="40838-234">In this module, you created an Azure Function app and learned how to use a serverless function to allow a web application to upload images to Blob storage.</span></span> <span data-ttu-id="40838-235">Далее вы научитесь создавать эскизы для отправленных изображений с помощью бессерверной функции, активируемой большим двоичным объектом.</span><span class="sxs-lookup"><span data-stu-id="40838-235">Next, you learn how to create thumbnails for the uploaded images using a Blob triggered serverless function.</span></span>
