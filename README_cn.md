# dice
eosbet骰子游戏
骰子
该合同在两名玩家之间实现简单的DICE游戏，获胜率为50/50。

在玩所有玩家之前，将资金存入他们的@dice账户，就像@exchange合约一样

玩家1建议下注1个EOS并提交SHA256（secret1）
玩家2建议下注1个EOS并提交SHA256（secret2）
因为玩家1和2下注相等的数量，他们的订单匹配并且游戏开始。

玩家揭露他们的秘密
5分钟的截止日期开始，第一个显示自动获胜，除非其他玩家显示
其他玩家显示并根据sha256（cat（secret1，secret2））的值选择并支付赢家
在截止日期之后，任何人都可以触发默认索赔和奖励
界面开发人员的经济激励
此游戏的一个变体是添加关于优惠创建的额外信息，当玩家获胜时将获得佣金。有了这个佣金，服务提供商就可以继续及时执行游戏以及在这个游戏之上提供高质量和有趣的界面。

其他游戏
这个相同的基本模型可用于构建更强大的游戏。

潜在的漏洞
块生产者可以排除披露交易
失败者可能会迫使获胜者等待5分钟以获得奖励
服务提供商可能无法代表您自动显示
您可能会在游戏中失去互联网连接
如果秘密泄露得太快，那么一个街区重组可能会导致一些骚乱
@dice可以通过拒绝显示来保护用户，直到游戏创建不可逆转（最多约45秒）
用户可以通过决定需要多少确认来自行承担风险
对于少量，它可能无关紧要
在DPOS链的正常运行下，几乎没有任何链重组
使用cleos的示例游戏会话
先决条件
电子钱包必须解锁并至少拥有以下私钥

5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3 5Jmsawgsp1tQ3GD6JyGCwy1dcvqKZgX6ugMVMdjirx85iv5VyPR

上传bios合同
cleos set contract eosio build / contracts / eosio.bios -p eosio
Ceate eosio.token帐户
cleos create account eosio eosio.token EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4 EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4
将eosio.token合同设置为eosio.token帐户
cleos set contract eosio.token build / contracts / eosio.token -p eosio.token
创建骰子帐户
cleos create account eosio dice EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4 EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4
将骰子合同设置为骰子帐户
cleos 设置合同骰子建立/合同/骰子-p骰子
创建本机EOS令牌
cleos push action eosio.token create ' [“eosio”，“1000000000.0000 EOS”，0,0,0] '- p eosio.token
创建alice帐户
cleos create account eosio alice EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4 EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4
创建bob帐户
cleos create account eosio bob EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4 EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4
向爱丽丝发出1000 EOS
cleos push action eosio.token issue ' [“alice”，“1000.0000 EOS”，“”] ' - p eosio
发出1000 EOS到bob
cleos push action eosio.token issue ' [“bob”，“1000.0000 EOS”，“”] ' - p eosio
允许骰子合同代表alice进行转账(存款)
cleos set account permission alice active '{"threshold": 1,"keys": [{"key": "EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4","weight": 1}],"accounts": [{"permission":{"actor":"dice","permission":"active"},"weight":1}]}' owner -p alice
允许骰子合同代表bob进行转账(保证金)
cleos set account permission bob active '{"threshold": 1,"keys": [{"key": "EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4","weight": 1}],"accounts": [{"permission":{"actor":"dice","permission":"active"},"weight":1}]}' owner -p bob
Alice将100 EOS存入骰子合同
cleos push action dice deposit ' [“alice”，“100.0000 EOS”] '- p alice
Bob将100 EOS存入骰子合同
cleos push action dice deposit ' [“bob”，“100.0000 EOS”] ' - p bob
爱丽丝生成一个秘密
openssl rand 32 
-hex 28349b1d4bcdc9905e4ef9719019e55743c84efa0c5e9a0b077f0b54fcd84905
爱丽丝生成sha256（秘密）
echo -n '28349b1d4bcdc9905e4ef9719019e55743c84efa0c5e9a0b077f0b54fcd84905' | xxd -r -p | sha256sum -b | awk '{print $1}'
d533f24d6f28ddcef3f066474f7b8355383e485681ba8e793e037f5cf36e4883
Alice下注3个EOS
cleos push action dice offerbet '[ "3.0000 EOS", "alice", "d533f24d6f28ddcef3f066474f7b8355383e485681ba8e793e037f5cf36e4883" ]' -p alice
鲍勃生成一个秘密
openssl rand 32 
-hex 15fe76d25e124b08feb835f12e00a879bd15666a33786e64b655891fba7d6c12
鲍勃生成sha256（秘密）
echo -n '15fe76d25e124b08feb835f12e00a879bd15666a33786e64b655891fba7d6c12' | xxd -r -p | sha256sum -b | awk '{print $1}'
50ed53fcdaf27f88d51ea4e835b1055efe779bb87e6cfdff47d28c88ffb27129

鲍勃也下注3个EOS（游戏开始）
cleos push action dice offerbet ' [“3.0000 EOS”，“bob”，“50ed53fcdaf27f88d51ea4e835b1055efe779bb87e6cfdff47d28c88ffb27129”] '- p bob
游戏开始后立即进行骰子合约表
cleos get table dice dice account
{
  "rows": [{
      "owner": "alice",
      "eos_balance": "97.0000 EOS",
      "open_offers": 0,
      "open_games": 1
    },{
      "owner": "bob",
      "eos_balance": "97.0000 EOS",
      "open_offers": 0,
      "open_games": 1
    }
  ],
  "more": false
}
cleos get table dice dice game
{
  "rows": [{
      "id": 1,
      "bet": "3.0000 EOS",
      "deadline": "1970-01-01T00:00:00",
      "player1": {
        "commitment": "d533f24d6f28ddcef3f066474f7b8355383e485681ba8e793e037f5cf36e4883",
        "reveal": "0000000000000000000000000000000000000000000000000000000000000000"
      },
      "player2": {
        "commitment": "50ed53fcdaf27f88d51ea4e835b1055efe779bb87e6cfdff47d28c88ffb27129",
        "reveal": "0000000000000000000000000000000000000000000000000000000000000000"
      }
    }
  ],
  "more": false
}
鲍勃透露了他的秘密
cleos push action dice reveal '[ "50ed53fcdaf27f88d51ea4e835b1055efe779bb87e6cfdff47d28c88ffb27129", "15fe76d25e124b08feb835f12e00a879bd15666a33786e64b655891fba7d6c12" ]' -p bob
鲍勃透露后的游戏表(现在游戏有一个爱丽丝透露的最后期限)
cleos get table dice dice game
{
  "rows": [{
      "id": 1,
      "bet": "3.0000 EOS",
      "deadline": "2018-04-17T07:45:49",
      "player1": {
        "commitment": "d533f24d6f28ddcef3f066474f7b8355383e485681ba8e793e037f5cf36e4883",
        "reveal": "0000000000000000000000000000000000000000000000000000000000000000"
      },
      "player2": {
        "commitment": "50ed53fcdaf27f88d51ea4e835b1055efe779bb87e6cfdff47d28c88ffb27129",
        "reveal": "15fe76d25e124b08feb835f12e00a879bd15666a33786e64b655891fba7d6c12"
      }
    }
  ],
  "more": false
}
爱丽丝揭示了她的秘密（获胜者已确定，游戏被删除）
cleos push action dice reveal '[ "d533f24d6f28ddcef3f066474f7b8355383e485681ba8e793e037f5cf36e4883", "28349b1d4bcdc9905e4ef9719019e55743c84efa0c5e9a0b077f0b54fcd84905" ]' -p alice

比赛结束后账户余额
 cleos get table dice dice account
{
  "rows": [{
      "owner": "alice",
      "eos_balance": "103.0000 EOS",
      "open_offers": 0,
      "open_games": 0
    },{
      "owner": "bob",
      "eos_balance": "97.0000 EOS",
      "open_offers": 0,
      "open_games": 0
    }
  ],
  "more": false
}
爱丽丝退出她的骰子帐户103 EOS
cleos push action dice withdraw ' [“alice”，“103.0000 EOS”] '- p alice
退出后爱丽丝的平衡
cleos get currency balance eosio.token alice eos
1003.0000 EOS


