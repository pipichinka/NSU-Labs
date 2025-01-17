# SOCKS-прокси

## Задание

1. Необходимо реализовать прокси-сервер, соответствующий стандарту *SOCKS* версии 5.
2. В параметрах программе передаётся только порт, на котором прокси будет ждать входящих подключений от клиентов.
3. Из трёх доступных в протоколе команд, обязательной является только реализация команды 1 (`establish a TCP/IP stream connection`)
4. Поддержку аутентификации и IPv6-адресов реализовывать не требуется.
5. Для реализации прокси использовать неблокирующиеся сокеты, работая с ними в рамках одного треда. Дополнительные треды использовать не допускается. Соответственно, никаких блокирующихся вызовов (кроме вызова селектора) не допускается.
6. Прокси не должна делать предположений о том, какой протокол уровня приложений будет использоваться внутри перенаправляемого TCP-соединения. В частности, должна поддерживаться передача данных одновременно в обе стороны, а соединения должны закрываться аккуратно (только после того, как они больше не нужны).
7. В приложении не должно быть холостых циклов ни в каких ситуациях. Другими словами, не должно быть возможно состояние программы, при котором неоднократно выполняется тело цикла, которое не делает ни одной фактической передачи данных за итерацию.
8. Не допускается неограниченное расходование памяти для обслуживания одного клиента.
9. Производительность работы через прокси не должна быть заметно хуже, чем без прокси. Для отслеживания корректности и скорости работы можно глядеть в *Developer tools* браузера на вкладку *Network*.
10. Прокси должен поддерживать резолвинг доменных имён (значение `0x03` в поле `address`). Резолвинг тоже должен быть неблокирующимся. Для этого предлагается использовать следующий подход:
	* На старте программы создать новый UDP-сокет и добавить его в селектор на чтение
	* Когда необходимо отрезолвить доменное имя, отправлять через этот сокет DNS-запрос A-записи на адрес рекурсивного DNS-резолвера
	* В обработчике чтения из сокета обрабатывать случай, когда получен ответ на DNS-запрос, и продолжать работу с полученным адресом  
Для получения [адреса рекурсивного резолвера](https://stackoverflow.com/questions/21923682/getting-the-address-of-a-dns-server-programmatically-in-java/51844866#51844866), а также для [формирования и парсинга DNS-сообщений](https://javadoc.io/doc/dnsjava/dnsjava/latest/org/xbill/DNS/Message.html) на Java предлагается использовать библиотеку [dnsjava](https://github.com/dnsjava/dnsjava) (для других языков найдите сами).

Для тестирования можно настроить любой Web-браузер на использование вашего прокси, и посещать любые веб-сайты, богатые контентом.

## Описание протокола

* [На английской Википедии](https://en.wikipedia.org/wiki/SOCKS)
* [SOCKS 5 RFC](https://www.ietf.org/rfc/rfc1928.txt)
* [SOCKS для самых маленьких](http://fit.ippolitov.me/CN_2/2022/socks.jpg)
