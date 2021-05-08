 ## Generate certifacate authority  ##

```bash
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -subj '/O=bookinfo Inc./CN=bookinfo.com' -keyout bookinfo-ca.key -out bookinfo-ca.crt
```

 ## Generate server certificate  ##

```bash
openssl req -out bookinfo-com.csr -newkey rsa:2048 -nodes -keyout bookinfo-com.key -subj "/CN=bookinfo.com/O=bookinfo organization"
openssl x509 -req -days 365 -CA bookinfo-ca.crt -CAkey bookinfo-ca.key -set_serial 0 -in bookinfo-com.csr -out bookinfo-com.crt
```

 ## Configure Secret  ##

```bash
oc create -n istio-system secret generic bookinfo-credential --from-file=tls.key=bookinfo-com.key \
--from-file=tls.crt=bookinfo-com.crt --from-file=ca.crt=bookinfo-ca.crt
```

 ## Client Certificate  ##

```bash
openssl req -out client-bookinfo.csr -newkey rsa:2048 -nodes -keyout client-bookinfo.key -subj "/CN=view-bookinfo/O=client organization"
openssl x509 -req -days 365 -CA bookinfo-ca.crt -CAkey bookinfo-ca.key -set_serial 1 -in client-bookinfo.csr -out client-bookinfo.crt
```

 ## Send Request  ##

```bash
export APP_URL=$(oc -n istio-system get route  -o custom-columns=HOST:'{..spec.host}' --no-headers | grep bookinfo-mtls)
curl -k https://${APP_URL}/productpage --cacert bookinfo-ca.crt --cert client-bookinfo.crt --key client-bookinfo.key
```
