---
title: "Data structures: Linked List"
date: 2023-03-10T11:55:27+01:00
# draft: false
---

## What is a linked list?
A linked list is a widely used data structure in which, as the name suggests, the items are stored in a not contigous way. We can think about a linked list as a chain of what we are going to call **nodes**. Essentially the function of the node is quite simple: store a value or key and the address of the next node. 

## Difference between arrays and linked lists
- Arrays store elements in contigous way. Thus the access to an element at given index is faster than in a linked list.
- Linked lists store elements in a not contigous way.
- Linked lists are dynamic in size. Arrays have a fixed size that cannot be modified at runtime.
- Insertion in a linked list is faster than in a array
- Accessing elements is faster in an array than in a linked list. In order to access an element in a linked list you have to traverse it.

## Building a linked list in C++
First thing first we are going to implement the Node class. Since we are not interested in any particular data type we might use templates.

```cpp
template <typename T>
class Node
{
public:
    Node(T value);
    T data;
    Node<T>* next;
};
```
Then we proceed to implement our LinkedList class

```cpp
template<typename T>
class LinkedList
{
public:
    // Constructor
    LinkedList();
    Node<T>* head;
    Node<T>* tail;

    // Methods
    Node<T>* GetData(int index); 
    void InsertHead(T val);
    void InsertTail(T val);
    void Insert(int index, T val);
    void RemoveHead();
    void RemoveTail();
    void Remove(int index);
    void PrintList();
    int CountElements();

private:
    int count_elements;
};
```
Now can we implement the class methods:
- GetData: return the value associated with the given index.
- InsertHead: replace the first node in the linked list with the given one.
- InsertTail: replace the last node in the linked list with the given one.
- Insert: Insert a node at given index.
- RemoveHead: Remove head node.
- RemoveTail: Remove tail node.
- Remove: Remove node at given index. 
- PrintList: print the list.
- CountElements: return the number of elements.

## Get the node at given index
```cpp
template<typename T>
Node<T>* LinkedList<T>::GetData(int index)
{   
    // Check if index is out of bound
    if(index < 0 || index > count_elements)
    {
        std::cout << "Invalid index";
        return nullptr;
    }
    // Start from head
    Node<T>* node = head;

    // Traverse the list to the selected index and return it
    for(int i = 0; i < index; ++i)
    {
        node = node->next;
    }

    return node;
}
```

## Insertion at the beginning of the list
```cpp
template<typename T>
void LinkedList<T>::InsertHead(T val)
{   
    // Create a new node
    Node<T>* node = new Node<T>(val);

    // Check if list is empty. If it is, head is equal to tail
    if(count_elements == 0)
    {
        tail = head;
    }
    // the new node becomes the head
    node->next = head;
    node = head;
}
```

## Insertion at the end of the list
```cpp
template <typename T>
void LinkedList<T>::InsertTail(T val)
{
    // If the linked list is empty, tail is equal to head
    if(count_elements == 0)
    {
        InsertHead(val);
        return;
    }

    // Create a new node
    Node<T> * node = new Node<T>(val);

    // The current Tail will no longer become a Tail
    // so the Next pointer of the current Tail will
    // point to the new node
    tail->next = node;

    // The new node now becomes the tail
    tail = node;
    count_elements++;
}
```

## Insert a node at a specific index
```cpp
template<typename T>
void LinkedList<T>::Insert(int index, T val)
{
    if(index < 0 || index > count_elements)
    {
        std::cout << "Invalid Index";
        return;
    }
    // Insert a new head
    if(index == 0)
    {
        InsertHead(val);
        return;
    }
    // Inserting a new tail
    if(index == count_elements)
    {
        InsertTail(val);
        return;
    }

    // Start to find previous node from the Head
    Node<T>* prevNode = Head;

    // Traverse the elements until the selected index is found
    for (int i = 0; i < index - 1; i++)
    {
        prevNode = prevNode->next;
    }
    
    // Create the next node which is the element after previous node
    Node<T>* nextNode = prevNode->next;

    // Create a new node
    Node<T>* node = new Node<T>(val);

    // Insert this new node between the previous node and the next node
    node->next = nextNode;
    prevNode->next = node;
    count_elements++;
}
```

## Remove head from linked list
```cpp
template <typename T>
void LinkedList<T>::RemoveHead()
{
    if(count_elements == 0)
    {
        std::cout << "No element found\n";
        return;
    }
    // Save the current Head to a new node
    Node<T>* node = head;

    // Point the Head pointer to the element
    // next to the current head(the second element)
    Head = head->next;

    // Now it is safe to remove the first element
    delete node;
    count_elements--;
}
```

## Remove tail from linked list
```cpp
template <typename T>
void LinkedList<T>::RemoveTail()
{
    if(count_elements == 0)
    {
        std::cout << "No element found\n";
        return;
    }
    if(count_elements == 1)
    {
        RemoveHead();
        return;
    }
    // Save the current head to a new node
    Node<T>* prevNode = head;
    
    // Node that will be removed
    Node<T>* node = head->next;

    // Traverse the list until the last element
    while(node->next != nullptr)
    {
        prevNode = prevNode->next;
        node = node->next;
    }
    // Delete the node
    delete node;
    count_elements--;
}
```

## Remove element at given index
```cpp
template <typename T>
void LinkedList<T>::Remove(int index)
{
    if(count_elements == 0)
    {
        std::cout << "No element found\n";
        return;
    }
    if(index == 0)
    {
        RemoveHead();
        return;
    }
    else if(index == count_elements -1)
    {
        RemoveTail();
        return;
    }

    Node<T>* prevNode = head;
    // Finding the element before given index
    for(int i = 0; i < index - 1; i++)
    {
        prevNode = prevNode->next;
    }

    // node to be removed
    Node<T>* node = prevNode->next;

    // Node after prevNode
    Node<T>* nextNode = node->next;
    
    prevNode->next = nextNode;
    delete node;
    count_elements--;
}
```

## Print the element inside the linked list
```cpp
template <typename T>
void LinkedList<T>::PrintList()
{
    if(count_elements == 0)
    {
        std::cout << "No element inside the list";
    }
    Node<T>* node = head;
    // Traverse the list and print the data
    while(node->next != nullptr)
    {
        std::cout << node->data << " ";
    }
    std::cout << std::endl;
}
```
## Count the elements inside a linked list
```cpp
template <typename T>
int LinkedList<T>::CountElements()
{
    if(count_elements == 0)
    {
        std::cout << "List is empty";
    }
    return count_elements;
}

```
## Conclusion
Linked lists are basic knowledge for any CS student. Don't forget to add them in your toolbox.



