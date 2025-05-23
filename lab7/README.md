# freeBSD_lab7
## Завдання №1
Використайте popen(), щоб передати вивід команди rwho (команда UNIX) до more (команда UNIX) у програмі на C.
## Реалізація завдання
Команда `rwho` на FreeBSD (і взагалі в UNIX) працює лише якщо запущений демон `rwhod` і в мережі є активні клієнти, які його використовують. Якщо я просто на локальній машині без інших комп’ютерів у мережі з активним `rwhod`, команда `rwho` поверне порожній результат.

Отже, було написано програму, яка використовує функцію `popen()` для запуску команд оболонки. Спочатку команда `ls -l /` виконується за допомогою `popen` з режимом читання (`"r"`) - це створює потік для читання виводу команди. Потім запускається команда `more` у режимі запису (`"w"`) через `popen`, що дозволяє передавати дані до команди `more`. Програма зчитує вміст потоку `in`, збережений у буфері, рядок за рядком, і передає ці рядки в потік `out`, який потім обробляється командою `more`. Наприкінці програма закриває обидва потоки (`pclose()`), забезпечуючи коректне завершення виконання.
## Компіляція та виконання завдання
![Знімок екрана 2025-05-01 165855](https://github.com/user-attachments/assets/9beff27f-7d77-4c56-a272-9b394d479e45)

## Завдання №2
Напишіть програму мовою C, яка імітує команду `ls -l` в UNIX — виводить список усіх файлів у поточному каталозі та перелічує права доступу тощо. (Варіант вирішення, що просто виконує `ls -l` із вашої програми, — не підходить.)
## Реалізація завдання
Було написано програму, яка імітує команду `ls -l` в UNIX без використання самої команди. Вона робить наступні дії:
+ Відкриває поточний каталог (`opendir`) і отримує список його файлів та папок (`readdir`).
+ Ігнорує спеціальні елементи каталогу `.` та `..`.
+ Для кожного файлу/папки використовує функцію `stat`, щоб отримати інформацію про файл, як-от права доступу, розмір, час останньої модифікації тощо.
+ Форматує права доступу у вигляді рядка за допомогою функції `print_permissions`.
+ Виводить кількість жорстких посилань, ім'я власника та групи (використовуючи `getpwuid` і `getgrgid`), розмір файлу та дату останньої модифікації (форматується через `strftime`).
+ Виводить ім'я файлу або папки.

## Компіляція та виконання завдання
![image](https://github.com/user-attachments/assets/8e38d1f6-7634-4b0b-b7bb-4d6706f3866d)

## Завдання №3
Напишіть програму, яка друкує рядки з файлу, що містять слово, передане як аргумент програми (проста версія утиліти grep в UNIX).
## Реалізація завдання
Було написано програму, яка імітує базову функціональність `grep`. Її дії:
- Перевіряє, що користувач передав два аргументи: слово для пошуку та назву файлу.
- Відкриває файл для читання. Якщо файл не відкривається, виводить повідомлення про помилку.
- Зчитує файл рядок за рядком за допомогою функції `fgets`.
- Перевіряє, чи містить кожен рядок задане слово (`strstr`).
- Якщо слово знайдено, друкує рядок на стандартний вихід (`printf`).
- Закриває файл після завершення роботи.

**Приклад використання**: команда `./Ex3 error /var/log/messages` друкує всі рядки з файлу `/var/log/messages`, що містять слово `error`.
## Компіляція та виконання завдання
![image](https://github.com/user-attachments/assets/2fd2ae17-27d1-4cf4-8a92-c123c53df9aa)

## Завдання №4
Напишіть програму, яка виводить список файлів, заданих у вигляді аргументів, з зупинкою кожні 20 рядків, доки не буде натиснута клавіша (спрощена версія утиліти more в UNIX).
## Реалізація завдання
Було написано програму, яка реалізує спрощену версію утиліти `more`, виводячи вміст файлів із паузами кожні 20 рядків. Основні етапи роботи даної програми:
* *Обробка аргументів*: перевіряє, чи передані файли для виводу.
* *Відкриття файлу*: читає його рядок за рядком за допомогою `fgets()`.
* *Лічильник рядків*: після кожних 20 рядків програма зупиняється та очікує, поки користувач натисне Enter (`getchar()`).
* *Обробка декількох файлів*: якщо передано кілька файлів, кожен обробляється окремо, виводячи заголовок із його назвою.
* *Закриття файлу*: після завершення читання потік закривається (`fclose()`).

## Компіляція та виконання завдання
![image](https://github.com/user-attachments/assets/8c2be793-b6a3-4daa-9f2c-855618a39469)

## Завдання №5
Напишіть програму, яка перелічує всі файли в поточному каталозі та всі файли в підкаталогах.
## Реалізація завдання
Було написано програму, яка здійснює рекурсивне перелічення всіх файлів і підкаталогів у поточному каталозі. Основні етапи роботи:
+ *Відкриття каталогу*: `opendir(path)` відкриває заданий каталог.
+ *Читання вмісту*: `readdir(dir)` проходить по кожному елементу каталогу.
+ *Фільтрація*: Пропускаємо спеціальні записи `.` та `..`, щоб уникнути нескінченного рекурсивного зациклення.
+ *Формування шляху*: `snprintf(fullpath, sizeof(fullpath), "%s/%s", path, entry->d_name);` створює повний шлях до поточного файлу або каталогу.
+ *Отримання інформації*: `stat(fullpath, &st)` перевіряє, чи є файл звичайним файлом або каталогом.
+ *Виведення файлів*: Всі файли та каталоги виводяться у консоль.
+ *Рекурсивний виклик*: Якщо знайдено каталог (`S_ISDIR(st.st_mode)`), програма знову викликає `list_directory(fullpath)`, щоб обробити його вміст.

## Компіляція та виконання завдання
![image](https://github.com/user-attachments/assets/d0b12e15-c443-4383-b4b5-01371871d46c)

## Завдання №6
Напишіть програму, яка перелічує лише підкаталоги у алфавітному порядку.
## Реалізація завдання
Було написано програму, яка перелічує всі підкаталоги поточного каталогу та виводить їх у алфавітному порядку. Ось як вона працює:

`opendir(".")` відкриває поточний каталог, а `readdir(dir)` отримує всі файли та каталоги. Далі програма пропускає `.` та `..`, щоб уникнути зайвих записів. `stat(fullpath, &st)` перевіряє, чи є елемент каталогом (`S_ISDIR(st.st_mode)`). Підкаталоги додаються у масив `dirs[count]`. Використовується `qsort(dirs, count, sizeof(char *), compare)`, де `compare()` забезпечує алфавітний порядок. У кінці `free(dirs[i])` звільняє виділену пам’ять.
## Компіляція та виконання завдання
![image](https://github.com/user-attachments/assets/d4348483-23c6-41a2-9095-be3c4c311830)

## Завдання №7
Напишіть програму, яка показує користувачу всі його/її вихідні програми на C, а потім в інтерактивному режимі запитує, чи потрібно надати іншим дозвіл на читання (read permission); у разі ствердної відповіді — такий дозвіл повинен бути наданий.
## Реалізація завдання
Було написано програму, яка сканує поточний каталог і відображає всі файли вихідного коду на C та виконувані файли користувача. Вона визначає C-файли за розширенням `.c`, а виконувані файли - за їх атрибутами доступу. Після знаходження виконуваних файлів програма в інтерактивному режимі запитує користувача, чи потрібно надати іншим дозвіл на читання. Якщо користувач погоджується, за допомогою `chmod()` додається відповідний дозвіл. Таким чином, реалізація дозволяє зручно переглядати свої C-файли та керувати доступом до виконуваних програм.
## Компіляція та виконання завдання
![image](https://github.com/user-attachments/assets/a0695877-b7a6-403c-99c0-8c30deb63811)

## Завдання №8
Напишіть програму, яка надає користувачу можливість видалити будь-який або всі файли у поточному робочому каталозі. Має з’являтися ім’я файлу з запитом, чи слід його видалити.
## Реалізація завдання
Ця програма дозволяє користувачу переглянути всі файли у поточному каталозі та вирішити, чи слід їх видалити. Вона відкриває каталог, перебирає його вміст і перевіряє, чи кожен елемент є звичайним файлом. Для кожного такого файлу програма виводить запит на видалення. Якщо користувач вводить "y" або "Y", файл видаляється за допомогою `remove()`. У разі помилки вона використовує `perror()` для виведення діагностичних повідомлень. Завершивши обробку всіх файлів, програма закриває каталог.
## Компіляція та виконання завдання
![image](https://github.com/user-attachments/assets/f1198e13-2f5d-4583-aad4-8a411a48184b)

## Завдання №9
Напишіть програму на C, яка вимірює час виконання фрагмента коду в мілісекундах.
## Реалізація завдання
Було написано програму, яка вимірює час виконання фрагмента коду в мілісекундах, використовуючи `gettimeofday()`. На початку вона фіксує поточний час перед виконанням обчислень у циклі, який підсумовує числа. Після завершення цього фрагмента коду знову отримується час, і функція `get_elapsed_milliseconds()` обчислює різницю між початковим і кінцевим часом у мілісекундах. Результат виводиться на екран, показуючи тривалість виконання зазначеного фрагмента програми.
## Компіляція та виконання завдання
![image](https://github.com/user-attachments/assets/c01f1372-0cab-43eb-85d3-7416a2f24af0)

## Завдання №10
Напишіть програму мовою C для створення послідовності випадкових чисел з плаваючою комою у діапазонах:
- від 0.0 до 1.0
- від 0.0 до n, де n — будь-яке дійсне число з плаваючою точкою.

Початкове значення генератора випадкових чисел має бути встановлене так, щоб гарантувати унікальну послідовність.

**Примітка**: використання прапорця -Wall під час компіляції є обов’язковим.
## Реалізація завдання
Було написано програму, яка генерує випадкові числа з плаваючою комою у двох діапазонах: `[0.0, 1.0)` та `[0.0, n)`, де `n` вводиться користувачем. Для забезпечення унікальності послідовності використовується `srand(time(NULL))`, ініціалізуючи генератор випадкових чисел поточним часом. Функція `random_0_to_1()` створює випадкове число в межах `[0.0, 1.0)` шляхом ділення `rand()` на `RAND_MAX`, а `random_0_to_n(n)` масштабує значення до заданої верхньої межі `n`. Програма запитує `n`, перевіряє коректність введення, а потім виводить 10 випадкових чисел у кожному діапазоні.
## Компіляція та виконання завдання
![image](https://github.com/user-attachments/assets/48d7cb31-7f58-4781-8b3e-6dc13d7c01ac)

## Завдання №20
Реалізуйте обмежувач використання CPU для вибраного процесу без використання nice, cgroups або taskset.
## Реалізація завдання
Було написано програму, яка обмежує використання CPU для вибраного процесу шляхом періодичного зупинки (`SIGSTOP`) і продовження (`SIGCONT`) виконання процесу. Користувач передає ідентифікатор процесу (`PID`) та бажаний рівень навантаження CPU (`CPU_percent`). Виходячи з цього значення, програма обчислює час, протягом якого процес буде активним та призупиненим. У циклі вона перемикає стан процесу кожні 100 мс відповідно до заданого відсоткового рівня використання CPU. Використовуючи `nanosleep()`, програма забезпечує точні часові інтервали між зупинкою та продовженням виконання процесу.
## Компіляція та виконання завдання
![image](https://github.com/user-attachments/assets/4d525047-88b7-448b-9ca0-9e6b07855294)
