## Kiner算法刷题记（一）：链表和链表思想

### 基本概念和链表思想

- 链表的基本概念和结构
  
  > 链表从js中的表现形式来看，其实就是一个具有一个属性用于存储数据，有一个属性用于存储下一个节点地址引用的对象结构，简易链表结构如下：
  > 
  > function LinkNode(val, next=null) {
  > 
  > ​    this.val = val;
  > 
  > ​    this.next = next;
  > 
  > }
  > 
  > 我们经常听说的区块链，其实本质上就是一个链表结构。至于链表结构都有哪些应用场景，下文会有详细讲解

- 链表思想
  
  > 按照个人的理解，链表思想其实并不局限于链表这样的一个数据结构，而是拥有一种特性：每一个节点都有一个唯一指向的结构或逻辑我们都可以认为是具备链表思想的结构。如果觉得这样理解太过于抽象，待会我们一起刷一下LeetCode的202题，你应该就能够有一个更加清晰的了解了

### 链表和链表思想在我们常见的框架中的应用

#### Vue

- `keep-alive`适用于缓存路由，让变切换回来后可以快速恢复，但既然缓存就要存储到内存中，那么，`keep-alive`是通过怎样的方式防止大量数据存入内存导致“内存爆炸”的呢？答案就是采用了链表这样的数据结构。
  - 简单的描述：频繁缓存数据，如何在保证效率的同时，避免内存卡爆的情况。使用：`LRU-CACHE`
  - `LRU——CACHE`缓存淘汰算法的细节在后面会详细描述
- `Vue3`中，在编译单文件组件时，也会使用`LRU_CACHE`,具体代码在`packages/compiler-sfc/src/parse.ts`文件中，直接搜索`lru-cache`就可以看到。这是为了在编译阶段的时候，防止内存卡爆的情况，因为你也不知道你的一个大型项目会有多少个单文件组件。

#### React

- `Fiber`使用了多向链表的结构，包括`child`、`sibing`、`return`三个指针，分别指向第一个子节点，第一个兄弟节点、父节点
- `Hooks`使用了`队列`或`链表`实现，因此，`Hooks`不能在`if...else`或嵌套函数中使用

### 链表思想（结构）的应用场景

- 操作系统中磁盘空间的动态分配
  - 假如我们的硬盘有`100GB`，现在要分配出`20GB`的空间出来存放一些东西，那么，我们再硬盘中的剩余空间是如何管理的呢？答案其实就是链表结构。如：【20GB】【20GB】【60GB】,假如说我们申请的空间是第二个20GB的空间，这样我们的硬盘就会出现两个不连续的碎片，这样的话，就可以使用类链表的结构，把第一个20GB和后面那个60GB串起来进行管理。
- LRU缓存淘汰算法（如果需要一个体积固定的缓存时，就可以使用`LRU-CACHE`）
  - 当CPU要获取一个资源的时候，他会先去我们的内存中查找，如果找到就直接使用，没找到就从硬盘中读取后，再把它缓存起来，这个过程，其实也是用了哈希链表的结构（之所以用哈希链表是为了提升查找效率），当有新的资源被插入时，会在链表的尾结点插入数据了，然后删除链表的头结点淘汰最旧的数据
- React中的Fiber
  - react使用类似于3向链表的结构来维护我们的fiber节点
- 区块链
  - 区块链本质上就是使用链表的结构把一个个数据区块串联起来，只是使用了挖矿的方式提升其复杂度和安全性而已，本质上还是一个链表结构
- ...

### 渐进刷题

#### 141. 环形链表

> 题目描述：
> 
> 给定一个链表，判断链表中是否有环。
> 
> 如果链表中有某个节点，可以通过连续跟踪 next 指针再次到达，则链表中存在环。 为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。注意：pos 不作为参数进行传递，仅仅是为了标识链表的实际情况。
> 
> 如果链表中存在环，则返回 true 。 否则，返回 false 。

##### 解题思路

判断一个链表有没有环其实有很多种方法，比如说借助一个哈希表使用空间换时间的方法，也可以使用快慢指针的方法。在这里，我们就讲一下更高效的快慢指针的方法吧。

首先，我们需要明确一个点，假如说，我们的链表有环，那么我们在遍历的时候，就有可能会一直在环里面转圈，那么。我们就可以利用这样的特性，定义一个慢指针p（每次只走一步），再定义一个快指针q（每次走两步）,假如这个链表有环的话，我们的快指针和快指针的下一个节点是不可能出现`null`的，因为有环的定义就是最后一个节点会指向开始节点。并且，我们的快指针再经过若干轮的追击后，最终一定会追上我们的慢指针，并与慢指针相遇。

这样，我们就可以把这个问题想象成我们熟悉的追击问题了，翻译一下：如果我们两个一起比赛跑步，我每小时跑1km,你每小时跑2km,假如说我们是在一个笔直的公路上比赛的话，那我跟你永远都不会相遇，因为你比我跑得快，我们之间的距离只会越拉越大。但是，如果我们在一个环形跑道中比赛的话，因为你跑的速度是我的两倍，所以，再跑了若干圈后，你最终肯定会出现比我比我多跑一整圈并且与我相遇的情况。

Ok，大概的思路就是这样，接下来开始撸代码：

##### 代码实现

```javascript
/*
 * @lc app=leetcode.cn id=141 lang=typescript
 *
 * [141] 环形链表
 */

// @lc code=start
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     val: number
 *     next: ListNode | null
 *     constructor(val?: number, next?: ListNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.next = (next===undefined ? null : next)
 *     }
 * }
 */

function hasCycle(head: ListNode | null): boolean {
    // 如果头结点不存在，那就不用判断了，肯定没环
    // 如果只有一个节点，那也不可能形成环
    if(!head || !head.next) return false;
    // 定义快慢指针，快指针比慢指针多走一步
    let slow = head, fast = head.next;
    // 在快慢指针没有相遇，并且快指针和快指针的下一个节点存在的情况就继续跑圈
    while(slow !== fast && fast && fast.next) {
        // 每次慢指针走一步
        slow = slow.next;
        // 快指针走两步
        fast = fast.next.next;
    }
    // 跑圈完毕后，如果快马指针相遇，则说明有环
    return slow === fast;
};
// @lc code=end
```

#### 142. 环形链表II

> 给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。
> 
> 为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。注意，pos 仅仅是用于标识环的情况，并不会作为参数传递到函数中。
> 
> 说明：不允许修改给定的链表。

##### 解题思路

上面我们已经了解了怎么判断一个链表是否有环,这一题相对以上一题的话，其实就是多了一步，让我们找出环的起始点在哪里。

那么，如果这个链表有环的话。我们假设链表的头结点`head`距离链表环的起始点`m`的距离是`a`，我们还是定义一快(`q`)一慢(`p`)两个指针,假设我们的慢指针`p`走到了环的起始点，那么慢指针距离头结点的距离就是`a`,而快指针`q`因为他的速度是慢指针的两倍，所以他走到了`2a`的位置。那么，接下来，我们来想一下，快指针`q`总共要走多远才能够比慢指针多跑一圈呢？我们暂且设这个距离为`x`,因为快指针每次都不慢指针夺走一步，那么要追上慢指针，那肯定就是多走了`a + x`步了。那么，我们就可以得到这样的一个公式:环的长度`n = a + x`,那么快指针走到环起始点要走的距离其实就是`a + x - x`,也就是`a`。好了，到了这一步，我们已经知道了，要走a步才能找到起始点，我们可以发现，相遇点到起始点的距离=头结点到起始点的距离，所以，我们可以让慢指针归位到头结点，然后快慢指针一起一步一步往前走，知道快慢指针再次相遇，这样，我们就找到了环的起始点了。

看上面的文案描述可能有点抽象，来看看具体实现代码吧：

##### 代码实现

```javascript
/*
 * @lc app=leetcode.cn id=142 lang=typescript
 *
 * [142] 环形链表 II
 */

// @lc code=start
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     val: number
 *     next: ListNode | null
 *     constructor(val?: number, next?: ListNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.next = (next===undefined ? null : next)
 *     }
 * }
 */

function detectCycle(head: ListNode | null): ListNode | null {
  if(!head) return head;
  // 如果链表只有一个节点，则不可能有环
  if(!head.next) return null;

  let p = head, q = head;

  // 首先，数组判环
  do {
    p = p.next;// 慢指针每次走一步，假设长度为：a
    q = q.next.next;// 快指针每次走两步，假设长度为2a
  } while(p !== q && q && q.next)// 如果快慢指针相遇或者快指针和快指针的下一个节点为空时跳出循环

  // 如果跳出循环后，快指针或快指针的下一个节点是null,说明链表没环，直接返回null，以为有环的链表，是不可能出现null的
  if(q === null || q.next === null) return null;

  // 让慢指针回到起点的位置
  p = head;

  // 然后让慢指针从起点位置往后走，快指针从相遇点位置往后走，知道他们的相遇点，就是环的起点
  // 由上可知，快指针位于2a的位置，快指针距离环的起始点的位置为a+x
  // 假设相遇点距离环的起始点的距离为x,由于快指针每次都比慢指针多走一步，那么什么时候快慢指针才会相遇呢，答案就是走了x步的时候相遇
  // 即，快指针每次比慢指针夺走一步，当走了x步时，快慢指针相遇了。
  // 所以，从相遇点开始，快指针还需要走：a+x-x=a步才能走到起始点
  // 因此，我们使用快慢指针分别从相遇点和头结点一步步往下走，知道相遇点便是我们的环的起始点
  while(p !== q) {
      p = p.next;
      q = q.next;
  }

  return q;

}
// @lc code=end
```

#### 202. 快乐数（链表思想的体现）

> 编写一个算法来判断一个数 n 是不是快乐数。
> 
> 「快乐数」定义为：
> 
> 对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和。
> 然后重复这个过程直到这个数变为 1，也可能是 无限循环 但始终变不到 1。
> 如果 可以变为  1，那么这个数就是快乐数。
> 如果 n 是快乐数就返回 true ；不是，则返回 false 。

##### 解题思路

这道第看似跟链表一毛钱关系都没有，但是，实际上，这道题起始是我们链表思想的一个体现。

我们前文已经说了，链表就是具有唯一指向的数据结构，纳闷，我们看看题干，起始可以看出，快乐数就是一个符合链表唯一指向的命题：

```bash
# 输入的数字为19
1**2+9**2 = 82 -> 8**2+2**2 = 68 -> 6**2+8**2 = 100 -> 1**2+0**2+0**2 -> 1
```

从上面的结构可以看出`82->68->100->1`都是具有唯一指向性的，所以我们可以使用链表的思路来解决这个问题。

那么，我们要从何下手呢？其实这个就是一个变相的链表判环问题，如果一个数不是快乐数，那么他就会形成一个带环链表，如果是快乐数的话，那么他最后一个节点唯一指向1，这样思考问题，会不会就简单了呢？

那么，让我们来看一下具体代码如何实现吧：

##### 代码实现

```javascript
/*
 * @lc app=leetcode.cn id=202 lang=typescript
 *
 * [202] 快乐数
 */

// @lc code=start
function isHappy(n: number): boolean {
    let p = n, q = n;

    do {
        // 慢指针进行一次运算
        p = getNext(p);
        // 快指针进行两次运算
        q = getNext(getNext(q));
    } while(p !== q && q !==1) // 如果快慢指针没有相遇并且快指针不为1时进入循环

    // 循环结束后，如果快指针为1，就说明是快乐数
    return q === 1;
};

function getNext(x: number): number {
    let res = 0;

    while(x) {
        // 不多获取数字的每一位求平方和
        res += (x%10)**2;
        // 求了平方和后，对原始数字除十取整再进入下一次的运算逻辑
        x = ~~(x / 10);
    }

    return res;
}


// @lc code=end
```

#### 206. 反转链表

> 反转一个单链表。
> 
> 示例:
> 
> 输入: 1->2->3->4->5->NULL
> 输出: 5->4->3->2->1->NULL
> 进阶:
> 你可以迭代或递归地反转链表。你能否用两种方法解决这道题？

##### 解题思路

我们要反转一个链表，其实就是把每个节点的下一个指针指向上一个节点

##### 代码实现

```javascript
/*
 * @lc app=leetcode.cn id=206 lang=typescript
 *
 * [206] 反转链表
 */

// @lc code=start
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     val: number
 *     next: ListNode | null
 *     constructor(val?: number, next?: ListNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.next = (next===undefined ? null : next)
 *     }
 * }
 */

function reverseList(head: ListNode | null): ListNode | null {
    // 解法1： 利用js的解构赋值特性实现
    // let pre = null, p = head;

    // while(p) {
    //     [p.next, pre, p] = [pre, p, p.next];
    // };

    // return pre;

    // 递归法
    if(!head || !head.next) return head;
    let tail = head.next, p = reverseList(head.next);
    head.next = tail.next;
    tail.next = head;

    return p;

};
// @lc code=end
```

#### 92. 反转链表II

> 反转从位置 m 到 n 的链表。请使用一趟扫描完成反转。
> 
> 说明:
> 1 ≤ m ≤ n ≤ 链表长度。
> 
> 示例:
> 
> 输入: 1->2->3->4->5->NULL, m = 2, n = 4
> 输出: 1->4->3->2->5->NULL

##### 解题思路

这个反转链表其实就是在上一道题的基础上，加了一个限定条件，从哪个节点反转到哪个节点。我们可以先让一个指针移动到待反转节点处，然后反转n各节点就可以实现了。

##### 代码实现

```javascript
/*
 * @lc app=leetcode.cn id=92 lang=typescript
 *
 * [92] 反转链表 II
 */

// @lc code=start
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     val: number
 *     next: ListNode | null
 *     constructor(val?: number, next?: ListNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.next = (next===undefined ? null : next)
 *     }
 * }
 */

// class ListNode {
//     val: number
//     next: ListNode | null
//     constructor(val?: number, next?: ListNode | null) {
//         this.val = (val===undefined ? 0 : val)
//         this.next = (next===undefined ? null : next)
//     }
// }

 function reverse(head: ListNode, n: number){
    // 当计数器减到1时，反转完毕，返回链表头结点
    if(n === 1) return head;
    // 定义一个尾指针指向头结点的下一个节点，利用递归的方式进行链表反转，没反转一次计数器建议
    let tail = head.next, p = reverse(head.next, n - 1);
    // 反转完成后，把头结点的下一个节点指向尾结点的下一个节点，
    head.next = tail.next;
    // 尾结点的下一个节点指向头结点
    tail.next = head;
    return p;
 }

function reverseBetween(head: ListNode | null, left: number, right: number): ListNode | null {
    if(!head || !head.next) return head;
    // 设置虚拟头结点
    let pre = new ListNode(-1, head);
    let p = pre, count = right - left +1;
    // 先让指针移动到待翻转链表的上一个节点
    // 如果left为2，那么，我们就要把指针移到1的位置，之后把反转后的链表挂在1的next上即可
    // 这也是为什么使用--left而不用left--的原因
    while(--left){
        p = p.next;
    }
    // 反转目标链表
    p.next = reverse(p.next, count);
    return pre.next;
};
// @lc code=end
```

#### 25. K个一组反转链表

> 给你一个链表，每 k 个节点一组进行翻转，请你返回翻转后的链表。
> 
> k 是一个正整数，它的值小于或等于链表的长度。
> 
> 如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。
> 
> 进阶：
> 
> 你可以设计一个只使用常数额外空间的算法来解决此问题吗？
> 你不能只是单纯的改变节点内部的值，而是需要实际进行节点交换。

##### 解题思路

这一道题有笔上一道题多了一点限制，要按照给定的节点个数为一组进行反转，其实原理还是跟上面两题差不多，只是多了一些边界条件的处理

##### 代码实现

```javascript
/*
 * @lc app=leetcode.cn id=25 lang=typescript
 *
 * [25] K 个一组翻转链表
 */

// @lc code=start
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     val: number
 *     next: ListNode | null
 *     constructor(val?: number, next?: ListNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.next = (next===undefined ? null : next)
 *     }
 * }
 */

// 反转n个节点 
function __reverseN(head: ListNode, n: number) {
    if(n === 1) return head;
    let tail = head.next, p = __reverseN(head.next, n -1);
    head.next = tail.next;
    tail.next = head;
    return p;
}

function reverseN(head: ListNode, n: number) {
    let count = n, p = head;
    while(--n && p) p = p.next;
    // 判断是否足够n个节点，如果不足够的话，不反转
    if(!p) return head;
    // 如果足够的话，反转count个节点
    return __reverseN(head, count);
}

function reverseKGroup(head: ListNode | null, k: number): ListNode | null {
    // hair为哨兵节点，依然使用双指针（快慢指针）的方式
    let hair = new ListNode(-1, head), p = hair, q = p.next;
    // 如果反转后的节点依然跟之前的q节点一样，说明没有执行实际的反转操作，说明不足n个节点了，跳出循环
    while((p.next = reverseN(q, k))!==q) {
        // hair -> 1 -> 2 -> 3 -> 4 -> 5 -> null
        // hair -> 3 -> 2 -> 1 -> 4 -> 5 -> null
        // 从上面的示例可以看出反转k个节点的链表中，1的下一个节点其实就是我们下一次要反转的单元
        // 所以，我们只需要让p指向q，即指针移动到4的位置，然后让q移动到p的下一个节点，即5的位置，
        // 然后进入下一次的判断
        p = q;
        q = q.next;
    }
    return hair.next;
};
// @lc code=end
```

#### 24. 两两交换链表

> 给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。
> 
> **你不能只是单纯的改变节点内部的值**，而是需要实际的进行节点交换。

##### 解题思路

这道题其实就是k个一组反转链表的特例,即当k===2的时候，我们可以直接搬过来用就可以了

##### 代码实现

```javascript
/*
 * @lc app=leetcode.cn id=24 lang=typescript
 *
 * [24] 两两交换链表中的节点
 */

// @lc code=start
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     val: number
 *     next: ListNode | null
 *     constructor(val?: number, next?: ListNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.next = (next===undefined ? null : next)
 *     }
 * }
 */

 function __reverseN(head: ListNode, n: number) {
     if(n === 1) return head;
     let tail = head.next, p = __reverseN(head.next, n - 1);
     head.next = tail.next;
     tail.next = head;
     return p;
 }

 function reverseN(head: ListNode, n: number=2) {
     let p = head, count = n;
     while(--n && p) {
        p = p.next;
     }
     if(!p) return head;
     return __reverseN(head, count);
 }

function swapPairs(head: ListNode | null): ListNode | null {
    let hair = new ListNode(-1, head), p = hair, q = p.next;
    while((p.next = reverseN(q)) !== q){
        p = q;
        q = q.next;
    }
    return hair.next;
};
// @lc code=end
```

#### 61. 旋转链表

> 给定一个链表，旋转链表，将链表每个节点向右移动 k 个位置，其中 k 是非负数。
> 
> 示例 1:
> 
> 输入: 1->2->3->4->5->NULL, k = 2
> 输出: 4->5->1->2->3->NULL
> 解释:
> 向右旋转 1 步: 5->1->2->3->4->NULL
> 向右旋转 2 步: 4->5->1->2->3->NULL
> 示例 2:
> 
> 输入: 0->1->2->NULL, k = 4
> 输出: 2->0->1->NULL
> 解释:
> 向右旋转 1 步: 2->0->1->NULL
> 向右旋转 2 步: 1->2->0->NULL
> 向右旋转 3 步: 0->1->2->NULL
> 向右旋转 4 步: 2->0->1->NULL

##### 解题思路

要将链表旋转k个节点，其实我们只要先把链表头尾相连串成一个环，然后再循环右移链表节点总数-k次，然后把环断开，那么断开位置的下一个节点其实就是我们要的新链表的头结点

##### 代码实现

```javascript
/*
 * @lc app=leetcode.cn id=61 lang=typescript
 *
 * [61] 旋转链表
 */

// @lc code=start
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     val: number
 *     next: ListNode | null
 *     constructor(val?: number, next?: ListNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.next = (next===undefined ? null : next)
 *     }
 * }
 */
function rotateRight(head: ListNode | null, k: number): ListNode | null {

    if(!head) return head;

    let n = 1, p = head;

    // 将指针移动到最后一个节点，并记录总节点数量
    while(p.next){
        p = p.next;
        n++;
    }
    // 将最后一个几点与头结点相连串成有环链表
    p.next = head;
    // 因为循环链表，无论转几圈都是等效的，因此让k与n求模得到一个较小的k值，避免无意义的移动
    k %= n;
    // 循环右移时需要用总的长度减去k得出真正需要右移的数量
    k = n - k;

    // 让链表右移k位
    while(k--) {
        p = p.next;
    }
    // 将头结点执行p的下一个节点
    head = p.next;
    // 断环
    p.next = null;
    return head;

};
// @lc code=end
```

#### 19. 删除链表倒数第n个节点

> 给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。

##### 解题思路

要删除导数第n各节点，那么我们就需要有个指针指向导数第n个节点的前一个节点，然后直接让这个节点的下一个节点指向导数第n个节点的下一个节点即可。

##### 代码实现

```javascript
/*
 * @lc app=leetcode.cn id=19 lang=typescript
 *
 * [19] 删除链表的倒数第 N 个结点
 */

// @lc code=start
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     val: number
 *     next: ListNode | null
 *     constructor(val?: number, next?: ListNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.next = (next===undefined ? null : next)
 *     }
 * }
 */

function removeNthFromEnd(head: ListNode | null, n: number): ListNode | null {
    // 先让慢指针指向虚拟头结点，快指针执行真实头结点
    let hair = new ListNode(-1, head),p = hair, q = head;
    // 让快指针往后走n步
    while(n-- && q){
        q = q.next;
    }
    // 然后让p和q一起向右移动，知道q(快指针)到了某位的时候停止
    while(q) {
        p = p.next;
        q = q.next;
    }
    // 此时，p指向的节点就是待删除节点的前一个节点，这时候，只需要让p的下一个节点指向p的下一个节点的下一个节点，便可实现删除操作
    p.next = p.next.next;
    return hair.next;
};
// @lc code=end
```

#### 83. 删除排序链表的重复元素

> 给定一个排序链表，删除所有重复的元素，使得每个元素只出现一次。
> 
> 示例 1:
> 
> 输入: 1->1->2
> 输出: 1->2
> 示例 2:
> 
> 输入: 1->1->2->3->3
> 输出: 1->2->3

##### 解题思路

这道题其实很简单，我们只需要判断当前节点的值和下一个节点的值是否相同，若相同，则删除下一个节点，否则，当前节点的指针右移继续遍历

##### 代码实现

```javascript
/*
 * @lc app=leetcode.cn id=83 lang=typescript
 *
 * [83] 删除排序链表中的重复元素
 */

// @lc code=start
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     val: number
 *     next: ListNode | null
 *     constructor(val?: number, next?: ListNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.next = (next===undefined ? null : next)
 *     }
 * }
 */

function deleteDuplicates(head: ListNode | null): ListNode | null {
    // if(!head) return head;
    // let p = head;
    // while(p){
    //     // 一直删除相同的节点，直至当前节点的值与下一个节点不同为止
    //     while(p && p.next && p.val===p.next.val) p.next = p.next.next;
    //     // 遇到了不同的值了，p节点后移
    //     p = p.next;
    // }
    // return head;
    if(!head) return head;
    let p = head;
    while(p.next){
        if(p.val === p.next.val) {
            p.next = p.next.next;
        } else {
            p = p.next;
        }
    }
    return head;
};
// @lc code=end
```

#### 82. 删除排序链表的重复元素II

> 给定一个排序链表，删除所有含有重复数字的节点，只保留原始链表中 没有重复出现 的数字。
> 
> 示例 1:
> 
> 输入: 1->2->3->3->4->4->5
> 输出: 1->2->5
> 示例 2:
> 
> 输入: 1->1->1->2->3
> 输出: 2->3

##### 解题思路

相较于上一题，这题我们就要借助于哨兵节点了，因为这题有可能会操作到头结点，其他的思路起始跟上面都是一样的

##### 代码实现

```javascript
/*
 * @lc app=leetcode.cn id=82 lang=typescript
 *
 * [82] 删除排序链表中的重复元素 II
 */

// @lc code=start
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     val: number
 *     next: ListNode | null
 *     constructor(val?: number, next?: ListNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.next = (next===undefined ? null : next)
 *     }
 * }
 */

function deleteDuplicates(head: ListNode | null): ListNode | null {
    let hair = new ListNode(-1, head), p = hair, q;

    while(p.next) {
        // 如果真实头结点存在并且它的值等于真实头结点的话
        if(p.next.next && p.next.val === p.next.next.val) {
            // 借助另外一个真正找到跟真实头结点值不一样的第一个节点
            q = p.next.next;
            while(q && q.val===p.next.val) q = q.next;
            // 将p节点的下一个节点指向找到的q节点，实现删除
            p.next = q;
        } else {
            // 节点后移
            p = p.next;
        }
    }
    return hair.next;
};
// @lc code=end
```

#### [287. 寻找重复数](https://leetcode-cn.com/problems/find-the-duplicate-number/)

##### 解题思路

这道题看似也跟链表没有一毛钱关系，但实际上也可以跟202题类似，使用链表思想解决。由于题目要求不能修改原数组，而且空间复杂度为`O(1)`，并且题目说明了至少有一个整数是重复的。那么加入我们尝试将数组的每一个元素当做是一个链表节点，当链表节点重复说明了什么？是不是说明这个链表出现了环，而我们要找的重复元素实际上就是链表环的起始点。

有了这个思路之后，问题就简单多了，我们定义快慢指针`q`和`p`，只要`p`和`q`不相遇，则说明没有进入环中，我们就继续走向下一层，一旦快慢指针相遇，则说明已经进入了环中，我们退出循环后，将慢指针重新归位到起始点，然后让快慢指针一起向后走，当快慢指针相遇时，就找到了环的起始点了。

##### 代码演示

```typescript
function findDuplicate(nums: number[]): number {
  // 将数组看成是一个链表，只要没有重复数字，那么这个链表就不会出现环，如果出现环了，只要找到环的起始点就是重复元素
  let p = 0,
    q = 0;
  do {
    p = nums[p];
    q = nums[nums[q]];
  } while (p !== q);
  p = 0;
  while (p !== q) {
    p = nums[p];
    q = nums[q];
  }
  return p;
}
```
