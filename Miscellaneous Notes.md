左结合性

```c++
while (a < b < c)//由左结合性等价(a<b)<c;a<b=1,当c<=1时候不能输出
	{
		printf("a=%d,b=%d,c=%d\n",a,b,c);
		c--;
	}
```

## **二、define中的三个特殊符号：#，##，#@**

 

1. \#define Conn(x,y) x##y
2. \#define ToChar(x) #@x
3. \#define ToString(x) #x

### **(1)x##y表示什么？表示x连接y**

举例说：

1. int n = Conn(123,456); /* 结果就是n=123456;*/
2. char* str = Conn("asdf", "adf"); /*结果就是 str = "asdfadf";*/

 

### **（2）再来看**[**#@x**](mailto:#@x)**，其实就是给x加上\**单引号\**，结果返回是一个\**const char\**。**

举例说：

char a = ToChar(1);结果就是a='1';
做个越界试验char a = ToChar(123);结果就错了;
但是如果你的参数超过四个字符，编译器就给给你报错了！

error C2015: too many characters in constant  ：P

 

### **(3）最后看看#x,估计你也明白了，他是给\**x加双引号\****

char* str = ToString(123132);就成了str="123132";



逗号表达式是将括号中所有表达式的值算出来，但是只使用最后一个表达式。在此题中，第一个表达式算出了a的值，使用最后一个表达式的值就是75啦，将这个值赋给b，结果就是75

```c
int a, b;
b = (a = 3*5, a*4, a*5);
printf("%d",b);
```

```c
#define PI 3.1415926    //chang
typedef struct s* tPS;   //类型替换
```

