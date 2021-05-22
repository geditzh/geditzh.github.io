---
title: 线性表
date: 2021/5/22 20:46:25
comments: true
---

# 一.线性表
## 线性表的抽象类型定义如下：
```c
ADT 线性表（List）
Data
	{a1,a2,a3,a4,a5}，除了第一个元素以外，每个元素只有一个前驱元素，也只有一个后驱元素。
	Qperation
	InitList(* L)	初始化操作，建立一个空的线性表L
	ListEmpty(L)	判断线性表是否为空，返回Tre和False
	ClearList(*L)	线性表清空
	GetElem(L, i, *e)	将线性表L中的第i个位置的元素返回给e
	ListInsert(*L, i, e)	在线性表L中的第i个位置插入新元素e
	ListDelete(*L, i, *e)	删除线性表第i个位置的元素，并用e返回
	ListLength(L)	返回线性表L的元素个数
endADT
```
### 1.顺序存储
~~~c
#define MAXSIZE 50
#define SUCCESS 0
#define FAIL    1
typedef int Status;
typedef int ElemType;

typedef struct {
    ElemType data[MAXSIZE];
    int length;
}SqList;

/* 得到线性表的元素 */
Status GetElem(SqList L, int i, ElemType *e)
{
    if (L.length == 0 || i < 1 || i > L.length) {
        return FAIL;
    }
    *e = L.data[i-1];
    return SUCCESS;
}

/* 插入元素 */
Status InsertElem(SqList *L, int i, ElemType e)
{
    int k;
    if (L->length == MAXSIZE) {
        return FAIL;
    }
    if (i < 1 || i > L->length) {
        return FAIL;
    }
    if (i <= L->length) {
        for (k = L->length - 1 ; k >= i - 1; k--) {
            L->data[k+1] = L->data[k];
        }
    }
    L->data[i-1] = e;
    L->length++;
    return SUCCESS;
}

/* 删除表中元素 */
Status ListDelete(SqList *L, int i, ElemType *e)
{
    int k;
    if (L->length == 0) {
        return FAIL;
    }
    if (i < 1 || i > L->length) {
        return FAIL;
    }
    *e = L->data[i-1];
    if (i < L->length) {
        for (k = i - 1; k < L->length - 1; k++) {
            L->data[k] = L->data[k+1];
        }
    }
    L->length--;
    return SUCCESS;
}

Status PrintList(SqList L) {
    if (L.length <= 0)
        return FAIL;
    int i;
    for(i = 0; i < L.length; i++) {
        printf("List[%d] = %d\n", i, L.data[i]);
    }
    return SUCCESS;
}
~~~

 - 优点：可以快速存取表中任一位置的元素，存取读取的复杂度为O（1）
 - 缺点：插入和删除需要移动大量元素，当线性表长度变化时，难以确定存储空间的容量，造成存储碎片。



### 2.链表存储

```c
#define MAXSIZE 50
#define SUCCESS 0
#define FAIL    1

typedef int ElemType;
typedef int Status;

typedef struct Node
{
    ElemType data;
    struct Node *next;
} Node;

typedef struct Node *LinkList;

/* 获取链表中的元素 */
Status GetElem(LinkList head, int i, ElemType *e)
{
    int j;
    LinkList p;
    p = head;
    j = 1;
    while (p && j < i) {
        p = p->next;
        j++;
    }
    if (p == NULL || j > i) {
        return FAIL;
    }
    *e = p->data;
    return SUCCESS;
}

 /* 插入节点 */
Status ListInsert(LinkList *head, int i, ElemType e)
{
    int j;
    LinkList p, s;
    p = *head;
    j = 1;
    while (p && j < i) {
        p = p->next;
        j++;
    }
    if (p == NULL) {
        return FAIL;
    }
    s = (LinkList)malloc(sizeof(struct Node));
    s->data = e;
    s->next = p->next;
    p->next = s;

    // s = (LinkList)malloc(sizeof(struct Node));
    // s->data = e;
    // s->next = *head;
    // *head = s;
    return SUCCESS;
}

/* 删除节点 */
Status ListDelete(LinkList head, int i, ElemType *e)
{
    int j;
    LinkList p, q;
    p = head;
    j = 1;
    while (p->next && j < i) {
        p = p->next;
        j++;
    }
    if (p->next == NULL) {
        return FAIL;
    }
    q = p->next;
    p->next = q->next;
    *e = q->data;
    free(q);
    return SUCCESS;
}

Status PrintList(LinkList head)
{
    LinkList p;
    p = head;
    if (p == NULL) {
        return FAIL;
    }
    while (p) {
        printf("%d\n", p->data);
        p = p->next;
    }
    printf("\n");
    return SUCCESS;
}

/* 尾部插入数据 */
Status CreateListTail(LinkList *L, int n)
{
    LinkList p, r;
    *L = (LinkList)malloc(sizeof(Node));
    r = *L;
    int i = 0;
    for (i = 0; i < n-1; i++) {
        p = (LinkList)malloc(sizeof(Node));
        p->data = i;
        r->next = p;
        r = p;\
    }
    r->next = NULL;
}

/* 表格删除 */
Status clearList(LinkList *L)
{
    LinkList p;
    while (*L) {
        p = *L;
        *L = (*L)->next;
        free(p);
    }
    *L = NULL;
    return SUCCESS;
}
```

# 二.栈和队列
## 栈的抽象数据类型

```c
ADT 栈（stack）
Data
同线性表。元素具有相同的类型，相邻元素具有前驱和后继关系
Operation
	InitStack(* S)	初始化操作，建立一个空栈
	DestoryStack(*S)	若栈存在，则销毁它
	ClearStack(*S)	将栈清空
	StackEmpty(S)	若栈为空，返回True，否则返回False
	GetTop(S, *e)	若栈存在且非空，用e返回S的栈顶元素
	Push(*S, e)	若栈存在，插入新元素e到栈中并成为栈顶元素
	Pop(*S, *e)	删除栈S中栈顶元素，并用e返回
	StackLength(S) 返回栈S的元素个数
```

### 1. 顺序存储的栈

```c
#define MAXSIZE 50
#define SUCCESS 0
#define FAIL    1

typedef int Status;
typedef int SElemType;
typedef struct {
    SElemType data[MAXSIZE];
    int top;
}SqStack;

Status Push(SqStack *S, SElemType e) {
    if (S->top == MAXSIZE - 1) {
        return FAIL;
    }
    S->top++;
    S->data[S->top] = e;
    return SUCCESS;
}

Status Pop(SqStack *S, SElemType *e) {
    if (S->top == -1) {
        return FAIL;
    }
    *e = S->data[S->top];
    S->top--;
    return SUCCESS;
}

Status Print(SqStack *S) {
    if (S->top == -1) {
        return FAIL;
    }
    int i = 0;
    for (i; i < S->top + 1; i++) {
        printf("%d\t", S->data[i]);
    }
}
```
### 2.栈的链式存储结构
~~~
#define MAXSIZE 50
#define SUCCESS 0
#define FAIL    1

typedef int Status;
typedef int SElemType;

typedef struct StackNode {
    SElemType data;
    struct StackNode *next;
}*LinkStackPtr;

typedef struct LinkStack {
    LinkStackPtr top;
    int count;
}LinkStack;

Status Push(LinkStack *S, SElemType e)
{
    LinkStackPtr s = (LinkStackPtr)malloc(sizeof(struct StackNode));
    s->data = e;
    s->next = S->top;
    S->top = s;
    S->count++;
    return SUCCESS;
}

Status StackEmpty(LinkStack *S)
{
    if (S->count <= 0) {
        return SUCCESS;
    }
    return FAIL;
}

Status Pop(LinkStack *S, SElemType *e)
{
    LinkStackPtr p;
    p = S->top;
    *e = S->top->data;
    S->top = S->top->next;
    free(p);
    S->count--;
    return SUCCESS;
}

Status Print(LinkStack *S)
{
    int i;
    LinkStackPtr p;
    p = S->top;
    for (i = 0; i < S->count; i++) {
        printf("%d\t", p->data);
        p = p->next;
    }
    return SUCCESS;
}

int main(int argc, char**argv)
{
    LinkStack linkstack;
    linkstack.top = NULL;
    linkstack.count = 0;
    Push(&linkstack, 100);
    Push(&linkstack, 200);
    Push(&linkstack, 300);
    SElemType e;
    Pop(&linkstack, &e);
    Print(&linkstack);

}
~~~
### 3. 栈的利用：
四则运算表达式，因为加减乘除还要加上（）,因此四则运算必须要利用栈来实现

> 后缀表达式可以利用栈来计算得到结果 9+（3-1）* 3 + 10 / 2 中缀表达式     --------->  9 3 1 - 3 * + 10 2 /  后缀表示式
> 1.将中缀表达式转化为后缀表达式（栈用来进出运算的符号）
> 2.将后缀表达式运行结果进行输出（栈用来进出运算的数字）

##  队列的抽象数据类型
```c
ADT (Queue)
Data
	同线性表，元素有相同的类型， 相邻元素有前驱和后继
	Operation
	InitQueue(*Q)	初始化队列
	DestoryQueue(*Q)	销毁队列
	ClearQueue(*Q)	将队列清空
	QueueEmpty(Q)	若队列Q为空，返回True，否则返回false
	GetHead(Q, *e)	若队列存在，返回队列头
	EnQueue(*Q, e)	若队列Q存在，插入新元素e到队列Q中并成为队尾元素
	DeQueue(*Q, *e)	删除队列Q队头元素，并用e返回其值
	QueueLength(Q)	返回队列Q的元素个数
endADT
```

### 1. 队列的顺序存储类型
~~~c
#define MAXSIZE 50
#define SUCCESS 0
#define FAIL    1

typedef int Status;
typedef int QElemType;

typedef struct {
    QElemType data[MAXSIZE];
    int front;
    int rear;
} SqQueue;

Status InitQueue(SqQueue *Q)
{
    Q->front = 0;
    Q->rear = 0;
    return SUCCESS;
}

Status QueueLength(SqQueue Q)
{
    return (Q.rear - Q.front + MAXSIZE) % MAXSIZE;
}

Status EnQueue(Squeue *Q, QElemType e)
{
    if ((Q->rear + 1) % MAXSIZE == Q->front) {
        return FAIL;
    }
    Q->data[Q->rear] = e;
    Q->rear = (Q->rear + 1) % MAXSIZE;
    return SUCCESS;
}

Status DEQueue(SqQueue *Q, QElemType *e)
{
    if (Q->front == Q->reae)
        return FAIL;
    *e = Q->data[Q->front];
    Q->front = (Q->front + 1) % MAXSIZE;
    return SUCCESS;
}
~~~

### 2. 队列的链式存储结构
~~~c
#define MAXSIZE 50
#define SUCCESS 0
#define FAIL    1

typedef int status;
typedef int QElemType;

typedef struct QNode {
    QElemType data;
    struct QNode *next;
} QNode, *QueuePrt;

typedef struct {
    QueuePrt head, rear;
} LinkQueue;

status EnQueue(LinkQueue *Q, QElemType e)
{
    QueuePrt s = (QueuePrt)malloc(sizeof(QNode));
    s->data = e;
    s->next = NULL;
    Q->rear->next = s;
    Q->rear = s;
    return SUCCESS;
}

status DEQueue(LinkQueue Q, QElemtype *e)
{
    QueuePrt p;
    if (Q->head == Q->rear) {
        return FAIL;
    }
    p = Q->head;
    *e = Q->head->data;
    Q->head = Q->head->next;
    free(p);
    return SUCCESS;
}
~~~
