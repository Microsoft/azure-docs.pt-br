---
title: Validar regras para associação de grupo dinâmico (visualização)-Azure AD | Microsoft Docs
description: Como testar Membros em relação a uma regra de associação para grupos dinâmicos no Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1048284e8e7a492bf0810a16e29409546ed414b
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547552"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>Validar uma regra de associação de grupo dinâmico (versão prévia) no Azure Active Directory

O Azure Active Directory (Azure AD) agora fornece os meios para validar regras de grupo dinâmicos (em visualização pública). Na guia **validar regras** , você pode validar sua regra dinâmica em relação aos membros do grupo de exemplo para confirmar se a regra está funcionando conforme o esperado. Ao criar ou atualizar regras de grupo dinâmicos, os administradores desejam saber se um usuário ou um dispositivo será membro do grupo. Isso ajuda a avaliar se o usuário ou o dispositivo atende aos critérios de regra e ajuda na solução de problemas quando a associação não é esperada.

## <a name="step-by-step-walk-through"></a>Passo a passo detalhado

Para começar, acesse **Azure Active Directory**  >  **grupos**. Selecione um grupo dinâmico existente ou crie um novo grupo dinâmico e clique em regras de associação dinâmica. Em seguida, você pode ver a guia **validar regras** .

![Localize a guia validar regras e comece com uma regra existente](./media/groups-dynamic-rule-validation/validate-tab.png)

Na guia **validar regras** , você pode selecionar os usuários para validar suas associações. 20 usuários ou dispositivos podem ser selecionados ao mesmo tempo.

![Adicionar usuários para validar a regra existente em relação a](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

Depois de escolher os usuários ou dispositivos no seletor, e **selecionar**, a validação será iniciada automaticamente e os resultados da validação serão exibidos.

![Exibir os resultados da validação de regra](./media/groups-dynamic-rule-validation/validate-tab-results.png)

Os resultados informam se um usuário é um membro do grupo ou não. Se a regra não for válida ou houver um problema de rede, o resultado será exibido como **desconhecido**. No caso de **desconhecido**, a mensagem de erro detalhada descreverá o problema e as ações necessárias.

![Exibir os detalhes dos resultados da validação da regra](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

Você pode modificar a regra e a validação de associações será disparada. Para ver por que o usuário não é um membro do grupo, clique em "Exibir detalhes" e os detalhes da verificação mostrarão o resultado de cada expressão que compõe a regra. Clique em **OK** para sair.

## <a name="next-steps"></a>Próximas etapas

- [Regras de associação dinâmica para grupos](groups-dynamic-membership.md)
