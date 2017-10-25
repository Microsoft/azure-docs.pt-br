---
title: "Problemas ao entrar em um aplicativo na galeria configurado para logon único federado | Microsoft Docs"
description: "Diretrizes para os erros específicos ao entrar em um aplicativo configurado para o logon único federado baseado em SAML com Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 0fc5a8eb3d033d60bf6082d61bf1698924ab91c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>Problemas ao entrar em um aplicativo na galeria configurado para logon único federado

Para solucionar o problema é necessário verificar a configuração do aplicativo no Azure AD, conforme a seguir:

-   Você seguiu todas as etapas de configuração do aplicativo na galeria do Azure AD.

-   O Identificador e a URL de Resposta configurados no AAD correspondem aos valores esperados no aplicativo

-   Você atribuiu os usuários para o aplicativo

## <a name="application-not-found-in-directory"></a>Aplicativo não encontrado no diretório

*Erro AADSTS70001: aplicativo com o identificador ‘https://contoso.com’ não encontrado no diretório*.

**Possível causa**

O atributo que o Emissor envia do aplicativo para o Azure AD na solicitação SAML não corresponde ao valor do Identificador configurado no aplicativo Azure AD.

**Resolução**

Certifique-se de que o atributo Emissor na solicitação SAML corresponde ao valor do Identificador configurado no Azure AD:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

  * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6.  Selecione o aplicativo para o qual deseja configurar o logon único

7.  Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8.  Acesse a seção **Domínio e URLs**. Verifique se o valor na caixa de texto Identificador corresponde ao valor para o valor do identificador exibido no erro.

Após atualizar o valor do Identificador no Azure AD e correspondê-lo com o valor enviado pelo aplicativo na solicitação SAML, deverá ser possível entrar no aplicativo.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>O endereço de resposta não corresponde aos endereços de resposta configurados para o aplicativo.

*Erro AADSTS50011: O endereço de resposta 'https://contoso.com' não corresponde aos endereços de resposta configurados para o aplicativo*

**Possível causa**

O valor de AssertionConsumerServiceURL na solicitação SAML não corresponde ao valor da URL de resposta ou ao padrão configurado no Azure AD. O valor de AssertionConsumerServiceURL na solicitação SAML é a URL que você vê no erro.

**Resolução**

Verifique se o valor de AssertionConsumerServiceURL na solicitação SAML corresponde ao valor da URL de resposta configurado no Azure AD.

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

  * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6.  Selecione o aplicativo para o qual deseja configurar o logon único

7.  Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8.  Acesse a seção **Domínio e URLs**. Verifique ou atualize o valor na caixa de texto URL de Resposta para corresponder ao valor AssertionConsumerServiceURL na solicitação SAML.  
    * Se você não vir a caixa de texto URL de Resposta, selecione a caixa de seleção **Mostrar configurações de URL avançadas**.

Após atualizar o valor da URL de Resposta no Azure AD e correspondê-lo com o valor enviado pelo aplicativo na solicitação SAML, deverá ser possível entrar no aplicativo.

## <a name="user-not-assigned-a-role"></a>Usuário não atribuído a uma função

*Erro AADSTS50105: o usuário conectado 'brian@contoso.com' não está atribuído a uma função para o aplicativo*.

**Possível causa**

O usuário não teve acesso concedido para o aplicativo no Azure AD.

**Resolução**

Para atribuir um ou mais usuários diretamente a um aplicativo, siga as etapas abaixo:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

  * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6.  Na lista, selecione o aplicativo ao qual deseja atribuir um usuário.

7.  Após o carregamento do aplicativo, clique em **Usuários e Grupos** no menu de navegação esquerdo do aplicativo.

8.  Clique no botão **Adicionar** na parte superior da lista **Usuários e Grupos** para abrir a folha **Adicionar Atribuição**.

9.  Clique no seletor **Usuários e grupos** da folha **Adicionar Atribuição**.

10. Digite o **nome completo** ou o **endereço de email** do usuário que você deseja atribuir na caixa de pesquisa **Pesquisar por nome ou endereço de email**.

11. Passe o mouse sobre o **usuário** na lista para mostrar uma **caixa de seleção**. Clique na caixa de seleção ao lado do logotipo ou da foto de perfil do usuário para adicioná-lo à lista **Selecionado**.

12. **Opcional:** caso queira **adicionar mais de um usuário**, digite outro **nome completo** ou **endereço de email** na caixa de pesquisa **Pesquisar por nome ou endereço de email** e clique na caixa de seleção para adicionar esse usuário à lista **Selecionado**.

13. Ao concluir a seleção dos usuários, clique no botão **Selecionar** para adicioná-los à lista de usuários e grupos a serem atribuídos ao aplicativo.

14. **Opcional:** clique no seletor **Selecionar Função** na folha **Adicionar Atribuição** para selecionar uma função que será atribuída aos usuários selecionados.

15. Clique no botão **Atribuir** para atribuir o aplicativo aos usuários selecionados.

Após um breve período, os usuários que você selecionou poderão iniciar esses aplicativos usando os métodos descritos na seção de descrição da solução.

## <a name="not-a-valid-saml-request"></a>Não é uma solicitação SAML válida

*Erro AADSTS75005: a solicitação não é uma mensagem de protocolo Saml2 válida.*

**Possível causa**

O Azure AD não oferece suporte para a solicitação SAML enviada pelo aplicativo para logon único. Alguns problemas comuns são:

-   Campos obrigatórios ausentes na solicitação SAML

-   Método codificado de solicitação SAML

**Resolução**

1.  Capturar solicitação SAML. siga o tutorial em [Como depurar o logon único baseado em SAML em aplicativos no Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) para saber mais sobre como capturar a solicitação SAML.

2.  Contate o fornecedor do aplicativo e compartilhe:

   -   Solicitação SAML

   -   [Requisitos de protocolo SAML de logon único do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

Eles devem validar que oferecem suporte à implementação do SAML do Azure AD para Logon Único.

## <a name="no-resource-in-requiredresourceaccess-list"></a>Nenhum recurso na lista requiredResourceAccess

*Erro AADSTS65005: o aplicativo cliente solicitou acesso ao recurso '00000002-0000-0000-c000-000000000000'. Essa solicitação falhou porque o cliente não especificou esse recurso na sua lista requiredResourceAccess*.

**Possível causa**

O objeto de aplicativo está corrompido.

**Resolução: opção 1**

Para resolver o problema, adicione o valor do identificador exclusivo na configuração do Azure AD. Para adicionar um valor de identificador, siga as etapas abaixo:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

  * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6.  Selecione o aplicativo para o qual você precisa configurar o logon único.

7.  Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação à esquerda do aplicativo

8.  Na seção **Domínio e URL**, marque **Mostrar configurações de URL avançadas**.

9.  Na caixa de texto **Identificador**, digite um identificador exclusivo para o aplicativo.

10. **Salve** a configuração.


**Resolução: opção 2**

Se a opção 1 acima não funcionou, tente remover o aplicativo do diretório. Em seguida, adicione e reconfigurar o aplicativo, siga as etapas abaixo:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

  * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6.  Selecione o aplicativo para o qual deseja configurar o logon único

7.  Clique em **Excluir** na parte superior esquerda da folha **Visão Geral** do aplicativo.

8.  Atualize o Azure AD e Adicione o aplicativo da galeria do Azure AD. Em seguida, Configure o aplicativo

<span id="_Hlk477190176" class="anchor"></span>Após reconfigurar o aplicativo, deverá ser possível entrar no aplicativo.

## <a name="certificate-or-key-not-configured"></a>Chave ou certificado não configurado

*Erro AADSTS50003: nenhuma chave de assinatura configurada.*

**Possível causa**

O objeto de aplicativo está corrompido e o Azure AD não reconhece o certificado configurado para o aplicativo.

**Resolução**

Para excluir e criar um novo certificado, siga as etapas abaixo:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

 * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6.  Selecione o aplicativo para o qual deseja configurar o logon único

7.  Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8.  clique em **Criar novo certificado** na seção **Certificado de Autenticação SAML**.

9.  Selecione a data de validade. Em seguida, clique em **Salvar.**

10. Marque **Tornar o novo certificado ativo** para substituir o certificado ative. Em seguida, clique em **Salvar** na parte superior da folha e aceite para ativar o certificado de substituição.

11. Na seção **Certificado de Autenticação SAML**, clique em **remover** para remover o certificado **Não Usado**.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problema ao personalizar as declarações SAML enviadas para um aplicativo

Para saber como personalizar as declarações de atributo SAML enviadas para seu aplicativo, consulte [Mapeamento de declarações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas
[Como depurar o logon único baseado em SAML em aplicativos no do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)
