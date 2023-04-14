# Определения и термины

Программное обеспечение (ПО) – программа или множество программ, используемых для управления компьютером.

Библиотека – сборник подпрограмм или объектов, используемых для разработки программного обеспечения.

LibSL – язык спецификации компонентов программного обеспечения.

Подсветка синтаксиса – выделение синтаксических конструкций текста с использованием различных цветов, шрифтов и
начертаний.

Конечный автомат – абстрактная математическая модель, представленная в виде ориентированного графа, вершины которого
характеризуют текущее состояние автомата, а дуги между вершинами характеризуют возможные переходы между состояниями,
осуществляемые в зависимости от входных данных.

PAP (Password Authentication Protocol) – протокол аутентификации, предусматривающий отправку имени пользователя и
пароля на сервер аутентификации.

OAuth – протокол авторизации, обеспечивающий предоставление третьей стороне ограниченного доступа к защищённым
ресурсам пользователя без передачи ей (третьей стороне) данных для аутентификации.

FE (Frontend) – часть системы, отвечающая за принятие http- и https-запросов пользователей к домену, взаимодействие с
BE и формирование графического пользовательского интерфейса.

BE (Backend) – часть системы, реализующая ее бизнес-логику.

Абстрактный семантический граф (Abstract Semantic Graph, ASG) – структура данных, используемая для представления или
извлечения семантики выражения на формальном языке. ASG используется для описания синтаксической структуры выражения
или программы.

Хедер – верхний элемент отображения веб-страницы.

# Общие положения

Требуется реализовать сетевую систему хранения, отображения и редактирования спецификаций программных библиотек на
языке LibSL.

Система должна предоставлять пользователям следующие возможности:

- любой пользователь может просматривать список имеющихся в системе спецификаций на языке LibSL, а также настраивать
  лично для себя фильтрацию этого списка;
- любой пользователь может скачивать или просматривать имеющиеся в файловой системе файлы спецификаций;
- отображение спецификаций должно быть представлено в 2 вариантах:
    - текстовое содержание файла спецификации на языке LibSL с конфигурируемой подсветкой синтаксиса;
    - визуализация конечных автоматов, описанных в файле спецификации при помощи специальной синтаксической конструкции;
- любой пользователь может авторизоваться;
- авторизованный пользователь может загружать новые файлы спецификации LibSL;
    - при загрузке для спецификаций должна задаваться метаинформация о специфицируемой библиотеке;
    - при загрузке спецификации должны пройти верификацию;
    - результаты верификации (абстрактный семантический граф или набор ошибок, выявленных при верификации) должны быть
      сохранены вместе со спецификацией

# Требования, ограничения и допущения

## EPIC: Авторизация пользователей

Требуется реализовать систему аутентификации пользователей для их дальнейшей авторизации.

### STORY: Интеграция сущности пользователя

Сущности пользователей должны храниться на стороне BE.

Сущность пользователя должна содержать следующую информацию:

- имя пользователя;
- имя учетной записи;
- адрес электронной почты;
- пароль;
- роль пользователя;
- аватар пользователя.

Каждый пользователь должен иметь уникальное имя учетной записи.

Каждый пользователь должен иметь уникальный адрес электронной почты.

Пользователь может обладать одной из следующих ролей

- обычный пользователь;
- суперпользователь.

Роль обычного пользователя должна предоставлять следующие права:

- изменение собственных пользовательских данных;
- создание директорий в корневой директории или в собственных директориях;
- создание спецификаций в корневой директории или в принадлежащих директориях;
- изменение принадлежащих спецификаций;
- удаление принадлежащих спецификаций.

Роль суперпользователя должна предоставлять следующие права:

- изменение собственных пользовательских данных;
- создание директорий в любой директории;
- создание спецификаций в любой директории;
- изменение любых спецификаций;
- удаление любых спецификаций.

По умолчанию пользователи должны иметь роль обычного пользователя.

На стороне BE требуются CRUD-средства для управления сущностями.

Пароли должны храниться в виде хэшей. Требуется возможность конфигурации методов шифрования на стороне BE. Требуется
обеспечить возможность шифровки пароля при его сохранении и дешифровки при чтении его.

### STORY: Создание суперпользователя

Требуется возможность конфигурировать данные суперпользователя:

- имя пользователя;
- имя учетной записи;
- адрес электронной почты;
- пароль.

Требуется генерировать пользователя с ролью суперпользователя при запуске системы. Данные суперпользователя
должны соответствовать данным конфигурации суперпользователя.

### STORY: Регистрация пользователя

Требуется REST API контроллер на стороне BE для создания пользователя.

Контроллер должен принимать данные необходимые для создания пользователя:

- имя пользователя;
- имя учетной записи;
- адрес электронной почты;
- пароль.

Требуется явная проверка уникальности имени учетной записи.

Требуется явная проверка уникальности электронной почты.

При несоответствии вышеуказанным условиям или любой ошибке в процессе создания пользователя, в ответе на запрос
должна фигурировать причина неудачи.

При успешном создании пользователя на стороне BE должен быть сохранен новый пользователь. Имя пользователя, имя учетной
записи, адрес электронной почты и пароль у нового пользователя должны соответствовать данным, полученным в текущем
запросе. В ответе на запрос должен фигурировать jwt-токен для дальнейшей авторизации.

Если вышеперечисленные условия соблюдены, на стороне BE должен быть сохранен новый пользователь. Имя пользователя и
пароль у измененного пользователя должны соответствовать данным, полученным в текущем запросе. В ответе на запрос
должны фигурировать данные измененного пользователя.

### STORY: Изменение пользователя

Требуется REST API контроллер на стороне BE для изменения пользователя.

Требуется обеспечить возможность идентифицировать изменяемого пользователя при помощи обязательного параметра пути
контроллера.

Требуется проверка авторизации пользователя. Требуется проверка наличия соответствующих прав для изменения
идентифицированного пользователя.

Контроллер должен принимать данные необходимые для изменения пользователя:

- имя пользователя;
- пароль.

При несоответствии вышеуказанным условиям или любой ошибке в процессе изменения пользователя, в ответе на запрос
должна фигурировать причина неудачи.

Если вышеперечисленные условия соблюдены, на стороне BE должен быть изменен идентифицированный пользователь. Имя
пользователя и пароль у измененного пользователя должны соответствовать данным, полученным в текущем запросе. В ответе
на запрос должны фигурировать данные измененного пользователя.

### STORY: Аутентификация пользователей по протоколу PAP

Требуется REST API контроллер на стороне BE для аутентификации пользователей при помощи его идентификации.

Контроллер должен получать данные, необходимые для аутентификации:

- идентификатор;
- пароль.

Все поля данных должны быть предоставлены.

В качестве идентификатора пользователя может выступать имя учетной записи пользователя или его адрес электронной почты.

В случае неуспешной аутентификации в ответе на запрос должна фигурировать причина неудачи.

В случае успешной аутентификации в ответе на запрос должен фигурировать jwt-токен для авторизации аутентифицированного
пользователя.

### STORY: Аутентификация пользователя по протоколу OAuth

Требуется REST API контроллер на стороне BE для получения списка доступных сторонних сервисов для авторизации по
протоколу OAuth2.

Доступные сервисы и настройки подключения к ним должны быть конфигурируемыми.

Требуется REST API контроллер на стороне BE для аутентификации пользователей при помощи сторонних сервисов по протоколу
OAuth2.

В случае неуспешной аутентификации в ответе на запрос должна фигурировать причина неудачи.

В случае успешной аутентификации пользователя на сервисе и отсутствии пользователя с соответствующим адресом
электронной почты на стороне BE в ответе на запрос об аутентификации должно фигурировать сообщение о том, что
требуется регистрация пользователя, а также данные о пользователе, полученные при аутентификации на сервисе:

- имя пользователя;
- адрес электронной почты.

В случае успешной аутентификации в ответе на запрос должен фигурировать jwt-токен для авторизации аутентифицированного
пользователя.

### STORY: Отображение формы регистрации

Требуется на стороне FE реализовать отображение для обеспечения возможности регистрации пользователя.

Отображение должно содержать набор полей для ввода необходимой для создания на стороне BE пользователя

- имя пользователя;
- имя учетной записи;
- адрес электронной почты;
- пароль.

Требуется возможность заполнения полей данных при переходе на страницу отображения.

Отображение должно содержать кнопку для передачи данных для регистрации пользователя на сторону BE.

Отображение должно содержать кнопку для перехода на страницу отображения формы аутентификации.

При неуспешной регистрации должно отображаться текстовое сообщение с причиной последней неудачи.

При успешной регистрации должен осуществляться переход на последнюю в истории навигации страницу, не являющейся
страницей отображения формы аутентификации или формы регистрации, или на страницу отображения навигации по
спецификациям.

### STORY: Отображение формы аутентификации

Требуется на стороне FE реализовать отображение для аутентификации пользователя.

Отображение должно содержать поле для ввода идентификатора пользователя.

Отображение должно содержать поле для ввода пароля пользователя.

Отображение должно содержать кнопку для передачи данных для аутентификации по протоколу PAP на сторону BE.

Отображение должно содержать набор кнопок, запрашивающий соответствующие данные со стороны BE для аутентификации по
протоколу OAuth2. Каждая кнопка должна соответствовать одному из доступных сервисов для аутентификации. Набор доступных
сервисов для аутентификации должен определяться на стороне BE.

Отображение должно содержать кнопку для перехода на страницу отображения формы регистрации.

При неуспешной аутентификации должно отображаться текстовое сообщение с причиной последней неудачи.

При аутентификации при помощи стороннего сервиса по протоколу OAuth и получении сообщении о необходимости создать
нового пользователя, требуется переход на страницу отображения формы регистрации; при этом поля формы регистрации
должны быть заполнены на основе данных из полученного ответа на запрос.

При успешной аутентификации должен осуществляться переход на последнюю в истории навигации страницу, не являющейся
страницей отображения формы аутентификации или формы регистрации, или на страницу отображения навигации по
спецификациям.

### STORY: Отображение формы управления данными пользователя

Требуется на стороне FE реализовать отображение формы для управления данными текущего пользователя.

Отображение должно содержать аватар пользователя. Отображение должно содержать кнопку для загрузки нового аватара
пользователя.

Отображение должно содержать текстовое поле для изменения имени пользователя.

Отображение должно содержать текстовое поля для изменения пароля пользователя; также требуется текстовое поле для
подтверждения нового пароля.

Отображение должно содержать кнопку подтверждения сохранения данных пользователя.

Если пользователь авторизован, в хедере требуется отображение кнопки перехода на страницу отображения формы управления
данными пользователя; также требуется кнопка выхода пользователя; также требуется кнопка для перехода на страницу
отображения настроек.

Если пользователь не авторизован, в хедере требуется кнопка перехода на страницу отображения аутентификации
пользователя; также требуется кнопка перехода на страницу отображения регистрации пользователя.

## EPIC: Хранение спецификаций

### STORY: Интеграция сущности узла директории спецификаций

Сущности узла директории спецификаций должны храниться на стороне BE.

Сущность узла директории спецификаций содержать следующую информацию:

- название узла;
- владелец узла;
- родительский узел.

Требуется обеспечить уникальность названий узлов с одним родительским узлом.

На стороне BE требуются CRUD-средства для управления сущностями узла директории спецификаций.

### STORY: Интеграция сущности спецификации

Сущности спецификаций должны храниться на стороне BE.

Сущность спецификации должна содержать следующую информацию:

- название спецификации;
- название специфицируемой библиотеки;
- краткое описание спецификации;
- список тегов;
- родительская директория;
- владелец спецификации;
- исходный текст libsl-спецификации;
- абстрактный семантический граф libsl-спецификации;
- список ошибок верификации libsl-спецификации;
- список описаний графических отображений конечных автоматов, описанных в libsl-спецификации.

Название спецификации должно быть уникальным для одной директории.

Каждый тег должен представлять пару "ключ-значение", где в качестве ключа выступает тип тега, а в качестве значения
выступает список соответствующих данному типу значений, заданных владельцем соответствующей спецификации. Требуется
возможность конфигурировать перечень доступных ключей.

В исходном тексте libsl-спецификации при помощи конструкции `automaton` могут быть описаны конечные автоматы,
создаваемые в процессе функционирования специфицируемой библиотеки. При помощи данной конструкции могут быть описаны
состояния КА, переходы между ними, а также локальные переменные КА. Также внутри конструкций `fun`, используемых для
спецификации функций API, могут быть декларированы конструкции для создания экземпляров дочерних КА. Требуется
обеспечить возможность хранения отображения каждого отдельного КА, описанного в спецификации, включая следующие данные:

- название КА;
- состояния КА, включая их названия и флаг конечного состояния;
- возможные переходы между состояниями КА включая названия функций, вызов которых инициирует соответствующий переход;
- локальные переменные КА, включая их названия.

Если в спецификации описано более одного КА, требуется сохранить общее отображение всех КА, описанных в спецификации,
включая следующие данные:

- названия КА;
- состояния КА, включая их названия, название КА, к которому данное состояние относится, и флаг конечного состояния;
- переходы между состояниями внутри каждого отдельного КА, включая названия функций, вызов которых инициирует
  соответствующий переход;
- возможные переходы между состояниями разных КА, характеризующие создание дочернего КА, включая названия функций,
  вызов которых инициирует соответствующее создание КА;
- локальные переменные КА, включая их названия и название КА, к которому соответствующая переменная относится.

На стороне BE требуются CRUD-средства для управления сущностями спецификаций.

### STORY: Верификация libsl-файла

Требуется REST API контроллер на стороне BE для верификации libsl-спецификации.

Текст исходной libsl-спецификации должен быть временно сохранен на стороне BE. Все сохраненные данные, касающиеся
данной спецификации (исходный текст, список ошибок, абстрактный семантический граф, отображения КА), хранящиеся на
стороне BE больше недели должны быть удалены.

Верификация заключается в статическом анализе содержимого исходного libsl-файла.

В случае, если статический анализ выявил ошибки в спецификации, требуется сохранить информацию о найденных ошибках:

- место, в котором найдена ошибка (интервал строк и символов);
- сообщение ошибки.

В случае, если статический анализ не выявил ошибок, на основе исходной libsl-спецификации должен быть сгенерирован и
сохранен ASG спецификации.

На основе сгенерированного ASG должны быть сгенерированы и сохранены отображения конечных автоматов, описанных в
спецификации, в том же формате, в котором данные отображения хранились бы для сущности спецификации.

В ответе на запрос должны фигурировать исходный текст спецификации, а также имеющиеся результаты, полученные в ходе
верификации (список ошибок, ASG, отображения КА).

### STORY: Создание спецификации

Требуется REST API контроллер на стороне BE для создания спецификации.

Требуется проверка авторизации пользователя. Требуется проверка наличия соответствующих прав для создания
спецификации.

Контроллер должен получать данные, необходимые для создания спецификации:

- название спецификации;
- краткое описание спецификации;
- список тегов;
- url родительской директории;
- файл libsl-спецификации.

Url спецификации в файловой иерархии могут быть не предоставлены. Все остальные поля данных должны быть предоставлены.

Требуется проверка, что название спецификации не является пустой строкой.

Требуется проверка, что название специфицируемой библиотеки не является пустой строкой.

Если url директории предоставлен, требуется проверка, что пользователь имеет соответствующие права для создания
спецификации в указанной директории.

Требуется проверка, что отсутствуют спецификации с указанным названием и родительской директорией, соответствующей
указанному url, полученному в текущем запросе.

Требуется верификация libsl-спецификации. Если для исходного текста libsl-спецификации ранее проводилась верификация
и имеются результаты верификации, повторная верификация не требуется.

Если вышеперечисленные условия соблюдены, на стороне BE должна быть сохранена новая спецификация. Владелец новой
спецификации должен соответствовать авторизованному для текущего запроса пользователя. Родительская директория новой
спецификации должна соответствовать url, полученному в текущем запросе. Абстрактный семантический граф
libsl-спецификации, список ошибок верификации libsl-спецификации, список описаний графических отображений конечных
автоматов у новой спецификации должны соответствовать результатам верификации спецификации. Название специфицируемой
библиотеки должно быть должно соответствовать значению специальной конструкции `library` в ASG. Название спецификации,
краткое описание спецификации, список тегов и исходный текст libsl-спецификации у новой спецификации должны
соответствовать данным, полученным в текущем запросе. Данные сохраненной сущности должны содержаться в ответе на
текущий запрос.

При несоответствии вышеуказанным условиям или любой ошибке в процессе создания спецификации, в ответе на запрос должна
фигурировать причина неудачи.

### STORY: Изменение спецификации

Требуется REST API контроллер на стороне BE для изменения спецификации.

Требуется обеспечить возможность идентифицировать изменяемую спецификацию при помощи обязательного параметра пути
контроллера.

Требуется проверка авторизации пользователя. Требуется проверка наличия соответствующих прав для изменения
идентифицированной спецификации.

Контроллер должен получать данные, необходимые для изменения спецификации:

- название спецификации;
- краткое описание спецификации;
- список тегов;
- url спецификации в файловой иерархии;
- файл libsl-спецификации.

Url спецификации в файловой иерархии могут быть не предоставлены. Все остальные поля данных должны быть предоставлены.

Требуется проверка, что название спецификации не является пустой строкой.

Требуется проверка, что название специфицируемой библиотеки не является пустой строкой.

Если url спецификации в файловой иерархии предоставлен, требуется проверка, что данный url указывает на директорию.

Если url директории предоставлен, требуется проверка, что пользователь имеет соответствующие права для создания
спецификации в указанной директории.

Требуется проверка, что отсутствуют спецификации с указанным названием и родительской директорией, соответствующей
указанному url, полученному в текущем запросе.

Требуется верификация libsl-спецификации. Если для исходного текста libsl-спецификации ранее проводилась верификация
и имеются результаты верификации, повторная верификация не требуется.

Если вышеперечисленные условия соблюдены, на стороне BE должна быть изменена идентифицированная спецификация.
Родительская директория изменяемой спецификации должна соответствовать url, полученному в текущем запросе. Абстрактный
семантический граф libsl-спецификации, список ошибок верификации libsl-спецификации, список описаний графических
отображений конечных автоматов у новой спецификации должны соответствовать результатам верификации спецификации.
Название специфицируемой библиотеки должно быть должно соответствовать значению специальной конструкции `library` в
ASG. Название спецификации, краткое описание спецификации, список тегов и исходный текст libsl-спецификации у
изменяемой спецификации должны соответствовать данным, полученным в текущем запросе. Данные сохраненной сущности должны
содержаться в ответе на текущий запрос.

При несоответствии вышеуказанным условиям или любой ошибке в процессе изменения спецификации, в ответе на запрос
должна фигурировать причина неудачи.

### STORY: Удаление спецификации

Требуется REST API контроллер на стороне BE для удаления спецификации.

Требуется обеспечить возможность идентифицировать удаляемую спецификацию при помощи обязательного параметра пути
контроллера.

Требуется проверка авторизации пользователя. Требуется проверка наличия соответствующих прав для удаления
идентифицированной спецификации.

Если вышеперечисленные условия соблюдены, соответствующая спецификация на стороне BE должна быть удалена.

При несоответствии вышеуказанным условиям или любой ошибке в процессе удаления спецификации, в ответе запрос должна
фигурировать причина неудачи.

### STORY: Отображение списка спецификаций

Требуется реализовать на стороне FE отображения списка спецификаций, хранящихся на стороне BE.

Отображение должно содержать элемент хедера.

Если пользователь авторизован, в хедере требуется отображение кнопки перехода на страницу отображения формы управления
данными пользователя; также требуется кнопка выхода пользователя; также требуется кнопка для перехода на страницу
отображения настроек.

Если пользователь не авторизован, в хедере требуется кнопка перехода на страницу отображения аутентификации
пользователя; также требуется кнопка перехода на страницу отображения регистрации пользователя.

Отображение должно содержать секцию фильтров. Если пользователь авторизован, секция фильтров должна содержать
переключатель между режимом отображения всех спецификаций и режимом отображения спецификаций текущего пользователя.
Секция фильтров должна содержать выпадающий список выбираемых фильтров, перечень которого должен определяться на
стороне BE; при выборе фильтра из списка, в секции должно появиться текстовое поле для данного фильтра; при отмене
выбора, данное текстовое поле должно быть убрано.

Требуется отображение прокручиваемой секции с элементами, отображающими информацию об имеющихся спецификациях; перечень
отображаемых спецификаций должен определяться на стороне BE и соответствовать установленным фильтрам. Каждый элемент
списка должен отображать следующую информацию о соответствующей ей спецификации:

- название специфицируемой библиотеки;
- url спецификации;
- списки тегов, сгруппированные по их типу.

### STORY: Отображение формы создания спецификации

Требуется реализовать на стороне FE отображение для создания спецификации на стороне BE.

Отображение должно содержать элемент хедера.

Отображение должно содержать текстовое поле для указания название спецификации.

Отображение должно содержать элемент выбора директории.

Отображение должно содержать текстовое поле для краткого описания спецификации.

Требуются текстовые поля для тегов. Каждое текстовое поле должно соответствовать типу тега. Перечень типов тегов должен
определяться на стороне BE.

Если ранее не был предоставлен файл libsl-спецификации, отображение должно содержать drag & drop элемент для загрузки
файла libsl-спецификации.

Если был предоставлен файл libsl-спецификации, требуется отправить его на верификацию. Во время верификации исходного
текста libsl-спецификации требуется отображать информацию о том, что результаты верификации ожидаются.

Если для предоставленного файла libsl-спецификации имеются результаты верификации, требуется отображать элемент
отображения спецификации; также требуется кнопка для загрузки и верификации нового файла спецификации, заменяющего
ранее загруженный файл.

Отображение должно содержать кнопку для сохранения спецификации.

В случае неудачного сохранения спецификации, требуется отображение причины неудачи.

В случае успешного сохранения спецификации, требуется переход на страницу отображения сохраненной спецификации.

### STORY: Отображение спецификации

Требуется реализовать на стороне FE отображение для просмотра спецификации, хранящейся на стороне BE.

Требуется обеспечить возможность идентифицировать отображаемую спецификацию при помощи обязательного параметра пути
страницы отображения.

Отображение должно содержать элемент хедера.

Отображение должно содержать секцию информации об отображаемой спецификации:

- url спецификации;
- название специфицируемой библиотеки;
- краткое описание спецификации;
- списки тегов, сгруппированные по их типу.

Список тегов должен быть сгруппирован по ключам и отображен в виде нескольких подсписков.

Отображение должно содержать панель управления спецификацией. Содержание панели управления должно определяться на
стороне BE с проверкой авторизации пользователя. Если пользователь имеет права на изменения текущей спецификации,
требуется отображать кнопку для перехода на страницу изменения спецификации. Если пользователь имеет права на удаление
текущей спецификации, требуется отображать кнопку для удаления спецификации.

Отображение должно содержать элемент отображения спецификации.

### STORY: Отображение формы изменения спецификации

Требуется реализовать на стороне FE отображение для изменения спецификации на стороне BE.

Отображение должно содержать элемент хедера.

Отображение должно содержать текстовое поле для указания название спецификации. Поле должно быть заполнено текущим
значением изменяемой спецификации.

Отображение должно содержать элемент выбора директории. Url, хранящийся в элементе, должен соответствовать текущему
значению изменяемой спецификации.

Отображение должно содержать текстовое поле для краткого описания спецификации. Поле должно быть заполнено текущим
значением изменяемой спецификации.

Требуются текстовые поля для тегов. Каждое текстовое поле должно соответствовать типу тега. Перечень типов тегов должен
определяться на стороне BE. Поля должны быть заполнены текущими значениями тегов изменяемой спецификации.

Если для предоставленного файла libsl-спецификации имеются результаты верификации, требуется отображать элемент
отображения спецификации; также требуется кнопка для загрузки и верификации нового файла спецификации, заменяющего
старый файл спецификации.

Если был предоставлен новый файл libsl-спецификации, требуется отправить его на верификацию. Во время верификации
исходного текста libsl-спецификации требуется отображать информацию о том, что результаты верификации ожидаются.

Отображение должно содержать кнопку для сохранения спецификации.

В случае неудачного сохранения спецификации, требуется отображение причины неудачи.

В случае успешного сохранения спецификации, требуется переход на страницу отображения сохраненной спецификации.

Отображение должно содержать кнопку для отмены изменений спецификации.

## EPIC: Общие элементы отображений

### STORY: Элемент хедера

Требуется на стороне FE реализовать элемент хедера.

Элемент должен содержать в хедере кнопку перехода на страницу отображения списка спецификаций.

Если пользователь авторизован, элемент должен содержать кнопку перехода на страницу отображения формы управления
данными пользователя; также элемент должен содержать кнопку выхода пользователя; также элемент должен содержать кнопку
для перехода на страницу отображения настроек.

Если пользователь не авторизован, элемент должен содержать кнопку перехода на страницу отображения аутентификации
пользователя; также элемент должен содержать кнопку перехода на страницу отображения регистрации пользователя.

### STORY: Элемент отображения спецификации

Требуется на стороне FE реализовать элемент отображения спецификации.

Элемент должен содержать секцию исходного текст libsl-спецификации. Секция должна быть прокручиваемой. Требуется
обеспечить возможность подсветки синтаксических конструкций текста спецификации. Требуется возможность конфигурировать
параметры синтаксической подсветки. Требуется особое оформление всех фрагментов исходного текста, в которых были
выявлены ошибки на этапе верификации спецификации; требуется при наведении на фрагмент с ошибкой выводить сообщение
связанной ошибки.

Если для отображаемой спецификации имеются графические отображения КА, элемент должен их отображать. Требуется
обеспечить поддержку 2 типов отображений КА:

- отображение отдельного КА;
- отображение всех КА.

Отображение отдельного КА должно отображать следующие элементы:

- название КА;
- состояния КА, включая их названия; конечные состояния КА должны иметь оформление, отличное от обычных состояний КА;
- переходы между состояниями КА, включая названия функций, вызов которых инициирует соответствующий переход;
- локальные переменные КА, включая их названия.

Отображение всех КА должно отображать следующие элементы:

- область КА, включая его название.
- состояния КА, включая их названия; состояния должны находиться внутри области КА, к которому они относятся; конечные
  состояния КА должны иметь отображение, отличное от обычных состояний КА;
- переходы между состояниями одного КА, включая названия функций, вызов которых инициирует соответствующий переход;
- переходы между состояниями разных КА, характеризующие создание дочернего КА, включая названия функций,
  вызов которых инициирует соответствующее создание КА;
- локальные переменные КА, включая их названия; локальные переменные должны находиться внутри области КА, к которому
  они относятся.

Если для отображаемой спецификации имеется больше одного графического отображения КА, требуется возможность выбирать
отображение.

Для всех графических отображений должна быть возможность изменять масштаб (приближение/удаление) и перемещать
отображение, если оно не может быть полностью отображено на экране.

При нажатии на синтаксическую конструкцию в исходном тексте libsl-спецификации, декларирующую состояние КА, требуется
особое выделение этой конструкции в исходном тексте; также требуется особое выделение соответствующего состояния КА в
графических отображениях; если выбранное состояние КА не отображено в выбранном отображении КА, требуется выбрать общее
отображение всех КА; на текущем отображении требуется приблизить и центрировать выбранное состояние КА.

При нажатии на синтаксическую конструкцию в исходном тексте libsl-спецификации, декларирующую переход между состояниями
КА или создание дочернего КА, требуется особое выделение этой конструкции в исходном тексте; также требуется особое
выделение соответствующих переходов КА в графических отображениях.

При нажатии на состояние КА в графическом отображении требуется особое выделение этого состояния в графическом
отображении; также требуется особое выделение соответствующей этому состоянию конструкции в исходном тексте; требуется
фокусировка соответствующей этому состоянию конструкции в исходном тексте спецификации.

При нажатии на переход между состояниями или создание дочернего КА в графическом отображении требуется особое выделение
этого перехода в графическом отображении; также требуется особое выделение соответствующей синтаксической конструкции в
исходном тексте спецификации; требуется фокусировка соответствующей этому переходу в исходном тексте спецификации.

Если для отображаемой спецификации имеются графические отображения КА, требуется панель управления отображением
элемента, позволяющее выбрать одну из 3 опций:

- отображать только исходный текст спецификации;
- отображать только графические отображения КА;
- отображать исходный текст и графических отображений КА одновременно.

По умолчанию должна быть выбрана опция отображения исходного текста и графических отображений КА одновременно.

### STORY: Элемент выбора директории

Требуется на стороне FE реализовать элемент отображения спецификации.

Элемент должен хранить url выбранной директории. По умолчанию элемент должен содержать url корневой директории.

Элемент должен иметь 2 варианта отображения:

- текстовое поле для отображения url выбранной директории;
- модальное окно для выбора директории в файловой иерархии.

## EPIC: Развертывание системы

### STORY: Создание docker-compose инфраструктуры

На данный момент планируется деление проекта на три контейнера:

- БД
- Frontend - веб-сервер
- Backend - реализация бизнес-логики

Для обеспечения взаимодействия контейнеров между собой планируется использование двух внутренних docker-сетей:

- БД-backend
- Backend-frontend

Следует отметить, что контейнер с веб-сервером должен иметь доступ к внешней сети для протоколов http/https.

### STORY: Настройка веб-сервера

Необходимо обеспечить как сборку проекта с фронтэндом, так и развёртывание артефактов сборки (сайт) на веб-сервере
(e.g. Nginx).

Так как требуется обеспечить подключение как с использованием http, так и с использованием https, необходимо
озаботиться сертификатами (использовать let's encrypt).

### STORY: Настройка бэкэнда

Необходимо обеспечить сборку бэкэнда с использованием выбранной системы сборки (e.g. Gradle) и запуском полученного в
результате сборки артефакта.

### STORY: Настройка БД

Необходимо настроить запуск контейнера с БД (e.g. Postgres) с сохранением данных в volume и первичной инициализацией
при отсутствии данных (пустом volume). Данные для доступа к БД будут находиться во внешнем .conf-файле и записываться в
глобальные переменные бэкэнд-контейнера при его запуске.

### STORY: Настроить CI

В рамках реализации CI необходимо настроить автоматический запуск автотестов при появлении MR или коммите в main и
develop-ветки.

### STORY: Настроить CD

Необходимо настроить автосборку контейнера и его выгрузку в docker registry для последующего автоматического обновления
на конечном сервере с использованием watchtower-контейнера.
