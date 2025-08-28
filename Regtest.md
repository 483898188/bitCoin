1. 创建专门的regest目录， 避免与原来的testnet冲突。

   ```shell
   mkdir -p ~/.bitcoin/regtest
   ./bitcoind -regtest -datadir="$HOME/.bitcoin/regtest" -daemon -rpcuser=bitcoinrpc -rpcpassword=123456
   
   ```

2. 创建钱包

   ```shell
   ./bitcoin-cli -regtest -datadir="$HOME/.bitcoin/regtest" createwallet mywallet
   
   ```

3. 生成新地址

   ```shell
   ./bitcoin-cli -regtest -rpcwallet=mywallet -datadir="$HOME/.bitcoin/regtest" getnewaddress
   
   ```

4. 快速生成新区块(挖矿)

```shell
./bitcoin-cli -regtest -rpcwallet=mywallet -datadir="$HOME/.bitcoin/regtest" generatetoaddress 101 <你的地址>

# <你的地址> 替换为第二步生成的地址

```

5. 查看区块和余额

```shell
# 当前区块高度
./bitcoin-cli -regtest -datadir="$HOME/.bitcoin/regtest" getblockcount

# 钱包余额
./bitcoin-cli -regtest -rpcwallet=mywallet -datadir="$HOME/.bitcoin/regtest" getbalance

```



6. 快速生成regest链

```shell
#!/bin/bash
# ==============================
# Bitcoin Regtest 一键启动脚本
# 自动启动节点、创建钱包、生成 101 个区块、显示余额
# ==============================

BITCOIND="$HOME/bitcoin/build/bin/bitcoind"
BITCOIN_CLI="$HOME/bitcoin/build/bin/bitcoin-cli"
REGTEST_DIR="$HOME/.bitcoin/regtest"
WALLET_NAME="mywallet"

RPC_USER="bitcoinrpc"
RPC_PASSWORD="123456"

# -----------------------------
# 启动 Regtest 节点
# -----------------------------
mkdir -p "$REGTEST_DIR"
echo "启动 Bitcoin Regtest 节点..."
$BITCOIND -regtest -daemon -datadir="$REGTEST_DIR" \
          -rpcuser="$RPC_USER" -rpcpassword="$RPC_PASSWORD"

# 等待 RPC 可用
echo -n "等待 RPC 可用..."
until $BITCOIN_CLI -regtest -datadir="$REGTEST_DIR" getblockcount >/dev/null 2>&1; do
    sleep 1
    echo -n "."
done
echo " RPC 已可用"

# -----------------------------
# 创建钱包（如果不存在）
# -----------------------------
WALLETS=$($BITCOIN_CLI -regtest -datadir="$REGTEST_DIR" listwallets)
if ! echo "$WALLETS" | grep -q "\"$WALLET_NAME\""; then
    echo "创建钱包: $WALLET_NAME"
    $BITCOIN_CLI -regtest -datadir="$REGTEST_DIR" createwallet "$WALLET_NAME"
else
    echo "钱包 $WALLET_NAME 已存在，直接使用"
fi

# -----------------------------
# 生成新地址
# -----------------------------
ADDRESS=$($BITCOIN_CLI -regtest -rpcwallet="$WALLET_NAME" -datadir="$REGTEST_DIR" getnewaddress)
echo "测试钱包地址: $ADDRESS"

# -----------------------------
# 快速生成新区块（101个）
# -----------------------------
echo "生成 101 个区块，激活测试币余额..."
$BITCOIN_CLI -regtest -rpcwallet="$WALLET_NAME" -datadir="$REGTEST_DIR" generatetoaddress 101 "$ADDRESS"
echo "生成完成"

# -----------------------------
# 显示区块高度和钱包余额
# -----------------------------
BLOCKS=$($BITCOIN_CLI -regtest -datadir="$REGTEST_DIR" getblockcount)
BALANCE=$($BITCOIN_CLI -regtest -rpcwallet="$WALLET_NAME" -datadir="$REGTEST_DIR" getbalance)

echo "当前区块高度: $BLOCKS"
echo "钱包余额: $BALANCE BTC"

# -----------------------------
# 提示下一步操作
# -----------------------------
echo "现在你可以使用该钱包进行交易或测试开发功能。"
echo "例如："
echo "  - 发送交易: ./bitcoin-cli -regtest -rpcwallet=$WALLET_NAME sendtoaddress <address> <amount>"
echo "  - 查询区块: ./bitcoin-cli -regtest -getblock <blockhash>"

```

