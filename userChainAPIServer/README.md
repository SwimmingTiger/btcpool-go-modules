# User Chain API Server
由两个模块合并而来，请看具体子模块的介绍：
* [Switcher API Server](switcherAPIServer/)
  提供触发 Stratum 切换的API
* [Init User Coin](initUserCoin/)
  初始化zookeeper里的用户币种记录

## 构建
```
go get -u github.com/btccom/btcpool-go-modules/userChainAPIServer
```

## 运行
```
cp config.default.json config.json
$GOPATH/bin/userChainAPIServer --config config.json --logtostderr -v 2
```

# Docker

## 构建
```
cd btcpool-go-modules/userChainAPIServer
docker build -t btcpool-user-chain-api-server -f Dockerfile ..
```

## 运行
```
docker run -it --rm --network=host \
  -e AvailableCoins='ubtc,btc,bcc,auto' \
  -e UserListAPI_ubtc='http://localhost:8000/userlist-ubtc.php' \
  -e UserListAPI_btc='http://localhost:8000/userlist-btc.php' \
  -e UserListAPI_bcc='http://localhost:8000/userlist-bch.php' \
  -e ZKBroker='10.0.1.176:2181,10.0.1.175:2181,10.0.1.174:2181' \
  -e ZKSwitcherWatchDir='/stratumSwitcher/btcbcc/' \
  -e EnableAPIServer='true' \
  -e APIUser='switchapi' \
  -e APIPassword='admin' \
  -e ListenAddr='0.0.0.0:8082' \
  -e EnableCronJob='true' \
  -e UserCoinMapURL='http://localhost:8000/usercoin.php' \
  btcpool-user-chain-api-server:latest -logtostderr -v 2

# 守护进程
docker run -it --name user-chain-api-server --network=host --restart always -d \
  -e AvailableCoins='ubtc,btc,bcc,auto' \
  -e UserListAPI_ubtc='http://localhost:8000/userlist-ubtc.php' \
  -e UserListAPI_btc='http://localhost:8000/userlist-btc.php' \
  -e UserListAPI_bcc='http://localhost:8000/userlist-bch.php' \
  -e ZKBroker='10.0.1.176:2181,10.0.1.175:2181,10.0.1.174:2181' \
  -e ZKSwitcherWatchDir='/stratumSwitcher/btcbcc/' \
  -e EnableAPIServer='true' \
  -e APIUser='switchapi' \
  -e APIPassword='admin' \
  -e ListenAddr='0.0.0.0:8082' \
  -e EnableCronJob='true' \
  -e UserCoinMapURL='http://localhost:8000/usercoin.php' \
  btcpool-user-chain-api-server:latest -logtostderr -v 2
```

币种`auto`可选，用于机枪切换，不需要实际配置到`sserver`的`chains`里。`sserver`只需要打开机枪切换功能（`auto_switch_chain`）即可识别币种`auto`。
