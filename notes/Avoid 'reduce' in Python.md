下面是 Python 中一些“缩减”操作和一些相关的工具，这些操作或工具通常比 `reduce` 函数更加高效和易读：

| Operation          | With `functools.reduce`            | Without `reduce`            |
| ------------------ | ---------------------------------- | --------------------------- |
| Sum all            | `reuce(lambda x, y: x+y, nums)`    | `sum(nums)`                 |
| Multiply all       | `reduce(lambda x, y: x*y, nums)`   | `math.prod(nums)`           |
| Join strings       | `reduce(lambda s, t: s+t, strs)`   | `"".join(strs)`             |
| Merge dictionaries | `reduce(lambda g, h: g\|h, cfgs)`  | `ChainMap(*reversed(cfgs))` |
| Set union          | `reduce(lambda s, t: s\|t, sets )` | `set.union(sets)`           |
| Set intersection   | `reduce(lambda s, t: s&t, sets)`   | `set.intersection(*sets)`   | 
