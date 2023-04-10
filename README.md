##### map数据结构
**散列表**（**Hash table**，也叫**哈希表**），是根据[键](https://zh.wikipedia.org/wiki/%E9%8D%B5)（关键字）而直接进行访问的数据结构。它采用了函数映射的思想将记录的存储位置与记录的关键字关联起来。这个映射函数称做[散列函数](https://zh.wikipedia.org/wiki/%E6%95%A3%E5%88%97%E5%87%BD%E6%95%B0)。GO中的哈希表的实现是map

map是一堆键值对的未排序集合,底层的数据结构：hmap
```

// A header for a Go map.
type hmap struct {
	// Note: the format of the hmap is also encoded in cmd/compile/internal/gc/reflect.go.
	// Make sure this stays in sync with the compiler's definition.
	count     int // # live cells == size of map.  Must be first (used by len() builtin)
	flags     uint8
	B         uint8  // log_2 of # of buckets (can hold up to loadFactor * 2^B items)
	noverflow uint16 // approximate number of overflow buckets; see incrnoverflow for details
	hash0     uint32 // hash seed
	buckets    unsafe.Pointer // array of 2^B Buckets. may be nil if count==0.
	oldbuckets unsafe.Pointer // previous bucket array of half the size, non-nil only when growing
	nevacuate  uintptr        // progress counter for evacuation (buckets less than this have been evacuated)
	extra *mapextra // optional fields
}


type mapextra struct {
	overflow    *[]*bmap
	oldoverflow *[]*bmap
	nextOverflow *bmap
}


```

- count: 元素个数，调用 len(map) 时，直接返回此值
- B：can hold up to （ loadFactor * 2^B items ）  最大可容纳元素数量
- buckets：指向一段连续的内存，保存当前桶数据。
- hash0：哈希因子
- flag: 标示状态
- oldbuckets：只有在扩容的时候非nil，旧桶的指针地址
- noverflow：溢出桶的数量
- extra：随着键值对数量的增加，溢出桶的数量和哈希的装载因子也会逐渐升高，超过一定范围就会触发扩容。这时候extra就派上用处。
##### Map动态扩容
map 类型的容量不受初始容量值限制

map 类型的容量不会受限于它的初始容量值，当其中的键值对数量超过初始容量后，Go 运行时会自动增加 map 类型的容量，保证后续键值对的正常插入。
