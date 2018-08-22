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
В рамках этого руководства вы развернете простое веб-приложение, которое представляет собой пользовательский интерфейс на основе HTML. Благодаря бессерверной внутренней части приложение может отправлять изображения и автоматически получать подписи с описанием.

![Запуск веб-приложения](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

На представленной ниже схеме показаны службы Azure, используемые приложением:

1. В хранилище BLOB-объектов обслуживается статическое веб-содержимое (HTML, каскадные таблицы стилей и JS) и хранятся изображения.
2. Служба "Функции Azure" управляет отправкой изображений, изменением их размера и хранением метаданных.
3. В Cosmos DB хранятся метаданные изображений.
4. Logic Apps получает подписи изображений из API компьютерного зрения.
5. Azure Active Directory управляет проверкой подлинности пользователей.

![Схема архитектуры решения](media/functions-first-serverless-web-app/0-architecture.jpg)

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * Настройка хранилища BLOB-объектов Azure для размещения статического веб-сайта и отправляемых изображений.
> * Отправка изображений в хранилище BLOB-объектов Azure с помощью службы "Функции Azure".
> * Изменение размера изображений с помощью службы "Функции Azure".
> * Хранение метаданных изображений в Azure Cosmos DB.
> * Использование API компьютерного зрения Cognitive Services для автоматического создания подписей изображений.
> * Использование Azure Active Directory для защиты веб-приложения путем проверки подлинности пользователей.