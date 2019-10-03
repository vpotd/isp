# Где коммутатор?

Была заявка на замену коммутаторов на Иванова 7 и 9. На иванова 3 все заменой, а на Иванова 9 2 замены + 1 новый

![](../../.gitbook/assets/image%20%2824%29.png)

Все сделали, в EqM завели

....спустя какое то время.....

Письмо от ведущего инженера по техническому учету:

![](../../.gitbook/assets/image%20%2830%29.png)

### Начало разбора

Решили проверить все письма по улице Иванова за последнее время.

Письмо от руководителя участка, территориальный отдел:

![](../../.gitbook/assets/image%20%2868%29.png)

Далее письмо от инженера активного оборудования, отдел Головной станции

![](../../.gitbook/assets/image%20%283%29.png)

Как видим коммутатора с адресом \#.\#.\#.3 нет в EqM

![](../../.gitbook/assets/image%20%2867%29.png)

Далее письмо от ведущего инженера по техническому учету, **с него как раз таки и началась история**

![](../../.gitbook/assets/image%20%2830%29.png)

В выводе мак адресов было видно что за одним ip-адресом висит 2 мака, подумали что петля. Но после проверки конфигов которые туда заливались, поняли что у нас в сети 2 устройства с одним ip-адресом. 

Сменили адрес на одном из коммутаторов и очистили ARP-таблицу на DGS \(МКУ\), все устройства стали доступны. Отрисовали всё полученной в EqM \(на основе lldp\).

![](../../.gitbook/assets/image%20%2811%29.png)

![](../../.gitbook/assets/image%20%2840%29.png)

После этого я отправил письмо ведущему инженеру по техническому учету

![](../../.gitbook/assets/image%20%2842%29.png)

История положительно закончилась письмом от ведущего инженера по техническому учету

![](../../.gitbook/assets/image%20%2847%29.png)

Как то так 






