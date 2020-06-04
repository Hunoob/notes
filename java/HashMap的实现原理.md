## HashMap的实现原理**

### **1.    HashMap概述**

​    HashMap是基于哈希表的Map接口的非同步实现。此实现提供所有可选的映射操作，并允许使用null值和null键。此类不保证映射的顺序，特别是它不保证该顺序恒久不变。

​    在java编程语言中，最基本的结构就是两种，一个是数组，另外一个是模拟指针（引用），所有的数据结构都可以用这两个基本结构来构造的，HashMap也不例外。HashMap实际上是一个“链表散列”的数据结构，即数组和链表的结合体。

![img](data:image/jpeg;base64,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)

从上图中可以看出，HashMap底层就是一个数组结构，数组中的每一项又是一个链表。当新建一个HashMap的时候，就会初始化一个数组。

其中Java源码如下：

![img](data:image/gif;base64,R0lGODlhFAAUANQHADJKYpKluc3P05qy4jJdta/M92KQ3vv8/nKi4rK/0rLC6mJ6mtHe7sHN4ZW19iJLmUp2wqKy0nKa8iJCgsLV9uLq8qKywnKa2oKy4lKC0qLC8qKqwnKS0oKn7cDI1AAAACH5BAEAAB8ALAAAAAAUABQAAAWE4CeOZGmeaKquLKkoSRwJrXLch7XQa4IfAc+ud6AUGIHCS+NwME6WX6Xw6xAaps2BiUFofpICwVMK3CoVxuDQZBgIhEf50EVc1rgKJSInmRkMDQ0dBx0IBhkZfGUAjY0cOAwUChZ9KQYHiIkZG5YoEAcRohYbAZ4nEHAPq6sTLa+wsSUhADs=)

/**

\* The table, resized as necessary. Length MUST Always be a power of two.

*/

transient Entry[] table; 



static class Entry<K,V> implements Map.Entry<K,V> { 

​    final K key; 

​    V value;

​    Entry<K,V> next; 

​    final int hash; 

​    ……

}

![img](data:image/gif;base64,R0lGODlhFAAUANQHADJKYpKluc3P05qy4jJdta/M92KQ3vv8/nKi4rK/0rLC6mJ6mtHe7sHN4ZW19iJLmUp2wqKy0nKa8iJCgsLV9uLq8qKywnKa2oKy4lKC0qLC8qKqwnKS0oKn7cDI1AAAACH5BAEAAB8ALAAAAAAUABQAAAWE4CeOZGmeaKquLKkoSRwJrXLch7XQa4IfAc+ud6AUGIHCS+NwME6WX6Xw6xAaps2BiUFofpICwVMK3CoVxuDQZBgIhEf50EVc1rgKJSInmRkMDQ0dBx0IBhkZfGUAjY0cOAwUChZ9KQYHiIkZG5YoEAcRohYbAZ4nEHAPq6sTLa+wsSUhADs=)

可以看出，Entry就是数组中的元素，每个 Map.Entry 其实就是一个key-value对，它持有一个指向下一个元素的引用，这就构成了链表。

### **2、HashMap实现存储和读取**

1）存储

![img](data:image/gif;base64,R0lGODlhFAAUANQHADJKYpKluc3P05qy4jJdta/M92KQ3vv8/nKi4rK/0rLC6mJ6mtHe7sHN4ZW19iJLmUp2wqKy0nKa8iJCgsLV9uLq8qKywnKa2oKy4lKC0qLC8qKqwnKS0oKn7cDI1AAAACH5BAEAAB8ALAAAAAAUABQAAAWE4CeOZGmeaKquLKkoSRwJrXLch7XQa4IfAc+ud6AUGIHCS+NwME6WX6Xw6xAaps2BiUFofpICwVMK3CoVxuDQZBgIhEf50EVc1rgKJSInmRkMDQ0dBx0IBhkZfGUAjY0cOAwUChZ9KQYHiIkZG5YoEAcRohYbAZ4nEHAPq6sTLa+wsSUhADs=)

1 public V put(K key, V value) { 

2     // HashMap允许存放null键和null值。 

3     // 当key为null时，调用putForNullKey方法，将value放置在数组第一个位置。 

4     if (key == null) 

5         return putForNullKey(value); 

6     // 根据key的keyCode重新计算hash值。 

7     int hash = hash(key.hashCode()); 

8     // 搜索指定hash值在对应table中的索引。 

9     int i = indexFor(hash, table.length); 

10     // 如果 i 索引处的 Entry 不为 null，通过循环不断遍历 e 元素的下一个元素。 

11     for (Entry<K,V> e = table[i]; e != null; e = e.next) { 

12         Object k; 

13         if (e.hash == hash && ((k = e.key) == key || key.equals(k))) { 

14             // 如果发现已有该键值，则存储新的值，并返回原始值 

15             V oldValue = e.value; 

16             e.value = value; 

17             e.recordAccess(this);

18             return oldValue; 

19         } 

20     } 

21     // 如果i索引处的Entry为null，表明此处还没有Entry。 

22     modCount++;

23     // 将key、value添加到i索引处。 

24     addEntry(hash, key, value, i); 

25     return null; 

26 } 

![img](data:image/gif;base64,R0lGODlhFAAUANQHADJKYpKluc3P05qy4jJdta/M92KQ3vv8/nKi4rK/0rLC6mJ6mtHe7sHN4ZW19iJLmUp2wqKy0nKa8iJCgsLV9uLq8qKywnKa2oKy4lKC0qLC8qKqwnKS0oKn7cDI1AAAACH5BAEAAB8ALAAAAAAUABQAAAWE4CeOZGmeaKquLKkoSRwJrXLch7XQa4IfAc+ud6AUGIHCS+NwME6WX6Xw6xAaps2BiUFofpICwVMK3CoVxuDQZBgIhEf50EVc1rgKJSInmRkMDQ0dBx0IBhkZfGUAjY0cOAwUChZ9KQYHiIkZG5YoEAcRohYbAZ4nEHAPq6sTLa+wsSUhADs=)

根据hash值得到这个元素在数组中的位置（即下标），如果数组该位置上已经存放有其他元素了，那么在这个位置上的元素将以链表的形式存放，新加入的放在链头，最先加入的放在链尾。如果数组该位置上没有元素，就直接将该元素放到此数组中的该位置上。

hash(int h)方法根据key的hashCode重新计算一次散列。此算法加入了高位计算，防止低位不变，高位变化时，造成的hash冲突。

1 static int hash(int h) { 

2     h ^= (h >>> 20) ^ (h >>> 12);

3     return h ^ (h >>> 7) ^ (h >>> 4); 

4 } 

我们可以看到在HashMap中要找到某个元素，需要根据key的hash值来求得对应数组中的位置。如何计算这个位置就是hash算法。前面说过HashMap的数据结构是数组和链表的结合，所以我们当然希望这个HashMap里面的元素位置尽量的分布均匀些，尽量使得每个位置上的元素数量只有一个，那么当我们用hash算法求得这个位置的时候，马上就可以知道对应位置的元素就是我们要的，而不用再去遍历链表，这样就大大优化了查询的效率。

根据上面 put 方法的源代码可以看出，当程序试图将一个key-value对放入HashMap中时，程序首先根据该 key的 hashCode() 返回值决定该 Entry 的存储位置：如果两个 Entry 的 key 的 hashCode() 返回值相同，那它们的存储位置相同。如果这两个 Entry 的 key 通过 equals 比较返回 true，新添加 Entry 的 value 将覆盖集合中原有 Entry的 value，但key不会覆盖。如果这两个 Entry 的 key 通过 equals 比较返回 false，新添加的 Entry 将与集合中原有 Entry 形成 Entry 链，而且新添加的 Entry 位于 Entry 链的头部——具体说明继续看 addEntry() 方法的说明。

**通过这种方式就可以高效的解决HashMap的冲突问题。**

2）读取

![img](data:image/gif;base64,R0lGODlhFAAUANQHADJKYpKluc3P05qy4jJdta/M92KQ3vv8/nKi4rK/0rLC6mJ6mtHe7sHN4ZW19iJLmUp2wqKy0nKa8iJCgsLV9uLq8qKywnKa2oKy4lKC0qLC8qKqwnKS0oKn7cDI1AAAACH5BAEAAB8ALAAAAAAUABQAAAWE4CeOZGmeaKquLKkoSRwJrXLch7XQa4IfAc+ud6AUGIHCS+NwME6WX6Xw6xAaps2BiUFofpICwVMK3CoVxuDQZBgIhEf50EVc1rgKJSInmRkMDQ0dBx0IBhkZfGUAjY0cOAwUChZ9KQYHiIkZG5YoEAcRohYbAZ4nEHAPq6sTLa+wsSUhADs=)

1 public V get(Object key) { 

2     if (key == null) 

3         return getForNullKey(); 

4     int hash = hash(key.hashCode()); 

5     for (Entry<K,V> e = table[indexFor(hash, table.length)]; 

6         e != null; 

7         e = e.next) { 

8         Object k; 

9         if (e.hash == hash && ((k = e.key) == key || key.equals(k))) 

10             return e.value; 

11     } 

12     return null; 

13 } 

![img](data:image/gif;base64,R0lGODlhFAAUANQHADJKYpKluc3P05qy4jJdta/M92KQ3vv8/nKi4rK/0rLC6mJ6mtHe7sHN4ZW19iJLmUp2wqKy0nKa8iJCgsLV9uLq8qKywnKa2oKy4lKC0qLC8qKqwnKS0oKn7cDI1AAAACH5BAEAAB8ALAAAAAAUABQAAAWE4CeOZGmeaKquLKkoSRwJrXLch7XQa4IfAc+ud6AUGIHCS+NwME6WX6Xw6xAaps2BiUFofpICwVMK3CoVxuDQZBgIhEf50EVc1rgKJSInmRkMDQ0dBx0IBhkZfGUAjY0cOAwUChZ9KQYHiIkZG5YoEAcRohYbAZ4nEHAPq6sTLa+wsSUhADs=)

从HashMap中get元素时，首先计算key的hashCode，找到数组中对应位置的某一元素，然后通过key的equals方法在对应位置的链表中找到需要的元素。

3）归纳起来简单地说，HashMap 在底层将 key-value 当成一个整体进行处理，这个整体就是一个 Entry 对象。HashMap 底层采用一个 Entry[] 数组来保存所有的 key-value 对，当需要存储一个 Entry 对象时，会根据hash算法来决定其在数组中的存储位置，在根据equals方法决定其在该数组位置上的链表中的存储位置；当需要取出一个Entry时，也会根据hash算法找到其在数组中的存储位置，再根据equals方法从该位置上的链表中取出该Entry。

### **3、HashMap的resize**

​       当hashmap中的元素越来越多的时候，碰撞的几率也就越来越高（因为数组的长度是固定的），所以为了提高查询的效率，就要对hashmap的数组进行扩容，数组扩容这个操作也会出现在ArrayList中，所以这是一个通用的操作，很多人对它的性能表示过怀疑，不过想想我们的“均摊”原理，就释然了，而在hashmap数组扩容之后，最消耗性能的点就出现了：原数组中的数据必须重新计算其在新数组中的位置，并放进去，这就是resize。

​       那么hashmap什么时候进行扩容呢？当hashmap中的元素个数超过数组大小*loadFactor时，就会进行数组扩容，loadFactor的默认值为0.75，也就是说，默认情况下，数组大小为16，那么当hashmap中元素个数超过16*0.75=12的时候，就把数组的大小扩展为2*16=32，即扩大一倍，然后重新计算每个元素在数组中的位置，而这是一个非常消耗性能的操作，所以如果我们已经预知hashmap中元素的个数，那么预设元素的个数能够有效的提高hashmap的性能。比如说，我们有1000个元素new HashMap(1000), 但是理论上来讲new HashMap(1024)更合适，不过上面annegu已经说过，即使是1000，hashmap也自动会将其设置为1024。 但是new HashMap(1024)还不是更合适的，因为0.75*1000 < 1000, 也就是说为了让0.75 * size > 1000, 我们必须这样new HashMap(2048)才最合适，既考虑了&的问题，也避免了resize的问题。

 

**总结：HashMap的实现原理：**

1. **利用key的hashCode重新hash计算出当前对象的元素在数组中的下标**
2. **存储时，如果出现hash值相同的key，此时有两种情况。(1)如果key相同，则覆盖原始值；(2)如果key不同（出现冲突），则将当前的key-value放入链表中**
3. **获取时，直接找到hash值对应的下标，在进一步判断key是否相同，从而找到对应值。**
4. **理解了以上过程就不难明白HashMap是如何解决hash冲突的问题，核心就是使用了数组的存储方式，然后将冲突的key的对象放入链表中，一旦发现冲突就在链表中做进一步的对比。**

 

class HashMap<K,V> extends AbstractMap<K,V>

- HashMap  put()
- HashMap  get()

**1.put()**

　　HashMap put()方法源码如下：

![img](data:image/gif;base64,R0lGODlhFAAUANQHADJKYpKluc3P05qy4jJdta/M92KQ3vv8/nKi4rK/0rLC6mJ6mtHe7sHN4ZW19iJLmUp2wqKy0nKa8iJCgsLV9uLq8qKywnKa2oKy4lKC0qLC8qKqwnKS0oKn7cDI1AAAACH5BAEAAB8ALAAAAAAUABQAAAWE4CeOZGmeaKquLKkoSRwJrXLch7XQa4IfAc+ud6AUGIHCS+NwME6WX6Xw6xAaps2BiUFofpICwVMK3CoVxuDQZBgIhEf50EVc1rgKJSInmRkMDQ0dBx0IBhkZfGUAjY0cOAwUChZ9KQYHiIkZG5YoEAcRohYbAZ4nEHAPq6sTLa+wsSUhADs=)

public V put(K key, V value) {  

​        if (key == null)  

​            return putForNullKey(value);  

​        int hash = hash(key.hashCode());  

​        int i = indexFor(hash, table.length);  

​        for (Entry<K,V> e = table[i]; e != null; e = e.next) {  

​            Object k; 

​            //判断当前确定的索引位置是否存在相同hashcode和相同key的元素，如果存在相同的hashcode和相同的key的元素，那么新值覆盖原来的旧值，并返回旧值。  

​            //如果存在相同的hashcode，那么他们确定的索引位置就相同，这时判断他们的key是否相同，如果不相同，这时就是产生了hash冲突。  

​            //Hash冲突后，那么HashMap的单个bucket里存储的不是一个 Entry，而是一个 Entry 链。  

​            //系统只能必须按顺序遍历每个 Entry，直到找到想搜索的 Entry 为止——如果恰好要搜索的 Entry 位于该 Entry 链的最末端（该 Entry 是最早放入该 bucket 中），  

​            //那系统必须循环到最后才能找到该元素。  

​            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {  

​                V oldValue = e.value;  

​                e.value = value;  

​                return oldValue;  

​            } 

​        } 

​        modCount++; 

​        addEntry(hash, key, value, i); 

​        return null;  

​    } 

![img](data:image/gif;base64,R0lGODlhFAAUANQHADJKYpKluc3P05qy4jJdta/M92KQ3vv8/nKi4rK/0rLC6mJ6mtHe7sHN4ZW19iJLmUp2wqKy0nKa8iJCgsLV9uLq8qKywnKa2oKy4lKC0qLC8qKqwnKS0oKn7cDI1AAAACH5BAEAAB8ALAAAAAAUABQAAAWE4CeOZGmeaKquLKkoSRwJrXLch7XQa4IfAc+ud6AUGIHCS+NwME6WX6Xw6xAaps2BiUFofpICwVMK3CoVxuDQZBgIhEf50EVc1rgKJSInmRkMDQ0dBx0IBhkZfGUAjY0cOAwUChZ9KQYHiIkZG5YoEAcRohYbAZ4nEHAPq6sTLa+wsSUhADs=)

　　hash值冲突是发生在put()时，从源码可以看出，hash值是通过hash(key.hashCode())来获取的，当put的元素越来越多时，难免或出现不同的key产生相同的hash值问题，也即是hash冲突，当拿到一个hash值，通过indexFor(hash, table.length)获取数组下标，先查询是否存在该hash值，若不存在，则直接以Entry<V,V>的方式存放在数组中，若存在，则再对比key是否相同,若hash值和key都相同，则替换value，若hash值相同，key不相同，则形成一个单链表，将hash值相同，key不同的元素以Entry<V,V>的方式存放在链表中，这样就解决了hash冲突，这种方法叫做**分离链表法**，与之类似的方法还有一种叫做 **开放定址法**，开放定址法师采用线性探测（从相同hash值开始，继续寻找下一个可用的槽位）hashMap是数组，长度虽然可以扩大，但用线性探测法去查询槽位查不到时怎么办？因此hashMap采用了分离链表法。

**2.get()**

　　

![img](data:image/gif;base64,R0lGODlhFAAUANQHADJKYpKluc3P05qy4jJdta/M92KQ3vv8/nKi4rK/0rLC6mJ6mtHe7sHN4ZW19iJLmUp2wqKy0nKa8iJCgsLV9uLq8qKywnKa2oKy4lKC0qLC8qKqwnKS0oKn7cDI1AAAACH5BAEAAB8ALAAAAAAUABQAAAWE4CeOZGmeaKquLKkoSRwJrXLch7XQa4IfAc+ud6AUGIHCS+NwME6WX6Xw6xAaps2BiUFofpICwVMK3CoVxuDQZBgIhEf50EVc1rgKJSInmRkMDQ0dBx0IBhkZfGUAjY0cOAwUChZ9KQYHiIkZG5YoEAcRohYbAZ4nEHAPq6sTLa+wsSUhADs=)

​    public V get(Object key) {   

​       if (key == null)   

​           return getForNullKey();   

​       int hash = hash(key.hashCode());   

​       for (Entry<K,V> e = table[indexFor(hash, table.length)];   

​           e != null;   

​           e = e.next) {   

​           Object k;  

​           if (e.hash == hash && ((k = e.key) == key || key.equals(k)))   

​                return e.value;   

​        }  

​        return null;   

​    }

![img](data:image/gif;base64,R0lGODlhFAAUANQHADJKYpKluc3P05qy4jJdta/M92KQ3vv8/nKi4rK/0rLC6mJ6mtHe7sHN4ZW19iJLmUp2wqKy0nKa8iJCgsLV9uLq8qKywnKa2oKy4lKC0qLC8qKqwnKS0oKn7cDI1AAAACH5BAEAAB8ALAAAAAAUABQAAAWE4CeOZGmeaKquLKkoSRwJrXLch7XQa4IfAc+ud6AUGIHCS+NwME6WX6Xw6xAaps2BiUFofpICwVMK3CoVxuDQZBgIhEf50EVc1rgKJSInmRkMDQ0dBx0IBhkZfGUAjY0cOAwUChZ9KQYHiIkZG5YoEAcRohYbAZ4nEHAPq6sTLa+wsSUhADs=)

　　有了上面存储时的hash算法作为基础，理解起来这段代码就很容易了。从上面的源代码中可以看出：从HashMap中get元素时，首先计算key的hashCode，找到数组中对应位置的某一元素，然后通过key的equals方法在对应位置的链表中找到需要的元素。

　　当hashMap没出现hash冲突时，没有形成单向链表，get方法能够直接定位到元素，但是，出现冲突后，形成了单向链表，bucket里存放的不再是一个entry对象，而是一个entry对象链，系统只能顺序的遍历每个entry直到找到想要搜索的entry为止，这时，问题就来了，如果恰好要搜索的entry位于该entry链的最末端，那循环必须要进行到最后一步才能找到元素，此时涉及到一个**负载因子**的概念，hashMap默认的负载因子为0.75，这是考虑到**存储空间**和**查询时间**上成本的一个折中值，增大负载因子，可以减少hash表（就是那个entry数组）所占用的内空间，但会增加查询数据的时间开销，而查询是最频繁的操作（put()和get()都用到查询）；减小负载因子，会提高查询时间，但会增加hash表所占的内存空间。

　　结合负载因子的定义公式可知，threshold就是在此loadFactor和capacity对应下允许的最大元素数目，超过这个数目就重新resize，以降低实际的负载因子。默认的的负载因子0.75是对空间和时间效率的一个平衡选择。当容量超出此最大容量时， resize后的HashMap容量是容量的两倍：

**3.hashMap数组扩容**

　　当HashMap中的元素越来越多的时候，hash冲突的几率也就越来越高，因为数组的长度是固定的。所以为了提高查询的效率，就要对HashMap的数组进行扩容，数组扩容这个操作也会出现在ArrayList中，这是一个常用的操作，而在HashMap数组扩容之后，最消耗性能的点就出现了：原数组中的数据必须重新计算其在新数组中的位置，并放进去，这就是resize。

 　　那么HashMap什么时候进行扩容呢？当HashMap中的元素个数超过数组大小*loadFactor时，就会进行数组扩容，loadFactor的默认值为0.75，这是一个折中的取值。也就是说，默认情况下，数组大小为16，那么当HashMap中元素个数超过16*0.75=12的时候，就把数组的大小扩展为 2*16=32，即扩大一倍，然后重新计算每个元素在数组中的位置，扩容是需要进行数组复制的，复制数组是非常消耗性能的操作，所以如果我们已经预知HashMap中元素的个数，那么预设元素的个数能够有效的提高HashMap的性能。



这里的Hash算法本质上就是三步：**取key的hashCode值、高位运算、取模运算**。

对于任意给定的对象，只要它的hashCode()返回值相同，那么程序调用方法一所计算得到的Hash码值总是相同的。我们首先想到的就是把hash值对数组长度取模运算，这样一来，元素的分布相对来说是比较均匀的。但是，模运算的消耗还是比较大的，在HashMap中是这样做的：调用方法二来计算该对象应该保存在table数组的哪个索引处。

这个方法非常巧妙，它通过h & (table.length -1)来得到该对象的保存位，而HashMap底层数组的长度总是2的n次方，这是HashMap在速度上的优化。当length总是2的n次方时，h& (length-1)运算等价于对length取模，也就是h%length，但是&比%具有更高的效率。

在JDK1.8的实现中，优化了高位运算的算法，通过hashCode()的高16位异或低16位实现的：(h = k.hashCode()) ^ (h >>> 16)，主要是从速度、功效、质量来考虑的，这么做可以在数组table的length比较小的时候，也能保证考虑到高低Bit都参与到Hash的计算中，同时不会有太大的开销。

有关负载因子的概念 参考：[https://www](https://www/).[cnblogs.com/yesiamhere/p/6653135.html](http://cnblogs.com/yesiamhere/p/6653135.html)