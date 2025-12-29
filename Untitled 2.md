```c++
#include <iostream>

using namespace std;

/**
 * ===================================================================================
 * Class: Node
 * ===================================================================================
 * Description:
 * This class represents the fundamental building block of the Tree.
 * In a Binary Tree, every node carries a payload (data) and acts as a junction point
 * connecting to two other nodes (Left and Right).
 */
class Node {
public:
    int data;       // The actual integer value stored in this node.
    Node * left;    // Pointer to the left child node (contains values < current data).
    Node * right;   // Pointer to the right child node (contains values > current data).

    /**
     * Constructor: Node
     * -----------------
     * Initializes a new node with a specific value.
     * Crucially, it sets child pointers to NULL to avoid "Dangling Pointers"
     * (pointers pointing to random garbage memory), which causes crashes.
     */
    Node(int data) {
        this->data = data;
        left = NULL;    // Safety: Initialize as a leaf node (no children).
        right = NULL;
    }

    ~Node() {
        // Destructor: Logic for memory cleanup would go here.
    }
};

/**
 * ===================================================================================
 * Class: Tree
 * ===================================================================================
 * Description:
 * This class manages the logic of the Binary Search Tree (BST).
 * It holds the entry point (Root) and provides methods to manipulate the structure.
 */
class Tree {
    Node * root; // The absolute beginning of the tree. If this is NULL, the tree is empty.

public:
    // Constructor: Starts the tree in an empty state.
    Tree() {
        root = NULL;
    }

    /**
     * Method: add
     * -----------
     * Purpose: Inserts a new integer into the BST while maintaining the sorting order.
     * Logic:
     * 1. Creates a new node in the Heap memory.
     * 2. If the tree is empty, the new node becomes the Root.
     * 3. If not empty, it traverses down to find the correct empty spot (NULL).
     * * Strategy: Trailing Pointer
     * We use two pointers:
     * - 'current': Runs ahead to find the destination (becomes NULL at the end).
     * - 'parent': Stays one step behind 'current' to hold the node we need to attach to.
     */
    void add(int data) {
        // Step 1: Memory Allocation
        // Request memory from Heap for the new block.
        Node * newNode = new Node(data);

        // Case 1: The Tree is Empty (First insertion)
        if (root == NULL) {
            root = newNode; // The new node is now the foundation of the tree.
        }
        // Case 2: The Tree is populated (Need to traverse)
        else {
            Node * current = root; // Start traversal from the top.
            Node * parent = NULL;  // Will eventually hold the parent of the new node.

            // Loop: Drill down the tree until we hit a dead end (NULL).
            while (current != NULL) {
                parent = current; // Snapshot: Remember this node before we move away.

                // Decision: Go Left or Right?
                // BST Rule: Smaller goes Left, Larger goes Right.
                if (data > current->data) {
                    current = current->right; // Target position is to the Right.
                } else {
                    current = current->left;  // Target position is to the Left.
                }
            }
            // End of Loop: 'current' is now NULL, but 'parent' is the valid leaf node.

            // Step 2: Linking Phase
            // We now attach the 'newNode' to the correct side of 'parent'.
            if (data > parent->data) {
                parent->right = newNode; // Attach to the Right side.
            } else {
                parent->left = newNode;  // Attach to the Left side.
            }
        }
    }

    /**
     * Method: checkDataInTree
     * -----------------------
     * Purpose: Boolean wrapper to check for existence of a value.
     */
    bool checkDataInTree(int data) {
        // Re-uses the search logic defined in 'getNodeByData'.
        // If it returns a valid address (not NULL), the data exists.
        if (getNodeByData(data) != NULL) {
            return true;
        }
        return false;
    }

    /**
     * Method: getParentMain
     * ---------------------
     * Purpose: Public interface to find the value of a node's parent.
     * Returns:
     * - value: If parent is found.
     * - -1   : If the node is the Root (Roots have no parents).
     * - -5   : If the target node itself doesn't exist in the tree.
     */
    int getParentMain(int data) {
        // 1. Verification: Does the node even exist?
        Node * node = getNodeByData(data);
        
        if (node != NULL) {
            // 2. Logic: Find the parent of this specific node pointer.
            Node * parent = getParent(node);
            
            if (parent != NULL) {
                return parent->data; // Success: Return parent's payload.
            } else {
                return -1; // Failure: Node exists but has no parent (It is Root).
            }
        } else {
            return -5; // Error: The data provided is not in the tree.
        }
    }

    /**
     * Method: getMaxRightMain
     * -----------------------
     * Purpose: Returns the value of the right-most node in the subtree starting at 'data'.
     */
    int getMaxRightMain(int data) {
        Node * node = getNodeByData(data); // Locate the starting node.
        
        if (node != NULL) {
            Node * maxRight = getMaxRight(node); // Traverse to the edge.
            return maxRight->data;
        }
        return -1; // Error code if node not found.
    }

private:
    /**
     * ===============================================================================
     * Private Helper Functions (Internal Logic)
     * ===============================================================================
     */

    /**
     * Method: getMaxRight
     * -------------------
     * Purpose: Finds the maximum value in a subtree.
     * Logic: In a BST, the maximum value is always the right-most node.
     * We keep moving 'right' until the next pointer is NULL.
     */
    Node * getMaxRight(Node * current) {
        if (current == NULL) {
            return NULL; // Safety check for empty inputs.
        }
        // Keep moving right as long as a right child exists.
        while (current->right != NULL) {
            current = current->right;
        }
        return current; // This is the right-most node.
    }

    /**
     * Method: getParent
     * -----------------
     * Purpose: Finds the parent node of a given child pointer.
     * Constraint: Nodes do not store 'parent' pointers, so we must search from Root.
     * Complexity: O(H) where H is the height of the tree.
     */
    Node * getParent(Node * childNode) {
        // Edge Case: The Root has no parent above it.
        if (childNode == root) {
            return NULL;
        }
        
        if (childNode != NULL) {
            Node * parent = root; // Start search from the top.
            
            // Loop: Traverse down to find the connection point.
            while (parent != NULL) {
                // Check immediate children:
                // "Is the node I am looking for directly below me?"
                if (parent->left == childNode || parent->right == childNode) {
                    return parent; // Found it!
                } 
                
                // Navigation Logic:
                // If not found yet, decide which way to go down (Left or Right)
                // based on BST rules (Value Comparison).
                if (childNode->data > parent->data) {
                    parent = parent->right;
                } else {
                    parent = parent->left;
                }
            }
        }
        return NULL; // Should logically not be reached if childNode exists in tree.
    }

    /**
     * Method: getNodeByData
     * ---------------------
     * Purpose: Standard Iterative Binary Search.
     * logic:
     * - Compare target with current node.
     * - Equal? Return current.
     * - Larger? Go Right.
     * - Smaller? Go Left.
     */
    Node * getNodeByData(int data) {
        Node * current = root;
        
        while (current != NULL) {
            if (data == current->data) {
                return current; // Target found.
            } else if (data > current->data) {
                current = current->right; // Target is in the right subtree.
            } else {
                current = current->left;  // Target is in the left subtree.
            }
        }
        return NULL; // Traversed entire path and reached NULL (Not Found).
    }
};

// ============================
// Main Execution Context
// ============================
int main() {
    Tree t;
    
    // Constructing the Tree structure
    // Adding nodes in this specific order creates the tree shape.
    t.add(50); // Root
    t.add(40);
    t.add(30);
    t.add(60);
    t.add(70);
    t.add(65);
    t.add(25);

    // ---------------------------------------------------------
    // Testing Search Functionality
    // ---------------------------------------------------------
    // Using ternary operator for compact printing: (Condition) ? True_Block : False_Block
    t.checkDataInTree(50) != false ? cout << "Found" << endl : cout << "Not Found" << endl;
    t.checkDataInTree(45) != false ? cout << "Found" << endl : cout << "Not Found" << endl;
    t.checkDataInTree(65) != false ? cout << "Found" << endl : cout << "Not Found" << endl;

    // ---------------------------------------------------------
    // Testing Parent Logic
    // ---------------------------------------------------------
    cout << "\n--- Parent Testing ---" << endl;
    
    // Case: Node 21 does not exist
    int parent = t.getParentMain(21); 
    parent == -1 ? cout << "Root" << endl : parent == -5 ? cout << "<<Not Found>>" << endl : cout << "Parent = " << parent << endl;

    // Case: Node 50 is Root (Expect "Root")
    parent = t.getParentMain(50);
    parent == -1 ? cout << "Root" << endl : parent == -5 ? cout << "<<Not Found>>" << endl : cout << "Parent = " << parent << endl;

    // Case: Node 65 (Parent should be 70)
    parent = t.getParentMain(65);
    parent == -1 ? cout << "Root" << endl : parent == -5 ? cout << "<<Not Found>>" << endl : cout << "Parent = " << parent << endl;

    // ---------------------------------------------------------
    // Testing Max Right Logic
    // ---------------------------------------------------------
    cout << "\n--- Max Right Testing ---" << endl;
    cout << "Max Right of subtree 40 : " << t.getMaxRightMain(40) << endl;

    cout << "Hello world!" << endl;
    return 0;
}
```