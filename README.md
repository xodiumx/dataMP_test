# dataMP_test

```
Опишите (можете нарисовать) архитектуру (верхнеуровневую) сервиса "Репрайсер"
с обоснованием тех компонентов, технологий и инструментов, которые предполагаете использовать.
Какие известные Вам технические нюансы репрайсера WildBerries могут возникнуть и как Вы планируете их решать?
```

## Архитектура приложения

### Получение рекомендации о цене товара, ELT процесс:

1. Есть база со свежей информацией о всех товарах маркетплейса, которая обновляется 2 раза в день:

<img 
  width="452" 
  alt="Снимок экрана 2024-02-15 в 13 02 33"
  src="https://github.com/xodiumx/dataMP_test/assets/111197771/03b05774-aea6-4cd4-ac0e-f880159a063a">

- `Scrapy` - эффективен при парсинге (работает асинхронно)
- `Aiohttp` - позволяет посылать асинхронные запросы
- `ClickHouse` - эффективная база для аналитики данных, позволяет с большой скоростью получать большие объемы данных.

2. Достаем информацию о нужных товарах и рассчитываем цену:

<img 
  width="1253" 
  alt="Снимок экрана 2024-02-15 в 13 17 29" 
  src="https://github.com/xodiumx/dataMP_test/assets/111197771/d5fc0d32-a915-426d-aedc-ddd0bcc40273">

- В целом для организации процесса я бы использовал `Apache Airflow`, который позволяет настраивать цепочки `DAG-и` формирования и обработки данных.
  Помимо удобства настройки процесса, `Airflow` предоставляет, информационную панель для контроля процесса, в которой можно просматривать логи и многое другое.
- Но также можно настроить и использовать `celery schedule` для более гибкой и низкоуровневой настройки, который используется под капотом `Airflow`

3. Автоматический контроль цены:
- Либо настраиваем cron на получение данных (парсинг, получение через api) о конкретном товаре напрямую и пвторяем процесс указанный выше.
- Либо мониторим нашу базу данных.

4. Вывод информации

<img 
  width="670" 
  alt="Снимок экрана 2024-02-15 в 13 27 41" 
  src="https://github.com/xodiumx/dataMP_test/assets/111197771/c91560ad-7320-4709-9579-e2516e27b69a">

- `Apache superset` - бесплатная платформа для вывода аналитической информации
- `Datalens` - платформа для вывода аналитической информации
- `Vue, Nuxt, React` - фронтенд для пользовательского интерфейса 
- `FastAPI` - Современный фреймворк для обработки большого количества запросов и передачи информации на фронтенд

## Полная блок схема

- Естественно каждый этап можно разделять на под этапы, для большего контроля.

<img width="900" alt="Снимок экрана 2024-02-15 в 13 38 11" src="https://github.com/xodiumx/dataMP_test/assets/111197771/7ff60232-c9b4-48f4-a5b9-97111708461d">

## Проблемы:
```
Одной из главных проблем, является сравнение наименований товаров, потому что они могут отличаться 
буквально на несколько символов, «Салют Форсаж» «Салют Форсаж 1шт.»

Я пробовал решать такую проблему с использованием не точного поиска в `Elasticserach`, 
но с этим подходом получается много не точностей, которые необходимо решать в ручную.

Для полного автоматического сравнения наименований товаров, 
должна быть настроена ML модель или можно использовать готовые решения по типу GPT, 
которые более точно произведут проверку.
```
