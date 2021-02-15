---
title: Preparar as Migrações para Azure para trabalhar com uma ferramenta ISV/Movere
description: Este artigo descreve como preparar as Migrações para Azure para trabalhar com uma ferramenta ISV ou um Movere e, em seguida, como começar a usar a ferramenta.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/10/2020
ms.openlocfilehash: 1716db0476169e12822b3f47f7199bf6e2c4ee92
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753766"
---
# <a name="prepare-to-work-with-an-isv-tool-or-movere"></a>Preparar-se para trabalhar com uma ferramenta ISV/Movere

Se você adicionou uma [ferramenta ISV](migrate-services-overview.md#isv-integration) ou Movere para um projeto de Migrações para Azure, há algumas etapas a serem seguidas antes de vincular a ferramenta e enviar dados para as Migrações para Azure. 

## <a name="check-azure-ad-permissions"></a>Verifique as permissões do Azure AD

Sua conta de usuário do Azure precisa dessas permissões:

- Permissão para registrar um aplicativo do Azure Active Directory (Azure AD) com seu locatário do Azure
- Permissão para alocar uma função ao aplicativo do Azure AD no nível de assinatura


### <a name="set-permissions-to-register-an-azure-ad-app"></a>Definir permissões para registrar um aplicativo do Azure AD

1. Verifique a função da sua conta no Azure AD.
2. Se você tiver a função de usuário, selecione **Configurações de usuário** à esquerda e verifique se os usuários podem registrar aplicativos. Se estiver definido como **Sim**, qualquer usuário no locatário do Azure AD poderá registrar um aplicativo. Se estiver definido como **Não**, somente usuários administradores podem registrar aplicativos.   
3. Se você não tiver permissões, um usuário administrador poderá fornecer a sua conta de usuário com a função de [Administrador de aplicativos](../active-directory/roles/permissions-reference.md#application-administrator), para que você possa registrar o aplicativo.
4. Depois que a ferramenta estiver vinculada às Migrações para Azure, o administrador poderá remover a função da sua conta.

### <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Definir permissões para atribuir uma função a um aplicativo do Azure AD
 
Em sua assinatura do Azure, sua conta precisa do acesso ao **Microsoft.Authorization/*/Write** para atribuir uma função a um aplicativo do Azure AD. 

1. No portal do Azure, abra **Assinaturas**.
2. Selecione a assinatura relevante. Se você não o vir, selecione o **filtro de assinaturas globais**. 
3. Selecionar **Minhas permissões**. Em seguida, selecione **Clique aqui para exibir o acesso completo detalhes para essa assinatura**.
4. Em **Atribuições de função** > **Exibição**, verifique as permissões. Se sua conta não tiver permissões, peça ao administrador da assinatura que o adicione à função [Administrador de acesso do usuário](../role-based-access-control/built-in-roles.md#user-access-administrator) ou à função [Proprietário](../role-based-access-control/built-in-roles.md#owner).

## <a name="allow-access-to-urls"></a>Permitir acesso às URLs

Para ferramentas ISV e Assistente de Migração de banco de dados do Azure, permita o acesso às URLs de nuvem pública resumidas na tabela. Se estiver usando um proxy baseado em URL para se conectar à Internet, certifique-se de que o proxy resolva todos os registros CNAME recebidos ao pesquisar as URLs. 

**URL** | **Detalhes**
--- | ---
*. portal.azure.com  | Navegue até o Portal do Azure. 
*.windows.net<br/> *.msftauth.net<br/> *.msauth.net <br/> *.microsoft.com<br/> *.live.com   | Entre em sua assinatura do Azure. 
*.microsoftonline.com<br/> *.microsoftonline p.com | Crie aplicativos do Azure AD (Active Directory) para que o dispositivo se comunique com as Migrações para Azure. 
management.azure.com | Faça chamadas Azure Resource Manager para o projeto de migrações para Azure.
*.servicebus.windows.net | Comunicação entre o dispositivo e o EventHub para enviar as mensagens.


## <a name="start-using-the-tool"></a>Comece a usar a ferramenta

1. Se você ainda não tiver uma licença ou uma avaliação gratuita para a ferramenta, na entrada da ferramenta nas Migrações para Azure, em **Registrar**, selecione **Saiba mais**.
2. Na ferramenta, siga as instruções para vincular a partir da ferramenta ao projeto de Migrações para Azure e para enviar dados para as Migrações para Azure.

## <a name="next-steps"></a>Próximas etapas

Siga as instruções do seu ISV ou Movere para enviar dados para as Migrações para Azure.

   
