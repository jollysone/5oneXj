# 区块链技术面试题


# 1 以太坊常见问题

{{&lt; admonition success &#34;在以太坊中，Wei和Ether(以太币)有什么区别？&#34; false &gt;}}
答： Wei是以太币的最小面值，就好比说人民币的最小面值是分，英镑的最小面值是便士。 其换算关系为 `1 ether = 10^18 wei`。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;以太坊的平均区块间隔时间是多少？&#34; false &gt;}}
答： 平均区块间隔时间为14秒，当然了这只是理论值，你可以在Etherscan（ https://etherscan.io/chart/blocktime ）中查到每日的平均区块时间间隔。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;以太坊的平均区块大小是多少？&#34; false &gt;}}
答： 区块大小视情况而定，大约是 `2KB` 。不同于比特币使用区块大小来规定区块的交易量上限，以太坊使用燃料（gas）限制。燃料限制决定了每个区块中处理的交易量、存储/带宽的上限，因为交易和智能合约中函数的执行都要根据指令的复杂度多少来付出相应的燃料，所以使用燃料限制来约束区块大小是可行的。

燃料限制会随网络的波动和矿工的意愿变化，每个矿工都可以选择自己愿意接受的燃料价格。燃料价格就像是比特币中的交易费，只是这里的价格是最小单位燃料的价格，而不是每笔交易的价格。

想要算出一个区块中可以容纳多少笔交易，你不需要清楚地知道燃料的价格，只需知道平均每笔交易使用多少燃料并用整个燃料限制除以它即可。

去年以太猫的发行造成了以太坊网络的大拥堵，整个网络中充斥着大量未被处理的交易。在这种情况下矿工有两种选择。他们可以投票提高燃料限制来处理更多交易，也可以开始提高自己的燃料价格标准并拒绝处理燃料费用过低的交易。

与比特币一样，即使燃料价格很低的交易也可能会被处理加入区块链中，但矿工肯定会先处理完燃料价格高的交易再处理它。如果你的交易并没有那么紧急，设置一个很低的燃料价格也不是不可以，就像我们现实生活中的“花时间来节省金钱”。

如果有恶意用户持续地发起海量交易堵塞网络，全网的交易成本就会越来越高，直到这个恶意用户用完资金或者矿工赚足了交易费并决定扩大网络容量。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;以太币是如何产生的？&#34; false &gt;}}
答： 2014年以太坊项目以众筹的形式创建并预售了6000万个以太币，除此之外，矿工挖矿也会生成新的以太币。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;以太坊中的节点是什么？&#34; false &gt;}}
答： 从本质上来说，节点是一台连接到区块链、可以处理交易的计算机。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;以太坊都有哪些网络？&#34; false &gt;}}
答： 以太坊共有三种类型的网络： `以太坊主链` （就是我们平时用的以太坊）、 `以太坊测试网络` （如早期的 Ropsten 和 Rinkeby，最新的 Goerli 和 Sepolia 供开发人员的学习和测试）和 `以太坊私有链` （也叫以太坊私有网络，任何人都能用以太坊的代码部署自己的私有链）。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;与以太坊网络交互都哪些方法？&#34; false &gt;}}
答： 区块链节点、web3.js&#43;智能合约、ethers.js&#43;智能合约、remix&#43;智能合约、ethscan&#43;智能合约、Ethreum JSON RPC API &#43; 智能合约。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;你是否能在以太坊中“隐藏”一笔交易？&#34; false &gt;}}
答： 不能。以太坊区块链中所有的交易都是公开可见的。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;以太坊的交易记录存放在哪里？&#34; false &gt;}}
答： 在公共可见的账本中，这个帐本通常被称为区块链。可以通过区块链浏览器进行查询。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;以太坊主链已经很强大了，为什么还要使用以太坊私有链？&#34; false &gt;}}
答： 原因有很多，主要是因为数据涉及隐私，将数据库去中心化，权限控制和测试。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;如何查看一笔交易或一个区块的详细信息？&#34; false &gt;}}
答： 你可以使用区块链浏览器，如etherscan.io或live.ether.camp。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;如何查看私有链中一笔交易或一个区块的详细信息？&#34; false &gt;}}
答： 一些开源的区块链浏览器满足这种需求，如etherparty推出的区块链浏览器（ https://github.com/etherparty/explorer ）。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;区块链的共识过程是什么？&#34; false &gt;}}
答： 共识是按照特定的协议（如以太坊的协议）验证交易，将交易打包进区块并加入区块链的过程。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;以太坊挖矿操作的工作原理是什么呢？&#34; false &gt;}}
答： 从原理上说，以太坊中的挖矿操作几乎与比特币相同。

简单地说，就是对于每个包含交易的区块，矿工使用计算机反复且非常快速地试验谜题的答案，直到有一个矿工猜对谜题。

更具体地说，矿工将当前区块唯一的区块头元数据（包括时间戳和软件版本）和一个随机数（nonce value）作为哈希函数的输入，函数将返回一个固定长度、看起来像是由数字和字母随机组成的乱码，叫做哈希值。哈希函数的特性是不同输入对应不同的哈希值，因此矿工仅需改变随机数的值，就会得到一个完全不同的哈希值。

如果算出来的哈希值小于当前的目标值（挖矿难度），则这个矿工就挖出了一个区块，他会获得一些以太币奖励，然后通过向全网络广播该区块，其他节点可以验证该区块中的交易，验证通过后将该区块加入到本地区块链的副本中。也就是说，如果矿工B算出了一个哈希值，矿工A将立刻停止当前区块的哈希值计算，把B挖出的区块加入区块链中并开始新一轮的哈希值计算。

矿工们很难在这场算力竞争中作弊。为了得到迷题的答案，除了一个个试没有更好的办法，也没有伪造这些计算工作的可能，这就是该解谜方法被称为“工作量证明”的原因。

从另一方面来说，用户不需要来验证哈希值是否正确，因为每个节点都已验证过。

一般来说，每12到15秒就会有一个矿工挖出一个新区块。如果矿工解谜的时间开始出现更快或更慢的倾向，算法会自动调整问题的难度，以使矿工解谜的时间稳定在14秒左右。

矿工有一定几率能挖到新区块赚取以太币奖励，他们的赚钱能力取决于运气和他们投入的计算能力。

以太坊使用的工作量证明算法被称为“ethash”，它被设计的需要更多内存，从而增大了使用昂贵的ASIC矿机挖矿的难度，因为ASIC矿机的出现严重压榨了使用其他设备矿工的收益，以至于在比特币中唯一能盈利的挖矿形式就是使用这种定制化的芯片。

从某种意义上来说，ethash可能已经成功实现了这一目标，因为专用的ASIC矿机不能用于挖掘以太坊（至少目前还没有）。

此外，由于以太坊将要从工作量证明挖矿逐步过渡到权益证明挖矿，因而购买ASIC矿机可能不是一个明智的选择，因为一旦以太坊转向权益证明它必将被淘汰。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;区块链中最常用的两种共识协议是什么？&#34; false &gt;}}
答： 工作量证明（PoW）和权益证明(PoS)，业界也在不断涌现其它新的共识协议。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;签署一笔交易需要用到什么工具？&#34; false &gt;}}
答： 用户的私钥。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;在私钥丢失后，用户是否还可以恢复以太坊帐户？&#34; false &gt;}}
答： 是的，用户可以使用12字助记词恢复。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;用什么方法可以连接到以太坊节点？&#34; false &gt;}}
答： IPC-RPC，JSON-RPC和WS-RPC（RPC指Remote Procedure Call，远程过程调用）。
{{&lt; /admonition &gt;}}


# 2 以太坊客户端相关

{{&lt; admonition success &#34;以太坊中异常火爆的Geth是什么呢？&#34; false &gt;}}
答： Geth是以太坊的一个命令行客户端。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;连接到Geth客户端的默认方式是什么？&#34; false &gt;}}
答： 默认情况下使用IPC-RPC，禁用其他所有的RPC。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;Geth客户端中都有哪些API（Application Programming Interface，应用程序编程接口）？&#34; false &gt;}}
答： Admin（管理员）、 eth（以太币）、web3、miner（矿工）、net（网络）、personal（个人）、shh、debug（调试）和 txpool（工具）。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;你可以使用哪些RPC通过网络连接到Geth客户端？&#34; false &gt;}}
答： 你可以使用JSON-RPC或WS-RPC通过网络连接到Geth客户端。 IPC-RPC只能用来连接本地部署的Geth客户端。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;如果你输入命令“--rpc”，启用的是哪一个RPC？&#34; false &gt;}}
答： JSON-RPC。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;默认情况下哪些RPC API是启用的？&#34; false &gt;}}
答： eth（以太币）、 web3和net（网络）。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;如何为JSON-RPC启用admin api？&#34; false &gt;}}
答： 输入命令“--rpcapi”。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;命令“--datadir”有什么功能？&#34; false &gt;}}
答： 它指定了区块链的存储位置。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;Geth的“快速”同步是什么，为什么它更快速呢？&#34; false &gt;}}
答： “快速”同步仅下载收款交易所在的区块，并拉取（pull）整个最近状态数据库，而不是像普通同步一样，下载整个区块链的数据并重放所有发生的交易。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;命令“--testnet”有什么功能？&#34; false &gt;}}
答： 它将客户端连接到以太坊测试网络。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;启动Geth客户端会在屏幕上打印大量的输出信息，如果不想被这些繁杂信息干扰该怎么办？&#34; false &gt;}}
答： 使用“--verbosity”命令调低输出信息复杂度的值（默认值为3）。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;如何使用IPC-RPC连接两个Geth客户端？&#34; false &gt;}}
答： 首先启动一个Geth客户端，复制其管道位置（pipe location），然后使用相同的数据文档存储目录（datadir）启动另一个Geth客户端，并使用”--attach”命令传递复制的管道位置。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;如何将自定义javascript文件加载到Geth控制台？&#34; false &gt;}}
答：输入”--preload”命令和文件的路径即可。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;Geth客户端中帐户存储在哪里？&#34; false &gt;}}
答：存储在密钥库（keystore）目录中。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;如何使用给定的账户发起一笔交易？&#34; false &gt;}}
答：首先你要在“ --unlock ”命令中传入帐户地址或索引来解锁账户。然后你需要使用“--password ”命令指定一个此账户的密码文件。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;我们刚才说到了有关索引的内容。账户的索引取决于什么？&#34; false &gt;}}
答：取决于你添加帐户的顺序。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;Geth客户端是否能用来挖矿？&#34; false &gt;}}
答：是的，输入“--mine”命令即可。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;挖矿选项中的“etherbase”是什么？&#34; false &gt;}}
答：这是接受挖矿奖励的帐户地址，该帐户的索引为0。
{{&lt; /admonition &gt;}}

# 3 智能合约常见问题

{{&lt; admonition success &#34;ABI是什么？&#34; false &gt;}}
答：简单来说，“ABI”就是低级别的“API”。

ABI（Application Binary Interface）应用程序二进制接口，从本质上讲就是你调用智能合约中的函数并获取它返回值的方式。

ABI中定义了如何调用智能合约中的函数以及应该选用哪种二进制格式将信息从一个程序组件传递到下一个程序组件的详细信息。

以太坊智能合约以字节码的形式部署在以太坊区块链上，一个智能合约中可能会有多个函数。所以说，有了ABI你才可以指定调用智能合约中的哪个函数，才能保证函数的返回值是你期望的格式。

这是一个来自以太坊ABI规范的例子：

```solidity
contract Foo {function bar(real[2] xy) {}function baz(uint32 x, bool y) returns (bool r) { r = x &gt; 32 || y; }function sam(bytes name, bool z, uint[] data) {}}
```

如果我们想使用参数“69”和“真”调用函数baz（），我们总共会传递68字节的数据，整个过程可以分解为：

0xcdcd77c0：baz（）函数的ID。这是函数baz（uint32，bool）以ASCII形式编码的Keccak-256哈希值的前4个字节。
```solidity
0x0000000000000000000000000000000000000000000000000000000000000045```

传入函数baz（）的第一个参数，32位无符号整数69被填充为32个字节（10进制中的69换算成16进制为45）。

```solidity
0x00000000000000000000000000000000000000000000000000000000000000000001 
```

传入函数baz（）的第二个参数，布尔值真，也就是1，被填充为32个字节。

这68个字节会存放在交易的数据字段（data），需要注意的是，一定要仔细检查交易数据字段中添加的内容，因为在将其传递给智能合约时可能会产生意外的，甚至可能是恶意的副作用。）

为了避免出现生成函数ID时的常见错误，在此过程中必须使用规范的数据类型，就比如说使用标准的256位无符号整型（uint256）而不是无符号整型（uint）。

在Solidity中计算上述sam（）函数ID的的代码如下：

```solidity
bytes4(sha3(&#34;sam(bytes,bool,uint256[])&#34;)
```

在这里可以使用诸如web3.js等高级程序库来抽象大部分的细节，不过提供给web3.js的JSON格式ABI是必不可少的。

**注意：** ABI是一个抽象，它并不是以太坊核心协议的一部分。任何人都可以为自己的智能合约定义专属的ABI，这些智能合约的任何调用者都必须遵守该ABI的规定才能得到有意义的调用结果。但是，对于所有开发人员来说，使用Solidity，Serpent和web3.js更为简单，这些也都符合ABI的规定。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;智能合约是什么？&#34; false &gt;}}
答：从本质上说，智能合约就是用多种语言编写的计算机代码。智能合约存在于区块链网络上，它们按照自身嵌入的规则执行相关操作，可以看做是参与者之间的契约。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;Metamask使用什么节点？&#34; false &gt;}}
答：它使用infura.io。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;相比于传统以太坊钱包，有哪些功能是Metamask不支持的？&#34; false &gt;}}
答：它不支持挖矿和部署智能合约。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;智能合约的执行是免费的吗？&#34; false &gt;}}
答：不，只能通过执行交易来调用智能合约，而交易需要燃料费用。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;查看智能合约的状态是免费的吗？&#34; false &gt;}}
答：是的，查询状态不需要执行交易。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;谁来执行智能合约？&#34; false &gt;}}
答：矿工。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;为什么调用智能合约中的函数需要花钱？&#34; false &gt;}}
答：除了一些不会改变智能合约状态，除了返回值之外没有其他逻辑的函数之外，调用智能合约中的函数都是需要花钱的。

这笔花费中，除了向智能合约中转入以太币执行调用之外，调用改变智能合约状态的函数需要花费燃料来执行。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;为什么以太坊中要引入燃料费用呢？&#34; false &gt;}}
答：因为矿工使用自己的计算机（矿机）执行智能合约代码，矿工如果能收回购买机器的成本并获得盈利才能保证整个系统生态的安全性，所以以太坊设计使得矿工可以通过执行调用者请求的代码来赚取燃料费用，从而维持一个健康的生态。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;燃料价格是否能决定交易被处理的时间？&#34; false &gt;}}
答：是的，也不全是。一般来说，你支付的燃料价格越高，交易越有可能被加入区块链。尽管如此，燃料价格并不能保证交易更快地被处理。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;交易中的燃料使用量取决于什么？&#34; false &gt;}}
答：燃料使用量取决于存储的用量，指令（操作码）的类型和数量。每一条以太坊虚拟机的操作码都明确规定了所需燃料的数量。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;交易费gas该如何计算？&#34; false &gt;}}
答：**交易费 = 使用的燃料数量 * 燃料价格 （燃料价格由交易者指定）**。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;如果执行智能合约的花费少于交易者支付的燃料费用，他是否会获得退款？&#34; false &gt;}}
答：是的。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;如果执行智能合约的花费超过了交易者支付的燃料费用，这时会发生什么？&#34; false &gt;}}
答：用户不会获得退款，并且一旦所有燃料耗尽执行就会停止，智能合约的状态就不会改变。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;谁来支付调用智能合约的费用？&#34; false &gt;}}
答：调用智能合约的用户。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;节点在哪里运行智能合约的代码呢？&#34; false &gt;}}
答：节点在以太坊虚拟机（EVM）中运行智能合约的代码。以太坊虚拟机规范是以太坊协议的一部分。以太坊虚拟机只是节点运行的一个进程。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;以太坊虚拟机需要什么工具来运行智能合约？&#34; false &gt;}}
答：它需要智能合约的字节码，它由高级别语言（如Solidity）编译生成。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;以太坊虚拟机都包含哪些部分？&#34; false &gt;}}
答：内存区域，堆栈和执行引擎。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;Remix是什么？&#34; false &gt;}}
答：Remix是一个用于开发，测试和部署智能合约的在线工具。它非常适合快速构建和测试轻量级的智能合约，但不适用于复杂的智能合约。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;在Remix中，你可以连接哪些节点？&#34; false &gt;}}
答：你可以使用Metamask钱包连接到公共节点，使用Geth钱包连接到本地节点以及使用Javascript虚拟机连接到内存中模拟的节点。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;什么是DApp，它与普通App有什么不同？&#34; false &gt;}}
答：应用程序通常包含一个与某些中心化资源（由组织拥有）交互的客户端，通常有一个与中心化数据层相连的中间层。如果中心化数据层中的数据丢失，则无法（轻松）恢复。

DApp表示去中心化的应用程序。DApp通过智能合约与区块链网络交互，它们使用的数据驻留在智能合约的实例中，与中心化的数据相比，去中心化的数据安全性更高。
{{&lt; /admonition &gt;}}

# 4 Solidity常见问题

{{&lt; admonition success &#34;Solidity是静态类型语言，还是动态类型语言？&#34; false &gt;}}
答：Solidity是静态类型语言，这意味着类型在编译阶段是已知的。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;Java和Solidity之间存在哪些差异。&#34; false &gt;}}
答：相比于Java，Solidity支持多继承（multiple inheritance），但不支持方法重载（Overloading）。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;在Solidity文件中需要指定的第一个参数是什么？&#34; false &gt;}}
答：第一个参数是Solidity编译器的版本，需要指定为^ 0.4.8。不要小看了这一步，因为它可以避免出现在使用其他版本编译器进行编译时引入的不兼容错误。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;一个智能合约包含什么？&#34; false &gt;}}
答：智能合约主要由状态变量，函数和事件组成。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;智能合约中都有哪些类型的函数？&#34; false &gt;}}
答：有构造函数（constructor），回退函数（fallback function），常量函数（constant functions）和修改智能合约状态的函数。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;如果我将多个智能合约定义放入单个Solidity文件中，会出现什么样的错误？&#34; false &gt;}}
答：将多个智能合约定义放入单个Solidity文件中是完全可行的。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;两个智能合约可以通过哪些方式进行交互？&#34; false &gt;}}
答：一个智能合约可以调用、创建和继承另一个智能合约。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;当你尝试部署具有多个智能合约的文件时会发生什么？&#34; false &gt;}}
答：编译器只会部署该文件中的最后一个智能合约，也就是说，其他所有智能合约都被忽略了。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;如果我有一个庞大的项目，我是否需要将所有相关的智能合约保存在一个文件中？&#34; false &gt;}}
答：不需要，你可以使用import语句导入文件。也可以使用HTTP导入文件（甚至是Github上的文件）。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;以太坊虚拟机的内存都有哪些部分？&#34; false &gt;}}
答：存储（Storage）、内存（Memory）和Calldata。

**Storage：** 可以把存储想象成一个数据库。每个智能合约都管理自己的存储变量，从而得到一个键值对数据库（256位的键和值）。存储与普通数据库的唯一区别就是，由于存在燃料费用的问题它读取和写入操作的成本更高。
**Memory：** 内存是一个临时性的存储。当函数调用执行完毕后，内存中的数据将会被释放。你可以在内存中分配各种复杂的数据类型，如数组和结构体。
**Calldata：** Calldata可以理解为一个函数调用堆栈（Callstack）。它是临时的，不可修改的，它存储着以太坊虚拟机的执行数据。

{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;存储区和内存区分别存储了哪些变量？&#34; false &gt;}}
答：状态变量和局部变量（通常局部变量都是对状态变量的引用）位于存储区中，而函数的参数位于内存区中。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;EVM调用和非EVM调用之间有什么区别呢？&#34; false &gt;}}
答：EVM调用是智能合约中的函数调用，它触发函数执行并需要燃料。

非EVM调用读取公开可见的数据，不需要燃料。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;你需要什么工具与DApp的智能合约进行交互？&#34; false &gt;}}
答：需要智能合约的ABI和字节码。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;ABI的作用是什么？&#34; false &gt;}}
答：ABI是智能合约公共接口的描述，DApp用这个公共接口来调用智能合约。每个节点上的以太坊虚拟机都需要智能合约的字节码来运行智能合约。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;为什么要使用大数运算程序库（BigNumber library）&#34; false &gt;}}
答：因为Javascript无法正确处理大数字。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;为什么要始终检查DApp代码的开头是否设置了web3提供程序（provider）？&#34; false &gt;}}
答：因为如果不这样，Metamask会用自己的web3提供程序覆盖掉它。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;为什么使用web3 js的1.x版本而不是0.2x.x版本？&#34; false &gt;}}
答：主要是因为1.x版本的异步调用使用promise对象（承诺将来会执行，比回调对象更合理和更强大）而不是回调对象，promise对象也是javascript中的首选。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;“.call”和“.send”有什么区别？&#34; false &gt;}}
答：“.send”发起交易并且产生费用，而“.call”仅查询智能合约的状态不产生费用。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;是否可以通过这样的命令“.send（{value：1}）”发送一个以太币？&#34; false &gt;}}
答：不可以，实际上这样你只送了1wei（1以太币 =10^18Wei）。交易中的单位是wei，而不是以太币。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;那是否意味着，为了发送一个以太币，我需要将值设置为10^18？&#34; false &gt;}}
答：不用，这样太麻烦。你可以使用util方法，即：web3.utils.toWei（1，&#39;ether&#39;） 。
{{&lt; /admonition &gt;}}

{{&lt; admonition success &#34;那是否意味着，为了发送一个以太币，我需要将值设置为10^18？&#34; false &gt;}}
答：不用，这样太麻烦。你可以使用util方法，即：web3.utils.toWei（1，&#39;ether&#39;） 。
{{&lt; /admonition &gt;}}







