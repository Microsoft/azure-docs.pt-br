---
title: Adicionar investigações de integridade ao seu pods AKS
description: Este artigo fornece informações sobre como adicionar investigações de integridade (prontidão e/ou vida) a AKS pods com um gateway de aplicativo.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 8c8b8b0090877db7abc8fae0e44f928e8b10dcf5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "84808002"
---
# <a name="add-health-probes-to-your-service"></a>Adicionar investigações de integridade ao seu serviço
Por padrão, o controlador de entrada provisionará uma investigação HTTP GET para o pods exposto.
As propriedades da investigação podem ser personalizadas com a adição de uma [investigação de preparação ou dinâmica](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) à sua `deployment` / `pod` especificação.

## <a name="with-readinessprobe-or-livenessprobe"></a>Com `readinessProbe` ou `livenessProbe`
```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aspnetapp
spec:
  replicas: 3
  template:
    metadata:
      labels:
        service: site
    spec:
      containers:
      - name: aspnetapp
        image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          periodSeconds: 3
          timeoutSeconds: 1
```

Referência da API do kubernetes:
* [Investigações de contêiner](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [Ação HttpGet](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe` e `livenessProbe` têm suporte quando configurado com o `httpGet` .
> * No momento, não há suporte para investigação em uma porta diferente daquela exposta no pod.
> * `HttpHeaders`, `InitialDelaySeconds` , `SuccessThreshold` não tem suporte.

##  <a name="without-readinessprobe-or-livenessprobe"></a>Sem `readinessProbe` ou `livenessProbe`
Se as investigações acima não forem fornecidas, o controlador de entrada fará uma suposição de que o serviço pode ser acessado no `Path` especificado para `backend-path-prefix` a anotação ou o `path` especificado na `ingress` definição do serviço.

## <a name="default-values-for-health-probe"></a>Valores padrão para investigação de integridade
Para qualquer propriedade que não pode ser inferida pela investigação de preparação/vida, os valores padrão são definidos.

| Propriedade de investigação do gateway de aplicativo | Valor padrão |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |