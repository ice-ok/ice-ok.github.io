# lua防坑指南


#### 前言
这是几年前总结lua坑的一篇东西，对lua新人会比较有用。

#### 个人总结
- **计数的起点是1，而非0**，在遍历时一定要记得这一点；
- **#运算符**查找1开始的连续元素个数。如果id不连续，那么#结果、遍历操作可能不符合预期；
- **#运算符**的结果与table.maxn的结果不一定一致；此外，table.maxn在5.2中被deprecated，不要用它；
- 数组中添加/删除元素，**请使用table.insert(...)，table.remove(...)；请明白直接赋值nil的后果**；
- **#运算符**可作用于任何**metatable有__len域**的对象；
- 模块编写，5.1.5/5.2/5.3都**推荐使用reutrn module_table**的方式，**不要**再使用module/package...；
- 使用**local修饰所有变量或函数，避免污染全局名字空间**；如果出现全局对象(未用local修饰)，请确定是你所期望的；
- lua中类似3元运算符的用法：**a and b or c，注意：前提是b为真**；
- lua中的与(and)/或(or)**返回的是原对象，而非boolean值，同时支持短路求值(提前退出)**，如：a or b or c，如果a为真，那么b/c不会参与计算，返回a对象；
- lua中**取默认值**的方式：a = a or {...}；
- **不同类型进行==比较，结果必定为假**，如：3 == "3"；而导出函数中，lua_tonumber会自动进行类型转换，如果**类型敏感**需要先判断类型；
- lua_tostring/lua_tonumber会**自动进行类型转换**，这可能导致lua_next出错；
- lua赋值给c整型时，如果是非unsigned，必须result = (unsigned)lua_tointegor(L, idx)，否则可能与预期不一致，原因是**超过result上限但在unsigned范围内的double数值赋值给result，和unsigined直接赋值给result表现并不一样(编译器相关)**。譬如：lua中，c.member = 4194967296，member在lua中是int类型；
- lua仅**保证其栈上有LUA_MINSTACK(20)个空间**，如果需要用到超过该数值的栈空间，**必须使用lua_checkstack扩展栈空间**，否则崩溃；
- lua的堆栈平衡：
   - **导出给lua用的函数**，lua在调用函数前后会自动进行堆栈平衡；**失败返回false可以用return 0代替**；
   - **c中进行lua栈操作，且非直接导出给lua调用的**，必须保证调用前后堆栈平衡：或者调用前后栈顶一致(失败往往如此处理), 或者仅多了一个插入的元素(调用该函数的目的)。成功/失败都必须符合预期，否则可能导致堆栈错乱，或者不断增长。
- **自动类型转换，以运算符前面的值类型为准**：a = 1 + '1'; b = '1' .. a。另，tostring()函数可作用于任何metatable有_ _tostring域的对象；
- **lua中仅false和nil是假，其他一律真**，包括0、''、{}。这里强调一点：**调用导出对象的函数，其返回值类型是bool或int判断成功与否方式是不一样的**；
- for循环体内**不要修改控制变量的值**，该值会要参与下一元素遍历的计算；
- 循环体内**不要删除元素**，会导致不可预期的问题。**可以记录id，再反序删除**(减少元素id变化)；
- **lua中字符串可以包含'\0'**;换句话说，**lua中的字符串实际上就是一块内存**，而lua_tolstring能拿到这块内存的起始地址与长度，lua中#运算符能拿到其长度；
- **浮点数比较不能直接用==，特别是*/计算后的结果，应该比较差异绝对值**。虽然lua中只有一种数值类型，**但并非所有相等判断都需要当作浮点数来进行**；
- lua5.1中metatable的__gc事件只有full userdata会触发。lua5.2中table也会触发。5.1中想要监控table的gc，可以通过newproxy方法完成，该方法在5.2中删除；
- 对于userdata，其仅能在c中lua_setmetatable，lua层的setmetatable仅对table有效，**对light/fulluserdata无效**；
- fulluserdata是lua分配的一块内存，其生命周期有lua管理；lightuserdata仅保存一个指针，指针所指对象的生命周期由c进行管理；二者都通过lua_touserdata取得指针；
- math.random([m [, n]])：
   - 无参数时，[0, 1)
   - 1个参数时(仅支持整数，5.3下不是整数则出错)，[1, m]
   - 2个参数时(仅支持整数，5.3下不是整数则出错)，[m, n]
- 代码执行之前，请先使用luac编译脚本为字节码，以进行语法检查；可配置到编辑器中，快捷执行；

#### 他人总结
[这篇blog](http://www.cnblogs.com/walfud/articles/2594700.html)也提到几个点，大家可以注意下(详细内容见blog内):
- for exp1, exp2, exp3 do <执行体> end 中, 三个表达式都是一次性求值.
- for 中的控制变量会被自动声明为局部变量, 并且仅在循环体内可见.
- 函数可以返回多个值, 只有函数作为表达式的最后一个值, 才能保留全部的返回值
补充，[这里](http://lilydjwg.is-programmer.com/2013/1/4/multiple-return-value-another-caveat-in-lua.36934.html)提到相应容易出错的场景：
```lua
--代码一
local t = {}
local item = myfunc()
table.insert(t, item)
item = nil

--代码二
local t = {}
table.insert(t, myfunc())

--行为可能不一致，请确定符合预期。
```

- 定义递归函数, 需要先声明, 后定义, 一定要分开写
- loadstring 所加载的程序块是在全局作用于下执行的, 因此任何 local 变量都不能被 loadstring 的程序块所见

