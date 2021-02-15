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
ms.openlocfilehash: 13388bf289c1d10a2e7da04a4dd5d26be109535e
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697776"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Solucionar erros comuns de integração de autogerenciamento
O autogerenci pode falhar ao carregar um computador no serviço. Este documento explica como solucionar problemas de falhas de implantação, compartilha alguns motivos comuns pelos quais as implantações podem falhar e descreve as próximas etapas potenciais na mitigação.

## <a name="troubleshooting-deployment-failures"></a>Solucionando problemas de falhas de implantação
A integração de um computador com o autogerencio resultará na criação de uma Azure Resource Manager implantação. Se a integração falhar, poderá ser útil consultar a implantação para obter mais detalhes sobre o motivo da falha. Há links para as implantações no submenu de detalhes da falha, mostrado abaixo.

:::image type="content" source="media\automanage-common-errors\failure-flyout.png" alt-text="Submenu de detalhes de falha de autogerenciamento.":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>Verificar as implantações do grupo de recursos que contém a VM com falha
O submenu de falha conterá um link para as implantações dentro do grupo de recursos que contém o computador que falhou na integração e um nome de prefixo que você pode usar para filtrar as implantações com o. Clicar no link levará você para a folha implantações, na qual você pode filtrar implantações para ver as implantações de gerenciamento automático em seu computador. Se você estiver implantando em várias regiões, certifique-se de clicar na implantação na região correta.

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>Verificar as implantações da assinatura que contém a VM com falha
Se você não vir nenhuma falha na implantação do grupo de recursos, a próxima etapa será examinar as implantações em sua assinatura que contém a VM que falhou na integração. Clique no link **implantações para assinatura** no submenu falha e filtre implantações usando o filtro **autogerenciar-defaultresource** . Use o nome do grupo de recursos da folha falha para filtrar as implantações. O nome da implantação será sufixado com um nome de região. Se você estiver implantando em várias regiões, certifique-se de clicar na implantação na região correta.

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Verificar implantações em uma assinatura vinculada a um espaço de trabalho Log Analytics
Se você não vir nenhuma implantação com falha no grupo de recursos ou assinatura que contém a VM com falha e se a VM com falha estiver conectada a um espaço de trabalho do Log Analytics em uma assinatura diferente, vá para a assinatura vinculada ao espaço de trabalho do Log Analytics e verifique se há implantações com falha.

## <a name="common-deployment-errors"></a>Erros de implantação comuns

Erro do |  Atenuação
:-----|:-------------|
Erro de permissões insuficientes na conta de autogerenciamento | Isso pode acontecer se você tiver movido recentemente uma assinatura que contém uma nova conta de autogerenciamento para um novo locatário. As etapas para resolver isso estão localizadas [aqui](./repair-automanage-account.md).
A região do espaço de trabalho não corresponde aos requisitos de mapeamento de região | O autogerenci não conseguiu carregar seu computador, mas o espaço de trabalho Log Analytics ao qual o computador está vinculado não está mapeado para uma região de automação com suporte. Verifique se o espaço de trabalho Log Analytics existente e a conta de automação estão localizados em um [mapeamento de região com suporte](https://docs.microsoft.com/azure/automation/how-to/region-mappings).
"A atribuição falhou; Não há informações adicionais disponíveis " | Abra um caso com suporte Microsoft Azure.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o autogerenciamento do Azure](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [Habilitar o autogerenci para máquinas virtuais no portal do Azure](quick-create-virtual-machines-portal.md)

