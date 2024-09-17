<img width="1231" alt="Screenshot 2024-09-15 at 18 54 02" src="https://github.com/user-attachments/assets/01406d9b-7ce3-4e5b-ab64-dbd781ac5901"># Homework 2

### Task

<img width="1422" alt="image" src="https://github.com/user-attachments/assets/6c889d85-83c1-4be6-abb7-0165949b7373">

Code:
```
main:
	mv t0, zero # Псевдокоманда, эквивалентна addi t0, zero, 0 (I-type)
	li t1, 1 # Псевдокоманда, эквивалентна addi t1, zero, 1 (I-type)

	li a7, 5       # Псевдокоманда, эквивалентна addi a7, zero, 5 (I-type)
	ecall          # Команда ECALL
	mv t3, a0      # Псевдокоманда, эквивалентна addi t3, a0, 0 (I-type)

fib:
	beqz t3, finish # (B-type)
	add t2, t1, t0 # (R-type)
	mv t0, t1 # Псевдокоманда, эквивалентна addi t0, t1, 0 (I-type)
	mv t1, t2 # Псевдокоманда, эквивалентна addi t1, t2, 0 (I-type)
	addi t3, t3, -1 # (I-type)
	j fib # (J-type)
finish:
	li, a7, 1 # Псевдокоманда, эквивалентна addi a7, zero, 1 (I-type)
	mv a0, t0 # Псевдокоманда, эквивалентна addi a0, t0, 0 (I-type)
	ecall # Команда ECALL

```

### Задание на 10 баллов:
<img width="1231" alt="Screenshot 2024-09-15 at 18 54 02" src="https://github.com/user-attachments/assets/3ca127c9-557e-40e1-b87c-b95379b31266">
