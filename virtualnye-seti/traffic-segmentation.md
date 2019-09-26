# Traffic segmentation

### **Настройка**

Все абоненты, подключенные к одному коммутатору доступа, работают в одном VLAN-е. Такая схема имеет два существенных недостатка:   
\* Абоненты могут оказывать взаимное влияние на трафик друг друга;  
\* Абоненты могут обмениваться неавторизованным трафиком;  
Обе проблемы решаются настройкой функционала traffic segmentation.

Трафик Служебных портов не должен форвардится на остальные интерфейсы. И наоборот, трафик с остальных интерфейсов не должен форвардится на служебные порты!

Запрещаем форвардинг трафика со служебных портов:

| `config traffic_segmentation <SVCPORTS> forward_list null` |
| :--- |


Разрешаем форвардинг трафика с абонентских интерфейсов только на магистральные порты кольца. Обратите внимание, что с интерфейсов &lt;BRNPORTS&gt;, используемых для подключения отводов, трафик также форвардится только на порты &lt;MAGPORTS&gt;.

| `config traffic_segmentation <ACCPORTS>,<BRNPORTS> forward_list <MAGPORTS>` |
| :--- |


Разрешаем форвардинг трафика с магистральных портов кольца на все остальные интерфейсы:

| `config traffic_segmentation <MAGPORTS> forward_list <ACCPORTS>,<BRNPORTS>,<MAGPORTS>` |
| :--- |


### Пример

`#TRAF-SEGMENTATION  
config traffic_segmentation 1-25,27 forward_list 28 config traffic_segmentation 26 forward_list 25 config traffic_segmentation 28 forward_list 1-28`

