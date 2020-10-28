**第一题**

```
package org.aicaprio

/**
 * @Author 王硕
 * @Create 2020-10-28
 * @Desc 实现一个 LRU 容器，丢弃近期最少访问的元素。
 */
class LRUCache(/* 最大缓存数 */ private val maxSize: Int) {

    /**
     *  缓存的内容
     */
    private val mData: MutableMap<Int, Node> = HashMap()

    /**
     * 固定的头节点，不包含在[mData]中
     */
    private val mHead = Node()

    /**
     *  固定的尾节点，不包含在[mData]中
     */
    private val mTail = Node()

    /**
     * 当前缓存数
     */
    private var size = 0

    init {
        mHead.next = mTail
        mTail.previous = mHead
    }

    /**
     * 将一个新节点添加为缓存中第一个元素节点.
     *
     * @param node 传入的节点.
     */
    private fun addNodeAsHead(node: Node) {
        node.next = mHead.next
        node.previous = mHead
        mHead.next.previous = node
        mHead.next = node
    }

    /**
     * 将一个已经添加的元素节点转移至缓存中的第一个位置.
     *
     * @param node 传入的元素节点.
     */
    private fun moveNodeToHead(node: Node) {
        removeNode(node)
        addNodeAsHead(node)
    }

    /**
     * 删除指定元素节点.
     *
     * @param node 待删除的元素节点.
     */
    private fun removeNode(node: Node) {
        node.previous.next = node.next
        node.next.previous = node.previous
    }

    /**
     * 删除缓存中最后一个元素节点.
     *
     * @return 删除成功的最后一个元素节点.
     */
    private fun removeTail(): Node {
        val targetNode = mTail.previous
        removeNode(targetNode)
        return targetNode
    }

    /**
     * 根据key查找value.
     *
     * @param key
     * @return
     */
    operator fun get(key: Int): Int {
        if (mData.containsKey(key)) {
            val targetNode = mData[key]
            moveNodeToHead(targetNode!!)
            return targetNode.value
        }
        return -1
    }

    /**
     * 插入key、value.
     */
    fun put(key: Int, value: Int) {
        if (mData.containsKey(key)) {
            val targetNode = mData[key]
            targetNode!!.value = value
            moveNodeToHead(targetNode)
        } else {
            val targetNode = Node(key, value)
            addNodeAsHead(targetNode)
            mData[key] = targetNode
            if (++size > maxSize) {
                val removedNode = removeTail()
                mData.remove(removedNode.key)
                size--
            }
        }
    }

    /**
     * 节点类.
     */
    internal class Node {
        /* 键 */
        var key = 0

        /* 值 */
        var value = 0

        /* 当前节点的上一个节点 */
        var previous: Node = Node()

        /* 当前节点的下一个节点 */
        var next: Node = Node()

        constructor()

        constructor(key: Int, value: Int) {
            this.key = key
            this.value = value
        }
    }
    
}
```

**第二题**

```
/**
 * @Author 王硕
 * @Create 2020-10-28
 * @Desc
 * 假设有面值 1、2、5 的三种硬币，数量无限。现在输入一个正整数 n，问所需硬币数量最少需要多少？
 * 只需要硬币总数，不必枚举每种硬币的个数。
 * 进一步的问题，如果硬币的个数与面额发生变化改如何解？比如 1、3、8
 */
class CoinChangingSolution {

    /**
     * @param coins 基本面值的硬币。如题中的：1、2、5
     * @param amount 待求解决的金额
     * @return 共需要多少个硬币
     */
    fun coinChange(coins: IntArray, amount: Int): Int {
        val max = amount + 1
        val dp = IntArray(amount + 1)
        Arrays.fill(dp, max)
        dp[0] = 0

        for (i in 1..amount) {
            for (j in coins.indices) {
                if (coins[j] <= i) {
                    dp[i] = Math.min(dp[i], dp[i - coins[j]] + 1)
                }
            }
        }

        return if (dp[amount] > amount) -1 else dp[amount]
    }

}
```

**第三题**

```
/**
 * @Author 王硕
 * @Create 2020-10-28
 * @Desc
 * 给出两个字符串，每个字符串包含字符范围限制在「0-9」，
 * 即每个字符串可被认为是一个正整数，且不限制字符串的长度。
 * 如何计算这两个字符串相乘的结果，结果需要同样以字符串的形式返回。
 */
object MaximumNumMultiplySolution {

    private const val ERROR_RESULT = "-1"

    private const val ZERO_RESULT = "0"

    /**
     * @param num1  乘数：正整数字符串
     * @param num2  被乘数：正整数字符串
     * @return [num1]*[num2]的结果，同样是正整数字符串
     */
    fun multiply(num1: String, num2: String): String {
        try {
            if (num1.toInt() < 0 || num2.toInt() < 0) {
                return ERROR_RESULT
            }
        } catch (e: NumberFormatException) {
            return ERROR_RESULT
        }

        if (num1 == ZERO_RESULT || num2 == ZERO_RESULT) {
            return ZERO_RESULT
        }

        val res = IntArray(num1.length + num2.length)
        for (x in num1.length - 1 downTo 0) {
            val n1 = num1[x] - '0'
            for (y in num2.length - 1 downTo 0) {
                val n2 = num2[y] - '0'
                val sum = res[x + y + 1] + n1 * n2
                res[x + y + 1] = sum % 10
                res[x + y] += sum / 10
            }
        }

        val result = StringBuilder()
        for (i in res.indices) {
            if (i == 0 && res[i] == 0) continue
            result.append(res[i])
        }
        return result.toString()
    }
}
```

**第四题**

```
private List<Task> sortByTime(List<Task> list) {
    Collections.sort(list, (lhs, rhs) -> {
        if(lhs.getPriority() == rhs.getPriority()){
            if(lhs.getDueDate() == null && rhs.getDueDate() == null){
                retun  -1 * lhs.getCreated().compareTo(rhs.getCreated());
            }else{
                if(lhs.getDueDate() != null && rhs.getDueDate() != null){
                    return lhs.getDueDate().compareTo(rhs.getDueDate());
                }else{
                    return lhs.getDueDate() != null ? -1 : 1;
                }
            }
        }else{
            return rhs.getPriority() - lhs.getPriority();
        }
    });
    return list;
}
```

