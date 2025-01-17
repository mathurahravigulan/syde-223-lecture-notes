# Heap
**Priority Queue**: An element with high priority is served before an element with low priority

## Linear list structure
- top for next dequeue

Peek: `O(1)`
Enqueue: `O(n)`
Dequeue: `O(1)`

## Branched Structure
Peek: `O(1)`
Enqueue: `O(log n)`
Dequeue: `O(log n)`

## Binary Max Heap
- Binary tree with keys stored in its nodes
- Parent key value >= children key value
- Therefore the root node has the max value
- A heap needs to be a **complete binary tree**

## Complete Binary Tree
- A binary tree that is completely filled at all levels with the exception of the bottom-most level
- And at the bottom-most level, all leaf nodes are as far left as possible

![](images/complete-binary-tree.png)

## Heaps ADT Operations
### Insert (enqueue)
- Insert a node into the heap
1. Insert node as bottom-right-most leaf in the tree
2. If the parent has a smaller value than the node, swap with the parent
3. Continue this **re-heap upwards**
    - Until the parent has a higher (or same value than the node) or
    - Unitl reaching the root node (no more parent)


![](images/heap-insert.png)

## Remove (dequeue)
- Remove a node from the heap **always remove the root node**
1. Save the root node for return
2. Replace the root node with the bottom-right-most leaf node in the tree
3. If there is any child of the new root, find the larger child. If same, prefer left
4. If the child has a key larger than the node, swap the node with that child
5. Continue **re-heap** downwards
    - Until that child is smaller, or same
    - Or until reaching a leaf node (the bottom)
6. Return the old node

![](images/heap-delete.png)

## Heap (Priority Queue) Implementation
- Use complete binary tree
Re-heap upwards needs to easily find parent
- Insert (enqueue) needs the next bottom right position

## Complete Binary Tree as Sequential Array
**Complete Binary Tree Access Formulas**
- Root node: `array index 1`
- Parent of node i: `floor(i/2)`
- Left child of node i: `2i`
- Right child of node i: `2i + 1`
- Is node i a leaf: check if `2i > size n`

### Max Heap Demo Code
```cpp
#include<iostream>
#include<string>

using namespace std;

class HeapNode{
private:
    int key;                              // Heap item priority key
    string data;                         // data value

public:
    HeapNode();
    HeapNode(int key, string data);     // Constructor
    int getKey();                         // Return item priority
    void setKey(int key);               // Set the priority key value
    string getData();                    // Return data item
    void setData(string data);          // Set the data item value
};

//-----------------------------------
// Constructor
//-----------------------------------
HeapNode::HeapNode(): key(0), data(""){ }

HeapNode::HeapNode(int k, string str): key(k), data(str){ }


//-----------------------------------
// Return item priority
//-----------------------------------
int HeapNode::getKey(){
    return key;
}

//-----------------------------------
// Set the priority key value
//-----------------------------------
void HeapNode::setKey(int key){
    key = key;
}

//-----------------------------------
// Return data item
//-----------------------------------
string HeapNode::getData(){
    return data;
}

//-----------------------------------
// Set the data item value
//-----------------------------------
void HeapNode::setData(string str){
    data = str;
}



class Heap{
private:
    HeapNode     *elements;                 // Pointer to dynamically allocated array
    int          size;              // Number of elements in the heap
    int          capacity;

public:
    Heap(int cap);
    ~Heap();                                 // Destructor

    bool Enqueue(HeapNode *item);
    void ReheapUp(int index);     // Reheap after inserting item

    HeapNode *Dequeue();                     // Get item at the root
    void ReheapDown(int index);   // Reheap after removing item

    int getSize();                    // Return number of elements in the heap
    void printAll();                         // Print all the elements in the heap
};

//---------------------------------------
// constructor
//---------------------------------------
Heap::Heap(int cap){
    elements = new HeapNode[cap];
    size = 0;
    capacity = cap;
}

//---------------------------------------
// Destructor
//---------------------------------------
Heap::~Heap(){
    delete[] elements;
}

//---------------------------------------
// Add an item to the heap
//---------------------------------------
bool Heap::Enqueue(HeapNode *item){
    if(size == capacity) return false;
    else {
        elements[size + 1] = *item; // Copy item into array
        ReheapUp(size + 1);
        size++;
        return true;
    }
}



//---------------------------------------
// Reheap after inserting item
//---------------------------------------
void Heap::ReheapUp(int index){
    ////////////////////////////////////////////////////////////////////

    //Until reaching the root node (no more parent)
    if(index == 1) return;

    //	If the parent has a smaller value than the node, swap with the parent
    int parent = index / 2;
    if(elements[parent].getKey() < elements[index].getKey()){
        // Swap these two elements
        HeapNode temp = elements[parent];
        elements[parent] = elements[index];
        elements[index] = temp;
        // Make recursive call till reheaping completed
        ReheapUp(parent);
    }
    else return;

    ////////////////////////////////////////////////////////////////////

}


//---------------------------------------
// Get item at the root
//---------------------------------------
HeapNode *Heap::Dequeue(){
    if(size == 0)  return NULL;

    //index 1 is the root. Use temp to save the to-be-returned node.
    HeapNode *temp = new HeapNode(elements[1]);  //default copy constructor, shallow copy

    // Copy last item into root
    elements[1] = elements[size]; //default assignment shallow copy

    size--;

    // Reheap the tree
    ReheapDown(1);

    return temp;
}

//---------------------------------------
// Reheap after removing item
//---------------------------------------
void Heap::ReheapDown(int index){
    ////////////////////////////////////////////////////////////////////
    //Exit when reaching a leaf node (the bottom)
    if (2 * index > size) return;

    //	If there is any child of the node at index,
    int chosenChild;

    // only one child, it must be the left child
    if (2 * index == size) chosenChild = size;
    else { // two children, find the larger child. If same, e.g., prefer left.
        int leftChild = 2 * index;
        int rightChild = 2 * index + 1;
        if( elements[leftChild].getKey() >= elements[rightChild].getKey() ) chosenChild = leftChild;
        else chosenChild = rightChild;
    }


    if(elements[chosenChild].getKey() > elements[index].getKey()){
        //If the child has a key larger than the node, swap the node with that child.

        HeapNode temp = elements[chosenChild];
        elements[chosenChild] = elements[index];
        elements[index] = temp;

        ReheapDown(chosenChild); //Continue re-heap downwards
    }
    else return;

    ////////////////////////////////////////////////////////////////////

}




//---------------------------------------
// Return number of elements in the heap
//---------------------------------------
int Heap::getSize(){
    return size;
}

//---------------------------------------
// Print all the elements in the heap
//---------------------------------------
void Heap::printAll(){
    if(size == 0) cout<< "Empty!" <<endl;
    for(int i=1; i<=size; i++){
        cout << "Heap element " << i << ". key = " << elements[i].getKey() << ",  data = " << elements[i].getData() << endl;
    }
}

//main
int main(){

    Heap *theHeap = new Heap(20);  // Create a heap of the default size


    theHeap->Enqueue(new HeapNode(10, "a"));
    theHeap->Enqueue(new HeapNode(5, "b"));
    theHeap->Enqueue(new HeapNode(7, "c"));
    theHeap->Enqueue(new HeapNode(4, "d"));
    theHeap->Enqueue(new HeapNode(2, "e"));
    theHeap->Enqueue(new HeapNode(1, "f"));
    theHeap->printAll(); //this is breadth first, level traversal

/*

    cout<<"dequeue data: " <<  theHeap->Dequeue()->getData() << endl;
    theHeap->printAll(); //this is breadth first, level traversal

	cout<<"enqueue 7 (g) " << endl;
	theHeap->Enqueue(new HeapNode(7, "g"));
    theHeap->printAll(); //this is breadth first, level traversal

	cout<<"enqueue 5 (h) " << endl;
	theHeap->Enqueue(new HeapNode(5, "h"));
    theHeap->printAll(); //this is breadth first, level traversal

    cout<<"dequeue data: " <<  theHeap->Dequeue()->getData() << endl;
    theHeap->printAll(); //this is breadth first, level traversal

    cout<<"dequeue data: " <<  theHeap->Dequeue()->getData() << endl;
    theHeap->printAll(); //this is breadth first, level traversal

    cout<<"dequeue data: " <<  theHeap->Dequeue()->getData() << endl;
    theHeap->printAll(); //this is breadth first, level traversal

    cout<<"dequeue data: " <<  theHeap->Dequeue()->getData() << endl;
    theHeap->printAll(); //this is breadth first, level traversal

    cout<<"dequeue data: " <<  theHeap->Dequeue()->getData() << endl;
    theHeap->printAll(); //this is breadth first, level traversal

    cout<<"dequeue data: " <<  theHeap->Dequeue()->getData() << endl;
    theHeap->printAll(); //this is breadth first, level traversal

    cout<<"dequeue data: " <<  theHeap->Dequeue()->getData() << endl;
    theHeap->printAll(); //this is breadth first, level traversal
*/

    return 0;
}
```