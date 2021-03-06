Хранилище BLOB-объектов — это недорогая служба с высокой масштабируемостью, которую можно использовать для размещения статических файлов. В рамках нашего руководства вы будете использовать эту службу, чтобы обслуживать статическое содержимое (например, HTML, JavaScript или каскадные таблицы стилей) для создаваемого веб-приложения.

## <a name="create-a-storage-account"></a>Создание учетной записи хранения

Учетная запись хранения — это ресурс Azure, который позволяет хранить таблицы, очереди, файлы, BLOB-объекты и диски виртуальных машин.

1. Войдите в Cloud Shell (Bash), нажав кнопку **Enter focus mode** (Перейти в режим фокусировки). Эта кнопка находится в правой верхней или нижней части страницы в зависимости от ширины окна браузера. В режиме фокусировки окно Cloud Shell закреплено в правой части окна браузера, что позволяет легко выполнять команды, приведенные в этом руководстве.

1. В Azure группа ресурсов представляет собой контейнер, содержащий связанные ресурсы Azure для простоты управления. Создайте группу ресурсов с именем **first-serverless-app**.

    ```azurecli
    az group create -n first-serverless-app -l westcentralus
    ```

1. В рамках этого руководства статическое содержимое (файлы HTML, JavaScript и каскадных таблиц стилей) размещено в хранилище BLOB-объектов. Для использования хранилища BLOB-объектов требуется учетная запись хранения. Создайте учетную запись хранения (общего назначения версии 2) в группе ресурсов. Замените `<storage account name>` уникальным именем.

    ```azurecli
    az storage account create -n <storage account name> -g first-serverless-app --kind StorageV2 -l westcentralus --https-only true --sku Standard_LRS
    ```

1. Используя строку поиска в верхней части окна [портала Azure](https://portal.azure.com), найдите и откройте только что созданную учетную запись хранения.

1. На панели навигации слева выберите **Статический веб-сайт (предварительная версия)**, чтобы настроить контейнер для размещения статического веб-сайта.
    - Выберите **Включено**, чтобы включить статический веб-сайт.
    - В качестве имени документа индекса введите *index.html*. В поле уже указано значение *index.html* серым шрифтом, но это только пример текста. Вам все же нужно ввести это значение в поле.
    - Выберите команду **Сохранить**.
    
    ![Ввод параметров статического веб-сайта](media/functions-first-serverless-web-app/1-storage-static-website.png)

1. Сохраните **первичную конечную точку** в любом расположении, откуда вы сможете ее легко скопировать во время работы с этим руководством. Это URL-адрес веб-приложения.

## <a name="upload-the-web-application"></a>Отправка веб-приложения

1. Исходные файлы для приложения, создаваемого в рамках этого руководства, находятся в [репозитории GitHub](https://github.com/Azure-Samples/functions-first-serverless-web-application). В Cloud Shell перейдите в корневой каталог и клонируйте этот репозиторий.

    ```azurecli
    cd ~
    git clone https://github.com/Azure-Samples/functions-first-serverless-web-application
    ```

    Репозиторий клонируется в каталог `/home/<username>/functions-first-serverless-web-application`.

1. Клиентское веб-приложение находится в папке **www** и создается с помощью платформы JavaScript Vue.js. Откройте эту папку и выполните команды npm, чтобы установить зависимости приложения и создать приложение. Выполнение этих команд может занять несколько минут.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www
    npm install
    npm run generate
    ```

    Приложение создается в папке **dist**.

1. Откройте каталог на **dist** и отправьте приложение в контейнер больших двоичных объектов **$web**.

    ```azurecli
    cd dist
    az storage blob upload-batch -s . -d \$web --account-name <storage account name>
    ```

1. В веб-браузере откройте URL-адрес первичной конечной точки для статических веб-сайтов службы хранилища, чтобы просмотреть приложение.

    ![Домашняя страница первого бессерверного веб-приложения](media/functions-first-serverless-web-app/1-app-screenshot-new.png)


## <a name="summary"></a>Сводка

В рамках этого модуля вы создали группу ресурсов с именем **first-serverless-app**, которая содержит учетную запись хранения. В контейнере больших двоичных объектов с именем **$web** в учетной записи хранения хранится статическое содержимое для веб-приложения. Это содержимое общедоступно. Далее вы узнаете, как с помощью бессерверной функции отправлять изображения из этого веб-приложения в хранилище BLOB-объектов.
