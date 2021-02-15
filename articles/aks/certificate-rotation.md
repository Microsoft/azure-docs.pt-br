---
title: Girar certificados no serviço kubernetes do Azure (AKS)
description: Saiba como girar seus certificados em um cluster do AKS (serviço kubernetes do Azure).
services: container-service
ms.topic: article
ms.date: 11/15/2019
ms.openlocfilehash: 90526b78e65c335f07a2a9d2d152b54b47233082
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88211037"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Girar certificados no serviço kubernetes do Azure (AKS)

O AKS (serviço kubernetes do Azure) usa certificados para autenticação com muitos de seus componentes. Periodicamente, talvez seja necessário girar esses certificados por motivos de segurança ou política. Por exemplo, você pode ter uma política para girar todos os seus certificados a cada 90 dias.

Este artigo mostra como girar os certificados em seu cluster AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer que você esteja executando o CLI do Azure versão 2.0.77 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>Certificados AKS, autoridades de certificação e contas de serviço

O AKS gera e usa os seguintes certificados, autoridades de certificação e contas de serviço:

* O servidor de API AKS cria uma autoridade de certificação (CA) chamada de AC do cluster.
* O servidor de API tem uma AC de cluster, que assina certificados para uma comunicação unidirecional do servidor de API para kubelets.
* Cada kubelet também cria uma solicitação de assinatura de certificado (CSR), que é assinada pela AC do cluster, para a comunicação do kubelet com o servidor de API.
* O repositório de valor de chave etcd tem um certificado assinado pela AC do cluster para comunicação do etcd com o servidor de API.
* O armazenamento de valor de chave etcd cria uma autoridade de certificação que assina certificados para autenticar e autorizar a replicação de dados entre réplicas do etcd no cluster AKS.
* O agregador de API usa a AC do cluster para emitir certificados para comunicação com outras APIs. O agregador de API também pode ter sua própria AC para emitir esses certificados, mas atualmente usa a AC do cluster.
* Cada nó usa um token de conta de serviço (SA), que é assinado pela AC do cluster.
* O `kubectl` cliente tem um certificado para se comunicar com o cluster AKs.

> [!NOTE]
> Os clusters AKS criados antes de março de 2019 têm certificados que expiram após dois anos. Qualquer cluster criado após 2019 de março ou qualquer cluster que tenha seus certificados girados tem certificados de AC de cluster que expiram após 30 anos. Todos os outros certificados expiram após dois anos. Para verificar quando o cluster foi criado, use `kubectl get nodes` para ver a *idade* de seus pools de nós.
> 
> Além disso, você pode verificar a data de validade do certificado do cluster. Por exemplo, o comando bash a seguir exibe os detalhes do certificado para o cluster *myAKSCluster* .
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d | openssl x509 -text | grep -A2 Validity
> ```

## <a name="rotate-your-cluster-certificates"></a>Girar seus certificados de cluster

> [!WARNING]
> Girar seus certificados usando `az aks rotate-certs` pode causar até 30 minutos de inatividade para o cluster AKs.

Use [AZ AKs Get-Credentials][az-aks-get-credentials] para entrar no seu cluster do AKS. Esse comando também baixa e configura o certificado do `kubectl` cliente no computador local.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Use `az aks rotate-certs` para girar todos os certificados, CAS e SAS no cluster.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Pode levar até 30 minutos para `az aks rotate-certs` ser concluído. Se o comando falhar antes de concluir, use `az aks show` para verificar se o status do cluster é de *rotação de certificado*. Se o cluster estiver em um estado de falha, execute novamente `az aks rotate-certs` para girar os certificados novamente.

Verifique se os certificados antigos não são mais válidos executando um `kubectl` comando. Como você não atualizou os certificados usados pelo `kubectl` , verá um erro.  Por exemplo:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Atualize o certificado usado pelo `kubectl` executando `az aks get-credentials` .

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Verifique se os certificados foram atualizados executando um `kubectl` comando, que agora terá sucesso. Por exemplo:

```console
kubectl get no
```

> [!NOTE]
> Se você tiver qualquer serviço que seja executado sobre AKS, como [Azure dev Spaces][dev-spaces], talvez seja necessário [Atualizar certificados relacionados a esses serviços][dev-spaces-rotate] também.

## <a name="next-steps"></a>Próximas etapas

Este artigo mostrou como girar automaticamente os certificados, as CAs e as SAs do seu cluster. Você pode ver [as práticas recomendadas para segurança de cluster e atualizações no serviço de kubernetes do Azure (AKs)][aks-best-practices-security-upgrades] para obter mais informações sobre as práticas recomendadas de segurança do AKS.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: ../dev-spaces/index.yml
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
