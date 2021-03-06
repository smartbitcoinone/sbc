# SBC节点规则

SBC架构中区块产生是以21个区块为一个周期。在每个出块周期开始时，21个区块生产者会被投票选出。前20名出块者首选自动选出，第21个出块者按所得投票数目对应概率选出。所选择的生产者会根据从块时间导出的伪随机数进行混合。以便保证出块者之间的连接尽量平衡。

每次生成一个块时，SBC系统都会奖励该区块生成者一个新的令牌。所创建的令牌数量由所有区块生成者所公布的期望报酬的中位数决定。SBC系统可能被配置为限制区块生成者所得奖励上限，这样，令牌供应的年总增长不超过5%。

简而言之： 一，21个矿工（即21个超级节点） 二，sbc持有人投票产生21个超级节点 三，21个节点当选人有奖励，而且奖励丰厚

# 节点奖励规则

1. SBC发行总量是100亿。
1. 每年奖励发行总量的1%。
2. 每0.5秒产一个块儿，每天生成的节点块儿数是`2*24*3600`， 每年生成的块儿数是`52*7*24*2*3600`
3. 最快每天领取一次奖励。

## 1%增发分配策略
1. 增发的25%的奖励放在产快奖励池中。
2. 增发的75%的奖励放在投票奖励池中。

## bp领取规则
1. bp产块的收益规则：

	```
	producer_per_block_pay = (gstate.perblock_bucket * prod.unpaid_blocks) / gstate.total_unpaid_blocks
	bp产快收益 = 产快收益池 * bp未获取收益的产块儿数 / 所有bp未获取收益的产块儿数
	```
2. bp投票收益规则：
	```
	prod_vote_pay = (g.vote_bucket*p.total_votes) / g.total_produver_vote_weight
	bp投票收益 = (总的投票奖励池 * 该bp总的投票数) / 总的bp投票权重 
	```


# SBC的投票规则

1. 按照eos主网的快照文件来分发相应的sbc。  
2. 收到SBC后（包含交易所也会收到，交易所收到SBC后可以随时提出来），到官方钱包进行投票操作。  
3. 一个SBC可以有30票，但是每个SBC投给每个候选节点最多只能投1票。  
4. 投完票后，相关SBC将进入一个为期3天的锁定期，锁定期结束，票可取消重新投，立即生效，或者提出到交易所卖掉自由流动。  
5. 第一轮投票很快结束后超级节点陆续开始替代创世节点开始运作，出块。  
6. 超级节点将轮流出块，21个节点，目前每0.5秒出一个块，每个节点出6个块。  
7. 一轮之后，也就是63秒之后，重新计算得票数，这时候产生新的超级节点。  
8. SBC是流动的，不断的流入投票、取消流出，所以每个候选节点每轮的选票都会改变。  
9. 投票一直不断的运行下去。

# SBC BP机器配置：

最低配置：

内存：32G
硬盘：512G
CPU：4核

推荐配置：
内存：48G或以上
硬盘：1T或以上
CPU：4核

# SBC BP推荐架构

![node_struct](./images/node-struct.png)

# BP节点安全配置建议

1. RPC 安全
	1. 屏蔽 RPC
	如无必要，建议禁止 RPC 对外访问，config.ini配置内容如下：
	配置为空值http-server-address =
	注释https-server-address
	1. 开启 SSL
	如果确实需要对外提供 RPC 服务，建议禁用 HTTP 协议，使用 HTTPS，config.ini配置内容如下：
	注释http-server-address，或者配置为127.0.0.1:8888
	配置https-server-address为0.0.0.0:443
	配置https-certificate-chain-file 和 https-private-key-file 为证书链文件路径和私钥文件路径，注意两个文件格式必须为 PEM
	配置证书链文件和私钥文件权限为 600
	1. 禁用 wallet_plugin 和 wallet_api_plugin
	在对外提供 RPC 服务的场景下，一定不要加载 wallet_plugin 和 wallet_api_plugin。如果加载了wallet_plugin 和 wallet_api_plugin，攻击者就可以通过 RPC API /v1/wallet/list_keys 获取已解锁账户的私钥。此外，攻击者还可以恶意循环调用/v1/wallet/lock_all使节点上的账户无法解锁。
	1. 禁用producer_api_plugin
	在对外提供 RPC 服务的场景下，一定不要加载 producer_api_plugin。如果加载了producer_api_plugin，攻击者就可以通过 RPC API /v1/producer/pause 远程控制节点停止生产。

