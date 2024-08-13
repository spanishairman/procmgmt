### Управление процессами. Процесс и его атрибуты, жизнь процессов, процессы и потоки, получение информации о процессе, управление процессом.

> [!NOTE] 
> **Процесс** - исполняющаяся программа и все необходимыне для неё ресурсы, такие как оперативная память, сеть, диск и т.д.

Системные вызовы:
  - обращение прикладной программы к ядру операционной системы
  - управляют процессами
  - работают с файлами
  - управляют устройствами
  - работают с информацией
  - обеспечивают связь и коммуникацию

> [!NOTE] 
> POSIX (англ. Portable Operating System Interface — переносимый интерфейс операционных систем) — набор стандартов, 
> описывающих интерфейсы между операционной системой и прикладной программой (системный API), библиотеку языка C и набор приложений и их интерфейсов. 
> Стандарт создан для обеспечения совместимости различных UNIX-подобных операционных систем и переносимости прикладных программ на уровне исходного кода, но может быть использован и для не-Unix систем. 
[Источник](https://ru.wikipedia.org/wiki/POSIX)

Системные вызовы для управления процессами:
  - fork() - создание процесса
  - exec() - загрузка (запуск) программы в процесс
  - exit() - завершение процесса
  - wait() - считывание кода возврата процесса

> [!NOTE]
> Ядро операционной системы отвечает за выделение ресурсов - процессорного времени,  файлов, оперативной памяти, дискового пространста и т.д.

Создание процессов происходит через системный вызов **fork()**. При этом, новый процесс создается путём копирования существующего. т.е. **fork()** копирует тот процесс, в котором он запущен.
> [!NOTE]
> Процесс с *PID* = 1 - это система инициализации - **SystemD**. Это первый процесс в пространстве пользователя. *PPID* - родительский процесс для **SystemD** = 0, т.е. его не существует.

Первый процесс в пространстве ядра, это **kthreadd**, его *PID* = 2. Пример:
```
localhost:~ # ps -elf | head -n 3
F S UID        PID  PPID  C PRI  NI ADDR SZ WCHAN  STIME TTY          TIME CMD
4 S root         1     0  0  80   0 - 47870 do_epo Aug12 ?        00:00:08 /usr/lib/systemd/systemd --switched-root --system --deserialize 30
1 S root         2     0  0  80   0 -     0 kthrea Aug12 ?        00:00:00 [kthreadd]
```
При запуске процесса, например утилиты **top**, **ls**, **df** и т.д., выполняется системный вызов fork() от оболочки, например **bash**, к ядру. 
Ядро выполняет системный вызов, клонируя процесс, который к нему обратился с системным вызовом **fork()**.
Системный вызов при этом тоже клонируется. 

В результате работы ядра, процесс получает код возврата, на основоании которого определяет, является ли он **Parent proceess** или **Child process**. 
В первом случае код равен любому положительному числу, во втором случае - нулю.

>[!IMPORTANT] Код возврата, возвращаемый родительскому процессу, равен идентификатору, присвоенному дочерним процессом.

У каждого процесса есть *родитель*, соответственно, помимо **PID** - идентификатора процесса, существует **PPID** - *Parent Process Identifier* - идентификатор родительского процесса.
