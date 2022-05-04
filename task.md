# Упражнение 1
## Операции с числами

### СЛОЖЕНИЕ УНАРНЫХ ЧИСЕЛ

Будем заменять + на 1, а следующий символ на 1. В конце удалим лишнюю единицу.

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

## операции с языками и символами
### проверка принадлежности к языку$\space L = \{0^n1^n2^n, n \geq 0\}$

Вычеркнем 0, перейдем к вычеркиванию 1, если 1 не нашлась отклоним, если нашлась вычеркнем перейдем к вычеркиванию 2, если 2 не нашлась отклоним, иначе вычеркнем и перейдем к возврату к 0. Когда найдем 0 вычеркнем и перейдем к вычеркиванию 1, если дойдем до пустого символа, перейдем к проверке, что все символы вычеркнуты.

Проверка для случая n = 0 может потребоваться бесконечное время т.к.

```
input: '001122'
blank: ' '
start state: x_0
table:

  x_0:
    0: {write: x, R: x_1}
    [1, 2, x, ' ']: {R: reject}

  x_1:
    [0, x]: R
    1: {write: x, R: x_2}
    [2, ' ']: {R: reject}
    
  x_2:
    [1, x]: R
    2: {write: x, L: return}
    [0, ' ']: {R: reject}
    
  return:
    ' ': {R: check}
    [1, 2, x]: L
    0: {write: x, R: x_1}
    
  check:
    x: R
    [0, 1, 2]: {R: reject}
    ' ': {L: accept}
    
  reject:
  
  accept:
```

### правильная скобочная последовательность (3 вида скобок)

идея алгоритма - вычеркивать пары скобок
```
input: '([{[]}])[({{}})]'
blank: ' '
start state: start
table:

  start:
    'x': R
    '(': {R: round}
    '[': {R: square}
    '{': {R: curly}
    [')', ']', '}']: {R: reject}

  search_left:
    [x, ')', ']', '}']: L
    '(': {R: round}
    '[': {R: square}
    '{': {R: curly}
    ' ': {R: search_right}

  search_right:
    x: R
    '(': {R: round}
    '[': {R: square}
    '{': {R: curly}
    ' ': {L: accept}
    [')', ']', '}']: {L: reject}

  round:
    x: R
    ')': {write: x, L: erase}
    '(': {R: round}
    '[': {R: square}
    '{': {R: curly}
    [']', '}', ' ']: {L: reject}

  square:
    x: R
    ']': {write: x, L: erase}
    '(': {R: round}
    '[': {R: square}
    '{': {R: curly}
    [')', '}', ' ']: {L: reject}

  curly:
    x: R
    '}': {write: x, L: erase}
    '(': {R: round}
    '[': {R: square}
    '{': {R: curly}
    [']', ')', ' ']: {L: reject}

  erase:
    x: L
    ['(', '[', '{']: {write: x, R: search_left}

  reject:

  accept:
```