---
title: Linux内嵌链表(<sys/queue.h>)
date: 2021-01-25
categories:
- Data-Structure
tags:
- Linux-API
---

C is not same as C++. Any structure must be realized on its own. There is a "queue" relies on "<sys/queue.h>".
Any detail can read the "man 3 queue".

These macros define and operate on four types of data structures: 
1. singly-linked lists
2. singly-linked tail queues
3. lists
4. tail queues

All four structures support the following functionality:

1.   Insertion of a new entry at the head of the list.
2.   Insertion of a new entry after any element in the list.
3.   O(1) removal of an entry from the head of the list.
4.   Forward traversal through the list.
5.   Swapping the contents of two lists.

Every data structures have its own characteristics.

## Singly-linked lists

Singly-linked lists are the simplest of the four data structures and support only the above functionality.  Singly-
linked lists are ideal for applications with large datasets and few or no removals, or for implementing a LIFO queue.
Singly-linked lists add the following functionality:

1.   O(n) removal of any entry in the list.

SLIST_EMPTY(SLIST_HEAD *head);      //Test the list is empty.

SLIST_ENTRY(TYPE);      //      Help you to create the "next" point.

SLIST_FIRST(SLIST_HEAD *head);      // Help you to create the head of list.

SLIST_FOREACH(TYPE *var, SLIST_HEAD *head, SLIST_ENTRY NAME);      // Help you to forward the list

SLIST_HEAD(HEADNAME, TYPE);     

SLIST_HEAD_INITIALIZER(SLIST_HEAD head);

SLIST_INIT(SLIST_HEAD *head);

SLIST_INSERT_AFTER(TYPE *listelm, TYPE *elm, SLIST_ENTRY NAME);

SLIST_INSERT_HEAD(SLIST_HEAD *head, TYPE *elm, SLIST_ENTRY NAME);

SLIST_NEXT(TYPE *elm, SLIST_ENTRY NAME);

SLIST_REMOVE_HEAD(SLIST_HEAD *head, SLIST_ENTRY NAME);

SLIST_REMOVE(SLIST_HEAD *head, TYPE *elm, TYPE, SLIST_ENTRY NAME);


### Use it

The macros is designed to be easy to use.



**Test code**

```c
#include <sys/queue.h>
#include <stdio.h>

struct my_list
{
    int data_1;
    int data_2;
    SLIST_ENTRY(my_list) next;
};

SLIST_HEAD(my_slist_head, my_list) my_slist_head;

int main()
{
    SLIST_INIT(&my_slist_head);
    
    struct my_list *var = NULL;

    struct my_list list_1 = {
        .data_1 = 1,
        .data_2 = 2,
    }; 
    struct my_list list_2 = {
        .data_1 = 3,
        .data_2 = 4,
    }; 

    SLIST_INSERT_HEAD(&my_slist_head, &list_1, next);

    SLIST_INSERT_AFTER(&list_1, &list_2, next);

    SLIST_REMOVE_HEAD(&my_slist_head, next);


    if(! SLIST_EMPTY(&my_slist_head)){
        SLIST_FOREACH(var, &my_slist_head, next){
            printf("data_1 = %d \t data_2 = %d\n", var->data_1, var->data_2);
        }
    }
    return 0;
}

/* OUTPUT */
data_1 = 3 	 data_2 = 4

```

## Singly-linked tail queues

Singly-linked tail queues add the following functionality:

1.   Entries can be added at the end of a list.
2.   O(n) removal of any entry in the list.
3.   They may be concatenated.

However:

1.   All list insertions must specify the head of the list.
2.   Each head entry requires two pointers rather than one.
3.   Code size is about 15% greater and operations run about 20% slower than singly-
    linked lists.

Singly-linked tail queues are ideal for applications with large datasets and few or no
removals, or for implementing a FIFO queue.


STAILQ_CONCAT(STAILQ_HEAD *head1, STAILQ_HEAD *head2);

STAILQ_EMPTY(STAILQ_HEAD *head);

STAILQ_ENTRY(TYPE);

STAILQ_FIRST(STAILQ_HEAD *head);

STAILQ_FOREACH(TYPE *var, STAILQ_HEAD *head, STAILQ_ENTRY NAME);

STAILQ_HEAD(HEADNAME, TYPE);

STAILQ_HEAD_INITIALIZER(STAILQ_HEAD head);

STAILQ_INIT(STAILQ_HEAD *head);

STAILQ_INSERT_AFTER(STAILQ_HEAD *head, TYPE *listelm, TYPE *elm, STAILQ_ENTRY NAME);

STAILQ_INSERT_HEAD(STAILQ_HEAD *head, TYPE *elm, STAILQ_ENTRY NAME);

STAILQ_INSERT_TAIL(STAILQ_HEAD *head, TYPE *elm, STAILQ_ENTRY NAME);

STAILQ_NEXT(TYPE *elm, STAILQ_ENTRY NAME);

STAILQ_REMOVE_HEAD(STAILQ_HEAD *head, STAILQ_ENTRY NAME);

STAILQ_REMOVE(STAILQ_HEAD *head, TYPE *elm, TYPE, STAILQ_ENTRY NAME);
