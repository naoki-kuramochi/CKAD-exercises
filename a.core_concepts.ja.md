# コアコンセプト(13％)

kubernetes.io>ドキュメント>リファレンス> kubectl CLI> [kubectlチートシート](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

kubernetes.io> Documentation> Tasks> Monitoring、Logging、and Debugging> [Get a Shell to a Running Container](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/)

kubernetes.io> Documentation> Tasks> Access Applications in a Cluster> [Configure Access to Multiple Clusters](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)

kubernetes.io>ドキュメント>タスク>クラスタ内のアプリケーションへのアクセス> [クラスタへのアクセス](https://kubernetes.io/docs/tasks/access-application-cluster/access-cluster/)
API 

kubernetes.io を使用>ドキュメント>タスク>クラスター内のアプリケーションにアクセス> [クラスター内のアプリケーションにアクセスするためにポート転送を使用](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/)

### 「mynamespace」という名前空間と、この名前空間にnginxというnginx imageを含むポッドを作成します

<details> <summary> show </summary> 
<p> 

```bash 
kubectl create namespace mynamespace 
kubectl run nginx --image=nginx --restart=Never -n mynamespace 
``` 
</p> 
</details>

### YAMLを使用して説明したポッドを作成します

<details> <summary> show </summary> 
<p> 
次のコマンドでYAMLを簡単に生成します:

```bash
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml> pod.yaml 
``` 

```bash 
cat pod.yaml 
``` 

```yaml 
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
``` 

```bash 
kubectl create -f pod.yaml -n mynamespace 
``` 

または、1行で実行できます

```bash 
kubectl run nginx --image = nginx --restart=Never --dry-run -o yaml | kubectl create -n mynamespace -f -
``` 
</p> 
<p>
`--dry-run`を指定すると最小構成のマニフェストを吐き出してくれる様になる
</p>

</details> 

### コマンド「env」を実行するbusyboxポッドを作成します(kubectlコマンドを使用)。それを実行して、出力

<details> <summary> show </summary> 
<p> 

```bash 
kubectl run busybox --image=busybox --command --restart=Never -it -- env ＃-it出力を確認するには
＃または、-it 
kubectl run busybox --image=busybox --command --restart=Never -- env 
＃なしで実行してから、
kubectl logs busybox 
``` 

</p> 
</details>を記録します

### コマンド「env」を実行するbusyboxポッドを(YAMLを使用して)作成します。それを実行して、出力

<details> <summary> show </summary> 
<p> 

```bash 
＃このコマンドでYAMLテンプレートを作成します
kubectl run busybox --image=busybox --restart=Never --dry-run -o yaml --command -- env > envpod.yaml 
＃参照してください
cat envpod.yaml 
``` 

``` YAML 
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: busybox
  name: busybox
spec:
  containers:
  - command:
    - env
    image: busybox
    name: busybox
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
``` 

``` bash 
＃apply it and logs the log 
kubectl apply -f envpod.yaml 
kubectl logs busybox 
``` 
</p> 
</details> 

### 'myns'という新しい名前空間のYAMLを作成せずに取得します

<details> <summary> show </summary> 
<p> 

```bash 
kubectl create namespace myns -o yaml --dry-run
``` 

</p> 
</details> 

### 新しいResourceQuotaためのYAMLは、1つのCPUのハード制限に'myrqを'と呼ばれる取得作成せずに1Gメモリと2つのポッド

see: https://cstoku.dev/posts/2018/k8sdojo-15/#resourcequota
<details> <summary> show </summary> 
<p> 

```bash 
kubectl create quota myrq --hard=cpu=1,memory=1G,pods=2 --dry-run -o yaml
``` 

</p> 
</details> 

### すべての名前空間のポッドを取得

<details> <summary> show </summary> 
<p> 

```bash 
kubectl get po --all-namespaces
``` 

</p> 
</details > 

### nginxと呼ばれるイメージnginxでポッドを作成し、ポート80でトラフィックを許可します

<details> <summary> show </summary> 
<p> 

```bash 
kubectl run nginx --image=nginx --restart=Never --port=80
``` 

</p> 
</details>

### ポッドのimageをnginx:1.7.1に変更します。イメージがプルされるとすぐにポッドが強制終了され、再作成されることを確認します

<details> <summary> show </summary> 
<p> 

```bash
# kubectl set image POD/POD_NAME CONTAINER_NAME=IMAGE_NAME:TAG
kubectl set image pod/nginx nginx=nginx:1.7.1
kubectl describe po nginx # you will see an event 'Container will be killed and recreated'
kubectl get po nginx -w # watch it
``` 
*注意*:実行すると、ポッドのイメージを確認できます

```bash 
kubectl get po nginx -o jsonpath='{.spec.containers[].image}{"\n"}'
``` 

</p> 
</details> 

### nginxポッドのIPを作成一つ前の手順、

<details> <summary> show </summary> 
<p> 

```bash 
kubectl get po -o wide # get the IP, will be something like '10.1.1.131'
# create a temp busybox pod
kubectl run busybox --image=busybox --rm -it --restart=Never -- wget -O- 10.1.1.131:80
``` 

また、より高度なオプションを試すこともできます:

``` bash 
# Get IP of the nginx pod
NGINX_IP=$(kubectl get pod nginx -o jsonpath='{.status.podIP}')
# create a temp busybox pod
kubectl run busybox --image=busybox --env="NGINX_IP=$NGINX_IP" --rm -it --restart=Never -- wget -O- $NGINX_IP:80
``` 

</p> 
</details> 

### ポッドのYAMLを取得

<details> <summary> show </summary> 
<p>

```bash 
kubectl get po nginx -o yaml
# or
kubectl get po nginx -oyaml
# or
kubectl get po nginx --output yaml
# or
kubectl get po nginx --output=yaml
``` 

</p> 
</details> 

### 潜在的な問題の詳細(ポッドが開始されていないなど)を含む、ポッドに関する情報を取得します

<details> <summary> show </summary> 
<p> 

```bash 
kubectl describe po nginx
``` 

</p> 
</details> 

### ポッドログを取得

<details> <summary> show </summary> 
<p> 

```bash 
kubectl logs nginx
``` 

</p> 
</details>

### ポッドがクラッシュして再起動した場合、以前のインスタンスに関するログを取得します

<details> <summary> show </summary> 
<p> 

```bash 
kubectl logs nginx -p
``` 

</p> 
</details> 

### nginxポッドで単純なシェルを実行します

<details> <summary> show </summary> 
<p> 

```bash 
kubectl exec -it nginx -- /bin/sh
``` 

</p> 
</details> 

### 'hello world'をエコーし​​、終了するbusyboxポッドを作成します

<details> <summary> show </summary>
<p> 

```bash 
kubectl run busybox --image=busybox -it --restart=Never -- echo 'hello world'
# or
kubectl run busybox --image=busybox -it --restart=Never -- /bin/sh -c 'echo hello world'
``` 

</p> 
</details> 

### 同じことを行いますが、ポッドがあります完了時に自動的に削除されます

<details> <summary> show </summary> 
<p> 

```bash 
kubectl run busybox --image=busybox -it --rm --restart=Never -- /bin/sh -c 'echo hello world'
kubectl get po # nowhere to be found :)
``` 

</p> 
</details> 

### nginxポッドを作成し、env値を' var1=val1 'として設定します。ポッド内の環境値の存在を確認してください

<details> <summary> show </summary>
<p>

```bash
kubectl run nginx --image=nginx --restart=Never --env=var1=val1
# then
kubectl exec -it nginx -- env
# or
kubectl describe po nginx | grep val1
# or
kubectl run nginx --restart=Never --image=nginx --env=var1=val1 -it --rm -- env
``` 

</p> 
</details>
