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
На этом этапе приложение представляет собой коллекцию функций, которая позволяет отправлять и просматривать изображения. Из этого модуля вы узнаете, как с помощью API компьютерного зрения из Microsoft Cognitive Services создавать подписи для отправляемых изображений и сохранять эти подписи с метаданными изображения в Cosmos DB.

## <a name="create-a-computer-vision-account"></a>Создание учетной записи API компьютерного зрения

Microsoft Cognitive Services — это набор служб, которые помогают разработчикам сделать свои приложения более интеллектуальными. API компьютерного зрения — это бессерверная служба, которая с помощью усовершенствованных алгоритмов обрабатывает изображения и возвращает сведения о каждом из них. Есть бесплатный уровень службы, на котором предоставляется 5000 вызовов API в месяц.

1. Убедитесь, что окно Cloud Shell открыто. Если вы уже вышли, выберите **Enter focus mode** (Перейти в режим фокусировки), чтобы открыть окно Cloud Shell. 

1. Создайте учетную запись Cognitive Services с типом **ComputerVision** и уникальным именем в группе ресурсов. Для уровня "Бесплатный" укажите **F0** в качестве номера SKU. Если у вас уже есть учетная запись API компьютерного зрения, возможно, потребуется создать учетную запись ценовой категории "Стандартный" (S1), что может повлечь за собой некоторые расходы.

    ```azurecli
    az cognitiveservices account create -g first-serverless-app -n <computer vision account name> --kind ComputerVision --sku F0 -l westcentralus
    ```


## <a name="create-function-app-settings-for-computer-vision-url-and-key"></a>Создание параметров приложения-функции для URL-адреса и ключа API компьютерного зрения

Чтобы вызвать API компьютерного зрения, вам потребуется URL-адрес и ключ.

1. Получите URL-адрес и ключ API компьютерного зрения и сохраните их в переменных bash.

    ```azurecli
    export COMP_VISION_KEY=$(az cognitiveservices account keys list -g first-serverless-app -n <computer vision account name> --query key1 --output tsv)
    ```
    ```azurecli
    export COMP_VISION_URL=$(az cognitiveservices account show -g first-serverless-app -n <computer vision account name> --query endpoint --output tsv)
    ```

1. В приложении-функции создайте параметры приложения с именами **COMP_VISION_KEY** и **COMP_VISION_URL** соответственно.

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings COMP_VISION_KEY=$COMP_VISION_KEY COMP_VISION_URL=$COMP_VISION_URL -o table
    ```


## <a name="call-computer-vision-api-from-resizeimage-function"></a>Вызов API компьютерного зрения из функции ResizeImage

На следующих этапах вы измените функцию **ResizeImage**, чтобы она вызывала API компьютерного зрения для описания каждого отправленного изображения и сохранения этого описания в Cosmos DB.

1. Откройте приложение-функцию на портале Azure.

1. **C#**

    1. (C#) С помощью области навигации слева найдите функцию **ResizeImage** и откройте окно с ее кодом.

    1. (C#) Замените код содержимым из репозитория [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx). В этом коде используется `HttpClient`, чтобы вызывать API компьютерного зрения и сохранять результат в Cosmos DB.

1. **JavaScript**

    1. (JavaScript) Чтобы эта функция могла выполнить вызов HTTP к API компьютерного зрения, ей требуется пакет `axios` из npm. Чтобы установить пакет npm, щелкните имя приложения-функции в области навигации слева и выберите **Функции платформы**.

    1. (JavaScript) Чтобы открыть окно консоли, щелкните **Консоль**.

    1. (JavaScript) В консоли выполните команду `npm install axios`. Выполнение операции может занять одну-две минуты.

    1. (JavaScript) Щелкните имя функции (**ResizeImage**) в области навигации слева, чтобы открыть окно с ее кодом. Замените содержимое файла **index.js** содержимым из репозитория [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).

1. Щелкните **Журналы** под окном кода, чтобы развернуть панель журналов.

1. Выберите команду **Сохранить**. Просмотрите записи на панели журналов и проверьте, успешно ли сохранена функция и нет ли ошибок.


## <a name="test-the-application"></a>Тестирование приложения

1. Откройте приложение в браузере. Выберите файл изображения и отправьте его.

1. Через несколько секунд на странице должен появиться эскиз нового изображения. Наведите указатель мыши на изображение, чтобы отобразилось описание, созданное с помощью API компьютерного зрения.


## <a name="summary"></a>Сводка

Из этого модуля вы узнали, как с помощью API компьютерного зрения Microsoft Cognitive Services автоматически создавать подписи для каждого отправленного изображения. Далее вы узнаете, как добавить проверку подлинности в приложение, используя соответствующую функцию Службы приложений Azure.
