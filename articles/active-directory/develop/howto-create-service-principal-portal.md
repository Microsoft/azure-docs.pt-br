---
title: Criar identidade para o aplicativo do Azure no portal | Microsoft Docs
description: Descreve como criar um novo aplicativo do Azure Active Directory e uma nova entidade de serviço, que possam ser usados com o controle de acesso baseado em função no Azure Resource Manager para gerenciar o acesso aos recursos.
services: active-directory
documentationcenter: na
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2019
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 825966fbb0db537aad8de39e69e17418e6432b44
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324672"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Como: Usar o portal para criar um aplicativo e uma entidade de serviço do Microsoft Azure Active Directory que possa acessar recursos

Este artigo mostra como criar um novo aplicativo Azure Active Directory (Azure AD) e uma entidade de serviço que pode ser usada com o controle de acesso baseado em função. Se você tiver código que precise acessar ou modificar recursos, crie uma identidade para o aplicativo. Essa identidade é conhecida como uma entidade de serviço. Desse modo, é possível atribuir as permissões necessárias à entidade de serviço. Este artigo mostra como usar o portal para criar a entidade de serviço. Ele se concentra em um aplicativo de locatário único que se destina a ser executado dentro de uma única organização. Você normalmente usa os aplicativos com um único locatário para os aplicativos da linha de negócios executados em sua organização.

> [!IMPORTANT]
> Em vez de criar uma entidade de serviço, considere o uso de identidades gerenciadas para recursos do Azure para a identidade do aplicativo. Se o seu código for executado em um serviço que dá suporte a identidades gerenciadas e a recursos de acesso que dão suporte à autenticação do Azure AD, as identidades gerenciadas são uma opção melhor para você. Para saber mais sobre identidades gerenciadas dos recursos do Azure, incluindo os serviços atualmente com suporte, consulte [O que são identidades gerenciadas para recursos do Azure?](../managed-identities-azure-resources/overview.md).

## <a name="create-an-azure-active-directory-application"></a>Criar um aplicativo do Azure Active Directory

Vamos diretamente para a criação da identidade. Se você encontrar um problema, verifique as [permissões necessárias](#required-permissions) para garantir que sua conta possa criar a identidade.

1. Entre sua conta do Azure através do [portal do Microsoft Azure](https://portal.azure.com).
1. Selecione **Azure Active Directory**.
1. Selecione **Registros do Aplicativo**.
1. Selecione **Novo registro**.
1. Nomeie o aplicativo. Selecione um tipo de conta com suporte, que determina quem pode usar o aplicativo. Em **URI**de redirecionamento, selecione **Web** para o tipo de aplicativo que você deseja criar. Insira o URI para o qual o token de acesso é enviado. Não é possível criar as credenciais para um [Aplicativo nativo](../manage-apps/application-proxy-configure-native-client-application.md). Não é possível usar esse tipo para um aplicativo automatizado. Depois de definir os valores, selecione **registrar**.

   ![Digite um nome para seu aplicativo](./media/howto-create-service-principal-portal/create-app.png)

Você criou o aplicativo e a entidade de serviço do Azure AD.

## <a name="assign-the-application-to-a-role"></a>Atribuir o aplicativo a uma função

Para acessar recursos em sua assinatura, você deve atribuir o aplicativo a uma função. Decida qual função oferece as permissões corretas para o aplicativo. Para saber mais sobre as funções disponíveis, consulte [RBAC: Funções internas](../../role-based-access-control/built-in-roles.md).

Você pode definir o escopo no nível da assinatura, do grupo de recursos ou do recurso. As permissão são herdadas para níveis inferiores do escopo. Por exemplo, adicionar um aplicativo à função Leitor de um grupo de recursos significa que ele pode ler o grupo de recursos e todos os recursos que ele contiver.

1. Navegue até o nível do escopo ao qual quer atribuir o aplicativo. Por exemplo, para atribuir uma função no escopo da assinatura, escolha **Todos os serviços** e **Assinaturas**.

   ![Por exemplo, atribua uma função no escopo da assinatura](./media/howto-create-service-principal-portal/select-subscription.png)

1. Escolha a assinatura específica à qual atribuir o aplicativo.

   ![Selecione a assinatura para atribuição](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Se você não vir a assinatura que está procurando, selecione **filtro de assinaturas globais**. Garanta que a assinatura desejada seja selecionada para o portal.

1. Selecione **IAM (Controle de acesso)** .
1. Selecione **Adicionar atribuição de função**.
1. Selecione a função que deseja atribuir ao aplicativo. Para permitir que o aplicativo execute ações como **reinicializar**, **iniciar** e **parar** instâncias, selecione a função de **Colaborador**. Por padrão, os aplicativos do Azure AD não são exibidos nas opções disponíveis. Para localizar seu aplicativo, pesquise o nome e selecione-o.

   ![Selecione a função a ser atribuída ao aplicativo](./media/howto-create-service-principal-portal/select-role.png)

1. Selecione **Salvar** para finalizar a atribuição da função. Agora você vê o aplicativo na lista de usuários atribuídos a uma função para esse escopo.

A entidade de serviço está configurada. Você pode começar a usá-lo para executar seus scripts ou aplicativos. A próxima seção mostra como obter valores necessários ao entrar de modo programático.

## <a name="get-values-for-signing-in"></a>Obter valores para entrar

Ao entrar de modo programático, você precisa passar a ID de locatário com a solicitação de autenticação. Você também precisa da ID para seu aplicativo e de uma chave de autenticação. Para obter esses valores, use as seguintes etapas:

1. Selecione **Azure Active Directory**.
1. Em **Registros de aplicativo** no Azure AD, selecione seu aplicativo.
1. Copie a ID do diretório (locatário) e armazene-a no código do aplicativo.

    ![Copie o diretório (ID do locatário) e armazene-o no código do aplicativo](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. Copie a **ID do aplicativo** e armazene-a no código do aplicativo.

   ![Copiar a ID do aplicativo (cliente)](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>Certificados e segredos
Os aplicativos daemon podem usar duas formas de credenciais para autenticar com o Azure AD: certificados e segredos do aplicativo.  É recomendável usar um certificado, mas você também pode criar um novo segredo do aplicativo.

### <a name="upload-a-certificate"></a>Carregar um certificado

Você pode usar um certificado existente se tiver um.  Opcionalmente, você pode criar um certificado autoassinado para fins de teste. Abra o PowerShell e execute [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) com os seguintes parâmetros para criar um certificado autoassinado no repositório de certificados do usuário em seu computador `$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature`:.  Exporte esse certificado usando o snap-in [gerenciar certificado do usuário](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) do MMC acessível no painel de controle do Windows.

Para carregar o certificado:

1. Selecione **certificados & segredos**.
1. Selecione **carregar certificado** e selecione o certificado (um certificado existente ou o certificado autoassinado que você exportou).

    ![Selecione carregar certificado e selecione aquele que você deseja adicionar](./media/howto-create-service-principal-portal/upload-cert.png)

1. Selecione **Adicionar**.

Depois de registrar o certificado com seu aplicativo no portal de registro de aplicativos, você precisa habilitar o código do aplicativo cliente para usar o certificado.

### <a name="create-a-new-application-secret"></a>Criar um novo segredo do aplicativo

Se você optar por não usar um certificado, poderá criar um novo segredo do aplicativo.

1. Selecione **certificados & segredos**.
1. Selecione os **segredos do cliente-> novo segredo do cliente**.
1. Forneça uma descrição do segredo e uma duração. Quando terminar, selecione **Adicionar**.

   Depois de salvar o segredo do cliente, o valor do segredo do cliente é exibido. Copie este valor, pois não é possível recuperar a chave posteriormente. Forneça o valor da chave com a ID do aplicativo para fazer logon como o aplicativo. Armazene o valor da chave onde seu aplicativo possa recuperá-lo.

   ![Copiar o valor secreto porque você não pode recuperá-lo mais tarde](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="required-permissions"></a>Permissões necessárias

Você deve ter permissões suficientes para registrar um aplicativo com o locatário do Azure AD e atribuir o aplicativo a uma função em sua assinatura do Azure.

### <a name="check-azure-ad-permissions"></a>Verifique as permissões do Azure AD

1. Selecione **Azure Active Directory**.
1. Anote sua função. Se você tiver a função **Usuário**, garanta que não administradores possam registrar aplicativos.

   ![Localize sua função. Se você for um usuário, verifique se os não-administradores podem registrar aplicativos](./media/howto-create-service-principal-portal/view-user-info.png)

1. Selecione **Configurações de usuário**.
1. Verifique a configuração **Registros do Aplicativo**. Esse valor só pode ser definido por um administrador. Se for definido como **Sim**, qualquer usuário no locatário do Azure AD poderá registrar um aplicativo.

Se a configuração de registros de aplicativo está definida como **Não**, somente os usuários com uma função de administrador podem registrar esses tipos de aplicativos. Confira as [funções disponíveis](../users-groups-roles/directory-assign-admin-roles.md#available-roles) e as [permissões de função](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) para saber mais sobre as funções de administrador disponíveis e as permissões específicas no Azure AD fornecidas para cada função. Se sua conta está atribuída à função Usuário, mas a configuração de registro de aplicativo está limitada a usuários administradores, peça a seu administrador para atribuir a você uma das funções de administrador que podem criar e gerenciar todos os aspectos de registros do aplicativo ou para permitir que os usuários registrem aplicativos.

### <a name="check-azure-subscription-permissions"></a>Verificar permissões de assinatura do Azure

Em sua assinatura do Azure, sua conta deve ter acesso de `Microsoft.Authorization/*/Write` para atribuir um aplicativo do AD a uma função. Esta ação deve ser concedida pela função [Proprietário](../../role-based-access-control/built-in-roles.md#owner) ou pela função [Administrador de Acesso do Usuário](../../role-based-access-control/built-in-roles.md#user-access-administrator). Se sua conta tiver a função **Colaborador**, você não terá a permissão adequada. Você verá um erro ao tentar atribuir a entidade de serviço a uma função.

Para verificar suas permissões de assinatura:

1. Selecione sua conta no canto superior direito e selecione **...-> minhas permissões**.

   ![Selecione sua conta e suas permissões de usuário](./media/howto-create-service-principal-portal/select-my-permissions.png)

1. Na lista suspensa, selecione a assinatura em que você deseja criar o serviço principal. Em seguida, selecione **Clique aqui para exibir o acesso completo detalhes para essa assinatura**.

   ![Selecione a assinatura na qual você deseja criar a entidade de serviço](./media/howto-create-service-principal-portal/view-details.png)

1. Selecione **atribuições de função** para exibir suas funções atribuídas e determine se você tem permissões adequadas para atribuir um aplicativo do AD a uma função. Caso contrário, peça ao administrador da assinatura para adicioná-lo à função Administrador de Acesso do Usuário. Na imagem a seguir, o usuário é atribuído à função Proprietário, o que significa que o usuário tem as permissões adequadas.

   ![Este exemplo mostra que o usuário está atribuído à função de proprietário](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>Próximas etapas

* Para configurar um aplicativo multilocatário, consulte [Guia do desenvolvedor para a autorização com a API do Azure Resource Manager](../../azure-resource-manager/resource-manager-api-authentication.md).
* Para aprender a especificar as políticas de segurança, consulte [Controle de Acesso baseado nas Funções do Azure](../../role-based-access-control/role-assignments-portal.md).  
* Para obter uma lista de ações disponíveis que podem ser concedidas ou negadas a usuários, consulte [Operações do Provedor de Recursos do Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md).
