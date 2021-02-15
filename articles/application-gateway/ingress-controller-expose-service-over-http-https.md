---
title: Expor um serviço AKS por HTTP ou HTTPS usando o gateway de aplicativo
description: Este artigo fornece informações sobre como expor um serviço AKS por HTTP ou HTTPS usando o gateway de aplicativo.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3b816ddc0eccf8c406cfed37d6bfc594e27d3629
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85850363"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>Expor um serviço AKS por HTTP ou HTTPS usando o gateway de aplicativo 

Esses tutoriais ajudam a ilustrar o uso de [recursos de entrada do kubernetes](https://kubernetes.io/docs/concepts/services-networking/ingress/) para expor um serviço de kubernetes de exemplo por meio do Gateway de [aplicativo Azure](https://azure.microsoft.com/services/application-gateway/) sobre http ou HTTPS.

## <a name="prerequisites"></a>Pré-requisitos

- `ingress-azure`Gráfico Helm instalado.
  - [**Implantação do Greenfield**](ingress-controller-install-new.md): se você estiver começando do zero, consulte estas instruções de instalação, que descreve as etapas para implantar um cluster do AKS com o gateway de aplicativo e instalar o controlador de entrada do gateway de aplicativo no cluster AKs.
  - [**Implantação de Brownfield**](ingress-controller-install-existing.md): se você tiver um cluster AKs e um gateway de aplicativo existentes, consulte estas instruções para instalar o controlador de entrada do gateway de aplicativo no cluster AKs.
- Se você quiser usar HTTPS neste aplicativo, será necessário um certificado X509 e sua chave privada.

## <a name="deploy-guestbook-application"></a>Implantar `guestbook` aplicativo

O aplicativo de livro de visitas é um aplicativo kubernetes canônico que compõe o front-end de uma interface do usuário da Web, um backend e um banco de dados Redis. Por padrão, `guestbook` o expõe seu aplicativo por meio de um serviço com o nome `frontend` na porta `80` . Sem um recurso de entrada kubernetes, o serviço não pode ser acessado de fora do cluster AKS. Usaremos o aplicativo e configuraremos os recursos de entrada para acessar o aplicativo por meio de HTTP e HTTPS.

Siga as instruções abaixo para implantar o aplicativo de livro de visitas.

1. Baixar `guestbook-all-in-one.yaml` daqui [here](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)
1. Implante `guestbook-all-in-one.yaml` em seu cluster do AKS executando

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

Agora, o `guestbook` aplicativo foi implantado.

## <a name="expose-services-over-http"></a>Expor serviços via HTTP

Para expor o aplicativo de livro de visitas, usaremos o seguinte recurso de entrada:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```

Essa entrada vai expor o `frontend` serviço da `guestbook-all-in-one` implantação como um back-end padrão do gateway de aplicativo.

Salve o recurso de entrada acima como `ing-guestbook.yaml` .

1. Implantar `ing-guestbook.yaml` executando:

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. Verifique o status da implantação no log do controlador de entrada.

Agora o `guestbook` aplicativo deve estar disponível. Você pode verificar isso visitando o endereço público do gateway de aplicativo.

## <a name="expose-services-over-https"></a>Expor serviços via HTTPS

### <a name="without-specified-hostname"></a>Sem o nome do host especificado

Sem especificar o nome do host, o serviço de livro de visitas estará disponível em todos os nomes de host que apontam para o gateway de aplicativo.

1. Antes de implantar a entrada, você precisa criar um segredo kubernetes para hospedar o certificado e a chave privada. Você pode criar um segredo kubernetes executando

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. Defina a seguinte entrada. Na entrada, especifique o nome do segredo na `secretName` seção.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - secretName: <guestbook-secret-name>
      rules:
      - http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

    > [!NOTE] 
    > Substitua `<guestbook-secret-name>` no recurso de entrada acima pelo nome do seu segredo. Armazene o recurso de entrada acima em um nome de arquivo `ing-guestbook-tls.yaml` .

1. Implante o ing-prolivror-TLS. YAML executando

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. Verifique o status da implantação no log do controlador de entrada.

Agora `guestbook` , o aplicativo estará disponível em http e HTTPS.

### <a name="with-specified-hostname"></a>Com o nome de host especificado

Você também pode especificar o nome do host na entrada para multiplexar configurações e serviços TLS.
Ao especificar o nome do host, o serviço de livro de visitas estará disponível apenas no host especificado.

1. Defina a seguinte entrada.
    Na entrada, especifique o nome do segredo na `secretName` seção e substitua o nome do host na seção de forma `hosts` adequada.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - hosts:
          - <guestbook.contoso.com>
          secretName: <guestbook-secret-name>
      rules:
      - host: <guestbook.contoso.com>
        http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

1. Implantar `ing-guestbook-tls-sni.yaml` executando

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. Verifique o status da implantação no log do controlador de entrada.

Agora `guestbook` , o aplicativo estará disponível em http e HTTPS somente no host especificado ( `<guestbook.contoso.com>` neste exemplo).

## <a name="integrate-with-other-services"></a>Integrar com outros serviços

A entrada a seguir permitirá que você adicione caminhos adicionais a essa entrada e redirecione esses caminhos para outros serviços:

```yaml
apiVersion: extensions/v1beta1
  kind: Ingress
  metadata:
    name: guestbook
    annotations:
      kubernetes.io/ingress.class: azure/application-gateway
  spec:
    rules:
    - http:
        paths:
        - path: </other/*>
          backend:
            serviceName: <other-service>
            servicePort: 80
        - backend:
            serviceName: frontend
            servicePort: 80
```
