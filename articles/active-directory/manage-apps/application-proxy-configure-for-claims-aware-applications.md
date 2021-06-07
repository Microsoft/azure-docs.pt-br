---
title: Aplicativos com reconhecimento de declarações - Proxy de aplicativo do Azure AD | Microsoft Docs
description: Como publicar aplicativos ASP.NET locais que aceitam declarações do ADFS para acesso remoto seguro por seus usuários.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/08/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: b90c2d47e70a4f7595ac535d5f8ba9506087eb72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258517"
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Trabalho com aplicativos com reconhecimento de declarações no Proxy de Aplicativo
Os [aplicativos com reconhecimento de declarações](/previous-versions/windows/desktop/legacy/bb736227(v=vs.85)) executam um redirecionamento para o STS (Serviço de Token de Segurança). O STS solicita as credenciais do usuário em troca de um token e, em seguida, redireciona o usuário para o aplicativo. Há algumas maneiras de habilitar o Proxy de Aplicativo para que ele funcione com esses redirecionamentos. Use este artigo para configurar sua implantação de aplicativos com reconhecimento de declarações. 

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que o STS para o qual o aplicativo com reconhecimento de declarações seja redirecionado está disponível fora da sua rede local. Você pode disponibilizar o STS, expondo-o por meio de um proxy ou permitindo conexões externas. 

## <a name="publish-your-application"></a>Publicar seu aplicativo

1. Publique seu aplicativo seguindo as instruções descritas em [Publicar aplicativos com o Proxy de Aplicativo](application-proxy-add-on-premises-application.md).
2. Navegue até a página de aplicativo no portal e selecione **Logon único**.
3. Se você tiver escolhido **Azure Active Directory** como seu **Método de Pré-autenticação**, selecione **Logon único do Azure AD desabilitado** como seu **Método de Autenticação Interno**. Se você tiver escolhido **Passagem** como seu **método de pré-autenticação**, não precisará alterar nada.

## <a name="configure-adfs"></a>Configurar o ADFS

Você pode configurar o ADFS para aplicativos com reconhecimento de declarações de uma de duas maneiras. A primeira é por meio de domínios personalizados. A segunda é com a especificação Web Services Federation. 

### <a name="option-1-custom-domains"></a>Opção 1: Domínios personalizados

Se todas as URLs internas para seus aplicativos forem nomes de domínio totalmente qualificados (FQDNs), você poderá configurar [domínios personalizados](application-proxy-configure-custom-domain.md) para seus aplicativos. Use os domínios personalizados para criar URLs externas que são iguais às URLs internas. Quando suas URLs externas corresponderem às URLs internas, os redirecionamentos de STS funcionarão se os usuários estiverem no local ou remotos. 

### <a name="option-2-ws-federation"></a>Opção 2: especificação Web Services Federation

1. Abra o Gerenciamento de ADFS.
2. Acesse **Terceiras Partes Confiáveis**, clique com o botão direito do mouse no aplicativo que você está publicando com o Proxy de Aplicativo e escolha **Propriedades**.  

   ![Confianças em Terceiras Partes Confiáveis, clique com o botão direito do mouse no nome do aplicativo - captura de tela](./media/application-proxy-configure-for-claims-aware-applications/appproxyrelyingpartytrust.png)  

3. Na guia **Pontos de Extremidade**, em **Tipo de ponto de extremidade**, selecione **WS-Federation**.
4. Em **URL Confiável**, insira a URL fornecida no Proxy de Aplicativo em **URL Externa** e clique em **OK**.  

   ![Adicionar um ponto de extremidade - definir valor de URL Confiável - captura de tela](./media/application-proxy-configure-for-claims-aware-applications/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Próximas etapas
* [Habilitar aplicativos clientes nativos para interagir com aplicativos de proxy](application-proxy-configure-native-client-application.md)