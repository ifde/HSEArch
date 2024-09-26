# Homework 4

###
Исходный код программы (выполнено на 10 баллов)

```
    .data
input:    .asciz "Введите целое число. Для завершения ввода введите 0: "
sum:      .asciz "\nСумма: "
overflow: .asciz "\nПереполнение!\n------\nПоследняя корректная сумма: "
count:    .asciz "\nКоличество просуммированных элементов: "
even:    .asciz "\nКоличество четных элементов: "
odd:    .asciz "\nКоличество нечетных элементов: "
break:         .asciz "\n"
.align 2
arr:             .space 40   # Массив для хранения до 10 целых чисел (4 байта на число)
arrend:

    .text
    .globl main
main:
    la t0, arr          
    la t1, arrend           # t1 - адрес в памяти сразу после массива
    li s0, 0            # s0 - сумма
    
fill_array:


 # Ввод очередного числа
    la a0, break
    li a7, 4
    ecall
    la a0, input
    li a7, 4            
    ecall
    
    li a7, 5            
    ecall
    
    # Записываем число в массив
    sw a0, (t0)
    addi t0, t0, 4
    
    beq a0, s0, calc_sum  # Если ввели 0, то переходим к подсчету суммы
    bge t0, t1, calc_sum  # Если ввели 10 элементов, то переходим к подсчету суммы
    j fill_array         
    
calc_sum:

    la t0, arr
    li t6, 0 # t6 - число просуммированных элементов   
    li s10, 0 # Even
    li s11, 0  # Odd      
    
add_to_sum:

    bge t0, t1, print_sum
    
    lw t3, (t0) # t3 - текущий элемент массива
    beqz t3, print_sum
    
    andi t5, t3, 1 # t5 - a flag on odd / even
    beqz t5, update_s10
    addi s11, s11, 1 # updating odd
    j contd1

update_s10:
    addi s10, s10, 1
    j contd1

contd1:
    
    add s1, s0, t3      # s1 - временная сумма
    
    bgtz t3, check_positive
    bltz t3, check_negative

check_positive:
    
    blt s1, s0, if_overflow 
    addi t6, t6, 1 
    j contd
   
check_negative:
    bgt s1, s0, if_overflow 
    addi t6, t6, 1 
    j contd

contd: 
    mv s0, s1           
    addi t0, t0, 4       
    j add_to_sum    

if_overflow:

    li a7, 4
    la a0, overflow
    ecall
    mv a0, s0
    li a7, 1
    ecall
    
    li a7, 4
    la a0, count
    ecall
    mv a0, t6
    li a7, 1
    ecall
    j print_odd_even

print_sum:

    li a7, 4
    la a0, sum
    ecall
    
    mv a0, s0
    li a7, 1
    ecall
    
    li a7, 4
    la a0, break
    ecall


    li a7, 4
    la a0, count
    ecall
    mv a0, t6
    li a7, 1
    ecall
    
    j print_odd_even
    
print_odd_even:
    li a7, 4
    la a0, even
    ecall
    mv a0, s10
    li a7, 1
    ecall
    
    li a7, 4
    la a0, odd
    ecall
    mv a0, s11
    li a7, 1
    ecall
    
    j exit

exit:
    li a7, 10           
    ecall

```
