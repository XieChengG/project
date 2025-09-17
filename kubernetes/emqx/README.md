# 安装 emqx-operator

## 安装 cert-manager
```sh
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm upgrade --install cert-manager jetstack/cert-manager \
        --namespace cert-manager \
        --create-namespace \
        --set crds.enabled=true
```

## 安装 emqx-operator
```sh
helm repo add emqx https://repos.emqx.io/charts
helm repo update
helm upgrade --install emqx-operator emqx/emqx-operator \
        --namespace emqx-operator-system \
        --create-namespace
```

## 查看是否安装成功CRD
```sh
kubectl get crd -o wide
```

## 查看是否有emqx相关的 apiVersion
```sh
kubectl api-versions
```

# 创建自签发证书

## 创建CA
```sh
openssl genrsa -out ca.pem 2048
openssl req -new -sha256 -key ca.pem -out ca.csr -subj "/C=CN/ST=GD/L=SZ/O=XYKJ/OU=www.xoriginai.com/CN=CA/emailAddress=xiechenggang@xoriginai.com"
openssl x509 -req -days 36500 -sha256 -extensions v3_ca -signkey ca.pem -in ca.csr -out ca.crt
```

## 创建tls 证书
```sh
openssl genrsa -out tls.key 2048
openssl req -new -sha256 -key tls.key -out tls.csr -subj "/C=CN/ST=GD/L=SZ/O=XYKJ/OU=www.xoriginai.com/CN=CA/emailAddress=xiechenggang@xoriginai.com"
openssl x509 -req -days 36500 -sha256 -extensions v3_req -CA ca.crt -CAkey ca.pem -CAserial ca.srl -CAcreateserial -in tls.csr -out tls.crt
```

# 使用阿里云的免费测试证书，有效期3个月

## 证书文件下载
下载 服务器类型为`Apache`类型的，证书格式是 `crt/key`的证书

## 配置对应证书文件
- ca.crt  -> mqtt.xoriginai.com_chain.crt
- tls.crt -> mqtt.xoriginai.com_public.crt
- tls.key -> mqtt.xoriginai.com.key


# 部署emqx 集群

## 创建 secret 清单文件 secret-tls.yaml，存放证书
```sh
kubectl apply -f secret-tls.yaml
```

## 创建 emqx-tls.yaml 清单文件部署emqx 集群
```sh
kubectl apply -f emqx-tls.yaml
```
