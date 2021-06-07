---
title: Criar controlador de dados
description: Crie um controlador de dados de arco do Azure em um cluster kubernetes típico de vários nós que você já tenha implantado.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 329df78bb5829695b95fcca5b7ed7e1439ced821
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101688358"
---
# <a name="create-the-azure-arc-data-controller"></a>Criar o controlador de dados de arco do Azure

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-creating-the-azure-arc-data-controller"></a>Visão geral da criação do controlador de dados de arco do Azure

Os serviços de dados habilitados para Arc do Azure podem ser criados em vários tipos diferentes de clusters kubernetes e serviços kubernetes gerenciados usando várias abordagens diferentes.

Atualmente, a lista de serviços e distribuições com suporte do kubernetes é a seguinte:

- AKS (Serviço de Kubernetes do Azure)
- Mecanismo do serviço de kubernetes do Azure (mecanismo do AKS) no Azure Stack
- Serviço kubernetes do Azure no HCI Azure Stack
- Azure RedHat OpenShift (ARO)
- Plataforma de contêiner OpenShift (OCP)
- AWS Elastic Kubernetes Service (EKS)
- Google Cloud kubernetes Engine (GKE)
- Software livre, Kubernetes upstream normalmente implantado usando kubeadm

> [!IMPORTANT]
> * A versão mínima com suporte do kubernetes é v 1.17. Consulte [problemas conhecidos](./release-notes.md#known-issues) para obter informações adicionais. 
> * A versão mínima com suporte do OCP é 4,5.
> * Consulte os [requisitos de conectividade](connectivity.md) para entender qual conectividade é necessária entre seu ambiente e o Azure.
> * Consulte as [diretrizes de configuração de armazenamento](storage-configuration.md) para entender os detalhes de como configurar o armazenamento persistente.
> * Se você estiver usando o serviço kubernetes do Azure, o tamanho da VM do nó de trabalho do cluster deverá ser pelo menos **Standard_D8s_v3** e usar **discos Premium.** O cluster não deve abranger várias zonas de disponibilidade. 
> * Se você estiver usando outra distribuição ou serviço do kubernetes, certifique-se de ter um tamanho mínimo de nó de 8 GB de RAM e 4 núcleos e uma capacidade total de soma de 32 GB de RAM disponível em todos os nós kubernetes. Por exemplo, você poderia ter 1 nó a 32 GB de RAM e 4 núcleos ou poderia ter 2 nós com 16 GB RAM e 4 núcleos cada.

> [!NOTE]
> Se você estiver usando a plataforma de contêiner do Red Hat OpenShift no Azure, é recomendável usar a versão mais recente disponível.

Dependendo da opção escolhida, determinadas ferramentas serão _necessárias_, mas é recomendável [instalar todas as ferramentas de cliente](./install-client-tools.md) antes de começar a criar o controlador de dados de arco do Azure.

Independentemente da opção que você escolher, durante o processo de criação, será necessário fornecer as seguintes informações:

- **Nome do controlador de dados** -um nome descritivo para o controlador de dados-por exemplo, "controlador de dados de produção", "controlador de dados de Seattle".
- **Nome** de usuário do controlador de dados-qualquer nome de usuários do administrador do controlador de dados.
- **Senha do controlador de dados** -uma senha para o usuário administrador do controlador de dados.
- **Nome do seu namespace kubernetes** -o nome do namespace kubernetes no qual você deseja criar o controlador de dados.
- **Modo de conectividade** – o modo de conectividade determina o grau de conectividade de seu ambiente de serviços de dados habilitados para o Azure ARC para o Azure. Atualmente, a visualização só dá suporte a modos conectados indiretamente e conectados diretamente.  Para obter informações, consulte [modo de conectividade](./connectivity.md). 
- **ID de assinatura do Azure** -a GUID de assinatura do Azure para onde você deseja que o recurso de controlador de dados no Azure seja criado.
- **Nome do grupo de recursos do Azure** -o nome do grupo de recursos em que você deseja que o recurso de controlador de dados no Azure seja criado.
- **Local do Azure** -o local do Azure onde os metadados de recurso do controlador de dados serão armazenados no Azure. Para obter uma lista de regiões disponíveis, consulte [infraestrutura global/produtos do Azure por região](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

## <a name="next-steps"></a>Próximas etapas

Há várias opções para criar o controlador de dados de arco do Azure:

> **Quer apenas experimentar as novidades?**  
> Comece rapidamente com o [Azure Arc JumpStart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) no Azure kubernetes Service (AKs), AWS elástico kubernetes Service (EKS), Google Cloud kubernetes Engine (GKE) ou em uma VM do Azure!
> 
- [Criar um controlador de dados com [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](create-data-controller-using-azdata.md)
- [Criar um controlador de dados com Azure Data Studio](create-data-controller-azure-data-studio.md)
- [Criar um controlador de dados do portal do Azure por meio de um notebook Jupyter no Azure Data Studio](create-data-controller-resource-in-azure-portal.md)
- [Criar um controlador de dados com ferramentas de kubernetes como kubectl ou OC](create-data-controller-using-kubernetes-native-tools.md)
- [Crie um controlador de dados com o Azure Arc JumpStart para uma experiência acelerada de uma implantação de teste](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)
