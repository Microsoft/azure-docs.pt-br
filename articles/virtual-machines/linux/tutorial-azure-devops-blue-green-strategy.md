---
title: Tutorial – Configurar implantações canário para máquinas virtuais do Linux do Azure
description: Neste tutorial, você aprenderá a configurar um pipeline de CD (implantação contínua). Esse pipeline atualiza um grupo de máquinas virtuais do Linux do Azure usando a estratégia de implantação azul-verde.
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
ms.openlocfilehash: 4545891cce926f049673cd2c2380a8309f2e71a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552579"
---
# <a name="tutorial---configure-the-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>Tutorial – Configurar a estratégia de implantação azul-verde para máquinas virtuais do Linux do Azure

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>IaaS (infraestrutura como serviço) – configurar CI/CD

O Azure Pipelines fornece um conjunto de ferramentas de automação de CI/CD para implantações em máquinas virtuais. Você pode configurar um pipeline de entrega contínua para uma VM do Azure do portal do Azure.

Este artigo mostra como configurar um pipeline de CI/CD que usa a estratégia azul-verde para implantações em vários computadores. O portal do Azure também dá suporte a outras estratégias, como [sem interrupção](./tutorial-devops-azure-pipelines-classic.md) e [canário](./tutorial-azure-devops-canary-strategy.md).

### <a name="configure-cicd-on-virtual-machines"></a>Configurar CI/CD em máquinas virtuais

Você pode adicionar máquinas virtuais como destinos a um [grupo de implantação](/azure/devops/pipelines/release/deployment-groups). Em seguida, você pode direcioná-las para atualizações de vários computadores. Depois de implantar o computadores, veja o **Histórico de Implantação** em um grupo de implantação. Essa exibição permite que você rastreie da VM para o pipeline e, em seguida, para a confirmação.

### <a name="blue-green-deployments"></a>Implantações azul-verde

Uma implantação azul-verde reduz o tempo de inatividade por ter um ambiente de espera idêntico. Apenas um ambiente está ativo por vez.

Ao se preparar para uma nova versão, você conclui o estágio final do teste no ambiente verde. Depois que o software funciona no ambiente verde, alterne o tráfego para todas as solicitações de entrada irem para o ambiente verde. O ambiente azul está ocioso.

Usando a opção de entrega contínua, você pode configurar implantações do tipo azul-verde em suas máquinas virtuais no portal do Azure. Veja um passo a passo do processo:

1. Entre no portal do Azure e navegue até uma máquina virtual.
1. No painel mais à esquerda das configurações da VM, selecione **Entrega contínua**. Em seguida, selecione **Configurar**.

   ![O painel Entrega contínua com o botão Configurar](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. No painel de configuração, clique em **Organização do Azure DevOps** para selecionar uma conta ou criar uma. Em seguida, selecione o projeto no qual deseja configurar o pipeline.  

   ![O painel de Entrega contínua](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Um grupo de implantação é um conjunto lógico de computadores de destino de implantação que representam os ambientes físicos. Desenvolvimento, teste, UAT e produção são exemplos. Você pode criar um grupo de implantação ou selecionar um existente.
1. Selecione o pipeline de build que publica o pacote a ser implantado na máquina virtual. O pacote publicado deve ter um script de implantação chamado deploy.ps1 ou deploy.sh na pasta deployscripts na pasta raiz do pacote. O pipeline executa esse script de implantação.
1. Em **Estratégia de implantação**, selecione **Azul-verde**.
1. Adicione uma tag "azul" ou "verde" às VMs que devem fazer parte das implantações do tipo azul-verde. Se uma VM for para uma função em espera, marque-a como "verde". Caso contrário, marque-a como "azul".

   ![O painel de Entrega contínua, com o valor da estratégia de implantação azul-verde escolhido](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

1. Selecione **OK** para configurar o pipeline de entrega contínua para implantar na máquina virtual.

   ![O pipeline azul-verde](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

1. Os detalhes da implantação para a máquina virtual são exibidos. Você pode selecionar o link para acessar o pipeline de lançamento no Azure DevOps. No pipeline de lançamento, selecione **Editar** para ver a configuração do pipeline. O pipeline tem estas três fases:

   1. Esta fase é uma fase de grupo de implantação. Os aplicativos são implantados em VMs em espera, que são marcadas como "verdes".
   1. Nessa fase, pipeline é colocado em pausa e aguarda a intervenção manual para retomar a execução. Os usuários podem retomar a execução do pipeline depois de garantir manualmente a estabilidade da implantação para as VMs marcadas como "verdes".
   1. Essa fase troca as marcas "azul" e "verde" nas VMs. Isso garante que as VMs com versões mais antigas do aplicativo agora estejam marcadas como "verdes". Durante a próxima execução do pipeline, os aplicativos serão implantados nessas VMs.

      ![O painel do grupo de implantação para a tarefa Implantar Azul-Verde](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

1. A tarefa Executar Script de Implantação, por padrão, executa o script de implantação deploy.ps1 ou deploy.sh. O script está na pasta deployscripts na pasta raiz do pacote publicado. Verifique se o pipeline de build selecionado publica a implantação na pasta raiz do pacote.

   ![O painel Artefatos mostrando deploy.sh na pasta deployscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Outras estratégias de implantação

- [Configurar a estratégia de implantação distribuída](./tutorial-devops-azure-pipelines-classic.md)
- [Configurar a estratégia de implantação canário](./tutorial-azure-devops-canary-strategy.md)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

Você pode começar a usar o Azure facilmente. Com o Azure DevOps Projects, comece a executar seu aplicativo em qualquer serviço do Azure em apenas três etapas, selecionando:

- Uma linguagem do aplicativo
- Um runtime
- Um serviço do Azure

[Saiba mais](https://azure.microsoft.com/features/devops-projects/).

## <a name="additional-resources"></a>Recursos adicionais

- [Implantar em máquinas virtuais do Azure usando Azure DevOps Projects](../../devops-project/azure-devops-project-vms.md)
- [Implementar a implantação contínua do aplicativo em um conjunto de dimensionamento de máquinas virtuais do Azure](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)