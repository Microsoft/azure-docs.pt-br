---
title: Usar certificados LetsEncrypt.org com o gateway de aplicativo
description: Este artigo fornece informações sobre como obter um certificado de LetsEncrypt.org e usá-lo em seu gateway de aplicativo para clusters AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: df8722e8160538daa1535711092790dbb2405097
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "84807025"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>Usar certificados com LetsEncrypt.org no gateway de aplicativo para clusters AKS

Esta seção configura seu AKS para aproveitar o [LetsEncrypt.org](https://letsencrypt.org/) e obter automaticamente um certificado TLS/SSL para seu domínio. O certificado será instalado no gateway de aplicativo, que executará o encerramento de SSL/TLS para o cluster AKS. A configuração descrita aqui usa o complemento [CERT-Manager](https://github.com/jetstack/cert-manager) kubernetes, que automatiza a criação e o gerenciamento de certificados.

Siga as etapas abaixo para instalar o [CERT-Manager](https://docs.cert-manager.io) em seu cluster AKs existente.

1. Gráfico de Helm

    Execute o script a seguir para instalar o `cert-manager` gráfico Helm. Isso vai:

    - criar um novo `cert-manager` namespace em seu AKs
    - Crie o seguinte CRDs: certificado, desafio, ClusterIssuer, emissor, ordem
    - instalar o gráfico de CERT-Manager (de [docs.cert-Manager.IO)](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)

    ```bash
    #!/bin/bash

    # Install the CustomResourceDefinition resources separately
    kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

    # Create the namespace for cert-manager
    kubectl create namespace cert-manager

    # Label the cert-manager namespace to disable resource validation
    kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

    # Add the Jetstack Helm repository
    helm repo add jetstack https://charts.jetstack.io

    # Update your local Helm chart repository cache
    helm repo update

    # Install the cert-manager Helm chart
    helm install \
      --name cert-manager \
      --namespace cert-manager \
      --version v0.8.0 \
      jetstack/cert-manager
    ```

2. Recurso ClusterIssuer

    Criar um `ClusterIssuer` recurso. Ele é exigido pelo `cert-manager` para representar a `Lets Encrypt` autoridade de certificação em que os certificados assinados serão obtidos.

    Usando o recurso não-namespace `ClusterIssuer` , o CERT-Manager emitirá certificados que podem ser consumidos de vários namespaces. `Let’s Encrypt` usa o protocolo ACME para verificar se você controla um determinado nome de domínio e emite um certificado. Mais detalhes sobre como configurar `ClusterIssuer` as propriedades [aqui](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html). `ClusterIssuer` o irá instruir `cert-manager` a emitir certificados usando o `Lets Encrypt` ambiente de preparo usado para teste (o certificado raiz não está presente em repositórios de confiança de navegador/cliente).

    O tipo de desafio padrão no YAML abaixo é `http01` . Outros desafios estão documentados em [tipos de letsencrypt.org de desafio](https://letsencrypt.org/docs/challenge-types/)

    > [!IMPORTANT] 
    > Atualizar `<YOUR.EMAIL@ADDRESS>` no YAML abaixo

    ```bash
    #!/bin/bash
    kubectl apply -f - <<EOF
    apiVersion: certmanager.k8s.io/v1alpha1
    kind: ClusterIssuer
    metadata:
    name: letsencrypt-staging
    spec:
    acme:
        # You must replace this email address with your own.
        # Let's Encrypt will use this to contact you about expiring
        # certificates, and issues related to your account.
        email: <YOUR.EMAIL@ADDRESS>
        # ACME server URL for Let’s Encrypt’s staging environment.
        # The staging environment will not issue trusted certificates but is
        # used to ensure that the verification process is working properly
        # before moving to production
        server: https://acme-staging-v02.api.letsencrypt.org/directory
        privateKeySecretRef:
        # Secret resource used to store the account's private key.
        name: example-issuer-account-key
        # Enable the HTTP-01 challenge provider
        # you prove ownership of a domain by ensuring that a particular
        # file is present at the domain
        http01: {}
    EOF
    ```

3. Implantar o Aplicativo

    Crie um recurso de entrada para expor o `guestbook` aplicativo usando o gateway de aplicativo com o certificado de criptografia permite criptografar.

    Verifique se o gateway de aplicativo tem uma configuração de IP de front-end pública com um nome DNS (usando o `azure.com` domínio padrão ou provisione um `Azure DNS Zone` serviço e atribua seu próprio domínio personalizado).
    Observe a anotação `certmanager.k8s.io/cluster-issuer: letsencrypt-staging` , que informa ao Gerenciador de certificados para processar o recurso de entrada marcado.

    > [!IMPORTANT] 
    > Atualize `<PLACEHOLDERS.COM>` no YAML abaixo com seu próprio domínio (ou o gateway de aplicativo, por exemplo, "KH-AKs-ingress.westeurope.cloudapp.Azure.com")

    ```bash
    kubectl apply -f - <<EOF
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
    name: guestbook-letsencrypt-staging
    annotations:
        kubernetes.io/ingress.class: azure/application-gateway
        certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    spec:
    tls:
    - hosts:
        - <PLACEHOLDERS.COM>
        secretName: guestbook-secret-name
    rules:
    - host: <PLACEHOLDERS.COM>
        http:
        paths:
        - backend:
            serviceName: frontend
            servicePort: 80
    EOF
    ```

    Depois de alguns segundos, você pode acessar o `guestbook` serviço por meio da URL https do gateway de aplicativo usando o certificado de **preparo** emitido automaticamente `Lets Encrypt` .
    Seu navegador pode avisá-lo sobre uma autoridade de certificação inválida. O certificado de preparo é emitido pelo `CN=Fake LE Intermediate X1` . Essa é uma indicação de que o sistema funcionou conforme o esperado e você está pronto para o certificado de produção.

4. Certificado de produção

    Depois que o certificado de preparo for configurado com êxito, você poderá alternar para um servidor de produção ACME:
    1. Substitua a anotação de preparo em seu recurso de entrada por: `certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. Exclua o preparo existente `ClusterIssuer` criado na etapa anterior e crie um novo substituindo o servidor Acme do CLUSTERISSUER YAML acima por `https://acme-v02.api.letsencrypt.org/directory`

5. Expiração e renovação do certificado

    Antes que o `Lets Encrypt` certificado expire, o `cert-manager` atualizará automaticamente o certificado no repositório de segredos do kubernetes. Nesse ponto, o controlador de entrada do gateway de aplicativo aplicará o segredo atualizado referenciado nos recursos de entrada que está usando para configurar o gateway de aplicativo.
