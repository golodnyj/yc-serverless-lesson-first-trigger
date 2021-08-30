# Создание Функции
## Создание функции

В предыдущем уроке мы создавали функцию с именем `my-first-function` с помощью команды: 

    yc serverless function create --name my-first-function

При создании функции вы получали URL по которому можно будет сделать вызов функции `http_invoke_url`. По умолчанию функция не является публичной.

## Загрузка кода новой версии

Наша новая версия функции имеет зависимости, которые описаны в файле `requirements.txt`, а это значит что для загрузки функции в облако необходимо  файлы `index.py` и `requirements.txt` заархивировать и получить файл `my-first-function.zip`. Находясь в каталоге с файлом `my-first-function.zip` вызовите следующую команду, это позволит вам загрузите код функции в облако и создать ее версию:

    yc serverless function version create \
    --function-name my-first-function \
    --memory 256m \
    --execution-timeout 5s \
    --runtime python37 \
    --entrypoint index.handler \
    --service-account-id $SERVICE_ACCOUNT_ID \
    --source-path my-first-function.zip 

Новая версия функции при вызове будет загружать в `Object Storage` новый файл. Для создания этой новой версии функции необходимо, подготовить переменные. Переменные `ACCESS_KEY` и `SECRET_KEY` были получены на шаге один, а значение `BUCKET_NAME` на шаге номер два:

    echo "export ACCESS_KEY=<ACCESS_KEY>" >> ~/.bashrc && . ~/.bashrc
    echo "export SECRET_KEY=<SECRET_KEY>" >> ~/.bashrc && . ~/.bashrc
    echo "export BUCKET_NAME=bucket-for-trigger" >> ~/.bashrc && . ~/.bashrc

Определим значение ID для последней загруженной версии функции:

    yc serverless function version list --function-name my-first-function

Создадим новую версию функции, задав при этом переменные окружения, для этого выставим значение переменной `source-version-id` равному полученному `ID` в следующей команде:

    yc serverless function version create \
    --function-name my-first-function          \
    --memory 256m                     \
    --execution-timeout 5s            \
    --runtime python37                \
    --entrypoint index.handler     \
    --service-account-id $SERVICE_ACCOUNT_ID     \
    --source-version-id <ID> \
    --environment ACCESS_KEY=$ACCESS_KEY \
    --environment SECRET_KEY=$SECRET_KEY \
    --environment BUCKET_NAME=$BUCKET_NAME
    
Успешное выполнение команды приведет к созданию версии функции.

## Вызов функции

Получите список функций и получите информацию о функции `my-first-function`:

    yc serverless function list
    yc serverless function version list --function-name my-first-function

В результате вызова последней команды в столбце `FUNCTION ID` вы узнаете идентификатор функции и сможете сделать вызов функции с помощью следующей команды:

    yc serverless function invoke <идентификатор функции>

По умолчанию функция создается не публичной. Чтобы сделать функцию `my-first-function` публичной, вызовете следующую команду: 
    
    yc serverless function allow-unauthenticated-invoke my-first-function

После этого вы можете сделать ее вызов в браузере. Получите параметр `http_invoke_url` для функции `my-first-function`

    yc serverless function get my-first-function

Введите значение параметра `http_invoke_url` в браузере и наслаждайтесь вызовом вашей функции. Во время ее вызова в `Object Storage` будет создан новый файл.