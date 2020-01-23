---
title: Algorithms of dynamic Sequential List
date: 2019-03-19 10:35:26
tags: "algorithms"
catogories: 
    - "DSAA"
---


## 线性表顺序结构-动态

### to declare

``` C
#define LIST_INIT_SIZE 10;
#define LIST_INCREMENT 5;
//array can't apply for a new capacity
//once it was defined.

typedef struct {
    DataList *data;
    int length;
    int datasize //to note the current capacity that has been occupied.
                // to judge if apply for a new capacity if it's full.   
} SeqList
```



<!--more-->



#### 1. to initialize


``` C
int initList(SeqList &L){
//initialize memory allocation
    L.data = (DataList*)molloc(LIST_INIT_SIZE * sizeof(DataList));
    if(!L.data)
        exit(0);
    L.length = 0;
    L.datasize = LIST_INIT_SIZE;
    return 0; //succeeded   
}
```


#### 2. to destroy all of member variables and recycle

``` C
int destroyList(SeqList &L){
    free(L.data);
    L.length = 0;
    L.datasize = 0;
    return 1;
}
```


#### 3. to recycle 
the process is *the same* as the above

#### 4. to check if it's empty

``` C
int checkEmpty(SeqList L){
    return !(L.length);
}
```

#### 5. to get the length of the list

``` C
int getLength(SeqList L){
    return L.length;
}
```

#### 6. to return the value of the n-th element 

``` C
DataList getElem(SeqList L, int n){
    if ( n < 1 || n > L.length )
        return NULL;
    //you also can only consider the feasible situation
    return (*(L.data+i-1))
}
```

#### 7. to return the index(AKA situation) of the given number 
if it's in the list, if succeed return 1, or return 0

``` C
int getLocation(SeqList L, int num){
    if ( data && L.length != 0 ){ //to ensure that pointer(data) or the list is not NULL
        int j;//default j=0;
        // to check if that given number is in the list
        while ( j < L.length && num != (*(data+j) )
            j++;
        if ( j != L.length)
            return j;
        else return -1;
    }else return -1;
}
```

#### 8. to get the value of its immediate successor
if it exits by giving values

``` C
DataList getI_suc(SeqList L, DataList values ){
    if ( data && L.length != 0 ){
        int j;
        while ( j < L.length-1 && values != (*(L.data+j)))
            j++;
        if ( j ! = L.length-1)
            return (*(L.data+j+1));
    
    }
    return NULL;
}
```

#### 9. to get...the value of its predecessor

``` C
DataList getI_prede(SeqList L, DataList value){
    if ( L.data && L.length != 0 ){
        int j = 1;
        while ( j < L.length && values != (*(L.data+j)))
            j++;
        if ( j != L.length )
            return (*(L.data+j-1));
    }
    return NULL;
}
```

#### 10. *ATTENTION*  
**insert a given value before an expected element of the list**

``` C
int insertBef(SeqList &L, DataList value, int i){
    //need to note here, applying for a new room for the value if feasible**
    if ( !L.data || L.length = 0 || i < 1 || i > L.length )
        return -1;      
        DataList  *newbase, *q, *p; //p,q that two being used into operation of putting backward other elements  
        //need to note here - function remolloc() just change its memory size(AKA resize the memory block), doesn't change its values that inside the original memory. 
        //check if the block of memory is full now
        if ( L.length == L.datasize ){ //L.length --> means the size of memory using now
        newbase = (DataList*)remolloc(L.data, (L.datasize + LIST_INCREMENT)*(sizeof(DataList)));
        //apply for room for value to be saved
        //what if failing to allocate the requested memory block, a null pointer is returned
        //appendix - dereference means “取指针所指向的对象的值” in Chinese. (AKA star sign(*))
        if ( !newbase )
            exit(0);  //terminate in advance ##reallyUsefulTrick##
        //on success
        L.datasize += LIST_INCREMENT;
        L.data = newbase;  //new address
        }
        //expected element and those behind need to move backwards,
        //the process starts with the last one.
        
        //NOTE: why need two pointers like p, q.
        // because all elements need being pointed by pointers.
        //so, we need two to deal with the process of offering a room for that value waiting to insert
        // and one of both to point that empty room
        q = L.data+i-1; //let the pointer q point to that room(by passing on the address)
        for ( p = L.data+L.length-1; p > i; --p ){
            *(p+1) = *p; //moving back a room   
        }//on success, it can't be oversized.
        *q = value;
        //don't forget add up its length.
        ++L.length;
        return 1;   
    }
        
}
```

#### 11. to delete an expected element of the list,
    if done return 1; or return 0.

```C
int delete_elem(SeqList &L; int s_num){
    if (!L.data || L.length == 0 || s_num < 1; s_num > L.length)
        exit(0);
    DataList *p;
    if ( p = L.data+s_num; p < L.data+L.length-1; ++p){
        *(p-1) = *p;
    }
    //on suceess
    --L.length;
    return 1;
}
```

### Reflection: A BIG-highlight

#### the difference between ++i and i++;

>it's mainly about the order of operations
for a 'for' loop, either works
but prefix(pre increment)seems more common
because you won't go wrong using it.
but
like post increment(AKA suffix/postfix)
it's more like, it returns the original value that it held before being incremented.


