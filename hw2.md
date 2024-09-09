# Homework 2

### Task

<img width="1431" alt="image" src="https://github.com/user-attachments/assets/6668db2e-d2ce-467a-a549-392872d37c36">

Code:
```
main:
	mv t0, zero # Псевдокоманда, эквивалентна addi t0, zero, 0
	li t1, 1 # Псевдокоманда, эквивалентна addi t1, zero, 1

	li a7, 5       # Псевдокоманда, эквивалентна addi a7, zero, 5
	ecall          # Команда ECALL
	mv t3, a0      # Псевдокоманда, эквивалентна addi t3, a0, 0

fib:
	beqz t3, finish # Команда BEQZ (B-type)
	add t2, t1, t0 # Команда ADD (R-type)
	mv t0, t1 # Псевдокоманда, эквивалентна addi t0, t1, 0
	mv t1, t2 # Псевдокоманда, эквивалентна addi t1, t2, 0
	addi t3, t3, -1 # Команда ADDI (I-type)
	j fib # Команда J (J-type)
finish:
	li, a7, 1 # Псевдокоманда, эквивалентна addi a7, zero, 1
	mv a0, t0 # Псевдокоманда, эквивалентна addi a0, t0, 0
	ecall # Команда ECALL
```
