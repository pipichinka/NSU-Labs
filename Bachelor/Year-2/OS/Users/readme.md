# Понятие пользователя. Управление правами

1. [Использование *SUID*-бита для доступа к файлам](suid):
	1. Создайте файл с правами "чтение только владельцу".
	2. Напишите программу, которая выводит:
		1. содержимое этого файла.
		2. реальный и эффективный идентификаторы пользователя
	3. Запустите программу из-под своего пользователя и из-под чужого.
	4. Объясните результат.
	5. Установите *SUID*-бит.
	6. Запустите программу из-под своего пользователя и из-под чужого.
	7. Объясните результат.
