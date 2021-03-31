# Группа бэкендов

Группа бэкендов определяет настройки, на основании которых L7-балансировщик будет подавать трафик на эндпоинты [целевых групп](target-group.md): протокол для соединения с экземплярами приложения, настройки проверок состояния эндпоинтов и правила распределения трафика между ними.  

Группа бэкендов состоит из списка бэкендов. Каждый бэкенд указывает на набор целевых групп, содержащих эндпоинты приложения. Каждому бэкенду можно задать относительный вес. Трафик между бэкендами будет распределяться пропорционально этим весам. В рамках одного бэкенда трафик между эндпоинтами целевых групп распределяется равномерно с учетом результатов работы [проверок состояния](#health-checks) и [локализации трафика](#locality). Протоколы, проверки состояния и распределение трафика настраиваются для каждого бэкенда отдельно. Использование нескольких бэкендов в одной группе облегчает разделение трафика между разными версиями приложения при обновлении или экспериментах.

## Настройки протоколов внутри группы бэкендов {#protocol-settings}

Поддерживаются бэкенды, реализующие протоколы HTTP/1 и HTTP/2. Передавать запросы можно как открытым текстом, так и с использованием TLS в HTTPS-бэкендах. При использовании TLS балансировщик по умолчанию не валидирует сертификаты, отдаваемые бэкендами. Но есть возможность указать сертификаты удостоверяющих центров, которым балансировщик будет доверять при установке безопасного соединения с эндпоинтами бэкендов.

### Режим паники {#panic-mode}

Режим паники позволяет защититься от отказа всех экземпляров приложения при резком росте нагрузки.
В этом режиме балансировщик будет распределять запросы во все эндпоинты, не учитывая результаты проверок состояния. Вы можете задать процент здоровых эндпоинтов, при котором включится режим паники.

Если режим паники не используется, отказ части бэкендов вызовет рост нагрузки на еще работающие бэкенды. Если приложение работает на пределе своих возможностей, это приведет к отказу всех бэкендов и полной недоступности сервиса. При включенном режиме паники трафик снова начнет распределяться между всеми эндпоинтами и, хотя часть запросов будет завершаться ошибкой, сервис останется работоспособным. Это даст время увеличить вычислительные ресурсы приложения [автоматически](../../compute/concepts/instance-groups/scale.md#auto-scale) или вручную.

### Локализация трафика {#locality}

По умолчанию балансировщик равномерно распределяет трафик между всеми эндпоинтами целевых групп бэкенда. Если приложение находится в нескольких зонах доступности, можно настроить L7-балансировщик так, чтобы он отправлял запросы эндпоинтам той зоны доступности, в которой балансировщик принял запрос. Если в этой зоне доступности нет работающих бэкендов, балансировщик отправит запрос в другую зону. 

Если включена строгая локализация, балансировщик будет отвечать ошибкой (503 Service Unavailable), если в зоне доступности, где был принят запрос, нет работающих бэкендов приложения.

## Проверки состояния {#health-checks}

Балансировщик будет отправлять проверочные запросы к указанным эндпоинтам через определенные промежутки времени и ожидать ответа в течение определенного периода.

Поддерживаются проверки по протоколам HTTP и TCP. Поддерживаются следующие настройки этих проверок:

* Таймаут — время ожидания ответа.
* Интервал — интервал времени между проверочными запросами.
* Показатели состояния ресурса: пороги количества удачных или неудачных результатов проверок, при превышении которых проверка будет считаться пройденной или непройденной.
* Настройки HTTP-проверок:
	* Имя хоста для заголовка `host`.
	* Путь к эндпоинту для проверки состояния.
	* Флаг использования протокола HTTP/2.
* Настройки TCP-проверок:
	* Тело запроса.
	* Подстрока в ответе, которая будет означать успешное прохождение проверки. Если тело запроса или ответа не указано, будет проверяться успех соединения с бэкендом.

Обратите внимание, что если бэкенд настроен на использование TLS с эндпоинтами целевых групп, проверки тоже будут производиться по протоколу TLS. Например, если используется HTTP-проверка, она будет происходить по протоколу HTTPS, а для TCP-проверок будет установлено TLS-соединение и результаты проверок будут возвращаться через это соединение.

