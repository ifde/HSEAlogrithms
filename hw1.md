# SET 1

## Задача A1. Анализ корректности SELECTION SORT

### Task 1.
P1 = {A[j] >= A[minId]}  
Пояснение: на каждой итерации внутреннго цикла, если элемент на позиции j меньше элемента на позиции 
minId, то выполняется присваиваие minId = j, то есть условие P1 становится верным.  

### Task 2.
P2 = {A[i] <= A[i + 1], A[i] <= A[i + 2], ... , A[i] <= A[n - 1]}  
Пояснение: на каждой итерации внешний цикл ставит на i-е место минимальный из элементов среди стоящих на  
i, i + 1, ... , n - 1 местах. 

### Task 3.

Инвариант P1.

INIT: j не существует, считаем что все верно  
MNT: От противного: Если A[j] < A[minId], то выполняется присваиваение minId = j, и тогда A[j] = A[minId]  
TRM: j не существует, считаем что все верно  

Инвариант P2.

INIT: i не существует, считаем что все верно  
MNT: От противного: Если A[i] > A[i + k] для какого-то k, то на (k - 1)-й итерации 
внутреннего цикла j = i + k, то есть A[i] > A[j]. Так как i = minId, то A[minId] > A[j] - противоречие, Ч.Т.Д.  
TRM: i не существует, считаем что все верно  

## Задача A2b. Анализ корректности FAST EXPONENT

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
