This demo allows transfer different sizes of files between pods in K8s with the aim of comparing the performance of Istio sidecar.

Demo has been packaged as a Chart to be able to easily installing with Helm.

Chart we deploy three Deployments. one is called go-httpbin, the other two are all upload-file-demo(one of them is sidecar injected). You can use Curl to tranfer files from go-httpbin pod to upload-file-demo pod.

Image:
maying0104/upload-file-demo:v1.0.0


Image	
maying0104/go-httpbin:1.2-alpine3.13


STEPS:
1. Download the .tgz file and install this Chart

`helm -n {NAMESPACE} install upload-file-demo upload-file-demo-0.1.0.tgz`

2. Confirm all the resources has been successfully deployed 

`kubectl -n {NAMESPACE} get all`

3. Copy files need to be tranferred into Pod

`kubectl -n {NAMESPACE} cp {目标文件路径及文件名与后缀} {go-httpbin的pod的全名}:/{目地路径}`

4. Get the Service address

`kubectl -n {NAMESPACE} get svc`

5. Exec into the go-httpbin Pod

`kubectl -n {NAMESPACE} exec -it {go-httpbin的pod的全名} -- sh`

6. Transfer the file

`curl -i -X POST -F "file=@{被传输的文件路径及名称与后缀}" "http://{步骤4中的第一个红框}:{步骤4中的第二个红框}/upload"`

`curl -i -X POST -F "file=@{被传输的文件路径及名称与后缀}" "http://{步骤4中的第一个红框下面的ip}:{步骤4中的第二个红框下面的port}/upload"`

7. Logging of the upload-file-demo pod will print the request timing info.

`kubectl -n {NAMESPACE} logs {pod名称} -f`
