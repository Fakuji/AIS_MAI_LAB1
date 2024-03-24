# Компонентная архитектура
<!-- Состав и взаимосвязи компонентов системы между собой и внешними системами с указанием протоколов, ключевые технологии, используемые для реализации компонентов.
Диаграмма контейнеров C4 и текстовое описание.
-->
## Компонентная диаграмма

```plantuml
@startuml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

AddElementTag("microService", $shape=EightSidedShape(), $bgColor="CornflowerBlue", $fontColor="white", $legendText="microservice")
AddElementTag("storage", $shape=RoundedBoxShape(), $bgColor="lightSkyBlue", $fontColor="white")

Person(admin, "Администратор")
Person(moderator, "Модератор")
Person(user, "Пользователь")

System_Ext(web_site, "Клиентский веб-сайт", "HTML, CSS, JavaScript, React", "Веб-интерфейс")

System_Boundary(conference_site, "Социальная сеть") {
   'Container(web_site, "Клиентский веб-сайт", ")
   Container(client_service, "Сервис аутенфикации", "C++", "Сервис управления пользователями", $tags = "microService")
   Container(post_service, "Сервис управления контентом", "C++", "Сервис управления контентом", $tags = "microService")
   Container(blog_service, "Сервис обмена сообщениями", "C++", "Сервис управления обмена сообщениями", $tags = "microService")
   ContainerDb(db, "База данных", "MySQL", "Хранение данных о записях, сообщениях и пользователях", $tags = "storage")

}

Rel(admin, web_site, "Просмотр, добавление и редактирование информации о пользователях, записях и сообщениях")
Rel(moderator, web_site, "Модерация контента и пользователей")
Rel(user, web_site, "Регистрация, просмотр информации о записях и сообщениях и получения их")

Rel(web_site, client_service, "Работа с пользователями", "localhost/person")
Rel(client_service, db, "INSERT/SELECT/UPDATE", "SQL")

Rel(web_site, post_service, "Работа с записями", "localhost/pres")
Rel(post_service, db, "INSERT/SELECT/UPDATE", "SQL")

Rel(web_site, blog_service, "Работа с сообщениями", "localhost/conf")
Rel(blog_service, db, "INSERT/SELECT/UPDATE", "SQL")

@enduml
```
## Список компонентов

### Сервис аутенфикации
**API**:
-	Создание нового пользователя
      - входные параметры: login, password, first_name, second_name, email
      - выходные параметры: отсутствуют
-	Поиск пользователя по логину
     - входные параметры:  login
     - выходные параметры: first_name, second_name, email
-	Поиск пользователя по маске имени и фамилии
     - входные параметры: маска first_name, маска second_name
     - выходные параметры: login, first_name, second_name, email

### Сервис управления контентом
**API**:
- Добавление записи на стену
  - Входные параметры: user_id, контент поста, автор контента, дата создания поста
  - Выходные параметры: идентификатор записи
- Загрузка стены пользователя
  - Входные параметры: user_id
  - Выходные параметры: массив с набором данных контента в котором содержится user_id, контент поста и автор поста


### Сервис обмена сообщениями
**API**:
- Отправка сообщения пользователю
  - Входные параметры: идентификатор чата, текст сообщения, автор сообщения, метка времени и даты
  - Выходные параметры: идентификатор сообщения
- Получение списка сообщений для пользователя
  - Входные параметры: идентификатор чата
  - Выходные параметры: массив с набор полученных и отправленных сообщений



### Модель данных
```puml
@startuml

class Wall {
  author_id
  post_id
  wall_id
  body
}

class User {
  user_id
  login
  first_name
  last_name
  email
  wall_id

}

class Post {
  post_id
  body
  author_id
  post_date
}

class Chat {
  chat_id
  message_id
  first_user_id
  second_user_id
}
class Message {
  message_id
  body
  date

}
Message -> Chat
Wall <- User
Post -> Wall
Chat <-> User



@enduml
```
