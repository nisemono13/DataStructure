# 8.05
> 问题1：
> 设计一个递归算法，删除不带头结点的单链表L中所有值为x的结点

**基本设计思想：**

​		设f(L,x)的功能是删除以L为首节点指针的单链表中所有值等于x的结点，显然有f(L->next, x)的功能是删除L->next为首节点指针的单链表中所有值等于x的结点，由此可以推出递归模型如下。

​		终止条件：f(L, x) ≡ 不做任何事情;							若L为空表

​		递归主体：f(L, x) ≡ 删除*L结点; f(L->next, x);		若L->data == x

​						   f(L, x) = f(L->next, x);							  其它情况

```c
void DelNum(LinkList *L, ElemType x)
{
    LNode *p;   // point the x node
    if ((*L) == NULL) return;     // Recursive export
    else if ((*L)->data == x)       
    {
        p = (*L);                      // Del (*L) and (*L) point the next node
        (*L) = p->next;
        free(p);
        DelNum(L, x);
    }
    else 
        DelNum(&((*L)->next), x);
}
```



# 8.06

> 问题2：
>
> 在带头结点的单链表L中，删除所有值为x的结点，并释放其空间，假设值为x的结点不唯一。

**基本设计思想：**

​		用p指针指向当前结点，遍历整个链表，pre指向*p结点的前驱。若p所指结点的值为x，则删除，并让p移向下一个结点，否则让pre、p指针同步后移一个结点。

```c
void DelNum_2(LinkList *L, ElemType x)
{
    LNode *p = (*L)->next;                    // p point the first node
    LNode *pre = (*L);                        // pre point p previous node
    
    // scan the linklist
    LNode *q;
    while (p)
    {
        if (p->data == x)
        {
            q = p;                          // point the present node
            p = p->next;                    // point the next node
            pre->next = p;                  // delete the node
            free(q);                        // free the node
        }
        else
        {
            // move the next node
            pre = p;                        
            p = p->next;
        }
    }

}
```



>问题3：
>
>设L为带头结点的单链表，编写算法实现从尾到头方向输出每个结点的值。

**基本设计思想：**

​		设f(L)的功能是寻找以L为首指针的单链表中指针域为NULL的结点，显然有f(L->next)是寻找以L->next为首指针的单链表中指针域为NULL的结点，由此可推出一下递归模型，

​		递归主体：f(L) ≡ f(L->next)；									  若L->next ！= NULL

​		终止条件：f(L) ≡ 打印当前结点的数据；					若L != NULL

```c
void ReversePrint(LinkList L)
{
    if (L->next != NULL)
        ReversePrint(L->next);
    if (L != NULL)
        printf("%4d", L->data);
}
```

**[注]** 因为头结点也有数据域且指针域指向的是第一个结点的地址，如果把头结点也传入该函数的话，头结点里的数据也会被打印，所以传参的时候传入第一个结点的地址，即ReversePrint(L->next);



# 8.07

> 问题4：
>
> 试编写在带头结点的单链表L中删除一个最小值结点的高效算法（假设最小值结点时唯一的）

**基本设计思想：**

​		创建一个指针p用来指向最小结点，然后遍历整个链表L，比较找出最小结点的地址存入p。然后将该节点的数据域和指针域交换(删除)，将最小结点地址作为返回值返回。

```c
LNode DelMinNode(LinkList *L)
{
    LNode *p = (*L)->next;          // point the first node
    LNode *q = (*L)->next;          // traverse the list
    LNode min;
    
    while (q)                       // find the min node
    {
        if (q->data < p->data)      // record the min node
            p = q;
        q = q->next;
    }

    // sava the min node
    min.next = p->next;
    min.data = p->data;
    // delete the min node
    p->data = p->next->data;
    p->next = p->next->next;      
    // free the next node of min node, because the min node has became it
    free(min.next);
    return min;

}
```

**书解：**

```c
void DelMinNodeAnwser(LinkList *L)
{
    // 书解
    LNode *pre = (*L), *p = pre->next;
    LNode *minpre = pre, *minp = p;
    while (p != NULL)
    {
        if (p->data < minp->data)
        {
            minp = p;
            minpre = pre;
        }
        pre = p;
        p = p->next;
    }
    
    minpre->next = minp->next;
    free(minp);
}
```



> 问题5：
>
> 试编写算法将带头结点的单链表就地逆置

**基本设计思想：**

​		解法一：利用头插法逆置

​		解法二：明天再说

```c
// 问题5：原地逆置单链表
void ReverseLinkList_1(LinkList *L)
{
    // 解法一：利用头插法逆置
    LNode *p = (*L)->next;                  // p是工作指针
    LNode *q;                               // q是p的后继指针
    (*L)->next = NULL;
    while (p)                               // 头插法
    {
        q = p->next;
        p->next = (*L)->next;
        (*L)->next = p;
        p = q;
    }
}

// 问题5：解法2
void ReverseLinkList_2(LinkList *L)
{
    LNode *pre, *p = (*L)->next, *q = p->next;
    p->next = NULL;
    while (q)
    {
        pre = p;
        p = q;
        q = q->next;
        p->next = pre;
    }
    (*L)->next = p;
}
```

# 8.10

> 问题6：
>
> 有一个带头结点的链表L，设计一个算法使其元素递增有序

**基本设计思想：**

​		设置指针p为工作指针，遍历链表

```c
bool SortList(LinkList *L)
{
    LNode *p = (*L)->next, *q = p->next;
    ElemType temp;

    if (*L == NULL)
        return false;

    while (p)
    {
        while (q)
        {
            if (p->data > q->data)
            {
                temp = p->data;
                p->data = q->data;
                q->data = temp;
            }
            q = q->next;
        }
        p = p->next;
        if (p != NULL)
            q = p->next;
    }
    return true;
}
```




> 问题7：
>
> 删除表中所有介于给定的两个值之间的元素

**基本设计思想：**

​		逐个结点进行检查，执行删除

```c
bool Del_S_T(LinkList *L, ElemType S, ElemType T)
{
    LNode *p = (*L)->next, *pre = (*L);
    while (p)
    {
        if (p->data >= S && p->data <= T)
        {
            pre->next = p->next;
            free(p);
            p = pre->next;
        }
        else
        {
            pre = p;
            p = p->next;
        }
        
    }
}
```

# 8.11

> 问题8：
>
> 给定两个单链表，编写算法找出两个链表的公共结点

**基本设计思想：**

​		原理：两个链表公共的结点的所有后继结点都是一样的。

```c
LNode *Search_Common(LinkList L, LinkList M)
{
    LNode *p = L->next, *q = M->next;
    int L_len, M_len;

    for (L_len = 0; p != NULL; p = p->next, L_len++);
    for (M_len = 0; q != NULL; q = q->next, M_len++);

    p = L->next;
    q = M->next;
    if (M_len > L_len)
        for (int i = 0; i < abs(L_len - M_len); i++) q = q->next;
    else
        for (int i = 0; i < abs(L_len - M_len); i++) p = p->next;

    while (q)
    {
        if (q == p)
            return p;
        p = p->next;
        q = q->next;
    }
    return NULL;
}
```



> 问题9：
>
> 存在一个无序链表，按递增序列删除列表

**基本设计思想：**

​		

```c
bool Del_List_By_Min(LinkList *L)
{
    // 书解
    LNode *pre, *p;
    while ((*L)->next != NULL)
    {
        pre = (*L);
        p = pre->next;
        while (p->next != NULL)
        {
            if (p->next->data < pre->next->data)
                pre = p;
            p = p->next;
        }
        printf("The min is %4d\n", pre->next->data);
        LNode *u = pre->next;
        pre->next = u->next;
        free(u);
    }
    free(*L);
}
```



> 问题10：
>
> 将一个带头结点的单链表A分解为两个带头结点的单链表A和B，使得A表中含有原表中序号为奇数的元素，而B表中含有原表中序号为偶数的元素，且保持其相对顺序不变。

**基本设计思想：**

​		

```c
bool Disolve_List(LinkList L, LinkList *M, LinkList *N)
{
    LNode *r = (*M), *s = (*N);

    LNode *q = L->next;
    while (q)
    {
        LNode *p = (LNode *) malloc (sizeof(LNode));
        if (q->data % 2 == 0)
        {
            p->data = q->data;
            r->next = p;
            r = p;
        }
        else
        {
            p->data = q->data;
            s->next = p;
            s = p;
        }
        q = q->next;
    }
    r->next = NULL;
    s->next = NULL;
}
```

# 8.12

> 问题11：
>
> 设C = {a1, b1, a2, b2, ... , an, bn}为线性表，采用头结点的hc单链表存放，设计一个就地算法，将其拆分成为两个线性表，使得A = {a1, a2, ... , an}, B = {bn, ... , b2, b1}

**基本设计思想：**

​		

```c
bool Disolve_List_2(LinkList *A, LinkList *B)
{
    // 与上题一样，同样是按序号奇偶分开，B链表采用头插法，A链表采用尾插法
    // 原地操作
    (*B) = (LinkList)malloc(sizeof(LNode));
    (*B)->next = NULL;
    LNode *p = (*A)->next, *q; // p为工作指针
    LNode *ra = *A;            // ra始终指向A的尾结点

    while (p != NULL)
    {
        ra->next = p;
        ra = p; // 将*p链到A的表尾
        p = p->next;
        if (p != NULL)
            q = p->next; // 头插后，*p将断链，因此用q记忆*p的后继
        // 头插法
        p->next = (*B)->next; // 将*p插入到B的前端
        (*B)->next = p;
        p = q;
    }
    ra->next = NULL; // A尾结点的next域置空
}
```



> 问题12：
>
> 删除链表中重复的元素

**基本设计思想：**

```c
bool Delete_Common_Num(LinkList *L)
{
    LNode *p = (*L)->next, *q = p->next;
    LNode *s;
    // 一定要用指向后面的指针判空作为循环终止的条件
    while (q)
    {
        if (p->data == q->data)
        {
            s = q;
            q = q->next;
            free(s);
        }
        else
        {
            p->next = q;
            p = p->next;
            q = q->next;
        }
    }
}
```



# 8.15

> 问题13：
>
> 原地将两个递增有序链表合并成一个递减有序链表

**基本设计思想：**

​		用指针q指向第一个链表，指针p指向第二个链表。判断谁的第一个元素大，然后遍历此链表找到比另一个链表更大的那个元素，连接到该链表，依此类推，直到一个链表遍历完成，则将另一个来链表接在后面。

​		

```c
// 问题13：书解
void MergeSqquentialList(LinkList *La, LinkList *Lb)
{
    LNode *r, *pa = (*La)->next, *pb = (*Lb)->next;
    (*La)->next = NULL;
    while (pa && pb)
    {
        // 头插法逐个插入新链表
        if (pa->data <= pb->data)
        {
            r = pa->next;
            pa->next = (*La)->next;
            (*La)->next = pa;
            pa = r;
        }
        else
        {
            r = pb->next;
            pb->next = (*La)->next;
            (*La)->next = pb;
            pb = r;
        }
    }
    if (pa)
        pb = pa;
    
    while (pb)
    {
        r = pb->next;
        pb->next = (*La)->next;
        (*La)->next = pb;
        pb = r;
    }
    free((*Lb));
}
```

> 问题14：
>
> 将两个递增有序链表的公共元素构成一个链表

**基本设计思想：**

​		表A、B都有序，可从第一个元素一次比较A、B两表的元素，若元素值不等，则值小的指针往后移，若元素值相等，则创建一个值等于两结点的元素值的新节点，使用尾插法插入到新的链表中，并将两个原表指针后移一位，直到其中一个链表遍历到表尾

​		【书解】表A、B都有序，可从第一个元素起依次比较A、B两表的元素，若元素值不等，则值小的指针往后移，若元素值相等，则创建一个值等于两结点的元素值的新结点，使用尾插法插入到行的链表中，并将两个原表指针后移一位，直到一个链表遍历到表尾。

```c
void Get_Common(LinkList A, LinkList B)
{
    LNode *p = A->next, *q = B->next, *r, *s;
    LinkList C = (LinkList) malloc (sizeof(LNode));
    r = C;
    while (p != NULL && q != NULL)
    {
        if (p->data < q->data)
            p = p->next;
        else if (p->data > q->next)
            q = q->next;
        else
        {
            s = (LNode *) malloc (sizeof(LNode));
            s->data = p->data;
            r->next = s;
            r = s;
            p = p->next;
            q = q->next;
        }
    }
    r->next = NULL;
}
```



# 8.17

> 问题15：:star::star::star::star::star:
>
> 链表A、B表示不同的集合，元素递增，求交集。

**基本设计思想：**

​		【书解】采用归并的思想，设置两个工作指针pa，pb，对两个链表进行归并扫描，只有同时出现在两个集合中的元素才链到结果表中且仅保留一个，其它的结点全部释放。当一个链表遍历完毕后，释放另一个表中剩下的全部节点。

```c
LinkList *Union(LinkList *la, LinkList *lb)
{
    // 如上题，依次遍历链表，比较节点的大小值，若相等则链入新链表，否则，按规则后移。
    LNode *pa = (*la)->next, *pb = (*lb)->next;
    LinkList pc = (*la);

    LNode *temp;
    while (pa && pb) // 一个链表遍历完成便结束
    {
        if (pa->data > pb->data)
        {
            temp = pa;
            pa = pa->next;
            free(temp);
        }
        else if (pa->data < pb->data)
        {
            temp = pb;
            pb = pb->next;
            free(temp);
        }
        else
        {
            pc->next = pa;
            pc = pa;
            pa = pa->next;
            temp = pb;
            pb = pb->next;
            free(temp);
        }
    }

    while (pa)
    {
        temp = pa;
        pa = pa->next;
        free(temp);
    }

    while (pb)
    {
        temp = pb;
        pb = pb->next;
        free(temp);
    }
    pc->next = NULL;
    free(lb);
    return la;
}
```



# 8.18

> 问题16：
>
> 两个整数序列存入链表A、B，判断B是A的连续子序列

**基本设计思想：**

​		

```c
bool Pattern(LinkList A, LinkList B)
{
    LNode *p = A->next, *head = B->next, *q = head;
    while (p && q)
    {
        if (q->data == p->data)
        {
            p = p->next;
            q = q->next;
        }
        else
        {
            q = head;
            p = p->next;
        }
    }

    if (q == NULL)
        return true;
    else
        return false;
}
```

