# SET 5

## Задача A1. Анализ линейного пробирования

### Task 1.
Случай 1: хеш-таблица полностью заполнена, в ней нет значений `NULL`. Тогда все алгоритмы могут работать бесконечно долго, ведь условие `table[ind] != NULL` будет всегда истинным, а условие выхода из цикла может не выполнится для всех элементов таблицы.

Случай 2: Рассмотрим такую последовательность операций. Состояние хеш-таблицы: пусть M = 10 и `table[i] = i` для всех i = 0, 1, ... , 9.  
Выполним ERASE[0]. После этого выполним операцию INSERT[0]. Запустится бесконечный цикл, ведь алгоритм INSERT не вставляет ключи на места, помеченные ERASED!  

Соображение: вообще, после большого количества удавлений таблица "засорится" значениями ERASED, из-за чего будет выполнятся очень много проб для вставок и поиска. Из-за этого эффективвность такого подхода низкая. Лучше было бы после удаления выполнять сдвиг всех элементов кластера влево, как обсуждалось на лекции.

### Task 2.
Сделаем необходимые изменения для корректной работы алгоритмов.  

INSERT:
```
def INSERT(key):
    ind = hash(key) % M
    cnt = 0 # счетчик для подсчета количества проходов 

    # если счетчик стал равен M, то завершаем цикл
    # если нашли ERASED, то завершаем цикл и выполняем вставку на это место
    while table[ind] is not None and table[ind] is not "ERASED" and cnt < M: 
        if table[ind] == key:
            return
        ind = (ind + 1) % M
        cnt += 1 # увеличиваем на каждой интерации

    if cnt < M:
        table[ind] = key
```

DELETE:
```
def DELETE(key):
    ind = hash(key) % M
    cnt = 0 # счетчик для подсчета количества проходов 

    while table[ind] is not None and cnt < M: # если счетчик стал равен M, то завершаем цикл
        if table[ind] == key:
            table[ind] = "ERASED"
            return
        ind = (ind + 1) % M
        cnt += 1 # увеличиваем на каждой интерации
```

SEARCH:
```
def SEARCH(key):
    ind = hash(key) % M
    cnt = 0 # счетчик для подсчета количества проходов 

    while table[ind] is not None and cnt < M: # если счетчик стал равен M, то завершаем цикл
        if table[ind] == key:
            return True
        ind = (ind + 1) % M
        cnt += 1 # увеличиваем на каждой интерации

    return False
```

## Задача A2. Анализ корректности FAST EXPONENT

<img width="1130" alt="image" src="https://github.com/user-attachments/assets/7ad43816-f98d-4139-adb8-ea79d79a7436">
<img width="572" alt="image" src="https://github.com/user-attachments/assets/e80bacdb-91ac-46ec-beeb-49f36aa4a3c6">

## Задача A3. Точная функция T(n) и порядок ее роста

<img width="441" alt="image" src="https://github.com/user-attachments/assets/858aebd1-4301-4a71-9ccf-6fc97c653fa2">

## Задача A4. Разные алгоритмы решения одной* задачи

<img width="436" alt="image" src="https://github.com/user-attachments/assets/52ed6f26-4ada-4272-85ff-034c50e00537">
<img width="578" alt="image" src="https://github.com/user-attachments/assets/037e17cd-a5d4-42d4-b59d-3d51d05765b9">


## Задача A5. Поиск значения в отсортированной матрице

```
std::pair<int, int> findElem(const std::vector<std::vector<int>>& A, int n, int key) {
  int row = 0; // c1
  int col = 0; // c1

  while (row < n && col < n) { // 2 * c3 * (2n - 1)
    int elem = A[row][col]; // c1

    if (elem == key) { // c3
      return {row, col}; // c4
    } else if (elem > key) { // c3
      ++row; // c2
    } else {
      ++col; // c2
    }
  }

  return {-1, -1}; // c4
}
```

<img width="575" alt="image" src="https://github.com/user-attachments/assets/6dd5ceba-e16f-497a-bc60-a657146ff42a">
