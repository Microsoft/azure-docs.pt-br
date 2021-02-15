---
title: Solução de problemas de exibições de custo empresarial do Azure
description: Saiba como resolver quaisquer problemas com modos de exibição de custo organizacional no Portal do Azure.
author: bandersmsft
ms.reviewer: amberb
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: troubleshooting
ms.date: 08/20/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: bd0abadb624bced7f603fcbd112f7c422e88fc0c
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371840"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Solucionar problemas de exibições de custo empresarial

Nos registros empresariais, há várias configurações que podem fazer com que os usuários do registro não consigam ver os custos.  Essas configurações são gerenciadas pelo administrador de registros. Ou, se o registro não foi comprado diretamente por meio da Microsoft, as configurações são gerenciadas pelo parceiro.  Este artigo ajuda você a entender quais são as configurações e como elas afetam o registro. Essas configurações são independentes das funções do Azure.

## <a name="enable-access-to-costs"></a>Habilitar o acesso aos custos

Você está vendo uma mensagem de Não autorizado ou *"As exibições de custo estão desabilitadas em seu registro."* ao procurar informações sobre custo?
![Captura de tela que mostra "não autorizado" no campo Custo Atual para a assinatura.](./media/enterprise-mgmt-grp-troubleshoot-cost-view/unauthorized.png)

Isso pode acontecer devido a um dos seguintes motivos:

1. Você adquiriu o Azure por meio de um parceiro empresarial e o parceiro ainda não liberou o preço. Entre em contato com seu parceiro para atualizar a configuração de preço no [Enterprise Portal](https://ea.azure.com).
2. Se você é um cliente direto de EA, há algumas possibilidades:
    * Você é um Proprietário de Conta e seu Administrador de Registros desabilitou a configuração **Exibir encargos do AO**.  
    * Você é um Administrador de Departamento e seu Administrador de Registros desabilitou a configuração **Exibir encargos do DA**.
    * Entre em contato com o Administrador de registro para obter acesso. O administrador de registros pode atualizar as configurações no [Enterprise Portal](https://ea.azure.com/manage/enrollment).

      ![Captura de tela que mostra as configurações do Enterprise Portal para exibir encargos.](./media/enterprise-mgmt-grp-troubleshoot-cost-view/ea-portal-settings.png)

## <a name="asset-is-unavailable"></a>Ativo está indisponível

Se você receber uma mensagem de erro **Este ativo não está disponível** ao tentar acessar uma assinatura ou um grupo de gerenciamento, isso significará que você não tem a função correta para exibir esse item.  

![Captura de tela que mostra a mensagem "O ativo está indisponível".](./media/enterprise-mgmt-grp-troubleshoot-cost-view/asset-not-found.png)

Solicite acesso ao administrador do grupo de gerenciamento ou da assinatura do Azure. Para obter mais informações, confira [Adicionar ou remover atribuições de função do Azure usando o portal do Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Próximas etapas
- Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
