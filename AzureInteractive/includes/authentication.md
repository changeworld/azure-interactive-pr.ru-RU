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
Функция проверки подлинности Службы приложений Azure обеспечивает полную поддержку проверки подлинности в приложении-функции Azure. Эта функция легко интегрируется с Facebook, Twitter, учетной записью Майкрософт, Google и Azure Active Directory. Вы добавите функцию проверки подлинности службы приложений, чтобы обеспечить защиту API-интерфейсов серверной части веб-приложения.

## <a name="enable-app-service-authentication"></a>Включение функции проверки подлинности службы приложений

1. Откройте приложение-функцию на портале Azure.

1. В разделе **Функции платформы** выберите **Проверка подлинности/авторизация**.

    ![Выбор пункта "Проверка подлинности/авторизация"](media/functions-first-serverless-web-app/6-authorization.jpg)

1. Выберите следующие значения:
    
    | Параметр      |  Рекомендуемое значение   | ОПИСАНИЕ                                        |
    | --- | --- | ---|
    | **Проверка подлинности службы приложений** | С | Включение проверки подлинности. |
    | **Предпринимаемое действие, если проверка подлинности для запроса не выполнена** | Вход с использованием Azure Active Directory | Выберите настроенный метод проверки подлинности (см. ниже). |
    | **Поставщики проверки подлинности** | См. ниже | См. ниже |
    | **Хранилище токенов** | С | Разрешение сохранять токены и управлять ими в службе приложений. |
    | **Разрешенные URL-адреса внешнего перенаправления** | URL-адрес приложения, например https://firstserverlessweb.z4.web.core.windows.net/. | URL-адреса, по которым службе приложений разрешено перенаправить пользователя, если он прошел проверку подлинности. |

1. Выберите **Azure Active Directory**, чтобы открыть панель **параметров Azure Active Directory**.

    1. Выберите **Экспресс** в качестве **режима управления** и укажите следующие сведения:
    
        | Параметр      |  Рекомендуемое значение   | ОПИСАНИЕ                                        |
        | --- | --- | ---|
        | **Режим управления** | Элементы "Экспресс" и "Создать новое приложение AD" | Обеспечивают автоматическую настройку субъекта-службы и проверки подлинности Azure Active Directory. |
        | **Создание приложения** | my-serverless-webapp | Введите уникальное имя приложения. |
    
    1. Нажмите кнопку **ОК**, чтобы сохранить параметры Azure Active Directory.

    ![Элемент "Проверка подлинности/авторизация" и параметры Azure Active Directory](media/functions-first-serverless-web-app/6-create-aad.png)

1. Выберите команду **Сохранить**.


## <a name="modify-the-web-app-to-enable-authentication"></a>Изменение веб-приложения для включения проверки подлинности

1. В Cloud Shell убедитесь, что в качестве текущего каталога используется папка **www/dist**.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. Чтобы включить проверку подлинности в приложении-функции, добавьте следующую строку кода в файл **settings.js**.

    `window.authEnabled = true`

    Внесите изменения и просмотрите результаты, выполнив приведенные ниже команды или используя редактор командной строки, например VIM.

    ```azurecli
    echo "window.authEnabled = true" >> settings.js
    ```

    Убедитесь, что изменения внесены в файл.

    ```azurecli
    cat settings.js
    ```

1. Отправьте файл в хранилище BLOB-объектов.

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-application"></a>Тестирование приложения

1. Откройте приложение в браузере. Нажмите кнопку **входа** и войдите в систему.

1. Выберите файл изображения и отправьте его.

    ![Страница входа](media/functions-first-serverless-web-app/6-aad-auth.png)
    

## <a name="summary"></a>Сводка

Из этого модуля вы узнали, как добавить проверку подлинности в приложение, используя соответствующую функцию Службы приложений Azure.
