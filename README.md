此方案旨在验证 Sidecar 注入与否时, Pod 间的文件传输效率的对比
This demo allows transfer different sizes of files between pods in K8s with the aim of comparing the performance of Istio sidecar.

方案相关的程序已打包成 Chart，可通过 Helm 进行部署。
Demo has been packaged as a Chart to be able to easily installing with Helm.

部署后会生成三个 Deployment，一个 go-httpbin, 两个 upload-file-demo (其中一个注入 sidecar ), 从 go-httpbin 发送 Curl 请求向 upload-file-demo 传输文件。
Chart we deploy three Deployments. one is called go-httpbin, the other two are all upload-file-demo(one of them is sidecar injected). You can use Curl to tranfer files from go-httpbin pod to upload-file-demo pod.

镜像	
build-harbor.alauda.cn/test/upload-file-demo:v1.0.0
或
maying0104/upload-file-demo:v1.0.0


镜像	
build-harbor.alauda.cn/test/go-httpbin:1.2-alpine3.13
或
maying0104/go-httpbin:1.2-alpine3.13


操作步骤
将上述物料包中的 Chart 同步到环境内 Install

''helm -n {NAMESPACE} install upload-file-demo upload-file-demo-0.1.0.tgz''
查看部署出来的资源

''kubectl -n {NAMESPACE} get all''
复制目标传输文件进 go-httpbin 的 pod 内.


''kubectl -n {NAMESPACE} cp {目标文件路径及文件名与后缀} {go-httpbin的pod的全名}:/{目地路径}''
获取文件传输应用的内部路由信息

''kubectl -n {NAMESPACE} get svc''
进入 go-httpbin 的 pod 内部

''kubectl -n {NAMESPACE} exec -it {go-httpbin的pod的全名} -- sh''
传输文件

''curl -i -X POST -F "file=@{被传输的文件路径及名称与后缀}" "http://{步骤4中的第一个红框}:{步骤4中的第二个红框}/upload"''
''curl -i -X POST -F "file=@{被传输的文件路径及名称与后缀}" "http://{步骤4中的第一个红框下面的ip}:{步骤4中的第二个红框下面的port}/upload"''
查看 upload-file-demo 的 pod 日志, 可以看到请求时长

''kubectl -n {NAMESPACE} logs {pod名称} -f''
