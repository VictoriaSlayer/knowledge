Используй DeepSeek когда нужна помощь.
Позволяет провалидировать, что данные по API поступили верные.
https://www.jsonschemavalidator.net/ - валидатор вебовский. У каждого вебовского валидатор свои ограничения. У одних в JSON SCHEMA не должно быть строки:
- "$schema": "https://json-schema.org/draft/2020-12/schema" иначе он начинает ругаться на https;
- в этом баги на наименование необязательных свойств (в других этого нет);

[неплохая статья на хабре](https://habr.com/ru/companies/otus/articles/851512/)
[словарь по JSON SCHEMA](https://json-schema.org/draft/2020-12/draft-bhutton-json-schema-validation-01#name-pattern)
[ядро](https://json-schema.org/draft/2020-12/json-schema-core)
[как использовать JSON Schema в Postman](https://www.youtube.com/watch?v=IbyYUTBDVXQ)
