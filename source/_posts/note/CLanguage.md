---
title: 2024 C语言程序设计
category: 笔记
tag: 总结
sticky: true
comment: true
abbrlink: 41659
date: 2023-09-08 17:51:00
---

# C语言与程序设计

## C语言特点

    1. 过程式语言：C语言是一种过程式编程语言，重点在于算法和逻辑的实现，以过程和函数为主要组织方式。
    2. 简洁高效：C语言设计简洁，执行高效，适合系统编程和底层开发。
    3. 指针：C语言提供了指针的概念，允许直接访问和操作内存地址，这在系统编程中很有用。
    4. 低级别控制：C语言提供了对计算机底层的直接控制，包括对硬件的访问、内存管理等。
    5. 面向过程：C语言是一种面向过程的语言，它将程序分解成一系列函数或过程，通过调用这些函数来完成任务。
- C++语言特点：
    1. 多范式编程：C++是一种多范式编程语言，支持面向对象编程、泛型编程和过程式编程。
    2. 面向对象：C++支持面向对象编程，提供类、继承、多态等面向对象的特性，使得代码更具可扩展性和可维护性。
    3. 标准模板库（STL）：C++标准库提供了丰富的模板类和函数，称为STL，包括容器、算法和迭代器等，可以大大提高开发效率。
    4. 异常处理：C++支持异常处理机制，允许在程序执行过程中抛出和捕获异常，提高了程序的健壮性。
    5. 运算符重载：C++允许运算符重载，使得用户自定义类型可以像内置类型一样使用运算符。
    6. 扩展性：C++提供了丰富的语言特性和库支持，可以进行底层编程，也可以用于开发大型、复杂的软件系统。
- 虽然C语言和C++有许多共同点，但C++作为C语言的超集，在功能上更加丰富，提供了更多的特性和工具，因此在实际应用中，开发者可以根据项目需求选择使用合适的语言。

## 计算机的解题过程
1. 分析问题
    - 在解决问题之前，需要完全理解问题的要求和约束条件。这可能需要对问题进行分析和拆解，以确保对问题的理解准确无误。
2. 设计算法
    - 一旦理解了问题，就需要设计一个算法来解决它。算法是一系列明确定义的步骤，用于解决特定问题或执行特定任务。在设计算法时，需要考虑效率、正确性和可维护性等因素。
3. 编写程序
    - 根据设计好的算法，编写相应的代码实现。编写的代码应该准确地反映算法的逻辑，并考虑到输入数据的边界情况和异常情况。
4. 运行验证
    - 编写完代码后，需要进行调试和测试以确保代码的正确性。这可能涉及在不同的输入数据上运行代码，并检查输出是否符合预期。如果发现错误，就需要进行调试，并修复代码中的问题。
5. 优化和改进
    - 一旦代码能够正确地解决问题，就可以考虑对代码进行优化和改进，以提高性能、减少资源消耗或使代码更易于理解和维护。优化可能涉及改进算法、优化数据结构或利用并行计算等技术。
6. 文档和维护
    - 最后，还需要编写文档来说明代码的功能、用法和限制。这有助于其他人理解和使用代码。此外，需要定期维护代码，以应对新的需求或发现的问题。

- PS: [这些步骤通常是迭代进行的，可能需要多次修改和调整代码，直到达到满意的解决方案。]{.blue}

## 算法的概念
- 算法是指解决问题或执行任务的一系列有序步骤。它是一种计算机程序的设计方法，通过确定性的、明确定义的步骤，将输入数据转换为输出结果。

## 算法的特性
1. 有序性：算法由一系列明确的步骤组成，每个步骤都有特定的执行顺序。
2. 确定性：算法对于给定的输入，在相同的条件下总是产生相同的输出，不受随机因素的影响。
3. 可行性：算法是可行的，可以在有限的时间内完成执行。
4. 输入输出：算法接受输入数据，并产生输出结果。
5. 解决问题：算法用于解决特定的问题或执行特定的任务。

## 算法的表示
- 算法可以用多种方式表示，以便于理解、实现和交流。以下是几种常见的表示方法：

    1. 自然语言的描述
    - 使用自然语言，如英语或中文，对算法进行文字描述。这种表示方法简单直观，适合于初步了解算法的工作原理。例如：
    ```txt
    步骤1：从输入数组中找到最小元素。
    步骤2：将最小元素放置在数组的第一个位置。
    步骤3：在剩余的元素中继续重复步骤1和步骤2，直到整个数组排序完成。
    ```
    2. 传统流程图（Flowchart）
    - 流程图是一种图形化的表示方法，用图形符号表示算法的执行流程和控制结构。流程图通常包括流程框（表示操作或处理）、连接线（表示流程的顺序）、判断框（表示条件判断）等元素。例如：
    ```css
    Start -> Initialize variable i to 0
       -> Repeat until i reaches the length of array A
           -> Find the index of the minimum element in the unsorted part of A
           -> Swap the minimum element with the first element in the unsorted part of A
           -> Increment i
       -> End
    ```
    3. N-S流程图
    4. 伪代码（Pseudocode）
    - 伪代码是一种介于自然语言和实际编程语言之间的表示方法，它结合了编程语言的结构和自然语言的表达方式，以简单的方式描述算法的逻辑流程和步骤。例如：
    ```less
    procedure SelectionSort(A: list of sortable items)
    n = length(A)
    for i = 0 to n-1
        min_index = i
        for j = i+1 to n
            if A[j] < A[min_index]
                min_index = j
        swap A[i] with A[min_index]
    ```
    5. 编程语言代码
    - 算法可以直接用编程语言代码表示，这种表示方法最为具体和精确，可以直接用于实现算法。不同的编程语言可以用不同的语法来表示同一个算法。例如，使用c 和 c++ 表示选择排序算法：
    ```c
    #include <stdio.h>
    // 选择排序函数
    void selection_sort(int A[], int n) {
        // 遍历数组
        for (int i = 0; i < n; i++) {
            // 找到未排序部分的最小元素的索引
            int min_index = i;
            for (int j = i + 1; j < n; j++) {
                if (A[j] < A[min_index]) {
                    min_index = j;
                }
            }
            // 将最小元素与当前位置交换
            int temp = A[i];
            A[i] = A[min_index];
            A[min_index] = temp;
        }
    }

    int main() {
        int A[] = {64, 25, 12, 22, 11};
        int n = sizeof(A) / sizeof(A[0]);
        // 调用选择排序函数
        selection_sort(A, n);
        printf("Sorted array: ");
        for (int i = 0; i < n; i++) {
        printf("%d ", A[i]);
        }
        printf("\n");
        return 0;
    }
    ```

    ```cpp
    #include <iostream>

    // 选择排序函数
    void selection_sort(int A[], int n) {
        // 遍历数组
        for (int i = 0; i < n; i++) {
            // 找到未排序部分的最小元素的索引
            int min_index = i;
            for (int j = i + 1; j < n; j++) {
                if (A[j] < A[min_index]) {
                    min_index = j;
                }
            }
            // 将最小元素与当前位置交换
            std::swap(A[i], A[min_index]);
        }
    }

    int main() {
        int A[] = {64, 25, 12, 22, 11};
        int n = sizeof(A) / sizeof(A[0]);
        // 调用选择排序函数
        selection_sort(A, n);
        std::cout << "Sorted array: ";
        for (int i = 0; i < n; i++) {
            std::cout << A[i] << " ";
        }
        std::cout << std::endl;
        return 0;
    }
    ```
    - 选择合适的表示方法取决于解释算法的目的、目标受众以及所处的环境和情境。
## 常用的算法介绍
1. 枚举法
2. 递推法
3. 递归法
4. 回溯法
5. 贪婪法 （贪心算法）
    1. 最小生成树（Minimum Spanning Tree，MST）：从图中选择边的子集，使得这些边连接了所有节点，且总权重最小。
    2. 最短路径（Shortest Path）：寻找两个节点之间的最短路径，以最小化路径的总权重。
6. 动态规划法
    1. 斐波那契数列（Fibonacci Sequence）：通过将问题分解为更小的子问题，并保存子问题的解，来避免重复计算，从而提高效率。
    2. 最长公共子序列（Longest Common Subsequence，LCS）：寻找两个序列中的最长子序列，使得它在两个原始序列中同时出现，且不一定连续。

7. 排序算法：
    1. 冒泡排序（Bubble Sort）：通过多次遍历数组，依次比较相邻的元素，并交换它们，将最大（或最小）的元素逐步“冒泡”到数组的顶端。
    2. 选择排序（Selection Sort）：每次遍历数组，选择未排序部分的最小元素，并将其放置到已排序部分的末尾。
    3. 插入排序（Insertion Sort）：将数组分为已排序和未排序两部分，每次将未排序部分的第一个元素插入到已排序部分的适当位置3
    4. **快速排序（Quick Sort）**：选择一个基准元素，将数组分为小于基准元素和大于基准元素的两部分，然后递归地对这两部分进行排序。
8. 搜索算法：
    1. 线性搜索（Linear Search）：逐个检查数组中的元素，直到找到目标元素或遍历完整个数组。
    2. 二分搜索（Binary Search）：仅适用于有序数组，通过反复将目标值与数组中间元素比较，来确定目标值在数组中的位置。
9. 图算法
    1. 深度优先搜索（Depth-First Search，DFS）：遍历图的节点，沿着路径尽可能深入，直到无法再继续为止，然后回溯并继续探索其他路径。
    2. 广度优先搜索（Breadth-First Search，BFS）：从起始节点开始，逐层遍历图的节点，先访问所有与起始节点相邻的节点，然后依次访问它们的相邻节点。

- PS: 这些算法只是众多常用算法的[一部分]{.blue}，它们应用广泛，可以用于解决各种不同类型的问题。

### 算法示例
- 题目： 假设一万个数组相同的数，查找出来，要求时间复杂度和空间复杂度最优

1. 分析思路
    - 理想情况下，我们可以通过对这些数组进行一次遍历来找到所有相同的值。以下是一种可能的方法，时间复杂度为 O(N)，其中 N 是所有数组中元素的总数，空间复杂度也是 O(N)，因为我们需要一个哈希集合来存储已经遇到的元素。
2. 算法步骤
    1.  遍历每个数组，并将数组中的每个元素添加到一个哈希集合中。
    2. 如果遇到的元素已经在哈希集合中存在，则将其添加到结果集中。
3. 编写代码
- Java版本：
    ```java
    import java.util.ArrayList;
    import java.util.HashMap;
    import java.util.List;
    import java.util.Map;

    public class FindDuplicates {

        public static List<Integer> findDuplicates(List<int[]> arrays) {
            // 创建一个哈希集合来存储已经遇到的元素
            Map<Integer, Integer> seen = new HashMap<>();
            // 创建一个结果列表来存储重复的元素
            List<Integer> duplicates = new ArrayList<>();

            // 遍历每个数组
            for (int[] array : arrays) {
                // 遍历数组中的每个元素
                for (int item : array) {
                    // 如果元素已经在哈希集合中存在，则将其添加到结果列表中
                    if (seen.containsKey(item)) {
                        // 只有当元素第二次出现时才添加到结果列表中，避免重复添加
                        if (seen.get(item) == 1) {
                            duplicates.add(item);
                        }
                    } else {
                        // 如果元素第一次出现，则将其添加到哈希集合中
                        seen.put(item, 1);
                    }
                }
            }

            return duplicates;
        }

        public static void main(String[] args) {
            // 示例输入
            List<int[]> arrays = new ArrayList<>();
            arrays.add(new int[]{1, 2, 3, 4});
            arrays.add(new int[]{3, 4, 5, 6});
            arrays.add(new int[]{5, 6, 7, 8});
            arrays.add(new int[]{1, 5, 9, 10});

            // 查找重复的元素
            List<Integer> duplicates = findDuplicates(arrays);
            System.out.println("Duplicates: " + duplicates);
        }
    }
    ```
- C++版本：
    ```cpp
    #include <iostream>
    #include <vector>
    #include <unordered_map>

    using namespace std;

    vector<int> findDuplicates(vector<vector<int>>& arrays) {
        // 创建一个哈希表来存储已经遇到的元素
        unordered_map<int, int> seen;
        // 创建一个结果向量来存储重复的元素
        vector<int> duplicates;

        // 遍历每个数组
        for (auto& array : arrays) {
            // 遍历数组中的每个元素
            for (int item : array) {
                // 如果元素已经在哈希表中存在，则将其添加到结果向量中
                if (seen.find(item) != seen.end()) {
                    // 只有当元素第二次出现时才添加到结果向量中，避免重复添加
                    if (seen[item] == 1) {
                        duplicates.push_back(item);
                    }
                } else {
                    // 如果元素第一次出现，则将其添加到哈希表中
                    seen[item] = 1;
                }
            }
        }

        return duplicates;
    }

    int main() {
        // 示例输入
        vector<vector<int>> arrays = {
            {1, 2, 3, 4},
            {3, 4, 5, 6},
            {5, 6, 7, 8},
            {1, 5, 9, 10}
        };

        // 查找重复的元素
        vector<int> duplicates = findDuplicates(arrays);
        cout << "Duplicates: ";
        for (int item : duplicates) {
            cout << item << " ";
        }
        cout << endl;

        return 0;
    }
    ```
- 结构化程序的设计基本思想
    1. 采用自顶向下，逐步求精的程序设计
    2. 任何程序只使用顺序、选择和循环这三种基本控制结构

# C语言基本概念

- 简单的C语言程序
    - 简单输出一个 [hello world]{.rainbow} 字符串，表明向全世界宣布 "C语言我来了！"

- 关键字和标识符

    - 字符集
    - 关键字
    - 标识符

- 数据类型
    - 基本类型
        - 整数类型
        - 浮点类型
        - 字符类型
        - 枚举类型
    - 构造类型
    - 指针类型
    - 空类型 (void)

- 常量和变量
    - 整数常量
    - 浮点数常量
    - 字符常量
    - 字符串常量
```c
 #define PI 3.14159
```
- 变量
    - 变量声明
        - 格式： 数据类型  变量名列表
    - 变量初始化

- 运算符和表达式
    - 算术运算符
    - 自增自减运算符
    - 算术运算符 (实际运用中少，考试出题较多)
    - 赋值运算符
    - 其他运算符
        - 逗号运算符
        - 条件运算符
        - 求字节运算符 (sizeof())

- 数据类型转换
    - 隐式转换
        - 自动类型类型转换
        - 赋值类型转换
    - 显式转换
        - 强制类型转换
# 程序控制结构

- 顺序结构
    - 赋值
        - =
    - 数据输出
        - putchar() , printf()
    - 数据输入
        - getchar() , scanf()
- 选择结构
    - 关系运算符、关系表达式
    - 逻辑运算符、逻辑表达式
    - if, else, else if, switch
    - 选择嵌套 
- 循环结构
    - while
    - do while
    - for
    - 循环嵌套

# 数组和字符串

- 一维数组

- 冒泡排序

- 选择排序

- 二维数组

- 多维数组


# 指针
- 指针是表示计算机内存地址的数据类型，
    - 指针就是地址，指针变量就是存储地址的变量
# 函数
- 定义 ：C语言和C++都使用函数来组织和重用代码。函数是一段可重用的代码块，它可以接受输入（参数）、执行特定的任务，并返回结果。下面是它们的基本格式：

    ```c
    返回类型 函数名(参数列表) {
        // 函数体
        // 可以包含声明、语句等
        return 返回值; // 返回结果，如果函数有返回类型的话
    }
    ```
    1. 返回类型（Return Type）: 函数执行完任务后返回的数据类型。如果函数不返回任何值，则返回类型为void。
    2. 函数名（Function Name）: 函数的标识符，用于调用函数。
    3. 参数列表（Parameter List）: 可选项，用于传递数据给函数。参数列表中可以包含零个或多个参数。
    4. 函数体（Function Body）: 函数的具体实现，包含了需要执行的代码。
    5. 返回值（Return Value）: 如果函数有返回类型，则使用return语句返回结果。

    ```cpp
    返回类型 函数名(参数列表) {
    // 函数体
    // 可以包含声明、语句等
        return 返回值; // 返回结果，如果函数有返回类型的话
    }
    ```
    - C++函数的格式与C语言相似，但C++增加了一些特性，例如函数重载、默认参数、函数模板等。

    - 在C++中，你还可以定义类成员函数，其格式为：
    ```cpp
    class 类名 {
    public:
        返回类型 函数名(参数列表) {
            // 函数体
            // 可以包含声明、语句等
            return 返回值; // 返回结果，如果函数有返回类型的话
        }
    };
    ```
    - 这样的函数属于类的一部分，称为成员函数
# 结构体、共用体和枚举

# 文件

# 编译预处理

# 实验指导