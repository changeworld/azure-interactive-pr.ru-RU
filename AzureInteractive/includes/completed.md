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
Вы успешно создали это полнофункциональное бессерверное приложение с помощью служб Azure.

## <a name="clean-up-resources"></a>Очистка ресурсов

Когда закончите работу с этим приложением, можете удалить все ресурсы, созданные в рамках этого руководства, с помощью следующей команды:

```azurecli
az group delete --name first-serverless-app
```

При появлении запроса введите `y`.  

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:
> [!div class="checklist"]
> * Настройка хранилища BLOB-объектов Azure для размещения статического веб-сайта и отправляемых изображений.
> * Отправка изображений в хранилище BLOB-объектов Azure с помощью службы "Функции Azure".
> * Изменение размера изображений с помощью службы "Функции Azure".
> * Хранение метаданных изображений в Azure Cosmos DB.
> * Использование API компьютерного зрения Cognitive Services для автоматического создания подписей изображений.
> * Использование Azure Active Directory для защиты веб-приложения путем проверки подлинности пользователей.

Чтобы узнать, как подключить больше служб к службе "Функции", перейдите к руководству по Logic Apps. 

> [!div class="nextstepaction"]
> [Использование службы "Функции" с Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)