# ИДЗ #1

## Целочисленная арифметика. Одномерные массивы

### Задание (Вариант 38): Сформировать массив B из элементов массива A, которые одновременно имеют четные и отрицательные значения.

### Настройки RARS RISK-V, которые были использованы при тестировании  
<img width="414" alt="image" src="https://github.com/user-attachments/assets/e9aea363-ce56-4719-85a7-7c9ae199ad0e">  
Примечание: запускать программу следует с файла `main.s`  


### Тестовые прогоны (скриншоты)
<img width="230" alt="image" src="https://github.com/user-attachments/assets/8fe30aa6-9fb0-45e1-93d7-ea9a3b1d7bdb">  
<img width="152" alt="image" src="https://github.com/user-attachments/assets/6a26b265-96ae-4a69-bf64-3dca13615e2d">  
<img width="230" alt="image" src="https://github.com/user-attachments/assets/a6588f41-c574-48f3-a111-d10dfa8ac329">  
<img width="316" alt="image" src="https://github.com/user-attachments/assets/6f6203f7-5722-469b-97de-c5ad895ffc32">  
<img width="313" alt="image" src="https://github.com/user-attachments/assets/efebafec-dd3e-419d-a1d8-f794b906ad56">  

### Описание функций
`input_array.s`  
<img width="405" alt="image" src="https://github.com/user-attachments/assets/a9295981-8001-477d-befa-e33d5613fde5">  
`create_array.s`  
<img width="771" alt="image" src="https://github.com/user-attachments/assets/731f8bbf-6075-4963-afe0-cd807abe068a">  
`print_array.s`  
<img width="649" alt="image" src="https://github.com/user-attachments/assets/c02f6984-945a-477a-90b8-02f909b29ec6">  

### Тестирующая программа
`tests.s`

```
#
# ТЕСТИРУЮЩАЯ ПРОГРАММА
#

.include "macros.s"          # Включаем макросы для вывода

    .data
# Тестовые массивы A и ожидаемые результаты для массива B
test_array_1: .word -2, -3, 4, -6, 8, 0   # Ожидается: -2, -6
len1: .word 6
expected_result_1: .word -2, -6

test_array_2: .word 5, 7, 9, 11, 13       # Ожидается: (пустой массив)
expected_result_2: .word

test_array_3: .word -1, -3, -5, -7        # Ожидается: (пустой массив)
expected_result_3: .word

test_array_4: .word -4, -8, -16, -32      # Ожидается: -4, -8, -16, -32
expected_result_4: .word -4, -8, -16, -32

test_array_5: .word 0, 0, 0      # Ожидается: (пустой массив)
expected_result_5: .word

newline: .asciz "\n"
pass_message: .asciz "Test passed!\n"
fail_message: .asciz "Test failed!\n"

    .text
    .globl test
test:


    # Прогон теста 1
    la a0, test_array_1      # Указатель на массив A
    lw a2,  len1                # Количество элементов в массиве A
    jal ra, create_array_b    # Вызываем подпрограмму для создания массива B
    la a0, expected_result_1  # Указатель на ожидаемый массив B
    li a2, 2                 # Ожидаемое количество элементов
    jal ra, compare_results   # Сравнение результата с ожиданием

    # Прогон теста 2
    la a0, test_array_2
    li a2, 5
    jal ra, create_array_b
    la a0, expected_result_2
    li a2, 0
    jal ra, compare_results

    # Прогон теста 3
    la a0, test_array_3
    li a2, 4
    jal ra, create_array_b
    la a0, expected_result_3
    li a2, 0
    jal ra, compare_results

    # Прогон теста 4
    la a0, test_array_4
    li a2, 4
    jal ra, create_array_b
    la a0, expected_result_4
    li a2, 4
    jal ra, compare_results
    
    # Прогон теста 5
    la a0, test_array_5
    li a2, 3
    jal ra, create_array_b
    la a0, expected_result_5
    li a2, 0
    jal ra, compare_results

    # Завершение программы
    li a7, 10
    ecall

# Подпрограмма для сравнения массива B с ожидаемым результатом
# Параметры:
# a0 - указатель на ожидаемый результат (массив)
# a1 - указатель на реальный результат (массив B)
# a2 - количество элементов в массиве B (реальный результат)
# a3 - ожидаемое количество элементов в массиве B
compare_results:
    li t0, 0           # Инициализируем индекс элементов
    mv t1, a0          
    mv t2, a1        
    
    bne a2, a3, fail   # Сравнение количества элементов

loop_compare:
    beq t0, a2, pass   # Если все элементы проверены, то успех
    
    lw t3, 0(t1)       
    lw t4, 0(t2)       
    
    bne t3, t4, fail   # Если элементы не равны, то ошибка

    addi t1, t1, 4     
    addi t2, t2, 4     
    addi t0, t0, 1     
    j loop_compare

pass:
    # Выводим сообщение об успешном прохождении теста
    la a0, pass_message
    li a7, 4
    ecall
    j exit_compare

fail:
    # Выводим сообщение о неудачном прохождении теста
    la a0, fail_message
    li a7, 4
    ecall

exit_compare:
    ret

```

Скриншот работы тестирующей программы:  
<img width="101" alt="image" src="https://github.com/user-attachments/assets/97ee1206-019d-46e3-be00-c0987de70ea8">  


### Автономная библиотека макросов

`macros.s`

```
#
# АВТОНОМНАЯ БИБИЛИОТЕКА МАКРОСОВ
#    

# Чтение целого числа
.macro read_int(%x)
	stack_push (a0)
	li a7, 5
	ecall
	mv %x, a0
	stack_pop (a0)
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

# Вывести символ в консоль
.macro print_char(%x)
   li a7, 11
   li a0, %x
   ecall
.end_macro

```

