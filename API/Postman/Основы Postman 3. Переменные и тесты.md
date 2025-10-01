В постмане переменные доступны на различных уровнях Scope:
- глобальный;
- коллекция;
- URL;

Имеются постоянные переменные, которые можем добавлять на различных уровнях Scope.
Динамические переменные - встроенные переменные в Postman дающие рандомную систематизированную информацию.
{{$Название динамической переменной}}
В случае JSON необходимо не забывать ставить переменные в кавычки.

# Тесты
Тесты в постмане используют библиотеку утверждений Chai - https://www.chaijs.com/ ответ запроса должен быть равен утверждению. И тест это сравнивает.
## Response headers
Предустановленный тест - *Response headers: Content-Type header check* позволяет проверить пришел ли в ответе в *Headers* интересующий нас заголовок или нет.

pm.test("Имя_заголовка is present", function () {
    pm.response.to.have.header("Имя_заголовка");
});

Таким образом можем написать по тесту на каждый из заголовков и мы будем знать - все заголовки нормально отправились или нет. Если согласно API эти заголовки не должны приходить, то хрен они придут.
![[Pasted image 20250307220643.png]]
## Response time is less than 200ms
pm.test("Response time is less than 200ms", function () {

    pm.expect(pm.response.responseTime).to.be.below(200);

});

# Написание своих тестов
pm.test("Response time is less than 200ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(1000);
});

const JsonData = pm.response.json();
pm.test ("Response is json", ()=>{
	pm.expect(JSONData).to.be.a("object")
});