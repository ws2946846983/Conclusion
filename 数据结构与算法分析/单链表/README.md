单链表
====

####单链表
* 信息域：用来存放表中的一个元素   
* 指针域：指针域用来存放指向一个结点的指针    
     
**顺序表：**    
优点：     
* 随机存储，直接可以通过下标访问表中元素，比如L.elem+i-1直接指向表中的第i个元素 
* 适合大量访问数据的操作的程序     
缺点：    
* 内存碎片化：因为顺序表是连续存储的，每次都需要申请一大块的内存空间，这样小部分的内容就浪费了，从而形成了内存碎片     
* 大数据量的插入删除需要移动大量的元素，效率不高 
     
**单链表：**     
优点：    
* 表中的数据离散存储， 通过指针进行操作访问     
* 插入与删除元素无需移动大量元素，只需修改指针域指向的地址即可   
* 适合大量插入删除操作的程序    
缺点：    
* 失去了随机存取的优点，所以每次访问表中的某个数据都要重头到尾扫一遍 

#####头插法
新插入结点始终未当前的第一个结点      
#####尾插法 
新插入结点始终为当前的最后一个结点    

例：有趣的算法：查找单链表的中间结点    
用两个不同的指针，按照不同的移动顺序来移动，这里我们暂且把他们成为**快慢指针**！     
每次循环， **快指针向后移动两个结点： p = p -> next -> next； 
慢指针向后移动一个结点： q = q -> next**    
原理：设置两个之子很*search、*mid都指向单链表的头节点。其中*search的移动速度是*mid的两倍。当*search指向末尾
节点的时候，mid正好就在中间了，这也是**标尺**的思想。
```c
Status GetMidNode(LinkList L, ELemType *e)
{
	LinkList search,mid;
	mid = search = L;
	
	while(search->next != NULL)
	{
		//search移动的速度是mid的2倍
		if(search->next->next != NULL)
		{
			search = search->next->next;
			mid = mid->next;
		}
		else
		{
			search = search->next;
		}
		 
		*e = mid->data;
		
		return OK;
	}
}
```
  
####12种基础基本操作代码实现  
* 构造空表  

```c
void InitList(LinkList L)
{
    L = (LinkList)malloc(sizeof(LNode));
    if(!L)exit(ERROR);
    L->next = NULL;
}
```
* 将链表置为空表  
```c
void ClearList(LinkList L)
{
    LinkList p = L->next;
    L->next = NULL;
    //接着就是释放头结点以外的结点了
    while(p)
    {
        p = L->next;
        free(L);  //释放首元结点
        L = p;    //移动到当前的首元结点 
    } 
} 
```     
* 判断是否为空表:**有头结点：L -> next = NULL;此时表为空表！ 
无头结点：L = NULL；此时为空表！**  
```c
Status ListEmpty(LinkList L)
{
    //有头节点的情况，只需判断头结点的指针域是否为空即可
    if(L->next) return FALSE;
    else return TRUE;   
}
```   
     
* 销毁单链表  

```c
void DestoryList(LinkList L)
{
    LinkList q;
    //删除头结点外的结点 
    while(L)
    {
        //指向首元结点，而不是头结点     
        q = L->next;
        free(L);
        L = q;      //删除后指向首元 
    }
} 
```   
	 
* 获得表长度  

```c
int ListLength(LinkList L)
{
    int i = 0;
    LinkList p = L->next;
    while(p)
    {
        i++;
        p = p->next;
    }
    return i;
} 
```   
	 
* 获得表中第i个元素的值  

```c
Status GetElem(LinkList L,int i,ElemType *e)
{
    int j = 1;
    //指向首元，然后依次后移，假如到了结尾或者j的值大于i
    //还没找个改元素说明i不合法
    LinkList p = L->next;
    while(p && j < i)
    {
        j++;
        p = p->next;
    } 
    if(!p || j> i) return ERROR;
    e = p->data;
    return OK;
}
```   
	 
* 查找表中是否存在满足条件的元素  

```c
int LocateElem(LinkList L,ElemType e,Status(*compare)(ElemType,ElemType))
{
    int i = 0;  
    LinkList p = L-> next;  
    while(p)  
    {  
        i++;  
        if(compare(p->data,e)) return i;  
        p = p-> next;  
    }  
    return 0;  
} 
```   
	 
* 获得某个结点的直接前驱 

```c
Status BeforeElem(LinkList L,ElemType choose,ElemType *before)
{
    LinkList q,p = L->next;
    while(p->next)
    {
        q = p->next;
        //判断p的后继是否为choose,是的话返回OK,否则继续后移 
        if(q->data == choose)
        {
            before = p->data;
            return OK; 
        }
        p = q;      
    }
    return ERROR; 
}
```   
	 
* 获得某个结点的直接后继  

```c
Status NextElem(LinkList L,ElemType choose,ElemType *behind)
{
    LinkList p = L->next;
    while(p->next)
    {
        if(p->data == choose)
        {
            behind = p->next->data;
            return OK;
        }
        p = p->next;
    }
    return ERROR;   
}
```   
	 
* 往表中第i个位置插入元素  

```c
Status ListInsert(LinkList L,int i,ElemType e)
{
    int j = 0;
    LinkList p,q =L;  //让q指向头结点
    while(p && j < i - 1)
    {
        j++;
        p = p->next;  //p指向下一个节点 
    }
    if(!p || j > i - 1) return ERROR;
    p = (LinkList)malloc(sizeof(LNode));
    //要先让插入的结点的指针域指向插入位置的后继结点  
    //再让插入节点的前驱的指针域指向插入结点  
    //！！！顺序不能乱  
    p->data = e;
    p->next = q->next; //插入节点的指针域指向第i个节点的后继节点
    q->next = p; //将第i个节点的指针域指向插入的新节点
    return OK;
} 
```   
	 
* 删除表中第i个元素  

```c
Status ListDelete(LinkList L,int i,ElemType *e)
{
    int j = 0;
    LinkList p,q = L;
    while(q->next && j < i-1)
    {
        j++;
        q = q->next;//将q指向要删除的第i个节点的前驱节点
    }
    if(!q || j >i -1) return ERROR;
    p = q->next;   //指向准备删除的结点
    q->next = p->next; //删除结点的前驱的指针域指向删除结点的后继   
    e = p->data; 
    free(p);    //释放要删除的结点  
    return OK;
}
```   
	 
* 遍历单链表中的所有元素  

```c
void ListTraverser(LinkList L,void(*visit)(ElemType))
{
    LinkList p = L->next;
    while(p)
    {
        visit(p->data);
        p = p->next;
    }
    printf("\n");
}
```   