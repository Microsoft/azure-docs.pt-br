---
title: Verifique suas implantações do Kubernetes no Azure para implementar as melhores práticas
description: Verifique suas implantações do Kubernetes no Azure para implementar as melhores práticas
services: container-service
author: seanmck
ms.service: container-service
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 03c5eb2e32a0a8ec51844511276d9efba5651068
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "65073770"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>Verificar as práticas recomendadas do Kubernetes no seu cluster

Existem várias práticas recomendadas que você deve seguir nas implantações do Kubernetes para garantir o melhor desempenho e resiliência dos seus aplicativos. Você pode usar a ferramenta kube-advisor para procurar implantações que não estejam seguindo essas sugestões.

## <a name="about-kube-advisor"></a>Sobre o Supervisor de kube

A ferramenta [kube-advisor][kube-advisor-github] é um único contêiner projetado para ser executado em seu cluster. Ele consulta o servidor da API do Kubernetes para obter informações sobre suas implementações e retorna um conjunto de melhorias sugeridas.

A ferramenta Supervisor de kube pode relatar sobre a solicitação de recurso e limites não encontrados em aplicativos de PodSpecs para Windows, bem como aplicativos do Linux, mas a ferramenta Supervisor kube em si deve ser agendada em um pod do Linux. Você pode agendar um pod para execução em um pool de nós com um sistema operacional específico usando um [seletor de nó] [ k8s-node-selector] na configuração do pod.

> [!NOTE]
> A ferramenta kube-advisor é suportada pela Microsoft com base no melhor esforço. Questões e sugestões devem ser arquivadas no GitHub.

## <a name="running-kube-advisor"></a>Executando o Supervisor de kube

Para executar a ferramenta em um cluster configurado para [controle de acesso baseado em função (RBAC)](azure-ad-integration.md), use os seguintes comandos. O primeiro comando cria uma conta de serviço do Kubernetes. O segundo comando executa a ferramenta em um pod usando essa conta de serviço e configura o pod para exclusão após sua saída. 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }"
```

Se você não estiver usando o RBAC, poderá executar o comando da seguinte maneira:

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

Em alguns segundos, você verá uma tabela descrevendo os possíveis aprimoramentos em suas implantações.

![Saída do Kube-Advisor](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>Verificações executadas

A ferramenta valida várias práticas recomendadas do Kubernetes, cada uma com sua própria remediação sugerida.

### <a name="resource-requests-and-limits"></a>Solicitações de recursos e limites

O Kubernetes suporta a definição de [solicitações de recursos e limites nas especificações do pod ][kube-cpumem]. A solicitação define o mínimo de CPU e memória necessária para executar o contêiner. O limite define a CPU e a memória máximas que devem ser permitidas.

Por padrão, nenhuma solicitação ou limite é definido nas especificações do pod. Isso pode levar a que os nós sejam excedidos em excesso e que os recipientes fiquem sem alimentação. A ferramenta kube-advisor destaca os pods sem solicitações e limites definidos.

## <a name="cleaning-up"></a>Limpando

Se o seu cluster tiver o RBAC ativado, você poderá limpar o `ClusterRoleBinding` depois de executar a ferramenta usando o seguinte comando:

```bash
kubectl delete -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml
```

Se você estiver executando a ferramenta em um cluster que não esteja habilitado para RBAC, nenhuma limpeza será necessária.

## <a name="next-steps"></a>Próximas etapas

- [Solucionar problemas com o serviço Kubernetes do Azure](troubleshooting.md)

<!-- RESOURCES -->

[kube-cpumem]: https://github.com/Azure/azure-quickstart-templates
[kube-advisor-github]: https://github.com/azure/kube-advisor
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors