---
comments: true
---

# 15.3 &nbsp; 最大容量问题

!!! question

    输入一个数组 $ht$ ，其中的每个元素代表一个垂直隔板的高度。数组中的任意两个隔板，以及它们之间的空间可以组成一个容器。
    
    容器的容量等于高度和宽度的乘积（面积），其中高度由较短的隔板决定，宽度是两个隔板的数组索引之差。
    
    请在数组中选择两个隔板，使得组成的容器的容量最大，返回最大容量。示例如图 15-7 所示。

![最大容量问题的示例数据](max_capacity_problem.assets/max_capacity_example.png){ class="animation-figure" }

<p align="center"> 图 15-7 &nbsp; 最大容量问题的示例数据 </p>

容器由任意两个隔板围成，**因此本题的状态为两个隔板的索引，记为 $[i, j]$** 。

根据题意，容量等于高度乘以宽度，其中高度由短板决定，宽度是两隔板的数组索引之差。设容量为 $cap[i, j]$ ，则可得计算公式：

$$
cap[i, j] = \min(ht[i], ht[j]) \times (j - i)
$$

设数组长度为 $n$ ，两个隔板的组合数量（状态总数）为 $C_n^2 = \frac{n(n - 1)}{2}$ 个。最直接地，**我们可以穷举所有状态**，从而求得最大容量，时间复杂度为 $O(n^2)$ 。

### 1. &nbsp; 贪心策略确定

这道题还有更高效率的解法。如图 15-8 所示，现选取一个状态 $[i, j]$ ，其满足索引 $i < j$ 且高度 $ht[i] < ht[j]$ ，即 $i$ 为短板、$j$ 为长板。

![初始状态](max_capacity_problem.assets/max_capacity_initial_state.png){ class="animation-figure" }

<p align="center"> 图 15-8 &nbsp; 初始状态 </p>

如图 15-9 所示，**若此时将长板 $j$ 向短板 $i$ 靠近，则容量一定变小**。

这是因为在移动长板 $j$ 后，宽度 $j-i$ 肯定变小；而高度由短板决定，因此高度只可能不变（ $i$ 仍为短板）或变小（移动后的 $j$ 成为短板）。

![向内移动长板后的状态](max_capacity_problem.assets/max_capacity_moving_long_board.png){ class="animation-figure" }

<p align="center"> 图 15-9 &nbsp; 向内移动长板后的状态 </p>

反向思考，**我们只有向内收缩短板 $i$ ，才有可能使容量变大**。因为虽然宽度一定变小，**但高度可能会变大**（移动后的短板 $i$ 可能会变长）。例如在图 15-10 中，移动短板后面积变大。

![向内移动短板后的状态](max_capacity_problem.assets/max_capacity_moving_short_board.png){ class="animation-figure" }

<p align="center"> 图 15-10 &nbsp; 向内移动短板后的状态 </p>

由此便可推出本题的贪心策略：初始化两指针，使其分列容器两端，每轮向内收缩短板对应的指针，直至两指针相遇。

图 15-11 展示了贪心策略的执行过程。

1. 初始状态下，指针 $i$ 和 $j$ 分列数组两端。
2. 计算当前状态的容量 $cap[i, j]$ ，并更新最大容量。
3. 比较板 $i$ 和板 $j$ 的高度，并将短板向内移动一格。
4. 循环执行第 `2.` 步和第 `3.` 步，直至 $i$ 和 $j$ 相遇时结束。

=== "<1>"
    ![最大容量问题的贪心过程](max_capacity_problem.assets/max_capacity_greedy_step1.png){ class="animation-figure" }

=== "<2>"
    ![max_capacity_greedy_step2](max_capacity_problem.assets/max_capacity_greedy_step2.png){ class="animation-figure" }

=== "<3>"
    ![max_capacity_greedy_step3](max_capacity_problem.assets/max_capacity_greedy_step3.png){ class="animation-figure" }

=== "<4>"
    ![max_capacity_greedy_step4](max_capacity_problem.assets/max_capacity_greedy_step4.png){ class="animation-figure" }

=== "<5>"
    ![max_capacity_greedy_step5](max_capacity_problem.assets/max_capacity_greedy_step5.png){ class="animation-figure" }

=== "<6>"
    ![max_capacity_greedy_step6](max_capacity_problem.assets/max_capacity_greedy_step6.png){ class="animation-figure" }

=== "<7>"
    ![max_capacity_greedy_step7](max_capacity_problem.assets/max_capacity_greedy_step7.png){ class="animation-figure" }

=== "<8>"
    ![max_capacity_greedy_step8](max_capacity_problem.assets/max_capacity_greedy_step8.png){ class="animation-figure" }

=== "<9>"
    ![max_capacity_greedy_step9](max_capacity_problem.assets/max_capacity_greedy_step9.png){ class="animation-figure" }

<p align="center"> 图 15-11 &nbsp; 最大容量问题的贪心过程 </p>

### 2. &nbsp; 代码实现

代码循环最多 $n$ 轮，**因此时间复杂度为 $O(n)$** 。

变量 $i$、$j$、$res$ 使用常数大小的额外空间，**因此空间复杂度为 $O(1)$** 。

=== "Python"

    ```python title="max_capacity.py"
    def max_capacity(ht: list[int]) -> int:
        """最大容量：贪心"""
        # 初始化 i, j，使其分列数组两端
        i, j = 0, len(ht) - 1
        # 初始最大容量为 0
        res = 0
        # 循环贪心选择，直至两板相遇
        while i < j:
            # 更新最大容量
            cap = min(ht[i], ht[j]) * (j - i)
            res = max(res, cap)
            # 向内移动短板
            if ht[i] < ht[j]:
                i += 1
            else:
                j -= 1
        return res
    ```

=== "C++"

    ```cpp title="max_capacity.cpp"
    /* 最大容量：贪心 */
    int maxCapacity(vector<int> &ht) {
        // 初始化 i, j，使其分列数组两端
        int i = 0, j = ht.size() - 1;
        // 初始最大容量为 0
        int res = 0;
        // 循环贪心选择，直至两板相遇
        while (i < j) {
            // 更新最大容量
            int cap = min(ht[i], ht[j]) * (j - i);
            res = max(res, cap);
            // 向内移动短板
            if (ht[i] < ht[j]) {
                i++;
            } else {
                j--;
            }
        }
        return res;
    }
    ```

=== "Java"

    ```java title="max_capacity.java"
    /* 最大容量：贪心 */
    int maxCapacity(int[] ht) {
        // 初始化 i, j，使其分列数组两端
        int i = 0, j = ht.length - 1;
        // 初始最大容量为 0
        int res = 0;
        // 循环贪心选择，直至两板相遇
        while (i < j) {
            // 更新最大容量
            int cap = Math.min(ht[i], ht[j]) * (j - i);
            res = Math.max(res, cap);
            // 向内移动短板
            if (ht[i] < ht[j]) {
                i++;
            } else {
                j--;
            }
        }
        return res;
    }
    ```

=== "C#"

    ```csharp title="max_capacity.cs"
    /* 最大容量：贪心 */
    int MaxCapacity(int[] ht) {
        // 初始化 i, j，使其分列数组两端
        int i = 0, j = ht.Length - 1;
        // 初始最大容量为 0
        int res = 0;
        // 循环贪心选择，直至两板相遇
        while (i < j) {
            // 更新最大容量
            int cap = Math.Min(ht[i], ht[j]) * (j - i);
            res = Math.Max(res, cap);
            // 向内移动短板
            if (ht[i] < ht[j]) {
                i++;
            } else {
                j--;
            }
        }
        return res;
    }
    ```

=== "Go"

    ```go title="max_capacity.go"
    /* 最大容量：贪心 */
    func maxCapacity(ht []int) int {
        // 初始化 i, j，使其分列数组两端
        i, j := 0, len(ht)-1
        // 初始最大容量为 0
        res := 0
        // 循环贪心选择，直至两板相遇
        for i < j {
            // 更新最大容量
            capacity := int(math.Min(float64(ht[i]), float64(ht[j]))) * (j - i)
            res = int(math.Max(float64(res), float64(capacity)))
            // 向内移动短板
            if ht[i] < ht[j] {
                i++
            } else {
                j--
            }
        }
        return res
    }
    ```

=== "Swift"

    ```swift title="max_capacity.swift"
    /* 最大容量：贪心 */
    func maxCapacity(ht: [Int]) -> Int {
        // 初始化 i, j，使其分列数组两端
        var i = ht.startIndex, j = ht.endIndex - 1
        // 初始最大容量为 0
        var res = 0
        // 循环贪心选择，直至两板相遇
        while i < j {
            // 更新最大容量
            let cap = min(ht[i], ht[j]) * (j - i)
            res = max(res, cap)
            // 向内移动短板
            if ht[i] < ht[j] {
                i += 1
            } else {
                j -= 1
            }
        }
        return res
    }
    ```

=== "JS"

    ```javascript title="max_capacity.js"
    /* 最大容量：贪心 */
    function maxCapacity(ht) {
        // 初始化 i, j，使其分列数组两端
        let i = 0,
            j = ht.length - 1;
        // 初始最大容量为 0
        let res = 0;
        // 循环贪心选择，直至两板相遇
        while (i < j) {
            // 更新最大容量
            const cap = Math.min(ht[i], ht[j]) * (j - i);
            res = Math.max(res, cap);
            // 向内移动短板
            if (ht[i] < ht[j]) {
                i += 1;
            } else {
                j -= 1;
            }
        }
        return res;
    }
    ```

=== "TS"

    ```typescript title="max_capacity.ts"
    /* 最大容量：贪心 */
    function maxCapacity(ht: number[]): number {
        // 初始化 i, j，使其分列数组两端
        let i = 0,
            j = ht.length - 1;
        // 初始最大容量为 0
        let res = 0;
        // 循环贪心选择，直至两板相遇
        while (i < j) {
            // 更新最大容量
            const cap: number = Math.min(ht[i], ht[j]) * (j - i);
            res = Math.max(res, cap);
            // 向内移动短板
            if (ht[i] < ht[j]) {
                i += 1;
            } else {
                j -= 1;
            }
        }
        return res;
    }
    ```

=== "Dart"

    ```dart title="max_capacity.dart"
    /* 最大容量：贪心 */
    int maxCapacity(List<int> ht) {
      // 初始化 i, j，使其分列数组两端
      int i = 0, j = ht.length - 1;
      // 初始最大容量为 0
      int res = 0;
      // 循环贪心选择，直至两板相遇
      while (i < j) {
        // 更新最大容量
        int cap = min(ht[i], ht[j]) * (j - i);
        res = max(res, cap);
        // 向内移动短板
        if (ht[i] < ht[j]) {
          i++;
        } else {
          j--;
        }
      }
      return res;
    }
    ```

=== "Rust"

    ```rust title="max_capacity.rs"
    /* 最大容量：贪心 */
    fn max_capacity(ht: &[i32]) -> i32 {
        // 初始化 i, j，使其分列数组两端
        let mut i = 0;
        let mut j = ht.len() - 1;
        // 初始最大容量为 0
        let mut res = 0;
        // 循环贪心选择，直至两板相遇
        while i < j {
            // 更新最大容量
            let cap = std::cmp::min(ht[i], ht[j]) * (j - i) as i32;
            res = std::cmp::max(res, cap);
            // 向内移动短板
            if ht[i] < ht[j] {
                i += 1;
            } else {
                j -= 1;
            }
        }
        res
    }
    ```

=== "C"

    ```c title="max_capacity.c"
    /* 最大容量：贪心 */
    int maxCapacity(int ht[], int htLength) {
        // 初始化 i, j，使其分列数组两端
        int i = 0;
        int j = htLength - 1;
        // 初始最大容量为 0
        int res = 0;
        // 循环贪心选择，直至两板相遇
        while (i < j) {
            // 更新最大容量
            int capacity = myMin(ht[i], ht[j]) * (j - i);
            res = myMax(res, capacity);
            // 向内移动短板
            if (ht[i] < ht[j]) {
                i++;
            } else {
                j--;
            }
        }
        return res;
    }
    ```

=== "Kotlin"

    ```kotlin title="max_capacity.kt"
    /* 最大容量：贪心 */
    fun maxCapacity(ht: IntArray): Int {
        // 初始化 i, j，使其分列数组两端
        var i = 0
        var j = ht.size - 1
        // 初始最大容量为 0
        var res = 0
        // 循环贪心选择，直至两板相遇
        while (i < j) {
            // 更新最大容量
            val cap = min(ht[i], ht[j]) * (j - i)
            res = max(res, cap)
            // 向内移动短板
            if (ht[i] < ht[j]) {
                i++
            } else {
                j--
            }
        }
        return res
    }
    ```

=== "Ruby"

    ```ruby title="max_capacity.rb"
    ### 最大容量：贪心 ###
    def max_capacity(ht)
      # 初始化 i, j，使其分列数组两端
      i, j = 0, ht.length - 1
      # 初始最大容量为 0
      res = 0

      # 循环贪心选择，直至两板相遇
      while i < j
        # 更新最大容量
        cap = [ht[i], ht[j]].min * (j - i)
        res = [res, cap].max
        # 向内移动短板
        if ht[i] < ht[j]
          i += 1
        else
          j -= 1
        end
      end

      res
    end
    ```

=== "Zig"

    ```zig title="max_capacity.zig"
    [class]{}-[func]{maxCapacity}
    ```

??? pythontutor "可视化运行"

    <div style="height: 549px; width: 100%;"><iframe class="pythontutor-iframe" src="https://pythontutor.com/iframe-embed.html#code=def%20max_capacity%28ht%3A%20list%5Bint%5D%29%20-%3E%20int%3A%0A%20%20%20%20%22%22%22%E6%9C%80%E5%A4%A7%E5%AE%B9%E9%87%8F%EF%BC%9A%E8%B4%AA%E5%BF%83%22%22%22%0A%20%20%20%20%23%20%E5%88%9D%E5%A7%8B%E5%8C%96%20i,%20j%EF%BC%8C%E4%BD%BF%E5%85%B6%E5%88%86%E5%88%97%E6%95%B0%E7%BB%84%E4%B8%A4%E7%AB%AF%0A%20%20%20%20i,%20j%20%3D%200,%20len%28ht%29%20-%201%0A%20%20%20%20%23%20%E5%88%9D%E5%A7%8B%E6%9C%80%E5%A4%A7%E5%AE%B9%E9%87%8F%E4%B8%BA%200%0A%20%20%20%20res%20%3D%200%0A%20%20%20%20%23%20%E5%BE%AA%E7%8E%AF%E8%B4%AA%E5%BF%83%E9%80%89%E6%8B%A9%EF%BC%8C%E7%9B%B4%E8%87%B3%E4%B8%A4%E6%9D%BF%E7%9B%B8%E9%81%87%0A%20%20%20%20while%20i%20%3C%20j%3A%0A%20%20%20%20%20%20%20%20%23%20%E6%9B%B4%E6%96%B0%E6%9C%80%E5%A4%A7%E5%AE%B9%E9%87%8F%0A%20%20%20%20%20%20%20%20cap%20%3D%20min%28ht%5Bi%5D,%20ht%5Bj%5D%29%20*%20%28j%20-%20i%29%0A%20%20%20%20%20%20%20%20res%20%3D%20max%28res,%20cap%29%0A%20%20%20%20%20%20%20%20%23%20%E5%90%91%E5%86%85%E7%A7%BB%E5%8A%A8%E7%9F%AD%E6%9D%BF%0A%20%20%20%20%20%20%20%20if%20ht%5Bi%5D%20%3C%20ht%5Bj%5D%3A%0A%20%20%20%20%20%20%20%20%20%20%20%20i%20%2B%3D%201%0A%20%20%20%20%20%20%20%20else%3A%0A%20%20%20%20%20%20%20%20%20%20%20%20j%20-%3D%201%0A%20%20%20%20return%20res%0A%0A%0A%22%22%22Driver%20Code%22%22%22%0Aif%20__name__%20%3D%3D%20%22__main__%22%3A%0A%20%20%20%20ht%20%3D%20%5B3,%208,%205,%202,%207,%207,%203,%204%5D%0A%0A%20%20%20%20%23%20%E8%B4%AA%E5%BF%83%E7%AE%97%E6%B3%95%0A%20%20%20%20res%20%3D%20max_capacity%28ht%29%0A%20%20%20%20print%28f%22%E6%9C%80%E5%A4%A7%E5%AE%B9%E9%87%8F%E4%B8%BA%20%7Bres%7D%22%29&codeDivHeight=472&codeDivWidth=350&cumulative=false&curInstr=4&heapPrimitives=nevernest&origin=opt-frontend.js&py=311&rawInputLstJSON=%5B%5D&textReferences=false"> </iframe></div>
    <div style="margin-top: 5px;"><a href="https://pythontutor.com/iframe-embed.html#code=def%20max_capacity%28ht%3A%20list%5Bint%5D%29%20-%3E%20int%3A%0A%20%20%20%20%22%22%22%E6%9C%80%E5%A4%A7%E5%AE%B9%E9%87%8F%EF%BC%9A%E8%B4%AA%E5%BF%83%22%22%22%0A%20%20%20%20%23%20%E5%88%9D%E5%A7%8B%E5%8C%96%20i,%20j%EF%BC%8C%E4%BD%BF%E5%85%B6%E5%88%86%E5%88%97%E6%95%B0%E7%BB%84%E4%B8%A4%E7%AB%AF%0A%20%20%20%20i,%20j%20%3D%200,%20len%28ht%29%20-%201%0A%20%20%20%20%23%20%E5%88%9D%E5%A7%8B%E6%9C%80%E5%A4%A7%E5%AE%B9%E9%87%8F%E4%B8%BA%200%0A%20%20%20%20res%20%3D%200%0A%20%20%20%20%23%20%E5%BE%AA%E7%8E%AF%E8%B4%AA%E5%BF%83%E9%80%89%E6%8B%A9%EF%BC%8C%E7%9B%B4%E8%87%B3%E4%B8%A4%E6%9D%BF%E7%9B%B8%E9%81%87%0A%20%20%20%20while%20i%20%3C%20j%3A%0A%20%20%20%20%20%20%20%20%23%20%E6%9B%B4%E6%96%B0%E6%9C%80%E5%A4%A7%E5%AE%B9%E9%87%8F%0A%20%20%20%20%20%20%20%20cap%20%3D%20min%28ht%5Bi%5D,%20ht%5Bj%5D%29%20*%20%28j%20-%20i%29%0A%20%20%20%20%20%20%20%20res%20%3D%20max%28res,%20cap%29%0A%20%20%20%20%20%20%20%20%23%20%E5%90%91%E5%86%85%E7%A7%BB%E5%8A%A8%E7%9F%AD%E6%9D%BF%0A%20%20%20%20%20%20%20%20if%20ht%5Bi%5D%20%3C%20ht%5Bj%5D%3A%0A%20%20%20%20%20%20%20%20%20%20%20%20i%20%2B%3D%201%0A%20%20%20%20%20%20%20%20else%3A%0A%20%20%20%20%20%20%20%20%20%20%20%20j%20-%3D%201%0A%20%20%20%20return%20res%0A%0A%0A%22%22%22Driver%20Code%22%22%22%0Aif%20__name__%20%3D%3D%20%22__main__%22%3A%0A%20%20%20%20ht%20%3D%20%5B3,%208,%205,%202,%207,%207,%203,%204%5D%0A%0A%20%20%20%20%23%20%E8%B4%AA%E5%BF%83%E7%AE%97%E6%B3%95%0A%20%20%20%20res%20%3D%20max_capacity%28ht%29%0A%20%20%20%20print%28f%22%E6%9C%80%E5%A4%A7%E5%AE%B9%E9%87%8F%E4%B8%BA%20%7Bres%7D%22%29&codeDivHeight=800&codeDivWidth=600&cumulative=false&curInstr=4&heapPrimitives=nevernest&origin=opt-frontend.js&py=311&rawInputLstJSON=%5B%5D&textReferences=false" target="_blank" rel="noopener noreferrer">全屏观看 ></a></div>

### 3. &nbsp; 正确性证明

之所以贪心比穷举更快，是因为每轮的贪心选择都会“跳过”一些状态。

比如在状态 $cap[i, j]$ 下，$i$ 为短板、$j$ 为长板。若贪心地将短板 $i$ 向内移动一格，会导致图 15-12 所示的状态被“跳过”。**这意味着之后无法验证这些状态的容量大小**。

$$
cap[i, i+1], cap[i, i+2], \dots, cap[i, j-2], cap[i, j-1]
$$

![移动短板导致被跳过的状态](max_capacity_problem.assets/max_capacity_skipped_states.png){ class="animation-figure" }

<p align="center"> 图 15-12 &nbsp; 移动短板导致被跳过的状态 </p>

观察发现，**这些被跳过的状态实际上就是将长板 $j$ 向内移动的所有状态**。前面我们已经证明内移长板一定会导致容量变小。也就是说，被跳过的状态都不可能是最优解，**跳过它们不会导致错过最优解**。

以上分析说明，移动短板的操作是“安全”的，贪心策略是有效的。
