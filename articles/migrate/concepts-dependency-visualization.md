---
title: Visualização de dependência em Migrações para Azure | Microsoft Docs
description: Fornece uma visão geral dos cálculos de avaliação no serviço de avaliação do servidor nas migrações para Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: hamusa
ms.openlocfilehash: 8934306efadc4ec732afbb658c081ada30f232cd
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68312215"
---
# <a name="dependency-visualization"></a>Visualização de dependência

Migrações para Azure: A avaliação do servidor avalia grupos de computadores locais para migração para o Azure. Você pode usar a funcionalidade de visualização de dependência na avaliação do servidor para criar grupos. Este artigo fornece informações sobre este recurso.

> [!NOTE]
> A funcionalidade de visualização de dependências não está disponível no Azure Governamental.

## <a name="overview"></a>Visão geral

A visualização de dependência na avaliação do servidor permite que você crie grupos de alta confiança para avaliações de migração. Usando a visualização de dependência, você pode exibir as dependências de rede de computadores e identificar os computadores relacionados que precisam ser migrados juntos para o Azure. Essa funcionalidade é útil em cenários em que você não está completamente ciente dos computadores que constituem o aplicativo e precisam ser migrados em conjunto para o Azure.

## <a name="before-you-start"></a>Antes de começar

- Certifique-se de ter [criado](how-to-add-tool-first-time.md) um projeto de migrações para Azure.
- Se você já tiver criado um projeto, certifique-se de ter [adicionado](how-to-assess.md) a migração do Azure: Ferramenta de avaliação do servidor.
- Verifique se você descobriu seus computadores nas migrações para Azure; Você pode fazer isso Configurando um dispositivo de migrações para Azure para [VMware](how-to-set-up-appliance-vmware.md) ou [Hyper-V](how-to-set-up-appliance-hyper-v.md). O dispositivo descobre computadores locais e envia metadados e dados de desempenho para migrações para o Azure: Avaliação do servidor. [Saiba mais](migrate-appliance.md).

## <a name="how-does-it-work"></a>Como ele funciona?

As migrações para Azure usam a solução [mapa do serviço](../operations-management-suite/operations-management-suite-service-map.md) em [logs de Azure monitor](../log-analytics/log-analytics-overview.md) para visualização de dependência.
- Para aproveitar a visualização de dependência, você precisa associar um espaço de trabalho do Log Analytics novo ou existente a um projeto das Migrações para Azure.
- Você só pode criar ou anexar um espaço de trabalho na mesma assinatura em que o projeto de migrações para Azure é criado.
- Para anexar um espaço de trabalho Log Analytics a um projeto:
    1. Na guia **servidores** , em **migrações para Azure: Bloco avaliação** do servidor, clique em **visão geral**.
    2. Em **visão geral**, clique na seta para baixo para expandir o **Essentials**.
    3. No **espaço de trabalho do OMS**, clique em **requer configuração**.
    4. Em **Configurar espaço de trabalho**, especifique se deseja criar um novo espaço de trabalho ou usar um existente:
    
    ![Adicionar espaço de trabalho](./media/how-to-create-group-machine-dependencies/workspace.png)

- Durante a associação de um workspace, haverá a opção de criar um workspace ou anexar um existente:
  - Ao criar um workspace, você precisará especificar um nome para ele. O workspace será criado em uma região na mesma [Geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) que o projeto de migração.
  - Ao anexar um workspace, você pode escolher entre todos os workspaces disponíveis na mesma assinatura que o projeto de migração. Observe que são listados somente os workspaces que foram criados em uma região [com suporte para o Mapa do Serviço](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions). É necessário ser capaz de anexar a um workspace, verifique se você tem acesso de 'Leitura' no workspace.

  > [!NOTE]
  > Depois de anexar um workspace a um projeto, você não poderá mais alterá-lo.

- O workspace associado é marcado com a chave **Projeto de Migração** e o valor **Nome do projeto**, que pode ser usado para pesquisar no portal do Azure.
- Para navegar até o workspace associado ao projeto, acesse a seção **Essentials** na página **Visão geral** do projeto e acesse o workspace

    ![Navegar no espaço de trabalho do Log Analytics](./media/concepts-dependency-visualization/oms-workspace.png)

Para usar a visualização de dependência, você precisa baixar e instalar agentes em cada computador local que você deseja analisar.  

- [O Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) precisa ser instalado em cada máquina. [Saiba mais](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma) sobre como instalar o agente do MMA.
- O [Agente de Dependência](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) precisa ser instalado em cada máquina. [Saiba mais](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent) sobre como instalar o Dependency Agent.
- Além disso, se você tiver máquinas sem conectividade com a Internet, será necessário fazer o download e instalar o gateway do Log Analytics nelas.

Você não precisa desses agentes nas máquinas que deseja avaliar, a menos que esteja usando a visualização de dependência.

## <a name="do-i-need-to-pay-for-it"></a>Eu preciso pagar por ele?

O recurso de visualização de dependência está disponível sem custo adicional. O uso do recurso de visualização de dependência na avaliação do servidor requer Mapa do Serviço e exige que você associe um espaço de trabalho Log Analytics, novo ou existente, ao projeto migrações para Azure. A funcionalidade de visualização de dependência na avaliação do servidor é gratuita para os primeiros 180 dias.

1. O uso de qualquer solução que não seja o Mapa do Serviço dentro desse espaço de trabalho do Log Analytics gerará cobranças do [Log Analytics Standard](https://azure.microsoft.com/pricing/details/log-analytics/).
2. Para dar suporte a cenários de migração sem custo adicional, a solução Mapa do Serviço não gerará nenhum encargo nos primeiros 180 dias desde o dia da associação do espaço de trabalho do Log Analytics ao projeto de Migrações para Azure. Após 180 dias, os encargos do Log Analytics Standard serão aplicados.

Quando você registrar agentes para o workspace, use a ID e a chave fornecida pelo projeto na página das etapas do agente de instalação.

Quando o projeto de Migrações para Azure é excluído, o workspace não é excluído junto com ele. Após a exclusão do projeto, o uso do Mapa do Serviço não será mais gratuito e cada nó será cobrado de acordo com a camada paga do espaço de trabalho do Log Analytics.

> [!NOTE]
> O recurso de visualização de dependência usa o Mapa do Serviço por meio de um espaço de trabalho do Log Analytics. Desde de 28 de fevereiro de 2018, com o anúncio da disponibilidade geral das Migrações para Azure, o recurso agora está disponível sem custo adicional. Você precisará criar um novo projeto para usar o workspace de uso gratuito. Os workspaces existentes antes da disponibilidade geral ainda serão cobrados, portanto, recomendamos que você passe para um novo projeto.

Saiba mais sobre os preços de Migrações para Azure [aqui](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="how-do-i-manage-the-workspace"></a>Como faço para gerenciar o workspace?

Você pode usar o espaço de trabalho do Log Analytics fora de Migrações para Azure. Ele não será excluído se você excluir o projeto de migrações para Azure no qual ele foi criado. Se você não precisar mais do workspace, [exclua-o](../azure-monitor/platform/manage-access.md) manualmente.

Não exclua o espaço de trabalho criado pela migração do Azure, a menos que você exclua o projeto de migrações para Azure. Se você fizer isso, a funcionalidade de visualização de dependência não funcionará conforme o esperado.

## <a name="next-steps"></a>Próximas etapas
- [Agrupar máquinas usando dependências da máquina](how-to-create-group-machine-dependencies.md)
- [Saiba mais](https://docs.microsoft.com/azure/migrate/resources-faq#what-is-dependency-visualization) sobre as perguntas frequentes na visualização de dependência.
