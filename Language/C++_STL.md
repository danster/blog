##C++ STL 

#### Resources Link
* 三十分钟掌握STL http://net.pku.edu.cn/~yhf/UsingSTL.htm
* STL 简明教程 http://lotabout.me/orgwiki/stl.html
* [STL 源码剖析PDF](http://scaukcg-download.stor.sinaapp.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/2/STL%E6%BA%90%E7%A0%81%E5%89%96%E6%9E%90.pdf) 
* [C++中的new、operator new与placement new](http://www.cnblogs.com/luxiaoxun/archive/2012/08/10/2631812.html)
* [内存动态分配函数malloc的基本实现原理](http://blog.csdn.net/ojshilu/article/details/17001165) brk() mmap()
* [malloc()工作机制](http://blog.csdn.net/llhyy17/article/details/5375298)

#### Category  
C++ 标准模板库的核心包括以下三个组件：  

- Containers	容器是用来管理某一类对象的集合。C++ 提供了 deque、list、vector、map 等。
- Algorithms	算法作用于容器。它们提供了执行各种操作的方式，包括对容器内容执行初始化、排序、搜索和转换等操作。
- Iterators 迭代器用于遍历对象集合的元素。这些集合可能是容器，也可能是容器的子集。

在C++标准中，STL被组织为下面的13个头文件：  
```
Algorithms: <algorithm>,<numeric>,<functional> 
Containers: <vector>,<list>,<deque>,<set>,<map>,<stack>,<queue> 
Iterators:  <iterator>,<memory>,<utility> 
```

迭代器在STL中用来将算法和容器联系起来，起着一种黏和剂的作用。几乎STL提供的所有算法都是通过迭代器存取元素序列进行工作的，每一个容器都定义了其本身所专有的迭代器，用以存取容器中的元素。

```
<algorithm>是所有STL头文件中最大的一个（尽管它很好理解），它是由一大堆模版函数组成的，可以认为每个函数在很大程度上都是独立的，
          其中常用到的功能范围涉及到比较、交换、查找、遍历操作、复制、修改、移除、反转、排序、合并等等。
<numeric>体积很小，只包括几个在序列上面进行简单数学运算的模板函数，包括加法和乘法在序列上的一些操作。
<functional>中则定义了一些模板类，用以声明函数对象。

向量(vector) 连续存储的元素<vector>
列表(list)   由节点组成的双向链表，每个结点包含着一个元素<list>
双队列(deque) 连续存储的指向不同元素的指针所组成的数组<deque>
集合(set) 由节点组成的红黑树，每个节点都包含着一个元素，节点之间以某种作用于元素对的谓词排列，没有两个不同元素的次序相同 <set>
多重集合(multiset) 允许存在两个次序相等的元素的集合 <set>
栈(stack) 后进先出的值的排列 <stack>
队列(queue) 先进先出的执的排列 <queue>
优先队列(priority_queue) 元素的次序是由作用于所存储的值对上的某种谓词决定的的一种队列 <queue>
映射(map) 由{键，值}对组成的集合，以某种作用于键对上的谓词排列 <map>
多重映射(multimap) 允许键对有相等的次序的映射 <map>

<utility>是一个很小的头文件，它包括了贯穿使用在STL中的几个模板的声明，
<iterator>中提供了迭代器使用的许多方法
<memory> 以不同寻常的方式为容器中的元素分配存储空间，同时也为某些算法执行期间产生的临时对象提供机制
<memory> 中的主要部分是模板类allocator，它负责产生所有容器中的默认分配器。
```

**序列式容器Sequence Containers**: Array(built-in), Vector(heap, priority-queue), List, Slist, Deque(stack, queue)   
**关联式容器Associative Containers**: RB-Tree(set,map,multiset,multimap), Hashtable(hash\_set,hash\_map,hash\_multiset,hash\_multimap)

#### Vector
> Vector可动态扩展，Array不行。  
> Vector 的实作技术，关键在于其对大小的控制以及重新配置时的数据搬移效率。  
> 扩展中会遇到“重新配置，复制数据，释放”的轮回  

```C++
// alloc 是SGI STL 的空间配置器
template <class T, class Alloc = alloc>
class vector {
public:
typedef T value_type;
typedef value_type* pointer;
typedef value_type* iterator;
typedef value_type& reference;
typedef size_t size_type;
typedef ptrdiff_t difference_type;

protected:
typedef simple_alloc<value_type, Alloc> data_allocator;
iterator start;  // 表示目前使用空间的头
iterator finish; // 表示目前使用空间的尾
iterator end_of_storage; // 表示目前可用空间的尾
void insert_aux(iterator position, const T& x);
void deallocate() {
    if (start)
    data_allocator::deallocate (start, end_of_storage - start);
}
void fill_initialize(size_type n, const T& value) {
    start = allocate_and_fill(n, value);
    finish = start + n;
    end_of_storage = finish;
}

public:
iterator begin()       { return start; }
iterator end()         { return finish; }
size_type size() const { return size_type(end() - begin()); }
size_type capacity() const { return size_type(end_of_storage - begin()); }
bool empty() const         { return begin() == end(); }
reference operator[](size_type n) { return *(begin() + n); }

vector() : start(0), finish(0), end_of_storage(0) {}
vector(size_type n, const T& value) { fill_initialize(n, value); }
vector(int n, const T& value) { fill_initialize(n, value); }
vector(long n, const T& value) { fill_initialize(n, value); }
explicit vector(size_type n) { fill_initialize(n, T()); }

~vector(){
          destroy (start, finish);
          deallocate();
}
reference front() { return *begin(); }    //第一个元素
reference back() { return *(end() - 1); } //第二个元素
void push_back(const T& x) {              //将元素安插至最尾端
    if (finish != end_of_storage) {
        construct (finish, x);
        ++finish;
    }else
        insert_aux (end(), x);
}
void pop_back() { // 将最尾端元素取出
    --finish;
    destroy(finish);
}
iterator erase(iterator position) { //清除某位置上的元素
    if (position + 1 != end())
        copy(position + 1, finish, position);
    --finish;
    destroy(finish); 
    return position;
}
void resize(size_type new_size, const T& x) {
    if (new_size < size())
        erase(begin() + new_size, end());
    else
        insert(end(), new_size - size(), x);
}
void resize(size_type new_size) { resize(new_size, T()); }
void clear() { erase(begin(), end()); }

protected:
iterator allocate_and_fill(size_type n, const T& x) { // 配置空间并填满内容
    iterator result = data_allocator::allocate(n);
    uninitialized_fill_n(result, n, x); 
    return result;
}
}
```
**heap** 完全二叉树   
heap所有元素都遵循特别的排列规则, 不提供遍历功能, 也不提供iterator.  
```C++
// STL中没有heap类，只是算法函数
void push_heap(RandomAccessIterator first, RandomAccessIterator last); // 先插入end, 再上溯
void pop_heap(RandomAccessIterator first, RandomAccessIterator last);  // 调换end与head, 再下溯
void sort_heap(RandomAccessIterator first, RandomAccessIterator last); // 每次执行pop(length), 再调换end与head，再pop(length-1)
void make_heap(RandomAccessIterator first, RandomAccessIterator last); // 将现有数据转换成一个heap
```
**priority_queue**   
默认基于vector实现  
只有queue顶端的元素可以被取用, 不提供遍历功能，也不提供iterator.   
```C++
template <class T, class Sequence=vector<T>, class Compare=less<typename Sequence::value_type>>
class priority_queue{
public: // 接口
  bool empty();
  size_type size();
  void push(const value_type& x);
  void pop();
  const value_type& top();
}
```
#### List
双链表，没有容量观念  

#### Deque
> deque由一段一段的定量连续空间构成，deque的最大任务就是维护整体连续假象并提供随机存取的接口  
> deque采用map(一块连续空间)作为主控，其中map上每一个node(指针)都指向另一端较大的连续线性空间，称为缓冲区(default 512 B)  
> 这些缓冲区才是deque的存储空间主体  

**stack**  不提供遍历功能，也不提供iterator.    
**queue**   不提供遍历功能，也不提供iterator.   

#### RB-Tree
红黑树：平衡二叉查找树  
```C++
template <class Key, class Value, class KeyOfValue, class Compare, class Alloc=alloc>
class rb_tree {
protected:
  typedef __rb_tree_node<Value> node;
  typedef simple_alloc<node, Alloc> node_allocator;

public:
  typedef Key key_type;
  typedef Value value_type
  typedef node* link_type;
  typedef size_t size_type;
  ...
  
  link_type get_node(){ return node_allocator::allocate(); }  // 分配内存
  void put_node(link_type p){ node_allocator::deallocate(p);} // 释放内存
  link_type create_node(const value_type& x){ 
          link_type tmp=get_node(); 
          constract(&tmp->value_field, x); 
          return tmp;
  }
  link_type clone_node(link_type x); // 复制一个节点的值和色
  void destroy_node(link_type x){ destroy(&p->value_field); put_node(p);}

protected:
  size_type node_count; // 树大小，节点数量
  link_type header;
  Compare key_compare;
  
  link_type& root() const;
  link_type& leftmost() const;
  link_type& rightmost() const;
 
private:
  __insert; __copy; void __erase(link_type x);
  void init(){ header = get_node();  color(header)=read; root() = 0;  leftmost()=header;  rightmost()=header;}
  
public: // 接口
  typedef __rb_tree_iterator<value_type, value_type&, value_type*> iterator;
  rb_tree(const Compare& comp = Compare()) : node_count(0), key_compare(comp){ init();}
  ~ rb_tree(){ clear(); put_node(header); }
  rb_tree<Key, Value, KeyOfValue, Compare,  Alloc>& operator=(const rb_tree<Key, Value, KeyOfValue, Compare,  Alloc>& x);
  
  Compare key_comp() const { return key_compare; }
  iterator begin() {return leftmost();}
  iterator end() {return rightmost();}
  bool empty() const { return node_count==0;}
  size_type size() const { return node_count;}
  size_type max_size() const { return size_type(-1);}
  
  pair<iterator, bool> insert_unique(const value_type& obj);
  iterator insert_equal(const value_type& obj);
  iterator find(const key_type& k);
}
```
**set**  
```C++
template <class Key, class Compare = less<Key>, class Alloc = alloc>
class set{
public:
  typedef Key key_value;        typedef Key value_value;
  typedef Compare key_compare;  typedef Compare value_compare; 

private: // 底层采用rb_tree实现
  typedef rb_tree<Key, Key, identity<Key>, key_compare, Alloc> rep_tree;
  rep_tree t;
  
public: // 接口
  set():t(Compare()) {}
  set(InputIterator f, InputIterator l):t(Compare()) {t.insert_uniqe(f, l)}
  set(InputIterator f, InputIterator l, const Compare& comp):t(comp) {t.insert_uniqe(f, l)}
  set(const set<Key, Compare, Alloc>& x)：t(x.t){} // copy constructor
  set<Key, Compare, Alloc> oprator= (const set<Key, Compare, Alloc>& x)：t(x.t){ t=x.t; return *this;}

  key_compare key_comp() const{ return t.key_comp(); }
  value_compare value_comp() const{ return t.key_comp(); }
  iterator begin() const{ return t.begin(); }
  iterator end() const{ return t.end(); }
  bool empty() const{ return t.empty(); }
  size_type size() const{ return t.size(); }
  size_type max_size() const{ return t.max_size(); }
  void swap(set<Key, Compare, Alloc>& x) { t.swap(x.t);}
  
  pair<iterator, bool> insert(const value_type& obj);
  iterator insert(iterator pos, const value_type& obj);
  void insert(InputIterator f, InputIterator l);
  void erase(iterator pos);
  size_type erase(const key_type& x)
  void erase(InputIterator f, InputIterator l);
  void clear();
  
  iterator find(const key_type& x) const;
  size_type count(const key_type& x) const;
  iterator lower_bound(const key_type& x) const;
  iterator upper_bound(const key_type& x) const;
  pair<iterator, bool> equal_range(const key_type& x) const;
  ...
}
```
**map**  
**multiset**  
**multimap**  

#### Hashtable
散列函数(hash function)带来的碰撞问题：不同元素可能被映射到相同的位置。  
STL的实现是用开链法解决“碰撞”问题(其他方法有线性探测、二次探测等)。  

```C++
template <class Value, class Key, class HashFcn, class ExtractKey, class EqualKey, class Alloc=alloc>
class hashtable {
public:
  typedef HashFcn hasher;
  typedef EqualKey key_equal;
  typedef size_t size_type;
  
private:
  typedef __hashtable_node<Value> node;
  typedef simple_alloc<node, Alloc> node_allocator;
  
  hasher hash;  
  key_equal equals; 
  ExtractKey get_key;
  
  vector<node*, Alloc> buckets;
  size_type num_elements;
  
public: // 接口
  size_type bucket_count() const { return buckets.size(); }
  void clear();
  void copy_from(const hashtable& h);
  iterator find(const ke_type& key);
  size_type count(const ke_type& key) const; // 查询含有key值的元素个数
  node* new_node(const value_type& obj){ ... }
  void delete_node(node* n){ destory(&n->value); node_allocator::deallocate(n); }
  pair<iterator, bool> insert_unique(const value_type& obj){ resize(num_elements+1); insert_unique_noresize(obj); }
  pair<iterator, bool> insert_equal(const value_type& obj){  resize(num_elements+1); insert_equal_noresize(obj); }
  
private：
  size_type next_size(size_type n) const { return 最接近并大于n的事先定义好的质数(共有28个) }
  size_type max_bucket_count() const { return 最接近并大于n的事先定义好的质数(共有28个) }
  
  void resize(size_type num_elements_hint); //判断是否需要重建表格，不需要就返回，需要就动手
  pair<iterator, bool> insert_unique_noresize(const value_type& obj);
  pair<iterator, bool> insert_equal_noresize(const value_type& obj);
  
  // bkt_num 判知元素的落脚处
  size_type bkt_num(const value_type& obj, size_t n) const { return bkt_num_key(get_key(obj), n);}
  size_type bkt_num(const value_type& obj) const { return bkt_num_key(get_key(obj));}
  size_type bkt_num_key(const value_type& obj) const { return bkt_num_key(get_key(obj), buckets.size());}
  size_type bkt_num_key(const value_type& obj, size_t n) const { return hash(key)/n;}
}
```

**hash_set**  
**hash_map**  
**hash_multiset**  
**hash_multimap**  

#### Allocator

SGI STL allocator 未能符合标准规格，这个事实通常不会对我们带来困扰，因为通常我们使用预设的空间配置器，很少需要自行指定配置器名称，而SGI STL 的每个容器都已经指定其预设的空间配置器为alloc。例如下面的vector 宣告：  
```C++
template <class T, class Alloc = alloc> // 预设使用 alloc 为配置器
class vector { ... };
```
建构和解构基本工具：construct() 和 destroy()  
<stl_construct.h>  
```C++ 
#include <new.h> // 欲使用 placement new，需先含入此檔
template <class T1, class T2>
inline void construct(T1* p, const T2& value) {
    new (p) T1(value); // placement new; 唤起T1::T1(value);
}
template <class T>
inline void destroy(T* pointer) {   pointer->~T(); }
template <class ForwardIterator>
inline void destroy(ForwardIterator first, ForwardIterator last) { __destroy(first, last, value_type(first));}

// 判断元素的数值型别（value type）是否有trivial destructor
template <class ForwardIterator, class T>
inline void __destroy(ForwardIterator first, ForwardIterator last, T*){
    typedef typename __type_traits<T>::has_trivial_destructor trivial_destructor;
    __destroy_aux(first, last, trivial_destructor());
}
// 如果元素的数值型别（value type）有non-trivial destructor…
template <class ForwardIterator>
inline void __destroy_aux(ForwardIterator first, ForwardIterator last, __false_type) {
    for ( ; first < last; ++first)
    destroy(&*first);
}
// 如果元素的数值型别（value type）有trivial destructor…
template <class ForwardIterator>
inline void __destroy_aux(ForwardIterator, ForwardIterator, __true_type ) {}
// 以下是destroy() 第二版本针对迭代器为char* 和wchar_t* 的特化版
inline void destroy(char*, char*) {}
inline void destroy(wchar_t*, wchar_t*) {}
```

对象建构前的空间配置，和对象解构后的空间释放，由\<stl\_alloc.h\>负责，SGI对此的设计哲学如下：  
```
向system heap 要求空间。
考虑多绪（multi-threads）状态。
考虑内存不足时的应变措施。
考虑过多「小型区块」可能造成的内存破碎（fragment）问题。
```

> C++ 的记忆体配置基本动作是::operator new() ， 记忆体释放基本动作是::operator delete()。
> 这两个全域函式相当于C 的malloc() 和free() 函式

> 考虑小型区块所可能造成的内存破碎问题，SGI 设计了双层级配置器，第一级配置器直接使用malloc() 和free()，第二级配置器则视情况采用不同的策略：当配置区块超过128bytes，视之为「足够大」，便呼叫第一级配置器；当配置区块小于128bytes，视之为「过小」，为了降低额外负担（overhead，见2.2.6 节），便采用复杂的memory pool 整理方式，而不再求助于第一级配置器。

无论 alloc 被定义为第一级或第二级配置器，SGI 还为它再包装一个接口如下，使配置器的接口能够符合STL 规格：    
```C++
template<class T, class Alloc>
class simple_alloc {
public:
  static T *allocate(size_t n){ return 0 == n? 0 : (T*) Alloc::allocate (n * sizeof (T)); }
  static T *allocate(void){ return (T*) Alloc::allocate(sizeof (T)); }
  static void deallocate(T *p, size_t n){ if (0 != n) Alloc::deallocate (p, n * sizeof (T)); }
  static void deallocate(T *p){ Alloc::deallocate(p, sizeof (T)); }
};
```
其内部四个成员函式其实都是单纯的转呼叫，呼叫传入之配置器（可能是第一级也可能是第二级）的成员函式。这个接口使配置器的配置单位从bytes 转为个别元素的大小（sizeof(T)），SGI STL 容器全都使用这个simple_alloc 接口。

#### malloc(), free()

```C++
// 我们的简单分配程序的全局变量
int has_initialized = 0;
void *managed_memory_start;
void *last_valid_address;

void malloc_init(){
  last_valid_address = sbrk(0); // grab the last valid address from the OS
  // we don''t have any memory to manage yet, so just set the beginning to be last_valid_address
  managed_memory_start = last_valid_address;
  has_initialized = 1; // Okay, we''re initialized and ready to go
}

// 主分配程序
void *malloc(long numbytes) {
    void *current_location;
    struct mem_control_block *current_location_mcb;
    void *memory_location;
    if(! has_initialized) {
        malloc_init();
    }
    
    numbytes = numbytes + sizeof(struct mem_control_block);
    memory_location = 0;
    current_location = managed_memory_start;
    while(current_location != last_valid_address){
        current_location_mcb = (struct mem_control_block *)current_location;
        if(current_location_mcb->is_available)
        {
            if(current_location_mcb->size >= numbytes)
            {
                current_location_mcb->is_available = 0;
                memory_location = current_location;
                break;
            }
        }

        current_location = current_location + current_location_mcb->size;
    }
    
    if(! memory_location)
    {
        sbrk(numbytes);
        memory_location = last_valid_address;
        last_valid_address = last_valid_address + numbytes;
        current_location_mcb = memory_location;
        current_location_mcb->is_available = 0;
        current_location_mcb->size = numbytes;
    }
    
    memory_location = memory_location + sizeof(struct mem_control_block);
    return memory_location;
}
```

```C++
void free(void *ptr) { 
  struct mem_control_block *free; 
  free = ptr - sizeof(struct mem_control_block); 
  free->is_available = 1; 
  return; 
}
```
