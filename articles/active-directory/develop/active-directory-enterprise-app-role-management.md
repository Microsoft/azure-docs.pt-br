---
title: Configurar a declaração de função emitida no token SAML para aplicativos empresariais no Azure AD | Microsoft Docs
description: Saiba como configurar a declaração de função emitida no token SAML para aplicativos empresariais no Azure Active Directory
services: active-directory
documentationcenter: ''
author: jeevansd
manager: CelesteDG
editor: ''
ms.assetid: eb2b3741-3cde-45c8-b639-a636f3df3b74
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 148324f293c36b88657f50a7405d85210b62dcc4
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321235"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>Como: configurar a declaração de função emitida no token SAML para aplicativos empresariais

Usando o Azure AD (Azure Active Directory), você pode personalizar o tipo de declaração para a declaração de função no token de resposta recebido após a autorização de um aplicativo.

## <a name="prerequisites"></a>pré-requisitos

- Uma assinatura do Microsoft Azure AD com configuração de diretório.
- Uma assinatura com logon único habilitado. Você precisa configurar o SSO com o aplicativo.

## <a name="when-to-use-this-feature"></a>Quando usar esse recurso

Se o aplicativo esperar que as funções personalizadas sejam passadas em uma resposta SAML, será necessário usar esse recurso. Você pode criar quantas funções precisar para passar do Azure AD para o aplicativo.

## <a name="create-roles-for-an-application"></a>Criar funções para um aplicativo

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione o ícone do **Azure Active Directory**.

    ![Ícone do Azure Active Directory][1]

2. Selecione **Aplicativos empresariais**. Em seguida, selecione **Todos os aplicativos**.

    ![Painel Aplicativos empresariais][2]

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![Botão “Novo aplicativo”][3]

4. Na caixa de pesquisa, digite o nome do seu aplicativo e selecione o aplicativo no painel de resultados. Selecione o botão **Adicionar** para adicionar o aplicativo.

    ![Aplicativo na lista de resultados](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Depois que o aplicativo for adicionado, vá para a página **Propriedades** e copie a ID de Objeto.

    ![Página Propriedades](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Abra o [Explorador do Graph do Azure AD](https://developer.microsoft.com/graph/graph-explorer) em outra janela e execute as seguintes etapas:

    a. Entre no site do Explorador do Graph usando as credenciais de administrador ou de coadministrador globais para o locatário.

    b. Você precisa ter permissões suficientes para criar as funções. Selecione **modificar permissões** para obter as permissões.

      ![O botão "modificar permissões"](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Selecione as permissões da lista a seguir (se você já não tiver) e clique em **Modificar Permissões**.

      ![Lista de permissões e botão "Modificar Permissões"](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    > [!Note]
    > A função de Administrador do Aplicativo na Nuvem e de Administrador do Aplicativo não funcionarão neste cenário, pois são necessárias as permissões de Administrador Global para Leitura e Gravação do Diretório.

    d. Aceite a solicitação de consentimento. Você está conectado ao sistema novamente.

    e. Altere a versão para **beta** e busque a lista de entidades de serviço do locatário usando a consulta abaixo:

     `https://graph.microsoft.com/beta/servicePrincipals`

      Se você está usando vários diretórios, siga este padrão: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

      ![Caixa de diálogo do Explorador do Graph, com a consulta para buscar as entidades de serviço](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

      > [!Note]
      > Já estamos no processo de atualizar as APIs; portanto, os clientes podem ter alguma interrupção no serviço.

    f. Da lista de entidades de serviço retornada, obtenha a que deve ser modificada. Você também pode usar Ctrl+F para pesquisar o aplicativo de todas as entidades de serviço listadas. Procure a ID de objeto copiada da página **Propriedades** e use a seguinte consulta para obter a entidade de serviço:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![Consulta para obter a entidade de serviço que você precisa modificar](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Extraia a propriedade **appRoles** do objeto da entidade de serviço.

      ![Detalhes da propriedade appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > Se você estiver usando o aplicativo personalizado (não o aplicativo do Azure Marketplace), verá duas funções padrão: msiam_access e user. Para o aplicativo do Marketplace, msiam_access é a única função padrão. Você não precisa fazer alterações nas funções padrão.

    h. Gere novas funções para o aplicativo.

      O JSON a seguir é um exemplo do objeto **appRoles**. Crie um objeto semelhante para adicionar as funções desejadas no aplicativo.

      ```
      {
         "appRoles": [
          {
              "allowedMemberTypes": [
                  "User"
              ],
              "description": "msiam_access",
              "displayName": "msiam_access",
              "id": "b9632174-c057-4f7e-951b-be3adc52bfe6",
              "isEnabled": true,
              "origin": "Application",
              "value": null
          },
          {
              "allowedMemberTypes": [
                  "User"
              ],
              "description": "Administrators Only",
              "displayName": "Admin",
              "id": "4f8f8640-f081-492d-97a0-caf24e9bc134",
              "isEnabled": true,
              "origin": "ServicePrincipal",
              "value": "Administrator"
          }
      ]
      }
      ```

      > [!Note]
      > Você só pode adicionar novas funções depois do msiam_access para a operação patch. Além disso, você pode adicionar quantas regras quiser de acordo com a necessidade da sua organização. O Azure AD enviará o valor dessas funções como o valor da declaração na resposta SAML. Para gerar os valores de GUID para a ID das novas funções, use ferramentas da Web como [esta](https://www.guidgenerator.com/)

    i. Volte ao Explorador do Graph e altere o método de **GET** para **PATCH**. Execute o patch no objeto da entidade de serviço para obter as funções desejadas após a atualização da propriedade **appRoles**, conforme exemplo anterior. Clique em **Executar Consulta** para executar a operação de patch. Uma mensagem confirma a criação da função.

      ![Operação de patch bem-sucedida](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Após a atualização da entidade de serviço com mais funções, será possível atribuir usuários às respectivas funções. Você pode atribuir os usuários acessando o portal e navegando até o aplicativo. Selecione a guia **Usuários e grupos**. Essa guia relaciona todos os usuários e grupos que já foram atribuídos ao aplicativo. Você pode adicionar novos usuários às novas funções. Você também pode selecionar um usuário existente e selecionar **Editar** para alterar a função.

    ![Guia “Usuários e grupos”](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Para atribuir a função a um usuário, selecione a nova função e selecione o botão **Atribuir** na parte inferior da página.

    ![Painel "Editar Atribuição" e painel "Selecionar Função"](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Você precisa atualizar sua sessão no Portal do Azure para ver as novas funções.

8. Atualize a tabela **Atributos** para definir um mapeamento personalizado da declaração de função.

9. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | Nome do atributo | Valor do atributo |
    | -------------- | ----------------|
    | Nome da função  | user.assignedroles |

    >[!NOTE]
    >Se o valor de declaração de função for nulo, o Azure AD não enviará esse valor no token e isso será padrão de acordo com o design.

    a. Clique no ícone **Editar** para abrir **atributos de usuário &** caixa de diálogo declarações.

      ![Botão "Adicionar atributo"](./media/active-directory-enterprise-app-role-management/editattribute.png)

    b. Na caixa de diálogo **gerenciar declarações do usuário** , adicione o atributo de token SAML clicando em **Adicionar nova declaração**.

      ![Botão "Adicionar atributo"](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      ![Painel “Adicionar Atributo”](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    c. Na caixa **Nome**, digite o nome do atributo conforme necessário. Este exemplo usa **Nome da Função** como o nome da declaração.

    d. Deixe a caixa **Namespace** em branco.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Salvar**.

10. Para testar seu aplicativo em um logon único iniciado por um provedor de identidade, entre no [Painel de Acesso](https://myapps.microsoft.com) e selecione o bloco do aplicativo. No token SAML, você deve ver todas as funções atribuídas ao usuário com o nome da declaração fornecido.

## <a name="update-an-existing-role"></a>Atualizar uma função existente

Para atualizar uma função existente, execute as seguintes etapas:

1. Abrir o [Explorador do Graph do Microsoft Azure AD](https://developer.microsoft.com/graph/graph-explorer).

2. Entre no site do Explorador do Graph usando as credenciais de administrador ou de coadministrador globais para o locatário.

3. Altere a versão para **beta** e busque a lista de entidades de serviço do locatário usando a consulta abaixo:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Se você está usando vários diretórios, siga este padrão: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Caixa de diálogo do Explorador do Graph, com a consulta para buscar as entidades de serviço](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. Da lista de entidades de serviço retornada, obtenha a que deve ser modificada. Você também pode usar Ctrl+F para pesquisar o aplicativo de todas as entidades de serviço listadas. Procure a ID de objeto copiada da página **Propriedades** e use a seguinte consulta para obter a entidade de serviço:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Consulta para obter a entidade de serviço que você precisa modificar](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Extraia a propriedade **appRoles** do objeto da entidade de serviço.

    ![Detalhes da propriedade appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

6. Para atualizar a função existente, use as etapas a seguir.

    ![Corpo da solicitação para "PATCH", com "description" e "displayname" realçados](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    a. Altere o método de **GET** para **PATCH**.

    b. Copie as funções existentes e cole-as em **Corpo da Solicitação**.

    c. Atualize o valor de uma função atualizando a descrição da função, o valor da função ou o nome de exibição da função, conforme necessário.

    d. Depois de atualizar todas as funções necessárias, selecione **Executar Consulta**.

## <a name="delete-an-existing-role"></a>Excluir uma função existente

Para excluir uma função existente, execute as seguintes etapas:

1. Abra o [Explorador do Graph do Microsoft Azure AD](https://developer.microsoft.com/graph/graph-explorer) em outra janela.

2. Entre no site do Explorador do Graph usando as credenciais de administrador ou de coadministrador globais para o locatário.

3. Altere a versão para **beta** e busque a lista de entidades de serviço do locatário usando a consulta abaixo:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Se você está usando vários diretórios, siga este padrão: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Caixa de diálogo do Explorador do Graph, com a consulta para buscar a lista de entidades de serviço](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. Da lista de entidades de serviço retornada, obtenha a que deve ser modificada. Você também pode usar Ctrl+F para pesquisar o aplicativo de todas as entidades de serviço listadas. Procure a ID de objeto copiada da página **Propriedades** e use a seguinte consulta para obter a entidade de serviço:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Consulta para obter a entidade de serviço que você precisa modificar](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Extraia a propriedade **appRoles** do objeto da entidade de serviço.

    ![Detalhes da propriedade appRoles do objeto da entidade de serviço](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Para excluir a função existente, use as etapas a seguir.

    ![Corpo da solicitação para "PATCH", com IsEnabled definido como false](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. Altere o método de **GET** para **PATCH**.

    b. Copie as funções existentes do aplicativo e cole-as em **Corpo da Solicitação**.

    c. Defina o valor **IsEnabled** como **false** para a função que deseja excluir.

    d. Selecione **Executar Consulta**.

    > [!NOTE]
    > Verifique se você tem a função msiam_access e se a ID corresponde com a função gerada.

7. Depois que a função é desabilitada, exclua esse bloco de função na seção **appRoles**. Mantenha o método como **PATCH** e selecione **Executar Consulta**.

8. Depois que a consulta é executada, a função é excluída.

    > [!NOTE]
    > A função deve ser desabilitada antes de ser removida.

## <a name="next-steps"></a>Próximas etapas

Para obter as etapas adicionais, confira a [documentação do aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
