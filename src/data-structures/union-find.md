# Union-Find

This implementation of Union-Find (Disjoint Set Union, DSU) provides the following operations:

1. Create a new structure with $n$ elements, each in its own set.
2. Unite the sets containing $x$ and $y$. If $x$ and $y$ are already in the same set, nothing happens.
3. Check if two elements $x$ and $y$ are in the same set.
4. Count the total number of elements.
5. Count the total number of distinct sets.
6. Get the size (number of elements) of the set containing $x$.
7. Add $m$ elements, each in its own set.

This structure only supports (unsigned) integers as its elements. To maintain other types, use a hash map.

## C

```c
#include <stdlib.h>

typedef struct {
    size_t elem_count;
    size_t set_count;
    size_t* father;
    size_t* size;
} UnionFind;

void union_find_init(UnionFind* uf, size_t cnt);
void union_find_free(UnionFind* uf);
size_t union_find_find_root(UnionFind* uf, size_t x);
bool union_find_connected(UnionFind* uf, size_t x, size_t y);
void union_find_unite(UnionFind* uf, size_t x, size_t y);
size_t union_find_set_size(UnionFind* uf, size_t x);
size_t union_find_elements_count(UnionFind* uf);
size_t union_find_sets_count(UnionFind* uf);
void union_find_add_elements(UnionFind* uf, size_t cnt);

void union_find_init(UnionFind* uf, size_t cnt) {
    uf->elem_count = cnt;
    uf->set_count = cnt;
    uf->father = (size_t*)malloc(sizeof(size_t) * cnt);
    uf->size = (size_t*)malloc(sizeof(size_t) * cnt);
    for (size_t i = 0; i < cnt; i++) {
        uf->father[i] = i;
        uf->size[i] = 1;
    }
}

void union_find_free(UnionFind* uf) {
    uf->elem_count = 0;
    uf->set_count = 0;
    free(uf->father);
    free(uf->size);
    uf->father = NULL;
    uf->size = NULL;
}

size_t union_find_find_root(UnionFind* uf, size_t x) {
    size_t root = x;
    while (uf->father[root] != root) {
        root = uf->father[root];
    }
    while (uf->father[x] != root) {
        size_t next = uf->father[x];
        uf->father[x] = root;
        x = next;
    }
    return root;
}

bool union_find_connected(UnionFind* uf, size_t x, size_t y) {
    return union_find_find_root(uf, x) == union_find_find_root(uf, y);
}

void union_find_unite(UnionFind* uf, size_t x, size_t y) {
    size_t root_x = union_find_find_root(uf, x);
    size_t root_y = union_find_find_root(uf, y);
    if (root_x != root_y) {
        if (uf->size[root_x] < uf->size[root_y]) {
            size_t temp = root_x;
            root_x = root_y;
            root_y = temp;
        }
        uf->father[root_y] = root_x;
        uf->size[root_x] += uf->size[root_y];
        uf->set_count--;
    }
}

size_t union_find_set_size(UnionFind* uf, size_t x) {
    return uf->size[union_find_find_root(uf, x)];
}

size_t union_find_elements_count(UnionFind* uf) {
    return uf->elem_count;
}

size_t union_find_sets_count(UnionFind* uf) {
    return uf->set_count;
}

void union_find_add_elements(UnionFind* uf, size_t cnt) {
    if (cnt == 0) {
        return;
    }

    size_t new_size = uf->elem_count + cnt;
    uf->father = (size_t*)realloc(uf->father, sizeof(size_t) * new_size);
    uf->size = (size_t*)realloc(uf->size, sizeof(size_t) * new_size);
    for (size_t i = uf->elem_count; i < new_size; i++) {
        uf->father[i] = i;
        uf->size[i] = 1;
    }
    uf->elem_count += cnt;
    uf->set_count += cnt;
}
```

## C#

```csharp
namespace DataStructures
{
    public class UnionFind
    {
        private int elemCount;
        private int setCount;
        private int[] father;
        private int[] size;

        public UnionFind(int cnt)
        {
            elemCount = cnt;
            setCount = cnt;
            father = new int[cnt];
            size = new int[cnt];
            for (int i = 0; i < cnt; i++)
            {
                father[i] = i;
                size[i] = 1;
            }
        }

        private int FindRoot(int x)
        {
            int root = x;
            while (father[root] != root)
            {
                root = father[root];
            }
            while (father[x] != root)
            {
                int next = father[x];
                father[x] = root;
                x = next;
            }
            return root;
        }

        public bool Connected(int x, int y) => FindRoot(x) == FindRoot(y);

        public void Unite(int x, int y)
        {
            int rootX = FindRoot(x);
            int rootY = FindRoot(y);
            if (rootX != rootY)
            {
                if (size[rootX] < size[rootY])
                {
                    (rootX, rootY) = (rootY, rootX);
                }
                father[rootY] = rootX;
                size[rootX] += size[rootY];
                setCount--;
            }
        }

        public int SetSize(int x) => size[FindRoot(x)];

        public int ElementsCount => elemCount;

        public int SetsCount => setCount;

        public void AddElements(int cnt)
        {
            if (cnt == 0)
            {
                return;
            }

            int newSize = elemCount + cnt;
            Array.Resize(ref father, newSize);
            Array.Resize(ref size, newSize);
            for (int i = elemCount; i < newSize; i++)
            {
                father[i] = i;
                size[i] = 1;
            }
            elemCount += cnt;
            setCount += cnt;
        }
    }
}
```

## C++

```cpp
#include <cstdlib>
#include <utility>
#include <vector>

namespace data_structures {
    using std::size_t;

    class UnionFind {
    private:
        size_t elem_count;
        size_t set_count;
        mutable std::vector<size_t> father;
        std::vector<size_t> size;

        size_t find_root(size_t x) const;

    public:
        UnionFind(size_t cnt);
        bool connected(size_t x, size_t y) const;
        void unite(size_t x, size_t y);
        size_t set_size(size_t x) const;
        size_t elements_count() const;
        size_t sets_count() const;
        void add_elements(size_t cnt);
    };
}

namespace data_structures {
    UnionFind::UnionFind(size_t cnt)
        : elem_count(cnt),
          set_count(cnt),
          father(cnt),
          size(cnt) {
        for (size_t i = 0; i < cnt; i++) {
            father[i] = i;
            size[i] = 1;
        }
    }

    size_t UnionFind::find_root(size_t x) const {
        size_t root = x;
        while (father[root] != root) {
            root = father[root];
        }
        while (father[x] != root) {
            size_t next = father[x];
            father[x] = root;
            x = next;
        }
        return root;
    }

    bool UnionFind::connected(size_t x, size_t y) const {
        return find_root(x) == find_root(y);
    }

    void UnionFind::unite(size_t x, size_t y) {
        size_t root_x = find_root(x);
        size_t root_y = find_root(y);
        if (root_x != root_y) {
            if (size[root_x] < size[root_y]) {
                std::swap(root_x, root_y);
            }
            father[root_y] = root_x;
            size[root_x] += size[root_y];
            set_count--;
        }
    }

    size_t UnionFind::set_size(size_t x) const {
        return size[find_root(x)];
    }

    size_t UnionFind::elements_count() const {
        return elem_count;
    }

    size_t UnionFind::sets_count() const {
        return set_count;
    }

    void UnionFind::add_elements(size_t cnt) {
        if (cnt == 0) {
            return;
        }

        size_t new_size = elem_count + cnt;
        father.resize(new_size);
        size.resize(new_size);
        for (size_t i = elem_count; i < new_size; i++) {
            father[i] = i;
            size[i] = 1;
        }
        elem_count += cnt;
        set_count += cnt;
    }
}
```

## Go

```go
package main

import (
	"slices"
)

type UnionFind struct {
	elemCount int
	setCount  int
	father    []int
	size      []int
}

func NewUnionFind(cnt int) *UnionFind {
	uf := &UnionFind{
		elemCount: cnt,
		setCount:  cnt,
		father:    make([]int, cnt),
		size:      make([]int, cnt),
	}

	for i := range cnt {
		uf.father[i] = i
		uf.size[i] = 1
	}

	return uf
}

func (uf *UnionFind) findRoot(x int) int {
	root := x
	for uf.father[root] != root {
		root = uf.father[root]
	}
	for uf.father[x] != root {
		next := uf.father[x]
		uf.father[x] = root
		x = next
	}
	return root
}

func (uf *UnionFind) Connected(x, y int) bool {
	return uf.findRoot(x) == uf.findRoot(y)
}

func (uf *UnionFind) Unite(x, y int) {
	rootX := uf.findRoot(x)
	rootY := uf.findRoot(y)
	if rootX != rootY {
		if uf.size[rootX] < uf.size[rootY] {
			rootX, rootY = rootY, rootX
		}
		uf.father[rootY] = rootX
		uf.size[rootX] += uf.size[rootY]
		uf.setCount--
	}
}

func (uf *UnionFind) SetSize(x int) int {
	return uf.size[uf.findRoot(x)]
}

func (uf *UnionFind) ElementsCount() int {
	return uf.elemCount
}

func (uf *UnionFind) SetsCount() int {
	return uf.setCount
}

func (uf *UnionFind) AddElements(cnt int) {
	if cnt == 0 {
		return
	}

	newSize := uf.elemCount + cnt
	uf.father = slices.Grow(uf.father, cnt)[:newSize]
	uf.size = slices.Grow(uf.size, cnt)[:newSize]
	for i := uf.elemCount; i < newSize; i++ {
		uf.father[i] = i
		uf.size[i] = 1
	}
	uf.elemCount += cnt
	uf.setCount += cnt
}
```

## Java

```java
import java.util.Arrays;

public class UnionFind {
    private int elemCount;
    private int setCount;
    private int[] father;
    private int[] size;

    public UnionFind(int cnt) {
        elemCount = cnt;
        setCount = cnt;
        father = new int[cnt];
        size = new int[cnt];
        for (int i = 0; i < cnt; i++) {
            father[i] = i;
            size[i] = 1;
        }
    }

    private int findRoot(int x) {
        int root = x;
        while (father[root] != root) {
            root = father[root];
        }
        while (father[x] != root) {
            int next = father[x];
            father[x] = root;
            x = next;
        }
        return root;
    }

    public boolean connected(int x, int y) {
        return findRoot(x) == findRoot(y);
    }

    public void unite(int x, int y) {
        int rootX = findRoot(x);
        int rootY = findRoot(y);
        if (rootX != rootY) {
            if (size[rootX] < size[rootY]) {
                int temp = rootX;
                rootX = rootY;
                rootY = temp;
            }
            father[rootY] = rootX;
            size[rootX] += size[rootY];
            setCount--;
        }
    }

    public int setSize(int x) {
        return size[findRoot(x)];
    }

    public int elementsCount() {
        return elemCount;
    }

    public int setsCount() {
        return setCount;
    }

    public void addElements(int cnt) {
        if (cnt == 0) {
            return;
        }

        int newSize = elemCount + cnt;
        father = Arrays.copyOf(father, newSize);
        size = Arrays.copyOf(size, newSize);
        for (int i = elemCount; i < newSize; i++) {
            father[i] = i;
            size[i] = 1;
        }
        elemCount += cnt;
        setCount += cnt;
    }
}
```

## Python

```python
class UnionFind:
    def __init__(self, cnt: int):
        self.elem_count = cnt
        self.set_count = cnt
        self.father = list(range(cnt))
        self.size = [1] * cnt

    def __find_root(self, x: int) -> int:
        root = x
        while self.father[root] != root:
            root = self.father[root]
        while self.father[x] != root:
            next_ = self.father[x]
            self.father[x] = root
            x = next_
        return root

    def connected(self, x: int, y: int) -> bool:
        return self.__find_root(x) == self.__find_root(y)

    def unite(self, x: int, y: int):
        root_x = self.__find_root(x)
        root_y = self.__find_root(y)
        if root_x != root_y:
            if self.size[root_x] < self.size[root_y]:
                root_x, root_y = root_y, root_x
            self.father[root_y] = root_x
            self.size[root_x] += self.size[root_y]
            self.set_count -= 1

    def set_size(self, x: int) -> int:
        return self.size[self.__find_root(x)]

    @property
    def elements_count(self) -> int:
        return self.elem_count

    @property
    def sets_count(self) -> int:
        return self.set_count

    def add_elements(self, cnt: int):
        if cnt == 0:
            return

        new_size = self.elem_count + cnt
        self.father.extend(range(self.elem_count, new_size))
        self.size.extend([1] * cnt)
        self.elem_count += cnt
        self.set_count += cnt
```

## Rust

```rust
pub mod data_structures {
    pub struct UnionFind {
        elem_count: usize,
        set_count: usize,
        father: Vec<usize>,
        size: Vec<usize>,
    }

    impl UnionFind {
        pub fn new(cnt: usize) -> Self {
            Self {
                elem_count: cnt,
                set_count: cnt,
                father: (0..cnt).collect(),
                size: vec![1; cnt],
            }
        }

        fn find_root(&mut self, mut x: usize) -> usize {
            let mut root = x;
            while self.father[root] != root {
                root = self.father[root];
            }
            while self.father[x] != root {
                let next = self.father[x];
                self.father[x] = root;
                x = next;
            }
            root
        }

        pub fn connected(&mut self, x: usize, y: usize) -> bool {
            self.find_root(x) == self.find_root(y)
        }

        pub fn unite(&mut self, x: usize, y: usize) {
            let mut root_x = self.find_root(x);
            let mut root_y = self.find_root(y);
            if root_x != root_y {
                if self.size[root_x] < self.size[root_y] {
                    std::mem::swap(&mut root_x, &mut root_y);
                }
                self.father[root_y] = root_x;
                self.size[root_x] += self.size[root_y];
                self.set_count -= 1;
            }
        }

        pub fn set_size(&mut self, x: usize) -> usize {
            let root = self.find_root(x);
            self.size[root]
        }

        pub fn elements_count(&self) -> usize {
            self.elem_count
        }

        pub fn sets_count(&self) -> usize {
            self.set_count
        }

        pub fn add_elements(&mut self, cnt: usize) {
            if cnt == 0 {
                return;
            }

            let new_size = self.elem_count + cnt;
            self.father.extend(self.elem_count..new_size);
            self.size.resize(new_size, 1);
            self.elem_count += cnt;
            self.set_count += cnt;
        }
    }
}
```

## Swift

```swift
public enum DataStructures {
    public struct UnionFind {
        private var elemCount: Int
        private var setCount: Int
        private var father: [Int]
        private var size: [Int]

        public init(count: Int) {
            elemCount = count
            setCount = count
            father = Array(0..<count)
            size = Array(repeating: 1, count: count)
        }

        private mutating func findRoot(of x: Int) -> Int {
            var x = x
            var root = x
            while father[root] != root {
                root = father[root]
            }
            while father[x] != root {
                let next = father[x]
                father[x] = root
                x = next
            }
            return root
        }

        public mutating func connected(x: Int, y: Int) -> Bool {
            findRoot(of: x) == findRoot(of: y)
        }

        public mutating func unite(x: Int, y: Int) {
            var rootX = findRoot(of: x)
            var rootY = findRoot(of: y)
            if rootX != rootY {
                if size[rootX] < size[rootY] {
                    (rootX, rootY) = (rootY, rootX)
                }
                father[rootY] = rootX
                size[rootX] += size[rootY]
                setCount -= 1
            }
        }

        public mutating func setSize(containing x: Int) -> Int {
            size[findRoot(of: x)]
        }

        public var elementsCount: Int { elemCount }

        public var setsCount: Int { setCount }

        public mutating func addElements(count: Int) {
            guard count > 0 else {
                return
            }

            let newSize = elemCount + count
            father.append(contentsOf: elemCount..<newSize)
            size.append(contentsOf: repeatElement(1, count: count))
            elemCount += count
            setCount += count
        }
    }
}
```

## TypeScript

```typescript
export class UnionFind {
    private elemCount: number;
    private setCount: number;
    private father: number[];
    private size: number[];

    constructor(cnt: number) {
        this.elemCount = cnt;
        this.setCount = cnt;
        this.father = Array.from({ length: cnt }, (_, i) => i);
        this.size = new Array(cnt).fill(1);
    }

    private findRoot(x: number): number {
        let root = x;
        while (this.father[root] !== root) {
            root = this.father[root];
        }
        while (this.father[x] !== root) {
            const next = this.father[x];
            this.father[x] = root;
            x = next;
        }
        return root;
    }

    connected(x: number, y: number): boolean {
        return this.findRoot(x) === this.findRoot(y);
    }

    unite(x: number, y: number) {
        let rootX = this.findRoot(x);
        let rootY = this.findRoot(y);
        if (rootX !== rootY) {
            if (this.size[rootX] < this.size[rootY]) {
                [rootX, rootY] = [rootY, rootX];
            }
            this.father[rootY] = rootX;
            this.size[rootX] += this.size[rootY];
            this.setCount--;
        }
    }

    setSize(x: number): number {
        return this.size[this.findRoot(x)];
    }

    get elementsCount(): number {
        return this.elemCount;
    }

    get setsCount(): number {
        return this.setCount;
    }

    addElements(cnt: number) {
        if (cnt === 0) {
            return;
        }

        const newFather = Array.from({ length: cnt }, (_, i) => this.elemCount + i);
        const newSize = new Array(cnt).fill(1);
        this.father = this.father.concat(newFather);
        this.size = this.size.concat(newSize);
        this.elemCount += cnt;
        this.setCount += cnt;
    }
}
```
