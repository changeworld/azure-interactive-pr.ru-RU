---
title: включение файла
description: включение файла
services: functions
author: tdykstra
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/25/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: 1c4aaf7afd9fbc54dd34c2cca13a8b74e947c16a
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079698"
---
<span data-ttu-id="7ae86-103">Вы успешно создали это полнофункциональное бессерверное приложение с помощью служб Azure.</span><span class="sxs-lookup"><span data-stu-id="7ae86-103">You've successfully created a full-featured, serverless application using Azure services.</span></span>

## <a name="clean-up-resources"></a><span data-ttu-id="7ae86-104">Очистка ресурсов</span><span class="sxs-lookup"><span data-stu-id="7ae86-104">Clean up resources</span></span>

<span data-ttu-id="7ae86-105">Когда закончите работу с этим приложением, можете удалить все ресурсы, созданные в рамках этого руководства, с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="7ae86-105">When you are done working with this application, you can use the following command to delete all resources created during the tutorial:</span></span>

```azurecli
az group delete --name first-serverless-app
```

<span data-ttu-id="7ae86-106">При появлении запроса введите `y`.</span><span class="sxs-lookup"><span data-stu-id="7ae86-106">Type `y` when prompted.</span></span>  

## <a name="next-steps"></a><span data-ttu-id="7ae86-107">Дополнительная информация</span><span class="sxs-lookup"><span data-stu-id="7ae86-107">Next steps</span></span>

<span data-ttu-id="7ae86-108">Из этого руководства вы узнали, как выполнить следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="7ae86-108">In this tutorial, you learned how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="7ae86-109">Настройка хранилища BLOB-объектов Azure для размещения статического веб-сайта и отправляемых изображений.</span><span class="sxs-lookup"><span data-stu-id="7ae86-109">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="7ae86-110">Отправка изображений в хранилище BLOB-объектов Azure с помощью службы "Функции Azure".</span><span class="sxs-lookup"><span data-stu-id="7ae86-110">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="7ae86-111">Изменение размера изображений с помощью службы "Функции Azure".</span><span class="sxs-lookup"><span data-stu-id="7ae86-111">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="7ae86-112">Хранение метаданных изображений в Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="7ae86-112">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="7ae86-113">Использование API компьютерного зрения Cognitive Services для автоматического создания подписей изображений.</span><span class="sxs-lookup"><span data-stu-id="7ae86-113">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="7ae86-114">Использование Azure Active Directory для защиты веб-приложения путем проверки подлинности пользователей.</span><span class="sxs-lookup"><span data-stu-id="7ae86-114">Use Azure Active Directory to secure the web app by authenticating users.</span></span>

<span data-ttu-id="7ae86-115">Чтобы узнать, как подключить больше служб к службе "Функции", перейдите к руководству по Logic Apps.</span><span class="sxs-lookup"><span data-stu-id="7ae86-115">To learn how to connect even more services to Functions, continue to the Logic Apps tutorial.</span></span> 

> [!div class="nextstepaction"]
> [<span data-ttu-id="7ae86-116">Использование службы "Функции" с Logic Apps</span><span class="sxs-lookup"><span data-stu-id="7ae86-116">Functions with Logic Apps</span></span>](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)