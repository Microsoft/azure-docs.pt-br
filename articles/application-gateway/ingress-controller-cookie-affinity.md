---
title: Habilitar afinidade baseada em cookie com o gateway de aplicativo
description: Este artigo fornece informações sobre como habilitar a afinidade baseada em cookie com um gateway de aplicativo.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a7806cf9518090539ba540a9a522af1aae2691f0
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397409"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Habilitar afinidade baseada em cookie com um gateway de aplicativo
Conforme descrito na [documentação do gateway de aplicativo Azure](./application-gateway-components.md#http-settings), o gateway de aplicativo dá suporte à afinidade baseada em cookie, o que significa que ele pode direcionar o tráfego subsequente de uma sessão de usuário para o mesmo servidor para processamento.

## <a name="example"></a>Exemplo
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```