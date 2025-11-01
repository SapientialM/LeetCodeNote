## 使用Stream做包装类数组到基本数据类型数组转换

```java
int[] res = resList.stream().mapToInt(Integer::intValue).toArray();
```

## 代码分解

### 1. `resList.stream()`

- 将 `List<Integer>` 转换为一个 **Stream**（流）
- Stream 是 Java 8 引入的函数式编程特性，用于对集合进行高效的数据处理

### 2. `.mapToInt(Integer::intValue)`

- **`mapToInt`**：将 Stream 中的元素映射为 `int` 类型
- **`Integer::intValue`**：方法引用，相当于 `x -> x.intValue()`
  - 将 `Integer` 对象转换为 `int` 基本类型（拆箱）
  - 例如：`Integer(5)` → `5`

### 3. `.toArray()`

- 将 IntStream 中的元素收集到一个 `int[]` 数组中
- 这个方法专门为 IntStream 设计，返回的是 `int[]` 而不是 `Integer[]`

## 执行流程示例

假设 `resList = [1, 2, 3, 4, 5]`：

```
List<Integer> → Stream<Integer> → IntStream → int[]
    [1,2,3,4,5]  →  1,2,3,4,5   →  1,2,3,4,5  → [1,2,3,4,5]
```

## 更简洁的写法

实际上，可以进一步简化：

```java
int[] res = resList.stream().mapToInt(i -> i).toArray();
```

或者：

```java
int[] res = resList.stream().mapToInt(Integer::intValue).toArray();
```

因为 `Integer` 到 `int` 的拆箱是自动的，`i -> i` 就足够了。