---
title: включение файла
description: включение файла
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/25/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a66fcc3a406c79fcf9881ddaaaf8330f5b373043
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460009"
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
