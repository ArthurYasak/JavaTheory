JWT (ID token) base flow:

![JWT_flow](https://github.com/ArthurYasak/JavaTheory/blob/c56a16bcf9648ffde53662286f4b49b8150ba5b7/images/JWT_flow.png)

Структура токена: header.payload.signature
Полезная нагр-ка payload токена не шифруется, не использовать ее для передачи чувствительных данных.
Данные JWT-токена наз-ся заявками (claims
