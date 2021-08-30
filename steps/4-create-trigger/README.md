# Создание Триггера
## Создание функции

Для создания триггера нам необходима функция, которую триггер будет запускать. Аналогично предыдущему шагу создадим функцию `my-trigger-function` и ее версию на основе файла `index.py`. 
    
    yc serverless function create --name my-trigger-function

    yc serverless function version create \
    --function-name my-trigger-function \
    --memory 256m \
    --execution-timeout 5s \
    --runtime python37 \
    --entrypoint index.handler \
    --service-account-id $SERVICE_ACCOUNT_ID \
    --source-path index.py

    yc serverless function version list --function-name my-trigger-function

## Создание триггера

Чтобы создать триггер `my-first-trigger`, который вызывает функцию `my-trigger-function` при создании нового объекта в бакете `BUCKET_NAME` выполните команду:

    yc serverless trigger create object-storage \
    --name my-first-trigger \
    --bucket-id $BUCKET_NAME \
    --events 'create-object' \
    --invoke-function-name my-trigger-function \
    --invoke-function-service-account-id $SERVICE_ACCOUNT_ID

## Вызов цепочки событий

Чтобы запустить цепочку событий вызовем первую функцию `my-first-function`. Получите список функций и получите информацию о функции `my-first-function`:

    yc serverless function list
    yc serverless function version list --function-name my-first-function

В результате вызова последней команды в столбце `FUNCTION ID` вы узнаете идентификатор функции и сможете сделать вызов функции с помощью следующей команды:

    yc serverless function invoke <идентификатор функции>

По умолчанию функция создается не публичной. Чтобы сделать функцию `my-first-function` публичной, вызовете следующую команду: 
    
    yc serverless function allow-unauthenticated-invoke my-first-function

После этого вы можете сделать ее вызов в браузере. Получите параметр `http_invoke_url` для функции `my-first-function`

    yc serverless function get my-first-function

Введите значение параметра `http_invoke_url` в браузере и наслаждайтесь вызовом вашей функции. Во время ее вызова в `Object Storage` будет создан новый файл. Сразу после создания объекта в `Object Storage` сработает триггер `my-first-trigger`, который вызовет функцию `my-trigger-function`. В итоге, наша вторая функция запишет в логи содержание переменной `event`. Убедиться в этом вы сможете как в UI так и с помощью CLI.

    yc serverless function logs my-trigger-function