---
title: Solucionar problemas de exibições de custo corporativo do Azure | Microsoft Docs
description: Saiba como resolver quaisquer problemas com modos de exibição de custo organizacional no Portal do Azure.
author: rthorn17
manager: adpick
editor: ''
ms.assetid: ''
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2017
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: d35996b16d615a198b9a6039386f6b295172f388
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615743"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Solucionar problemas de exibições de custo empresarial

Nos registros empresariais, há várias configurações que podem fazer com que os usuários do registro não consigam ver os custos.  Essas configurações são gerenciadas pelo administrador de registros. Ou, se o registro não foi comprado diretamente por meio da Microsoft, as configurações são gerenciadas pelo parceiro.  Este artigo ajuda você a entender quais são as configurações e como elas afetam o registro. Essas configurações são independentes das funções de RBAC (Role-based access control) do Azure.

## <a name="enabling-access-to-costs"></a>Habilitando o acesso aos custos

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

Se você estiver recebendo uma mensagem de erro "Este ativo está indisponível" ao tentar acessar uma assinatura ou um grupo de gerenciamento, significa que você não tem a função correta para exibir esse item.  

![Captura de tela que mostra a mensagem "O ativo está indisponível".](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Solicite acesso ao administrador do grupo de gerenciamento ou da assinatura do Azure. Para obter mais informações, confira [gerenciar o acesso usando o portal do Azure e o RBAC](../role-based-access-control/role-assignments-portal.md).
