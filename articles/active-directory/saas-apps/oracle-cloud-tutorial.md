---
title: 'Tutorial: Integração do Azure Active Directory com o Console da Infraestrutura de Nuvem da Oracle | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Console da Infraestrutura de Nuvem da Oracle.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: f045fe19-11f8-4ccf-a3eb-8495fdc8716f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0725988ff88baea2458f0a5e459440874e66088
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596431"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>Tutorial: Integrar o Console da Infraestrutura de Nuvem da Oracle com o Azure Active Directory

Neste tutorial, você aprenderá a integrar o Console da Infraestrutura de Nuvem da Oracle ao Azure Active Directory (Azure AD). Quando você integra o Console da Infraestrutura de Nuvem da Oracle com o Azure Active Directory, você pode:

* Controle no Azure AD quem tem acesso ao Console da Infraestrutura de Nuvem da Oracle.
* Permita que os usuários sejam conectados automaticamente ao Console da Infraestrutura de Nuvem da Oracle com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura com logon único (SSO) do Console da Infraestrutura de Nuvem da Oracle habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Console da Infraestrutura de Nuvem da Oracle é compatível com o SSO iniciado por **SP**.

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>Adicionar o Console da Infraestrutura de Nuvem da Oracle da galeria

Para configurar a integração do Console da Infraestrutura de Nuvem da Oracle com o Azure AD, você precisará adicionar o Console da Infraestrutura de Nuvem da Oracle à sua lista de aplicativos SaaS gerenciados da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Console da Infraestrutura de Nuvem da Oracle** na caixa de pesquisa.
1. Selecione **Console da Infraestrutura de Nuvem da Oracle** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o Console da Infraestrutura de Nuvem da Oracle usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Console da Infraestrutura de Nuvem da Oracle.

Para configurar e testar o SSO do Azure AD com o Console da Infraestrutura de Nuvem da Oracle, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
1. **[Configure o Console da Infraestrutura de Nuvem da Oracle](#configure-oracle-cloud-infrastructure-console)** para definir as configurações do SSO no lado do aplicativo.
1. **[Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Crie um usuário de teste do Console da Infraestrutura de Nuvem da Oracle](#create-oracle-cloud-infrastructure-console-test-user)** para ter um equivalente de Brenda Fernandes no Console da Infraestrutura de Nuvem da Oracle que esteja vinculado à representação de usuário do Azure AD.
1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Console da Infraestrutura de Nuvem da Oracle**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configuração Básica de SAML**, insira os valores nos seguintes campos:

   > [!NOTE]
   > Você obterá o arquivo de metadados do provedor de serviço, da seção **Configurar o logon único do Console da Infraestrutura de Nuvem da Oracle** do tutorial.
    
   1. Clique em **Carregar arquivo de metadados**.

   1. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

   1. Depois que o arquivo de metadados for carregado com êxito, os valores de **Identificador** e **URL de Resposta** serão preenchidos automaticamente na seção de caixa de texto **Configuração Básica do SAML**.
    
      > [!NOTE]
      > Se os valores de **Identificador** e **URL de Resposta** não forem preenchidos automaticamente, preencha-os manualmente de acordo com seus requisitos.

      Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://console.<REGIONNAME>.oraclecloud.com/`

      > [!NOTE]
      > O valor não é real. Atualize o valor com a URL de Logon real. Entre em contato com [equipe de suporte do cliente do Console da Infraestrutura de Nuvem da Oracle](https://www.oracle.com/support/advanced-customer-support/products/cloud.html) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

   ![O link de download do Certificado](common/metadataxml.png)

1. O aplicativo Console da Infraestrutura de Nuvem da Oracle espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone  **Editar** para abrir a caixa de diálogo Atributos do usuário.

   ![image](common/edit-attribute.png)

1. Além do indicado acima, o aplicativo Console da Infraestrutura de Nuvem da Oracle espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção **Atributos e Declarações de Usuário** da caixa de diálogo **Declarações de Grupo (Versão Prévia)**  , execute as seguintes etapas:

   1. Clique na **caneta** ao lado do **Valor do identificador de nome**.

   1. Selecione **Persistente** como **Escolha o formato do identificador de nome**.
 
   1. Clique em **Save** (Salvar).

      ![image](./media/oracle-cloud-tutorial/config07.png)
    
      ![image](./media/oracle-cloud-tutorial/config11.png)

   1. Clique na **caneta** ao lado de **Grupos retornados na declaração**.

   1. Selecione **Grupos de Segurança** na lista de opções.

   1. Selecione **Atributo de Origem** da **ID do Grupo**.

   1. Marque **Personalizar o nome da declaração de grupo**.

   1. Na caixa de texto **Nome**, digite **GroupName**.

   1. Na caixa de texto **Namespace (opcional)** , digite `https://auth.oraclecloud.com/saml/claims`.

   1. Clique em **Save** (Salvar).

      ![image](./media/oracle-cloud-tutorial/config08.png)

1. Na seção **Configurar o Console da Infraestrutura de Nuvem da Oracle**, copie as URLs apropriadas de acordo com suas necessidades.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-oracle-cloud-infrastructure-console"></a>Configurar Console da Infraestrutura de Nuvem da Oracle

1. Em outra janela do navegador da Web, entre no Console da Infraestrutura de Nuvem da Oracle como um Administrador.

1. Clique no lado esquerdo do menu e clique em **Identidade** e navegue até **Federação**.

   ![Configuração](./media/oracle-cloud-tutorial/config01.png)

1. Salve o **arquivo de metadados do Provedor de Serviços** clicando no link **Baixar este documento** e faça o upload para a seção **Configuração Básica do SAML** do portal do Azure e, em seguida, clique em **Adicionar Provedor de Identidade**.

   ![Configuração](./media/oracle-cloud-tutorial/config02.png)

1. No item pop-up **Adicionar Provedor de Identidade**, execute as seguintes etapas:

   ![Configuração](./media/oracle-cloud-tutorial/config03.png)

   1. Na caixa de texto **NOME**, insira seu nome.

   1. Na caixa de texto **DESCRIÇÃO**, insira sua descrição.

   1. Selecione **SERVIÇO DE FEDERAÇÃO DE DIRETÓRIO ATIVO DA MICROSOFT (ADFS) OU PROVEDOR DE IDENTIDADE EM CONFORMIDADE COM SAML 2.0** como **TIPO**.

   1. Clique em **Procurar** para fazer upload do XML de Metadados de Federação que você baixou do portal do Azure.

   1. Clique em **Continuar** e, na seção **Editar Provedor de Identidade**, execute as seguintes etapas:

      ![Configuração](./media/oracle-cloud-tutorial/config09.png)

   1. O **GRUPO DE PROVEDOR DE IDENTIDADE** deve ser selecionado como Grupo Personalizado. A ID DO GRUPO deve ser a GUID do grupo do Azure Active Directory. O grupo precisa ser mapeado com o grupo correspondente no campo **GRUPO OCI**.

   1. Você pode mapear vários grupos de acordo com sua configuração no portal do Azure e sua necessidade de organização. Clique em **+ Adicionar mapeamento** para adicionar quantos grupos conforme necessário.

   1. Clique em **Enviar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B. Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B. Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure permitindo a ela acesso ao Console da Infraestrutura de Nuvem da Oracle.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Console da Infraestrutura de Nuvem da Oracle**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

   ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B. Fernandes** na lista Usuários e, em seguida, clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>Criar o usuário de teste do Console da Infraestrutura de Nuvem da Oracle

 O Console da Infraestrutura de Nuvem da Oracle oferece suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário não é criado durante uma tentativa de acesso e também não é necessário criar o usuário.

### <a name="test-sso"></a>Testar o SSO

Quando selecionar o bloco do Console da Infraestrutura de Nuvem da Oracle no Painel de Acesso, você será redirecionado para a página de entrada do Console da Infraestrutura de Nuvem da Oracle. Selecione o **PROVEDOR DE IDENTIDADE** no menu suspenso e clique em **Continuar**, conforme mostrado abaixo, para entrar. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

![Configuração](./media/oracle-cloud-tutorial/config10.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
