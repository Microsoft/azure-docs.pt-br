---
title: Solucionar problemas de exibições de custo empresarial do Azure
description: Saiba como resolver quaisquer problemas com modos de exibição de custo organizacional no Portal do Azure.
author: bandersmsft
manager: amberb
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 83f7f424b265582a3830c02973cbbb9962ddfbfb
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491269"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Solucionar problemas de exibições de custo empresarial

Nos registros empresariais, há várias configurações que podem fazer com que os usuários do registro não consigam ver os custos.  Essas configurações são gerenciadas pelo administrador de registros. Ou, se o registro não foi comprado diretamente por meio da Microsoft, as configurações são gerenciadas pelo parceiro.  Este artigo ajuda você a entender quais são as configurações e como elas afetam o registro. Essas configurações são independentes das funções de RBAC (Role-based access control) do Azure.

## <a name="enable-access-to-costs"></a>Habilitar o acesso aos custos

Você está vendo uma mensagem de Não autorizado ou *"As exibições de custo estão desabilitadas em seu registro."* ao procurar informações sobre custo?
![Captura de tela que mostra "não autorizado" no campo Custo Atual para a assinatura.](media/billing-enterprise-mgmt-groups/unauthorized.png)

Isso pode acontecer devido a um dos seguintes motivos:

1. Você adquiriu o Azure por meio de um parceiro empresarial e o parceiro ainda não liberou o preço. Entre em contato com seu parceiro para atualizar a configuração de preço no [Enterprise Portal](https://ea.azure.com).
2. Se você é um cliente direto de EA, há algumas possibilidades:
    * Você é um Proprietário de Conta e seu Administrador de Registros desabilitou a configuração **Exibir encargos do AO**.  
    * Você é um Administrador de Departamento e seu Administrador de Registros desabilitou a configuração **Exibir encargos do DA**.
    * Entre em contato com o Administrador de registro para obter acesso. O administrador de registros pode atualizar as configurações no [Enterprise Portal](https://ea.azure.com/manage/enrollment).

      ![Captura de tela que mostra as configurações do Enterprise Portal para exibir encargos.](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)

## <a name="asset-is-unavailable"></a>Ativo está indisponível

Se você receber uma mensagem de erro informando **este ativo está indisponível** ao tentar acessar uma assinatura ou grupo de gerenciamento, em seguida, você não tiver a função correta para exibir este item.  

![Captura de tela que mostra a mensagem "O ativo está indisponível".](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Solicite acesso ao administrador do grupo de gerenciamento ou da assinatura do Azure. Para obter mais informações, confira [gerenciar o acesso usando o portal do Azure e o RBAC](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Próximas etapas
- Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
