---
title: "Como migrar seus usuários individuais licenciados para um grupo no Azure Active Directory | Microsoft Docs"
description: "Como alternar licenças de usuário individuais para licenciamento baseado em grupo usando o Azure Active Directory"
services: active-directory
keywords: Licenciamento do AD do Azure
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6b77dd4e9a6d361a05382397e89b575896fdad84
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-add-licensed-users-to-a-group-for-licensing-in-azure-active-directory"></a>Como adicionar usuários licenciados a um grupo para o licenciamento no Azure Active Directory

É possível implantar licenças existentes em usuários nas organizações por meio da “atribuição direta”; ou seja, usando scripts do PowerShell ou outras ferramentas para atribuir licenças de usuários individuais. Se você quiser começar a usar o licenciamento baseado em grupo para gerenciar as licenças da sua organização, precisará de um plano de migração para substituir de forma direta as soluções existentes com licenciamento baseado em grupo.

A coisa mais importante para ter em mente é que você deve evitar uma situação onde a migração para o licenciamento baseado em grupo resultará em usuários temporariamente perdendo suas licenças atribuídas. Deve-se evitar qualquer processo que pode resultar na remoção de licenças, a fim de remover o risco de os usuários perderem o acesso aos serviços e a seus dados.

## <a name="recommended-migration-process"></a>Processo de migração recomendado

1. Você tem a automação existente (por exemplo, PowerShell) gerenciando a atribuição e a remoção de licenças para usuários. Deixe-o em execução como está.

2. Crie um novo grupo de licenciamento (ou decida quais grupos existentes serão usados) e verifique se todos os usuários necessários foram adicionados como membros.

3. Atribua as licenças necessárias para esses grupos; sua meta deve ser refletir o estado de licenciamento mesmo que sua automação existente (por exemplo, PowerShell) esteja sendo aplicada aos usuários.

4. Verifique se as licenças foram aplicadas a todos os usuários nesses grupos. Isso pode ser feito verificando o estado de processamento em cada grupo e verificando os Logs de Auditoria.

  - Você pode identificar usuários individuais ao examinar os detalhes de suas licenças. Você verá que eles têm as licenças atribuídas "diretamente" e "herdadas" de grupos.

  - Você pode executar um script do PowerShell para [verificar como as licenças são atribuídas a usuários](active-directory-licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

  - Quando a mesma licença do produto é atribuída para o usuário diretamente e por meio de um grupo, apenas uma licença é consumida pelo usuário. Portanto, não há nenhuma licença adicional é necessária para realizar uma migração.

5. Verifique se nenhuma atribuição de licença falhou ao verificar cada grupo de usuários em estado de erro. Para saber mais, veja [Identificar e resolver problemas de licença para um grupo](active-directory-licensing-group-problem-resolution-azure-portal.md).

6. Considere remover as atribuições diretas originais; talvez você deseje fazer isso gradualmente, em “ondas”, para monitorar o resultado em um subconjunto de usuários primeiro.

  Você poderia deixar as atribuições diretas originais em usuários, mas quando os usuários deixarem seus grupos licenciados, eles ainda manterão a licença original, o que provavelmente não é o que você quer.

## <a name="an-example"></a>Um exemplo

Temos uma organização com 1.000 usuários. Todos os usuários exigem licenças EMS (Enterprise Mobility + Security). 200 usuários estão no departamento de finanças e exigem licenças do Office 365 Enterprise E3. Temos um script do PowerShell em execução no local, adicionando e removendo licenças de usuários à medida que eles vêm e vão. Queremos substituir o script com o licenciamento baseado em grupo para que licenças sejam gerenciadas automaticamente pelo Azure AD.

Veja como deve se parecer o processo de migração:

1. Usando o portal do Azure, atribua as licenças EMS ao grupo **Todos os usuários** no Azure AD. Atribua a licença E3 ao grupo **departamento financeiro** que contém todos os usuários exigidos.

2. Para cada grupo, confirme que a atribuição de licença foi concluída para todos os usuários. Vá até a folha de cada grupo, selecione **Licenças** e verifique o status de processamento na parte superior da folha **Licenças**.

  - Procure "As alterações mais recentes de licença foram aplicadas a todos os usuários" para confirmar que o processamento foi concluído.

  - Procure uma notificação na parte superior sobre quaisquer usuários para os quais as licenças talvez não tenham sido atribuídas com êxito. Nós não temos mais licenças para alguns usuários? Alguns usuários têm SKUs com licenças conflitantes que os impedem de herdar as licenças de grupo?

3. Verifique alguns usuários para ver se eles têm as licenças diretas ou de grupo aplicadas. Vá até a folha de um usuário, selecione **Licenças** e examine o estado das licenças.

  - Este é o estado do usuário esperado durante a migração:

      ![estado do usuário esperado](media/active-directory-licensing-group-migration-azure-portal/expected-user-state.png)

  Isso confirma que o usuário tem licenças diretas e herdadas. Podemos ver que **EMS** e **E3** estão atribuídas.

  - Selecione cada licença para mostrar os detalhes sobre os serviços habilitados. Isso pode ser usado para verificar se as licenças diretas e de grupo permitem exatamente os mesmos planos de serviço para o usuário.

      ![planos do serviço de aplicativo](media/active-directory-licensing-group-migration-azure-portal/check-service-plans.png)

4. Depois de confirmar que as licenças direta e de grupo são equivalentes, você poderá começar a remover as licenças diretas dos usuários. É possível testar isso removendo-as para usuários individuais no portal e, em seguida, executar scripts de automação para removê-las em massa. Este é um exemplo do mesmo usuário com as licenças diretas removidas por meio do portal. Observe que o estado da licença permanece inalterado, mas não vemos mais as atribuições diretas.

  ![licenças diretas removidas](media/active-directory-licensing-group-migration-azure-portal/direct-licenses-removed.png)


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre outros cenários de gerenciamento de licença por meio de grupos, leia

* [Atribuição de licenças a um grupo no Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [O que é o licenciamento baseado em grupo no Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Cenários adicionais de licenciamento baseado em grupo do Azure Active Directory](active-directory-licensing-group-advanced.md)
