```java
public class LRUCache {
	class Node{
		int key;
		int value;
		Node(int key,int value){
			this.key=key;
			this.value=value;
		}
		Node(){
		}
		Node pre;
		Node post;
	}
	void addNode_head(Node node){
		node.post=head.post;
		node.pre=head;
		head.post.pre=node;
		head.post=node;
	}
	void deleteNode(Node node){
		node.pre.post=node.post;
		node.post.pre=node.pre;
	}
    int capacity;
    int count;
    Map<Integer,Node> map;
    Node head,tail;
    public LRUCache(int capacity) {
    	this.capacity=capacity;
    	this.count=0;
        map=new HashMap<Integer,Node>();
        head = new Node();
    	tail = new Node();
    	head.pre = null;
    	tail.post = null; 	
    	head.post = tail;
    	tail.pre = head;
    }
    public int get(int key) {
    	Node node=map.get(key);
        if(node==null){
        	return -1;
        }
        deleteNode(node);
        addNode_head(node);
        return node.value;
    }
    public void set(int key, int value) {
    	Node node=map.get(key);
        if(node==null){
        	Node tmp=new Node(key,value);
        	map.put(key, tmp);
        	if(count==capacity){
        		map.remove(tail.pre.key);
        		deleteNode(tail.pre);
        		count--;
        	}
        	addNode_head(tmp);
        	count++;
        }else{
        	node.value=value;
        	deleteNode(node);
        	addNode_head(node);
        }
    }
}
```

