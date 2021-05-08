```bash
TOKEN=$(curl https://raw.githubusercontent.com/istio/istio/master/security/tools/jwt/samples/demo.jwt -s)
export APP_URL=$(oc -n istio-system get route  -o custom-columns=HOST:'{..spec.host}' --no-headers | grep bookinfo)
curl --header "Authorization: Bearer $TOKEN" http://${APP_URL}/productpage -s -o /dev/null -w "%{http_code}\n"
```
