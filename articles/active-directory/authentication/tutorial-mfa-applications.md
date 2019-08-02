---
title: Habilitar um piloto da Autenticação Multifator do Azure
description: Neste tutorial, você habilitará a Autenticação Multifator do Azure AD para um grupo piloto de usuários
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: a229eef3d49964e71becf066d67f60b18da6fc18
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113234"
---
# <a name="tutorial-complete-an-azure-multi-factor-authentication-pilot-roll-out"></a>Tutorial: Concluir uma distribuição de um piloto da Autenticação Multifator do Azure

Neste tutorial, você aprende a configurar uma política de Acesso Condicional, habilitando a Autenticação Multifator do Azure (MFA do Azure) ao fazer logon no portal do Azure. A política é implantada e testada em um grupo específico de usuários piloto. A implantação do Azure MFA usando o Acesso Condicional fornece uma flexibilidade significativa para organizações e administradores em comparação com o método tradicional imposto.

> [!div class="checklist"]
> * Habilitar a Autenticação Multifator do Microsoft Azure
> * Testar a Autenticação Multifator do Microsoft Azure

## <a name="prerequisites"></a>Pré-requisitos

* Um locatário de trabalho do Azure AD com pelo menos uma licença de avaliação habilitada.
* Uma conta com privilégios de Administrador Global.
* Um usuário de teste não administrador com uma senha que você sabe para testar. Se você precisar criar um usuário, confira o artigo [Início Rápido: Adicionar novos usuários ao Azure Active Directory](../add-users-azure-active-directory.md).
* Um grupo piloto para testar que o usuário não-administrador é membro, ou se você precisar criar um grupo, confira o artigo [Criar um grupo e adicionar membros ao Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-azure-multi-factor-authentication"></a>Habilitar a Autenticação Multifator do Microsoft Azure

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta de Administrador Global.
1. Navegue até **Azure Active Directory**, **Acesso Condicional**
1. Selecione **Nova política**
1. Nomeie a política **Piloto de MFA**
1. Em **usuários e grupos**, selecione o botão de opção **Selecionar usuários e grupos**
    * Selecione seu grupo piloto criado como parte da seção de pré-requisitos deste artigo
    * Clique em **Concluído**
1. Em **Aplicativos de nuvem**, selecione o botão de opção **Selecionar aplicativos**
    * O aplicativo de nuvem para o portal do Azure é o **Gerenciamento do Microsoft Azure**
    * Clique em **Selecionar**
    * Clique em **Concluído**
1. Ignore a seção **Condições**
1. Em **Concessão**, verifique se o botão de opção **Conceder acesso** está selecionado
    * Marque a caixa para **Exigir autenticação multifator**
    * Clique em **Selecionar**
1. Ignore a seção **Sessão**
1. Defina a alternância **Habilitar política** para **Ativado**
1. Clique em **Criar**

## <a name="test-azure-multi-factor-authentication"></a>Testar a Autenticação Multifator do Microsoft Azure

Para provar que a política de Acesso Condicional funciona, você testa o logon em um recurso que não deve exigir o MFA e, em seguida, no portal do Azure que requer o MFA.

1. Abra uma nova janela do navegador no modo InPrivate ou incógnito e navegue até [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com).
   * Faça logon com o usuário de teste criado como parte da seção de pré-requisitos deste artigo e observe que ele não deve solicitar para concluir o MFA.
   * Feche a janela do navegador.
2. Abra uma nova janela do navegador no modo InPrivate ou anônimo e navegue até [https://portal.azure.com](https://portal.azure.com).
   * Faça logon com o usuário de teste criado como parte da seção de pré-requisitos deste artigo e observe que agora você deve ser solicitado a se registrar e usar a Autenticação Multifator do Azure.
   * Feche a janela do navegador.

## <a name="clean-up-resources"></a>Limpar recursos

Se você decidir que não deseja mais usar a funcionalidade configurada como parte deste tutorial, faça a alteração a seguir.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Navegue até **Azure Active Directory**, **Acesso Condicional**.
1. Selecione a política de Acesso Condicional que você criou.
1. Clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você habilitou a Autenticação Multifator do Azure. Continue com o próximo tutorial para ver como a Proteção de Identidade do Azure pode ser integrada na redefinição de senha de autoatendimento e as experiências da Autenticação Multifator.

> [!div class="nextstepaction"]
> [Avaliar o risco ao entrar](tutorial-risk-based-sspr-mfa.md)
