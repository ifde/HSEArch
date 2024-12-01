# ИДЗ #3
# Фролов Иван Григорьевич, БПИ-235

## RISC-V. Арифметический сопроцессор.

### Задание (Вариант 2):
Разработать программу, которая меняет на обратный порядок следования символов каждого слова в ASCII–строке символов. Порядок слов остается неизменным. Слова состоят только из букв.
Разделителями слов являются все прочие символы. Преобразование осуществляется внутри введенной строки

### Настройки RARS RISK-V, которые были использованы при тестировании  
<img width="420" alt="image" src="https://github.com/user-attachments/assets/3d8f0083-9fe6-4b55-a55a-243ccedcbed8">

Примечание: запускать программу следует с файла `main.s` 

### Примечание

Постарался сделать все на 10 баллов!

Сначала пользователь в графическом диалоговом окне вводит имя входного файла вместе с расширением.  
Файл должен распологаться в директории с программой.  
Для удобства такой файл с текстом уже создан и называется `test.txt`

После этого пользователь вводит имя выходного файла.
Для удобства такой файл уже создан и называется `res.txt`
ВАЖНО: на каждом запуске программы файл `res.txt` очищается, чтобы не возникало путаницы.

Наконец, в третьем диалогом окне пользователь вводит Y или N в зависимости от того, желает ли он выводить строки с перевернутыми словами в консоль.


### Тестовый прогон (скриншоты)
<img width="645" alt="image" src="https://github.com/user-attachments/assets/a37ea602-95e3-4d26-b1c2-b6974ae04d46">
<img width="646" alt="image" src="https://github.com/user-attachments/assets/27a6599f-d2cb-4dc2-b8fc-a9c0ed96051f">
<img width="447" alt="image" src="https://github.com/user-attachments/assets/24a64526-cc08-4955-8f70-90f14d5287bf">
Если слов нет, то не переворачиваем:
<img width="444" alt="image" src="https://github.com/user-attachments/assets/10dfa0de-e3d0-4730-879f-76c898ff9235">

А еще программа читает длинные тексты! (по кусочкам, в буфере всегда 512 элементов максимум)
<img width="645" alt="image" src="https://github.com/user-attachments/assets/5a47991a-1267-4e2b-b82a-18472eaba0c1">
<img width="646" alt="image" src="https://github.com/user-attachments/assets/745f452e-5a98-4ea8-9966-57517c0e4392">



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


