测试环境
=
* 使用Caliper来测试fabric环境。
* 首先先配置好fabric环境并启动
* 本例环境如下：
1orderer、3peer、kafka共识、无ca、native启动（非docker）。
| IP       | 节点         | 域名|
|:-----------:| :-------------:| :-------------:|
| 10.254.186.164|orderer  |  orderer.example.com|
| 10.254.186.164 | peer |peer0.org1.example.com  |
| 10.254.247.165 | peer |peer1.org1.example.com   |
| 10.254.207.154 | peer  |peer0.org2.example.com  |
| 10.254.186.164 | kafka |无需 |

测试流程
=
1、部署好fabric被测环境
2、先下载caliper https://github.com/hyperledger/caliper
3、node.js 8.x node-gyp docker docker-cpmpose 环境
4、caliper安装依赖时注意 npm install grpc@1.10.1 fabric-ca-client@1.1.0 fabric-client@1.1.0 版本
5、把config-test.json 和 fabric-test.json放到benchmark/simple/ 文件夹下
6、把对应fabric部署的证书文件替换fabric-test.json 修改config-test.json对应的测试配置
7、执行node ./benchmark/simple/main.js -c ./config-test.json -n ./fabric-test.json
8、如果要测试自己的chaincode，需要修改config-test.json 和 fabric-test.json文件，并实现init/run/end三个函数。

注意：
-
由于是native非doceker环境，测试完成后不注销 所以需要改动代码。
在区块链网络配置中chaincodes项加deployed参数，并且在src/fabric/install-chaincode.js文件以及src/fabric/instantiate-chaincode.js文件中加入相应的处理逻辑：
```go
   if(chaincode.deployed) {
       return Promise.resolve();
   }
```
在安装初始化之前chaincodes的deployed为false:
```
    "chaincodes": [{"id": "simple11", "path": "contract/fabric/simple/go", "language":"golang", "version": "v2", "channel": "mychannel","deployed": false}],
```
安装成功后再执行的时候 chaincodes的deployed为true：
```
 "chaincodes": [{"id": "simple11", "path": "contract/fabric/simple/go", "language":"golang", "version": "v2", "channel": "mychannel","deployed": true}],
```
