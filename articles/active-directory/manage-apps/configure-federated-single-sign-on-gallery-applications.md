---
title: Como configurar o logon único federado para um aplicativo na Galeria do Azure AD | Microsoft Docs
description: Como configurar o logon único federado para um aplicativo na Galeria do Azure AD existente e usar tutoriais para começar rapidamente
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
ROBOTS: NOINDEX
ms.openlocfilehash: bb5d2c693047dd0aa53430ba531dfd246cc77be9
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422540"
---
# <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar o logon único federado para um aplicativo na Galeria do Azure AD

Todos os aplicativos na galeria do Azure AD habilitados com o recurso de logon único Corporativo possuem um tutorial passo a passo disponível. É possível acessar a [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) para uma orientação passo a passo detalhada.

## <a name="overview-of-steps-required"></a>Visão geral das etapas necessárias
Para configurar um aplicativo da galeria do Azure AD, será necessário:

-   [Adicionar um aplicativo da galeria do Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurar os valores de metadados do aplicativo no Azure AD (URL de Logon, Identificador, URL de Resposta)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Selecionar o Identificador de Usuário e adicionar os atributos de usuário a serem enviados ao aplicativo](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Recuperar certificado e metadados Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Configurar os valores de metadados do Azure AD no aplicativo (URL de Logon, Emissor, URL de Logoff e certificado)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Atribuir usuários ao aplicativo](#assign-users-to-the-application)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicionar um aplicativo da galeria do Azure AD

Para adicionar um aplicativo da galeria do Azure AD, siga as etapas abaixo:

1.  Abra o [Portal do Azure](https://portal.azure.com) e entre como um **Administrador Global** ou **Coadministrador**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  clique no botão **Adicionar** no canto superior direito do painel **Aplicativos Empresariais**.

6.  Na caixa de texto **Inserir um nome** da seção **Adicionar da galeria**, digite o nome do aplicativo.

7.  Selecione o aplicativo para o qual você deseja configurar o logon único.

8.  Antes de adicionar o aplicativo, é possível alterar seu nome pela caixa de texto **Nome**.

9.  Clique no botão **Adicionar** para adicionar o aplicativo.

Após um curto período de tempo, você poderá ver o painel de configuração do aplicativo.

## <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Configurar o logon único para um aplicativo da galeria do Azure AD

Para configurar o logon único para um aplicativo, siga as etapas abaixo:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global** ou **Coadministrador**.

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6. Selecione o aplicativo para o qual você deseja configurar o logon único.

7. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8. Selecione **Logon com base em SAML** da lista suspensa **Modo**.

9. Insira os valores necessários em **Domínio e URLs.** É possível obter esses valores do fornecedor do aplicativo.

   1. Para configurar o aplicativo como SSO iniciado por SP, a URL de Logon é um valor obrigatório. Para alguns aplicativos, o Identificador também é um valor obrigatório.

   2. Para configurar o aplicativo como SSO iniciado por IdP, a URL de Resposta é um valor obrigatório. Para alguns aplicativos, o Identificador também é um valor obrigatório.

10. **Opcional:** clique **Mostrar configurações avançadas de URL** se quiser ver os valores não obrigatórios.

11. Nos **Atributos de usuário**, selecione o identificador exclusivo para os usuários na lista suspensa **Identificador de usuário**.

12. **Opcional:** clique em **Exibir e editar todos os outros atributos de usuário** para editar os atributos a serem enviados ao aplicativo no token SAML quando os usuário entrarem.

    Para adicionar um atributo:
   
    1. clique em **Adicionar atributo**. Insira o **Nome** e selecione o **Valor** da lista suspensa.

    1. Clique em **Salvar.** Você verá o novo atributo na tabela.

13. clique em **Configurar &lt;nome do aplicativo&gt;**  para acessar a documentação sobre como configurar o logon único no aplicativo. Além disso, você tem URLs de metadados e o certificado necessários para configurar o SSO com o aplicativo.

14. Clique em **Salvar** para salvar a configuração.

15. Atribuir usuários a um aplicativo.

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

   2. Clique em **Salvar**. Você verá o novo atributo na tabela.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Baixar o certificado ou metadados do Azure AD

Para baixar o certificado ou metadados do aplicativo Azure AD, siga as etapas abaixo:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   *  Se você não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos**.

6. Selecione o aplicativo para o qual você precisa configurar o logon único.

7. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8. Vá para a seção **Certificado de Autenticação SAML** e, em seguida, clique no valor da coluna **Download**. Dependendo do aplicativo que exigir a configuração de logon único, você verá a opção para baixar o Certificado ou o XML de Metadados.

O Microsoft Azure Active Directory também fornece uma URL para obter os metadados. Siga esse padrão para obter a URL de metadados específicos ao aplicativo: `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`

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



