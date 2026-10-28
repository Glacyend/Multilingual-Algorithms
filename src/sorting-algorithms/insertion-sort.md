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
namespace Sorting
{
    public static class Sorter
    {
        public static void InsertionSort<T>(Span<T> arr) where T : IComparable<T>
        {
            if (arr.Length <= 1)
            {
                return;
            }

            int n = arr.Length;

            for (int i = 1; i < n; i++)
            {
                T key = arr[i];
                int j = i;
                while (j > 0 && key.CompareTo(arr[j - 1]) < 0)
                {
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
public class Sorting {
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

**Note on the Rust implementation:** This version uses `unsafe` to express the conventional insertion-sort shape without imposing a `Copy` bound. In safe Rust, `arr[j] = arr[j - 1]` is a move out of an indexed slot when `T` is not `Copy`, which the borrow checker rejects; the alternatives are to require `T: Copy` (restricting generality) or to use adjacent `swap` calls (turning each shift into three assignments). The pointer operations here preserve the standard form: `ptr::read` bit-copies the key out without dropping the source, `ptr::copy_nonoverlapping` shifts elements right, and a final `ptr::write` places the key back. The loop maintains a single invariant — every `ptr::write` targets the current hole vacated by a preceding read — so no live value is ever overwritten, and each element is dropped exactly once by the slice itself. `ManuallyDrop` keeps the bit-copied key from being dropped twice, and `mem::forget(hole)` on the success path prevents the guard from writing the key a second time.

Panic safety is handled by the `InsertionHole` guard. Because `PartialOrd::lt` is allowed to panic, an unwind can occur while `key` and the vacated slot still refer to the same allocation, and while shifted slots temporarily hold duplicates bytes. On unwind, the guard copies `key` back into the current hole, restoring the slice to a consistent state and eliminating the duplicate ownership before the slice is dropped; `ManuallyDrop` ensures `key` itself is not dropped on the same unwind. The result is sound for any `T: PartialOrd`, at the cost of a few hundred lines of reasoning that a safe version would delegate to the compiler.

```rust
pub mod sorting {
    use std::{
        mem::{self, ManuallyDrop},
        ptr,
    };

    pub fn insertion_sort<T: PartialOrd>(arr: &mut [T]) {
        struct InsertionHole<U> {
            key_ptr: *const U,
            hole_ptr: *mut U,
        }

        impl<U> Drop for InsertionHole<U> {
            fn drop(&mut self) {
                // SAFETY: `key_ptr` and `hole_ptr` are valid, non-overlapping,
                // and the hole is logically uninitialized, so copying into it
                // cannot drop or duplicate a live value.
                unsafe {
                    ptr::copy_nonoverlapping(self.key_ptr, self.hole_ptr, 1);
                }
            }
        }

        if arr.len() <= 1 {
            return;
        }

        let n = arr.len();

        for i in 1..n {
            // SAFETY: `i` is in bounds and `arr[i]` is initialized;
            // `ptr::read` bit-copies it out without dropping, and
            // `ManuallyDrop` prevents the duplicate from being dropped on
            // unwind.
            let key = unsafe { ManuallyDrop::new(ptr::read(&arr[i])) };
            let mut hole = InsertionHole {
                key_ptr: &*key,
                hole_ptr: &mut arr[i],
            };
            let mut j = i;
            while j > 0 && &*key < &arr[j - 1] {
                // SAFETY: `j - 1` and `j` are in bounds and non-overlapping;
                // `arr[j]` is the current hole, so overwriting it drops
                // nothing.
                unsafe {
                    ptr::copy_nonoverlapping(&mut arr[j - 1], &mut arr[j], 1);
                }
                j -= 1;
                hole.hole_ptr = &mut arr[j];
            }
            mem::forget(hole);
            // SAFETY: `j` is in bounds and `arr[j]` is the final hole;
            // `into_inner` moves `key` out so it will not be dropped twice.
            unsafe {
                ptr::write(&mut arr[j], ManuallyDrop::into_inner(key));
            }
        }
    }
}
```

## Swift

```swift
public enum Sorting {
    public static func insertionSort<T: Comparable>(_ arr: inout [T]) {
        guard arr.count > 1 else {
            return
        }

        let n = arr.count

        for i in 1..<n {
            let key = arr[i]
            var j = i
            while j > 0 && key < arr[j - 1] {
                arr[j] = arr[j - 1]
                j -= 1
            }
            arr[j] = key
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
