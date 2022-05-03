# Упражнение 1
## Операции с числами

### СЛОЖЕНИЕ УНАРНЫХ ЧИСЕЛ

Будем сдвигать плюс на один символ вправо. В конце удалим лишнюю единицу.

```
input: '11+1'
blank: ' '
start state: search_plus
table:
  search_plus:
    1: R
    +: {write: 1, R: write_plus}
    
  write_plus:
    1: {write: +, L: search_plus}
    ' ': {L: del_last}
  
  del_last:
    1: {write: ' ', L: done}
    
  done:
```

### умножение унарных чисел

вычеркнем в первом числе первую единицу, затем будем вычеркивать единицы во втором и записывать единицу после знака равенства. Когда единицы во втором числе кончатся, восстановим их и вернемся к первому числу. Повторим тоже самое вычеркнув вторую единицу в первом числе. Когда единицы в первом числе кончатся, восстановим их и завершим работу.

```
input: '1*1='
blank: ' '
start state: x_first
table:

  x_first:
    1: {write: x, R: search_second}
    '*': {L: recover_first}
    x: {write: 1, L: recover_first}
  
  search_second:
    1: R
    '*': {R: x_second}

  x_second:
    1: {write: x, R: write}
    x: R
    =: {L: recover_ones}
  
  write:
    [1, x, '*', =]: R
    ' ': {write: 1, R: return_to_second}
  
  return_to_second:
    [1, '*', =, ' ']: L
    x: {R: x_second}
  
  recover_ones:
    '*': {L: search_next_one}
    x: {write: 1, L: recover_ones}
  
  search_next_one:
    1: L
    x: {R: x_first}
  
  recover_first:
    x: {write: 1, L: recover_first }
    ' ': {R: done}
    
  done:
```