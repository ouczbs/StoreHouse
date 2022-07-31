#### 编译
- C3859
	- 导致这个问题的原因是 预分配 头内存不足 可以通过 -Zm 多分配一些
	-<PCHMemoryAllocationFactor>100</PCHMemoryAllocationFactor>
	
- C1076
	- 
- C1060
- C1455
	- 手动设置此盘符下的虚拟内存页面
```
c1xx : error C3859: Failed to create virtual memory for PCH

// 编译器现在可以根据需要使堆动态增大，只要求分配给预编译头的内存的大小固定不变。只有在极少数涉及非常大或非常复杂的程序的情况下，才会导致超出预编译头的堆大小限制。如果程序超过这些限制，请使用 /Zm 调整所有限制的总大小。

c1xx : fatal error C1076: compiler limit: internal heap limit reached



c1xx: note: the system returned code 1455: 页面文件太小，无法完成操作。

fatal error C1060: compiler is out of heap space



```