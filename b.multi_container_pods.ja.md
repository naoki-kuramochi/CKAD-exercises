![](https://gaforgithub.azurewebsites.net/api?repo=CKAD-exercises/multi_container&empty)
# multi-container pods (10%)

### 2つのコンテナでポッドを作成し、両方にimage busyboxとコマンド "echo hello; sleep 3600"を含めます。 2番目のコンテナーに接続し、「ls」を実行します 。
<原文>  
create a pod with two containers, both with image busybox and command "echo hello; sleep 3600". connect to the second container and run 'ls'

<details><summary>show</summary>
<p>

これを行う最も簡単な方法は、単一のコンテナでポッドを作成し、その定義をyamlファイルに保存することです。  

<原文>  
easiest way to do it is create a pod with a single container and save its definition in a yaml file:

```bash
kubectl run busybox --image=busybox --restart=never -o yaml --dry-run -- /bin/sh -c 'echo hello;sleep 3600' > pod.yaml
vi pod.yaml
```

コンテナーに関連する値をコピーして貼り付けます。  
そのため、最終的なyamlには次の2つのコンテナを含める必要があります（これらのコンテナーに異なる名前を付けるようにしてください）。  

<原文>
copy/paste the container related values, so your final yaml should contain the following two containers (make sure those containers have a different name):

```yaml
containers:
  - args:
    - /bin/sh
    - -c
    - echo hello;sleep 3600
    image: busybox
    imagepullpolicy: ifnotpresent
    name: busybox
    resources: {}
  - args:
    - /bin/sh
    - -c
    - echo hello;sleep 3600
    image: busybox
    name: busybox2
```

```bash
kubectl create -f pod.yaml
# connect to the busybox2 container within the pod
kubectl exec -it busybox -c busybox2 -- /bin/sh
ls
exit

# or you can do the above with just an one-liner
kubectl exec -it busybox -c busybox2 -- ls

# you can do some cleanup
kubectl delete po busybox
```

</p>
</details>
