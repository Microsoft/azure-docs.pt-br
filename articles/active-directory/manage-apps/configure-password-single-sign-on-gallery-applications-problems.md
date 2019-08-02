---
title: Problemas ao configurar o logon único com senha para o aplicativo da galeria do Azure AD | Microsoft Docs
description: Compreender os problemas comuns que as pessoas enfrentam ao configurar o Logon Único com Senha para aplicativos já listados na Galeria do Aplicativo Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc75346b1093cc41a44edad1376c5f10dfec2409
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381135"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Problema ao configurar o logon único com senha para um aplicativo na Galeria do Azure AD

Este artigo ajuda você a compreender os problemas comuns que as pessoas enfrentam ao configurar o **Logon Único com Senha** com um aplicativo na Galeria do Azure AD.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>As credenciais são preenchidas, mas a extensão não as envia

Este problema geralmente acontece se o fornecedor do aplicativo alterou a página de entrada recentemente para adicionar um campo, alterar um identificador que usamos para detectar os campos de nome de usuário e senha ou modificar como a experiência de entrada do usuário funciona no seu aplicativo. Felizmente, em muitos casos, a Microsoft pode trabalhar com fornecedores de aplicativos para resolver rapidamente esses problemas.

Ainda que a Microsoft possua tecnologias para detectar automaticamente quando integrações interrompem, talvez não seja possível localizar esses problemas imediatamente ou os problemas levem algum tempo para serem corrigidos. Quando uma dessas integrações não funcionar corretamente, abra um caso de suporte para que seja corrigido o mais rapidamente possível.

**Se você estiver em contato com o fornecedor desse aplicativo,** coloque-o em contato conosco de modo que a Microsoft possa trabalhar em conjunto para integrar nativamente o aplicativo com o Azure Active Directory. É possível enviar o fornecedor para o [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/howto-app-gallery-listing.md) para iniciá-los.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>As credenciais estão preenchidas e enviadas, mas a página indica que as credenciais estão incorretas

Para resolver este problema, primeiro teste estas coisas:

- Peça ao usuário que primeiro tente **entrar diretamente no site do aplicativo** com as credenciais armazenadas para eles.

  * Se sign-in funcionar, faça com que o usuário clique no botão **Atualizar Credenciais** no **Bloco do Aplicativo** na seção **Aplicativos** do [Painel de Acesso a Aplicativo](https://myapps.microsoft.com/) para atualizá-los com o nome de usuário e senha mais recentes conhecidos.

  * Se você ou outro administrador atribuiu as credenciais para esse usuário, localize a atribuição de aplicativo do usuário ou grupo, navegando até a guia **Usuários e Grupos** do aplicativo, selecionando a atribuição e clicando no botão **Atualizar Credenciais**.

- Se o usuário atribuiu suas próprias credenciais, solicite ao usuário que **certifique-se de que a senha dele não tenha expirado no aplicativo** e, esse for o caso, **atualize a senha expirada** entrando diretamente no aplicativo.

  * Após a atualização da senha no aplicativo, solicite que o usuário clique no botão **Atualizar Credenciais** no **Bloco do Aplicativo** na seção **Aplicativos** do [Painel de Acesso a Aplicativo](https://myapps.microsoft.com/) para atualizá-los com o nome de usuário e senha mais recentes conhecidos.

  * Se você ou outro administrador atribuiu as credenciais para esse usuário, localize a atribuição de aplicativo do usuário ou grupo, navegando até a guia **Usuários e Grupos** do aplicativo, selecionando a atribuição e clicando no botão **Atualizar Credenciais**.

- Peça ao usuário para que atualize a extensão do navegador do painel de acesso, seguindo as etapas abaixo na seção [Como instalar a extensão do Navegador do Painel de Acesso](#how-to-install-the-access-panel-browser-extension).

- Certifique-se de que a extensão do navegador do painel de acesso esteja em execução e habilitada no navegador do usuário.

- Certifique-se de que os usuários não estão tentando entrar no aplicativo pelo painel de acesso enquanto estão no **modo incógnito, inPrivate ou Privado**. Não há suporte para a extensão do painel de acesso nesses modos.

Caso as sugestões anteriores não funcionem, pode ser que tenha ocorrido uma alteração no aplicativo que interrompeu temporariamente a integração do aplicativo com o Azure AD. Por exemplo, isso pode ocorrer quando o fornecedor do aplicativo introduz um script em sua página que se comporta de forma diferente para entrada manual ou automatizada, o que faz com que a integração automatizada, como a nossa, seja interrompida. Felizmente, em muitos casos, a Microsoft pode trabalhar com fornecedores de aplicativos para resolver rapidamente esses problemas.

Ainda que a Microsoft possua tecnologias para detectar automaticamente quando integrações interrompem, talvez não seja possível localizar esses problemas imediatamente ou os problemas levem algum tempo para serem corrigidos. Quando uma integração não funciona corretamente, você pode abrir um caso de suporte para arrumar o problema o mais rápido possível. 

Além disso, **se você estiver em contato com o fornecedor desse aplicativo,** **coloque-o em contato conosco** de modo que possamos trabalhar em conjunto para integrar nativamente o aplicativo com o Azure Active Directory. É possível enviar o fornecedor para o [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/howto-app-gallery-listing.md) para iniciá-los.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>A extensão funciona no Chrome e no Firefox, mas não no Internet Explorer

Existem duas principais causas para esse problema:

- Dependendo das configurações de segurança habilitadas no Internet Explorer, se o site não faz parte de uma **Zona Confiável**, às vezes nosso script é bloqueado para executar o aplicativo.

  *  Para resolver isso, instrua o usuário a **adicionar sites da Web do aplicativo** para o **Sites confiáveis** lista dentro de suas **as configurações de segurança do Internet Explorer**. Você pode encaminhar seus usuários para o artigo [Como adicionar um site à minha lista de sites confiáveis](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) para obter instruções detalhadas.

- Em raras circunstâncias, a validação de segurança do Internet Explorer pode, às vezess, fazer com que a página seja carregada mais lentamente do que a execução do nosso script.

  * Infelizmente, essa situação pode variar dependendo da versão do navegador, da velocidade do computador ou do site visitado. Nesse caso, sugerimos que você contate o suporte para que possamos corrigir a integração para esse aplicativo específico.

Além disso, **se você estiver em contato com o fornecedor desse aplicativo,** **coloque-o em contato conosco** de modo que possamos trabalhar em conjunto para integrar nativamente o aplicativo com o Azure Active Directory. É possível enviar o fornecedor para o [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/howto-app-gallery-listing.md) para iniciá-los.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Verifique se a página de logon do aplicativo foi alterada recentemente ou exige um campo adicional

Se a página de logon do aplicativo foi drasticamente alterada, às vezes isso causa a interrupção de nossas integrações. Um exemplo disso é quando um fornecedor de aplicativo adiciona um campo de entrada, um captcha ou uma autenticação multifator às suas experiências. Felizmente, em muitos casos, a Microsoft pode trabalhar com fornecedores de aplicativos para resolver rapidamente esses problemas.

Ainda que a Microsoft possua tecnologias para detectar automaticamente quando integrações interrompem, talvez não seja possível localizar esses problemas imediatamente ou os problemas levem algum tempo para serem corrigidos. Quando uma integração não funciona corretamente, você pode abrir um caso de suporte para arrumar o problema o mais rápido possível. 

Além disso, **se você estiver em contato com o fornecedor desse aplicativo,** **coloque-o em contato conosco** de modo que possamos trabalhar em conjunto para integrar nativamente o aplicativo com o Azure Active Directory. É possível enviar o fornecedor para o [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/howto-app-gallery-listing.md) para iniciá-los.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão do Navegador do Painel de Acesso

Para instalar a extensão do Navegador do Painel de Acesso, siga as etapas a seguir:

1.  Abra o [Painel de Acesso](https://myapps.microsoft.com) em um dos navegadores compatíveis e entre como um **usuário** no Azure AD.

2.  Clique no **aplicativo de SSO com senha** no Painel de Acesso.

3.  No prompt solicitando a instalação do software, selecione **Instalar Agora**.

4.  Com base no seu navegador, você é direcionado para o link de download. **Adicione** a extensão ao seu navegador.

5.  Se o navegador solicitar, selecione como **Habilitar** ou **Permitir** a extensão.

6.  Quando estiver instalado, **reinicie** a sessão do navegador.

7.  Entrar no Painel de Acesso e verificar se é possível **iniciar** os aplicativos de SSO de senha

Também é possível baixar a extensão para Chrome e Firefox diretamente pelos links abaixo:

-   [Extensão do Painel de Acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do Painel de Acesso do Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>Próximas etapas
[Fornecer logon único para seus aplicativos com Proxy de Aplicativo](application-proxy-configure-single-sign-on-with-kcd.md)

