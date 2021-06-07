---
title: Adicionar usuários com licenças diretas ao licenciamento de grupo-Azure AD | Microsoft Docs
description: Como migrar de licenças de usuário individuais para licenciamento baseado em grupo usando o Azure Active Directory
services: active-directory
keywords: Licenciamento do AD do Azure
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: adc80cf579ce3086abd4171b065f859acd0b9294
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96546498"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>Como migrar usuários com licenças individuais para grupos para licenciamento

Você pode ter licenças existentes implantadas para usuários nas organizações por meio de atribuição direta; ou seja, usando scripts do PowerShell ou outras ferramentas para atribuir licenças de usuário individuais. Antes de começar a usar o licenciamento baseado em grupo para gerenciar licenças em sua organização, você pode usar esse plano de migração para substituir diretamente as soluções existentes com o licenciamento baseado em grupo.

A coisa mais importante para ter em mente é que você deve evitar uma situação onde a migração para o licenciamento baseado em grupo resultará em usuários temporariamente perdendo suas licenças atribuídas. Deve-se evitar qualquer processo que pode resultar na remoção de licenças, a fim de remover o risco de os usuários perderem o acesso aos serviços e a seus dados.

## <a name="recommended-migration-process"></a>Processo de migração recomendado

1. Você tem a automação existente (por exemplo, PowerShell) gerenciando a atribuição e a remoção de licenças para usuários. Deixe-o em execução como está.

1. Crie um novo grupo de licenciamento (ou decida quais grupos existentes serão usados) e verifique se todos os usuários necessários foram adicionados como membros.

1. Atribua as licenças necessárias para esses grupos; sua meta deve ser refletir o estado de licenciamento mesmo que sua automação existente (por exemplo, PowerShell) esteja sendo aplicada aos usuários.

1. Verifique se as licenças foram aplicadas a todos os usuários nesses grupos. Esta aplicação pode ser feita verificando o estado de processamento em cada grupo e conferindo os logs de auditoria.

   - Você pode identificar usuários individuais ao examinar os detalhes de suas licenças. Você verá que eles têm as licenças atribuídas "diretamente" e "herdadas" de grupos.

   - Você pode executar um script do PowerShell para [verificar como as licenças são atribuídas a usuários](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

   - Quando a mesma licença do produto é atribuída para o usuário diretamente e por meio de um grupo, apenas uma licença é consumida pelo usuário. Portanto, não há nenhuma licença adicional é necessária para realizar uma migração.

1. Verifique se nenhuma atribuição de licença falhou ao verificar cada grupo de usuários em estado de erro. Para saber mais, veja [Identificar e resolver problemas de licença para um grupo](licensing-groups-resolve-problems.md).

Considere remover as atribuições diretas originais. É recomendável que você o faça gradualmente e monitore o resultado em um subconjunto de usuários primeiro. Se você pode deixar as atribuições diretas originais nos usuários, mas quando os usuários deixam seus grupos licenciados, eles retêm as licenças atribuídas diretamente, o que pode não ser o que você deseja.

## <a name="an-example"></a>Um exemplo

Uma organização tem 1.000 usuários. Todos os usuários exigem licenças do Office 365 Enterprise E3. Atualmente, a organização tem um script do PowerShell em execução local, adicionando e removendo licenças de usuários à medida que elas entram e saem. No entanto, a organização deseja substituir o script pelo licenciamento baseado em grupo para que as licenças possam ser gerenciadas automaticamente pelo Azure AD.

Veja como deve se parecer o processo de migração:

1. Usando o portal do Azure, atribua a licença do Office 365 E3 ao grupo **todos os usuários** no Azure AD.

1. Confirme se a atribuição de licença foi concluída para todos os usuários. Vá para a página Visão geral do grupo, selecione **licenças** e verifique o status de processamento na parte superior da folha **licenças** .

   - Procure "As alterações mais recentes de licença foram aplicadas a todos os usuários" para confirmar que o processamento foi concluído.

   - Procure uma notificação na parte superior sobre quaisquer usuários para os quais as licenças talvez não tenham sido atribuídas com êxito. Nós não temos mais licenças para alguns usuários? Alguns usuários têm planos de licença conflitantes que os impedem de herdar licenças de grupo?

1. Verifique alguns usuários para ver se eles têm as licenças diretas ou de grupo aplicadas. Acesse a página de perfil de um usuário, selecione **licenças** e examine o estado das licenças.

   - Este é o estado do usuário esperado durante a migração:

      ![o estado de usuário esperado durante a migração](./media/licensing-groups-migrate-users/expected-user-state.png)

     Isso confirma que o usuário tem licenças diretas e herdadas. Vemos que o Office 365 E3 está atribuído.

   - Selecione cada licença para ver quais serviços estão habilitados. Para verificar se as licenças diretas e de grupo permitem exatamente os mesmos serviços para o usuário, selecione **atribuições**.

1. Depois de confirmar que as licenças direta e de grupo são equivalentes, você poderá começar a remover as licenças diretas dos usuários. É possível testar isso removendo-as para usuários individuais no portal e, em seguida, executar scripts de automação para removê-las em massa. Este é um exemplo do mesmo usuário com as licenças diretas removidas por meio do portal. Observe que o estado da licença permanece inalterado, mas não vemos mais as atribuições diretas.

   ![confirmar se as licenças diretas foram removidas](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre outros cenários de gerenciamento de licenças de Grupo:

- [O que é o licenciamento baseado em grupo no Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
- [Atribuição de licenças a um grupo no Azure Active Directory](licensing-groups-assign.md)
- [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](licensing-groups-resolve-problems.md)
- [Como migrar usuários entre licenças de produto usando o licenciamento baseado em grupo no Microsoft Azure Active Directory](licensing-groups-change-licenses.md)
- [Cenários adicionais de licenciamento baseado em grupo do Azure Active Directory](licensing-group-advanced.md)
- [Exemplos do PowerShell para licenciamento baseado em grupo no Azure Active Directory](licensing-ps-examples.md)
