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

## Задача A2. Кубическое пробирование

### Теоретический анализ:
Квадратичное пробирование является частным случаем кубического.  
Добавление третьего слагаемого с множителем `i^3` предположительно будет увеличивать разброс ключей, для которых возникла коллизия. Так как Остатки `i^3` для большинства модулей M будут иметь более сильный разброс и большую длину цикла. 

Пример для M = 16 и i = 0, 1, ... , 11:  
<img width="186" alt="image" src="https://github.com/user-attachments/assets/154f925d-5584-4cb0-a922-3ff7af8d53f8" />
<img width="215" alt="image" src="https://github.com/user-attachments/assets/aec124dc-f6bb-43e9-8284-8540ef3a4920" />

При квадратичном пробировании возник цикл, при тричином - не возник.

Однако все зависит от коэффицентов и размера хеш-таблицы. В реальных условия для больших M использование кубического пробирования не дас больших преимущесв в противодействии коллизиям и образовании кластеров, а вот вычислительной мощности потребуется больше. 

### Программный анализ:

```
#include <iostream>
#include <vector>
#include <random>
#include <chrono>

using namespace std;
using namespace std::chrono;

// Базовая функция хеширования
int my_hash(int key, int m) {
  return key % m;
}

// Класс хеш-таблицы с квадратичным пробированием
class Hash2 {
private:
    vector<int> table;
    int size, c1, c2;
public:
    Hash2(int size, int c1, int c2) : size(size), c1(c1), c2(c2) {
      table.resize(size, -1);
    }

    int insert(int key) {
      int cnt = 0;
      int index = my_hash(key, size);
      while (table[index] != -1 && cnt < size) {
        cnt++;
        index = (my_hash(key, size) + c1 * cnt + c2 * cnt * cnt) % size;
      }
      if (cnt < size) {
        table[index] = key;
      }
      return cnt + 1;
    }
};

class Hash3 {
private:
    vector<int> table;
    int size, c1, c2, c3;
public:
    Hash3(int size, int c1, int c2, int c3) : size(size), c1(c1), c2(c2), c3(c3) {
      table.resize(size, -1);
    }

    int insert(int key) {
      int cnt = 0;
      int index = my_hash(key, size);
      while (table[index] != -1 && cnt < size) {
        cnt++;
        index = (my_hash(key, size) + c1 * cnt + c2 * cnt * cnt + c3 * cnt * cnt * cnt) % size;
      }
      if (cnt < size) {
        table[index] = key;
      }
      return cnt + 1;
    }
};

int main() {
  const int tableSize = 65007;
  const int numInsertions = tableSize * 2 / 3;

  int quadC1 = 1, quadC2 = 3;
  int cubicC1 = 1, cubicC2 = 1, cubicC3 = 1;

  // Для генерации случайных ключей
  mt19937 rng((unsigned)chrono::steady_clock::now().time_since_epoch().count());
  uniform_int_distribution<int> dist(0, 1000000);

  double totalHash2 = 0, totalHash3 = 0;
  double totalQuadTime = 0, totalCubicTime = 0;

  for (int exp = 0; exp < 10; ++exp) {
    Hash2 quadTable(tableSize, quadC1, quadC2);
    Hash3 cubicTable(tableSize, cubicC1, cubicC2, cubicC3);
    int cnt2 = 0, cnt3 = 0;

    auto startQuad = steady_clock::now();
    for (int i = 0; i < numInsertions; ++i) {
      int key = dist(rng);
      cnt2 += quadTable.insert(key);
    }
    auto endQuad = steady_clock::now();
    totalQuadTime += duration_cast<microseconds>(endQuad - startQuad).count();

    auto startCubic = steady_clock::now();
    for (int i = 0; i < numInsertions; ++i) {
      int key = dist(rng);
      cnt3 += cubicTable.insert(key);
    }
    auto endCubic = steady_clock::now();
    totalCubicTime += duration_cast<microseconds>(endQuad - startQuad).count();

    totalHash2 += (double)cnt2 / numInsertions;
    totalHash3 += (double)cnt3 / numInsertions;
  }

  double avgQuadAttempts = totalHash2 / 10;
  double avgCubicAttempts = totalHash3 / 10;



  cout << "Среднее число попыток при вставке (квадратичное пробирование): " << avgQuadAttempts << endl;
  cout << "Среднее число попыток при вставке (кубическое пробирование): " << avgCubicAttempts << endl;

  cout << "Среднее время на вставки (квадратичное пробирование): " << totalQuadTime / 10 << endl;
  cout << "Среднее время на вставки (кубическое пробирование): " << totalCubicTime / 10 << endl;

  return 0;
}
```

### Cкриншоты работы программы:  

<img width="563" alt="image" src="https://github.com/user-attachments/assets/a65017d5-865c-4435-9542-081aefc9e3d2" />
<img width="559" alt="image" src="https://github.com/user-attachments/assets/3cd5b2c5-23e5-41bd-8851-23ac7591c914" />
<img width="560" alt="image" src="https://github.com/user-attachments/assets/658d978f-df0f-4be4-b96b-de977cfe3ce4" />
<img width="564" alt="image" src="https://github.com/user-attachments/assets/8ddf4c04-f8b9-439d-8aba-d380f0e6d813" />

Как мы видим, кубическое пробирование дает очень небольшое преимущетсво. А ногда даже немного хуже. При это время работы для квадратичного и кубического случая неотличимы. 

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
