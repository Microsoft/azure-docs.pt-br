---
title: Habilitar o suporte a vários namespaces para o controlador de entrada do gateway de aplicativo
description: Este artigo fornece informações sobre como habilitar o suporte a vários namespaces em um cluster kubernetes com um controlador de entrada do gateway de aplicativo.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: c13c4410852d97f0bf4548578f40a5cc560804d7
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874586"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Habilitar o suporte a vários namespaces em um cluster AKS com o controlador de entrada do gateway de aplicativo

## <a name="motivation"></a>Motivação
Os [namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) kubernetes possibilitam que um cluster kubernetes seja particionado e alocado a subgrupos de uma equipe maior. Essas subequipes podem implantar e gerenciar a infraestrutura com controles mais precisos de recursos, segurança, configuração, etc. O kubernetes permite que um ou mais recursos de entrada sejam definidos de forma independente em cada namespace.

A partir da versão 0,7 [aplicativo Azure gateway kubernetes IngressController](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC) pode ingerir eventos e observar vários namespaces. Se o administrador do AKS decidir usar o [Gateway de aplicativo](https://azure.microsoft.com/services/application-gateway/) como uma entrada, todos os namespaces usarão a mesma instância do gateway de aplicativo. Uma única instalação do controlador de entrada irá monitorar namespaces acessíveis e configurará o gateway de aplicativo ao qual ele está associado.

A versão 0,7 do AGIC continuará a observar exclusivamente o `default` namespace, a menos que isso seja explicitamente alterado para um ou mais namespaces diferentes na configuração do Helm (consulte a seção abaixo).

## <a name="enable-multiple-namespace-support"></a>Habilitar o suporte a vários namespaces
Para habilitar o suporte a vários namespaces:
1. Modifique o arquivo [Helm-config. YAML](#sample-helm-config-file) de uma das seguintes maneiras:
   - Exclua a `watchNamespace` chave inteiramente de [Helm-config. YAML](#sample-helm-config-file) -AGIC irá observar todos os namespaces
   - definido `watchNamespace` como uma cadeia de caracteres vazia-AGIC observará todos os namespaces
   - adicionar vários namespaces separados por uma vírgula ( `watchNamespace: default,secondNamespace` )-AGIC irá observar esses namespaces exclusivamente
2. aplicar alterações do modelo Helm com: `helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

Uma vez implantado com a capacidade de observar vários namespaces, o AGIC irá:
  - listar recursos de entrada de todos os namespaces acessíveis
  - filtrar os recursos de entrada anotados com `kubernetes.io/ingress.class: azure/application-gateway`
  - compor [configuração do gateway de aplicativo](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744) combinado
  - aplicar a configuração ao gateway de aplicativo associado por meio do [ARM](../azure-resource-manager/management/overview.md)

## <a name="conflicting-configurations"></a>Configurações conflitantes
Vários recursos de [entrada](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) de namespace podem instruir o AGIC a criar configurações conflitantes para um único gateway de aplicativo. (Duas insere reivindicando o mesmo domínio por instância.)

Na parte superior da hierarquia-os **ouvintes** (endereço IP, porta e host) e **as regras de roteamento** (ouvinte de associação, pool de back-end e configurações de http) podem ser criados e compartilhados por vários namespaces/insere.

Por outro lado, os caminhos, os pools de back-end, as configurações de HTTP e os certificados TLS podem ser criados somente por um namespace e as duplicatas serão removidas.

Por exemplo, considere os seguintes namespaces definidos por recursos de entrada duplicados `staging` e `production` para `www.contoso.com` :

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: staging
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

Apesar dos dois recursos de entrada que exigem tráfego para serem `www.contoso.com` roteados para os respectivos namespaces do kubernetes, somente um back-end pode atender ao tráfego. O AGIC criaria uma configuração na base "primeira vez, servida" para um dos recursos. Se dois recursos de insere forem criados ao mesmo tempo, aquele anterior no alfabeto terá precedência. No exemplo acima, só será possível criar configurações para a `production` entrada. O gateway de aplicativo será configurado com os seguintes recursos:

  - Listener `fl-www.contoso.com-80`
  - Regra de roteamento: `rr-www.contoso.com-80`
  - Pool de back-end: `pool-production-contoso-web-service-80-bp-80`
  - Configurações de HTTP: `bp-production-contoso-web-service-80-80-websocket-ingress`
  - Investigação de integridade: `pb-production-contoso-web-service-80-websocket-ingress`

Observe que, exceto para o *ouvinte* e a *regra de roteamento*, os recursos do gateway de aplicativo criados incluem o nome do namespace ( `production` ) para o qual foram criados.

Se os dois recursos de entrada forem introduzidos no cluster AKS em diferentes pontos no tempo, é provável que o AGIC acabe em um cenário no qual reconfigura o gateway de aplicativo e reroteia o tráfego do `namespace-B` para o `namespace-A` .

Por exemplo, se você adicionou `staging` First, AGIC configurará o gateway de aplicativo para rotear o tráfego para o pool de back-end de preparo. Em um estágio posterior, a introdução `production` à entrada causará AGIC para reprogramar o gateway de aplicativo, o que iniciará o roteamento do tráfego para o `production` pool de back-end.

## <a name="restrict-access-to-namespaces"></a>Restringir o acesso a namespaces
Por padrão, o AGIC configurará o gateway de aplicativo com base em entrada anotada dentro de qualquer namespace. Se você quiser limitar esse comportamento, terá as seguintes opções:
  - limitar os namespaces, definindo explicitamente os namespaces AGIC deve observar por meio da `watchNamespace` chave YAML em [Helm-config. YAML](#sample-helm-config-file)
  - Use [role/rolebinding](../aks/azure-ad-rbac.md) para limitar o AGIC a namespaces específicos

## <a name="sample-helm-config-file"></a>Arquivo de configuração Helm de exemplo

```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
```