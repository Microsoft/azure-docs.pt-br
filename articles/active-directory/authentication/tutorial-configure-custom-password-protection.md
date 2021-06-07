---
title: Configurar listas personalizadas de proteção de senha do Azure Active Directory
description: Neste tutorial, você aprenderá a configurar listas personalizadas de proteção de senhas proibidas do Azure Active Directory para restringir palavras comuns no ambiente.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89abdcf4c76c92c2ed967594527d323b2cd8be32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96741177"
---
# <a name="tutorial-configure-custom-banned-passwords-for-azure-active-directory-password-protection"></a>Tutorial: Configurar senhas proibidas personalizadas para proteção de senha do Azure Active Directory

Os usuários geralmente criam senhas que usam palavras locais comuns, como uma escola, uma equipe esportiva ou uma pessoa famosa. Essas senhas são fáceis de serem adivinhadas e fracas contra os ataques baseados em dicionário. Para impor senhas fortes na sua organização, a lista personalizada de senhas proibidas do Azure AD (Azure Active Directory) permite que você adicione cadeias de caracteres específicas para avaliação e bloqueio. Uma solicitação de alteração de senha falhará se houver uma correspondência na lista personalizada de senhas proibidas.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Habilitar senhas proibidas personalizadas
> * Adicionar entradas à lista personalizada de senhas proibidas
> * Testar alterações de senha com uma senha proibida

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes recursos e privilégios:

* Um locatário do Azure AD em funcionamento com pelo menos uma licença de avaliação ou Premium P1 do Azure AD habilitada.
    * Se necessário, [crie um gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Uma conta com privilégios de *administrador global*.
* Um usuário que não seja administrador com uma senha que você conheça, como *testuser*. Você testará um evento de alteração de senha usando essa conta neste tutorial.
    * Se você precisar criar um usuário, confira [Início rápido: Adicionar novos usuários ao Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).
    * Para testar a operação de alteração de senha usando uma senha proibida, o locatário do Azure AD precisará ser [configurado para a redefinição de senha por autoatendimento](tutorial-enable-sspr.md).

## <a name="what-are-banned-password-lists"></a>O que são listas de senhas proibidas?

O Azure AD inclui uma lista global de senhas proibidas. O conteúdo da lista global de senhas proibidas não se baseia em nenhuma fonte de dados externa. Em vez disso, a lista global de senhas proibidas se baseia nos resultados contínuos da telemetria e da análise de segurança do Azure AD. Quando um usuário ou um administrador tenta alterar ou redefinir as respectivas credenciais, a senha desejada é verificada em relação à lista de senhas proibidas. A solicitação de alteração de senha falhará se houver uma correspondência na lista global de senhas proibidas. Não é possível editar essa lista global padrão de senhas proibidas.

Para oferecer flexibilidade em quais senhas são permitidas, você também pode definir uma lista personalizada de senhas proibidas. A lista personalizada de senhas proibidas funciona junto com a lista global de senhas proibidas para impor senhas fortes na sua organização. Os termos específicos da organização podem ser adicionados à lista personalizada de senhas proibidas, como os seguintes exemplos:

* Nomes de marcas
* Nomes de produtos
* Localizações, como a sede da empresa
* Termos internos específicos da empresa
* Abreviações que tenham um significado específico para a empresa

Quando um usuário tenta redefinir uma senha para algo que está na lista global ou personalizada de senhas proibidas, ele vê uma das seguintes mensagens de erro:

* *Sua senha contém uma palavra, uma frase ou um padrão que pode ser facilmente adivinhado. Tente novamente com outra senha.*
* *Você não pode usar essa senha, porque ela contém palavras ou caracteres que foram bloqueados pelo administrador. Tente novamente com outra senha.*

A lista personalizada de senhas proibidas é limitada a um máximo de 1.000 termos. Ela não foi projetada para bloquear listas grandes de senhas. Para maximizar os benefícios da lista personalizada de senhas proibidas, examine os [conceitos da lista personalizada de senhas proibidas](concept-password-ban-bad.md#custom-banned-password-list) e a [visão geral do algoritmo de avaliação de senha](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="configure-custom-banned-passwords"></a>Configurar senhas proibidas personalizadas

Vamos habilitar a lista personalizada de senhas proibidas e adicionar algumas entradas. Você pode adicionar mais entradas à lista personalizada de senhas proibidas a qualquer momento.

Para habilitar a lista personalizada de senhas proibidas e adicionar entradas a ela, conclua as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta com permissões de *administrador global*.
1. Procure e selecione **Azure Active Directory**. Em seguida, escolha **Segurança** no menu no lado esquerdo.
1. No cabeçalho do menu **Gerenciar**, selecione **Métodos de autenticação** e **Proteção de senha**.
1. Defina a opção **Impor lista personalizada** como *Sim*.
1. Adicione cadeias de caracteres à **Lista personalizada de senhas proibidas**, uma cadeia de caracteres por linha. As seguintes considerações e limitações se aplicam à lista personalizada de senhas proibidas:

    * A lista personalizada de senhas proibidas pode conter até 1.000 termos.
    * A lista de senhas proibidas personalizada não diferencia maiúsculas de minúsculas.
    * A lista personalizada de senhas proibidas considera a substituição de caracteres comuns, como "o" e "0" ou "a" e "\@".
    * O tamanho mínimo da cadeia de caracteres é de quatro caracteres; o máximo é de 16 caracteres.

    Especifique as próprias senhas personalizadas a serem proibidas, conforme mostrado no exemplo a seguir

    [ ![Modifique a lista de senhas proibidas personalizada em Métodos de Autenticação no portal do Azure](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png) ](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords.png#lightbox)

1. Mantenha a opção **Habilitar proteção de senha no Windows Server Active Directory** como *Não*.
1. Para habilitar as senhas proibidas personalizadas e as entradas, selecione **Salvar**.

É possível que demore várias horas até que as atualizações da lista de senhas proibidas sejam aplicadas.

Em um ambiente híbrido, você também pode [implantar a proteção de senha do Azure AD em um ambiente local](howto-password-ban-bad-on-premises-deploy.md). As mesmas listas global e personalizada de senhas proibidas são usadas para solicitações de alteração de senha local e na nuvem.

## <a name="test-custom-banned-password-list"></a>Testar a lista personalizada de senhas proibidas

Para ver a lista personalizada de senhas proibidas em ação, tente alterar a senha para uma variação de uma que você adicionou na seção anterior. Quando o Azure AD tenta processar a alteração de senha, é feita a correspondência da senha em relação a uma entrada na lista personalizada de senhas proibidas. Um erro é então exibido para o usuário.

> [!NOTE]
> Antes que o usuário possa redefinir a senha no portal baseado na Web, o locatário do Azure AD precisa ser [configurado para a redefinição de senha por autoatendimento](tutorial-enable-sspr.md). Se necessário, o usuário poderá [se registrar na SSPR em https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).

1. Acesse a página **Meus Aplicativos** em [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. No canto superior direito, selecione o seu nome e, em seguida, escolha **Perfil** no menu suspenso.

    ![Selecionar perfil](media/tutorial-configure-custom-password-protection/myapps-profile.png)

1. Na página **Perfil**, selecione **Alterar senha**.
1. Na página **Alterar a senha**, insira a senha existente (antiga). Insira e confirme uma nova senha na lista personalizada de senhas proibidas que você definiu na seção anterior e, em seguida, selecione **Enviar**.
1. É retornada uma mensagem de erro que informa que a senha foi bloqueada pelo administrador, conforme mostrado no seguinte exemplo:

    ![Mensagem de erro exibida quando você tenta usar uma senha que faz parte da lista personalizada de senhas proibidas](media/tutorial-configure-custom-password-protection/password-change-error.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não desejar mais usar a lista personalizada de senhas proibidas configurada como parte deste tutorial, conclua as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Procure e selecione **Azure Active Directory**. Em seguida, escolha **Segurança** no menu no lado esquerdo.
1. No cabeçalho do menu **Gerenciar**, selecione **Métodos de autenticação** e **Proteção de senha**.
1. Defina a opção **Impor lista personalizada** como *Não*.
1. Para atualizar a configuração personalizada de senhas proibidas, selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você habilitou e configurou listas personalizadas de proteção de senha para o Azure AD. Você aprendeu a:

> [!div class="checklist"]
> * Habilitar senhas proibidas personalizadas
> * Adicionar entradas à lista personalizada de senhas proibidas
> * Testar alterações de senha com uma senha proibida

> [!div class="nextstepaction"]
> [Habilitar a Autenticação Multifator do Azure AD baseada em risco](./tutorial-enable-azure-mfa.md)