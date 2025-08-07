---
{"dg-publish":true,"permalink":"/c1/solidity/","dgPassFrontmatter":true}
---

[WTF-Solidity](https://github.com/AmazingAng/WTF-Solidity)




# -1.提示词
请你依据此小节Readme，出几道简单的词，方便我做自我检测。检测内容需要包含以下几个方面（可选），可自由发挥，选择我没提到的方面。均以选择题形式出给我。
1.关键概念
2.关键概念的运用条件
3.代码辨析或相关代码题
# 0. MetaMask钱包
![image.png](https://dk-obsidian-1358400187.cos.ap-guangzhou.myqcloud.com/obsidian/202507282007804.png)

# 02.Value Type

**pure和view的区别**

`pure`：函数既不能读取也不能写入链上的状态变量。
`view`：函数能读取但不能写入状态变量。

# 03. Function Output(return / returns)

返回值

命令式返回
- `returns`中定义好返回值
- 同样可以使用`return`返回变量值
解构式返回

# 04.变量数据存储和作用域

数据位置
- `storage`默认状态变量>`memory`函数参数和临时变量，存储内存不上链>`calldata`存储存不上链，且不能修改

# 06.引用类型

数组
- 固定长度数组：`T[K]`
- 可变长度数组：`T[]`
- `bytes`也是数组，不需要加`[]`

数组创建规则
- `memory`修饰的动态数组，使用`new`操作符创建，且必须**声明长度**，声明后长度不变
- 创建数组元素默认最小单位`uint8`,需要对数组进行数组转换，不然传入大整数报错

结构体
- 结构体元素：原始类型，引用类型
- 结构体可以作为数组或映射的元素

# 07.映射类型

映射规则
- 声明映射的格式为`mapping(_KeyType => _ValueType)`
- **规则1**：映射的`_KeyType`只能选择Solidity内置的值类型，比如`uint`，`address`等，不能用自定义的结构体。而`_ValueType`可以使用自定义的类型。
- **规则2**：映射的存储位置必须是`storage`，因此可以用于合约的状态变量，函数中的`storage`变量和library函数的参数（见[例子](https://github.com/ethereum/solidity/issues/4635)）。不能用于`public`函数的参数或返回结果中，因为`mapping`记录的是一种关系 (key - value pair)。
- **规则3**：如果映射声明为`public`，那么Solidity会自动给你创建一个`getter`函数，可以通过`Key`来查询对应的`Value`。
-  **规则4**：给映射新增的键值对的语法为`_Var[_Key] = _Value`，其中`_Var`是映射变量名，`_Key`和`_Value`对应新增的键值对。例子：

映射原理
- **原理1**: 映射不储存任何键（`Key`）的资讯，也没有length的资讯。(减少开支)
- **原理2**: 对于映射使用`keccak256(h(key) . slot)`计算存取value的位置。
- **原理3**: 因为Ethereum会定义所有未使用的空间为0，所以未赋值（`Value`）的键（`Key`）初始值都是各个type的默认值，如uint的默认值是0。

# 08.变量初始值

delete操作符
- `delete a`：会让变量`a`的值变为初始值

# 09.常数

只有数值变量可以声明`constant`和`immutable`；`string`和`bytes`可以声明为`constant`，但不能为`immutable`。

constant(常量)
- `constant`变量必须在声明的时候初始化，之后再也不能改变。尝试改变的话，编译不通过。

immutable(不变量)
- `immutable`变量可以在声明时或构造函数中初始化，因此更加灵活。

# 10.控制流

语法格式
- if-else：`if(条件){执行语句}；`
- for循环：for( ； ；){执行语句};`
- while循环：`while(条件){执行语句}`
- do-while循环： `do{执行语句}while{条件}`
- 三元运算符：`条件? 条件为真的表达式:条件为假的表达式`

# 11.构造函数和修饰器

构造函数
-  语法格式：`constructor(可选参数){初始变量值}

修饰器
- `modifier`的主要使用场景是运行函数前的检查，例如地址，变量，余额等。

# 12.事件

`Solidity`中的事件（`event`）是`EVM`上日志的抽象，它具有两个特点：
- 响应：应用程序（[`ethers.js`](https://learnblockchain.cn/docs/ethers.js/api-contract.html#id18)）可以通过`RPC`接口订阅和监听这些事件，并在前端做响应。
- 经济：事件是`EVM`上比较经济的存储数据的方式，每个大概消耗2,000 `gas`；相比之下，链上存储一个新变量至少需要20,000 `gas`。

声明事件
- `event 事件名称（变量类型 变量名，...)`
- 释放（调用）事件：`emit 事件名称（变量）`

以太坊虚拟机（EVM）用日志`Log`来存储`Solidity`事件，每条日志记录都包含主题`topics`和数据`data`两部分。日志的第一部分是主题数组，用于描述事件，长度不能超过`4`。它的第一个元素是事件的签名（哈希）。除了事件哈希，主题还可以包含至多`3`个`indexed`参数，也就是`Transfer`事件中的`from`和`to`。

事件中不带 `indexed`的参数会被存储在 `data` 部分中，可以理解为事件的“值”。`data` 部分的变量不能被直接检索，但可以存储任意大小的数据。因此一般 `data` 部分可以用来存储复杂的数据结构，例如数组和字符串等等，因为这些数据超过了256比特，即使存储在事件的 `topics` 部分中，也是以哈希的方式存储。另外，`data` 部分的变量在存储上消耗的gas相比于 `topics` 更少。

# 13.继承

这一讲，我们介绍`Solidity`中的继承（`inheritance`），包括简单继承，多重继承，以及修饰器（`Modifier`）和构造函数（`Constructor`）的继承。

 规则
-  `virtual`: 父合约中的函数，如果希望子合约重写，需要加上`virtual`关键字。
-  `override`：子合约重写了父合约中的函数，需要加上`override`关键字。
- **注意**：用`override`修饰`public`变量，会重写与变量同名的`getter`函数

多重继承
1. 继承时要按辈分最高到最低的顺序排。比如我们写一个`Erzi`合约，继承`Yeye`合约和`Baba`合约，那么就要写成`contract Erzi is Yeye, Baba`，而不能写成`contract Erzi is Baba, Yeye`，不然就会报错。
2.  如果某一个函数在多个继承的合约里都存在，比如例子中的`hip()`和`pop()`，在子合约里必须重写，不然会报错。
3.  重写在多个父合约中都重名的函数时，`override`关键字后面要加上所有父合约名字，例如`override(Yeye, Baba)`。

修饰器的继承
- `Solidity`中的修饰器（`Modifier`）同样可以继承，用法与函数继承类似，在相应的地方加`virtual`和`override`关键字即可。

构造函数的继承
- 在继承时声明父构造函数的参数，例如：`contract B is A(1)`
- 在子合约的构造函数中声明构造函数的参数
```
contract C is A {
    constructor(uint _c) A(_c * _c) {}
}
```

调用父合约的函数
- 直接调用：子合约可以直接用`父合约名.函数名()`的方式来调用父合约函数，例如`Yeye.pop()`
- `super`关键字：子合约可以利用`super.函数名()`来调用最近的父合约函数。`Solidity`继承关系按声明时从右到左的顺序是：`contract Erzi is Yeye, Baba`，那么`Baba`是最近的父合约，`super.pop()`将调用`Baba.pop()`而不是`Yeye.pop()`：

# 14.抽象合约和接口

抽象合约
- 如果合约中存在**未实现的函数**，那么应该将合约标记为`abstract`，不然编译会报错。同时需要加上`virtual`，以便子函数重写
- 被标记为`abstract`的合约不能部署

接口规则
1. 不能包含状态变量
2. 不能包含构造函数
3. 不能继承除接口外的其他合约
4. 所有函数都必须是external且不能有函数体
5. 继承接口的非抽象合约必须实现接口定义的所有功能

接口提供的信息
- 合约里每个函数的`bytes4`选择器，以及函数签名`函数名(每个参数类型）`。
- 接口id
- 接口与合约`ABI`（Application Binary Interface）等价，可以相互转换

【代码题】例题：已知Azuki合约中存在approve(address to, uint256 tokenId)函数可以让NFT的拥有者将自己某一NFT的许可权授予另一地址，且该函数没有返回值，现在某个Azuki拥有者想利用上文中创建的接口合约变量调用这一函数 ，那么他写出的代码可能是？
```
//假设已经定义接口
interface Azuki{
	function approve (address to,uint256 tokenId) external;
}

//假设已创建接口合约变量azuki，指向接口
Azuki public azuki;

//调用approval代码
function approveAzuki(address to, uint256 tokenId) external{
	azuki.approve(to,tokenId);
}
```

【代码题】已知Azuki的合约地址为`0xED5AF388653567Af2F388E6224dC7C4b3241C544`，那么利用该地址创建接口合约变量的语句是
```
// 利用合约地址创建接口合约变量.强制类型转换
Azuki public azukiContract = Azuki(0xED5AF388653567Af2F388E6224dC7C4b3241C544);
```

# 15.异常

Error
- 在合约外定义异常`error 异常名称（携带参数`。必须搭配`revert`使用。`revert 异常名称（携带参数)`

Require
- 缺点是gas随着描述异常字符串长度增加而增加。
- `require(检查条件，异常的描述）`，条件不成立抛出异常。可以不写异常的描述

Assert
- `assert(检查条件）`，不成立时抛出异常。

# 16.函数重载

函数重载
- 定义：名字相同，但输入参数类型不同的函数可以同时存在。不允许修饰器`modifier`重载
- 函数重载时，输入的实际参数不能匹配多个函数，否则报错。

# 17.库合约

库合约
1. 不能存在状态变量
2. 不能够继承或被继承
3. 不能接收以太币
4. 不可以被销毁

Strings库合约（Strings library)
- `Strings库合约`是将`uint256`类型转换为相应的`string`类型的代码库

使用库合约
- 我们用`Strings`库合约的`toHexString()`来演示两种使用库合约中函数的办法。
- 利用using for指令指令`using A for B;`可用于附加库合约（从库 A）到任何类型（B）。添加完指令后，库`A`中的函数会自动添加为`B`类型变量的成员，可以直接调用。注意：在调用的时候，这个变量会被当作第一个参数传递给函数：
```
// 利用using for指令
using Strings for uint256;
function getString1(uint256 _number) public pure returns(string memory){
    // 库合约中的函数会自动添加为uint256型变量的成员
    return _number.toHexString();
}
```
- 通过库合约名称调用函数
```
// 直接通过库合约名调用 
function getString2(uint256 _number) public pure returns(string memory){ 
	return Strings.toHexString(_number); }
```

常用的库
1. [Strings](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/4a9cc8b4918ef3736229a5cc5a310bdc17bf759f/contracts/utils/Strings.sol)：将`uint256`转换为`String`
2. [Address](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/4a9cc8b4918ef3736229a5cc5a310bdc17bf759f/contracts/utils/Address.sol)：判断某个地址是否为合约地址
3. [Create2](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/4a9cc8b4918ef3736229a5cc5a310bdc17bf759f/contracts/utils/Create2.sol)：更安全的使用`Create2 EVM opcode`
4. [Arrays](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/4a9cc8b4918ef3736229a5cc5a310bdc17bf759f/contracts/utils/Arrays.sol)：跟数组相关的库合约

# 18.Import

用法
- 作用是导入其他合约中的`全局符号`
- `import '相对路径''`或者`import '绝对路径'；
- 通过npm的目录导入：`import '@openzeppelin/contracts/access/Ownable.sol';`
- 通过指定`全局符号`导入合约特定的全局符号`import {Yeye} from './Yeye.sol';`
- 位置：声明版本号之后，其余代码之前

全局符号
- 导入的全局符号可以单独指定其中的`合约、纯函数、结构体类型`
- 被导入文件的全局符号想要被其他合约单独导入，应该`与合约并列在文件结构中`

# 19.接收ETH

接收ETH函数receive
- 收到ETH时被调用，一个合约最多只有一个`receive`函数
- 不需要`function`关键字，`receive() external payable{ ... }`。不包含参数，不包含返回值，必须有`external payable`

回退函数fallback
- 调用合约不存在的函数时触发，可以用于接收ETH，也可以用于代理合约`proxy contract`
- `fallback()`声明时不需要`function`关键字，必须由`external`修饰，一般也会用`payable`修饰，用于接收ETH:`fallback() external payable { ... }`。

其余
- [!] 二者都不能在合约内部被调用。如果传入的参数为空，则调用`fallback`
- [!] `recive`函数没有专门处理没有附带`msg.data`的以太币转账。如果想要合约既能接收ETH又能处理`msg.data`，就必须要有`fallback`函数

# 20.发送ETH

transfer
-  用法是`接收方地址.transfer(发送ETH数额)`。
- `transfer()`的`gas`限制是`2300`，足够用于转账，但对方合约的`fallback()`或`receive()`函数不能实现太复杂的逻辑。
- `transfer()`如果转账失败，会自动`revert`（回滚交易）。

send
- 用法是`接收方地址.send(发送ETH数额)`。
- `send()`的`gas`限制是`2300`，足够用于转账，但对方合约的`fallback()`或`receive()`函数不能实现太复杂的逻辑。
- `send()`如果转账失败，不会`revert`。
- `send()`的返回值是`bool`，代表着转账成功或失败，需要额外代码处理一下。

call
- 用法是`接收方地址.call{value: 发送ETH数额}("")`。
- `call()`没有`gas`限制，可以支持对方合约`fallback()`或`receive()`函数实现复杂逻辑。
- `call()`如果转账失败，不会`revert`。
- `call()`的返回值是`(bool, bytes)`，其中`bool`代表着转账成功或失败，需要额外代码处理一下。

# 21.调用其他合约

我们可以利用合约的地址和合约代码（或接口）来创建合约的引用：`_Name(_Address)`，其中`_Name`是合约名，应与合约代码（或接口）中标注的合约名保持一致，`_Address`是合约地址。然后用合约的引用来调用它的函数：`_Name(_Address).f()`，其中`f()`是要调用的函数。

调用方法
- 1.传入合约地址，语法格式：`_Name(_Address).f()`
```
function callSetX(address _Address, uint256 x) external{
    OtherContract(_Address).setX(x);
}
```
- 2.传入合约变量，只需要把上面参数的`address`类型改为目标合约名，比如`OtherContract`。复制`OtherContract`合约的地址，填入`callGetX`函数的参数中，调用后成功获取`x`的值
```
function callGetX(OtherContract _Address) external view returns(uint x){
    x = _Address.getX();
}
```
- 3.创建合约变量。
```
function callGetX2(address _Address) external view returns(uint x){
	//oc变量存储为othercontract的引用
    OtherContract oc = OtherContract(_Address);
    x = oc.getX();
}
```
- 4.调用合约并发送ETH。如果目标合约的函数是`payable`的，那么我们可以通过调用它来给合约转账：`_Name(_Address).f{value: _Value}()`，其中`_Name`是合约名，`_Address`是合约地址，`f`是目标函数名，`_Value`是要转的`ETH`数额（以`wei`为单位）。
```
function setXTransferETH(address otherContract, uint256 x) payable external{
    OtherContract(otherContract).setX{value: msg.value}(x);
}
```

# 22.Call

call
- `call` 是`address`类型的低级成员函数，它用来与其他合约交互。它的返回值为`(bool, bytes memory)`，分别对应`call`是否成功以及目标函数的返回值。
- `call`是`Solidity`官方推荐的通过触发`fallback`或`receive`函数发送`ETH`的方法。
- 不推荐用`call`来调用另一个合约，因为当你调用不安全合约的函数时，你就把主动权交给了它。推荐的方法仍是声明合约变量后调用函数，见[第21讲：调用其他合约](https://github.com/AmazingAng/WTF-Solidity/tree/main/21_CallContract)
- 当我们不知道对方合约的源代码或`ABI`，就没法生成合约变量；这时，我们仍可以通过`call`调用对方合约的函数。

call使用规则
- `目标合约地址.call(字节码);`
- 字节码的获取：`abi.encodeWithSignature("函数签名", 逗号分隔的具体参数)`。
  `函数签名`为`"函数名（逗号分隔的参数类型）"`。例如`abi.encodeWithSignature("f(uint256,address)", _x, _addr)`。
- call在调用合约时可以指定交易发送到ETH数额和gas数额
```
目标合约地址.call{value:发送数额, gas:gas数额}(字节码);
```

- [!] 注意，下面写法是错误的,address是类型，不是合约实例。 
```
 address.call{gas: 1000000, value: 1 ether}
```

# 23.Delegatecall

使用call的语境如下
![image.png](https://dk-obsidian-1358400187.cos.ap-guangzhou.myqcloud.com/obsidian/202507290959519.png)

使用delegatecall的语境如下
![image.png](https://dk-obsidian-1358400187.cos.ap-guangzhou.myqcloud.com/obsidian/202507291000352.png)

可以这样理解：一个投资者（用户`A`）把他的资产（`B`合约的`状态变量`）都交给一个风险投资代理（`C`合约）来打理。执行的是风险投资代理的函数，但是改变的是资产的状态。

delegatecall语法
- `目标合约地址.delegatecall(二进制编码);`
- 获取二进制编码：`abi.encodeWithSignature("函数签名", 逗号分隔的具体参数)`
- 调用合约时可以指定gas，但不能指定发送到ETH数额
- [!] A通过B调用C时，`当前合约B`和`目标合约C`的变量名可以不同，但变量类型，声明顺序必须相同。

delegatecall使用场景
1.  代理合约（`Proxy Contract`）：将智能合约的存储合约和逻辑合约分开：代理合约（`Proxy Contract`）存储所有相关的变量，并且保存逻辑合约的地址；所有函数存在逻辑合约（`Logic Contract`）里，通过`delegatecall`执行。当升级时，只需要将代理合约指向新的逻辑合约即可。
2.  EIP-2535 Diamonds（钻石）：钻石是一个支持构建可在生产中扩展的模块化智能合约系统的标准。钻石是具有多个实施合约的代理合约。 更多信息请查看：[钻石标准简介](https://eip2535diamonds.substack.com/p/introduction-to-the-diamond-standard)

- [!] 当用户 A 通过合约 B 来`delegatecall`合约 C 时，执行了==**合约 C**==的函数，语境是==**合约 B 的运行环境**==，`msg.sender`和`msg.value`来自==**用户 A**==（即原始调用者），并且如果函数改变一些状态变量，产生的效果会作用于==**合约 B**==的变量上。
	-  `delegatecall`的核心特点是 “代码借用”：调用者（合约 B）借用被调用者（合约 C）的函数代码，但在自身的上下文（存储、余额等）中执行。
	- 因此，函数逻辑由合约 C 提供，但状态修改会影响合约 B 的存储，而调用的发起者信息（`msg.sender`/`msg.value`）会穿透中间合约 B，直接保留原始用户 A 的信息。

- [!] 在代理合约中，存储相关变量的是==代理合约==，存储函数的是==逻辑合约==，==代理合约`delegatecall`逻辑合约来执行函数==。

# 24.在合约中创建新合约

在以太坊链上，用户（外部账户，`EOA`）可以创建智能合约，智能合约同样也可以创建新的智能合约。去中心化交易所`uniswap`就是利用工厂合约（`PairFactory`）创建了无数个币对合约（`Pair`）。这一讲，我会用简化版的`uniswap`讲如何通过合约创建合约。

create
- 用法：`Contract x = new Contract{value:_value}(params)
- new一个合约，并传入新合约构造函数所需的函数
- 其中`Contract`是要创建的合约名，`x`是合约对象（地址），如果构造函数是`payable`，可以创建时转入`_value`数量的`ETH`，`params`是新合约构造函数的参数。
- [!] 构造合约时的语境是当前合约


# 25.Create2

`CREATE2` 操作码使我们在智能合约部署在以太坊网络之前就能预测合约的地址。

Create如何计算地址
- 创建者的地址(通常为部署的钱包地址或者合约地址)和`nonce`(该地址发送交易的总数,对于合约账户是创建的合约总数,每创建一个合约nonce+1)的哈希。创建者地址不会变，但`nonce`可能会随时间而改变，因此用`CREATE`创建的合约地址不好预测。
```
新地址 = hash(创建者地址, nonce)
```

Create2如何计算地址
- `CREATE2`的目的是为了让合约地址独立于未来的事件。不管未来区块链上发生了什么，你都可以把合约部署在事先计算好的地址上。用`CREATE2`创建的合约地址由4个部分决定：
-  `0xFF`：一个常数，避免和`CREATE`冲突
- `CreatorAddress`: 调用 CREATE2 的当前合约（创建合约）地址。
- `salt`（盐）：一个创建者指定的`bytes32`类型的值，它的主要目的是用来影响新创建的合约的地址。
- `initcode`: 新合约的初始字节码（合约的Creation Code和构造函数的参数）。

```
新地址 = hash("0xFF",创建者地址, salt, initcode)
```

`CREATE2` 确保，如果创建者使用 `CREATE2` 和提供的 `salt` 部署给定的合约`initcode`，它将存储在 `新地址` 中。

Create2使用语法
```
Contract x = new Contract{salt:_salt,value:_value}(params)
```
- 其中`Contract`是要创建的合约名，`x`是合约对象（地址），`_salt`是指定的盐；如果构造函数是`payable`，可以创建时转入`_value`数量的`ETH`，`params`是新合约构造函数的参数。

Salt的使用语法
```
bytes salt = keccak256(abi.encodePacked(token0,token1));
```

- [!] 事先计算pair地址
加密（oxff,地址，salt，哈希）->求加密的哈希->将哈希值转uint->将uint转uint256值->将uin256值作为地址。
```
// 提前计算pair合约地址
function calculateAddr(address tokenA, address tokenB) public view returns(address predictedAddress){
    require(tokenA != tokenB, 'IDENTICAL_ADDRESSES'); //避免tokenA和tokenB相同产生的冲突
    // 计算用tokenA和tokenB地址计算salt
    (address token0, address token1) = tokenA < tokenB ? (tokenA, tokenB) : (tokenB, tokenA); //将tokenA和tokenB按大小排序
    bytes32 salt = keccak256(abi.encodePacked(token0, token1));
    // 计算合约地址方法 hash()
    predictedAddress = address(uint160(uint(keccak256(abi.encodePacked(
        bytes1(0xff),address(this),salt,keccak256(type(Pair).creationCode)
        )))));
}
```

# 26.删除合约

`selfdestruct`命令可以用来删除智能合约，并将该合约剩余`ETH`转到指定地址。然而，在以太坊坎昆（Cancun）升级中，[EIP-6780](https://eips.ethereum.org/EIPS/eip-6780)被纳入升级以实现对`Verkle Tree`更好的持。EIP-6780减少了`SELFDESTRUCT`操作码的功能。根据提案描述，当前`SELFDESTRUCT`仅会被用来将合约中的ETH转移到指定地址，而原先的删除功能只有在`合约创建-自毁`这两个操作处在同一笔交易时才能生效。所以目前来说：

1. 已经部署的合约无法被`SELFDESTRUCT`了。
2. 如果要使用原先的`SELFDESTRUCT`功能，必须在同一笔交易中创建并`SELFDESTRUCT`。

使用`selfdestruct`
- `selfdustruct(_addr);`
- 其中`_addr`是接收合约中剩余`ETH`的地址。`_addr` 地址不需要有`receive()`或`fallback()`也能接收`ETH`。

- [!] 状态变量`value`不等于`ETH`。调用`selfdestruct`后，`ETH`被转移了，但是`value`如果没被修改的话，仍不变。

# 27.ABI编码解码

`ABI` (Application Binary Interface，应用二进制接口)是与以太坊智能合约交互的标准。数据基于他们的类型编码；并且由于编码后不包含类型信息，解码时需要注明它们的类型。

`abi.encode`
- 作用：将每个参数填充为32字节的数据，并拼接在一起，如果要和合约交互，使用。
```
function encode() public view returns(bytes memory result) {
    result = abi.encode(x, addr, name, array);
}
```

`abi.encodePacked`
-  作用：将给定参数根据其所需最低空间编码。它类似 `abi.encode`，但是会把其中填充的很多`0`省略。当你想省空间，并且不与合约交互的时候，可以使用`abi.encodePacked`，例如算一些数据的`hash`时。需要注意，`abi.encodePacked`因为不会做填充，所以不同的输入在拼接后可能会产生相同的编码结果，导致冲突，这也带来了潜在的安全风险。
```
function encodePacked() public view returns(bytes memory result) {
    result = abi.encodePacked(x, addr, name, array);
}
```

`abi.encodeWithSignature`
- 作用：与`abi.encode`功能类似，只不过第一个参数为`函数签名`，比如`"foo(uint256,address,string,uint256[2])"`。当调用其他合约的时候可以使用。
```
function encodeWithSignature() public view returns(bytes memory result) {
result = abi.encodeWithSignature("foo(uint256,address,string,uint256[2])", x, addr, name, array);
}
```

`abi.encodeWithSelector`
- 作用：与`abi.encodeWithSignature`功能类似，只不过第一个参数为`函数选择器`，为`函数签名`Keccak哈希的前4个字节。
```
function encodeWithSelector() public view returns(bytes memory result) {
result = abi.encodeWithSelector(bytes4(keccak256("foo(uint256,address,string,uint256[2])")), x, addr, name, array);
}
```

`abi.decode`
- 作用：`abi.decode`用于解码`abi.encode`生成的二进制编码，将它还原成原本的参数。
```
function decode(bytes memory data) public pure returns(uint dx, address daddr, string memory dname, uint[2] memory darray) {

(dx, daddr, dname, darray) = abi.decode(data, (uint, address, string, uint[2]));
}
```

ABI的使用场景
- 在合约开发中，ABI常配合call来实现对合约的底层调用。
- ethers.js中常用ABI实现合约的导入和函数调用。
- 对不开源合约进行反编译后，某些函数无法查到函数签名，可通过ABI进行调用。

# 28.Hash

Hash性质
- 单向性：从输入的消息到它的哈希的正向运算简单且唯一确定，而反过来非常难，只能靠暴力枚举。
- 灵敏性：输入的消息改变一点对它的哈希改变很大。
- 高效性：从输入的消息到哈希的运算高效。
- 均一性：每个哈希值被取到的概率应该基本相等。
- 抗碰撞性：
    - 弱抗碰撞性：给定一个消息`x`，找到另一个消息`x'`，使得`hash(x) = hash(x')`是困难的。
    - 强抗碰撞性：找到任意`x`和`x'`，使得`hash(x) = hash(x')`是困难的。

Hash用法
- `hash = keccak256(数据);`

# 29.选择器

当我们调用智能合约时，本质上是向目标合约发送了一段`calldata`，在remix中发送一次交易后，可以在详细信息中看见`input`即为此次交易的`calldata`。发送的`calldata`中前4个字节是`selector`（函数选择器）。这一讲，我们将介绍`selector`是什么，以及如何使用。

msg.data
- `msg.data`是`Solidity`中的一个全局变量，值为完整的`calldata`（调用函数时传入的数据）。
- 其实`calldata`就是告诉智能合约，我要调用哪个函数，以及参数是什么。

methon id, selector 和函数签名
- `method id`定义为`函数签名`的`Keccak`哈希后的前4个字节，当`selector`与`method id`相匹配时，即表示调用该函数。
- 函数签名，为`"函数名（逗号分隔的参数类型)"`在同一个智能合约中，不同的函数有不同的函数签名，因此我们可以通过函数签名来确定要调用哪个函数。
- **注意**，在函数签名中，`uint`和`int`要写为`uint256`和`int256`。
- 计算`method id`时，需要通过函数名和函数的参数类型来计算。

各种参数Method id计算
- 基础类型参数：`bytes4(keccak256("elementaryParamSelector(uint256,bool)"));`
- 固定长度类型参数：`bytes4(keccak256("fixedSizeParamSelector(uint256[3])"))`
- 可变长度类型参数：bytes4(keccak256("nonFixedSizeParamSelector(uint256\[],string)"))
- 映射类型参数：`bytes4(keccak256("mappingParamSelector(address,(uint256,bytes),uint256[],uint8)"))`

使用selector调用函数
- 我们可以利用`selector`来调用目标函数。例如我想调用`elementaryParamSelector`函数，我只需要利用`abi.encodeWithSelector`将`elementaryParamSelector`函数的`method id`作为`selector`和参数打包编码，传给`call`函数
```
    // 使用selector来调用函数
    function callWithSignature() external{
	...
        // 调用elementaryParamSelector函数
        (bool success1, bytes memory data1) = address(this).call(abi.encodeWithSelector(0x3ec37834, 1, 0));
	...
    }
```

# 30.Try Catch

try-catch
- `try-catch`只能被用于`external`函数或`public`函数或创建合约时`constructor`（被视为`external`函数）的调用。
- 基本语法
```
try externalContract.f() {
    // call成功的情况下 运行一些代码
} catch {
    // call失败的情况下 运行一些代码
}
```
其中`externalContract.f()`是某个外部合约的函数调用，`try`模块在调用成功的情况下运行，而`catch`模块则在调用失败时运行。
- 同样可以使用`this.f()`来替代`externalContract.f()`，`this.f()`也被视作为外部调用，但不可在构造函数中使用，因为此时合约还未创建。
- 如果调用的函数有返回值，那么必须在`try`之后声明`returns(returnType val)`，并且在`try`模块中可以使用返回的变量；如果是创建合约，那么返回值是新创建的合约变量。
```
try externalContract.f() returns(returnType val){
    // call成功的情况下 运行一些代码
} catch {
    // call失败的情况下 运行一些代码
}
```
- 另外，`catch`模块支持捕获特殊的异常原因：
```
try externalContract.f() returns(returnType){
    // call成功的情况下 运行一些代码
} catch Error(string memory /*reason*/) {
    // 捕获revert("reasonString") 和 require(false, "reasonString")
} catch Panic(uint /*errorCode*/) {
    // 捕获Panic导致的错误 例如assert失败 溢出 除零 数组访问越界
} catch (bytes memory /*lowLevelData*/) {
    // 如果发生了revert且上面2个异常类型匹配都失败了 会进入该分支
    // 例如revert() require(false) revert自定义类型的error
}
```


# 31.ERC20

ERC20
- 是以太坊上的代币标准，实现了代币转账的基本逻辑

IERC20
- `IERC20`是`ERC20`代币标准的接口合约，规定了`ERC20`代币需要实现的函数和事件。

两个事件
```
//释放条件：当 `value` 单位的货币从账户 (`from`) 转账到另一账户 (`to`)时.
event Transfer(address indexed from, address indexed to, uint256 value);

//释放条件：当 `value` 单位的货币从账户 (`owner`) 授权给另一账户 (`spender`)时.
event Approval(address indexed owner, address indexed spender, uint256 value);
```

六个函数
- 账户余额(balanceOf())
- 转账(transfer())
- 授权转账(transferFrom())
- 授权(approve())——==只有先授权了，才能调用`transferFrom()`函数
- 代币总供给(totalSupply())
- 授权转账额度(allowance())
- 代币信息（可选）：名称(name())，代号(symbol())，小数位数(decimals())

# 32. 代币水龙头合约
实现逻辑：
我们将一些ERC20代币转移到水龙头合约中，用户通过合约的requesToken()函数来领取100单位的代币，每个地址只能领取一次。

# 33.Airdrop Contract
# 34.ERC721

EIP721与ERC721
- `EIP`全称 `Ethereum Improvement Proposals`(以太坊改进建议), 是以太坊开发者社区提出的改进建议, 是一系列以编号排定的文件, 类似互联网上IETF的RFC。
- `ERC`全称 Ethereum Request For Comment (以太坊意见征求稿), 用以记录以太坊上应用级的各种开发标准和协议。

ERC165
- 单纯检查一个智能合约是不是支持了`ERC721`,`ERC1155`的接口
```
//检查接口ID代码
interfaceId == type(IERC721).interfaceId
```

IERC721
- `IERC721`是`ERC721`标准的接口合约，规定了`ERC721`要实现的基本函数。它利用`tokenId`来表示特定的非同质化代币，授权或转账都要明确`tokenId`；而`ERC20`只需要明确转账的数额即可。

IERC721事件
- `Transfer`事件：在转账时被释放，记录代币的发出地址`from`，接收地址`to`和`tokenid`。
- `Approval`事件：在授权时释放，记录授权地址`owner`，被授权地址`approved`和`tokenid`。
- `ApprovalForAll`事件：在批量授权时释放，记录批量授权的发出地址`owner`，被授权地址`operator`和授权与否的`approved`。

IERC721函数
- `balanceOf`：返回某地址的NFT持有量`balance`。
- `ownerOf`：返回某`tokenId`的主人`owner`。
- `transferFrom`：普通转账，参数为转出地址`from`，接收地址`to`和`tokenId`。
- `safeTransferFrom`：安全转账（如果接收方是合约地址，会要求实现`ERC721Receiver`接口）。参数为转出地址`from`，接收地址`to`和`tokenId`。
- `approve`：授权另一个地址使用你的NFT。参数为被授权地址`approve`和`tokenId`。
- `getApproved`：查询`tokenId`被批准给了哪个地址。
- `setApprovalForAll`：将自己持有的该系列NFT批量授权给某个地址`operator`。
- `isApprovedForAll`：查询某地址的NFT是否批量授权给了另一个`operator`地址。
- `safeTransferFrom`：安全转账的重载函数，参数里面包含了`data`。

IERC721Receiver接口
- 如果一个合约没有实现`ERC721`的相关函数，转入的`NFT`就进了黑洞，永远转不出来了。为了防止误转账，`ERC721`实现了`safeTransferFrom()`安全转账函数，目标合约必须实现了`IERC721Receiver`接口才能接收`ERC721`代币，不然会`revert`。`IERC721Receiver`接口只包含一个`onERC721Received()`函数。

IERC721Metadata接口
- `IERC721Metadata`是`ERC721`的拓展接口，实现了3个查询`metadata`元数据的常用函数：
- `name()`：返回代币名称。
- `symbol()`：返回代币代号。
- `tokenURI()`：通过`tokenId`查询`metadata`的链接`url`，`ERC721`特有的函数。

# 35.荷兰拍卖

# 36. Merkle Tree

Merkle Tree
- `Merkle Tree`是一种自下而上构建的加密树，每个叶子是对应数据的哈希，而每个非叶子为它的`2`个子节点的哈希。
- 对于有`N`个叶子节点的`Merkle Tree`，在已知`root`根值的情况下，验证某个数据是否有效（属于`Merkle Tree`叶子节点）只需要`ceil(log₂N)`个数据（也叫`proof`）

MerkleProof库
1. `verify()`函数：利用`proof`数来验证`leaf`是否属于根为`root`的`Merkle Tree`中，如果是，则返回`true`。它调用了`processProof()`函数。
2.  `processProof()`函数：利用`proof`和`leaf`依次计算出`Merkle Tree`的`root`。它调用了`_hashPair()`函数。
3.  `_hashPair()`函数：用`keccak256()`函数计算非根节点对应的两个子节点的哈希（排序后）。