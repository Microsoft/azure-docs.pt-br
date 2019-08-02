---
title: Como configurar o logon único federado para um aplicativo inexistente na galeria | Microsoft Docs
description: Como configurar o logon único federado para um aplicativo inexistente na galeria personalizado que você deseja integrar com o Azure AD
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7de7384b93237e240352651eda6e16383bcb143
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190194"
---
# <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Como configurar o logon único federado para um aplicativo inexistente na galeria

Para configurar o logon único para um aplicativo que não seja de galeria *sem escrever código*, você precisa ter uma assinatura ou o Azure AD Premium e o aplicativo devem ter suporte para SAML 2.0. Para obter mais informações sobre as versões do Azure AD, visite [Preços do Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="overview-of-steps-required"></a>Visão geral das etapas necessárias
Abaixo está uma visão geral de alto nível das etapas necessárias para configurar logon único federado com SAML 2.0 para um aplicativo não de galeria (por exemplo, personalizado).

-   Configurar os valores de metadados do aplicativo no Azure AD (URL de Logon, Identificador, URL de Resposta)

-   [Selecionar o Identificador de Usuário e adicionar os atributos de usuário a serem enviados ao aplicativo](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Recuperar certificado e metadados Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   Configurar os valores de metadados do Azure AD no aplicativo (URL de Logon, Emissor, URL de Logoff e certificado)

-   Atribuir usuários ao aplicativo

## <a name="configuring-single-sign-on-to-non-gallery-applications"></a>Configurar logon único para aplicativos não existentes na galeria

Para configurar o logon único para um aplicativo que não está na galeria do Azure AD, siga as etapas abaixo:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique no botão **Adicionar** no canto superior direito do painel **Aplicativos Empresariais**.

6. clique em **Aplicativo inexistente na galeria** na seção **Adicionar seu próprio aplicativo**

7. Digite o nome do aplicativo na caixa de texto **Nome**.

8. Clique no botão **Adicionar** para adicionar o aplicativo.

9. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

10. Selecione **Logon com base em SAML** na lista suspensa **Modo**.

11. Insira os valores necessários em **Domínio e URLs.** É possível obter esses valores do fornecedor do aplicativo.

    1. Para configurar o aplicativo como SSO iniciado por IdP, insira a URL de Resposta e o Identificador.

    2. **Opcional:** Para configurar o aplicativo como SSO iniciado por SP, a URL de Logon é um valor obrigatório.

12. Nos **Atributos de usuário**, selecione o identificador exclusivo para os usuários na lista suspensa **Identificador de usuário**.

13. **Opcional:** clique em **Exibir e editar todos os outros atributos de usuário** para editar os atributos a serem enviados ao aplicativo no token SAML quando os usuário entrarem.

    Para adicionar um atributo:

    1. clique em **Adicionar atributo**. Insira o **Nome** e selecione o **Valor** da lista suspensa.

    2. Clique em **Salvar.** Você verá o novo atributo na tabela.

14. clique em **Configurar &lt;nome do aplicativo&gt;**  para acessar a documentação sobre como configurar o logon único no aplicativo. Além disso, você tem URLs do Azure AD e o certificado necessários para o aplicativo.

15. [Atribuir usuários ao aplicativo.](#assign-users-to-the-application)

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecionar o Identificador de Usuário e adicionar os atributos de usuário a serem enviados ao aplicativo

Para selecionar o Identificador de Usuário ou adicionar os atributos de usuário, siga as etapas abaixo:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6. Selecione o aplicativo para o qual você precisa configurar o logon único.

7. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8. Na seção **Atributos de usuário**, selecione o identificador exclusivo para os usuários na lista suspensa **Identificador de usuário**. A opção selecionada precisa corresponder ao valor esperado no aplicativo para autenticar o usuário.

   >[!NOTE]
   >O Azure AD seleciona o formato para o atributo NameID (Identificador de Usuário) com base no valor selecionado ou no formato solicitado pelo aplicativo no AuthRequest do SAML. Para obter mais informações, consulte o artigo [Protocolo SAML de Logon Único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) na seção NameIDPolicy.
   >
   >

9. Para adicionar atributos de usuário, clique em **Exibir e editar todos os outros atributos de usuário** para editar os atributos a serem enviados ao aplicativo no token SAML quando os usuários entrarem.

   Para adicionar um atributo:

   1. clique em **Adicionar atributo**. Insira o **Nome** e selecione o **Valor** da lista suspensa.

   2. Clique em **Salvar.** Você verá o novo atributo na tabela.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Baixar o certificado ou metadados do Azure AD

Para baixar o certificado ou metadados do aplicativo Azure AD, siga as etapas abaixo:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6. Selecione o aplicativo para o qual você precisa configurar o logon único.

7. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8. Vá para a seção **Certificado de Autenticação SAML** e, em seguida, clique no valor da coluna **Download**. Dependendo do aplicativo que exigir a configuração de logon único, você verá a opção para baixar o Certificado ou o XML de Metadados.

O Microsoft Azure Active Directory também fornece uma URL para obter os metadados. Siga esse padrão para obter a URL de metadados específicos ao aplicativo: `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

## <a name="assign-users-to-the-application"></a>Atribuir usuários ao aplicativo

Para atribuir um ou mais usuários diretamente a um aplicativo, siga as etapas abaixo:

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

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Personalizando as declarações SAML enviadas para um aplicativo

Para saber como personalizar as declarações de atributo SAML enviadas para seu aplicativo, consulte [Mapeamento de declarações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas
[Fornecer logon único para seus aplicativos com Proxy de Aplicativo](application-proxy-configure-single-sign-on-with-kcd.md)
