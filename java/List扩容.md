# List扩容

 private void grow(int minCapacity) {

​        // overflow-conscious code

​        int oldCapacity = elementData.length;

​        //>>位运算，右移动一位。 整体相当于newCapacity =oldCapacity + 0.5 * oldCapacity  

​        // jdk1.7采用位运算比以前的计算方式更快

​        int newCapacity = oldCapacity + (oldCapacity >> 1);

​        if (newCapacity - minCapacity < 0)

​            newCapacity = minCapacity;

​       //jdk1.7这里增加了对元素个数的最大个数判断,jdk1.7以前是没有最大值判断的，MAX_ARRAY_SIZE 为int最大值减去8（不清楚为什么用这个值做比较）

​        if (newCapacity - MAX_ARRAY_SIZE > 0)

​            newCapacity = hugeCapacity(minCapacity);

​        // 最重要的复制元素方法

​        elementData = Arrays.copyOf(elementData, newCapacity);

​    }

综上所述，ArrayList相当于在没指定initialCapacity时就是会使用延迟分配对象数组空间，当第一次插入元素时才分配10（默认）个对象空间。假如有20个数据需要添加，那么会分别在第一次的时候，将ArrayList的容量变为10 ；之后扩容会按照1.5倍增长。也就是当添加第11个数据的时候，Arraylist继续扩容变为10*1.5=15；当添加第16个数据时，继续扩容变为15 * 1.5 =22个。

​      每次扩容都是通过Arrays.copyOf(elementData, newCapacity) 这样的方式实现的。