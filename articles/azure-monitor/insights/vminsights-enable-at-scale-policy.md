---
title: Habilitar o Azure Monitor para VMs usando a política do Azure | Microsoft Docs
description: Este artigo descreve como habilitar o Azure Monitor para máquinas virtuais para várias máquinas virtuais do Azure ou conjuntos de dimensionamento de máquina virtual por meio da política do Azure.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: magoedte
ms.openlocfilehash: cf06004c70609dbea59a47b207e3568299260a82
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594443"
---
# <a name="enable-azure-monitor-for-vms-preview-by-using-azure-policy"></a>Habilitar o Azure Monitor para máquinas virtuais (versão prévia) por meio da política do Azure

Este artigo explica como habilitar o Azure Monitor para máquinas virtuais (versão prévia) para máquinas virtuais ou conjuntos de dimensionamento de máquina virtual por meio da política do Azure. No final deste processo, você terá com êxito configurado a habilitação do Log Analytics e agentes de dependência e identificamos máquinas virtuais que não são compatíveis.

Para descobrir, gerenciar e habilitar o Azure Monitor para máquinas virtuais para todas as suas máquinas virtuais ou conjuntos de dimensionamento de máquina virtual, você pode usar a política do Azure ou Azure PowerShell. O Azure Policy é o método recomendado porque você pode gerenciar as definições de política para controlar efetivamente suas assinaturas para garantir a conformidade consistente e habilitação automática de recentemente provisionado VMs. Essas definições de política:

* Implantam o agente do Log Analytics e o Dependency Agent.
* Relatam os resultados da conformidade.
* Corrigi para VMs incompatíveis.

Se você estiver interessado em realizar essas tarefas com o Azure PowerShell ou um modelo do Azure Resource Manager, consulte [habilitar o Azure Monitor para máquinas virtuais usando modelos do Azure PowerShell ou Gerenciador de recursos do Azure (visualização)](vminsights-enable-at-scale-powershell.md).

## <a name="manage-policy-coverage-feature-overview"></a>Gerenciar visão geral do recurso de cobertura de política

Originalmente, a experiência com o Azure Policy para gerenciar e implantar as definições de política para o Azure Monitor para máquinas virtuais era feita exclusivamente do Azure Policy. O recurso de cobertura de política gerenciar torna mais simples e mais fácil de descobrir, gerenciar e habilitar em grande escala a **habilitar o Azure Monitor para VMs** iniciativa, que inclui as definições de política mencionadas anteriormente. Você pode acessar esse novo recurso do **começar** guia no Azure Monitor para máquinas virtuais. Selecione **Gerenciar política de cobertura** para abrir o **Azure Monitor para a cobertura de política de VMs** página.

![O Azure Monitor na guia máquinas virtuais começar](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

A partir daqui, você pode verificar e gerenciar cobertura para a iniciativa por grupos de gerenciamento e assinaturas. Você pode entender quantas VMs existem em cada um dos grupos de gerenciamento, assinaturas e seus status de conformidade.

![O Azure Monitor para a página de política de gerenciar VMs](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Essas informações são úteis para ajudá-lo a planejar e executar seu cenário de governança para o Azure Monitor para VMs de um local central. Enquanto a política do Azure fornece uma exibição de conformidade quando uma política ou iniciativa é atribuída a um escopo, com essa nova página você pode descobrir onde a política ou iniciativa não está atribuída e atribuí-la no lugar. Todas as ações, como atribuir, exibir e editar diretamente o redirecionamento para a política do Azure. O **Azure Monitor para a cobertura de política de VMs** página é uma experiência de expandida e integrada para apenas a iniciativa **habilitar o Azure Monitor para VMs**.

Nessa página, você também pode configurar seu espaço de trabalho do Log Analytics para o Azure Monitor para VMs, que:

- Instala as soluções de mapa do serviço de instalação e Insights de infraestrutura.
- Permite que os contadores de desempenho do sistema operacional usados pelos gráficos de desempenho, pastas de trabalho e suas consultas de log personalizado e alertas.

![Configurar o Azure Monitor para VMs do espaço de trabalho](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Essa opção não está relacionada a qualquer ação de política. Ele está disponível para fornecer uma maneira fácil de atender a [pré-requisitos](vminsights-enable-overview.md) necessário para habilitar o Azure Monitor para VMs.  

### <a name="what-information-is-available-on-this-page"></a>Quais informações estão disponíveis nesta página?
A tabela a seguir fornece uma análise das informações que são apresentadas na página de cobertura de política e como interpretá-lo.

| Função | DESCRIÇÃO | 
|----------|-------------| 
| **Escopo** | Grupo de gerenciamento e assinaturas que você possui ou acesso herdado com capacidade de fazer uma busca detalhada por meio da hierarquia de grupo de gerenciamento.|
| **Função** | A função para o escopo, que pode ser leitor, proprietário ou colaborador. Em alguns casos, pode parecer em branco para indicar que talvez você tenha acesso à assinatura, mas não para o grupo de gerenciamento pertence a. Informações de outras colunas variam dependendo da sua função. A função é a chave para determinar quais dados você pode ver e ações que você pode executar em termos de atribuição de políticas ou iniciativas (proprietário), editá-los ou exibir a conformidade. |
| **Total de VMs** | Número de VMs sob o escopo. Para um grupo de gerenciamento é uma soma de VMs aninhado sob o grupo de gerenciamento filho ou assinaturas. |
| **Cobertura de atribuição** | Porcentagem de VMs que são cobertas pela política ou iniciativa. |
| **Status de atribuição** | Informações sobre o status de sua atribuição de política ou iniciativa. |
| **VMs compatíveis** | Número de VMs que são compatíveis com a política ou iniciativa. Para a iniciativa **habilitar o Azure Monitor para VMs**, este é o número de VMs que têm o agente do Log Analytics e o agente de dependência. Em alguns casos, pode parecer em branco por causa de nenhuma atribuição, nenhuma VM ou não há permissões suficientes. Informações são fornecidas em **estado de conformidade**. |
| **Conformidade** | O número da conformidade geral é a soma dos recursos distintos que são compatíveis com dividido pela soma de todos os recursos distintos. |
| **Estado de conformidade** | Informações sobre o estado de conformidade para sua atribuição de política ou iniciativa.|

Quando você atribui a política ou iniciativa, o escopo selecionado na atribuição pode ser o escopo listados ou um subconjunto dela. Por exemplo, você pode ter criado uma atribuição para uma assinatura (escopo de política) e não um grupo de gerenciamento (escopo de cobertura). Nesse caso, o valor de **cobertura de atribuição** indica as VMs na política ou iniciativa escopo dividido por máquinas virtuais no escopo de cobertura. Em outro caso, você pode ter excluído algumas VMs, grupos de recursos ou uma assinatura do escopo de política. Se o valor estiver em branco, isso indica que a política ou iniciativa não existe ou você não tem permissão. Informações são fornecidas em **Status de atribuição**.

## <a name="enable-by-using-azure-policy"></a>Habilitar usando o Azure Policy

Para habilitar o Azure Monitor para VMs usando o Azure Policy no locatário:

- Atribua a iniciativa a um escopo: grupo de gerenciamento, assinatura ou grupo de recursos.
- Revisar e corrigir os resultados de conformidade.

Para obter mais informações sobre como atribuir o Azure Policy, confira [Visão geral do Azure Policy](../../governance/policy/overview.md#policy-assignment) e examine a [Visão geral dos grupos de gerenciamento](../../governance/management-groups/index.md) antes de continuar.

### <a name="policies-for-azure-vms"></a>Políticas para VMs do Azure

As definições de política para uma VM do Azure são listadas na tabela a seguir.

|Nome |DESCRIÇÃO |Tipo |
|-----|------------|-----|
|\[Versão Prévia\]: Habilitar o Azure Monitor para VMs |Habilite o Azure Monitor para as máquinas virtuais no escopo especificado (grupo de gerenciamento, assinatura ou grupo de recursos). Usa o espaço de trabalho do Log Analytics como parâmetro. |Iniciativa |
|\[Versão Prévia\]: Implantação do agente de dependência – imagem de VM (SO) removido da lista de auditoria |Relata as VMs como não compatível se a imagem de VM (SO) não está definida na lista e o agente não está instalado. |Política |
|\[Versão Prévia\]: Implantação do agente do Log Analytics – imagem de VM (SO) removido da lista de auditoria |Relata as VMs como não compatível se a imagem de VM (SO) não está definida na lista e o agente não está instalado. |Política |
|\[Versão Prévia\]: Implantar agente de dependência para VMs do Linux |Implante o agente de dependência para VMs do Linux se a imagem de VM (SO) é definida na lista e o agente não está instalado. |Política |
|\[Versão Prévia\]: Implantar agente de dependência para VMs do Windows |Implante o agente de dependência para VMs do Windows se a imagem de VM (SO) é definida na lista e o agente não está instalado. |Política |
|\[Versão Prévia\]: Implantar o agente do Log Analytics para VMs do Linux |Implante o agente do Log Analytics para VMs do Linux se a imagem de VM (SO) é definida na lista e o agente não está instalado. |Política |
|\[Versão Prévia\]: Implantar o agente do Log Analytics para VMs do Windows |Implante o agente do Log Analytics para VMs do Windows se a imagem de VM (SO) é definida na lista e o agente não está instalado. |Política |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de políticas para a máquina virtual do Azure

As definições de política para um conjunto de dimensionamento de máquina virtual do Azure são listadas na tabela a seguir.

|Nome |DESCRIÇÃO |Tipo |
|-----|------------|-----|
|\[Versão Prévia\]: Habilitar o Azure Monitor para conjuntos de dimensionamento de máquina virtual |Habilite o Azure Monitor para os conjuntos de dimensionamento de máquinas virtuais no escopo especificado (grupo de gerenciamento, assinatura ou grupo de recursos). Usa o espaço de trabalho do Log Analytics como parâmetro. Observação: Se sua política de atualização de conjunto de dimensionamento é definida como Manual, aplica a extensão para todas as VMs no conjunto ao chamar o upgrade nelas. Na CLI, isso é az vmss update-instances. |Iniciativa |
|\[Versão Prévia\]: Implantação de agente de dependência em conjuntos de dimensionamento de máquinas virtuais – imagem de VM (SO) removido da lista de auditoria |Relatórios de dimensionamento de máquinas virtuais definido como não compatível se a imagem de VM (SO) não está definida na lista e o agente não está instalado. |Política |
|\[Versão Prévia\]: Implantação do agente do Log Analytics em conjuntos de dimensionamento de máquinas virtuais – imagem de VM (SO) removido da lista de auditoria |Relatórios de dimensionamento de máquinas virtuais definido como não compatível se a imagem de VM (SO) não está definida na lista e o agente não está instalado. |Política |
|\[Versão Prévia\]: Implantar agente de dependência para conjuntos de dimensionamento de máquina virtual do Linux |Implante o agente de dependência para Linux, conjuntos de dimensionamento de máquina virtual se a imagem de VM (SO) é definida na lista e o agente não está instalado. |Política |
|\[Versão Prévia\]: Implantar agente de dependência para conjuntos de dimensionamento de máquinas virtuais do Windows |Implante o agente de dependência para Windows, conjuntos de dimensionamento de máquina virtual se a imagem de VM (SO) é definida na lista e o agente não está instalado. |Política |
|\[Versão Prévia\]: Implantar o agente do Log Analytics para conjuntos de dimensionamento de máquina virtual do Linux |Implante o agente do Log Analytics para Linux, conjuntos de dimensionamento de máquina virtual se a imagem de VM (SO) é definido na lista e o agente não está instalado. |Política |
|\[Versão Prévia\]: Implantar o agente do Log Analytics para conjuntos de dimensionamento de máquinas virtuais do Windows |Implante o agente do Log Analytics para Windows, conjuntos de dimensionamento de máquina virtual se a imagem de VM (SO) é definida na lista e o agente não está instalado. |Política |

A política independente (não incluída com a iniciativa) é descrita aqui:

|NOME |DESCRIÇÃO |Tipo |
|-----|------------|-----|
|\[Versão Prévia\]: Espaço de trabalho de análise de Log de auditoria para a VM – incompatibilidade de relatório |Relatórios de VMs como não compatível se eles não estão se conectando ao espaço de trabalho do Log Analytics especificado na atribuição de política ou iniciativa. |Política |

### <a name="assign-the-azure-monitor-initiative"></a>Atribuir a iniciativa do Azure Monitor
Para criar a atribuição de política do **Azure Monitor para a cobertura de política de VMs** página, siga estas etapas. Para entender como concluir essas etapas, confira  [Criar uma atribuição de política no portal do Azure](../../governance/policy/assign-policy-portal.md).

Quando você atribui a política ou iniciativa, o escopo selecionado na atribuição pode ser o escopo listados aqui ou um subconjunto dela. Por exemplo, você pode ter criado uma atribuição para a assinatura (escopo de política) e não o grupo de gerenciamento (escopo de cobertura). Nesse caso, a porcentagem de cobertura indicaria as VMs na política ou iniciativa escopo dividido por máquinas virtuais no escopo de cobertura. Em outro caso, você pode ter excluído algumas VMs ou grupos de recursos ou uma assinatura do escopo de política. Se ele estiver em branco, ele indica que a política ou iniciativa não existe ou você não tem permissões. Informações são fornecidas em **Status de atribuição**.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No portal do Azure, selecione **Monitor**. 

3. Escolha **Máquinas Virtuais (versão prévia)** na seção **Insights**.
 
4. Selecione o **começar** guia. Na página, selecione **Gerenciar política de cobertura**.

5. Selecione um grupo de gerenciamento ou uma assinatura da tabela. Selecione **escopo** selecionando as reticências (...). No exemplo, um escopo limita a atribuição de política a um agrupamento de máquinas virtuais para a imposição.

6. Sobre o **atribuição de política do Azure** página, ele tem preenchido previamente com a iniciativa **habilitar o Azure Monitor para VMs**. 
    O **nome da atribuição** caixa é preenchida automaticamente com o nome da iniciativa, mas você pode alterá-lo. Você também pode adicionar uma descrição opcional. O **atribuído pelo** caixa é preenchida automaticamente com base em quem está conectado. Esse valor é opcional.

7. (Opcional) Para remover um ou mais recursos do escopo, selecione **Exclusões**.

8. Na lista suspensa **Espaço de Trabalho do Log Analytics** da região com suporte, selecione um espaço de trabalho.

   > [!NOTE]
   > Se o workspace estiver fora do escopo da atribuição, conceda as permissões *Colaborador do Log Analytics* à ID de Entidade de Segurança da atribuição de política. Se você não fizer isso, você poderá ver uma falha de implantação, como `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` para conceder acesso, examine [como configurar manualmente a identidade gerenciada](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  O **identidade gerenciada** caixa de seleção é marcada como a iniciativa que está sendo atribuída inclui uma política com as *deployIfNotExists* em vigor.
    
9. Na lista suspensa **Gerenciar localização de identidade**, selecione a região apropriada.

10. Selecione **Atribuir**.

Depois de criar a atribuição, o **Azure Monitor para a cobertura de política de VMs** atualização de página **cobertura de atribuição**, **Status de atribuição**, **em conformidade VMs**, e **estado de conformidade** para refletir as alterações. 

A matriz a seguir mapeia cada estado de conformidade possíveis para a iniciativa.  

| Estado de conformidade | DESCRIÇÃO | 
|------------------|-------------|
| **Em conformidade** | Todas as VMs no escopo têm os agentes de Log Analytics e dependência implantados para eles.|
| **Não está em conformidade** | Nem todas as VMs no escopo tem o Log Analytics e agentes de dependência implantada para eles e podem exigir a correção.|
| **Não foi iniciado** | Uma nova atribuição foi adicionada. |
| **Bloqueio** | Você não tem privilégios suficientes para o grupo de gerenciamento. <sup>1</sup> | 
| **Em branco** | Nenhuma política for atribuída. | 

<sup>1</sup> se você não tiver acesso ao grupo de gerenciamento, peça ao proprietário para fornecer acesso. Ou, exibir a conformidade e gerenciar atribuições por meio de grupos de gerenciamento filhos ou assinaturas. 

A tabela a seguir mapeia cada status de atribuição possíveis para a iniciativa.

| Status de atribuição | DESCRIÇÃO | 
|------------------|-------------|
| **Êxito** | Todas as VMs no escopo têm os agentes de Log Analytics e dependência implantados para eles.|
| **Aviso** | A assinatura não está em um grupo de gerenciamento.|
| **Não foi iniciado** | Uma nova atribuição foi adicionada. |
| **Bloqueio** | Você não tem privilégios suficientes para o grupo de gerenciamento. <sup>1</sup> | 
| **Em branco** | Nenhuma VM existe ou não está atribuída a uma política. | 
| **Ação** | Atribuir uma política ou editar uma atribuição. | 

<sup>1</sup> se você não tiver acesso ao grupo de gerenciamento, peça ao proprietário para fornecer acesso. Ou, exibir a conformidade e gerenciar atribuições por meio de grupos de gerenciamento filhos ou assinaturas.

## <a name="review-and-remediate-the-compliance-results"></a>Revisar e corrigir os resultados de conformidade

O exemplo a seguir é uma VM do Azure, mas ele também se aplica a conjuntos de dimensionamento de máquina virtual. Para saber como examinar os resultados de conformidade, consulte [identificar os resultados de não conformidade](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). Sobre o **Azure Monitor para a cobertura de política de VMs** , selecione um grupo de gerenciamento ou uma assinatura da tabela. Selecione **exibir a conformidade** selecionando as reticências (...).   

![Conformidade com a política para máquinas virtuais do Azure](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

VMs com base nos resultados das diretivas incluídas com a iniciativa, são relatadas como não compatível nos seguintes cenários:

* Agente do log Analytics ou o agente de dependência não será implantado.  
    Esse cenário é típico para um escopo com as VMs existentes. Para resolvê-lo, implantar os agentes necessários por [criação de tarefas de correção](../../governance/policy/how-to/remediate-resources.md) em uma política não compatível.  
    - \[Versão Prévia\]: Implantar agente de dependência para VMs do Linux
    - \[Versão Prévia\]: Implantar agente de dependência para VMs do Windows
    - \[Versão Prévia\]: Implantar o agente do Log Analytics para VMs do Linux
    - \[Versão Prévia\]: Implantar o agente do Log Analytics para VMs do Windows

* Imagem de VM (SO) não está identificada na definição de política.  
    Os critérios da política de implantação incluem apenas as VMs implantadas com base em imagens de VM conhecidas do Azure. Verifique a documentação para ver se o sistema operacional da VM é compatível. Caso ele não seja, duplique a política de implantação e atualize-a ou modifique-a para fazer com que a imagem fique em conformidade.  
    - \[Versão Prévia\]: Implantação do agente de dependência – imagem de VM (SO) removido da lista de auditoria
    - \[Versão Prévia\]: Implantação do agente do Log Analytics – imagem de VM (SO) removido da lista de auditoria

* As VMs não estão fazendo logon no espaço de trabalho do Log Analytics especificado.  
    É possível que algumas VMs no escopo da iniciativa estejam fazendo logon em um espaço de trabalho do Log Analytics diferente daquele especificado na atribuição de política. Essa política é uma ferramenta para identificar quais VMs estão relatando para um espaço de trabalho fora de conformidade.  
    - \[Versão Prévia\]: Espaço de trabalho de análise de Log de auditoria para a VM – incompatibilidade de relatório

## <a name="edit-an-initiative-assignment"></a>Editar uma atribuição de iniciativa

A qualquer momento depois de atribuir uma iniciativa para um grupo de gerenciamento ou a assinatura, você pode editá-lo para modificar as propriedades a seguir:

- Nome da atribuição
- DESCRIÇÃO
- Atribuída por
- Espaço de trabalho do Log Analytics
- Exceções

## <a name="next-steps"></a>Próximas etapas

Agora que o monitoramento está habilitado para suas máquinas virtuais, essas informações estão disponíveis para análise com o Azure Monitor para VMs. 

- Para saber como usar o recurso de integridade, consulte [modo de exibição do Azure Monitor de integridade de VMs](vminsights-health.md). 
- Para exibir as dependências de aplicativos descobertas, confira [Exibir o Mapa do Azure Monitor para VMs](vminsights-maps.md). 
- Para identificar gargalos e a utilização geral do desempenho da sua VM, consulte [desempenho da VM do Azure de modo de exibição](vminsights-performance.md). 
- Para exibir as dependências de aplicativos descobertas, confira [Exibir o Mapa do Azure Monitor para VMs](vminsights-maps.md).
