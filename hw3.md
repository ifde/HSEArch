# Task 3

### Скриншоты работы программы для различных возможных комбинаций делимого и делителя:

<img width="315" alt="image" src="https://github.com/user-attachments/assets/78d4bdb6-22e9-487b-ac5f-c6a8335b2ba6">
<img width="284" alt="image" src="https://github.com/user-attachments/assets/a6d4435e-dc85-4af5-8e69-f285c07f0368">
<img width="300" alt="image" src="https://github.com/user-attachments/assets/e6c632c0-c48f-4bd3-8c2b-0804f2b4cfe1">
<img width="285" alt="image" src="https://github.com/user-attachments/assets/d1060f24-3800-48ac-af6e-02caa6ce5084">
<img width="292" alt="image" src="https://github.com/user-attachments/assets/29623fa3-95d7-4cec-8e25-81d17a13aebf">
<img width="290" alt="image" src="https://github.com/user-attachments/assets/0d1dce5c-9e65-4033-b1c3-e71a38080701">


### Исходный код

```
.data
prompt_dividend: .asciz "Введите делимое: "
prompt_divisor: .asciz "Введите делитель: "
error_division_by_zero: .asciz "Ошибка: Деление на ноль!"
result_quotient: .asciz "Частное: "
result_remainder: .asciz "Остаток: "
empty_string: .asciz "\n"

.text
.global main

main:
    # Ввод делимого
    li a7, 4                  
    la a0, prompt_dividend    
    ecall                      

    li a7, 5                 
    ecall
    mv t0, a0                # сохраняем делимое в t0

    # Ввод делителя
    li a7, 4                  
    la a0, prompt_divisor    
    ecall                      

    li a7, 5                   
    ecall
    mv t1, a0                # сохраняем делитель в t1

    # Проверка делителя на ноль
    beq t1, zero, division_by_zero

    mv t2, t0                    # модуль деломого
    mv t3, t1                    # модуль делителя
    
    bltz t0, abs_dividend      # Если делимое < 0, делаем его положительным
    j    abs_done
    
abs_dividend:
    neg  t2, t0

abs_done:    
    bltz t1, abs_divisor      # Если делитель < 0, делаем его положительным
    j    abs_div_done
    
abs_divisor:
    neg  t3, t1 
               
abs_div_done:


    # Ищем частное и остаток
    li   t4, 0                # Частное
    mv   t5, t2               # Остаток
division_loop:
    bge  t5, t3, subtract     
    j    division_done
subtract:
    sub  t5, t5, t3           # делимое -= делитель
    addi t4, t4, 1            # Увеличиваем частное на 1
    j    division_loop

division_done:

    # Расставляем знаки и частного и остатка
    xor  t6, t0, t1           
    bltz t6, negate_quotient  
    j check_remainder
negate_quotient:
    neg  t4, t4   

check_remainder:    
    xor t6, t0, t2
    bltz t6, negate
    j output_result
negate:
    neg t5, t5  

# Вывод результата
output_result:
    # Выводим частное
    li a7, 4                   
    la a0, result_quotient     
    ecall

    mv a0, t4               
    li a7, 1                 
    ecall
    
    li a7, 4                 
    la a0, empty_string
    ecall     

    # Выводим  остаток
    
    li a7, 4                   
    la a0, result_remainder   
    ecall

    li a7, 1 
    mv a0, t5               
    ecall
    
    li a7, 10                 
    ecall                   

# Ситуация деления на 0
division_by_zero:
    li a7, 4                   
    la a0, error_division_by_zero 
    ecall
    
    li a7, 10                  
    ecall 
```
