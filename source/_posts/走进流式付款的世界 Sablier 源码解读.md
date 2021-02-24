---
title: 走进流式付款的世界 Sablier 源码解读
date: 2021-01-24 11:04:36
tags: Defi
categories: Defi
banner_img: /banners/sablier.png
---

Sablier是以太坊实时金融协议，可以实现流式付款。简单的来说，就是可以最规定时间内，向指定地址不断的匀速付款，它改变了原有的支付方式。

以老板支付工资为例，传统金融往往是每个月在固定时日支付工资，或是日结工资，难以做到秒结、分结。在去中心化金融（Defi）世界中，Sablier利用以太坊的智能合约，完美做到了向任何人付款，达到“秒结”的目的。

Sablier有三个特色：

1. 秒结 
2. 使用Dai（去中心化稳定币）支付 
3. 基于ERC 1620构建

可以应用的场景：支付薪水、订阅内容、咨询服务、纳税、租赁行业、停车场付费等。

而这一切，都依赖于智能合约，那就跟我一起走进这个神奇的智能合约的世界。

## ERC 20

开始之前，需要做一些基础铺垫，先解释一下万物之源——ERC 20。

ERC全称“Ethereum Request for Comment"，其实就是以太坊草案的意思，ERC20，就是其草案编号。ERC20是Token标准。

ERC20让“发币”变得更加简单，门槛极低，开启了通证经济时代。任何人只要符合这个标准，就可以发行自己的Token合约（代币）。

### Token合约

Token合约中的token，指的是代币，通过智能合约实现了一种代币持有情况的映射关系。如下图所示，一个token合约就是一个包含了一个对账户地址及其余额的映射的智能合约。

![](/content/images/sablier/1.png)

在[以太坊浏览器](https://etherscan.io/tokens)中，我们可以浏览发行的ERC20代币Token和其合约代码。


![](https://upload-images.jianshu.io/upload_images/4092004-5e3abcd42ecac85d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### EIP 20 协议

[EIP 20](https://eips.ethereum.org/EIPS/eip-1620)是对ERC20的协议实现，并且已经实现到以太坊中。EIP20提供的功能类似于将代币从一个账户转移到另一个账户，以及获取当前代币余额等功能。

例子
![](/content/images/sablier/2.png)


方法

```java
// 代币名称 如"MyToken"
function name() public view returns (string)
// 代币缩写 如"MT"
function symbol() public view returns (string)
// 精度，如8，则意味着代币实际值需除以100000000
function decimals() public view returns (uint8)
// 总发行量
function totalSupply() public view returns (uint256)
// 返回某个地址的余额
function balanceOf(address _owner) public view returns (uint256 balance)
// 转账_value个Token到_to地址上，同时必须触发Transfer事件
function transfer(address _to, uint256 _value) public returns (bool success)
// 转账_value个Token从_from到_to地址上，同时必须触发Transfer事件
function transferFrom(address _from, address _to, uint256 _value) public returns (bool success)
// 允许_spender多次提现，_value为限额，授权用户可代表我们花费的代币数
function approve(address _spender, uint256 _value) public returns (bool success)
// 允许_spender从_owner提现，授权花费的代币数
function allowance(address _owner, address _spender) public view returns (uint256 remaining)
```

事件

以太坊的事件更类似于日志性质，可与前端同步数据，同时可以存储数据记录每笔交易的过程，方便溯源。

```java
event Transfer(address indexed _from, address indexed _to, uint256 _value)
event Approval(address indexed _owner, address indexed _spender, uint256 _value)
```

**例子**

```javascript
pragma solidity ^0.4.20;

interface tokenRecipient { function receiveApproval(address _from, uint256 _value, address _token, bytes _extraData) external; }

contract TokenERC20 {
    string public name;
    string public symbol;
    uint8 public decimals = 18;  // decimals 可以有的小数点个数，最小的代币单位。18 是建议的默认值
    uint256 public totalSupply;

    // 用mapping保存每个地址对应的余额
    mapping (address => uint256) public balanceOf;
    // 存储对账号的控制
    mapping (address => mapping (address => uint256)) public allowance;

    // 事件，用来通知客户端交易发生
    event Transfer(address indexed from, address indexed to, uint256 value);

    // 事件，用来通知客户端代币被消费
    event Burn(address indexed from, uint256 value);

    /**
     * 初始化构造
     */
    constructor(uint256 initialSupply, string tokenName, string tokenSymbol) public {
        totalSupply = initialSupply * 10 ** uint256(decimals);  // 供应的份额，份额跟最小的代币单位有关，份额 = 币数 * 10 ** decimals。
        balanceOf[msg.sender] = totalSupply;                // 创建者拥有所有的代币
        name = tokenName;                                   // 代币名称
        symbol = tokenSymbol;                               // 代币符号
    }

    /**
     * 代币交易转移的内部实现
     */
    function _transfer(address _from, address _to, uint _value) internal {
        // 确保目标地址不为0x0，因为0x0地址代表销毁
        require(_to != 0x0);
        // 检查发送者余额
        require(balanceOf[_from] >= _value);
        // 确保转移为正数个
        require(balanceOf[_to] + _value > balanceOf[_to]);

        // 以下用来检查交易，
        uint previousBalances = balanceOf[_from] + balanceOf[_to];
        // Subtract from the sender
        balanceOf[_from] -= _value;
        // Add the same to the recipient
        balanceOf[_to] += _value;
        emit Transfer(_from, _to, _value);

        // 用assert来检查代码逻辑。
        assert(balanceOf[_from] + balanceOf[_to] == previousBalances);
    }

    /**
     *  代币交易转移
     * 从创建交易者账号发送`_value`个代币到 `_to`账号
     *
     * @param _to 接收者地址
     * @param _value 转移数额
     */
    function transfer(address _to, uint256 _value) public {
        _transfer(msg.sender, _to, _value);
    }

    /**
     * 账号之间代币交易转移
     * @param _from 发送者地址
     * @param _to 接收者地址
     * @param _value 转移数额
     */
    function transferFrom(address _from, address _to, uint256 _value) public returns (bool success) {
        require(_value <= allowance[_from][msg.sender]);     // Check allowance
        allowance[_from][msg.sender] -= _value;
        _transfer(_from, _to, _value);
        return true;
    }

    /**
     * 设置某个地址（合约）可以交易者名义花费的代币数。
     *
     * 允许发送者`_spender` 花费不多于 `_value` 个代币
     *
     * @param _spender The address authorized to spend
     * @param _value the max amount they can spend
     */
    function approve(address _spender, uint256 _value) public
    returns (bool success) {
        allowance[msg.sender][_spender] = _value;
        return true;
    }

    /**
     * 设置允许一个地址（合约）以交易者名义可最多花费的代币数。
     *
     * @param _spender 被授权的地址（合约）
     * @param _value 最大可花费代币数
     * @param _extraData 发送给合约的附加数据
     */
    function approveAndCall(address _spender, uint256 _value, bytes _extraData)
    public
    returns (bool success) {
        tokenRecipient spender = tokenRecipient(_spender);
        if (approve(_spender, _value)) {
            spender.receiveApproval(msg.sender, _value, this, _extraData);
            return true;
        }
    }

    /**
     * 销毁创建者账户中指定个代币
     */
    function burn(uint256 _value) public returns (bool success) {
        require(balanceOf[msg.sender] >= _value);   // Check if the sender has enough
        balanceOf[msg.sender] -= _value;            // Subtract from the sender
        totalSupply -= _value;                      // Updates totalSupply
        emit Burn(msg.sender, _value);
        return true;
    }

    /**
     * 销毁用户账户中指定个代币
     *
     * Remove `_value` tokens from the system irreversibly on behalf of `_from`.
     *
     * @param _from the address of the sender
     * @param _value the amount of money to burn
     */
    function burnFrom(address _from, uint256 _value) public returns (bool success) {
        require(balanceOf[_from] >= _value);                // Check if the targeted balance is enough
        require(_value <= allowance[_from][msg.sender]);    // Check allowance
        balanceOf[_from] -= _value;                         // Subtract from the targeted balance
        allowance[_from][msg.sender] -= _value;             // Subtract from the sender's allowance
        totalSupply -= _value;                              // Update totalSupply
        emit Burn(_from, _value);
        return true;
    }
}
```

只要将上述的智能合约发布到以太坊上，与该智能合约做交易，即可完成代币的发行、金额分配。

## ERC 1620

铺垫了那么多，其实ERC 1620 也是一种Token代币协议，是对ERC 20的封装与改进，为其加入了流式协议。

在流式协议中，对每一个流式付款的开始、停止、提现，都有记录。

前端根据对应的事件记录，做出UI上的响应。

```javascript
pragma solidity 0.5.11;

/**
 * @title ERC-1620 Money Streaming Standard
 * @author Sablier
 * @dev See https://eips.ethereum.org/EIPS/eip-1620
 */
interface IERC1620 {
    /**
     * @notice Emits when a stream is successfully created.
     */
    event CreateStream(
        uint256 indexed streamId,
        address indexed sender,
        address indexed recipient,
        uint256 deposit,
        address tokenAddress,
        uint256 startTime,
        uint256 stopTime
    );

    /**
     * @notice Emits when the recipient of a stream withdraws a portion or all their pro rata share of the stream.
     */
    event WithdrawFromStream(uint256 indexed streamId, address indexed recipient, uint256 amount);

    /**
     * @notice Emits when a stream is successfully cancelled and tokens are transferred back on a pro rata basis.
     */
    event CancelStream(
        uint256 indexed streamId,
        address indexed sender,
        address indexed recipient,
        uint256 senderBalance,
        uint256 recipientBalance
    );

    function balanceOf(uint256 streamId, address who) external view returns (uint256 balance);

    function getStream(uint256 streamId)
        external
        view
        returns (
            address sender,
            address recipient,
            uint256 deposit,
            address token,
            uint256 startTime,
            uint256 stopTime,
            uint256 remainingBalance,
            uint256 ratePerSecond
        );

    function createStream(address recipient, uint256 deposit, address tokenAddress, uint256 startTime, uint256 stopTime)
        external
        returns (uint256 streamId);

    function withdrawFromStream(uint256 streamId, uint256 funds) external returns (bool);

    function cancelStream(uint256 streamId) external returns (bool);
}
```

### Sablier 关键源码

本质上来说，Sablier是对ERC 1620的实现，仔细阅读代码，不难看出流式付款，不是每秒做交易，只是在关键的时间节点，打上对应的事件记录，中间看似 秒薪，实际上是web上的显示动画，只有“提现withdraw”才会真正的触发交易。

当然，这个动画值得信任，毕竟起始点和终止点都有记录，在任何一个计算机上展示的动画，都应该是一样的，谁也无法篡改中间的值。

```javascript
   /**
     * @notice Creates a new stream funded by `msg.sender` and paid towards `recipient`.
     * @dev Throws if paused.
     *  Throws if the recipient is the zero address, the contract itself or the caller.
     *  Throws if the deposit is 0.
     *  Throws if the start time is before `block.timestamp`.
     *  Throws if the stop time is before the start time.
     *  Throws if the duration calculation has a math error.
     *  Throws if the deposit is smaller than the duration.
     *  Throws if the deposit is not a multiple of the duration.
     *  Throws if the rate calculation has a math error.
     *  Throws if the next stream id calculation has a math error.
     *  Throws if the contract is not allowed to transfer enough tokens.
     *  Throws if there is a token transfer failure.
     * @param recipient The address towards which the money is streamed.
     * @param deposit The amount of money to be streamed.
     * @param tokenAddress The ERC20 token to use as streaming currency.
     * @param startTime The unix timestamp for when the stream starts.
     * @param stopTime The unix timestamp for when the stream stops.
     * @return The uint256 id of the newly created stream.
     */
    function createStream(address recipient, uint256 deposit, address tokenAddress, uint256 startTime, uint256 stopTime)
        public
        whenNotPaused
        returns (uint256)
    {
        require(recipient != address(0x00), "stream to the zero address");
        require(recipient != address(this), "stream to the contract itself");
        require(recipient != msg.sender, "stream to the caller");
        require(deposit > 0, "deposit is zero");
        require(startTime >= block.timestamp, "start time before block.timestamp");
        require(stopTime > startTime, "stop time before the start time");

        CreateStreamLocalVars memory vars;
        (vars.mathErr, vars.duration) = subUInt(stopTime, startTime);
        /* `subUInt` can only return MathError.INTEGER_UNDERFLOW but we know `stopTime` is higher than `startTime`. */
        assert(vars.mathErr == MathError.NO_ERROR);

        /* Without this, the rate per second would be zero. */
        require(deposit >= vars.duration, "deposit smaller than time delta");

        /* This condition avoids dealing with remainders */
        require(deposit % vars.duration == 0, "deposit not multiple of time delta");

        (vars.mathErr, vars.ratePerSecond) = divUInt(deposit, vars.duration);
        /* `divUInt` can only return MathError.DIVISION_BY_ZERO but we know `duration` is not zero. */
        assert(vars.mathErr == MathError.NO_ERROR);

        /* Create and store the stream object. */
        uint256 streamId = nextStreamId;
        streams[streamId] = Types.Stream({
            remainingBalance: deposit,
            deposit: deposit,
            isEntity: true,
            ratePerSecond: vars.ratePerSecond,
            recipient: recipient,
            sender: msg.sender,
            startTime: startTime,
            stopTime: stopTime,
            tokenAddress: tokenAddress
        });

        /* Increment the next stream id. */
        (vars.mathErr, nextStreamId) = addUInt(nextStreamId, uint256(1));
        require(vars.mathErr == MathError.NO_ERROR, "next stream id calculation error");

        require(IERC20(tokenAddress).transferFrom(msg.sender, address(this), deposit), "token transfer failure");
        emit CreateStream(streamId, msg.sender, recipient, deposit, tokenAddress, startTime, stopTime);
        return streamId;
    }

    struct CreateCompoundingStreamLocalVars {
        MathError mathErr;
        uint256 shareSum;
        uint256 underlyingBalance;
        uint256 senderShareMantissa;
        uint256 recipientShareMantissa;
    }
```

如下是计算当前流式付款单id的账户余额的方法。

```javascript
 /**
     * @notice Returns the available funds for the given stream id and address.
     * @dev Throws if the id does not point to a valid stream.
     * @param streamId The id of the stream for which to query the balance.
     * @param who The address for which to query the balance.
     * @return The total funds allocated to `who` as uint256.
     */
    function balanceOf(uint256 streamId, address who) public view streamExists(streamId) returns (uint256 balance) {
        Types.Stream memory stream = streams[streamId];
        BalanceOfLocalVars memory vars;

        uint256 delta = deltaOf(streamId);
        (vars.mathErr, vars.recipientBalance) = mulUInt(delta, stream.ratePerSecond);
        require(vars.mathErr == MathError.NO_ERROR, "recipient balance calculation error");

        /*
         * If the stream `balance` does not equal `deposit`, it means there have been withdrawals.
         * We have to subtract the total amount withdrawn from the amount of money that has been
         * streamed until now.
         */
        if (stream.deposit > stream.remainingBalance) {
            (vars.mathErr, vars.withdrawalAmount) = subUInt(stream.deposit, stream.remainingBalance);
            assert(vars.mathErr == MathError.NO_ERROR);
            (vars.mathErr, vars.recipientBalance) = subUInt(vars.recipientBalance, vars.withdrawalAmount);
            /* `withdrawalAmount` cannot and should not be bigger than `recipientBalance`. */
            assert(vars.mathErr == MathError.NO_ERROR);
        }

        if (who == stream.recipient) return vars.recipientBalance;
        if (who == stream.sender) {
            (vars.mathErr, vars.senderBalance) = subUInt(stream.remainingBalance, vars.recipientBalance);
            /* `recipientBalance` cannot and should not be bigger than `remainingBalance`. */
            assert(vars.mathErr == MathError.NO_ERROR);
            return vars.senderBalance;
        }
        return 0;
    }

```

不难看出，流式付款协议的事件计算方法，因为以太坊默认没有系统时间函数，是以区块时间戳为关键计算变量的。

```javascript
   /**
     * @notice Returns either the delta in seconds between `block.timestamp` and `startTime` or
     *  between `stopTime` and `startTime, whichever is smaller. If `block.timestamp` is before
     *  `startTime`, it returns 0.
     * @dev Throws if the id does not point to a valid stream.
     * @param streamId The id of the stream for which to query the delta.
     * @return The time delta in seconds.
     */
    function deltaOf(uint256 streamId) public view streamExists(streamId) returns (uint256 delta) {
        Types.Stream memory stream = streams[streamId];
        if (block.timestamp <= stream.startTime) return 0;
        if (block.timestamp < stream.stopTime) return block.timestamp - stream.startTime;
        return stream.stopTime - stream.startTime;
    }
```

如下是取现的实现方式，本质上是调用了ERC20上的`transfer`方法，把流式协议中对应的金额提取到对应收款人账户下，取现需收款人主动触发交易。

```javascript
  /**
     * @notice Makes the withdrawal to the recipient of the stream.
     * @dev If the stream balance has been depleted to 0, the stream object is deleted
     *  to save gas and optimise contract storage.
     *  Throws if the stream balance calculation has a math error.
     *  Throws if there is a token transfer failure.
     */
    function withdrawFromStreamInternal(uint256 streamId, uint256 amount) internal {
        Types.Stream memory stream = streams[streamId];
        WithdrawFromStreamInternalLocalVars memory vars;
        (vars.mathErr, streams[streamId].remainingBalance) = subUInt(stream.remainingBalance, amount);
        /**
         * `subUInt` can only return MathError.INTEGER_UNDERFLOW but we know that `remainingBalance` is at least
         * as big as `amount`. See the `require` check in `withdrawFromInternal`.
         */
        assert(vars.mathErr == MathError.NO_ERROR);

        if (streams[streamId].remainingBalance == 0) delete streams[streamId];

        require(IERC20(stream.tokenAddress).transfer(stream.recipient, amount), "token transfer failure");
        emit WithdrawFromStream(streamId, stream.recipient, amount);
    }

```

如下是Sablier上的Events记录，真实的例子。

![](/content/images/sablier/3.png)



## 总结

Sablier给我们带来了一种全新的理念，付款可以达到流式效果，分析源码，不难得出，实际上在区块链的交易上，并没有每一秒都触发一次`transfer`转账交易，而是在流式付款协议创建、结束、提现的时候，记录了关键的时间节点，其实，只有提现的时间，才是真正完成转账的时间。

值得注意的是，每一次提现都会带来交易手续费，这恐怕是无法避免的缺点，ERC1620标准将这种缺点降低到了最低，使用“虚假的前端展示”的流式转账，既保障了“流式”，又保障了尽可能少的手续费，也保障了交易的真实性。
