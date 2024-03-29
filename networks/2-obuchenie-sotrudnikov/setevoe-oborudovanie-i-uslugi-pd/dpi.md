# DPI

### **Что такое DPI**

Механизм DPI \(**Deep Packet Inspection**\), исходя из названия, основан на глубоком анализе пакетов \(от 2го и выше уровней модели OSI\) и накоплении статистической информации. Основная его идея - реализация QoS и Subscriber Management. 

Комплексы Deep Packet Inspection могут принимать решение о действия по отношению к анализируемому трафику не только по содержимому пакетов, но и по косвенным признакам, присущим каким-то определённым сетевым программам и протоколам. Для этого может использоваться статистический анализ \(например статистический анализ частоты встречи определённых символов, длины пакета и т. д.\).

### **QoS**

С точки зрения эксплуатации, оператор может контролировать утилизацию подключенных через DPI каналов на уровне приложений. Раньше он решал задачи реализации QoS \(Quality of Service\) исключительно средствами построения очередей на основании маркировки трафика служебными битами в заголовках IP, 802.1q и MPLS, выделяя наиболее приоритетный трафик \(разного рода VPN’ы, IPTV, SIP и т.д.\), и гарантируя ему определённую пропускную способность в любой момент времени. Трафик типа Best Effort, к которому относится весь интернет трафик домашних абонентов \(HSI — High Speed Internet\), оставался фактически без контроля, что давало возможность тому же Bittorrent забрать себе всю свободную полосу, что, в свою очередь, вело к деградации любых других веб-приложений. С использованием DPI у оператора появляется возможность распределить канал между различными приложениями. К примеру, в ночные часы разрешить трафику Bittorrent забирать себе больше полосы, чем днём, в часы-пик, когда в сети ходит большое количество другого веб-трафика. Другая популярная мера у многих мобильных операторов – блокировка Skype-трафика, а также любых видов SIP-телефонии. Вместо полной блокировки оператор может разрешать работу данных протоколов, но на очень низкой скорости с соответствующей деградацией качества предоставления сервиса у конкретного приложения, чтобы вынудить пользователя платить за услуги традиционной телефонии, либо за специальный пакет услуг, разрешающий доступ к VoIP-сервисам.

### **Subscriber Management**

Важным моментом является то, что правила, на основании которых выполняется шейпинг/блокировка, могут быть заданы посредством двух основных базисов – per-service или per-subscriber. В первом случае простейшим образом оговаривается, что конкретному приложению позволяется утилизировать определённую полосу. Во втором привязка приложения к полосе осуществляется для каждого подписчика или группы подписчиков независимо от других, что производится через интеграцию DPI с существующими OSS/BSS системами оператора. 

Т.е. можно настроить систему таким образом, что подписчик Вася, который за неделю накачал торрентов на 100 гигабайт, до конца месяца будет ограничен по скорости скачивания этих же торрентов на уровне 70% от купленного им тарифа. А у подписчика Пети, который купил дополнительную услугу под названием «Skype без проблем», трафик приложения Skype не будет блокироваться ни при каких условиях, но любой другой – легко. 

Можно сделать привязку к User-Agent и разрешить браузинг только при помощи рекомендуемых браузеров, можно делать хитрые редиректы в зависимости от типа браузера или ОС. Иными словами, гибкость тарифных планов и опций ограничена лишь здравым смыслом. Если же речь идёт о трафике мобильных операторов, то DPI позволяет контролировать загрузку каждой базовой станции в отдельности, справедливо распределяя ресурсы БС таким образом, чтобы все пользователи остались довольны качеством сервиса. Разумеется, данную задачу можно решать силами мобильного ядра, но это не всегда бюджетно. Раз уж я упомянул мобильных операторов, то хотелось бы отметить, что каждый уважающий себя производитель пакетного ядра EPC \(Evolved Packet Core\) для LTE интегрирует в свой PDN-GW функционал DPI, заточенный под решение задач мобильных операторов.  


### **DPI на нашей сети. Основные функции**

В качестве системы DPI на нашей сети выступает Equila \(Эквила\). Эквила - это программно-аппаратный комплекс, используется для улучшения качества предоставляемых абонентам услуг путём более эффективного использования внешних каналов. 

Используемое у нас оборудование это карты обработки сетевого трафика на основе процессоров от \#\#\#\# и сервер с управляющим ПО, разработанным компанией \#\#\#\#.

Основные функции выполняемые DPI:

* мониторинг внешних каналов, используется система мониторинга EQUILA, в которой отслеживается \(**Все параметры в системе EQUILA снимаются и публикуются раз в 5 минут**\):
  * загрузка общего трафика - данные снимаются snmp-опросом счетчиков трафика на физических портах коммутаторов, куда подключен внешний оператор;
  * загрузка трафика с разбивкой по протоколам - данные получены после обработки и классификации отзеркалированного трафика сервером EQUILA;
  * QoS \(Quality of Service\) - задержка и потери пакетов до популярных ресурсов через конкретного оператора по протоколам IPv4 и IPv6 - статистика снимается по каждому логическому каналу после выполнения Ping и Ping6;
  * состояние BGP-сессии с внешним оператором - данные снимаются snmp-опросом маршрутизатора, на котором терминируется BGP сессия \(BGW\).
* Приоритезация трафика, позволяет осуществлять контроль над «жадными» протоколами \(p2p\), возможность ограничения количества одновременных соединений для таких протоколов. Возможность выделения и приоритезации в общей полосе трафика определенных пользователей и групп \(трафик ЮЛ/Wi-Fi и т.п.\)
* URL фильтрация трафика в соответствии с ФЗ РФ от 28.06.2012 г. N139-ФЗ \(Список запрещенных ресурсов синхронизируется с Единым Реестром РКН [http://eais.rkn.gov.ru/](http://eais.rkn.gov.ru/) в автоматическом режиме, имеется возможность добавлять URL страниц не включенные в список РКН. Например, экстремистские ресурсы запрещенные локальными судебными инстанциями.\) Блокировка происходит на уровне URL, что позволяет абонентам пользоваться разрешенными ресурсами с IP-адреса, на котором есть запрещенные web-страницы. Это позволяет повышать лояльность клиентов. Способы обнаружения запросов:
  1. Анализ HTTP заголовков
  2. Анализ DNS запросов
* Коммуникация с абонентом. DPI позволяет осуществлять редирект клиентских http-запросов на специально подготовленную страницу с информацией об оплате, новых тарифах и т.п. Информация на такой странице может быть персонализированной
* Построение поведенческих отчетов. Информация о том, какие ресурсы посещают абоненты провайдера, насколько активно они это делают, какими браузерами при этом пользуются и т.п.
* Родительский контроль, включенная функция позволяет осуществлять фильтрацию для отдельно взятого абонента:
  1. Экстремистских материалов
  2. Нецензурной лексики
  3. Азартных игр
  4. Информации для взрослых
* Антиспам. DPI может ограничивать количество одновременных соединений по протоколу SMTP \(либо блокировать совсем\), что позволяет избегать попадания IP адресов провайдера в «черные списки» компаний, занимающихся мониторингом рассылок спама.
* Сбор статистической информации о сети: данные о количестве одновременных абонентских сессий и в разрезе протоколов, удельные показатели скорости абонентов, количество трафика генерируемое различными ресурсам/приложениями
* Коррекция TCP MSS для IPv6. Механизм изменения TCP MSS позволяет на лету корректировать значения MSS для корректного согласования максимального размера пакета

### **Схема включения DPI**

Как известно, физическое подключение магистральных внешних каналов произведено к коммутатору ядра сети CSW через порты TenGigabitEthernet от вышестоящего коммутатора магистральной сети BSW.  
Логическое включение внешних каналов происходит между маршрутизаторами BSR и BGW посредством организации и установки BGP-сессии.

Так же, данная система используется для мониторинга состояний внешних каналов, загрузки потерь пакетов и т.п. Данные для мониторинга загрузки каналов и состояниях BGP сессий берутся с оборудования по SNMP протоколу. Принцип работы Ping через определённый канал заключается в том, что каждому конкретному апстриму анонсируется сеть /24  этим достигается то, что пакеты пойдут в обе стороны через нужный канал, так как это будет наиболее специфичный маршрут. При деградации ключевых показателей каналов срабатывают триггеры, которые отображаются в web интерфейсе. Можно так же выполнять команды на  сервере в каждом городе вручную, доступные команды ping, traceroute, mtr, находятся в пункте меню команды. В эквиллу зеркалируется radius аккаунтинг, сделано это для того чтобы сервер эквиллы знал соответствие логинов абонентов текущему выданному ip адресу. Это необходимо для работы услуги родительский контроль, для всяких маркетинговых акций \(реклама для клиентов и т.п.\). Так же данное решение используется для анализа потребления трафика конкретными абонентами, а также для выявления наиболее популярных генераторов контента.

### Примеры того, как работает DPI

* Идентификация протокола транспортного уровня сетевой модели OSI. В структуре пакета протокола IPv4 выделен специальный байт для указания номера протокола транспортного уровня. Им является десятый байт от начала заголовка IPv4 пакета. Например: 6 - для TCP протокола, 17 - для UDP протокола. В структуре пакета IPv6 так же существует специальная область, в которой находится аналогичный идентификатор протокола транспортного уровня. Это область носит название Next Header.
* Клиенты BitTorrent соединяются с трекером по протоколу TCP. Для того, чтобы обнаружить среди всего трафика TCP такие пакеты, достаточно проверить, что содержимое данных TCP пакета со второго байта совпадает с «BitTorrent protocol». Соответственно, чтобы зашейпить у пользователя торрент-трафик - достаточно в исходящие tcp-пакеты подменить на tcp-reset/tcp-fin. Подобные вещи можно производить с любым трафиком, порт которого зарегистрирован в IANA
* Идентификация HTTP. Для идентификации HTTP протокола достаточно проверить, что пакет является TCP, и содержимое этого TCP пакета начинается с одной из следующих команд: «GET», «POST», «HEAD». Кроме того, после команды должен стоять пробел, а также через некоторый промежуток должен встретиться текст «HTTP/». Если всё это выполняется, то этот пакет несёт в себе HTTP запрос.
* Идентификация RTSP. Для того, чтобы обнаружить среди всего трафика пакеты RTSP достаточно убедиться, что пакет является TCP, и содержимое этого TCP пакета начинается с одной из следующих команд: «OPTIONS», «DESCRIBE», «ANNOUNCE», «PLAY», «SETUP», «GET\_PARAMETER», «SET\_PARAMETER», «TEARDOWN». После команды должен стоять пробел. Так же, через некоторый промежуток должен встретиться текст «RTSP.
* Исполнение статьи 15 [федерального закона N 149-ФЗ «Об информации, информационных технологиях и о защите информации»](http://ru.wikipedia.org/wiki/%D0%A4%D0%B5%D0%B4%D0%B5%D1%80%D0%B0%D0%BB%D1%8C%D0%BD%D1%8B%D0%B9_%D0%B7%D0%B0%D0%BA%D0%BE%D0%BD_%C2%AB%D0%9E%D0%B1_%D0%B8%D0%BD%D1%84%D0%BE%D1%80%D0%BC%D0%B0%D1%86%D0%B8%D0%B8,_%D0%B8%D0%BD%D1%84%D0%BE%D1%80%D0%BC%D0%B0%D1%86%D0%B8%D0%BE%D0%BD%D0%BD%D1%8B%D1%85_%D1%82%D0%B5%D1%85%D0%BD%D0%BE%D0%BB%D0%BE%D0%B3%D0%B8%D1%8F%D1%85_%D0%B8_%D0%BE_%D0%B7%D0%B0%D1%89%D0%B8%D1%82%D0%B5_%D0%B8%D0%BD%D1%84%D0%BE%D1%80%D0%BC%D0%B0%D1%86%D0%B8%D0%B8%C2%BB) - так называемый "[Единый реестр запрещённых сайтов](http://zapret-info.gov.ru/)" - В HTTP url можно взять из GET-запроса.
* Реализация различных маркетинговых услуг, например - "Сессия на отлично" - так же с помощью определения url/ip/номеров портов.

