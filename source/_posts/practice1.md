---
title: how to sort two list
date: 2018-06-30 16:44:27
categories:
- practice
tags:
- practice
- sort
- algorithm
---
I code by C and use pointer to solve.But my code is not concise.Maybe I will optimize in the future.

Ok,let us see.

<!--more-->

{% codeblock %}
#include <stdio.h>
#include <stdlib.h>

//define a struct 
 struct ListNode {
	int val;
    struct ListNode *next;
 };

void addData(struct ListNode* l,int i,int j){
	for(i ;i < j;i++){
		struct ListNode* p = (struct ListNode*)malloc(sizeof(struct ListNode));	
		p->val  = i;
		p->next = NULL;
		l->next = p;
		l = l->next;
	}
	
}

//print list
void printList(struct ListNode* l){
	while(l != NULL){
		printf("%d",l->val);
		l = l->next;
	}
}

//sort two list
struct ListNode* addTwoNumbers(struct ListNode* l1, struct ListNode* l2) {
    struct ListNode* b = (struct ListNode*)malloc(sizeof(struct ListNode));
    struct ListNode* a = b; // a save address
    while(l1 || l2){
		if(l1->val > l2->val){
			b->val  = l2->val;
			b->next = l2;
			l2 = l2->next;
		}else{
			b->val  = l1->val;
			b->next = l1;
			l1 = l1->next;
		}
		if(l1 == NULL){
	    	b->next = l2;
	    	break;
	    }else if(l2 == NULL){
	    	b->next = l1;
	    	break;
    	}
	    b = b->next;
    }
    return a;
}

int main(){
	struct ListNode* l1 = (struct ListNode*)malloc(sizeof(struct ListNode));
	l1 -> val           = 1;
	l1 -> next          = NULL;
	struct ListNode* t1 = l1;
	addData(t1,3,5);
	struct ListNode* l2 = (struct ListNode*)malloc(sizeof(struct ListNode));
	l2 -> val           = 2;
	l2 -> next          = NULL;
	struct ListNode* t2 = l2;
	addData(t2,4,8);
	struct ListNode* d  = addTwoNumbers(l1,l2);
	printList(d);
	return 0;
}
{% endcodeblock %}