---
title: Solucionar erros comuns de integração de autogerenciamento do Azure
description: Erros comuns de integração de autogerenciamento e como solucioná-los
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alsin
ms.openlocfilehash: 18165ce5f39b32fe1c5af28bc88e8e1bd0e9cb62
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104955543"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Solucionar erros comuns de integração de autogerenciamento
O autogerenci pode falhar ao carregar um computador no serviço. Este documento explica como solucionar problemas de falhas de implantação, compartilha alguns motivos comuns pelos quais as implantações podem falhar e descreve as próximas etapas potenciais na mitigação.

## <a name="troubleshooting-deployment-failures"></a>Solucionando problemas de falhas de implantação
A integração de um computador com o autogerencio resultará na criação de uma Azure Resource Manager implantação. Se a integração falhar, poderá ser útil consultar a implantação para obter mais detalhes sobre o motivo da falha. Há links para as implantações no submenu de detalhes da falha, mostrado abaixo.

:::image type="content" source="media\common-errors\failure-flyout.png" alt-text="Submenu de detalhes de falha de autogerenciamento.":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>Verificar as implantações do grupo de recursos que contém a VM com falha
O submenu de falha conterá um link para as implantações dentro do grupo de recursos que contém o computador que falhou na integração e um nome de prefixo que você pode usar para filtrar as implantações com o. Clicar no link levará você para a folha implantações, na qual você pode filtrar implantações para ver as implantações de gerenciamento automático em seu computador. Se você estiver implantando em várias regiões, certifique-se de clicar na implantação na região correta.

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>Verificar as implantações da assinatura que contém a VM com falha
Se você não vir nenhuma falha na implantação do grupo de recursos, a próxima etapa será examinar as implantações em sua assinatura que contém a VM que falhou na integração. Clique no link **implantações para assinatura** no submenu falha e filtre implantações usando o filtro **autogerenciar-defaultresource** . Use o nome do grupo de recursos da folha falha para filtrar as implantações. O nome da implantação será sufixado com um nome de região. Se você estiver implantando em várias regiões, certifique-se de clicar na implantação na região correta.

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Verificar implantações em uma assinatura vinculada a um espaço de trabalho Log Analytics
Se você não vir nenhuma implantação com falha no grupo de recursos ou assinatura que contém a VM com falha e se a VM com falha estiver conectada a um espaço de trabalho do Log Analytics em uma assinatura diferente, vá para a assinatura vinculada ao espaço de trabalho do Log Analytics e verifique se há implantações com falha.

## <a name="common-deployment-errors"></a>Erros de implantação comuns

Erro |  Atenuação
:-----|:-------------|
Erro de permissões insuficientes na conta de autogerenciamento | Isso pode acontecer se você tiver movido recentemente uma assinatura que contém uma nova conta de autogerenciamento para um novo locatário. As etapas para resolver isso estão localizadas [aqui](./repair-automanage-account.md).
A região do espaço de trabalho não corresponde aos requisitos de mapeamento de região | O autogerenci não conseguiu carregar seu computador, mas o espaço de trabalho Log Analytics ao qual o computador está vinculado não está mapeado para uma região de automação com suporte. Verifique se o espaço de trabalho Log Analytics existente e a conta de automação estão localizados em um [mapeamento de região com suporte](../automation/how-to/region-mappings.md).
"Acesso negado devido à atribuição de negação com o nome ' atribuição de negação do sistema criada pelo aplicativo gerenciado '" | Um [denyAssignment](../role-based-access-control/deny-assignments.md) foi criado em seu recurso, o que impediu o autogerenciamento de acessar seu recurso. Isso pode ter sido causado por um [plano gráfico](../governance/blueprints/concepts/resource-locking.md) ou um [aplicativo gerenciado](../azure-resource-manager/managed-applications/overview.md).
"Informações do sistema operacional: nome = ' (NULL) ', ver = ' (NULL) ', status do agente = ' não está pronto '." | Verifique se você está executando uma [versão mínima do agente com suporte](/troubleshoot/azure/virtual-machines/support-extensions-agent-version), se o agente está em execução ([Linux](/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) e [Windows](/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)) e se o agente está atualizado ([Linux](../virtual-machines/extensions/update-linux-agent.md) e [Windows](../virtual-machines/extensions/agent-windows.md)).
"A VM relatou uma falha ao processar a extensão ' Iaasantimalware da '" | Verifique se você não tem outra oferta de antimalware/antivírus já instalada em sua VM. Se isso falhar, contate o suporte.
Espaço de trabalho ASC: o autogerenci não dá suporte atualmente ao serviço de Log Analytics no _local_. | Verifique se sua VM está localizada em uma [região com suporte](./automanage-virtual-machines.md#supported-regions).
A implantação de modelo falhou por causa da violação de política. Consulte os detalhes para obter mais informações. | Há uma política que impede o autogerenciamento de integrar sua VM. Verifique as políticas que são aplicadas à sua assinatura ou grupo de recursos que contém a VM que você deseja integrar ao autogerenci.
"A atribuição falhou; Não há informações adicionais disponíveis " | Abra um caso com suporte Microsoft Azure.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o autogerenciamento do Azure](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [Habilitar o autogerenci para máquinas virtuais no portal do Azure](quick-create-virtual-machines-portal.md)