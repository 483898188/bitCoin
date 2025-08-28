1.  创建两个钱包

   ```shell
   # 创建收款钱包
   ./bitcoin-cli -regtest -datadir="$HOME/.bitcoin/regtest" createwallet receiver
   
   # 创建付款钱包（如果还没创建）
   ./bitcoin-cli -regtest -datadir="$HOME/.bitcoin/regtest" createwallet sender
   
   ```

   

2.  生成地址

   ```shell
   # 付款钱包地址（用来接收找零）
   SENDER_ADDRESS=$(./bitcoin-cli -regtest -rpcwallet=sender -datadir="$HOME/.bitcoin/regtest" getnewaddress)
   
   # 收款钱包地址
   RECEIVER_ADDRESS=$(./bitcoin-cli -regtest -rpcwallet=receiver -datadir="$HOME/.bitcoin/regtest" getnewaddress)
   
   ```

3.  给付款钱包生成初始币

   ```shell
   ./bitcoin-cli -regtest -rpcwallet=sender -datadir="$HOME/.bitcoin/regtest" generatetoaddress 101 "$SENDER_ADDRESS"
   
   ```

4.  查看余额

   ```shell
   ./bitcoin-cli -regtest -rpcwallet=sender -datadir="$HOME/.bitcoin/regtest" getbalance
   
   ```

5. 发送交易

   ```shell
   TXID=$(./bitcoin-cli -regtest -rpcwallet=sender -datadir="$HOME/.bitcoin/regtest" sendtoaddress "$RECEIVER_ADDRESS" 10)
   echo "交易 TXID: $TXID"
   
   ```



6.  生成区块确认交易 

```shell
./bitcoin-cli -regtest -rpcwallet=sender -datadir="$HOME/.bitcoin/regtest" generatetoaddress 1 "$SENDER_ADDRESS"

# 在 Regtest 下，交易只有打包进区块才算确认
# 生成 1 个新区块确认交易
```

7. 查看区块高度和确认数

```shell
./bitcoin-cli -regtest -datadir="$HOME/.bitcoin/regtest" getblockcount
./bitcoin-cli -regtest -rpcwallet=receiver -datadir="$HOME/.bitcoin/regtest" getbalance

```

8. 查询交易详情

```shell
./bitcoin-cli -regtest -datadir="$HOME/.bitcoin/regtest" getrawtransaction "$TXID" true

```



