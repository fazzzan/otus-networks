# DMVPN и IPSec

###  Задание:

1. Настроить DMVPN в фазе 3;
2. Перевести маршрутизаторы на работу с IPSec для защиты DMVPN;
3. Задокументировать все изменения.



###  Решение:

  Переведём DMVPN из фазы 2 в фазу 3 добавив ```ip nhrp redirect``` в конфигурацию интерфейса на DMVPN hub, а на DMVPN spoke ```ip nhrp redirect``` и ```ip nhrp shortcut```. Для IPv6 аналогично: ```ipv6 nhrp redirect``` и ```ipv6 nhrp redirect```. 

  Далее синхронизируем время через NTP: настроим клиенты NTP на R5 и R15 и разрешим на сервере R9 подключение этих клиентов к нему.


###  Схема NTP

![](ntp3.png)

  На R5 нужно получить клиентский сертификат от CA. Процедура такая:
   - Зададим имя домена;
   - Генерируем ключевую пару, которую будем использовать для IPSec;
   - Создаем точку доверия (trust point) и указываем R9;
   - Подтверждаем, что мы доверяем этому CA;
   - Запрашиваем себе сертификат.


  Аналогичная процедура требуется на R13: 
   - Зададим имя домена;
   - Генерируем ключевую пару, которую будем использовать для IPSec;
   - Создаем точку доверия (trust point) и указываем К9;
   - Подтверждаем, что мы доверяем этому CA;
   - Запрашиваем себе сертификат.
  

  Сейчас у сервера PKI на R9 в очереди два запроса на выдачу сертификатов.
   - Подтвердим запросы на сертификаты, например, командой ```crypto pki server R9 grant all```;
   - Дождемся, пока ответы на запросы дойдут обратно.

  
  Теперь можно настраивать IPSec:
   - Зададим политику ISAKMP, в частности, включим проверку подлинности по сертификатам;
   - Зададим политику шифрования IPSec;
   - Применим защиту навесив нужный профиль IPSec на нужные интерфейсы маршрутизаторов R1, R5 и R13.

  Все файлы изменений приведены [здесь](configs/).

###  Схема получения сертификатов для IPSec

![](CA2.png)
