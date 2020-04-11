![](https://gaforgithub.azurewebsites.net/api?repo=CKAD-exercises/multi_container&empty)
# Multi-container Pods (10%)

### 2つのコンテナを持つPodを作成、コンテナは両方ともbusyboxイメージで、"echo hello; sleep 3600" を実行します。2つ目のコンテナに繋いで `ls` を実行します。

<details><summary>show</summary>
<p>

最も簡単な方法は、1つのコンテナでポッドを作成し、その定義をYAMLファイルに保存することです:

```bash
kubectl run busybox --image=busybox --restart=Never -o yaml --dry-run -- /bin/sh -c 'echo hello;sleep 3600' > pod.yaml
vi pod.yaml
```

コンテナに関連するValuesをコピペしなさい

すると最終的なYAMLは次の2つのコンテナが含まれているはずです(これらのコンテナが異なるnameを持つことを確認しなさい)

```YAML
containers:
  - args:
    - /bin/sh
    - -c
    - echo hello;sleep 3600
    image: busybox
    imagePullPolicy: IfNotPresent
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

# Pod の中の busybox2 コンテナに繋ぐ
kubectl exec -it busybox -c busybox2 -- /bin/sh
ls
exit

# もしくは上記のワンライナーはこうできる
kubectl exec -it busybox -c busybox2 -- ls

# Cleanupは下記でできる
kubectl delete po busybox
```

</p>
</details>
