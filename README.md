## System Design социальной сети для курса по [System Design](https://balun.courses/courses/system_design)
### Функциональные требования
-   публикация постов из путешествий с фотографиями, небольшим описанием и привязкой к конкретному месту путешествия;
-   оценка и комментарии постов других путешественников;
-   подписка на других путешественников, чтобы следить за их активностью;
-   поиск популярных мест для путешествий и просмотр постов с этих мест;
-   просмотр ленты других путешественников;

### Нефункциональные требования

 **DAU - 10 000 000
 Доступность - 99.95%
 Сезонность x2 - декабрь, январь, июнь-август
 Данные храним всегда
 Аудитория - СНГ**
 - Лимиты
	 - Максимально число подписчиков у пользователя - 1 000 000
	 - Не более 20 постов в день
	 - Не более 1000 реквестов на чтение в день
	 - Не более 1000 комментов в день
	 - Длина комента - 250 символов
	 - Длина поста - 4000 символов
 - Тайминги 
	 - публикация поста без фото (1 сек)
	 - публикация поста с фото, в зависимости от кол-ва фото (~ сек)
		 - максимум 10 фото, и не более 1Мб/шт ~ 10mb
	- оценка поста (1 сек)
	- комментирование поста (1 сек)
	- подписка на других (1 сек)
	- загрузка поста (2 сек)
	- поиск популярных мест (2 сек)
	- просмотр ленты с подгрузкой по 10 постов (3 сек)
- Активность
	- Запись
		- публикация поста с фото - 10 в месяц с 10 фото
		- оценка поста - 30 в месяц
		- комментирование поста - 15 в меяц
		- подписка на других - 5 в месяц
	- Чтение
		- открытие поста с комментариями - 10 раз в месяц
		- просмотр ленты - 10 раз в месяц по 20 минут - 4 часа, 200 постов в месяц
		- поиск популярных мест - 5 раз в месяц по 10 популярных мест с постами, 50 постов в месяц
- Нагрузка
	- RPS запись = 233
		- публикация поста с фото - (10000000 * (10/30)) / 86400 = 39
		- оценка поста - (10000000 * (30/30)) / 86400 = 116
		- комментирование поста - (10000000 * (15/30)) / 86400 = 58
		- подписка на других - (10000000 * (5/30)) / 86400 = 20
	- RPS чтение = 831
		- открытие поста с комментариями и фото - (10000000 * (10/30)) / 86400 = 39
		- просмотр ленты с 5 фото и без коментов - (10000000 * (200/30)) / 86400 = 772
		- поиск популярных мест - (10000000 * (5/30)) / 86400 = 20
- Транзакция
	- Post
		- id - BIGINT 8 байт
		- date - TIMESTAMP 8 байт
		- title - VARCHAR(255) 255 байт
		- text - TEXT 8000 байт
		- author - VARCHAR(100) 100 байт
		- geo - VARCHAR(50) 50 байт
		- rating - DECIMAL(5,2) 5 байт
		- photo - BYTEA 10 Mb
	- Comment
		- comment id - BIGINT 8 байт
		- text - TEXT 2000 байт
		- who commented - VARCHAR(100) 100 байт
		- date commented - TIMESTAMP 8 байт
		- post id - BIGINT 8 байт
- Трафик
	- Запись = 386 MB/s
		- публикация поста с фото - (10000000 * (10/30) * 10009 KB) / 86400 = 386 MB/s
		- оценка, комментирование поста, подписка на других = 2 KB/s 
	- Чтение = 5.218 GB/s
		- открытие поста с комментариями и фото - (10000000 * (10/30) * 10011 KB) / 86400 = 386 MB/s
		- просмотр ленты с 5 фото и без коментов - (10000000 * (200/30) * 5011 KB) / 86400 = 3.866 GB/s
		- поиск популярных мест - (10000000 * (50/30) * 5011 KB) / 86400 = 966 MB/s
- Сезонность x2
	- Нагрузка
		- RPS запись = 466
			- публикация поста с фото - (10000000 * (10/30)) / 86400 = 39 * 2 = 78
			- оценка поста - (10000000 * (30/30)) / 86400 = 116 * 2 = 232
			- комментирование поста - (10000000 * (15/30)) / 86400 = 58 * 2 = 116
			- подписка на других - (10000000 * (5/30)) / 86400 = 20 * 2 = 40
		- RPS чтение = 1662
			- открытие поста с комментариями и фото - (10000000 * (10/30)) / 86400 = 39 * 2 = 78
			- просмотр ленты с 5 фото и без коментов - (10000000 * (200/30)) / 86400 = 772 * 2 = 1544
			- поиск популярных мест - (10000000 * (5/30)) / 86400 = 20 * 2 = 40
	- Трафик
		- Запись = 772 MB/s
			- публикация поста с фото - (10000000 * (10/30) * 10009 KB) / 86400 = 386 MB/s * 2 = 772 MB/s
			- оценка, комментирование поста, подписка на других = 2 KB/s * 2 = 4 KB/s
		- Чтение = 10.436 GB/s
			- открытие поста с комментариями и фото - (10000000 * (10/30) * 10011 KB) / 86400 = 386 MB/s * 2 = 772 MB/s
			- просмотр ленты с 5 фото и без коментов - (10000000 * (200/30) * 5011 KB) / 86400 = 3.866 GB/s * 2 = 7.732 GB/s
			- поиск популярных мест - (10000000 * (50/30) * 5011 KB) / 86400 = 966 MB/s * 2 = 1932 MB/s