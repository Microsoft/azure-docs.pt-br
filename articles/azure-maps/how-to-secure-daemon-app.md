---
title: Como proteger um aplicativo daemon
titleSuffix: Azure Maps
description: Use o portal do Azure para gerenciar a autenticação para configurar um aplicativo de daemon confiável.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2dd04f404330a6c86e2df09da610e16ba9b721f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895640"
---
# <a name="secure-a-daemon-application"></a>Proteger um aplicativo daemon

O guia a seguir é para processos em segundo plano, temporizadores e trabalhos que são hospedados em um ambiente confiável e protegido. Os exemplos incluem trabalhos Web do Azure, aplicativos de funções do Azure, serviços do Windows e qualquer outro serviço de segundo plano confiável.

> [!Tip]
> A Microsoft recomenda implementar o Azure Active Directory (Azure AD) e o Azure RBAC (controle de acesso baseado em função) para aplicativos de produção. Para obter uma visão geral dos conceitos, consulte [autenticação do Azure Maps](./azure-maps-authentication.md).

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="scenario-shared-key-authentication"></a>Cenário: autenticação de chave compartilhada

Depois de criar uma conta do Azure Maps, as chaves primária e secundária são geradas. Recomendamos que você use a chave primária como a chave de assinatura ao [usar a autenticação de chave compartilhada para chamar o Azure Maps](./azure-maps-authentication.md#shared-key-authentication). Você pode usar uma chave secundária em cenários como alterações de chave sem interrupção. Para obter mais informações, consulte [autenticação no Azure Maps](./azure-maps-authentication.md).

### <a name="securely-store-shared-key"></a>Armazenar chave compartilhada com segurança

A chave primária e secundária permitem a autorização para todas as APIs da conta do Maps. Os aplicativos devem armazenar as chaves em um repositório seguro, como Azure Key Vault. O aplicativo deve recuperar a chave compartilhada como um segredo Azure Key Vault para evitar armazenar a chave compartilhada em texto sem formatação na configuração do aplicativo. Para entender como configurar um Azure Key Vault, consulte [Azure Key Vault guia do desenvolvedor](../key-vault/general/developers-guide.md).

As etapas a seguir descrevem esse processo:

1. Crie um Cofre de chaves do Azure.
2. Crie uma entidade de serviço do Azure AD criando um registro de aplicativo ou uma identidade gerenciada, a entidade de segurança criada é responsável por acessar Azure Key Vault.
3. Atribua a permissão acesso da entidade de serviço à chave segredos do Azure `Get` .
4. Atribua temporariamente acesso à permissão de segredos `Set` para você como desenvolvedor.
5. Defina a chave compartilhada na Key Vault segredos e referencie a ID secreta como configuração para o aplicativo daemon e remova sua permissão de segredos `Set` .
6. Implemente a autenticação do Azure AD no aplicativo daemon para recuperar o segredo de chave compartilhada do Azure Key Vault.
7. Criar solicitação da API REST do Azure Maps com chave compartilhada.

> [!Tip]
> Se o aplicativo estiver hospedado no ambiente do Azure, você deverá implementar uma identidade gerenciada para reduzir o custo e a complexidade do gerenciamento de um segredo para autenticação no Azure Key Vault. Consulte o tutorial de Azure Key Vault a seguir [para se conectar por meio de identidade gerenciada](../key-vault/general/tutorial-net-create-vault-azure-web-app.md).

O aplicativo daemon é responsável por recuperar a chave compartilhada de um armazenamento seguro. A implementação com Azure Key Vault requer autenticação por meio do Azure AD para acessar o segredo. Em vez disso, incentivamos a autenticação direta do Azure AD para o Azure Maps como resultado da complexidade adicional e dos requisitos operacionais do uso da autenticação de chave compartilhada.

> [!IMPORTANT]
> Para simplificar a regeneração de chave, recomendamos que os aplicativos usem uma chave por vez. Os aplicativos podem regenerar a chave não utilizada e implantar a nova chave regenerada em um repositório secreto seguro, como Azure Key Vault.

## <a name="scenario-azure-ad-role-based-access-control"></a>Cenário: controle de acesso baseado em função do AD do Azure

Depois que uma conta do Azure Maps é criada, o valor do Azure Maps `x-ms-client-id` está presente na página de detalhes de portal do Azure autenticação. Esse valor representa a conta que será usada para solicitações da API REST. Esse valor deve ser armazenado na configuração do aplicativo e recuperado antes de fazer solicitações HTTP. O objetivo do cenário é habilitar o aplicativo daemon para autenticar no Azure AD e chamar as APIs REST do Azure Maps.

> [!Tip]
> É recomendável hospedar em máquinas virtuais do Azure, conjuntos de dimensionamento de máquinas virtuais ou serviços de aplicativos para habilitar os benefícios de componentes de identidade gerenciados.

### <a name="daemon-hosted-on-azure-resources"></a>Daemon hospedado em recursos do Azure

Ao executar em recursos do Azure, configure as identidades gerenciadas do Azure para habilitar o esforço mínimo de gerenciamento de credenciais e baixo custo. 

Consulte [visão geral de identidades gerenciadas](../active-directory/managed-identities-azure-resources/overview.md) para habilitar o acesso do aplicativo a uma identidade gerenciada.

Benefícios de identidade gerenciada:

* Autenticação de criptografia de chave pública de certificado X509 gerenciado pelo sistema do Azure.
* Segurança do Azure AD com certificados X509 em vez de segredos do cliente.
* O Azure gerencia e renova todos os certificados associados ao recurso de identidade gerenciada.
* Gerenciamento operacional de credencial simplificado ao remover qualquer necessidade de um serviço de repositório de segredo seguro como Azure Key Vault. 

### <a name="daemon-hosted-on-non-azure-resources"></a>Daemon hospedado em recursos que não são do Azure

Quando em execução em um ambiente não Azure, as identidades gerenciadas não estão disponíveis. Portanto, você deve configurar uma entidade de serviço por meio de um registro de aplicativo do Azure AD para o aplicativo daemon.

1. Na portal do Azure, na lista de serviços do Azure, selecione **Azure Active Directory**  >  **registros de aplicativo**  >  **novo registro**.  

    > [!div class="mx-imgBorder"]
    > ![Registro do Aplicativo](./media/how-to-manage-authentication/app-registration.png)

2. Se você já registrou seu aplicativo, continue na próxima etapa. Se você ainda não registrou seu aplicativo, insira um **nome**, escolha um **tipo de conta de suporte** e, em seguida, selecione **registrar**.  

    > [!div class="mx-imgBorder"]
    > ![Detalhes de registro do aplicativo](./media/how-to-manage-authentication/app-create.png)

3. Para atribuir permissões de API delegadas para mapas do Azure, vá para o aplicativo. Em **registros de aplicativo**, selecione **permissões**  >  **de API adicionar uma permissão**. Em **APIs que minha organização usa**, pesquise e selecione **mapas do Azure**.

    > [!div class="mx-imgBorder"]
    > ![Adicionar permissões de API de aplicativo](./media/how-to-manage-authentication/app-permissions.png)

4. Marque a caixa de seleção ao lado de **acessar mapas do Azure** e, em seguida, selecione **adicionar permissões**.

    > [!div class="mx-imgBorder"]
    > ![Selecionar permissões de API de aplicativo](./media/how-to-manage-authentication/select-app-permissions.png)

5. Conclua as etapas a seguir para criar um segredo do cliente ou configurar o certificado.

    * Se seu aplicativo usar a autenticação de servidor ou aplicativo, em sua página de registro do aplicativo, acesse **certificados & segredos**. Em seguida, carregue um certificado de chave pública ou crie uma senha selecionando **novo segredo do cliente**.

        > [!div class="mx-imgBorder"]
        > ![Criar um segredo do cliente](./media/how-to-manage-authentication/app-keys.png)

    * Depois de selecionar **Adicionar**, copie o segredo e armazene-o com segurança em um serviço, como Azure Key Vault. Examine [Azure Key Vault guia do desenvolvedor](../key-vault/general/developers-guide.md) para armazenar o certificado ou segredo com segurança. Você usará esse segredo para obter tokens do Azure AD.

        > [!div class="mx-imgBorder"]
        > ![Adicionar um segredo do cliente](./media/how-to-manage-authentication/add-key.png)

### <a name="grant-role-based-access-for-the-daemon-application-to-azure-maps"></a>Conceder acesso baseado em função para o aplicativo daemon para mapas do Azure

Conceda o *controle de acesso baseado em função do Azure (RBAC do Azure)* atribuindo a identidade gerenciada criada ou a entidade de serviço a uma ou mais definições de função do Azure Maps. Para exibir as definições de função do Azure que estão disponíveis para mapas do Azure, vá para **controle de acesso (iam)**. Selecione **funções** e, em seguida, procure funções que começam com o *Azure Maps*. Essas funções do Azure Maps são as funções às quais você pode conceder acesso.

> [!div class="mx-imgBorder"]
> ![Visualizar funções disponíveis](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

1. Vá para sua **conta do Azure Maps**. Selecione **Controle de acesso (IAM)** > **Atribuições de função**.

    > [!div class="mx-imgBorder"]
    > ![Conceder acesso usando o RBAC do Azure](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Na guia **atribuições de função** , **adicione** uma atribuição de função. 

    > [!div class="mx-imgBorder"]
    > ![Captura de tela mostra as atribuições de roll com adicionar selecionado.](./media/how-to-manage-authentication/add-role-assignment.png)

3. Selecione uma definição de função interna do Azure Maps, como **leitor de dados do Azure Maps** ou **colaborador de dados do Azure Maps**. Em **atribuir acesso a**, selecione **usuário do Azure AD, grupo ou entidade de serviço** ou identidade gerenciada com identidade gerenciada atribuída ao sistema de **identidade gerenciada atribuída pelo usuário**  /  . Selecione a entidade de segurança. Em seguida, selecione **Salvar**.

    > [!div class="mx-imgBorder"]
    > ![Como adicionar atribuição de função](./media/how-to-manage-authentication/how-to-add-role-assignment.png)

4. Você pode confirmar se a atribuição de função foi aplicada na guia atribuição de função.

## <a name="request-token-with-managed-identity"></a>Solicitar token com identidade gerenciada

Depois que uma identidade gerenciada é configurada para o recurso de hospedagem, use o SDK do Azure ou a API REST para adquirir um token para mapas do Azure, veja detalhes sobre como [adquirir um token de acesso](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md). Seguindo o guia, a expectativa é que um token de acesso será retornado, que pode ser usado em solicitações da API REST.

## <a name="request-token-with-application-registration"></a>Solicitar token com o registro do aplicativo

Depois de registrar seu aplicativo e associá-lo ao Azure Maps, você pode solicitar tokens de acesso.

* ID de recurso do Azure AD `https://atlas.microsoft.com/`
* ID do Aplicativo do Azure AD
* ID de Locatário do Azure AD
* Segredo do cliente de registro de Aplicativo Azure AD

Solicitação:

```http
POST /<Azure AD Tenant ID>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<Azure AD App ID>&resource=https://atlas.microsoft.com/&client_secret=<client secret>&grant_type=client_credentials
```

Resposta:

```json
{
    "token_type": "Bearer",
    "expires_in": "...",
    "ext_expires_in": "...",
    "expires_on": "...",
    "not_before": "...",
    "resource": "https://atlas.microsoft.com/",
    "access_token": "ey...gw"
}
```

Consulte [cenários de autenticação do Azure ad](../active-directory/develop/authentication-vs-authorization.md)para obter exemplos mais detalhados.

## <a name="next-steps"></a>Próximas etapas

Localize as métricas de uso da API para sua conta do Azure Maps:
> [!div class="nextstepaction"]
> [Obter métricas de uso](how-to-view-api-usage.md)

Explore os exemplos que mostram como integrar o Azure AD ao Azure Maps:
> [!div class="nextstepaction"]
> [Exemplos do Azure Maps](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)