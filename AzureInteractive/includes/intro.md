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
ms.openlocfilehash: 0f86f2698a3a0c1e20272c335b63faf03b4b92d6
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460030"
---
<span data-ttu-id="fcd6a-103">В рамках этого руководства вы развернете простое веб-приложение, которое представляет собой пользовательский интерфейс на основе HTML.</span><span class="sxs-lookup"><span data-stu-id="fcd6a-103">In this tutorial, you deploy a simple web application that presents an HTML-based user interface.</span></span> <span data-ttu-id="fcd6a-104">Благодаря бессерверной внутренней части приложение может отправлять изображения и автоматически получать подписи с описанием.</span><span class="sxs-lookup"><span data-stu-id="fcd6a-104">A serverless backend enables the application to upload images and automatically get captions describing them.</span></span>

![Запуск веб-приложения](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

<span data-ttu-id="fcd6a-106">На представленной ниже схеме показаны службы Azure, используемые приложением:</span><span class="sxs-lookup"><span data-stu-id="fcd6a-106">The following diagram shows the Azure services used by the application:</span></span>

1. <span data-ttu-id="fcd6a-107">В хранилище BLOB-объектов обслуживается статическое веб-содержимое (HTML, каскадные таблицы стилей и JS) и хранятся изображения.</span><span class="sxs-lookup"><span data-stu-id="fcd6a-107">Blob Storage serves static web content (HTML, CSS, JS) and stores images.</span></span>
2. <span data-ttu-id="fcd6a-108">Служба "Функции Azure" управляет отправкой изображений, изменением их размера и хранением метаданных.</span><span class="sxs-lookup"><span data-stu-id="fcd6a-108">Azure Functions manages image uploads, resizing, and metadata storage.</span></span>
3. <span data-ttu-id="fcd6a-109">В Cosmos DB хранятся метаданные изображений.</span><span class="sxs-lookup"><span data-stu-id="fcd6a-109">Cosmos DB stores image metadata.</span></span>
4. <span data-ttu-id="fcd6a-110">Logic Apps получает подписи изображений из API компьютерного зрения.</span><span class="sxs-lookup"><span data-stu-id="fcd6a-110">Logic Apps gets image captions from Computer Vision API.</span></span>
5. <span data-ttu-id="fcd6a-111">Azure Active Directory управляет проверкой подлинности пользователей.</span><span class="sxs-lookup"><span data-stu-id="fcd6a-111">Azure Active Directory manages user authentication.</span></span>

![Схема архитектуры решения](media/functions-first-serverless-web-app/0-architecture.jpg)

<span data-ttu-id="fcd6a-113">Из этого руководства вы узнаете, как выполнять следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="fcd6a-113">In this tutorial, you learn how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="fcd6a-114">Настройка хранилища BLOB-объектов Azure для размещения статического веб-сайта и отправляемых изображений.</span><span class="sxs-lookup"><span data-stu-id="fcd6a-114">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="fcd6a-115">Отправка изображений в хранилище BLOB-объектов Azure с помощью службы "Функции Azure".</span><span class="sxs-lookup"><span data-stu-id="fcd6a-115">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="fcd6a-116">Изменение размера изображений с помощью службы "Функции Azure".</span><span class="sxs-lookup"><span data-stu-id="fcd6a-116">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="fcd6a-117">Хранение метаданных изображений в Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="fcd6a-117">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="fcd6a-118">Использование API компьютерного зрения Cognitive Services для автоматического создания подписей изображений.</span><span class="sxs-lookup"><span data-stu-id="fcd6a-118">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="fcd6a-119">Использование Azure Active Directory для защиты веб-приложения путем проверки подлинности пользователей.</span><span class="sxs-lookup"><span data-stu-id="fcd6a-119">Use Azure Active Directory to secure the web app by authenticating users.</span></span>
