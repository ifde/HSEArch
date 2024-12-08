# ИДЗ #4
# Фролов Иван Григорьевич, БПИ-235

## Задание No 4 Разработка многопоточных приложений

### Задание (Вариант 31):
**Задача о Пути Кулака.** На седых склонах Гималаев стоит древ- ний буддистский монастырь: Гуань-Инь-Янь. Каждый год в день сошествия на землю боддисатвы монахи монастыря собираются на совместное празднество и показывают свое совершенствование на Пути Кулака. Всех соревнующихся монахов первоначально разби- вают на пары. Бои продолжаются до выявления победителя. Мо- нах который победил в финальном бою, забирает себе на хранение статую боддисатвы. Реализовать многопоточное приложение, опре- деляющего победителя. В качестве входных данных используется массив, в котором хранится количество энергии Ци каждого мо- наха. При победе монах забирает энергию Ци своего противника. Новые пары образуются среди победителей других пар в порядке завершения поединков. То есть, возможна ситуация, когда бойцы, участвующие в поединке могут быстро победить и начать биться с другими, в то время как поединки начавшиеся ранее, могут продол- жаться. Причем длительное время. Каждый поединок протекает некоторое случайное время, которое пропорционально отношению энергии Ци побежденного к энергии Ци победитея, умноженному на поправочный коэффициент, позволяющий отслеживать проте- кание поединка на экране дисплея (например, путем умножения этого отношения на 1000 миллисекунд или другое более удобное значение).


### Запуск программы

Программа запускается с использованием `Makefile` командой:  

`make run QI="10 30 20 40" OUTPUT="output.txt" INPUT="input.txt"` 

Исходный код лежит в файле `main.cpp`   

Ключи:  

`QI`: Список энергий Ци монахов, разделенных пробелами. Если передан, игнорируется файл INPUT.  
`INPUT`: Путь к конфигурационному файлу, содержащему значения энергий Ци монахов. Используется, если не задан QI.  
`OUTPUT`: Путь к выходному файлу для записи результатов турнира. Если не указан, результаты выводятся только в консоль.

### Примечание

Постарался сделать все на 10 баллов!

В архиве папке есть три пары файлов ввода / вывода для тестов.  
`input.txt` и `output.txt`.  
`input1.txt` и `output1.txt`.  
`input2.txt` и `output2.txt`.  

### Сценарий задачи в терминах предметной области

В буддийском монастыре проходит турнир между монахами, демонстрирующими свои навыки Пути Кулака. 

Каждый монах обладает определенной энергией Ци, которая используется в бою. Турнир организован следующим образом:  

Монахи делятся на пары случайным образом.
Каждый поединок длится время, пропорциональное энергии проигравшего монаха.  
Победитель в поединке забирает энергию Ци побежденного монаха.  
После завершения всех поединков текущего раунда победители снова делятся на пары, и цикл продолжается.    
Турнир заканчивается, когда остается один победитель, который становится хранителем статуи боддисатвы.  

### Модель параллельных вычислений
Для симуляции поединков используется многопоточная модель:

Каждый поединок происходит в отдельном потоке, что моделирует независимость сражений между парами монахов.  

**Потоки синхронизируются с использованием мьютексов:**  
print_mutex: Защищает вывод в консоль, чтобы результаты поединков не перемешивались.  
tournament_mutex: Используется для управления общим состоянием турнира.  
После завершения всех потоков текущего раунда собираются победители, которые участвуют в следующем раунде.  

### Обобщенный алгоритм  
Инициализация:  
Чтение данных о монахах из командной строки (QI) или файла (INPUT).  
Создание структуры данных для монахов, содержащей их ID и энергию Ци.  

Перемешивание участников:  
В каждом раунде монахи случайным образом разбиваются на пары. Если количество участников нечетное, один монах автоматически переходит в следующий раунд.  

Симуляция поединков:  
Для каждой пары создается поток, который моделирует бой между монахами.  
Победитель забирает энергию Ци побежденного.  
Результаты записываются в консоль и, при наличии файла OUTPUT, в него.  

Переход в следующий раунд:  
После завершения текущего раунда собираются победители, которые участвуют в следующем раунде.  

Завершение турнира:  
Турнир продолжается до тех пор, пока не останется один победитель.  
Итоговый результат выводится в консоль и записывается в файл (если указан).  

### Задание на 9 баллов  

Оно запускается командой 

`make runBarrier QI="10 30 20 40" OUTPUT="output.txt" INPUT="input.txt"`  

Исходный код лежит в файле `mainBarrier.cpp`  

Отличия от первой программы:  

**Использование барьеров (pthread_barrier_t):**   
Все матчи раунда должны завершиться, прежде чем начать следующий раунд.  
Это гарантирует синхронизацию между потоками.  

**Условные переменные (pthread_cond_t):**  
Используются для уведомления об окончании раунда.  
Потоки ждут завершения раунда через условную переменную.  

**Устранены мьютексы для управления турниром, их заменил барьеры и условные переменные.**  

Примечание: сравнительный анализ был проведен, результат получается идентичный первой программе.  

### Задание на 10 баллов  

Используем OpenMP.

Чтобы все работало, сначала необходимо установить OpenMP: `brew install llvm libomp`(это комаанда для MacOS)  

После этого в файле `CMakeLists.txt` раскомментировать строчку `# include_directories("/opt/homebrew/opt/libomp/include")`  
Эта команда подключает заголовочный файл `omp.h`  

Далее запускаем с помощью make:

`make runOMP QI="10 30" OUTPUT="output.txt" INPUT="input.txt"`  

Исходный код лежит в файле `mainOMP.cpp`  


### Тестовые прогоны (скриншоты)
<img width="426" alt="image" src="https://github.com/user-attachments/assets/baab3c7f-78fb-4240-b5bd-d98d8d9c58a7">
<img width="436" alt="image" src="https://github.com/user-attachments/assets/f098e917-3b3c-4207-9ece-3b20d542e07b">
<img width="421" alt="image" src="https://github.com/user-attachments/assets/bba36fad-da64-4b23-bd0e-df20527cc1dd">
<img width="444" alt="image" src="https://github.com/user-attachments/assets/5f9298e8-b29e-4a3f-a3a9-e215e24f0275">


### Описание файлов
`macros.s` - стандратные макросы
`macros_file_io.s` - макросы для ввода / вывода
`macros_reverse_words.s` - макросы для опеределения, является ли считанный символ буквой
`reverse_words.s` - подпрограмма, которая переворачивает слова внутри строки
`file_input.s` - подпрограмма, которая считывает текст из файла и переворачивает его, а после запускает подпрограмму для записи в файл
`file_output.s` - подпрограмма для записи в файл  


<img width="448" alt="image" src="https://github.com/user-attachments/assets/7e9570e0-417f-47fe-9bed-059cadf4a064">


### Тестирующая программа
`test_script.s`  

Примечание: для чтения и записи она использует фиксированные файлы `input.txt`и `output.txt`, чтобы не конфликтовать с основной программой.  

```
# ТЕСТИРУЮЩАЯ ПОДРОГРАММА. Вызываются функции через макросы. Делает несколько тестов, выводя результаты на коноль.

.include "macros.s"
.include "macros_file_io.s"

.data
input_file:       .asciz "input.txt"       # Fixed input file
output_file:      .asciz "output.txt"      # Fixed output file

test1_input:      .asciz "hello world"
test1_expected:   .asciz "olleh dlrow"

test2_input:      .asciz "Assembly language is great!"
test2_expected:   .asciz "ylbmessA egaugnal si taerg!"

test3_input:      .asciz "12345!@# abc"
test3_expected:   .asciz "12345!@# cba"

buffer:           .space 256               # Temporary buffer for reading output
len:              .word 0                  # Temporary storage for string lengths

success:  .asciz "Test passed\n"
fail_msg: .asciz "Test failed\n"

.text
.globl test

test:
    print_string("test 1\n")
    # Test 1
    la s0, test1_input                     # Address of input string
    la s1, input_file                      # Address of input file path
    li s2, 11                              # Length of input string
    EMPTY_FILE(s1)                         # Emptying the source file
    write_to_path(s0, s1, s2)              # Write input to the input file

    la s1, input_file                      # Address of input file path
    la s2, output_file                     # Address of output file path
    li s3, 'N'                               # Don't print to the console
    process_string_from_path(s1, s2, s3)   # Process string
    
    la s2, output_file 
    OPEN_FILE(s2, 0, s4) # s4 = file descriptor
    READ_FILE_PART(s4, s0, 256, s5) # s0 - string from the output file
    CLOSE_FILE(s4)
    print_string("__result: ")
    print_asciz_string(s0)
    print_string("\n")
    la s1, test1_expected                  # Address of expected output
    print_string("expected: ")
    print_asciz_string(s1)
    print_string("\n")
    li s2, 11                              # Length to compare
    # call compare_strings                 

    print_string("test 2\n")
    # Test 2
    la s0, test2_input                     # Address of input string
    la s1, input_file                      # Address of input file path
    li s2, 29                              # Length of input string
    EMPTY_FILE(s1)                         # Emptying the source file
    write_to_path(s0, s1, s2)              # Write input to the input file

    la s1, input_file                      # Address of input file path
    la s2, output_file                     # Address of output file path
    li s3, 'N'                               # Don't print to the console
    process_string_from_path(s1, s2, s3)   # Process string
    
    la s2, output_file 
    li s4, 0
    OPEN_FILE(s2, 0, s4) # s4 = file descriptor
    READ_FILE_PART(s4, s0, 256, s5) # s0 - string from the output file
    CLOSE_FILE(s4)
    print_string("__result: ")
    print_asciz_string(s0)
    print_string("\n")
    la s1, test2_expected                  # Address of expected output
    print_string("expected: ")                 # Address of expected output
    print_asciz_string(s1)
    print_string("\n")
    li s2, 29                              # Length to compare
    
    print_string("test 3\n")
    # Test 3
    la s0, test3_input                     # Address of input string
    la s1, input_file                      # Address of input file path
    li s2, 17                              # Length of input string
    EMPTY_FILE(s1)                         # Emptying the source file
    write_to_path(s0, s1, s2)              # Write input to the input file

    la s1, input_file                      # Address of input file path
    la s2, output_file                     # Address of output file path
    li s3, 'N'                               # Don't print to the console
    process_string_from_path(s1, s2, s3)   # Process string
    
    la s2, output_file 
    OPEN_FILE(s2, 0, s4) # s4 = file descriptor
    READ_FILE_PART(s4, s0, 256, s5) # s0 - string from the output file
    CLOSE_FILE(s4)
    print_string("__result: ")
    print_asciz_string(s0)
    print_string("\n")
    la s1, test3_expected                  # Address of expected output
    print_string("expected: ")                 # Address of expected output
    print_asciz_string(s1)
    print_string("\n")
    li s2, 17                              # Length to compare
    
    li a7, 10
    ecall

```

### Скриншот работы тестирующей программы:  
<img width="273" alt="image" src="https://github.com/user-attachments/assets/84633c19-713f-446d-a1dc-ee93f87e7b76">

### Автономная библиотека макросов

`macros.s`

```
#
# АВТОНОМНАЯ БИБИЛИОТЕКА МАКРОСОВ
#   

.macro ASK_THE_USER(%res)
.data
prompt:    .asciz "Желаете выводить строки в консоль? (Y/N): "  # Вопрос пользователю
.text

# Вывод вопроса
    la      a0, prompt        # Загрузить адрес строки вопроса
    li      a7, 4            
    ecall

    # Чтение ответа
    li      a7, 12            
    ecall

    # Сохранение ответа в a0
    mv      %res, a0      # Загрузить ответ пользователя в %res

.end_macro

.macro exit
    li a7, 10
    ecall
.end_macro

# Reads data from path and reverses it
.macro process_string_from_path(%path, %destination_path, %answer)
	mv a1 %path
	mv a2 %destination_path
	mv a3 %answer
	call read 
.end_macro 

# Writes a string to path
.macro write_to_path(%str, %path, %len)
        mv a0, %str
	mv a1, %path
	mv a2, %len
	call write 
.end_macro 

# Reverses the string
.macro reverse_string(%str, %res, %len)
	mv a1 %str
	mv a2 %len
	call reverse
	mv %res a1
	mv %len a2
.end_macro 

.macro strncpy(%res, %src, %n)
	mv a0 %res
	mv a1 %src
	mv a2 %n
	call strncpy
.end_macro  

# Чтение целого числа
.macro read_int(%x)
	stack_push (a0)
	li a7, 5
	ecall
	mv %x, a0
	stack_pop (a0)
.end_macro

# Чтение строки
.macro read_string(%str)
	mv a0 %str
	li a1 127
	li a7 8
	ecall
.end_macro 

# Вывод целого числа в консоль
.macro print_int(%x)
	stack_push (a0)
	li a7, 1
	mv a0, %x
	ecall
	stack_pop (a0)
.end_macro

# Добавление элемента в стек
.macro stack_push(%x)
	addi	sp, sp, -4
	sw	%x, (sp)
.end_macro

# Извлечение элемента из стека
.macro stack_pop(%x)
	lw	%x, (sp)
	addi	sp, sp, 4
.end_macro

# Вывод строки в консоль
.macro print_string (%x)
	.data
		str: .asciz %x
   	.text
		stack_push (a0)
		li a7, 4
		la a0, str
		ecall
		stack_pop (a0)
.end_macro

.macro PRINT_N_CHARS(%buffer, %length)
    mv t0, %buffer       # Address of the buffer into t0
    mv t1, %length       # Number of characters to print into t1
    
    # Loop to print each character
loop:
    beqz t1, finish           # Exit loop if t1 (remaining characters) is 0
    lb a0, 0(t0)         # Load the current character from the buffer
    beqz a0, finish          # Stop if we encounter a null terminator
    li a7, 11            # Syscall for printing a single character
    ecall
    addi t0, t0, 1       # Move to the next character
    addi t1, t1, -1      # Decrease the counter
    j loop                 # Repeat the loop

finish:
.end_macro


# Вывод .asciz строки в консоль
.macro print_asciz_string(%x)
	stack_push(a0)
	li a7, 4
	mv a0, %x
	ecall
	stack_pop(a0)
.end_macro 

# Вывести символ в консоль
.macro print_char(%x)
   li a7, 11
   li a0, %x
   ecall
.end_macro
```


