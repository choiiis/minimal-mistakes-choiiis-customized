---
title: "[test] 링크드리스트"
excerpt: "업로드테스트"

categories:
  - Categories1
tags:
  - [tag1, tag2]

permalink: /categories2/linkedlist/

toc: true
toc_sticky: true

date: 2023-09-19
last_modified_at: 2023-09-19
---

# 자료구조_링크드리스트

220707

- 링크드리스트(Linked List)
    - 앞쪽 노드가 뒷쪽 노드의 주소값을 저장
    - 공간은 물리적으로 떨어져 있지만, 논리적으로 주소값을 저장해 연결
    - 맨 앞 노드 : 헤드노드Head Node / 맨 뒤 노드 : 꼬리노드Tail Node
    - 삽입과 삭제(사이즈 조절)가 용이
    - 임의적으로 접근이 힘듦
    - 크기의 제한이 없을 때 사용
    - 노드 추가 Insert / Append / Remove

- LinkedList.h

```c
#ifndef LINKEDLIST_H
#define LINKEDLIST_H

#include <stdio.h>
#include <stdlib.h>	

typedef int ElementType;

typedef struct tagNode
{
	ElementType Data;
	struct tagNode* NextNode;
}Node;

/*함수 원형 선언*/
Node* SLL_CreateNode(ElementType NewData);
void SLL_DestroyNode(Node* Node);
void SLL_AppendNode(Node** Head, Node* NewNode);
void SLL_InsertAfter(Node* Current, Node* NewNode);
void SLL_InsertNewHead(Node** Head, Node* NewHead);
void SLL_RemoveNode(Node** Head, Node* Remove);
Node* SLL_GetNodeAt(Node* Head, int Location);
int SLL_GetNodeCount(Node* Head);

#endif
```

- LinkedList.c

```c
#include "LinkedList.h"

/* 노드 생성 */
Node* SLL_CreateNode(ElementType NewData)
{
	Node* NewNode = (Node*)malloc(sizeof(Node));

	NewNode->Data = NewData; /* 데이터를 저장한다 */
	NewNode->NextNode = NULL; /* 다음 노드에 대한 포인터는 NULL로 초기화 */

	return NewNode; /* 노드의 주소를 반환 */
	
}

/* 노드 소멸 */
void SLL_DestroyNode(Node* Node)
{
	free(Node);
}

/* 노드 추가 */
void SLL_AppendNode(Node** Head, Node* NewNode)
{
	/* 헤드 노드가 NULL이라면 새로운 노드가 Head */
	if ((*Head) == NULL)
	{
		*Head = NewNode;
	}
	else
	{
		/* 테일을 찾아 NewNode 연결 */
		Node* Tail = (*Head);
		while (Tail->NextNode != NULL)
		{
			Tail = Tail->NextNode;
		}

		Tail->NextNode = NewNode;
	}
}

/* 노드 삽입 */
void SLL_InsertAfter(Node* Current, Node* NewNode)
{
	NewNode->NextNode = Current->NextNode;
	Current->NextNode = NewNode;
 }

void SLL_InsertNewHead(Node** Head, Node* NewHead)
{
	if (Head == NULL)
	{
		(*Head) == NewHead;
	}
	else
	{
		NewHead->NextNode = (*Head);
		(*Head) == NewHead;
	}
}

/* 노드 제거 */
void SLL_RemoveNode(Node** Head, Node* Remove)
{
	if (*Head == Remove)
	{
		*Head = Remove->NextNode;
	}
	else
	{
		Node* Current = *Head;
		while (Current != NULL && Current->NextNode != Remove)
		{
			Current = Current->NextNode;
		}
		if (Current != NULL)
			Current->NextNode = Remove->NextNode;
	}
}

/* 노드 탐색 */
Node* SLL_GetNodeAt(Node* Head, int Location)
{
	Node* Current = Head;

	while (Current != NULL && (--Location) >= 0)
	{
		Current = Current->NextNode;
	}

	return Current;
}

/* 노드 수 세기 */
int SLL_GetNodeCount(Node* Head)
{
	int Count = 0;
	Node* Current = Head;

	while (Current != NULL) {
		Current = Current->NextNode;
		Count++;
	}
	return Count;
}
```

- Test_LinkedLst.c

```c
#include "LinkedList.h"

int main(void)
{
	int i = 0;
	int Count = 0;
	Node* List = NULL;
	Node* Current = NULL;
	Node* NewNode = NULL;

	/* 노드 5개 추가 */
	for (i = 0; i < 5; i++)
	{
		NewNode = SLL_CreateNode(i);
		SLL_AppendNode(&List, NewNode);
	}

	NewNode = SLL_CreateNode(-1);
	SLL_InsertNewHEad(&List, NewNode);

	/* 리스트 출력 */
	Count = SLL_GetNodeCount(List);
	for (i = 0; i < Count; i++)
	{
		Current = SLL_GetNodeAt(List, i);
		printf("List[%d] : %d\n", i, Current->Data);
	}

	/* 리스트의 세번째 노드 뒤에 새 노드 삽입 */
	printf("\nInserting 3000 After [2]....\n\n");

	Current = SLL_GetNodeAt(List, 2);
	NewNode = SLL_CrateNode(3000);

	SLL_InsertAfter(Current, NewNode);

	/* 리스트 출력 */
	Count = SLL_GetNodeCount(List);
	for (i = 0; i < Count; i++)
	{
		Current = SLL_GetNodeAt(List, i);
		printf("List[%d] : %d\n", i, Current -> Data);
	}

	/* 모든 노드를 메모리에서 제거 */
	printf("\nDestroying List...\n");

	for (i = 0; i < Count; i++)
	{
		Current = SLL_GetNodeAt(List, 0);

		if (Current != NULL)
		{
			SLL_RemoveNode(&List, Current);
			SLL_DestroyNode(Current);
		}
	}

	return 0;

}
```

C언어로 표현하는 링크드 리스트의 노드

```c
struct Node
{
	int Data; /* 데이터 필드 */
	struct Node* NextNode;  /* 다음 노드를 가리키는 포인터 */
};
```

```c
typedef struct tagNode
{
	int Data; /* 데이터 필드 */
	struct tagNode* NextNode; /* 다음 노드를 가리키는 포인터 */
} Node;
```

- 노드 생성

```c
Node* SLL_CreateNode(ElementType NewData)
{
	Node* NewNode = (Node*)mallc(sizeof(Node));

	NewNode->data = NewData;  /* 데이터를 저장한다 */
	NewNode->NextNode = NULL; /* 다음 노드에 대한 포인터는 NULL로 초기화한다.*/

	return NewNode;  /* 노드의 주소를 반환한다 */
}

```

- 노드 소멸

```c
void SLL_DestroyNode(Node* Node)
{
	free(Node);
}
```

- SLL_ ?
    - Singly Linked List의 약자
    - 링크드 리스트는 한쪽 방향으로 엮어 있어 더블 링크드 리스트와 구별
    
- 모든 함수를 한 파일에 넣을 수 없음 → 함수의 역할에 따라 파일 분할
