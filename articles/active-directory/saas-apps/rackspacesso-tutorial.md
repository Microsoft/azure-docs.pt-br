---
title: 'Tutorial: Integração do Azure Active Directory ao Rackspace SSO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Rackspace SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 36b398be-2f7e-4ce8-9031-53587299bc4a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: 31826f5d4d88c977f859a009bface2fddf3a1c88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093190"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Tutorial: Integração do Azure Active Directory ao Rackspace SSO

Neste tutorial, você aprenderá a integrar o Rackspace SSO com o Microsoft Azure AD (Azure Active Directory).
A integração do Rackspace SSO ao Microsoft Azure AD oferece os seguintes benefícios:

* É possível controlar no Microsoft Azure AD quem tem acesso ao Rackspace SSO.
* Você pode permitir que os usuários sejam conectados automaticamente ao Rackspace SSO (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure AD ao Rackspace SSO, são necessários os seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura do Rackspace SSO habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Rackspace SSO é compatível com SSO iniciado por **SP**

## <a name="adding-rackspace-sso-from-the-gallery"></a>Adicionando o Rackspace SSO da galeria

Para configurar a integração do Rackspace SSO ao Microsoft Azure AD, é necessário adicionar o Rackspace SSO da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Rackspace SSO da galeria, execute as etapas abaixo:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Rackspace SSO**, selecione **Rackspace SSO** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Rackspace SSO na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure AD com o Rackspace SSO, com base em um usuário de teste chamado **Brenda Fernandes**.
Ao usar o logon único com Rackspace, os usuários Rackspace serão automaticamente criados na primeira vez que entrarem no portal do Rackspace. 

Para configurar e testar o logon único do Microsoft Azure AD com o Rackspace SSO, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar logon único no Rackspace SSO](#configure-rackspace-sso-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Configurar o mapeamento de atributos no painel de controle do Rackspace](#set-up-attribute-mapping-in-the-rackspace-control-panel)** – para atribuir funções de Rackspace a usuários do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Microsoft Azure AD com o Rackspace SSO, siga as etapas abaixo:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Rackspace SSO**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, carregue o **arquivo de metadados do Provedor de Serviço** que você pode baixar da [URL](https://login.rackspace.com/federate/sp.xml) e execute as seguintes etapas:

    a. Clique em **Carregar arquivo de metadados**.

    ![image](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![image](common/browse-upload-metadata.png)

    c. Depois que o arquivo de metadados tiver sido carregado com êxito, as URLs necessárias são automaticamente preenchidas.

    d. Na caixa de texto **URL de Logon**, digite uma URL: `https://login.rackspace.com/federate/`

    ![Informações de logon único em Domínio e URLs do Rackspace SSO](common/sp-signonurl.png)   

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

Esse arquivo será carregado para Rackspace para preencher as definições de configuração de Federação de Identidade necessárias.

### <a name="configure-rackspace-sso-single-sign-on"></a>Configurar logon único do Rackspace SSO

Para configurar o logon único no lado de **SSO do Rackspace SSO**:

1. Veja a documentação em [Adicionar um Provedor de Identidade ao Painel de Controle](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/)
1. Ele o guiará pelas etapas para:
    1. Crie um novo Provedor de Identidade
    1. Especifique um domínio de email que os usuários usarão para identificar sua empresa ao entrar.
    1. Carregue o **XML de Metadados de Federação** baixado anteriormente do painel de controle do Azure.

Isso configurará corretamente as definições básicas de SSO necessárias para o Azure e Rackspace se conectar.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Rackspace SSO.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Rackspace SSO**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Rackspace SSO**.

    ![O link do Rackspace SSO na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Configurar o Mapeamento de Atributos no painel de controle do Rackspace

O Rackspace usa uma **Política de Mapeamento de Atributos** para atribuir funções e grupos do Rackspace a usuários de logon únicos. A **Política de Mapeamento de Atributo** converte declarações SAML do Azure AD nos campos de configuração do usuário que o Rackspace requer. Mais documentação pode ser encontrada na [documentação de Noções básicas de mapeamento de atributo](https://developer.rackspace.com/docs/rackspace-federation/attribmapping-basics/) do Rackspace. Algumas considerações:

* Se você deseja atribuir vários níveis de acesso Rackspace usando grupos do Azure AD, precisa habilitar a declaração de grupos nas configurações de Logon Único de **SSO do Rackspace** do Azure. A **Política de Mapeamento de Atributo** será usada para combinar esses grupos com as funções e os grupos do Rackspace desejados:

    ![As configurações de declaração de Grupos](common/sso-groups-claim.png)

* Por padrão, o Azure AD envia o UID dos Grupos do Azure AD na declaração SAML versus o nome do Grupo. No entanto, se você estiver sincronizando seu Active Directory local com o Azure AD, terá a opção de enviar os nomes reais dos grupos:

    ![As configurações de nome da declaração de Grupo](common/sso-groups-claims-names.png)

O exemplo a seguir de **Política de Mapeamento de Atributo** demonstra:
1. Definir o nome do usuário do Rackspace na declaração SAML `user.name`. Qualquer declaração pode ser usada, mas é mais comum defini-la como um campo que contém o endereço de email do usuário.
1. Definir as funções de Rackspace `admin` e `billing:admin` em um usuário combinando um grupo do Azure AD, seja por Nome de Grupo ou por UID de Grupo. Uma *substituição* de `"{0}"` no campo `roles` é usada e será substituída pelos resultados das expressões da regra `remote`.
1. Usar a `"{D}"` *substituição padrão* para permitir que o Rackspace recupere campos adicionais de SAML procurando declarações SAML padrão e bem conhecidas na troca de SAML.

```yaml
---
mapping:
    rules:
    - local:
        user:
          domain: "{D}"
          name: "{At(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)}"
          email: "{D}"
          roles:
              - "{0}"
          expire: "{D}"
      remote:
          - path: |
              (
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='7269f9a2-aabb-9393-8e6d-282e0f945985') then ('admin', 'billing:admin') else (),
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='MyAzureGroup') then ('admin', 'billing:admin') else ()
              )
            multiValue: true
  version: RAX-1
```
> [!TIP]
> Verifique se você usa um editor de texto que valida a sintaxe YAML ao editar seu arquivo de política.

Consulte a [documentação de Noções básicas de mapeamento de atributo](https://developer.rackspace.com/docs/rackspace-federation/attribmapping-basics/) do Rackspace para obter mais exemplos.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Rackspace SSO no Painel de Acesso, você deverá ser conectado automaticamente ao Rackspace SSO, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Você também pode usar o botão **Validar** nas configurações de logon único de **SSO do Rackspace**:

   ![Botão Validar de SSO](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

