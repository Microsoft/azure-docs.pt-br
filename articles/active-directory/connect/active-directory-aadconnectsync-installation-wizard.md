---
title: "Executar novamente o assistente de instalação do Azure AD Connect | Microsoft Docs"
description: "Explica como o assistente de instalação funciona na segunda vez que é executado."
keywords: "O assistente de instalação do Azure AD Connect permite configurar as configurações de manutenção da segunda vez que é executado"
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 42855b785c0ab334e33a622c8db912ce2438c627
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Sincronização do Azure AD Connect: executar o assistente de instalação pela segunda vez
Na primeira vez que você executa o assistente de instalação do Azure AD Connect, ele explica como configurar a instalação. Se você executar o assistente de instalação novamente, ele oferecerá opções para manutenção.

Você pode encontrar o assistente de instalação no menu Iniciar chamado **Azure Connect AD**.

![Menu Iniciar](./media/active-directory-aadconnectsync-installation-wizard/startmenu.png)

Ao iniciar o assistente de instalação, você vê uma página com as seguintes opções:

![Página com uma lista de tarefas adicionais](./media/active-directory-aadconnectsync-installation-wizard/additionaltasks.png)

Se tiver instalado o ADFS com o Azure AD Connect, você terá ainda mais opções. As opções adicionais disponíveis para ADFS estão documentadas em [Gerenciamento do ADFS](active-directory-aadconnect-federation-management.md#manage-ad-fs).

Selecione uma das tarefas e clique em **Avançar** para continuar.

> [!IMPORTANT]
> Enquanto o assistente de instalação estiver aberto, todas as operações no mecanismo de sincronização serão suspensas. Lembre-se de fechar o assistente de instalação assim que concluir as alterações de configuração.
>
>

## <a name="view-current-configuration"></a>Exibir a configuração atual
Essa opção fornece uma visão rápida das opções configuradas no momento.

![Página com uma lista de todas as opções e seus estados](./media/active-directory-aadconnectsync-installation-wizard/viewconfig.png)

Clique em **Voltar** para voltar. Se você selecionar **Sair**, o assistente de instalação será fechado.

## <a name="customize-synchronization-options"></a>Personalizar opções de sincronização
Esta opção é usada para fazer alterações na configuração de sincronização. Você vê um subconjunto das opções do caminho de instalação de configuração personalizada. Você vê essa opção mesmo que tenha usado a instalação expressa inicialmente.

* [Adicionar mais diretórios](active-directory-aadconnect-get-started-custom.md#connect-your-directories). Para remover um diretório, consulte [Excluir um Conector](active-directory-aadconnectsync-service-manager-ui-connectors.md#delete).
* [Alterar a filtragem de domínio e de unidade organizacional](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering).
* Remova a filtragem de grupo.
* [Alterar recursos opcionais](active-directory-aadconnect-get-started-custom.md#optional-features).

As outras opções de instalação inicial não podem ser alteradas e não estão disponíveis. Essas opções são:

* Altere o atributo a ser usado para userPrincipalName e sourceAnchor.
* Altere o método de ingresso para objetos de uma floresta diferente.
* Habilite a filtragem baseada em grupo.

## <a name="refresh-directory-schema"></a>Atualizar esquema de diretório
Essa opção é usada se você alterou o esquema em uma das suas florestas do AD DS locais. Por exemplo você pode ter instalado o Exchange ou atualizado para um esquema do Windows Server 2012 com objetos de dispositivo. Nesse caso, você precisa instruir o Azure AD Connect para ler o esquema novamente do AD DS e atualizar seu cache. Essa ação também regenera as Regras de Sincronização. Se você adicionar o esquema do Exchange, por exemplo, as Regras de Sincronização para o Exchange serão adicionadas à configuração.

Quando você seleciona essa opção, todos os diretórios na sua configuração são listados. Você pode manter a configuração padrão e atualizar todas as florestas ou desmarcar algumas delas.

![Página com uma lista de todos os diretórios no ambiente](./media/active-directory-aadconnectsync-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Configurar modo de preparo
Essa opção permite habilitar e desabilitar o modo de preparo no servidor. Encontre mais informações sobre o modo de preparo e como ele é usado em [Operações](active-directory-aadconnectsync-operations.md#staging-mode).

A opção mostra se o teste está habilitado ou desabilitado atualmente:   
![Opção que também está mostrando o estado atual do modo de preparo](./media/active-directory-aadconnectsync-installation-wizard/stagingmodecurrentstate.png)

Para alterar o estado, selecione essa opção e marque ou desmarque a caixa de seleção.  
![Opção que também está mostrando o estado atual do modo de preparo](./media/active-directory-aadconnectsync-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Alterar a entrada do usuário
Esta opção permite mudar da sincronização de senha para federação ou o oposto. Você não pode alterar para **não configurar**.

Para obter mais informações sobre essa opção, consulte [entrada do usuário](active-directory-aadconnect-user-signin.md#changing-the-user-sign-in-method).

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o modelo de configuração usado pela sincronização do Azure AD Connect em [Noções básicas do provisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

**Tópicos de visão geral**

* [Sincronização do Azure AD Connect: compreender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)
