---
title: "Introdução ao Azure AD Connect usando configurações expressas | Microsoft Docs"
description: "Saiba como baixar, instalar e executar o assistente de instalação do Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: curtand
ms.assetid: b6ce45fd-554d-4f4d-95d1-47996d561c9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 8a08f6e441a856a06bf7870747ca20af45a0364e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-azure-ad-connect-using-express-settings"></a>Introdução ao Azure AD Connect usando configurações expressas
As **configurações expressas** do Azure AD Connect são usadas quando você tem uma topologia de floresta única e a [sincronização de senha](active-directory-aadconnectsync-implement-password-synchronization.md) para autenticação. **configurações expressas** são a opção padrão e são usadas para o cenário de implantação mais comum. São apenas alguns cliques curtos para estender seu diretório local para a nuvem.

Antes de iniciar a instalação do Azure AD Connect, [baixe o Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) e conclua as etapas de pré-requisito em [Azure AD Connect: hardware e pré-requisitos](active-directory-aadconnect-prerequisites.md).

Se configurações expressas não corresponderem à sua topologia, confira a [documentação relacionada](#related-documentation) para outros cenários.

## <a name="express-installation-of-azure-ad-connect"></a>Instalação expressa do Azure AD Connect
Você pode ver essas etapas em ação na seção de [vídeos](#videos) .

1. Entre como um administrador local no servidor no qual você deseja instalar o Azure AD Connect. Você deve fazer isso no servidor que deseja ser o servidor de sincronização.
2. Navegue até **AzureADConnect.msi**e clique duas vezes nessa opção.
3. Na tela de boas-vindas, marque a caixa de concordar com os termos da licença e clique em **Continuar**.  
4. Na tela de configurações expressas, clique em **Usar configurações expressas**.  
   ![Bem-vindo ao Azure AD Connect](./media/active-directory-aadconnect-get-started-express/express.png)
5. Na tela Conectar AD do Azure, insira o nome de usuário e senha de um administrador global do seu AD do Azure. Clique em **Avançar**.  
   ![Conecte-se ao AD do Azure](./media/active-directory-aadconnect-get-started-express/connectaad.png) Se você encontrar um erro e tiver problemas de conectividade, confira [Solucionar problemas de conectividade](active-directory-aadconnect-troubleshoot-connectivity.md).
6. Na tela Conectar AD DS, digite o nome de usuário e senha para uma conta de administrador corporativa. Você pode inserir a parte do domínio no formato NetBios ou FQDN, ou seja, FABRIKAM\administrador ou fabrikam.com\administrador. Clique em **Avançar**.  
   ![Conectar-se ao AD DS](./media/active-directory-aadconnect-get-started-express/connectad.png)
7. A página [**Configuração de entrada do Azure AD**](active-directory-aadconnect-user-signin.md#azure-ad-sign-in-configuration) será mostrada somente se você não tiver concluído [verificar seus domínios](../active-directory-add-domain.md) nos [pré-requisitos](active-directory-aadconnect-prerequisites.md).
   ![Domínios não verificados](./media/active-directory-aadconnect-get-started-express/unverifieddomain.png)  
   Se essa página for mostrada, examine todos os domínios marcados como **Não Adicionado** e **Não Verificado**. Confira se os domínios que você usa foram verificados no Azure AD. Clique no símbolo de Atualização quando tiver verificado os domínios.
8. Na tela Pronto para configurar, clique em **Instalar**.
   * Opcionalmente, na página Pronto para configurar, você pode desmarcar a caixa de seleção **Iniciar o processo de sincronização assim que a configuração for concluída** . Você deverá desmarcar essa caixa de seleção se desejar fazer configurações adicionais, como a [filtragem](active-directory-aadconnectsync-configure-filtering.md). Se você desmarcar essa opção, o assistente irá configurar a sincronização, mas deixará o agendador desabilitado. Ele não será executado até você habilitá-lo manualmente [executando o assistente de instalação de novo](active-directory-aadconnectsync-installation-wizard.md).
   * Se tiver o Exchange no Active Directory local, você também terá uma opção para habilitar a [**Implantação Híbrida do Exchange**](https://technet.microsoft.com/library/jj200581.aspx). Habilite essa opção se você planeja ter caixas de correio do Exchange na nuvem e no local ao mesmo tempo.
     ![Pronto para configurar o Azure AD Connect](./media/active-directory-aadconnect-get-started-express/readytoconfigure.png)
9. Quando a instalação for concluída, clique em **Sair**.
10. Após a conclusão da instalação, saia e entre novamente antes de usar o Gerenciador de Serviços de Sincronização ou o Editor de Regra de Sincronização.

## <a name="videos"></a>vídeos
Para ver um vídeo sobre como usar a instalação expressa, consulte:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Express-Settings/player]
> 
> 

## <a name="next-steps"></a>Próximas etapas
Agora que você tem o Azure AD Connect instalado, é possível [verificar a instalação e atribuir licenças](active-directory-aadconnect-whats-next.md).

Saiba mais sobre estes recursos, que foram habilitados com a instalação: [Atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md), [Impedir exclusões acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) e [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-sync.md).

Saiba mais sobre estes tópicos comuns: [Agendador e como disparar a sincronização](active-directory-aadconnectsync-feature-scheduler.md).

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

## <a name="related-documentation"></a>documentação relacionada
| Tópico |
| --- | --- |
| Visão geral do Azure AD Connect |
| Instalar usando configurações personalizadas |
| Atualização do DirSync |
| Contas usadas para instalação |

