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
ms.openlocfilehash: 426a7287458a48d1bda220ad1a5f067be2ce77d6
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460047"
---
<span data-ttu-id="986ae-103">Функция проверки подлинности Службы приложений Azure обеспечивает полную поддержку проверки подлинности в приложении-функции Azure.</span><span class="sxs-lookup"><span data-stu-id="986ae-103">Azure App Service Authentication enables turn-key authentication support in an Azure Function app.</span></span> <span data-ttu-id="986ae-104">Эта функция легко интегрируется с Facebook, Twitter, учетной записью Майкрософт, Google и Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="986ae-104">It integrates seamlessly with Facebook, Twitter, Microsoft Account, Google, and Azure Active Directory.</span></span> <span data-ttu-id="986ae-105">Вы добавите функцию проверки подлинности службы приложений, чтобы обеспечить защиту API-интерфейсов серверной части веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="986ae-105">You'll add App Service Authentication to protect the backend APIs of your web app.</span></span>

## <a name="enable-app-service-authentication"></a><span data-ttu-id="986ae-106">Включение функции проверки подлинности службы приложений</span><span class="sxs-lookup"><span data-stu-id="986ae-106">Enable App Service Authentication</span></span>

1. <span data-ttu-id="986ae-107">Откройте приложение-функцию на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="986ae-107">Open the function app in the Azure Portal.</span></span>

1. <span data-ttu-id="986ae-108">В разделе **Функции платформы** выберите **Проверка подлинности/авторизация**.</span><span class="sxs-lookup"><span data-stu-id="986ae-108">Under **Platform features**, select **Authentication/Authorization**.</span></span>

    ![Выбор пункта "Проверка подлинности/авторизация"](media/functions-first-serverless-web-app/6-authorization.jpg)

1. <span data-ttu-id="986ae-110">Выберите следующие значения:</span><span class="sxs-lookup"><span data-stu-id="986ae-110">Select the following values:</span></span>
    
    | <span data-ttu-id="986ae-111">Параметр</span><span class="sxs-lookup"><span data-stu-id="986ae-111">Setting</span></span>      |  <span data-ttu-id="986ae-112">Рекомендуемое значение</span><span class="sxs-lookup"><span data-stu-id="986ae-112">Suggested value</span></span>   | <span data-ttu-id="986ae-113">ОПИСАНИЕ</span><span class="sxs-lookup"><span data-stu-id="986ae-113">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="986ae-114">**Проверка подлинности службы приложений**</span><span class="sxs-lookup"><span data-stu-id="986ae-114">**App Service Authentication**</span></span> | <span data-ttu-id="986ae-115">С</span><span class="sxs-lookup"><span data-stu-id="986ae-115">On</span></span> | <span data-ttu-id="986ae-116">Включение проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="986ae-116">Enable authentication.</span></span> |
    | <span data-ttu-id="986ae-117">**Предпринимаемое действие, если проверка подлинности для запроса не выполнена**</span><span class="sxs-lookup"><span data-stu-id="986ae-117">**Action when request is not authenticated**</span></span> | <span data-ttu-id="986ae-118">Вход с использованием Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="986ae-118">Log in with Azure Active Directory</span></span> | <span data-ttu-id="986ae-119">Выберите настроенный метод проверки подлинности (см. ниже).</span><span class="sxs-lookup"><span data-stu-id="986ae-119">Select a configured authentication method (below).</span></span> |
    | <span data-ttu-id="986ae-120">**Поставщики проверки подлинности**</span><span class="sxs-lookup"><span data-stu-id="986ae-120">**Authentication Providers**</span></span> | <span data-ttu-id="986ae-121">См. ниже</span><span class="sxs-lookup"><span data-stu-id="986ae-121">See below</span></span> | <span data-ttu-id="986ae-122">См. ниже</span><span class="sxs-lookup"><span data-stu-id="986ae-122">See below</span></span> |
    | <span data-ttu-id="986ae-123">**Хранилище токенов**</span><span class="sxs-lookup"><span data-stu-id="986ae-123">**Token store**</span></span> | <span data-ttu-id="986ae-124">С</span><span class="sxs-lookup"><span data-stu-id="986ae-124">On</span></span> | <span data-ttu-id="986ae-125">Разрешение сохранять токены и управлять ими в службе приложений.</span><span class="sxs-lookup"><span data-stu-id="986ae-125">Allow App Service to store and manage tokens.</span></span> |
    | <span data-ttu-id="986ae-126">**Разрешенные URL-адреса внешнего перенаправления**</span><span class="sxs-lookup"><span data-stu-id="986ae-126">**Allowed external redirect URLs**</span></span> | <span data-ttu-id="986ae-127">URL-адрес приложения, например https://firstserverlessweb.z4.web.core.windows.net/.</span><span class="sxs-lookup"><span data-stu-id="986ae-127">The URL of your application, for example: https://firstserverlessweb.z4.web.core.windows.net/</span></span> | <span data-ttu-id="986ae-128">URL-адреса, по которым службе приложений разрешено перенаправить пользователя, если он прошел проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="986ae-128">URL(s) that App Service is allowed to redirect to after a user is authenticated.</span></span> |

1. <span data-ttu-id="986ae-129">Выберите **Azure Active Directory**, чтобы открыть панель **параметров Azure Active Directory**.</span><span class="sxs-lookup"><span data-stu-id="986ae-129">Select **Azure Active Directory** to reveal **Azure Active Directory Settings**.</span></span>

    1. <span data-ttu-id="986ae-130">Выберите **Экспресс** в качестве **режима управления** и укажите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="986ae-130">Select **Express** as the **Management Mode** and fill in the following information.</span></span>
    
        | <span data-ttu-id="986ae-131">Параметр</span><span class="sxs-lookup"><span data-stu-id="986ae-131">Setting</span></span>      |  <span data-ttu-id="986ae-132">Рекомендуемое значение</span><span class="sxs-lookup"><span data-stu-id="986ae-132">Suggested value</span></span>   | <span data-ttu-id="986ae-133">ОПИСАНИЕ</span><span class="sxs-lookup"><span data-stu-id="986ae-133">Description</span></span>                                        |
        | --- | --- | ---|
        | <span data-ttu-id="986ae-134">**Режим управления**</span><span class="sxs-lookup"><span data-stu-id="986ae-134">**Management mode**</span></span> | <span data-ttu-id="986ae-135">Элементы "Экспресс" и "Создать новое приложение AD"</span><span class="sxs-lookup"><span data-stu-id="986ae-135">Express, Create new AD app</span></span> | <span data-ttu-id="986ae-136">Обеспечивают автоматическую настройку субъекта-службы и проверки подлинности Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="986ae-136">Automatically set up a service principal and Azure Active Directory authentication.</span></span> |
        | <span data-ttu-id="986ae-137">**Создание приложения**</span><span class="sxs-lookup"><span data-stu-id="986ae-137">**Create app**</span></span> | <span data-ttu-id="986ae-138">my-serverless-webapp</span><span class="sxs-lookup"><span data-stu-id="986ae-138">my-serverless-webapp</span></span> | <span data-ttu-id="986ae-139">Введите уникальное имя приложения.</span><span class="sxs-lookup"><span data-stu-id="986ae-139">Enter a unique application name.</span></span> |
    
    1. <span data-ttu-id="986ae-140">Нажмите кнопку **ОК**, чтобы сохранить параметры Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="986ae-140">Click **OK** to save the Azure Active Directory settings.</span></span>

    ![Элемент "Проверка подлинности/авторизация" и параметры Azure Active Directory](media/functions-first-serverless-web-app/6-create-aad.png)

1. <span data-ttu-id="986ae-142">Выберите команду **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="986ae-142">Click **Save**.</span></span>


## <a name="modify-the-web-app-to-enable-authentication"></a><span data-ttu-id="986ae-143">Изменение веб-приложения для включения проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="986ae-143">Modify the web app to enable authentication</span></span>

1. <span data-ttu-id="986ae-144">В Cloud Shell убедитесь, что в качестве текущего каталога используется папка **www/dist**.</span><span class="sxs-lookup"><span data-stu-id="986ae-144">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="986ae-145">Чтобы включить проверку подлинности в приложении-функции, добавьте следующую строку кода в файл **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="986ae-145">To enable authentication in your function app, append the following line of code to **settings.js**.</span></span>

    `window.authEnabled = true`

    <span data-ttu-id="986ae-146">Внесите изменения и просмотрите результаты, выполнив приведенные ниже команды или используя редактор командной строки, например VIM.</span><span class="sxs-lookup"><span data-stu-id="986ae-146">Make the change and view the result by using the following commands or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.authEnabled = true" >> settings.js
    ```

    <span data-ttu-id="986ae-147">Убедитесь, что изменения внесены в файл.</span><span class="sxs-lookup"><span data-stu-id="986ae-147">Confirm the change was made to the file.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="986ae-148">Отправьте файл в хранилище BLOB-объектов.</span><span class="sxs-lookup"><span data-stu-id="986ae-148">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-application"></a><span data-ttu-id="986ae-149">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="986ae-149">Test the application</span></span>

1. <span data-ttu-id="986ae-150">Откройте приложение в браузере.</span><span class="sxs-lookup"><span data-stu-id="986ae-150">Open the application in a browser.</span></span> <span data-ttu-id="986ae-151">Нажмите кнопку **входа** и войдите в систему.</span><span class="sxs-lookup"><span data-stu-id="986ae-151">Click **Log in** and log in.</span></span>

1. <span data-ttu-id="986ae-152">Выберите файл изображения и отправьте его.</span><span class="sxs-lookup"><span data-stu-id="986ae-152">Select an image file and upload it.</span></span>

    ![Страница входа](media/functions-first-serverless-web-app/6-aad-auth.png)
    

## <a name="summary"></a><span data-ttu-id="986ae-154">Сводка</span><span class="sxs-lookup"><span data-stu-id="986ae-154">Summary</span></span>

<span data-ttu-id="986ae-155">Из этого модуля вы узнали, как добавить проверку подлинности в приложение, используя соответствующую функцию Службы приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="986ae-155">In this module, you learned how to add authentication to the applcation using Azure App Service Authentication.</span></span>
