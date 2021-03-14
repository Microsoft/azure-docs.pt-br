---
title: Programa de validação kubernetes habilitado para Arc do Azure
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Descreve o programa de validação ARC para distribuições kubernetes
keywords: Kubernetes, Arc, Azure, K8s, validação
ms.openlocfilehash: 819df906add6275997e01fab310fe8dd57a87b51
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121364"
---
# <a name="azure-arc-validation-program"></a>Programa de validação do Arc do Azure

O Kubernetes habilitado para Azure Arc funciona com qualquer cluster Kubernetes certificado pela CNCF (Cloud Native Computing Foundation). A equipe de arco do Azure também trabalhou com os principais fornecedores de ofertas de kubernetes da indústria para validar kubernetes habilitados para o Azure com suas distribuições kubernetes. As versões futuras e secundárias das distribuições kubernetes lançadas por esses provedores serão validadas para compatibilidade com o kubernetes habilitado para o Arc do Azure.

## <a name="validated-distributions"></a>Distribuições validadas

As seguintes distribuições de kubernetes e provedores de infraestrutura fornecidos pela Microsoft passaram com êxito os testes de conformidade para o kubernetes habilitado para Arc do Azure:

| Provedor de distribuição e infraestrutura | Versão |
| ---------------------------------------- | ------- |
| Provedor de API de cluster no Azure            | Versão de lançamento: [0.4.12](https://github.com/kubernetes-sigs/cluster-api-provider-azure/releases/tag/v0.4.12); Versão do kubernetes: [1.18.2](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.2) |
| AKS no Azure Stack HCI                   | Versão de lançamento: [atualização de dezembro de 2020](https://github.com/Azure/aks-hci/releases/tag/AKS-HCI-2012); Versão do kubernetes: [1.18.8](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.8) |

Os provedores a seguir e suas distribuições kubernetes correspondentes passaram com êxito os testes de conformidade para o kubernetes habilitado para Arc do Azure:

| Nome do provedor | Nome da distribuição | Versão |
| ------------ | ----------------- | ------- |
| RedHat       | [OpenShift Container Platform](https://www.openshift.com/products/container-platform) | [4,5](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html), [4,6](https://docs.openshift.com/container-platform/4.6/release_notes/ocp-4-6-release-notes.html), [4,7](https://docs.openshift.com/container-platform/4.7/release_notes/ocp-4-7-release-notes.html) |
| VMware       | [Grade Tanzu kubernetes](https://tanzu.vmware.com/kubernetes-grid) | Versão do kubernetes: v 1.17.5 |
| Canônico    | [Kubernetes com botão](https://ubuntu.com/kubernetes) | [1,19](https://ubuntu.com/kubernetes/docs/1.19/components) |
| SUSE Rancher      | [Rancher kubernetes Engine](https://rancher.com/products/rke/) | Versão da CLI do RKE: [v 1.2.4](https://github.com/rancher/rke/releases/tag/v1.2.4); Versões kubernetes: [1.19.6](https://github.com/kubernetes/kubernetes/releases/tag/v1.19.6)), [1.18.14](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.14)), [1.17.16](https://github.com/kubernetes/kubernetes/releases/tag/v1.17.16))  |
| Nutanix      | [Karbon](https://www.nutanix.com/products/karbon)    | 2.2.1 da versão |

A equipe de arco do Azure também executou os testes de conformidade e validou cenários de kubernetes habilitados para o Arc do Azure nos seguintes provedores de nuvem pública:

| Nome do provedor de nuvem pública | Nome da distribuição | Versão |
| -------------------------- | ----------------- | ------- |
| Amazon Web Services        | Serviço de kubernetes elástico (EKS) | v 1.18.9  |
| Google Cloud Platform      | Google Kubernetes Engine (GKE) | v 1.17.15 |

## <a name="scenarios-validated"></a>Cenários validados

Os testes de conformidade executados como parte da validação de kubernetes habilitada para o Arc do Azure abrangem os seguintes cenários:

1. Conecte os clusters do kubernetes ao arco do Azure: 
    * Implante o gráfico do kubernetes agente do Helm habilitado para Arc no cluster.
    * Configure o certificado MSI (identidade do sistema gerenciado) no cluster.
    * Os agentes enviam metadados de cluster para o Azure.

2. Configuração: 
    * Crie a configuração sobre o recurso kubernetes habilitado para Arc do Azure.
    * [Fluxo, necessário](https://docs.fluxcd.io/)para configurar o fluxo de trabalho do GitOps, é implantado no cluster.
    * O fluxo extrai manifestos e gráficos de Helm do repositório git de demonstração e implanta no cluster.

## <a name="next-steps"></a>Próximas etapas

Saiba como conectar um cluster ao arco do Azure.
> [!div class="nextstepaction"]
> [Conectar um cluster ao Azure Arc](./quickstart-connect-cluster.md)
