# Insertion Sort

## C

```c
#include <stddef.h>
#include <stdlib.h>
#include <string.h>

typedef int (*CompareFn)(const void* a, const void* b);

void insertion_sort(void* arr, size_t length, size_t ele_size, CompareFn compare_fn) {
    if (length <= 1) {
        return;
    }

    unsigned char* base = (unsigned char*)arr;
    unsigned char* key = (unsigned char*)malloc(ele_size);
    unsigned char* end = base + length * ele_size;

    for (unsigned char* cur = base + ele_size; cur < end; cur += ele_size) {
        memcpy(key, cur, ele_size);
        unsigned char* ptr = cur;
        while (ptr > base && compare_fn(key, ptr - ele_size) < 0) {
            memcpy(ptr, ptr - ele_size, ele_size);
            ptr -= ele_size;
        }
        memcpy(ptr, key, ele_size);
    }

    free(key);
}
```

## C#

```csharp
namespace Sorting {
    public static class Sorter {
        public static void InsertionSort<T>(Span<T> arr) where T : IComparable<T> {
            if (arr.Length <= 1) {
                return;
            }

            int n = arr.Length;

            for (int i = 1; i < n; i++) {
                T key = arr[i];
                int j = i;
                while (j > 0 && key.CompareTo(arr[j - 1]) < 0) {
                    arr[j] = arr[j - 1];
                    j--;
                }
                arr[j] = key;
            }
        }
    }
}
```

## C++

```cpp
#include <concepts>
#include <cstddef>
#include <span>
#include <utility>

namespace sorting {
    using std::size_t;

    template <std::totally_ordered T>
    void insertion_sort(std::span<T> arr) {
        if (arr.size() <= 1) {
            return;
        }

        size_t n = arr.size();

        for (size_t i = 1; i < n; i++) {
            T key = std::move(arr[i]);
            size_t j = i;
            while (j > 0 && key < arr[j - 1]) {
                arr[j] = std::move(arr[j - 1]);
                j--;
            }
            arr[j] = std::move(key);
        }
    }
}
```

## Go

```go
package sorting

import (
	"cmp"
)

func InsertionSort[T cmp.Ordered](arr []T) {
	if len(arr) <= 1 {
		return
	}

	n := len(arr)

	for i := 1; i < n; i++ {
		key := arr[i]
		j := i
		for j > 0 && key < arr[j - 1] {
			arr[j] = arr[j - 1]
			j--
		}
		arr[j] = key
	}
}
```

## Java

```java
class Sorting {
    public static <T extends Comparable<T>> void insertionSort(T[] arr) {
        if (arr.length <= 1) {
            return;
        }

        int n = arr.length;

        for (int i = 1; i < n; i++) {
            T key = arr[i];
            int j = i;
            while (j > 0 && key.compareTo(arr[j - 1]) < 0) {
                arr[j] = arr[j - 1];
                j--;
            }
            arr[j] = key;
        }
    }
}
```

## Python

```python
from typing import Protocol, Self

class SupportsLT(Protocol):
    def __lt__(self, other: Self, /) -> bool: ...

class Sorting:
    @staticmethod
    def insertion_sort[T: SupportsLT](arr: list[T]):
        if len(arr) <= 1:
            return

        n = len(arr)

        for i in range(1, n):
            key = arr[i]
            j = i
            while j > 0 and key < arr[j - 1]:
                arr[j] = arr[j - 1]
                j -= 1
            arr[j] = key
```

## Rust

```rust
pub mod sorting {
    pub fn insertion_sort<T: PartialOrd>(arr: &mut [T]) {
        if arr.len() <= 1 {
            return;
        }

        let n = arr.len();

        for i in 1..n {
            let mut j = i;
            while j > 0 && arr[j] < arr[j - 1] {
                arr.swap(j, j - 1);
                j -= 1;
            }
        }
    }
}
```

## TypeScript

```typescript
export function insertionSort<T>(arr: T[], compareFn: (a: T, b: T) => number) {
    if (arr.length <= 1) {
        return;
    }

    const n = arr.length;

    for (let i = 1; i < n; i++) {
        const key = arr[i];
        let j = i;
        while (j > 0 && compareFn(key, arr[j - 1]) < 0) {
            arr[j] = arr[j - 1];
            j--;
        }
        arr[j] = key;
    }
}
```
