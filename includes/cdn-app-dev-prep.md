---
author: zhangmanling
ms.service: azure-cdn
ms.topic: include
ms.date: 11/21/2018
ms.author: mazha
ms.openlocfilehash: f21a768733456a6c00e5a87612f3055dd76d416c
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594134"
---
## <a name="prerequisites"></a>Pré-requisitos
Antes de gravar o código de gerenciamento da CDN, é necessário fazer algumas preparações para habilitar o código a interagir com o Azure Resource Manager. Para fazer essas preparações, é necessário:

* Crie um grupo de recursos para conter o perfil CDN criada neste tutorial
* Configurar o Azure Active Directory para fornecer autenticação ao aplicativo
* Aplicar permissões ao grupo de recursos, de modo que somente usuários autorizados do locatário do Azure Active Directory possam interagir com o perfil CDN

### <a name="creating-the-resource-group"></a>Criando o grupo de recursos
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **Criar um recurso**.
3. Procure o **Grupo de recursos** e no painel de Grupos de Recursos, clique em **Criar**.

    ![Criando um novo grupo de recursos](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Nomeie o grupo de recursos *CdnConsoleTutorial*.  Selecione sua assinatura e escolha uma localização perto de você.  Se desejar, você poderá clicar na caixa de seleção **Fixar no painel** para fixar o grupo de recursos no painel que está no portal.  Ao fixar, facilita a localização posteriormente.  Após fazer suas seleções, clique em **Criar**.

    ![Nomeando o grupo de recursos](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. Após a criação do grupo de recursos, se você não o fixou ao painel, encontre-o clicando em **Procurar** e em **Grupos de Recursos**.  Para abri-lo, clique no grupo de recursos.  Anote sua **ID da assinatura**. Ela será necessária mais tarde.

    ![Nomeando o grupo de recursos](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Criar o aplicativo do Azure AD e aplicar permissões
Há duas abordagens de autenticação de aplicativo com o Azure Active Directory: Usuários individuais ou uma entidade de serviço. Uma entidade de serviço é semelhante a uma conta de serviço do Windows.  Em vez de conceder permissões particulares de um usuário para interagir com os perfis CDN, as permissões são concedidas à entidade de serviço.  As entidades de serviço, geralmente são utilizadas para processos automatizados não interativos.  Embora este tutorial esteja gravando um aplicativo de console interativo, nos concentraremos na abordagem da entidade de serviço.

A criação de uma entidade de serviço abarca várias etapas, incluindo o desenvolvimento de um aplicativo do Azure Active Directory.  Para criá-lo, vamos [seguir este tutorial](../articles/active-directory/develop/howto-create-service-principal-portal.md).

> [!IMPORTANT]
> Siga todas as etapas do [tutorial vinculado](../articles/active-directory/develop/howto-create-service-principal-portal.md).  É *importante* que você conclua-o exatamente conforme descrito.  Anote a **ID do locatário**, o **nome de domínio do locatário** (normalmente um domínio *.onmicrosoft.com*, a menos que você tenha especificado um domínio personalizado), a **ID do cliente** e a **chave de autenticação do cliente**, pois essas informações serão necessárias mais tarde.  Proteja a **ID do cliente** e a **chave de autenticação de cliente**, pois essas credenciais podem ser utilizadas por qualquer pessoa para executar operações como a entidade de serviço.
>
> Quando chegar à etapa chamada Configurar aplicativo multilocatário, selecione **Não**.
>
> Quando você chegar à etapa [Atribua o aplicativo a uma função](../articles/active-directory/develop/howto-create-service-principal-portal.md#assign-the-application-to-a-role), use o grupo de recursos criado anteriormente, *CdnConsoleTutorial*, mas, em vez da função **Reader**, atribua a função **CDN Profile Contributor**.  Depois de atribuir a função **Colaborador do Perfil CDN** ao aplicativo em seu grupo de recursos, volte para este tutorial. 
>
>

Após ter criado a entidade de serviço e atribuído a função **Colaborador de Perfil de CDN**, a folha **Usuários** do grupo de recursos deverá ser semelhante à seguinte imagem.

![Folha de usuários](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>Autenticação de usuário interativo
Se, em vez de uma entidade de serviço você preferir a autenticação de usuário individual interativa, o processo será semelhante ao de uma entidade de serviço.  De fato, é necessário seguir o mesmo procedimento, mas fazer algumas pequenas alterações.

> [!IMPORTANT]
> Siga as próximas etapas, se escolher usar a autenticação de usuário individual, em vez de uma entidade de serviço.
>
>

1. Ao criar seu aplicativo, em vez de **Aplicativo Web**, escolha **Aplicativo nativo**.

    ![Aplicativo nativo](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. Na próxima página, será solicitado um **URI de Redirecionamento**.  O URI não será validado, mas lembre-se do que você digitou. Isso será necessário mais tarde.
3. Não é necessário criar uma **chave de autenticação do cliente**.
4. Em vez de atribuir uma entidade de serviço para a função **Colaborador do Perfil CDN** , vamos atribuir usuários individuais ou grupos.  Neste exemplo, é possível ver que atribui o *Usuário de Demonstração CDN* à função **Colaborador do Perfil CDN**.  

    ![Acesso de usuário individual](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
