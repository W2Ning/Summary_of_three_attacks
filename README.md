# Summary_of_three_attacks
在财务自由之前,攻击者就已经财务自由了

### 写在前面的废话

以下是对2022年上半年3个独立攻击事件的汇总分析, 虽然漏洞原理各不相同, 但他们都有一个共同点:

攻击者不再只依赖`FlashLoan`进行`Single-Transaction-Attack`(我自己瞎起的说法).

而是真金白银地砸出数百万美金去操纵价格,制造漏洞触发的前置环境, 最后成功完成恶意攻击.

如果2022年之前的攻击者们还只是钻研代码, 空手套白狼的穷小子.

那么非常明显的, 2022年之后的他们不仅更加专业, 而且手上弹药充足, 人均索罗斯了可以说.

这样的大环境对众多Dapp的鲁棒性提出了更高的要求, 曾经被认为安全的经济模型或许需要重新思考:

> 所谓`高昂`的攻击成本是否还可以让那些心怀恶意的人望而却步.

以下3个事件按时间顺序排列

## Fortress

2022年5月9日, 攻击者通过预言机合约`Chain.sol`的`submit()`函数未授权调用的问题,

恶意操纵了`FTS`的价格, 用原本低价值`FTS`抵押借贷了多种高价值Token离场

<img width="962" alt="submit" src="https://user-images.githubusercontent.com/33406415/168532696-f205536f-f284-47bd-bb3a-ba0ccb6e9bc6.png">

但其实攻击者早在4月就已经开始了前置准备工作:

1. 4月27日,分配购买大量的`FTS`

<img width="746" alt="4 27" src="https://user-images.githubusercontent.com/33406415/168532713-77c5e30b-6e06-4838-b9f4-893e25f99ba0.png">


2. 5月2日,发布恶意提案

因为在此前`FTS`并不是官方承认的抵押物, 并不能凭`FTS`借出其他Token

所以提案内容就是把`FTS`添加进`collateral`的名单中

```
https://bsc.fortress.loans/vote/proposal/11
```

<img width="1272" alt="11" src="https://user-images.githubusercontent.com/33406415/168532725-ab03893d-959c-4770-8fb8-d3be6659f697.png">


3. 5月9日,提案正式通过后, 部署攻击合约

攻击合约在一次Transaction中同时完成了`执行提案➡️篡改价格➡️添加质押➡️借出其他Token`的操作

<img width="1022" alt="攻击合约的四步操作" src="https://user-images.githubusercontent.com/33406415/168532740-d6ccca2e-be79-446f-ade3-f6f92f159cc6.png">


非常耐心了属于是.

## DEUS

2022年4月28日, DEUS所使用的`Muon VWAP`预言机被恶意操纵价格, 导致`DEI`的价格翻了20倍.

攻击者用`DEI`超额借出约1300万美金的资产离场

1. 跨链转入两百多万的`USDC`作为攻击预备资金

![200万USDC](https://user-images.githubusercontent.com/33406415/168532752-3096ae0d-336a-4ed1-b838-3c703f4ef6a3.jpeg)

2. 随后在`USDC/DEI`交易对上精心构造了一笔`200万USDC➡️10万DEI`的`swap`,先实现对`Muon VWAP`预言机喂价的操纵.

<img width="861" alt="swap" src="https://user-images.githubusercontent.com/33406415/168532763-86f91466-be75-4e2c-9ddc-ac7f916b8d26.png">

3. 丧心病狂的调用了22个不同Dex不同池子的闪电贷...

(在Trace中点开至少22*3层嵌套才找到核心攻击逻辑是一种怎样的体验?)

这一举动几乎借空了`Fantom`链上的1.4亿`USDC`, 再次在`USDC/DEI`交易对上兑换`DEI`

确保`USDC/DEI`交易对的价格和`Muon VWAP`预言机的链下签名喂价保持一致.

```
https://ftmscan.com/tx/0x39825ff84b44d9c9983b4cff464d4746d1ae5432977b9a65a92ab47edac9c9b5
```

<img width="1014" alt="22" src="https://user-images.githubusercontent.com/33406415/168532772-c4ce9f60-9bbc-49cb-8957-4bb569a0624f.png">


4. 最后抵押`USDC/DEI`的LPToken, 借走1700万的`DEI`, 换回`USDC`, 归还22个闪电贷, 带着约1300万美元的利润跨链离场

```
https://ftmscan.com/address/0x750007753eCf93CeF7cFDe8D1Bef3AE5ea1234Cc#tokentxns
```

<img width="1367" alt="1300万" src="https://user-images.githubusercontent.com/33406415/168532793-3e94c95b-c50a-4ec0-97a8-b87c436bded2.png">

> 这次攻击事件我是真的服气

> 不论是前期巨额的预备资金

> 22次闪电贷的嵌套调用

> 还有对`链下预言机`和`链上交易对`运行机制的理解

> 我佩服的五体投地

> 或许1300万美金只能在`Rekt`上排第41位

<img width="725" alt="41" src="https://user-images.githubusercontent.com/33406415/168532809-7e9e73f9-458f-4ba1-a0ec-2eb2181061fc.png">

> 但是在我心里它排前三


## INVERSE

2022年4月2日, `INV`的价格被恶意操纵, 损失约1500万美金

1. 从混币器中取出900个`ETH`作为攻击预备资金

<img width="1389" alt="900" src="https://user-images.githubusercontent.com/33406415/168532822-af854367-0822-4d22-bce7-9f38fca44ddc.png">

2. 在`INV-WETH`交易对上进行swap操作, 用500个`ETH`换`1700`INV, 一定程度上操纵了价格

```
https://etherscan.io/tx/0x20a6dcff06a791a7f8be9f423053ce8caee3f9eecc31df32445fc98d4ccd8365
```

<img width="1277" alt="eth-inv" src="https://user-images.githubusercontent.com/33406415/168532834-a2bb84dc-5771-499f-b0fe-c10d16762417.png">

说一定程度是因为`Inverse Finance`所使用的预言机为`Sushi Twap`

它会采样每个块的第一笔与`INV`有关的历史交易价格作为参考,加权平均算出当前价格

也就是说, 攻击者需要在时间窗口期,多个块区间内维持这一扭曲的价格.

3. 于是攻击者开始通过多个地址发布垃圾交易, 挤占整个区块
 
排挤掉其他正常的交易, 确保扭曲的价格不被其他人破坏

攻击者通过`Disperse`把361.5个`ETH`平均分给了241个地址

```
https://etherscan.io/tx/0x561e94c8040c82f8ec717a03e49923385ff6c9e11da641fbc518ac318e588984
```

<img width="737" alt="1 5" src="https://user-images.githubusercontent.com/33406415/168532905-283c3dd4-a5a4-47bf-83de-722c68719a59.png">


拆分后241个地址同时向一个地址转账, 大量左手倒右手的交易只是为了挤占整个区块,让`Twap`的价格生效

```
https://etherscan.io/txs?a=0x8b4c1083cd6aef062298e1fa900df9832c8351b3
```

<img width="1454" alt="1eth" src="https://user-images.githubusercontent.com/33406415/168532929-bfed6e21-c79f-4a8b-8ded-3545f4849fbd.png">

4. 随后攻击者质押`INV`, 借出价值1500万美金`wbtc`,`YFI`,`DOLA`离场.

```
https://etherscan.io/tx/0x600373f67521324c8068cfd025f121a0843d57ec813411661b07edc5ff781842
```

<img width="1050" alt="lend" src="https://user-images.githubusercontent.com/33406415/168532968-d96e71a7-fb29-46bb-a865-3c00398aaf4b.png">

### 综上

这些攻击者在`财务自由`之前就已经`财务自由`了.

很多之前被认为`成本很高, 哪有人这么下本儿`的攻击手段,正在一个一个在现实中发生.

Dapp开发者在设计协议时, 可能需要重新给`假想敌`做`画像`了.
