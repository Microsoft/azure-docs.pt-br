---
title: Gerenciar atualizações e patches para suas VMs na automação do Azure
description: Este artigo informa como usar Gerenciamento de Atualizações para gerenciar atualizações e patches para suas VMs do Azure e não Azure.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: c86c9049bc0afc81f5dfd8553d2aa98cfd4b1a46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98915975"
---
# <a name="manage-updates-and-patches-for-your-vms"></a>Gerenciar atualizações e patches para suas VMs

As atualizações de software no Azure Automation Gerenciamento de Atualizações fornecem um conjunto de ferramentas e recursos que podem ajudar a gerenciar a tarefa complexa de rastrear e aplicar atualizações de software a computadores no Azure e na nuvem híbrida. Um processo de gerenciamento de atualização de software eficaz é necessário para manter a eficiência operacional, superar problemas de segurança e reduzir os riscos de ameaças de segurança cibernéticos maiores. No entanto, devido à natureza variável da tecnologia e ao aparecimento contínuo de novas ameaças à segurança, a eficiência do gerenciamento de atualização de software exige atenção consistente e contínua.

> [!NOTE]
> O Gerenciamento de Atualizações dá suporte à implantação de atualizações de terceiros e ao pré-download delas. Esse suporte requer alterações nos sistemas que estão sendo atualizados. Confira [Definir configurações do Windows Update para o Gerenciamento de Atualizações da Automação do Azure](configure-wuagent.md) para saber como definir essas configurações em seus sistemas.

Antes de tentar gerenciar atualizações para suas VMs, verifique se você habilitou o Gerenciamento de Atualizações usando um destes métodos:

* [Habilitar o Gerenciamento de Atualizações de uma conta de Automação](enable-from-automation-account.md)
* [Habilitar o Gerenciamento de Atualizações navegando no portal do Azure](enable-from-portal.md)
* [Habilitar o Gerenciamento de Atualizações de um runbook](enable-from-runbook.md)
* [Habilitar o Gerenciamento de Atualizações de uma VM do Azure](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Limitar o escopo para a implantação

O Gerenciamento de Atualizações usa uma configuração de escopo dentro do workspace para definir os computadores a receberem atualizações. Para obter mais informações, confira [Limitar o escopo de implantação do Gerenciamento de Atualizações](scope-configuration.md).

## <a name="compliance-assessment"></a>Avaliação de conformidade

Antes de implantar as atualizações de software em seus computadores, examine os resultados da avaliação de conformidade da atualização para computadores habilitados. Para cada atualização de software, seu estado de conformidade é registrado e, depois que a avaliação é concluída, ele é coletado e encaminhado em massa para Azure Monitor logs.

Em um computador Windows, a verificação de conformidade é executada a cada 12 horas por padrão e é iniciada dentro de 15 minutos do Log Analytics agente para Windows é reiniciado. Os dados de avaliação são encaminhados para o espaço de trabalho e atualiza a tabela de **atualizações** . Antes e depois da instalação da atualização, uma verificação de conformidade de atualização é executada para identificar as atualizações ausentes, mas os resultados não são usados para atualizar os dados de avaliação na tabela.

É importante examinar nossas recomendações sobre como [Configurar o cliente do Windows Update](configure-wuagent.md) com gerenciamento de atualizações para evitar problemas que o impeçam de ser gerenciado corretamente.

Para um computador com Linux, a verificação de conformidade é executada a cada hora por padrão. Se o agente de Log Analytics para Linux for reiniciado, uma verificação de conformidade será iniciada dentro de 15 minutos.

Os resultados de conformidade são apresentados em Gerenciamento de Atualizações para cada computador avaliado. Pode levar até 30 minutos para o painel exibir dados atualizados de um novo computador habilitado para gerenciamento.

Examine [as atualizações de software do monitor](view-update-assessments.md) para saber como exibir os resultados de conformidade.

## <a name="deploy-updates"></a>Implantar atualizações e testar

Depois de examinar os resultados de conformidade, a fase de implantação de atualização de software é o processo de implantação de atualizações de software. Para instalar atualizações, agende uma implantação que alinhe-se com a agenda de liberação e período de serviço. Você pode escolher quais tipos de atualização deseja incluir na implantação. Por exemplo, você pode incluir atualizações críticas ou de segurança e excluir pacotes cumulativos de atualizações.

Examine [implantar atualizações de software](deploy-updates.md) para saber como agendar uma implantação de atualização.

## <a name="review-update-deployments"></a>Examinar implantações de atualização

Após a conclusão da implantação, examine o processo para determinar o êxito da implantação de atualização por máquina ou grupo de destino. Consulte [examinar o status da implantação](deploy-updates.md#check-deployment-status) para saber como você pode monitorar o status da implantação.

## <a name="next-steps"></a>Próximas etapas

* Para saber como criar alertas para notificá-lo sobre os resultados da implantação de atualização, consulte [criar alertas para gerenciamento de atualizações](configure-alerts.md).

* Você pode [consultar logs de Azure monitor](query-logs.md) para analisar avaliações de atualização, implantações e outras tarefas de gerenciamento relacionadas. Ele inclui consultas predefinidas para ajudá-lo a começar.