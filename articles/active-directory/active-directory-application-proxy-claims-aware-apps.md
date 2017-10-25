---
title: "Aplicativos com reconhecimento de declarações - Proxy de aplicativo do Azure AD | Microsoft Docs"
description: "Como publicar aplicativos ASP.NET locais que aceitam declarações do ADFS para acesso remoto seguro por seus usuários."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: 91e6211b-fe6a-42c6-bdb3-1fff0312db15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: kgremban
ms.openlocfilehash: 5784222608b01509fc4ff84b1a8792cbcfea89e6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Trabalho com aplicativos com reconhecimento de declarações no Proxy de Aplicativo
Os [aplicativos com reconhecimento de declarações](https://msdn.microsoft.com/library/windows/desktop/bb736227.aspx) executam um redirecionamento para o STS (Serviço de Token de Segurança). O STS solicita as credenciais do usuário em troca de um token e, em seguida, redireciona o usuário para o aplicativo. Há algumas maneiras de habilitar o Proxy de Aplicativo para que ele funcione com esses redirecionamentos. Use este artigo para configurar sua implantação de aplicativos com reconhecimento de declarações. 

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que o STS para o qual o aplicativo com reconhecimento de declarações seja redirecionado está disponível fora da sua rede local. Você pode disponibilizar o STS, expondo-o por meio de um proxy ou permitindo conexões externas. 

## <a name="publish-your-application"></a>Publicar seu aplicativo

1. Publique seu aplicativo seguindo as instruções descritas em [Publicar aplicativos com o Proxy de Aplicativo](application-proxy-publish-azure-portal.md).
2. Navegue até a página de aplicativo no portal e selecione **Logon único**.
3. Se você tiver escolhido **Azure Active Directory** como seu **Método de Pré-autenticação**, selecione **Logon único do Azure AD desabilitado** como seu **Método de Autenticação Interno**. Se você tiver escolhido **Passagem** como seu **método de pré-autenticação**, não precisará alterar nada.

## <a name="configure-adfs"></a>Configurar o ADFS

Você pode configurar o ADFS para aplicativos com reconhecimento de declarações de uma de duas maneiras. A primeira é por meio de domínios personalizados. A segunda é com a especificação Web Services Federation. 

### <a name="option-1-custom-domains"></a>Opção 1: Domínios personalizados

Se todas as URLs internas para seus aplicativos forem nomes de domínio totalmente qualificados (FQDNs), você poderá configurar [domínios personalizados](active-directory-application-proxy-custom-domains.md) para seus aplicativos. Use os domínios personalizados para criar URLs externas que são iguais às URLs internas. Quando suas URLs externas corresponderem às URLs internas, os redirecionamentos de STS funcionarão se os usuários estiverem no local ou remotos. 

### <a name="option-2-ws-federation"></a>Opção 2: especificação Web Services Federation

1. Abra o Gerenciamento de ADFS.
2. Acesse **Terceiras Partes Confiáveis**, clique com o botão direito do mouse no aplicativo que você está publicando com o Proxy de Aplicativo e escolha **Propriedades**.  

   ![Confianças em Terceiras Partes Confiáveis, clique com o botão direito do mouse no nome do aplicativo - captura de tela](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  

3. Na guia **Pontos de Extremidade**, em **Tipo de ponto de extremidade**, selecione **WS-Federation**.
4. Em **URL Confiável**, insira a URL fornecida no Proxy de Aplicativo em **URL Externa** e clique em **OK**.  

   ![Adicionar um ponto de extremidade - definir valor de URL Confiável - captura de tela](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Próximas etapas
* [Habilitar logon único](application-proxy-sso-overview.md) para aplicativos sem reconhecimento de declarações
* [Habilitar aplicativos clientes nativos para interagir com aplicativos de proxy](active-directory-application-proxy-native-client.md)


