# ИДЗ #2
# Фролов Иван Григорьевич, БПИ-235

## RISC-V. Арифметический сопроцессор.

### Задание (Вариант 2):
<img width="535" alt="image" src="https://github.com/user-attachments/assets/b0801794-b39b-4823-b797-fe9ef6121aea">

### Настройки RARS RISK-V, которые были использованы при тестировании  
<img width="414" alt="image" src="https://github.com/user-attachments/assets/e9aea363-ce56-4719-85a7-7c9ae199ad0e">  
Примечание: запускать программу следует с файла `main.s` 

### Примечание

Область допустимых значений x поставлена от -2.5 до 2.5, так каак опытным путем проверено, что для бОльших по модулю значения (от 2.6) при расчетах степенного ряда происходит переполнения double


### Тестовые прогоны (скриншоты)
<img width="322" alt="image" src="https://github.com/user-attachments/assets/d375d807-7cb4-4ec9-a9fb-d32be4411365">
<img width="293" alt="image" src="https://github.com/user-attachments/assets/24518f84-1bc3-4e4a-a26f-073342e762d4">
<img width="275" alt="image" src="https://github.com/user-attachments/assets/af47aff6-dee9-4bdc-a3f8-4a42ca8e1408">
<img width="280" alt="image" src="https://github.com/user-attachments/assets/3cce4da3-6ac8-4f87-afce-678fc67349b4">
<img width="299" alt="image" src="https://github.com/user-attachments/assets/4351ffde-7ab5-433b-8c8c-b019509bea5d">
<img width="280" alt="image" src="https://github.com/user-attachments/assets/acfd77e6-992b-4048-b4cb-f8cddbdb2fb6">
<img width="278" alt="image" src="https://github.com/user-attachments/assets/6d975753-74c0-4601-ab98-0b6c5383f8ad">


### Описание функций
`calculate_func.s`  
<img width="448" alt="image" src="https://github.com/user-attachments/assets/7e9570e0-417f-47fe-9bed-059cadf4a064">


### Тестирующая программа
`tests.s`

```
#
# ТЕСТИРУЮЩАЯ ПРОГРАММА
#

.include "macros.s"      # Подключаем макросы для работы с вводом/выводом

.data
    # Точность для всех тестов
    delta: .double 0.001       # Ожидаемая точность 0.1%

    # Тестовые значения x и ожидаемые результаты для функции гиперболического синуса
    test_x1: .double 1.0             # x = 1.0
    expected_result_1: .double 1.17520   # Ожидаемое значение sh(1) ≈ 1.17520

    test_x2: .double -1.0            # x = -1.0
    expected_result_2: .double -1.17520  # Ожидаемое значение sh(-1) ≈ -1.17520

    test_x3: .double 0.0             # x = 0.0
    expected_result_3: .double 0.0       # Ожидаемое значение sh(0) = 0

    test_x4: .double 2.5            # x = 2.5
    expected_result_4: .double 6.0502  # Ожидаемое значение sh(2.5) ≈ 6.0502

    test_x5: .double -2.5           # x = -2.5
    expected_result_5: .double -6.0502 # Ожидаемое значение sh(-2.5) ≈ -6.0502

    test_x6: .double 2.0           # x = 2.0
    expected_result_6: .double 3.62686 # Ожидаемое значение sh(2.0) ≈ 3.62686

    test_x7: .double -2.0          # x = -2.0
    expected_result_7: .double -3.62686 # Ожидаемое значение sh(-2.0) ≈ -3.62686

    test_x8: .double 0.5             # x = 0.5
    expected_result_8: .double 0.5210953   # Ожидаемое значение sh(0.5) ≈ 0.5210953

    newline: .asciz "\n"
    pass_message: .asciz "Test passed!\n"
    fail_message: .asciz "Test failed!\n"

.text
.globl test
test:   
    fld fa0, delta, t0        # fa0 = точность

    # Тест 1: x = 1.0, ожидаемое значение ≈ 1.17520
    la t1, test_x1
    fld fa1, 0(t1)         # Загружаем значение x в fa1
    call calculate_func # Вызываем calculate_func
    fld ft0, expected_result_1, t2
    jal ra, compare_result    # Сравниваем fa2 с ft0

    # Тест 2: x = -1.0, ожидаемое значение ≈ -1.17520
    la t1, test_x2
    fld fa1, 0(t1)
    jal ra, calculate_func
    fld ft0, expected_result_2, t2
    jal ra, compare_result

    # Тест 3: x = 0.0, ожидаемое значение = 0.0
    la t1, test_x3
    fld fa1, 0(t1)
    jal ra, calculate_func
    fld ft0, expected_result_3, t2
    jal ra, compare_result

    # Тест 4: x = 2.5, ожидаемое значение ≈ 6.0502
    la t1, test_x4
    fld fa1, 0(t1)
    jal ra, calculate_func
    fld ft0, expected_result_4, t2
    jal ra, compare_result

    # Тест 5: x = -2.5, ожидаемое значение ≈ -6.0502
    la t1, test_x5
    fld fa1, 0(t1)
    jal ra, calculate_func
    fld ft0, expected_result_5, t2
    jal ra, compare_result

    # Тест 6: x = 2, ожидаемое значение ≈ 3.62686
    la t1, test_x6
    fld fa1, 0(t1)
    jal ra, calculate_func
    fld ft0, expected_result_6, t2
    jal ra, compare_result

    # Тест 7: x = -2, ожидаемое значение ≈ -3.62686
    la t1, test_x7
    fld fa1, 0(t1)
    jal ra, calculate_func
    fld ft0, expected_result_7, t2
    jal ra, compare_result

    # Тест 8: x = 0.5, ожидаемое значение ≈ 0.5210953
    la t1, test_x8
    fld fa1, 0(t1)
    jal ra, calculate_func
    fld ft0, expected_result_8, t2
    jal ra, compare_result

    # Завершение программы
    li a7, 10
    ecall

# Подпрограмма для сравнения результата с ожидаемым значением
# fa2 - результат функции
# ft0 - ожидаемое значение
compare_result:
   fld ft2, delta, t0        # ft2 = точность

    fsub.d ft1, fa2, ft0       # Вычисляем разницу между результатом и ожидаемым значением
    fsgnj.d ft1, ft1, ft2    # Вычисляем модуль разницы

    flt.d t4, ft1, ft2         # Проверка: |result - expected| < точность
    beqz t4, fail              # Если проверка не пройдена, выводим fail

pass:
    # Сообщение об успешном прохождении теста
    print_string("Тест пройден\n")
    jr ra

fail:
    # Сообщение о неудачном прохождении теста
    print_string("Тест не пройден\n")
    jr ra
```

### Скриншот работы тестирующей программы:  
<img width="135" alt="image" src="https://github.com/user-attachments/assets/5b0fc4a6-b28c-4d5f-83e4-560629771dc8">

### Тестирующая программа на C++: 
```
#include <iostream>
#include <cmath>
#include <iomanip>

const double DELTA = 0.001;

struct Test {
    double x;           // Входное значение
    double expected;    // Ожидаемое значение sinh(x)
};

bool isCloseEnough(double computed, double expected, double epsilon) {
  return std::abs(computed - expected) < epsilon;
}

int main() {
  Test tests[] = {
          {1.0, 1.1752011684303352},
          {-1.0, -1.1752011684303352},
          {0.0, 0.0},
          {2.5, 6.050256948361309},
          {-2.5, -6.050256948361309},
          {2.0, 3.6268590668590663},
          {-2.0, -3.6268590668590663},
          {0.5, 0.5210953000992063}
  };

  bool allTestsPassed = true;

  for (const auto& test : tests) {
    double computed = std::sinh(test.x);
    double deviation = std::abs(computed - test.expected);

    // Вывод результатов для каждого теста
    std::cout << "x = " << test.x << ", computed sinh(x) = " << std::setprecision(15) << computed
              << ", expected = " << test.expected
              << ", deviation = " << deviation;

    if (isCloseEnough(computed, test.expected, DELTA)) {
      std::cout << " -> Pass\n";
    } else {
      std::cout << " -> Fail\n";
      allTestsPassed = false;
    }
  }

  if (allTestsPassed) {
    std::cout << "\nAll tests passed within the allowed deviation of " << DELTA << ".\n";
  } else {
    std::cout << "\nSome tests did not pass within the allowed deviation of " << DELTA << ".\n";
  }

  return 0;
}
```

### Скриншот работы тестирующей программы на C++: 
<img width="953" alt="image" src="https://github.com/user-attachments/assets/dd8695ca-e884-452f-b00c-cedd755fc604">

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

# Чтение double
.macro read_double(%x)
	double_stack_push(fa0)
	li a7, 7
	ecall
	fmv.d %x fa0
	double_stack_pop(fa0)
.end_macro 

# Вывод целого числа в консоль
.macro print_int(%x)
	stack_push (a0)
	li a7, 1
	mv a0, %x
	ecall
	stack_pop (a0)
.end_macro

# Вывод double в консоль
.macro print_double(%x)
        double_stack_push (fa0)
	li a7, 3
	fmv.d fa0 %x
	ecall
	double_stack_pop (fa0)
.end_macro 


# Добавление элемента в стек
.macro stack_push(%x)
	addi	sp, sp, -4
	sw    %x, 0(sp)
.end_macro

# Добавление double элемента в стек
.macro double_stack_push(%x)
	addi	sp, sp, -8
	fsd 	%x, 0(sp)
.end_macro


# Извлечение элемента из стека
.macro stack_pop(%x)
	lw	%x, 0(sp)
	addi	sp, sp, 4
.end_macro

# Извлечение double элемента из стека
.macro double_stack_pop(%x)
	fld 	%x, 0(sp)
	addi	sp, sp, 8
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


