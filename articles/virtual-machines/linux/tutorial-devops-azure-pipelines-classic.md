---
title: Tutorial – Configurar implantações sem interrupção para máquinas virtuais do Linux do Azure
description: Neste tutorial, você aprenderá a configurar um pipeline de CD (implantação contínua). Esse pipeline atualiza incrementalmente um grupo de máquinas virtuais do Linux do Azure usando a estratégia de implantação sem interrupção.
author: moala
manager: jpconnock
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
ms.openlocfilehash: dd47250989be5c31d5f0ade2b602b9d6af535d83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563992"
---
# <a name="tutorial---configure-the-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>Tutorial – Configurar estratégia de implantação sem interrupção para Máquinas Virtuais do Linux do Azure

O Azure DevOps é um serviço interno do Azure que automatiza cada parte do processo de DevOps para qualquer recurso do Azure. Quer o aplicativo use máquinas virtuais, aplicativos Web, Kubernetes ou qualquer outro recurso, você pode implementar IaC (infraestrutura como código), integração contínua, teste contínuo, entrega contínua e monitoramento contínuo com o Azure e o Azure DevOps.

![O portal do Azure com o Azure DevOps selecionado em serviços](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png)

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>IaaS (infraestrutura como serviço) – configurar CI/CD

O Azure Pipelines fornece um conjunto de ferramentas de automação de CI/CD para implantações em máquinas virtuais. Você pode configurar um pipeline de entrega contínua para uma VM do Azure do portal do Azure.

Este artigo mostra como configurar um pipeline de CI/CD para a reversão de implantações de vários computadores do portal do Azure. O portal do Azure também dá suporte a outras estratégias, como [canário](./tutorial-azure-devops-canary-strategy.md) e [azul-verde](./tutorial-azure-devops-blue-green-strategy.md).

### <a name="configure-cicd-on-virtual-machines"></a>Configurar CI/CD em máquinas virtuais

Você pode adicionar máquinas virtuais como destinos a um [grupo de implantação](/azure/devops/pipelines/release/deployment-groups). Em seguida, você pode direcioná-las para atualizações de vários computadores. Depois de implantar o computadores, veja o **Histórico de Implantação** em um grupo de implantação. Essa exibição permite que você rastreie da VM para o pipeline e, em seguida, para a confirmação.

### <a name="rolling-deployments"></a>Implantações sem interrupção

Em cada iteração, uma implantação sem interrupção substitui as instâncias da versão anterior de um aplicativo. Ela os substitui por instâncias da nova versão em um conjunto fixo de computadores (conjunto dinâmico). As instruções a seguir mostram como configurar uma atualização sem interrupção para máquinas virtuais.

Usando a opção de entrega contínua, você pode configurar atualizações sem interrupção em suas máquinas virtuais dentro do portal do Azure. Veja um passo a passo do processo:

1. Entre no portal do Azure e navegue até uma máquina virtual.
1. No painel mais à esquerda das configurações da VM, selecione **Entrega contínua**. Em seguida, selecione **Configurar**.

   ![O painel Entrega contínua com o botão Configurar](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. No painel de configuração, clique em **Organização do Azure DevOps** para selecionar uma conta ou criar uma. Em seguida, selecione o projeto no qual deseja configurar o pipeline.  

   ![O painel de Entrega contínua](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Um grupo de implantação é um conjunto lógico de computadores de destino de implantação que representam os ambientes físicos. Desenvolvimento, teste, UAT e produção são exemplos. Você pode criar um grupo de implantação ou selecionar um existente.
1. Selecione o pipeline de build que publica o pacote a ser implantado na máquina virtual. O pacote publicado deve ter um script de implantação chamado deploy.ps1 ou deploy.sh na pasta deployscripts na pasta raiz do pacote. O pipeline executa esse script de implantação.
1. Em **Estratégia de implantação**, selecione **Sem interrupção**.
1. Opcionalmente, você pode marcar cada computador com sua função. As marcas "web" e "db" são exemplos. Essas marcas ajudam a direcionar apenas as VMs que têm uma função específica.
1. Selecione **OK** para configurar o pipeline de entrega contínua.
1. Após a conclusão da configuração, você tem um pipeline de entrega contínua configurado para implantar na máquina virtual.  

   ![O painel de entrega contínua mostrando o histórico de implantação](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)

1. Os detalhes da implantação para a máquina virtual são exibidos. Você pode selecionar o link para acessar o pipeline, **Release-1** para exibir a implantação ou **Editar** para modificar a definição do pipeline de lançamento.

1. Se você estiver configurando várias VMs, repita as etapas de 2 a 4 para outras VMs a serem adicionadas ao grupo de implantação. Se você selecionar um grupo de implantação que já tenha uma execução de pipeline, as VMs serão adicionadas apenas ao grupo de implantação. Nenhum pipeline é criado.
1. Após a conclusão da configuração, selecione a definição de pipeline, navegue até a organização Azure DevOps e selecione **Editar** para o pipeline de lançamento.

   ![Como editar o pipeline sem interrupção](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)

1. Selecione **1 trabalho, 1 tarefa** na fase **dev**. Selecione a fase **Implantar**.

   ![Tarefas do pipeline sem interrupção com a tarefa Implantar selecionada](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)

1. No painel de configuração à direita, você pode especificar o número de computadores que deseja implantar em paralelo em cada iteração. Se você quiser implantar em vários computadores por vez, poderá especificar o número de computadores como um percentual usando o controle deslizante.  

1. A tarefa Executar Script de Implantação, por padrão, executa o script de implantação deploy.ps1 ou deploy.sh. O script está na pasta deployscripts na pasta raiz do pacote publicado.

   ![O painel Artefatos mostrando deploy.sh na pasta deployscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Outras estratégias de implantação

- [Configurar a estratégia de implantação canário](./tutorial-azure-devops-canary-strategy.md)
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