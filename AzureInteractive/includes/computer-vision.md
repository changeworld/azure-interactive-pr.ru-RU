---
title: включение файла
description: включение файла
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f51b864cab14273c1e88dd85d22400e0e76ef770
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460010"
---
<span data-ttu-id="bed26-103">На этом этапе приложение представляет собой коллекцию функций, которая позволяет отправлять и просматривать изображения.</span><span class="sxs-lookup"><span data-stu-id="bed26-103">At this point, the application is a functional gallery that allows you to upload and view images.</span></span> <span data-ttu-id="bed26-104">Из этого модуля вы узнаете, как с помощью API компьютерного зрения из Microsoft Cognitive Services создавать подписи для отправляемых изображений и сохранять эти подписи с метаданными изображения в Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="bed26-104">In this module, you learn how to use the Computer Vision API from Microsoft Cognitive Services to generate captions for uploaded images and save the captions with the image metadata in Cosmos DB.</span></span>

## <a name="create-a-computer-vision-account"></a><span data-ttu-id="bed26-105">Создание учетной записи API компьютерного зрения</span><span class="sxs-lookup"><span data-stu-id="bed26-105">Create a Computer Vision account</span></span>

<span data-ttu-id="bed26-106">Microsoft Cognitive Services — это набор служб, которые помогают разработчикам сделать свои приложения более интеллектуальными.</span><span class="sxs-lookup"><span data-stu-id="bed26-106">Microsoft Cognitive Services is a collection of services available to developers to make their applications more intelligent.</span></span> <span data-ttu-id="bed26-107">API компьютерного зрения — это бессерверная служба, которая с помощью усовершенствованных алгоритмов обрабатывает изображения и возвращает сведения о каждом из них.</span><span class="sxs-lookup"><span data-stu-id="bed26-107">The Computer Vision API is a serverless service that processes images using advanced algorithms and returns information about each image.</span></span> <span data-ttu-id="bed26-108">Есть бесплатный уровень службы, на котором предоставляется 5000 вызовов API в месяц.</span><span class="sxs-lookup"><span data-stu-id="bed26-108">It has a free tier that provides up to 5000 API calls per month.</span></span>

1. <span data-ttu-id="bed26-109">Убедитесь, что окно Cloud Shell открыто.</span><span class="sxs-lookup"><span data-stu-id="bed26-109">Ensure you are still signed into the Cloud Shell.</span></span> <span data-ttu-id="bed26-110">Если вы уже вышли, выберите **Enter focus mode** (Перейти в режим фокусировки), чтобы открыть окно Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="bed26-110">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="bed26-111">Создайте учетную запись Cognitive Services с типом **ComputerVision** и уникальным именем в группе ресурсов.</span><span class="sxs-lookup"><span data-stu-id="bed26-111">Create a new Cognitive Services account of kind **ComputerVision** with a unique name in your resource group.</span></span> <span data-ttu-id="bed26-112">Для уровня "Бесплатный" укажите **F0** в качестве номера SKU.</span><span class="sxs-lookup"><span data-stu-id="bed26-112">For the free tier, use **F0** as the SKU.</span></span> <span data-ttu-id="bed26-113">Если у вас уже есть учетная запись API компьютерного зрения, возможно, потребуется создать учетную запись ценовой категории "Стандартный" (S1), что может повлечь за собой некоторые расходы.</span><span class="sxs-lookup"><span data-stu-id="bed26-113">If you already have an existing Computer Vision account, you may need to create a Standard account (S1); however, this may incur some costs.</span></span>

    ```azurecli
    az cognitiveservices account create -g first-serverless-app -n <computer vision account name> --kind ComputerVision --sku F0 -l westcentralus
    ```


## <a name="create-function-app-settings-for-computer-vision-url-and-key"></a><span data-ttu-id="bed26-114">Создание параметров приложения-функции для URL-адреса и ключа API компьютерного зрения</span><span class="sxs-lookup"><span data-stu-id="bed26-114">Create Function App settings for Computer Vision URL and key</span></span>

<span data-ttu-id="bed26-115">Чтобы вызвать API компьютерного зрения, вам потребуется URL-адрес и ключ.</span><span class="sxs-lookup"><span data-stu-id="bed26-115">To call the Computer Vision API, a URL and key are required.</span></span>

1. <span data-ttu-id="bed26-116">Получите URL-адрес и ключ API компьютерного зрения и сохраните их в переменных bash.</span><span class="sxs-lookup"><span data-stu-id="bed26-116">Obtain the Computer Vision API key and URL and save them in bash variables.</span></span>

    ```azurecli
    export COMP_VISION_KEY=$(az cognitiveservices account keys list -g first-serverless-app -n <computer vision account name> --query key1 --output tsv)
    ```
    ```azurecli
    export COMP_VISION_URL=$(az cognitiveservices account show -g first-serverless-app -n <computer vision account name> --query endpoint --output tsv)
    ```

1. <span data-ttu-id="bed26-117">В приложении-функции создайте параметры приложения с именами **COMP_VISION_KEY** и **COMP_VISION_URL** соответственно.</span><span class="sxs-lookup"><span data-stu-id="bed26-117">Create app settings named **COMP_VISION_KEY** and **COMP_VISION_URL**, respectively, in the function app.</span></span>

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings COMP_VISION_KEY=$COMP_VISION_KEY COMP_VISION_URL=$COMP_VISION_URL -o table
    ```


## <a name="call-computer-vision-api-from-resizeimage-function"></a><span data-ttu-id="bed26-118">Вызов API компьютерного зрения из функции ResizeImage</span><span class="sxs-lookup"><span data-stu-id="bed26-118">Call Computer Vision API from ResizeImage function</span></span>

<span data-ttu-id="bed26-119">На следующих этапах вы измените функцию **ResizeImage**, чтобы она вызывала API компьютерного зрения для описания каждого отправленного изображения и сохранения этого описания в Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="bed26-119">In the following steps, you modify the **ResizeImage** function to call the Computer Vision API to describe each uploaded image and save the description in Cosmos DB.</span></span>

1. <span data-ttu-id="bed26-120">Откройте приложение-функцию на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="bed26-120">Open your function app in the Azure Portal.</span></span>

1. <span data-ttu-id="bed26-121">**C#**</span><span class="sxs-lookup"><span data-stu-id="bed26-121">**C#**</span></span>

    1. <span data-ttu-id="bed26-122">(C#) С помощью области навигации слева найдите функцию **ResizeImage** и откройте окно с ее кодом.</span><span class="sxs-lookup"><span data-stu-id="bed26-122">(C#) Using the left navigation, locate the **ResizeImage** function and open its code window.</span></span>

    1. <span data-ttu-id="bed26-123">(C#) Замените код содержимым из репозитория [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx).</span><span class="sxs-lookup"><span data-stu-id="bed26-123">(C#) Replace the code with the contents of [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx).</span></span> <span data-ttu-id="bed26-124">В этом коде используется `HttpClient`, чтобы вызывать API компьютерного зрения и сохранять результат в Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="bed26-124">This code uses `HttpClient` to call Computer Vision API and save its result in Cosmos DB.</span></span>

1. <span data-ttu-id="bed26-125">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="bed26-125">**JavaScript**</span></span>

    1. <span data-ttu-id="bed26-126">(JavaScript) Чтобы эта функция могла выполнить вызов HTTP к API компьютерного зрения, ей требуется пакет `axios` из npm.</span><span class="sxs-lookup"><span data-stu-id="bed26-126">(JavaScript) This function requires the `axios` package from npm to make an HTTP call to the Computer Vision API.</span></span> <span data-ttu-id="bed26-127">Чтобы установить пакет npm, щелкните имя приложения-функции в области навигации слева и выберите **Функции платформы**.</span><span class="sxs-lookup"><span data-stu-id="bed26-127">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="bed26-128">(JavaScript) Чтобы открыть окно консоли, щелкните **Консоль**.</span><span class="sxs-lookup"><span data-stu-id="bed26-128">(JavaScript) Click **Console** to reveal a console window.</span></span>

    1. <span data-ttu-id="bed26-129">(JavaScript) В консоли выполните команду `npm install axios`.</span><span class="sxs-lookup"><span data-stu-id="bed26-129">(JavaScript) Run the command `npm install axios` in the console.</span></span> <span data-ttu-id="bed26-130">Выполнение операции может занять одну-две минуты.</span><span class="sxs-lookup"><span data-stu-id="bed26-130">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="bed26-131">(JavaScript) Щелкните имя функции (**ResizeImage**) в области навигации слева, чтобы открыть окно с ее кодом. Замените содержимое файла **index.js** содержимым из репозитория [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).</span><span class="sxs-lookup"><span data-stu-id="bed26-131">(JavaScript) Click on the function name (**ResizeImage**) in the left navigation to reveal the function, and then replace all of **index.js** with the contents of [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).</span></span>

1. <span data-ttu-id="bed26-132">Щелкните **Журналы** под окном кода, чтобы развернуть панель журналов.</span><span class="sxs-lookup"><span data-stu-id="bed26-132">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="bed26-133">Выберите команду **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="bed26-133">Click **Save**.</span></span> <span data-ttu-id="bed26-134">Просмотрите записи на панели журналов и проверьте, успешно ли сохранена функция и нет ли ошибок.</span><span class="sxs-lookup"><span data-stu-id="bed26-134">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-application"></a><span data-ttu-id="bed26-135">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="bed26-135">Test the application</span></span>

1. <span data-ttu-id="bed26-136">Откройте приложение в браузере.</span><span class="sxs-lookup"><span data-stu-id="bed26-136">Open the application in a browser.</span></span> <span data-ttu-id="bed26-137">Выберите файл изображения и отправьте его.</span><span class="sxs-lookup"><span data-stu-id="bed26-137">Select an image file, and upload it.</span></span>

1. <span data-ttu-id="bed26-138">Через несколько секунд на странице должен появиться эскиз нового изображения.</span><span class="sxs-lookup"><span data-stu-id="bed26-138">After a few seconds, the thumbnail of the new image should appear on the page.</span></span> <span data-ttu-id="bed26-139">Наведите указатель мыши на изображение, чтобы отобразилось описание, созданное с помощью API компьютерного зрения.</span><span class="sxs-lookup"><span data-stu-id="bed26-139">Hover over the image to see the description generated by Computer Vision.</span></span>


## <a name="summary"></a><span data-ttu-id="bed26-140">Сводка</span><span class="sxs-lookup"><span data-stu-id="bed26-140">Summary</span></span>

<span data-ttu-id="bed26-141">Из этого модуля вы узнали, как с помощью API компьютерного зрения Microsoft Cognitive Services автоматически создавать подписи для каждого отправленного изображения.</span><span class="sxs-lookup"><span data-stu-id="bed26-141">In this module, you learned how to automatically generate captions for each uploaded image using Microsoft Cognitive Services Computer Vision API.</span></span> <span data-ttu-id="bed26-142">Далее вы узнаете, как добавить проверку подлинности в приложение, используя соответствующую функцию Службы приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="bed26-142">Next, you learn how to add authentication to the application using Azure App Service Authentication.</span></span>
