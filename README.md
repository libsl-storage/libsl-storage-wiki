# Определения и термины

- Программное обеспечение (ПО) – программа или множество программ, используемых для управления компьютером.

- Библиотека – сборник подпрограмм или объектов, используемых для разработки программного обеспечения.

- LibSL – язык спецификации компонентов программного обеспечения.

- Подсветка синтаксиса – выделение синтаксических конструкций текста с использованием различных цветов, шрифтов и начертаний. 

- Конечный автомат – абстрактная математическая модель, представленная в виде ориентированного графа, вершины котрого характеризуют текущее состояние автомата, а дуги между вершинами характеризуют возможные переходы между состояниями, осуществляемые в зависимости от входных данных.

- PAP (Password Authentication Protocol) – протокол аутентификации, предусматривающий отправку имени пользователя и пароля на сервер аутентификации.

- OAuth – протокол авторизации, обеспечивающий предоставление третьей стороне ограниченного доступа к защищённым ресурсам пользователя без передачи ей (третьей стороне) данных для аутентификации.

- FE (Frontend) – часть системы, отвечающая за принятие http- и https-запросов пользователей к домену, взаимодействие с BE и формирование графического пользовательского интерфейса.

- BE (Backend) – часть системы, реализующая ее бизнес-логику.

# Общие положения

Требуется реализовать сетевую систему хранения, отображения и редактирования спецификаций программных библиотек на языке LibSL. 

Система дожлна предоставлять пользователям следующие возможности:

- любой пользователь может осуществлять навигацию по каталогу загруженных в систему спецификаций на языке LibSL;

- любой пользователь может скачивать или просматривать имеющиеся в файловой системе файлы спецификаций;

- отображение спецификаций должно быть представлено в 2 вариантах:
  
  - текстовое содержание файла спецификации на языке LibSL с подсветкой синтаксиса;
  
  - визуализация конечных автоматов, описанных в файле спецификации при помощи синтаксической конструкции `automaton`;

- любой пользователь может авторизироваться;

- авторизованный пользователь может загружать новые файлы спецификации LibSL;
  
  - при загрузке для спецификаций должна задаваться метаинформация о специфицируемой библиотеке;
  
  - при загрузке спецификации должны пройти проверку на синтаксическую корректность;

# Требования, ограничения и допущения

## EPIC: Авторизация пользователей

Требуется реализовать возможность авторизации пользователя для дальнейшего разделения прав доступа.

Система авторизация должна служить для классификации пользователей:

- неавторизованный пользователь;

- авторизованный пользователь;

- суперпользователь.

### STORY: Интеграция сущности пользователя

Сущности пользователей должны храниться на стороне BE.

Сущность пользователя должна содержать следующую информацию:

- имя пользователя;

- имя учетной записи;

- адрес электронной почты;

- пароль;

- роль пользователя.

Каждая пользователь должен иметь уникальное имя учетной записи.

Каждая пользователь должен иметь уникальный адрес электронной почты.

На стороне BE требуются CRUD-средства для управления сущностями.

Пароли должны храниться в виде хэшей. Требуется возможность конфигурации методов шифрования на стороне BE. Требуется обеспечить возможность шифровки и дешифровки пароля при всех операциях его чтения или записи.

Все пользователи по умолчанию должны иметь роль `default`.

### STORY: Интеграция ролей пользователей

Роли должны определять набор привелегий, доступные пользователям.

Перечень ролей и их привелегий:

- обычный пользователь
  
  - изменение собственных пользовательских данных (кроме роли);
  
  - создание спецификаций;
  
  - изменение принадлежащих спецификаций;

- суперпользователь (админ)
  
  - изменение любых пользовательских данных
  
  - создание спецификаций;
  
  - изменение любых спецификаций.

### STORY: Регистрация новых пользователей

Требуется REST API контроллер на стороне BE для создания пользователей.

Контроллер должен принимать данные необходимые для создания пользователя (имя пользователя, имя учетной записи, адрес электронной почты, пароль).

При создании пользователя требуется явная валидация уникальности имени учетной записи регистрируемого пользователя.

При создании пользователя требуется явная валидация уникальности электронной почты регистрируемого пользователя.

При несоответствии вышеуказанным условиям или любой ошибке в процессе создания пользователя, в ответе на запрос о регистрации должна фигурировать причина неудачи.

При успешном создании пользователя в ответе на запрос о регистрации должен фигурировать jwt-токен для дальнейшей авторизации.

### STORY: Создание аккаунта суперпользователя

Генерация пользователя с ролью суперпользователя должна осуществляться при запуске системы, если он не было сгенерирован до этого.

Требуется возможность конфигурировать данные суперпользователя.

### STORY: Аутентификация пользователей по протоколу PAP

Требуется REST API контроллер на стороне BE для аутентификации пользоваьелей при помощи идентификатор + пароль.

В качестве идентификатора пользователя может выступать имя учетной записи пользователя или его адрес электронной почты.

В случае неуспешной аутентификации в ответе на запрос об аутентификации должна фигурировать причина неудачи.

В случае успешной аутентификации в ответе на запрос об аутентификации должен фигурировать jwt-токен для дальнейшей авторизации.

### STORY: Аутентификация пользвателя по протоколу OAuth

Требуется REST API контроллер на стороне BE для аутентификации пользоваьелей при помощи сторонних сервисов по протоколу OAuth2.

Доступные сервисы и настройки подключения к ним должны быть конфигурируемыми. 

Требуется REST API контроллер на стороне BE для получения списка доступных сервисов для авторизации при помощи OAuth.

В случае неуспешной аутентификации в ответе на запрос об аутентификации должна фигурировать причина неудачи.

В случае успешной аутентификации пользователя на сервисе и отсутствии пользователя с соответствующим адресом электронной почты на стороне BE в ответе на запрос об аутентификации должно фигурировать сообщение о том, что требуется регистрация пользователя, а также данные о пользователе, полученые при аутентификации на сервисе (имя пользователя, адрес электронной почты).

В случае успешной аутентификации в ответе на запрос об аутентификации должен фигурировать jwt-токен для дальнейшей авторизации.

### STORY: Отображение формы аутентификации

Требуется на стороне FE реализовать отображение для обеспечения возможности аутентификации пользователя.

Отображение должно содержать поле для ввода идентификатора пользователя.

Отображение должно содержать поле для ввода пароля пользователя.

Отображение должно содержать кнопку для передачи данных для аутентификации по протоколу PAP на сторону BE.

Отображение должно содержать набор кнопок, запрашивающий соответствующие данные сос тороны BE для аутентификации по протоколу OAuth. Каждая кнопка должна соответствовать одному из доступных сервисов для аутентификации. Список доступных сервисов должен приходить со стороны BE по соответствующему запросу.

Отображение должно содержать кнопку для перехода на страницу отображения формы регистрации.

При неуспешной аутентификации должно отображаться текстовое сообщение с причиной последней неудачи.

При аутентификации при помощи стороннего сервиса по протоколу OAuth и получении сообщении о необходимости создать нового пользователя, требуется навигироваться на страницу отображения формы регистрации; при этом поля формы регистрации должны быть заполнены на основе данных, полученных из ответа на запрос о неудачной аутентификации.

При успешной аутентификации должна осуществляться на последнию в истории навигации страницу, не являющейся страницей отображения формы аутентификации или формы регистрации, или на страницу отображения навигации по спецификациям.

### STORY: Отображение формы регистрации

Требуется на стороне FE реализовать отображение для обеспечения возможности регистрации пользователя.

Отображение должно содержать набор полей для ввода необходимой для создания на стороне BE пользователя (имя пользователя, имя учетной записи, адрес электронной почты, пароль).

Отображение должно содержать кнопку для передачи данных для создания пользователя на сторону BE.

Отображение должно содержать кнопку для перехода на страницу отображения формы аутентификации.

При неуспешной регистрации должно отображвться текстовое сообщение с причиной последней неудачи.

При успешной регистрации должна осуществляться навигация на последнию в истории навигации страницу, не являющейся страницей отображения формы аутентификации или формы регистрации, или на страницу отображения навигации по спецификациям.

## EPIC: Навигация по имеющимся спецификациям

### STORY: Фильтрация отображаемых спецификаций

### STORY: Навигация между элементами хранилища

### STORY: Отображение хранилища спецификаций

### STORY: Отображение параметров фильрации спецификаций

### STORY: Отображение панели управления

## 

## EPIC: Просмотр спецификаций

### STORY: Отображение мета-информации спецификации

### STORY: Отображение содержимого спецификаций

### STORY: Отображение визуализации конечных автоматов спецификаций

### STORY: Отображение подсветка синтаксических конструкций LibSL

## EPIC: Изменение спецификаций

Пользователь хочет внести правки по следующим причинам:
  - Изменение мета-данных спецификации библиотеки
  - Изменение Wiki спецификации
  - Изменение текущей спецификации
  - Создание нового релиза
  - Изменение владельцев спецификации
  - Удаление спецификации

Для внесение изменений пользоваелю необходимо быть авторизованным пользователем с достаточными правами на данную спецификацию. В этом случае в интерфейсе должна появиться соответствующая опция.

### STORY: Изменение мета-информации спецификации

Пользователь хочет поменять
  - Название библиотеки
  - Краткое описание
  - Языки релиазции
  - Теги

Остальные элементы мета-информации для правок не доступны. В форме должна присутствовать опция "Настроить/Править". При нажатии на неё ооткрывается форма, содержащая в полях ввода текущие мета-данные спецификации. Пользователь вводит новое название, при этом проверяется уникальность названия спецификации для данного пользователя. Таким же образом изменяется краткое описание. Языки реализации добавляются по кнопке \+ и выбираются из раскрывающегося списка. Тэги записываются в поле текстового ввода через запятую. Далее пользователь применяет или отменяет изменения.

### STORY: Изменение Wiki спецификации

Пользователь хочет изменить описание спецификации на Wiki вкладке. Изменение Wiki спецификации может происходить двумя методами:
  - Изменение во встроенном markdown текстовом редакторе
  - Загрузка файла

Для этого на Wiki вкладке должна быть опция "Изменить/Править" и "Загрузить".

В первом случае открывается текстовый редактор с подстветкой синтаксиса и базовой панели инструментов markdown редактора. В редакторе должны быть опции "Сохранить" и "Отменить".

Во втором случае должно открываться системное окно файлового менеджера с возможностью выбора файла с нужным расширением `.md`.

После сохранения markdown верски необходимо отрендерить wiki страницу.

### STORY: Изменение текущей спецификации

Пользователь хочет изменить текст спецификации на соответствующей вкладке.

Изменение текущей спецификации может происходить двумя методами:
  - Изменение во встроенном текстовом редакторе
  - Загрузка файла

Для этого на вкладке с описанием спецификации на LibSL должна быть опция "Изменить/Править" и "Загрузить".

В первом случае открывается текстовый редактор с подстветкой синтаксиса LibSL. В редакторе должны быть опции "Сохранить" и "Отменить". После активации обоих опций текстовый редактор не изменяется, но пропадает воможность ввода.

Во втором случае должно открываться системное окно файлового менеджера с возможностью выбора файла с нужным расширением.


### STORY: Создание нового релиза

Пользователь выбирает опцию "Создать релиз" в поле мета-инормации спецификации. Открывается форма, в которой вводится:
  - Код релиза
  - Название релиза
  - Описание изменений

В форме присутствует кнопка "Выбрать файл спецификации", открывающая системное окно файлового менеджера для выбора LibSL файла.
Для возмодного обновления wiki страницы форме присутствует поле с двумя кнопками "Выбрать файл описания" и "Оставить текущее описание".
Создание нового релиза завершается кнопками "Создать" или "Отменить". Если новый релиз создаётся, то в список релизов в мета-информации добавлсяется новая строка.

### STORY: Изменение владельцев спецификации

Пользователю необходимо поделиться правом доступа с коллегами и соавторами. Для этого ему необходимо быть автором данной спецификации, иначе соответствующая опция в интерфейсе не отображается.

Для добавления владельцев пользователь нажимает на опцию "Изменение прав доступа". Открывается форма со списком текущих вдадельцев спецификации и автора. Далее пользователь нажимает на кнопку "Добавить владельца". Активируется поле ввода имени пользователя. Желательно при вводе отображать список возможных совпадений с возможностью выбора. После ввода имени и нажатии кнопки "Добавить" пользователь появляется в списке владельцев и ему присваиваются соответствующие права на данную специфкацию. Если пользователя с введённым именем не существует, то вывести ошибку под тестовым полем.

Выход из формы осуществляется по кнопке "Выйти".

### STORY: Удаление спецификаций

Пользователь хочет удалить конкретную версию спецификации или всю спецификацию целиком. Так как страница спецификации отображает конкретную её версию, то этот контекст учитывается при операции удаления.

Пользователь выбирает опцию "Удалить" на главной странице спецификации. Открывается контекстное меню из двух пунктов
  - Удаль текущую версию спецификации
  - Полностью удалить спецификацию

После нажатия на нужную опцию возникает окно подтверждения или отмены действия.

Если пользователь подтвердил удаление текущей версии, то далее отображается последняя версия спецификации.

Если пользователь полностью удалил спецификацию или удалил единственную её версию, то интерфейс переходит в состояние навигации по спецификациям.

## EPIC: Импорт спецификаций

### STORY: Отображение окна выбора файлов

### STORY: Загрузка файлов

## EPIC: Развертывание системы

### STORY: Создание docker-compose инфраструктуры

На данный момент планируется деление проекта на три контейнера:

- БД

- Фронтэнд - веб-сервер

- Бэкэнд - реализация бизнес-логики

Для обеспечения взаимодействия контейнеров между собой планируется использование двух внутренних docker-сетей:

- БД-бэкэнд

- бэкэнд-фронтэнд

Следует отметить, что контейнер с веб-сервером должен иметь доступ к внешней сети для протоколов http/https.

### STORY: Настройка веб-сервера

Необходимо обеспечить как сборку проекта с фронтэндом, так и развёртывание артефактов сборки (сайт) на веб-сервере (e.g. Nginx). 

Так как требуется обеспечить подключение как с использованием http, так и с использованием https, необходимо озаботиться сертификатами (использовать let's encrypt). 

### STORY: Настройка бэкэнда

Необходимо обеспечить сборку бэкэнда с использованием выбранной системы сборки (e.g. Gradle) и запуском полученного в результате сборки артефакта.

### STORY: Настройка БД

Необходимо настроить запуск контейнера с БД (e.g. Postgres) с сохранением данных в volume и первичной инициализацией при отсутствии данных (пустом volume). Данные для доступа к БД будут находиться во внешнем .conf-файле и записываться в глобальные переменные бэкэнд-контейнера при его запуске.

### STORY: Настроить CI

В рамках реализации CI необходимо настроить автоматический запуск автотестов при появлении MR или коммите в main и develop-ветки.

### STORY: Настроить CD

Необходимо настроить автосборку контейнера и его выгрузку в docker registry для последующего автоматического обновления на конечном сервере с импользованием watchtower-контейнера.
