# Bubble Sort

## C

```c
#include <stddef.h>
#include <stdlib.h>
#include <string.h>

typedef int (*CompareFn)(const void* a, const void* b);

void bubble_sort(void* arr, size_t length, size_t ele_size, CompareFn compare_fn) {
    if (length <= 1) {
        return;
    }

    unsigned char* arr_ptr = (unsigned char*)arr;
    unsigned char* temp = (unsigned char*)malloc(ele_size);

    for (unsigned char* end = arr_ptr + (length - 1) * ele_size; end > arr_ptr; end -= ele_size) {
        bool swapped = false;
        for (unsigned char* ptr = arr_ptr; ptr < end; ptr += ele_size) {
            if (compare_fn(ptr, ptr + ele_size) > 0) {
                memcpy(temp, ptr, ele_size);
                memcpy(ptr, ptr + ele_size, ele_size);
                memcpy(ptr + ele_size, temp, ele_size);
                swapped = true;
            }
        }
        if (!swapped) {
            break;
        }
    }

    free(temp);
}
```

## C#

```csharp
namespace Sorting {
    public static class Sorter {
        public static void BubbleSort<T>(Span<T> arr) where T : IComparable<T> {
            if (arr.Length <= 1) {
                return;
            }

            int n = arr.Length;

            for (int i = 0; i < n - 1; i++) {
                bool swapped = false;
                for (int j = 0; j < n - i - 1; j++) {
                    if (arr[j].CompareTo(arr[j + 1]) > 0) {
                        (arr[j], arr[j + 1]) = (arr[j + 1], arr[j]);
                        swapped = true;
                    }
                }
                if (!swapped) {
                    break;
                }
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
    void bubble_sort(std::span<T> arr) {
        if (arr.size() <= 1) {
            return;
        }

        size_t n = arr.size();

        for (size_t i = 0; i < n - 1; i++) {
            bool swapped = false;
            for (size_t j = 0; j < n - i - 1; j++) {
                if (arr[j] > arr[j + 1]) {
                    std::swap(arr[j], arr[j + 1]);
                    swapped = true;
                }
            }
            if (!swapped) {
                break;
            }
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

func BubbleSort[T cmp.Ordered](arr []T) {
	if len(arr) <= 1 {
		return
	}

	n := len(arr)

	for i := range n - 1 {
		swapped := false
		for j := range n - i - 1 {
			if arr[j] > arr[j+1] {
				arr[j], arr[j+1] = arr[j+1], arr[j]
				swapped = true
			}
		}
		if !swapped {
			break
		}
	}
}
```

## Java

```java
class Sorting {
    public static <T extends Comparable<T>> void bubbleSort(T[] arr) {
        if (arr.length <= 1) {
            return;
        }

        int n = arr.length;

        for (int i = 0; i < n - 1; i++) {
            boolean swapped = false;
            for (int j = 0; j < n - i - 1; j++) {
                if (arr[j].compareTo(arr[j + 1]) > 0) {
                    T temp = arr[j];
                    arr[j] = arr[j + 1];
                    arr[j + 1] = temp;
                    swapped = true;
                }
            }
            if (!swapped) {
                break;
            }
        }
    }
}
```

## Python

```python
from typing import Protocol, Self

class SupportsGt(Protocol):
    def __gt__(self, other: Self, /) -> bool: ...

class Sorting:
    @staticmethod
    def bubble_sort[T: SupportsGt](arr: list[T]):
        if len(arr) <= 1:
            return

        n = len(arr)

        for i in range(n - 1):
            swapped = False
            for j in range(n - i - 1):
                if arr[j] > arr[j + 1]:
                    arr[j], arr[j + 1] = arr[j + 1], arr[j]
                    swapped = True
            if not swapped:
                break
```

## Rust

```rust
pub mod sorting {
    pub fn bubble_sort<T: PartialOrd>(arr: &mut [T]) {
        if arr.len() <= 1 {
            return;
        }

        let n = arr.len();

        for i in 0..(n - 1) {
            let mut swapped = false;
            for j in 0..(n - i - 1) {
                if arr[j] > arr[j + 1] {
                    arr.swap(j, j + 1);
                    swapped = true;
                }
            }
            if !swapped {
                break;
            }
        }
    }
}
```

## TypeScript

```typescript
export function bubbleSort<T>(arr: T[], compareFn: (a: T, b: T) => number) {
    if (arr.length <= 1) {
        return;
    }

    const n = arr.length;

    for (let i = 0; i < n - 1; i++) {
        let swapped = false;
        for (let j = 0; j < n - i - 1; j++) {
            if (compareFn(arr[j], arr[j + 1]) > 0) {
                [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
                swapped = true;
            }
        }
        if (!swapped) {
            break;
        }
    }
}
```
