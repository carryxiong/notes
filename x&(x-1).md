#x&(x-1)

	每执行一次x = x&(x-1)，会将x用二进制表示时最右边的一个1变为0，因为x-1将会将该位(x用二进制表示时最右边的一个1)变为0。
而如果是2的次方的话，jdk中经常用&来表示取模运算，因为2的n次方，减去一个1，就会成为后面全是1，前面为0的情况，再&一下就会将前面所有的1去掉，相当于取模运算了。