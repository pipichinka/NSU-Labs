# Сеть
1. [UDP](src/1) - эхо сервер:
	1. Сделайте UDP-сервер, который принимает данные от клиентов и пересылает их обратно клиенту.
	2. Напишите UDP-клиента, для теста UDP-сервера.
	3. Проверьте, что UDP-сервер, работает с несколькими клиентами.
2. [ТСP](src/2) - эхо сервер:
	1. Сделайте TCP-сервер, который
		1. принимает соединения от клиентов на заданном ip и port.
		2. создает новый процесс, в котором:
			1. читает данные от клиента;
			2. пересылает их ему обратно.
	2. Напишите TCP-клиента для проверки TCP-сервера.
	3. Проверьте, что TCP-сервер работает с несколькими клиентами и обрабатывает сессии в разных процессах.

3. [Реализуйте](src/3) задачу из пункта 2 при помощи мультиплексирования ввода-вывода
`poll(2)`/`select(2)`.