# C-Project-1

#include "assignment3.h"
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

static SLnodePtr createSLList(int n, int k) // function to make SLnodePtr
{
  if (n == 0) { // base case
    return NULL;
  } else {
    SLnodePtr new_node =
        malloc(sizeof(struct SLnode));      // create space for new SL node
    new_node->key = (rand() % (k + 1)) + k; // make the value of the node random
    new_node->next = createSLList(
        n - 1, new_node->key); // call itself recursively with new values
  }
}

HBnodePtr createHBlist(int n, int m) {
  srand(time(NULL));
  if (n == 0) {
    return NULL;
  } else {
    HBnodePtr new_HBnode =
        malloc(sizeof(struct HBnode)); // make space for first HB node
    new_HBnode->key = rand() % 100;    // make value for first HB node
    int k = new_HBnode->key;           // make k equal value of new HB node
    HBnodePtr head = new_HBnode;       // make first HB node equal head
    for (int i = 1; i < (n);
         i++) // for loop to make each HB node and its SL list
    {   
      new_HBnode->bottom =
          createSLList(rand() % (m + 1), k); // call SL list function and
      new_HBnode->next =
          malloc(sizeof(struct HBnode)); // make space for new HB node
      new_HBnode = new_HBnode->next;     // make pointer point at new HB node
      new_HBnode->key = (rand() % (k + 1)) + k; // make value of HB node
      k = new_HBnode->key; // make value of current HB node equal k
      if (i == (n - 1)) {
        new_HBnode->bottom = createSLList(rand() % (m + 1), k);
        new_HBnode->next = NULL;
      }
    }

    return head;
  }
}

void printSLlist(const SLnodePtr L) {
  if (L == NULL) {
    return;
  }
  printf("%d->", L->key);
  printSLlist(L->next);
}

void printHBlist(const HBnodePtr L) {
  if (L == NULL) {
    return;
  }
  printf("\n%d->", L->key);
  printf("|");
  printSLlist(L->bottom);
  printHBlist(L->next);
}

void freeSLlist(SLnodePtr L) {
  if (L == 0) {
    return;
  } else {
    freeSLlist(L->next);
  }
  free(L);
}
void freeHBlist(HBnodePtr L) {
  if (L == NULL) {
    return;
  } else {
    freeHBlist(L->next);
  }
  freeSLlist(L->bottom);
  free(L);
}

static SLnodePtr copySLList(SLnodePtr L) {
  if (L == NULL) {
    return NULL;
  }
  SLnodePtr newhead = malloc(sizeof(struct SLnode));
  SLnodePtr location = newhead;
  while (L != NULL) {
    location->key = L->key;
    L = L->next;
    if (L != NULL) {
      location->next = malloc(sizeof(struct SLnode));
      location = location->next;
    }
  }
  location->next = NULL;
  return newhead;
}
static SLnodePtr mergesort(SLnodePtr a, SLnodePtr b) {
  SLnodePtr newSLList;          // make first node NULL
  SLnodePtr returnvalue = NULL; // make it NULL
  if (a != NULL || b != NULL) {
    newSLList = malloc(sizeof(struct SLnode));
    returnvalue = newSLList;
  }

  while (a != NULL ||
         b != NULL) { // while loop runs while both lists are not Null
    if (a == NULL) {
      newSLList->key = b->key;
      newSLList->next = copySLList(b->next);
      break;
    }

    if (b == NULL) {
      newSLList->key = a->key;
      newSLList->next = copySLList(a->next);
      break;
    }
    newSLList->next =
        malloc(sizeof(struct SLnode)); // make new node of newSLList
    if ((a->key <= b->key)) { // if the element in list B is > than A element or
                              // if element in A list is null
      newSLList->key = a->key; // then value of A element becomes value of new
                               // element in newlist
      a = a->next;             // the A pointer points at the next A element
} else { // if the element in list B is > than B element or element in A
             // list is null
      newSLList->key = b->key; // then the value of B element becomes value oof
                               // new element in newlist
      b = b->next;             // the B pointer now points at the next B element
    }
    newSLList = newSLList->next; // moving newSLList node
  }
  return returnvalue;
}

SLnodePtr flattenList(const HBnodePtr L) {
  SLnodePtr head = malloc(sizeof(struct SLnode)); // make a head
  SLnodePtr newSLList = NULL; // declare a newSLlist variable
  SLnodePtr freer;
  HBnodePtr HBhead = L;
  while (HBhead != NULL) {
    head->key = HBhead->key;     // make value of head equal to value of HB list
    head->next = HBhead->bottom; // make head point at SL list
    freer = newSLList;
    newSLList = mergesort(
        newSLList,
        head); // call my merge function with accumulated SL list as first
               // argument and with my new SL list as second argument
    freeSLlist(freer);
    HBhead = HBhead->next; // make HB head point at next HB node
  }
  free(head); // free up head
  return newSLList;
}

void main() {
  int n = 5, m = 5;
  HBnodePtr L = createHBlist(n, m);
  printf(" HB list \n ");
  printHBlist(L);
  printf(" \n ");
  printf(" SL list \n ");
  SLnodePtr P = flattenList(L);
  printSLlist(P);
  printf(" \n ");
  freeSLlist(P);
  freeHBlist(L);
}



