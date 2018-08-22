<span data-ttu-id="79fcb-101">Хранилище BLOB-объектов — это недорогая служба с высокой масштабируемостью, которую можно использовать для размещения статических файлов.</span><span class="sxs-lookup"><span data-stu-id="79fcb-101">Azure Blob storage is a low-cost and massively scalable service that can be used to host static files.</span></span> <span data-ttu-id="79fcb-102">В рамках нашего руководства вы будете использовать эту службу, чтобы обслуживать статическое содержимое (например, HTML, JavaScript или каскадные таблицы стилей) для создаваемого веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="79fcb-102">For this tutorial, you use it to serve static content (for example, HTML, JavaScript, CSS) for the web app that you build.</span></span>

## <a name="create-a-storage-account"></a><span data-ttu-id="79fcb-103">Создание учетной записи хранения</span><span class="sxs-lookup"><span data-stu-id="79fcb-103">Create a Storage account</span></span>

<span data-ttu-id="79fcb-104">Учетная запись хранения — это ресурс Azure, который позволяет хранить таблицы, очереди, файлы, BLOB-объекты и диски виртуальных машин.</span><span class="sxs-lookup"><span data-stu-id="79fcb-104">A Storage account is an Azure resource that allows you to store tables, queues, files, blobs (objects), and virtual machine disks.</span></span>

1. <span data-ttu-id="79fcb-105">Войдите в Cloud Shell (Bash), нажав кнопку **Enter focus mode** (Перейти в режим фокусировки).</span><span class="sxs-lookup"><span data-stu-id="79fcb-105">Log in to the Cloud Shell (Bash), by selecting the **Enter focus mode** button.</span></span> <span data-ttu-id="79fcb-106">Эта кнопка находится в правой верхней или нижней части страницы в зависимости от ширины окна браузера.</span><span class="sxs-lookup"><span data-stu-id="79fcb-106">This button is at the top right or the bottom of the page, depending on how wide your browser window is.</span></span> <span data-ttu-id="79fcb-107">В режиме фокусировки окно Cloud Shell закреплено в правой части окна браузера, что позволяет легко выполнять команды, приведенные в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="79fcb-107">Focus mode docks a Cloud Shell window on the right side of your browser window, so you can easily execute commands that are shown in the tutorial.</span></span>

1. <span data-ttu-id="79fcb-108">В Azure группа ресурсов представляет собой контейнер, содержащий связанные ресурсы Azure для простоты управления.</span><span class="sxs-lookup"><span data-stu-id="79fcb-108">In Azure, a Resource Group is a container that holds related Azure resources for ease of management.</span></span> <span data-ttu-id="79fcb-109">Создайте группу ресурсов с именем **first-serverless-app**.</span><span class="sxs-lookup"><span data-stu-id="79fcb-109">Create a new resource group named **first-serverless-app**.</span></span>

    ```azurecli
    az group create -n first-serverless-app -l westcentralus
    ```

1. <span data-ttu-id="79fcb-110">В рамках этого руководства статическое содержимое (файлы HTML, JavaScript и каскадных таблиц стилей) размещено в хранилище BLOB-объектов.</span><span class="sxs-lookup"><span data-stu-id="79fcb-110">The static content (HTML, CSS, and JavaScript files) for this tutorial is hosted in Blob Storage.</span></span> <span data-ttu-id="79fcb-111">Для использования хранилища BLOB-объектов требуется учетная запись хранения.</span><span class="sxs-lookup"><span data-stu-id="79fcb-111">Blob Storage requires a Storage account.</span></span> <span data-ttu-id="79fcb-112">Создайте учетную запись хранения (общего назначения версии 2) в группе ресурсов.</span><span class="sxs-lookup"><span data-stu-id="79fcb-112">Create a Storage account (general purpose V2) in the resource group.</span></span> <span data-ttu-id="79fcb-113">Замените `<storage account name>` уникальным именем.</span><span class="sxs-lookup"><span data-stu-id="79fcb-113">Replace `<storage account name>` with a unique name.</span></span>

    ```azurecli
    az storage account create -n <storage account name> -g first-serverless-app --kind StorageV2 -l westcentralus --https-only true --sku Standard_LRS
    ```

1. <span data-ttu-id="79fcb-114">Используя строку поиска в верхней части окна [портала Azure](https://portal.azure.com), найдите и откройте только что созданную учетную запись хранения.</span><span class="sxs-lookup"><span data-stu-id="79fcb-114">Using the Search bar at the top of the [Azure portal](https://portal.azure.com), find the storage account you just created and open it.</span></span>

1. <span data-ttu-id="79fcb-115">На панели навигации слева выберите **Статический веб-сайт (предварительная версия)**, чтобы настроить контейнер для размещения статического веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="79fcb-115">On the left navigation, select **Static website (preview)** to configure a container for static website hosting.</span></span>
    - <span data-ttu-id="79fcb-116">Выберите **Включено**, чтобы включить статический веб-сайт.</span><span class="sxs-lookup"><span data-stu-id="79fcb-116">Select **Enabled** to enable static website.</span></span>
    - <span data-ttu-id="79fcb-117">В качестве имени документа индекса введите *index.html*.</span><span class="sxs-lookup"><span data-stu-id="79fcb-117">Enter *index.html* as the index document name.</span></span> <span data-ttu-id="79fcb-118">В поле уже указано значение *index.html* серым шрифтом, но это только пример текста. Вам все же нужно ввести это значение в поле.</span><span class="sxs-lookup"><span data-stu-id="79fcb-118">The field already has *index.html* in a gray font but this is only example text; you still have to enter that value in the field.</span></span>
    - <span data-ttu-id="79fcb-119">Выберите команду **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="79fcb-119">Click **Save**.</span></span>
    
    ![Ввод параметров статического веб-сайта](media/functions-first-serverless-web-app/1-storage-static-website.png)

1. <span data-ttu-id="79fcb-121">Сохраните **первичную конечную точку** в любом расположении, откуда вы сможете ее легко скопировать во время работы с этим руководством.</span><span class="sxs-lookup"><span data-stu-id="79fcb-121">Save the **Primary Endpoint** somewhere you can conveniently copy it from while working through the tutorial.</span></span> <span data-ttu-id="79fcb-122">Это URL-адрес веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="79fcb-122">This is the URL of your web application.</span></span>

## <a name="upload-the-web-application"></a><span data-ttu-id="79fcb-123">Отправка веб-приложения</span><span class="sxs-lookup"><span data-stu-id="79fcb-123">Upload the web application</span></span>

1. <span data-ttu-id="79fcb-124">Исходные файлы для приложения, создаваемого в рамках этого руководства, находятся в [репозитории GitHub](https://github.com/Azure-Samples/functions-first-serverless-web-application).</span><span class="sxs-lookup"><span data-stu-id="79fcb-124">The source files for the application that you build in this tutorial are located in a [GitHub repository](https://github.com/Azure-Samples/functions-first-serverless-web-application).</span></span> <span data-ttu-id="79fcb-125">В Cloud Shell перейдите в корневой каталог и клонируйте этот репозиторий.</span><span class="sxs-lookup"><span data-stu-id="79fcb-125">Make sure that you are in your home directory in Cloud Shell and clone this repository.</span></span>

    ```azurecli
    cd ~
    git clone https://github.com/Azure-Samples/functions-first-serverless-web-application
    ```

    <span data-ttu-id="79fcb-126">Репозиторий клонируется в каталог `/home/<username>/functions-first-serverless-web-application`.</span><span class="sxs-lookup"><span data-stu-id="79fcb-126">The repository is cloned to `/home/<username>/functions-first-serverless-web-application`.</span></span>

1. <span data-ttu-id="79fcb-127">Клиентское веб-приложение находится в папке **www** и создается с помощью платформы JavaScript Vue.js.</span><span class="sxs-lookup"><span data-stu-id="79fcb-127">The client-side web application is located in the **www** folder and is built using the Vue.js JavaScript framework.</span></span> <span data-ttu-id="79fcb-128">Откройте эту папку и выполните команды npm, чтобы установить зависимости приложения и создать приложение.</span><span class="sxs-lookup"><span data-stu-id="79fcb-128">Change into the folder and run npm commands to install the application's dependencies and build the application.</span></span> <span data-ttu-id="79fcb-129">Выполнение этих команд может занять несколько минут.</span><span class="sxs-lookup"><span data-stu-id="79fcb-129">The last of these commands might take several minutes to complete.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www
    npm install
    npm run generate
    ```

    <span data-ttu-id="79fcb-130">Приложение создается в папке **dist**.</span><span class="sxs-lookup"><span data-stu-id="79fcb-130">The application is generated in the **dist** folder.</span></span>

1. <span data-ttu-id="79fcb-131">Откройте каталог на **dist** и отправьте приложение в контейнер больших двоичных объектов **$web**.</span><span class="sxs-lookup"><span data-stu-id="79fcb-131">Change the current directory to **dist** and upload the application to the **$web** Blob container.</span></span>

    ```azurecli
    cd dist
    az storage blob upload-batch -s . -d \$web --account-name <storage account name>
    ```

1. <span data-ttu-id="79fcb-132">В веб-браузере откройте URL-адрес первичной конечной точки для статических веб-сайтов службы хранилища, чтобы просмотреть приложение.</span><span class="sxs-lookup"><span data-stu-id="79fcb-132">Open the Storage static websites primary endpoint URL in a web browser to view the application.</span></span>

    ![Домашняя страница первого бессерверного веб-приложения](media/functions-first-serverless-web-app/1-app-screenshot-new.png)


## <a name="summary"></a><span data-ttu-id="79fcb-134">Сводка</span><span class="sxs-lookup"><span data-stu-id="79fcb-134">Summary</span></span>

<span data-ttu-id="79fcb-135">В рамках этого модуля вы создали группу ресурсов с именем **first-serverless-app**, которая содержит учетную запись хранения.</span><span class="sxs-lookup"><span data-stu-id="79fcb-135">In this module, you created a resource group named **first-serverless-app** containing a Storage account.</span></span> <span data-ttu-id="79fcb-136">В контейнере больших двоичных объектов с именем **$web** в учетной записи хранения хранится статическое содержимое для веб-приложения. Это содержимое общедоступно.</span><span class="sxs-lookup"><span data-stu-id="79fcb-136">A blob container named **$web** in the Storage account stores the static content for your web application and makes the content available publicly.</span></span> <span data-ttu-id="79fcb-137">Далее вы узнаете, как с помощью бессерверной функции отправлять изображения из этого веб-приложения в хранилище BLOB-объектов.</span><span class="sxs-lookup"><span data-stu-id="79fcb-137">Next, you learn how to use a serverless function to upload images to Blob storage from this web application.</span></span>
