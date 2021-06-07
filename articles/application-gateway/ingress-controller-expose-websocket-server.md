---
title: Expor um servidor WebSocket ao gateway de aplicativo
description: Este artigo fornece informações sobre como expor um servidor WebSocket ao gateway de aplicativo com controlador de entrada para clusters AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 68d4ff7e4617136e4c58ce672f34de56e46f0229
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85207780"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>Expor um servidor WebSocket ao gateway de aplicativo

Conforme descrito na documentação do Application Gateway v2, ele [fornece suporte nativo para os protocolos WebSocket e http/2](features.md#websocket-and-http2-traffic). Observe que, para o gateway de aplicativo e a entrada kubernetes-não há nenhuma configuração configurável pelo usuário para habilitar ou desabilitar seletivamente o suporte ao WebSocket.

O YAML de implantação kubernetes abaixo mostra a configuração mínima usada para implantar um servidor WebSocket, que é o mesmo que implantar um servidor Web normal:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: websocket-server
spec:
  selector:
    matchLabels:
      app: ws-app
  replicas: 2
  template:
    metadata:
      labels:
        app: ws-app
    spec:
      containers:
        - name: websocket-app
          imagePullPolicy: Always
          image: your-container-repo.azurecr.io/websockets-app
          ports:
            - containerPort: 8888
      imagePullSecrets:
        - name: azure-container-registry-credentials

---

apiVersion: v1
kind: Service
metadata:
  name: websocket-app-service
spec:
  selector:
    app: ws-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8888

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-repeater
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: ws.contoso.com
      http:
        paths:
          - backend:
              serviceName: websocket-app-service
              servicePort: 80
```

Considerando que todos os pré-requisitos são atendidos e você tem um gateway de aplicativo controlado por uma entrada kubernetes em seu AKS, a implantação acima resultaria em um servidor WebSockets exposto na porta 80 do IP público do seu gateway de aplicativo e no `ws.contoso.com` domínio.

O comando de rotação a seguir testaria a implantação do servidor WebSocket:
```shell
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>Investigações de integridade do WebSocket

Se sua implantação não definir explicitamente investigações de integridade, o gateway de aplicativo tentará um HTTP GET em seu ponto de extremidade do servidor WebSocket.
Dependendo da implementação do servidor ([aqui está um que amamos](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)) cabeçalhos específicos do WebSocket podem ser necessários ( `Sec-Websocket-Version` por exemplo).
Como o gateway de aplicativo não adiciona cabeçalhos WebSocket, a resposta de investigação de integridade do gateway de aplicativo do servidor WebSocket provavelmente será `400 Bad Request` .
Como resultado, o gateway de aplicativo marcará o pods como não íntegro, o que eventualmente resultará em um `502 Bad Gateway` para os consumidores do servidor WebSocket.
Para evitar isso, talvez seja necessário adicionar um manipulador HTTP GET para uma verificação de integridade em seu servidor ( `/health` por exemplo, que retorna `200 OK` ).
