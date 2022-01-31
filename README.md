# municorn-interview

Сделал два варианта реализации. 
1. Подразумевает, что сервис, который обращается к api, знает какого типа уведомление он отправляет и использует разные методы для отправки уведомлений (/AndroidNotificationMessage/send, /IosNotificationMessage/send)
2. Второй принимает только тело сообщения, тип сообщения не указывается (/Notification/send)

На мой взгляд первый вариант лучше. Т.к. в таком случае используются валидации asp.net, контроллеры генерируются автоматический, в swagger указываются типы данных, которые принимают методы. 
Во втором нужно  поддерживать метод класса DetectNotificationType, чтобы понимать, какого типа сообщение приходит. Появляется дополнительный слой абстракции, который определяет, какой INotificationService нужно использовать. Нужно кастомные валидаторы (AndroidMessageValidator, IosMessageValidator). Не указываются  сигнатуры методов в swagger. 

В обоих случаях для добавления нового типа  сообщений нужно добавить тело сообщения (и унаследовать его от NotificationMessage), реализовать INotificationSender<TMessage> для нового типа сообщений. Добавить  регистрацию сервисов в AddNotificationsSender. 
Для второго случая дополнительно: Добавить в serviceCollection новый INotificationService, модифицировать DetectNotificationType, чтобы распознавал новый тип сообщения. 

Статусы отправки сохраняются в память, потому что в целом запросы в бд будут достаточно простые, поэтому прикручивать базу для тестового задания в таком случае не вижу необходимости. 

Можно запустить сервис из Rider или в докере. Для докера есть скрипты:
- run_by_docker.sh - пересоберет контейнер, запустит в нем приложение и откроет страницу swagger'a
- stop_docker.sh - остановит контейнер
- docker_cleanup.sh - удалит созданные контейнерны  (удалит еще mcr.microsoft.com/dotnet/skd и mcr.microsoft.com/dotnet/aspnet, которые при сборке используются, если вы используете эти image, то не запускайте скрипт)

Чтобы логи посмотреть в контейнере используйте `docker logs notification_service`