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


## Use it

The macros is designed to be easy to use.

/* First, design your list structure */

struct nd_list_entry_t
{
    int    status_flag;
    struct ether_addr macaddr;
    struct in6_addr ip6addr;
    time_t jiontime;
    time_t timo;
    LIST_ENTRY(nd_list_entry_t) entries;     // Use this macro 
};

/* Second, create the head of list */

SLIST_HEAD([your list head name], nd_list_entry_t);

/* that's it. The header name is used to manipulate the list */

/* If you want to insert element at the head */

SLIST_INSERT_HEAD([head name], [your new element], entries);




## Test code

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
