---
title: Problemas ao entrar no aplicativo de logon único federado não Galeria | Microsoft Docs
description: Diretrizes para os problemas específicos que você pode enfrentar ao entrar em um aplicativo configurado para o logon único federado baseado em SAML com Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: d26f52db394877cff13bf4b0b476a9603c1ddba8
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381387"
---
# <a name="problems-signing-in-to-a-non-gallery-application-configured-for-federated-single-sign-on"></a>Problemas ao entrar em um aplicativo inexistente na galeria configurado para logon único federado

Para solucionar os problemas de entrada abaixo, recomendamos que você siga estas sugestões para obter um diagnóstico melhor e automatizar as etapas de resolução:

- Instale a [extensão do navegador seguro meus aplicativos](access-panel-extension-problem-installing.md) para ajudar a Azure Active Directory (Azure AD) a fornecer melhor diagnóstico e resoluções ao usar a experiência de teste no portal do Azure.
- Reproduza o erro usando a experiência de teste na página de configuração do aplicativo no portal do Azure. Saiba mais em [depurar aplicativos de logon único baseados em SAML](../develop/howto-v1-debug-saml-sso-issues.md)

## <a name="application-not-found-in-directory"></a>Aplicativo não encontrado no diretório

*Erro AADSTS70001: O aplicativo com `https://contoso.com` identificador não foi encontrado no diretório*.

**Possível causa**

O atributo que o Emissor envia do aplicativo para o Azure AD na solicitação SAML não corresponde ao valor do Identificador configurado no aplicativo Azure AD.

**Resolução**

Verifique se o `Issuer` atributo na solicitação SAML corresponde ao valor do identificador configurado no Azure AD. Se você usar a [experiência de teste](../develop/howto-v1-debug-saml-sso-issues.md) no portal do Azure com a extensão de navegador seguro meus aplicativos, não precisará seguir estas etapas manualmente.

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6. Selecione o aplicativo para o qual você deseja configurar o logon único.

7. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8. Depois que o aplicativo for carregado, abra **Configuração Básica de SAML**. Verifique se o valor na caixa de texto identificador corresponde ao valor do valor do identificador exibido no erro.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>O endereço de resposta não corresponde aos endereços de resposta configurados para o aplicativo. 

*Erro AADSTS50011: O endereço `https://contoso.com` de resposta não corresponde aos endereços de resposta configurados para o aplicativo* 

**Possível causa** 

O valor de AssertionConsumerServiceURL na solicitação SAML não corresponde ao valor da URL de resposta ou ao padrão configurado no Azure AD. O valor de AssertionConsumerServiceURL na solicitação SAML é a URL que você vê no erro. 

**Resolução** 

Verifique se o `Issuer` atributo na solicitação SAML corresponde ao valor do identificador configurado no Azure AD. Se você usar a [experiência de teste](../develop/howto-v1-debug-saml-sso-issues.md) no portal do Azure com a extensão de navegador seguro meus aplicativos, não precisará seguir estas etapas manualmente.
 
1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.** 

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal. 

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**. 

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory. 

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos. 

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**
  
6. Selecione o aplicativo para o qual deseja configurar o logon único

7. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8. Depois que o aplicativo for carregado, abra **Configuração Básica de SAML**. Verifique ou atualize o valor na caixa de texto URL de resposta para `AssertionConsumerServiceURL` corresponder ao valor na solicitação SAML.    
    
Depois de atualizar o valor da URL de resposta no Azure AD, e ele corresponder ao valor enviado pelo aplicativo na solicitação SAML, você deverá ser capaz de entrar no aplicativo.

## <a name="user-not-assigned-a-role"></a>Usuário não atribuído a uma função

*Erro AADSTS50105: O usuário `brian\@contoso.com` conectado não está atribuído a uma função para o aplicativo*

**Possível causa**

O usuário não teve acesso concedido para o aplicativo no Azure AD.

**Resolução**

Para atribuir um ou mais usuários diretamente a um aplicativo, siga as etapas abaixo. Se você usar a [experiência de teste](../develop/howto-v1-debug-saml-sso-issues.md) no portal do Azure com a extensão de navegador seguro meus aplicativos, não precisará seguir estas etapas manualmente.

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6. Na lista, selecione o aplicativo ao qual deseja atribuir um usuário.

7. Após o carregamento do aplicativo, clique em **Usuários e Grupos** no menu de navegação esquerdo do aplicativo.

8. Clique no botão **Adicionar** na parte superior da lista **Usuários e Grupos** para abrir o painel **Adicionar Atribuição**.

9. Clique no seletor **Usuários e grupos** do painel **Adicionar Atribuição**.

10. Digite o **nome completo** ou o **endereço de email** do usuário que você deseja atribuir na caixa de pesquisa **Pesquisar por nome ou endereço de email**.

11. Passe o mouse sobre o **usuário** na lista para mostrar uma **caixa de seleção**. Clique na caixa de seleção ao lado do logotipo ou da foto de perfil do usuário para adicioná-lo à lista **Selecionado**.

12. **Opcional:** Caso queira **adicionar mais de um usuário**, digite outro **nome completo** ou **endereço de email** na caixa de pesquisa **Pesquisar por nome ou endereço de email** e clique na caixa de seleção para adicionar esse usuário à lista **Selecionado**.

13. Ao concluir a seleção dos usuários, clique no botão **Selecionar** para adicioná-los à lista de usuários e grupos a serem atribuídos ao aplicativo.

14. **Opcional:** clique no seletor **Selecionar Função** no painel **Adicionar Atribuição** para selecionar uma função que será atribuída aos usuários selecionados.

15. Clique no botão **Atribuir** para atribuir o aplicativo aos usuários selecionados.

Após um breve período, os usuários que você selecionou poderão iniciar esses aplicativos usando os métodos descritos na seção de descrição da solução.

## <a name="not-a-valid-saml-request"></a>Não é uma solicitação SAML válida

*Erro AADSTS75005: a solicitação não é uma mensagem de protocolo Saml2 válida.*

**Possível causa**

O Azure AD não oferece suporte para a solicitação SAML enviada pelo aplicativo para logon único. Alguns problemas comuns são:

-   Campos obrigatórios ausentes na solicitação SAML

-   Método codificado de solicitação SAML

**Resolução**

1.  Capturar solicitação SAML. siga o tutorial em [como depurar o logon único baseado em SAML em aplicativos no Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) para saber mais sobre como capturar a solicitação SAML.

2.  Contate o fornecedor do aplicativo e compartilhe:

    -   Solicitação SAML

    -   [Requisitos de protocolo SAML de logon único do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

O fornecedor do aplicativo deve validar que eles dão suporte à implementação SAML do Azure AD para logon único.

## <a name="misconfigured-application"></a>Aplicativo configurado incorretamente

*Erro AADSTS650056: Aplicativo configurado incorretamente. Isso pode ser devido a um dos seguintes: O cliente não listou nenhuma permissão para ' AAD Graph ' nas permissões solicitadas no registro do aplicativo do cliente. Ou, o administrador não consentiu no locatário. Ou então, verifique o identificador do aplicativo na solicitação para garantir que ele corresponda ao identificador do aplicativo cliente configurado. Entre em contato com seu administrador para corrigir a configuração ou o consentimento em nome do locatário.* .

**Possível causa**

O `Issuer` atributo enviado do aplicativo para o Azure ad na solicitação SAML não corresponde ao valor do identificador configurado para o aplicativo no Azure AD.

**Resolução**

Verifique se o `Issuer` atributo na solicitação SAML corresponde ao valor do identificador configurado no Azure AD. Se você usar a [experiência de teste](../develop/howto-v1-debug-saml-sso-issues.md) no portal do Azure com a extensão de navegador seguro meus aplicativos, não precisará seguir estas etapas manualmente:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global** ou **Coadministrador**.

1.  Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.

1.  Digite **"Azure Active Directory"** na caixa de pesquisa de filtro e selecione o item de **Azure Active Directory** .

1.  Selecione **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

1.  Marque **Todos os aplicativos** para exibir uma lista com todos os seus aplicativos.

    Se você não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos**.

1.  Selecione o aplicativo para o qual você deseja configurar o logon único.

1.  Depois que o aplicativo for carregado, abra **Configuração Básica de SAML**. Verifique se o valor na caixa de texto identificador corresponde ao valor do valor do identificador exibido no erro.

## <a name="certificate-or-key-not-configured"></a>Chave ou certificado não configurado

Erro AADSTS50003: nenhuma chave de assinatura configurada.

**Possível causa**

O objeto de aplicativo está corrompido e o Azure AD não reconhece o certificado configurado para o aplicativo.

**Resolução**

Para excluir e criar um novo certificado, siga as etapas abaixo:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6. Selecione o aplicativo para o qual você deseja configurar o logon único.

7. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8. clique em **Criar novo certificado** na seção **Certificado de Autenticação SAML**.

9. Selecione a data de validade. Em seguida, clique em **Salvar.**

10. Marque **Tornar o novo certificado ativo** para substituir o certificado ative. Em seguida, clique em **Salvar** na parte superior do painel e aceite para ativar o certificado de substituição.

11. Na seção **Certificado de Autenticação SAML**, clique em **remover** para remover o certificado **Não Usado**.

## <a name="saml-request-not-present-in-the-request"></a>A solicitação SAML não está presente na solicitação

*Erro AADSTS750054: SAMLRequest ou SAMLResponse deve estar presente como parâmetro de cadeia de caracteres na solicitação HTTP para a Associação de redirecionamento SAML.*

**Possível causa**

O Azure AD não pôde identificar a solicitação SAML dentro dos parâmetros de URL na solicitação HTTP. Isso pode acontecer se o aplicativo não estiver usando a associação de redirecionamento HTTP ao enviar a solicitação SAML ao Azure AD.

**Resolução**

O aplicativo precisa enviar a solicitação SAML codificada para o cabeçalho Location usando a associação de redirecionamento HTTP. Para obter mais informações sobre como implementá-la, leia a seção sobre Associação de redirecionamento HTTP no [Documento de especificação de protocolo SAML](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>O Azure AD está enviando o token para um ponto de extremidade incorreto

**Possível causa**

Durante o logon único, se a solicitação de entrada não contiver uma URL de resposta explícita (URL de serviço do consumidor de asserção), o Azure AD selecionará qualquer uma das URLs de confiança configuradas para esse aplicativo. Mesmo que o aplicativo tenha uma URL de resposta explícita configurada, o usuário pode ser redirecionado https://127.0.0.1:444. 

Quando o aplicativo foi adicionado como um aplicativo inexistente na galeria, o Azure Active Directory criou essa URL de resposta como um valor padrão. Esse comportamento mudou e o Azure Active Directory não adiciona mais essa URL por padrão. 

**Resolução**

Exclua as URLs de resposta não utilizadas configuradas para o aplicativo.

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global** ou **Coadministrador**.

2.  Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **"Azure Active Directory"** na caixa de pesquisa de filtro e selecione o item de **Azure Active Directory** .

4.  Selecione **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5.  Marque **Todos os aplicativos** para exibir uma lista com todos os seus aplicativos.

    Se você não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos**.

6.  Selecione o aplicativo para o qual você deseja configurar o logon único.

7.  Depois que o aplicativo for carregado, abra **Configuração Básica de SAML**. Na **URL de resposta (URL do serviço de consumidor de asserção)** , exclua as URLs de resposta não usadas ou padrão criadas pelo sistema. Por exemplo, `https://127.0.0.1:444/applications/default.aspx`.



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problema ao personalizar as declarações SAML enviadas para um aplicativo

Para saber como personalizar as declarações de atributo SAML enviadas para seu aplicativo, consulte [Mapeamento de declarações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas
[Requisitos de protocolo SAML de logon único do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)
