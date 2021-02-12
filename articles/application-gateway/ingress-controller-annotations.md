---
title: Anotações do controlador de entrada do gateway de aplicativo
description: Este artigo fornece documentação sobre as anotações específicas do controlador de entrada do gateway de aplicativo.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 4f570f9f18f9c9d484a9bc9c1a5c64d42dbdc714
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397443"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>Anotações para o controlador de entrada do gateway de aplicativo 

## <a name="introductions"></a>Introduções

O recurso de entrada do kubernetes pode ser anotado com pares de chave/valor arbitrários. O AGIC se baseia em anotações para programar recursos do gateway de aplicativo, que não são configuráveis por meio do YAML de entrada. As anotações de entrada são aplicadas a todas as configurações de HTTP, pools de back-end e ouvintes derivados de um recurso de entrada.

## <a name="list-of-supported-annotations"></a>Lista de anotações com suporte

Para que um recurso de entrada seja observado pelo AGIC, ele **deve ser anotado** `kubernetes.io/ingress.class: azure/application-gateway` . Somente o AGIC funcionará com o recurso de entrada em questão.

| Chave de anotação | Tipo de valor | Valor padrão | Valores Permitidos
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#tls-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32` seg | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32` seg | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>Prefixo do caminho de back-end

Esta anotação permite que o caminho de back-end especificado em um recurso de entrada seja reescrito com o prefixo especificado nesta anotação. Isso permite que os usuários exponham serviços cujos pontos de extremidade são diferentes dos nomes de ponto de extremidades usados para expor um serviço em um recurso de entrada.

### <a name="usage"></a>Uso

```yaml
appgw.ingress.kubernetes.io/backend-path-prefix: <path prefix>
```

### <a name="example"></a>Exemplo

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-bkprefix
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```
No exemplo acima, definimos um recurso de entrada chamado `go-server-ingress-bkprefix` com uma anotação `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"` . A anotação informa ao gateway de aplicativo para criar uma configuração de HTTP, que terá uma substituição de prefixo de caminho para o caminho `/hello` para `/test/` .

> [!NOTE] 
> No exemplo acima, temos apenas uma regra definida. No entanto, as anotações são aplicáveis a todo o recurso de entrada; portanto, se um usuário tivesse definido várias regras, o prefixo de caminho de back-end seria configurado para cada um dos caminhos especificados. Portanto, se um usuário quiser regras diferentes com prefixos de caminho diferentes (mesmo para o mesmo serviço), precisariam definir recursos de entrada diferentes.

## <a name="tls-redirect"></a>Redirecionamento de TLS

O gateway de aplicativo [pode ser configurado](./redirect-overview.md) para redirecionar automaticamente URLs HTTP para seus correspondentes HTTPS. Quando essa anotação estiver presente e o TLS estiver configurado corretamente, o controlador de entrada do kubernetes criará uma [regra de roteamento com uma configuração de redirecionamento](./redirect-http-to-https-portal.md#add-a-routing-rule-with-a-redirection-configuration) e aplicará as alterações ao seu gateway de aplicativo. O redirecionamento criado será o HTTP `301 Moved Permanently` .

### <a name="usage"></a>Uso

```yaml
appgw.ingress.kubernetes.io/ssl-redirect: "true"
```

### <a name="example"></a>Exemplo

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-redirect
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
   - hosts:
     - www.contoso.com
     secretName: testsecret-tls
  rules:
  - host: www.contoso.com
    http:
      paths:
      - backend:
          serviceName: websocket-repeater
          servicePort: 80
```

## <a name="connection-draining"></a>Descarga de conexão

`connection-draining`: Essa anotação permite que os usuários especifiquem se deseja habilitar o descarregamento de conexão.
`connection-draining-timeout`: Essa anotação permite que os usuários especifiquem um tempo limite após o qual o gateway de aplicativo encerrará as solicitações para o ponto de extremidade de back-end de descarga.

### <a name="usage"></a>Uso

```yaml
appgw.ingress.kubernetes.io/connection-draining: "true"
appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
```

### <a name="example"></a>Exemplo

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-drain
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/connection-draining: "true"
    appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="cookie-based-affinity"></a>Afinidade baseada em cookie

Esta anotação permite especificar se a afinidade baseada em cookie deve ser habilitada.

### <a name="usage"></a>Uso

```yaml
appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
```

### <a name="example"></a>Exemplo

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-affinity
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="request-timeout"></a>Tempo Limite da Solicitação

Essa anotação permite especificar o tempo limite da solicitação em segundos após o qual o gateway de aplicativo falhará na solicitação, se a resposta não for recebida.

### <a name="usage"></a>Uso

```yaml
appgw.ingress.kubernetes.io/request-timeout: "20"
```

### <a name="example"></a>Exemplo

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/request-timeout: "20"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="use-private-ip"></a>Usar IP privado

Essa anotação nos permite especificar se deve-se expor esse ponto de extremidade no IP privado do gateway de aplicativo.

> [!NOTE]
> * O gateway de aplicativo não dá suporte a vários IPs na mesma porta (exemplo: 80/443). A entrada com anotação `appgw.ingress.kubernetes.io/use-private-ip: "false"` e outra com `appgw.ingress.kubernetes.io/use-private-ip: "true"` on fará com `HTTP` que o AGIC falhe ao atualizar o gateway de aplicativo.
> * Para o gateway de aplicativo que não tem um IP privado, insere com `appgw.ingress.kubernetes.io/use-private-ip: "true"` será ignorado. Isso será refletido nos logs do controlador e nos eventos de entrada para esses insere com `NoPrivateIP` aviso.


### <a name="usage"></a>Uso
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

### <a name="example"></a>Exemplo
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/use-private-ip: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="backend-protocol"></a>Protocolo de back-end

Essa anotação nos permite especificar o protocolo que o gateway de aplicativo deve usar ao conversar com o pods. Protocolos com suporte: `http` , `https`

> [!NOTE]
> * Embora os certificados autoassinados tenham suporte no gateway de aplicativo, atualmente, o AGIC dá suporte apenas `https` quando o pods está usando um certificado assinado por uma autoridade de certificação conhecida.
> * Certifique-se de não usar a porta 80 com HTTPS e a porta 443 com HTTP no pods.

### <a name="usage"></a>Uso
```yaml
appgw.ingress.kubernetes.io/backend-protocol: "https"
```

### <a name="example"></a>Exemplo
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-protocol: "https"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 443
```