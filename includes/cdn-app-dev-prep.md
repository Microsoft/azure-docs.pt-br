## <a name="prerequisites"></a>Pré-requisitos
Para podermos escrever código de gerenciamento de CDN, precisamos fazer algumas preparações para habilitar o código a interagir com o Azure Resource Manager.  Para fazer isso, você precisará:

* Criar um grupo de recursos para conter o perfil de CDN que criamos neste tutorial
* Configurar o Azure Active Directory para fornecer autenticação ao aplicativo
* Aplicar permissões ao grupo de recursos para que somente usuários autorizados do nosso locatário do Azure AD possam interagir com o perfil de CDN

### <a name="creating-the-resource-group"></a>Criando o grupo de recursos
1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Clique no botão **Novo** no canto superior esquerdo e clique em **Gerenciamento**, depois em **Grupo de Recursos**.

    ![Criando um novo grupo de recursos](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Chame seu grupo de recursos de *CdnConsoleTutorial*.  Selecione sua assinatura e escolha uma localização perto de você.  Se desejar, você poderá clicar na caixa de seleção **Fixar no painel** para fixar o grupo de recursos no painel que está no portal.  Isso facilitará a localização posteriormente.  Após fazer suas seleções, clique em **Criar**.

    ![Nomeando o grupo de recursos](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. Após a criação do grupo de recursos, se você não o fixou ao painel, encontre-o clicando em **Procurar** e em **Grupos de Recursos**.  Clique no grupo de recursos para abri-lo.  Anote sua **ID da assinatura**.  Precisaremos dela mais tarde.

    ![Nomeando o grupo de recursos](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Criar o aplicativo do Azure AD e aplicar permissões
Há duas abordagens para autenticação de aplicativo com o Azure Active Directory: usuários individuais ou uma entidade de serviço. Uma entidade de serviço é semelhante a uma conta de serviço do Windows.  Em vez de conceder permissões a um determinado usuário para interagir com os perfis de CDN, conceda as permissões à entidade de serviço.  As entidades de serviço geralmente são usadas para processos automatizados não interativos.  Embora este tutorial esteja gravando um aplicativo de console interativo, nos concentraremos na abordagem da entidade de serviço.

A criação de uma entidade de serviço abarca várias etapas, incluindo o desenvolvimento de um aplicativo do Azure Active Directory.  Para fazer isso, seguiremos [este tutorial](../articles/resource-group-create-service-principal-portal.md).

> [!IMPORTANT]
> Siga todas as etapas do [tutorial vinculado](../articles/resource-group-create-service-principal-portal.md).  É *extremamente importante* que você faça exatamente o que está descrito.  Anote a **ID do locatário**, o **nome de domínio do locatário** (normalmente um domínio *.onmicrosoft.com*, a menos que você tenha especificado um domínio personalizado), a **ID do cliente** e a **chave de autenticação do cliente**, pois essas informações serão necessárias mais tarde.  Procure proteger sua **ID do cliente** e a **chave de autenticação do cliente**, pois essas credenciais podem ser usadas por qualquer pessoa para executar operações como a entidade de serviço.
>
> Quando chegar à etapa chamada Configurar aplicativo multilocatário, selecione **Não**.
>
> Quando chegar à etapa [Atribuir aplicativo à função](../articles/azure-resource-manager/resource-group-create-service-principal-portal.md#assign-application-to-role), use o grupo de recursos criado anteriormente, *CdnConsoleTutorial*, mas, em vez da função **Leitor**, atribua a função **Colaborador do Perfil CDN**.  Depois de atribuir a função **Colaborador do Perfil CDN** ao aplicativo em seu grupo de recursos, volte para este tutorial. 
>
>

Depois de ter criado a entidade de serviço e atribuído a função **Colaborador do Perfil CDN**, a folha **Usuários** do seu grupo de recursos deve ser semelhante a esta.

![Folha de usuários](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>Autenticação de usuário interativo
Se, em vez de uma entidade de serviço, você preferir a autenticação de usuário individual interativa, o processo será muito semelhante ao de uma entidade de serviço.  Na verdade, você precisará seguir o mesmo procedimento, mas fazer algumas pequenas alterações.

> [!IMPORTANT]
> Siga as próximas etapas, se escolher usar a autenticação de usuário individual, em vez de uma entidade de serviço.
>
>

1. Ao criar seu aplicativo, em vez de **Aplicativo Web**, escolha **Aplicativo nativo**.

    ![Aplicativo nativo](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. Na próxima página, será solicitada o **URI de redirecionamento**.  O URI não será validado, mas lembre-se do que você digitou.  Você precisará dela mais tarde.
3. Não é necessário criar uma **chave de autenticação do cliente**.
4. Em vez de atribuir uma entidade de serviço para a função **Colaborador do Perfil CDN** , vamos atribuir usuários individuais ou grupos.  Neste exemplo, é possível ver que atribui o *Usuário de Demonstração CDN* à função **Colaborador do Perfil CDN**.  

    ![Acesso de usuário individual](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
