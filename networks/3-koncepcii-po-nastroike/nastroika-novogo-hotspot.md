# Настройка нового Hotspot

Тикет в Jira:

![](../../.gitbook/assets/image%20%2812%29.png)

Новые точки по хот-спотам настраиваем через контроллер.   
От нас нужно дотащить два влана до контроллера:   
1 - для управления - всегда \#\#  
2 - для сервиса 

Сами с точками доступа ничего не делаем!

### Фото и MAC-адрес

Запросить фото мак адреса и серийного номера у техника который будет ставит точки на сеть. 

### Выделение влана для сервиса

Смотрим конфиг браса в NOC !  
Ищем по внешнему &lt;тегу&gt; и началу влана \#\#хх.   
То есть, последовательно, ищем:   
&lt;тег&gt;.\#\#\#0 - что-то находим.   
&lt;тег&gt;.\#\#\#1 - что-то находим.   
&lt;тег&gt;.\#\#\#2 - что-то находим.   
&lt;тег&gt;.\#\#\#3 - ничего нет. Значит, наш сервисный влан будет \#\#\#3.

Оставляем комментарий в тикете. lag-\#\#\#\# определяется по ПГС

```text
"Название клиента"
Alcatel lag-####:<тег>.<сервисный влан>
Alcatel lag-####:<тег>.<влан управления>

Влан для ARMа <тег>.<сервисный влан>
```

Назначаем другого исполнителя, того у кого есть доступ до BRAS'а

### Проброс вланов

На порт коммутатора влан управления отдается **аксессом**, клиентский влан - **транком**. Не забываем поправить **аксесс-листы**, прописать на аплинках оба этих влана.

Прокидываем влан на всех коммутаторах до DGS.   
На самой DGS влан управления уже скорее всего создан, а вот клиентский влан скорее всего придется создать. И добавляем их оба на нужный нам порт, а клиентский влан еще и на аплинки.

```text
create vlan <сервисный влан> tag <сервисный влан>
config vlan <сервисный влан> add tagged 15, 25-26
config vlan <влан управления> add tagged 15
```

Обязательно настраиваем QinQ. Иначе работать не будет. Для обоих вланов! Номер порта в сторону от МКУ до нашей сети

```text
create vlan_translation ports ## replace outer_vid <сервисный влан> svid <сервисный влан>
create vlan_translation ports ## replace outer_vid <влан управления> svid <влан управления>
```

### Получаем IP-адрес для хот-спота

Далее отправить эти данные на рассылку "ЦП\__рассылка\__Межрегиональные инженеры беспровод оборуд;" для того чтобы нам выдали IP-адрес. 

```text
1)Название клиента:
Название клиента
2)Адрес размещения
ул. Спартаковская, 12
3)Номер сервисного VLAN
<сервисный влан>
4)Мак адрес:
<MAC>
5)SN: 
<SN>
```

### Заведение точек доступа в EqM

Точки доступа рисуем в отдельной папке &lt;Название города 16&gt; -&gt; Юридические лица -&gt; Hotspots -&gt; группа с нашим адресом -&gt; группа конкретно нашего абонента.   
В эту группу добавляем коммутатор, с которого будет подключен хотспот, и рисуем сам хотспот. Сначала хотспот добавляем в группу Стройка.   
Убираем оттуда только после фактической установки и звонка монтажника!
