---
title: Tutorial – Configurar implantações canário para Máquinas Virtuais do Linux do Azure
description: Neste tutorial, você aprenderá a configurar um pipeline de CD (implantação contínua). Esse pipeline atualiza um grupo de máquinas virtuais do Linux do Azure usando a estratégia de implantação canário.
author: moala
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: bbfe6571cf075b2ce4930eea91bfd1e239470c5a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552500"
---
# <a name="tutorial---configure-the-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>Tutorial – Configurar a estratégia de implantação canário para Máquinas Virtuais do Linux do Azure

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>IaaS (infraestrutura como serviço) – configurar CI/CD

O Azure Pipelines fornece um conjunto de ferramentas de automação de CI/CD para implantações em máquinas virtuais. Você pode configurar um pipeline de entrega contínua para uma VM do Azure do portal do Azure.

Este artigo mostra como configurar um pipeline de CI/CD que usa a estratégia canário para implantações em vários computadores. O portal do Azure também dá suporte a outras estratégias, como [dinâmica](./tutorial-devops-azure-pipelines-classic.md) e [azul-verde](./tutorial-azure-devops-blue-green-strategy.md).

### <a name="configure-cicd-on-virtual-machines"></a>Configurar CI/CD em máquinas virtuais

Você pode adicionar máquinas virtuais como destinos a um [grupo de implantação](/azure/devops/pipelines/release/deployment-groups). Em seguida, você pode direcioná-las para atualizações de vários computadores. Depois de implantar o computadores, veja o **Histórico de Implantação** em um grupo de implantação. Essa exibição permite que você rastreie da VM para o pipeline e, em seguida, para a confirmação.

### <a name="canary-deployments"></a>Implantações canário

Uma implantação canário reduz o risco, distribuindo lentamente as alterações para um pequeno subconjunto de usuários. À medida que você tiver confiança na nova versão, poderá liberá-la para mais servidores na sua infraestrutura e rotear mais usuários para ela.

Usando a opção de entrega contínua, você pode configurar implantações canário para suas máquinas virtuais usando o portal do Azure. Veja um passo a passo do processo:

1. Entre no portal do Azure e navegue até uma máquina virtual.
1. No painel mais à esquerda das configurações da VM, selecione **Entrega contínua**. Em seguida, selecione **Configurar**.

   ![O painel Entrega contínua com o botão Configurar](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. No painel de configuração, clique em **Organização do Azure DevOps** para selecionar uma conta ou criar uma. Em seguida, selecione o projeto no qual deseja configurar o pipeline.  

   ![O painel de Entrega contínua](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Um grupo de implantação é um conjunto lógico de computadores de destino de implantação que representam os ambientes físicos. Desenvolvimento, teste, UAT e produção são exemplos. Você pode criar um grupo de implantação ou selecionar um existente.
1. Selecione o pipeline de build que publica o pacote a ser implantado na máquina virtual. O pacote publicado deve ter um script de implantação chamado deploy.ps1 ou deploy.sh na pasta deployscripts na pasta raiz do pacote. O pipeline executa esse script de implantação.
1. Em **Estratégia de implantação**, selecione **Canário**.
1. Adicione uma marca "canário" às VMs que fazem parte de implantações canário. Adicione uma marca "prod" às VMs que fazem parte das implantações feitas após a implantação do canário ser realizada com sucesso. As marcas ajudam a direcionar apenas as VMs que têm uma função específica.

   ![O painel de Entrega contínua, com o valor da estratégia de implantação Canário escolhido](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

1. Selecione **OK** para configurar o pipeline de entrega contínua para implantar na máquina virtual.

   ![O pipeline canário](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)

1. Os detalhes da implantação para a máquina virtual são exibidos. Você pode selecionar o link para acessar o pipeline de lançamento no Azure DevOps. No pipeline de lançamento, selecione **Editar** para ver a configuração do pipeline. O pipeline tem estas três fases:

   1. Esta fase é uma fase de grupo de implantação. Os aplicativos são implantados em VMs que são marcadas como "canário".
   1. Nessa fase, pipeline é colocado em pausa e aguarda a intervenção manual para retomar a execução.
   1. Essa é novamente uma fase do grupo de implantação. A atualização agora é implantada em VMs marcadas como "Prod".

      ![O painel do grupo de implantação para a tarefa implantar canário](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

1. Antes de retomar a execução do pipeline, verifique se pelo menos uma VM está marcada como "prod". Na terceira fase do pipeline, os aplicativos são implantados somente para as VMs que têm a marca "prod".

1. A tarefa Executar Script de Implantação, por padrão, executa o script de implantação deploy.ps1 ou deploy.sh. O script está na pasta deployscripts na pasta raiz do pacote publicado. Verifique se o pipeline de build selecionado publica a implantação na pasta raiz do pacote.

   ![O painel Artefatos mostrando deploy.sh na pasta deployscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Outras estratégias de implantação
- [Configurar a estratégia de implantação distribuída](./tutorial-devops-azure-pipelines-classic.md)
- [Configurar a estratégia de implantação azul-verde](./tutorial-azure-devops-blue-green-strategy.md)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

Você pode começar a usar o Azure facilmente. Com o Azure DevOps Projects, comece a executar seu aplicativo em qualquer serviço do Azure em apenas três etapas, selecionando:

- Uma linguagem do aplicativo
- Um runtime
- Um serviço do Azure

[Saiba mais](https://azure.microsoft.com/features/devops-projects/).

## <a name="additional-resources"></a>Recursos adicionais

- [Implantar em máquinas virtuais do Azure usando Azure DevOps Projects](../../devops-project/azure-devops-project-vms.md)
- [Implementar a implantação contínua do aplicativo em um conjunto de dimensionamento de máquinas virtuais do Azure](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)