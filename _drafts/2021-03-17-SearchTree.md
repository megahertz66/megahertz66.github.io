

**学习树一定要有递归的思想，因为递归是处理树ADT很好的方法**

性质：  
 对于每个树的每个节点 X ，它的左子树的关键值 X 小于它的右子树的关键值。而右子树的所有关键值大于左子树的关键值。  
 正常情况下的时间复杂度为 O（logn）。

缺点：  
 在特殊情况下就会退化成为 O（n）。如图：  

![查找树的特殊情况](/picture/searchtree_1.jpg)  

## 树结构

和普通的树一样，查找二叉树只是组织树的方式不同而已。

```c

struct TreeNode{
    ElementType Element;
    struct TreeNode left;
    struct TreeNode right;
}

```


## 操作

### Insert

```c

struct TreeNode* insert(Element x, struct TreeNode *node)
{
    if(NULL == node){
        node = malloc(sizeof(struct Treenode));
        node->Element = x;
        node->left = NULL;
        node->left = NULL;
    }
    else if(x < node->Element){
        node->left = insert(x, node->left);
    }
    else if(x > node->Element){
        node->right = insert(x, node->right);
    }

    return node;
}

```


### MakeEmpty

### Find

```c
struct TreeNode *find(Element x, struct TreeNode *root)
{
    if(NULL == root) return NULL;
    if( x < root->left )
        find(x, root->left);
    else if( x > root->right )
        find(x, root->right);
    else
        return root;
}
```
#### FindMin
```c
struct TreeNode *find_min(struct TreeNode *root)
{
    if(root != NULL || root->left != NULL){
        while(root->left != NULL)
            root = root->left;
    }
    return root;
}
```
#### FindMIX
```c
struct TreeNode* find_max(struct TreeNode *node)
{
    if(node == NULL)
        return NULL;
    else if(node->right == NULL)
        return node;
    else 
        find_max(node->right);
}
```

### Dlete

1. 如果是叶子节点，直接删除；
2. 




```c

struct TreeNode* delete(ELementType x, struct TreeNode *node)
{
    struct TreeNode* tmp_node;

    if(node == NULL)
        ERROR("Element not found");
    else if(x < node->Element)
        node->left = delete(x, node->left);
    else if(x > node->Element)
        node->right = delete(x, node->right);
    /* Two children */
    else if(node->right != NULL && node->right != NULL){
        tmp_node = find_min(node->right);
        node->Element = tmp_node->Element;
        node->right = delete(node->Element, node->right);
    }
    /*one or no children*/
    
}
```