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
ms.openlocfilehash: d651fd3d03f2678d625e60f9ab1e9f59e623964f
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079685"
---
<span data-ttu-id="ee13f-103">В рамках этого руководства вы развернете простое веб-приложение, которое представляет собой пользовательский интерфейс на основе HTML.</span><span class="sxs-lookup"><span data-stu-id="ee13f-103">In this tutorial, you deploy a simple web application that presents an HTML-based user interface.</span></span> <span data-ttu-id="ee13f-104">Благодаря бессерверной внутренней части приложение может отправлять изображения и автоматически получать подписи с описанием.</span><span class="sxs-lookup"><span data-stu-id="ee13f-104">A serverless backend enables the application to upload images and automatically get captions describing them.</span></span>

![Запуск веб-приложения](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

<span data-ttu-id="ee13f-106">На представленной ниже схеме показаны службы Azure, используемые приложением:</span><span class="sxs-lookup"><span data-stu-id="ee13f-106">The following diagram shows the Azure services used by the application:</span></span>

1. <span data-ttu-id="ee13f-107">В хранилище BLOB-объектов обслуживается статическое веб-содержимое (HTML, каскадные таблицы стилей и JS) и хранятся изображения.</span><span class="sxs-lookup"><span data-stu-id="ee13f-107">Blob Storage serves static web content (HTML, CSS, JS) and stores images.</span></span>
2. <span data-ttu-id="ee13f-108">Служба "Функции Azure" управляет отправкой изображений, изменением их размера и хранением метаданных.</span><span class="sxs-lookup"><span data-stu-id="ee13f-108">Azure Functions manages image uploads, resizing, and metadata storage.</span></span>
3. <span data-ttu-id="ee13f-109">В Cosmos DB хранятся метаданные изображений.</span><span class="sxs-lookup"><span data-stu-id="ee13f-109">Cosmos DB stores image metadata.</span></span>
4. <span data-ttu-id="ee13f-110">Logic Apps получает подписи изображений из API компьютерного зрения.</span><span class="sxs-lookup"><span data-stu-id="ee13f-110">Logic Apps gets image captions from Computer Vision API.</span></span>
5. <span data-ttu-id="ee13f-111">Azure Active Directory управляет проверкой подлинности пользователей.</span><span class="sxs-lookup"><span data-stu-id="ee13f-111">Azure Active Directory manages user authentication.</span></span>

![Схема архитектуры решения](media/functions-first-serverless-web-app/0-architecture.jpg)

<span data-ttu-id="ee13f-113">Из этого руководства вы узнаете, как выполнять следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="ee13f-113">In this tutorial, you learn how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="ee13f-114">Настройка хранилища BLOB-объектов Azure для размещения статического веб-сайта и отправляемых изображений.</span><span class="sxs-lookup"><span data-stu-id="ee13f-114">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="ee13f-115">Отправка изображений в хранилище BLOB-объектов Azure с помощью службы "Функции Azure".</span><span class="sxs-lookup"><span data-stu-id="ee13f-115">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="ee13f-116">Изменение размера изображений с помощью службы "Функции Azure".</span><span class="sxs-lookup"><span data-stu-id="ee13f-116">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="ee13f-117">Хранение метаданных изображений в Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="ee13f-117">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="ee13f-118">Использование API компьютерного зрения Cognitive Services для автоматического создания подписей изображений.</span><span class="sxs-lookup"><span data-stu-id="ee13f-118">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="ee13f-119">Использование Azure Active Directory для защиты веб-приложения путем проверки подлинности пользователей.</span><span class="sxs-lookup"><span data-stu-id="ee13f-119">Use Azure Active Directory to secure the web app by authenticating users.</span></span>