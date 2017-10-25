---
title: "Usuários licenciados no Azure Active Directory | Microsoft Docs"
description: "Saiba como licenciar a si mesmo e seus usuários no Azure Active Directory."
services: active-directory
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jeffgilb
custom: it-pro
ms.openlocfilehash: c4509cdb003687083d0456c1957b19cf35ee056a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="quickstart-license-users-in-azure-active-directory"></a>Início rápido: Licenciar usuários no Azure Active Directory
Os Serviços do Azure AD baseados em licença funcionam por meio da ativação da assinatura do Azure AD (Azure Active Directory) em seu locatário do Azure. Depois que a assinatura estiver ativa, as funcionalidades de serviço poderão ser gerenciadas por administradores do Azure AD e usadas por usuários licenciados. Quando você comprar o Enterprise Mobility + Security, Azure AD Premium ou Azure AD Basic, seu locatário será atualizado com a assinatura, incluindo período de validade e licenças pré-pagas. As informações da assinatura, incluindo o número de licenças atribuídas ou disponíveis, estão disponíveis no portal do Azure, em **Azure Active Directory** ao abrir o bloco **Licenças**. A folha **Licenças** também é o melhor lugar para gerenciar suas atribuições de licença.

Embora a aquisição de uma assinatura seja tudo o que você precisa para configurar funcionalidades pagas, você ainda deve atribuir licenças para recursos pagos do Azure AD aos usuários. Qualquer usuário que deve ter acesso ou é gerenciado por meio de um recurso pago do Azure AD deve ter uma licença atribuída. Uma atribuição de licença é um mapeamento entre um usuário e um serviço comprado, como o Azure AD Premium, Básico ou o Enterprise Mobility + Security.

Você pode usar [atribuição de licença baseada em grupo](active-directory-licensing-whatis-azure-portal.md) para configurar as regras, como o seguinte:
* Todos os usuários em seu diretório obtêm automaticamente uma licença
* Todos com o cargo apropriado obtêm uma licença
* Você pode delegar a decisão de outros gerenciadores na organização (ao usar [grupos de autoatendimento](active-directory-accessmanagement-self-service-group-management.md))

> [!TIP]
> Para obter uma discussão detalhada sobre a atribuição de licenças para grupos, incluindo cenários avançados e cenários de licenciamento do Office 365, consulte [Atribuir licenças a usuários por associação de grupo no Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md).

## <a name="assign-licenses-to-users-and-groups"></a>Atribuir licenças a usuários e grupos
Usando uma assinatura ativa, primeiro você deve atribuir uma licença a si mesmo e atualizar seu navegador para garantir que você veja todos os recursos esperados incluídos em sua assinatura. A próxima etapa é atribuir licenças aos usuários que precisam de acesso a recursos pagos do Azure AD. É uma maneira fácil para atribuir licenças é atribuir licenças a grupos de usuários em vez de indivíduos. Ao atribuir licenças a um grupo, uma licença é atribuída a todos os membros do grupo. Se usuários forem adicionados ou removidos do grupo, a licença apropriada será automaticamente atribuída ou removida. 

> [!NOTE]
> Alguns serviços da Microsoft não estão disponíveis em todos os locais. Para que uma licença possa ser atribuída a um usuário, o administrador deve especificar a propriedade **Local de uso** para o usuário. É possível definir essa propriedade em **Usuário** &gt; **Perfil** &gt; **Configurações** no Portal do Azure. Ao usar a atribuição de licença de grupo, qualquer usuário, cujo local de uso não está especificado, herda o local do diretório.

Para atribuir uma licença, no **Azure Active Directory** &gt; **Licenças** &gt; **Todos os Produtos**, selecione um ou mais produtos e selecione **Atribuir** na barra de comandos.

![Selecione uma licença para atribuir](media/license-users-groups/select-license-to-assign.png)

Você pode usar a folha **Usuários e grupos** para escolher vários usuários ou grupos ou desabilitar os planos de serviço no produto. Use a caixa de pesquisa na parte superior para pesquisar nomes de usuário e grupo.

![Selecione um usuário ou grupo para atribuição de licença](media/license-users-groups/select-user-for-license-assignment.png)

Quando você atribuir licenças a um grupo, pode levar algum tempo até que todos os usuários herdem a licença, dependendo do tamanho do grupo. É possível verificar o status de processamento na folha **Grupo**, no bloco **Licença**.

![Status de atribuição de licença](media/license-users-groups/license-assignment-status.png)

Erros de atribuição podem ocorrer durante a atribuição de licença do Azure AD, mas são relativamente raros ao gerenciar os produtos do Azure AD e do Enterprise Mobility + Security. Os possíveis erros de atribuição estão limitados a:
- Conflito de atribuição: quando um usuário recebeu anteriormente uma licença que é incompatível com a licença atual. Nesse caso, a atribuição da nova licença exigirá a remoção da atual.
- Licenças disponíveis excedidas: quando o número de usuários em grupos atribuídos excede as licenças disponíveis, o status de atribuição do usuário reflete uma falha ao atribuir devido à falta de licenças.

### <a name="azure-ad-b2b-collaboration-licensing"></a>Licenciamento da colaboração B2B do Azure AD

A colaboração B2B permite chamar usuários convidados no seu locatário do Azure AD para fornecer acesso aos serviços do Azure AD e a qualquer recurso do Azure que você disponibilizar.  

Não há nenhum custo para convidar usuários B2B e atribuí-los a um aplicativo no Azure AD. Até 10 aplicativos por usuário convidado e 3 relatórios básicos também são gratuitos para usuários de colaboração B2B. Se o usuário convidado tiver as licenças apropriadas atribuídas no locatário do Azure AD do parceiro, ele será licenciado também na sua.

Não é obrigatório, mas se você desejar fornecer acesso a recursos pagos do Azure AD, os usuários convidados B2B deverão ser licenciados com licenças adequadas do Azure AD. Um locatário que está convidando com uma licença paga do Azure AD pode atribuir direitos de usuário de colaboração B2B a outros cinco usuários convidados adicionais para o locatário. Para obter informações e cenários, consulte [Diretrizes de licenciamento de colaboração B2B](active-directory-b2b-licensing.md).

## <a name="view-assigned-licenses"></a>Exibir licenças atribuídas

É mostrada uma exibição resumida de licenças disponíveis e atribuídas no **Azure Active Directory** &gt; **Licenças** &gt; **Todos os produtos**.

![Exibir resumo da licença](media/license-users-groups/view-license-summary.png)

Uma lista detalhada de usuários e grupos atribuídos está disponível ao selecionar um produto específico. A lista de **Usuários licenciados** mostra todos os usuários atualmente consumindo uma licença e se a licença foi atribuída diretamente ao usuário ou se ela é herdada de um grupo.

![Exibir detalhes da licença](media/license-users-groups/view-license-detail.png)

Da mesma forma, a lista de **Grupos Licenciados** mostra todos os grupos aos quais as licenças foram atribuídas. Selecione um usuário ou grupo para abrir a folha **Licenças**, que mostra todas as licenças atribuídas àquele objeto.

## <a name="remove-a-license"></a>Remover uma licença

Para remover uma licença, vá até o usuário ou grupo e abra o bloco **Licenças**. Selecione a licença e clique em **Remover**.

![Remover uma licença](media/license-users-groups/remove-license.png)

As licenças herdadas pelo usuário de um grupo não podem ser removidas diretamente. Em vez disso, remova o usuário do grupo do qual eles estão herdando a licença.


## <a name="next-steps"></a>Próximas etapas
Neste guia de início rápido, você aprendeu a atribuir licenças a usuários e grupos no diretório do Azure AD. 

Você pode usar o link a seguir para configurar as atribuições de licença de assinatura no Azure AD no portal do Azure.

> [!div class="nextstepaction"]
> [Atribuir licenças do Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Overview) 