---
title: Criar um aplicativo do Azure AD & entidade de serviço no portal
titleSuffix: Microsoft identity platform
description: Crie um novo aplicativo Azure Active Directory & entidade de serviço para gerenciar o acesso a recursos com controle de acesso baseado em função no Azure Resource Manager.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.date: 06/26/2020
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.openlocfilehash: 6adc3a8af90f6f05f640de97b8fa74c8d40e0329
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175526"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Como usar o portal para criar um aplicativo e uma entidade de serviço do Azure AD que possa acessar recursos

Este artigo mostra como criar um novo aplicativo Azure Active Directory (Azure AD) e uma entidade de serviço que pode ser usada com o controle de acesso baseado em função. Quando você tem aplicativos, serviços hospedados ou ferramentas automatizadas que precisam acessar ou modificar recursos, você pode criar uma identidade para o aplicativo. Essa identidade é conhecida como uma entidade de serviço. O acesso aos recursos é restrito pelas funções atribuídas à entidade de serviço, dando a você controle sobre quais recursos podem ser acessados e em qual nível. Por motivos de segurança, é sempre recomendado usar entidades de serviço com ferramentas automatizadas em vez de permitir a entrada com uma identidade de usuário.

Este artigo mostra como usar o portal para criar a entidade de serviço no portal do Azure. Ele se concentra em um aplicativo de locatário único que se destina a ser executado dentro de uma única organização. Você normalmente usa os aplicativos com um único locatário para os aplicativos da linha de negócios executados em sua organização.  Você também pode [usar Azure PowerShell para criar uma entidade de serviço](howto-authenticate-service-principal-powershell.md).

> [!IMPORTANT]
> Em vez de criar uma entidade de serviço, considere o uso de identidades gerenciadas para recursos do Azure para a identidade do aplicativo. Se o seu código for executado em um serviço que dá suporte a identidades gerenciadas e a recursos de acesso que dão suporte à autenticação do Azure AD, as identidades gerenciadas são uma opção melhor para você. Para saber mais sobre identidades gerenciadas dos recursos do Azure, incluindo os serviços atualmente com suporte, consulte [O que são identidades gerenciadas para recursos do Azure?](../managed-identities-azure-resources/overview.md).

## <a name="app-registration-app-objects-and-service-principals"></a>Registro de aplicativo, objetos de aplicativo e entidades de serviço
Não é possível criar uma entidade de serviço diretamente usando o portal do Azure.  Quando você registra um aplicativo por meio do portal do Azure, um objeto de aplicativo e uma entidade de serviço são criados automaticamente em seu diretório base ou locatário.  Para obter mais informações sobre a relação entre o registro do aplicativo, objetos de aplicativo e entidades de serviço, leia [objetos de aplicativo e entidade de serviço no Azure Active Directory](app-objects-and-service-principals.md).

## <a name="permissions-required-for-registering-an-app"></a>Permissões necessárias para registrar um aplicativo

Você deve ter permissões suficientes para registrar um aplicativo com seu locatário do Azure AD e atribuir ao aplicativo uma função em sua assinatura do Azure.

### <a name="check-azure-ad-permissions"></a>Verifique as permissões do Azure AD

1. Selecione **Azure Active Directory**.
1. Anote sua função. Se você tiver a função **Usuário**, garanta que não administradores possam registrar aplicativos.

   ![Localize sua função. Se você for um usuário, verifique se os não-administradores podem registrar aplicativos](./media/howto-create-service-principal-portal/view-user-info.png)

1. No painel esquerdo, selecione **configurações de usuário**.
1. Verifique a configuração **Registros do Aplicativo**. Esse valor só pode ser definido por um administrador. Se for definido como **Sim**, qualquer usuário no locatário do Azure AD poderá registrar um aplicativo.

Se a configuração de registros de aplicativo está definida como **Não**, somente os usuários com uma função de administrador podem registrar esses tipos de aplicativos. Consulte [funções internas do Azure ad](../roles/permissions-reference.md#all-roles) para saber mais sobre as funções de administrador disponíveis e as permissões específicas no Azure AD que são dadas a cada função. Se sua conta for atribuída à função de usuário, mas a configuração de registro do aplicativo for limitada a usuários administradores, peça ao administrador para atribuir a você uma das funções de administrador que podem criar e gerenciar todos os aspectos de registros do aplicativo ou para permitir que os usuários registrem aplicativos.

### <a name="check-azure-subscription-permissions"></a>Verificar permissões de assinatura do Azure

Em sua assinatura do Azure, sua conta deve ter `Microsoft.Authorization/*/Write` acesso para atribuir uma função a um aplicativo do AD. Esta ação deve ser concedida pela função [Proprietário](../../role-based-access-control/built-in-roles.md#owner) ou pela função [Administrador de Acesso do Usuário](../../role-based-access-control/built-in-roles.md#user-access-administrator). Se sua conta for atribuída à função **colaborador** , você não terá a permissão adequada. Você receberá um erro ao tentar atribuir a entidade de serviço a uma função.

Para verificar suas permissões de assinatura:

1. Pesquise e selecione **assinaturas**, ou selecione **assinaturas** na **Home** Page do.

   ![Search](./media/howto-create-service-principal-portal/select-subscription.png)

1. Selecione a assinatura na qual você deseja criar a entidade de serviço.

   ![Selecione a assinatura para atribuição](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Se você não vir a assinatura que está procurando, selecione **filtro de assinaturas globais**. Garanta que a assinatura desejada seja selecionada para o portal.

1. Selecionar **Minhas permissões**. Em seguida, selecione **Clique aqui para exibir o acesso completo detalhes para essa assinatura**.

   ![Selecione a assinatura na qual você deseja criar a entidade de serviço](./media/howto-create-service-principal-portal/view-details.png)

1. Selecione **Exibir** nas **atribuições de função** para exibir suas funções atribuídas e determine se você tem permissões adequadas para atribuir uma função a um aplicativo do AD. Caso contrário, peça ao administrador da assinatura para adicioná-lo à função Administrador de Acesso do Usuário. Na imagem a seguir, o usuário recebe a função proprietário, o que significa que o usuário tem as permissões adequadas.

   ![Este exemplo mostra ao usuário a função de proprietário atribuída](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="register-an-application-with-azure-ad-and-create-a-service-principal"></a>Registrar um aplicativo com o Azure AD e criar uma entidade de serviço

Vamos diretamente para a criação da identidade. Se você encontrar um problema, verifique as [permissões necessárias](#permissions-required-for-registering-an-app) para garantir que sua conta possa criar a identidade.

1. Entre sua conta do Azure através do <a href="https://portal.azure.com/" target="_blank">portal do Microsoft Azure</a>.
1. Selecione **Azure Active Directory**.
1. Selecione **Registros do Aplicativo**.
1. Selecione **Novo registro**.
1. Nomeie o aplicativo. Selecione um tipo de conta com suporte, que determina quem pode usar o aplicativo. Em **URI de redirecionamento**, selecione **Web** para o tipo de aplicativo que você deseja criar. Insira o URI para o qual o token de acesso é enviado. Não é possível criar as credenciais para um [Aplicativo nativo](../manage-apps/application-proxy-configure-native-client-application.md). Não é possível usar esse tipo para um aplicativo automatizado. Depois de definir os valores, selecione **registrar**.

   ![Digite um nome para seu aplicativo](./media/howto-create-service-principal-portal/create-app.png)

Você criou o aplicativo e a entidade de serviço do Azure AD.

> [!NOTE]
> Você pode registrar vários aplicativos com o mesmo nome no Azure AD, mas os aplicativos devem ter IDs de aplicativo (cliente) diferentes.

## <a name="assign-a-role-to-the-application"></a>Atribuir uma função ao aplicativo

Para acessar recursos em sua assinatura, você deve atribuir uma função ao aplicativo. Decida qual função oferece as permissões corretas para o aplicativo. Para saber mais sobre as funções disponíveis, confira [funções internas do Azure](../../role-based-access-control/built-in-roles.md).

Você pode definir o escopo no nível da assinatura, do grupo de recursos ou do recurso. As permissão são herdadas para níveis inferiores do escopo. Por exemplo, adicionar um aplicativo à função *leitor* para um grupo de recursos significa que ele pode ler o grupo de recursos e todos os recursos que ele contém.

1. Na portal do Azure, selecione o nível de escopo ao qual você deseja atribuir o aplicativo. Por exemplo, para atribuir uma função no escopo da assinatura, procure e selecione **assinaturas**, ou selecione **assinaturas** na **Home** Page.

   ![Por exemplo, atribua uma função no escopo da assinatura](./media/howto-create-service-principal-portal/select-subscription.png)

1. Escolha a assinatura específica à qual atribuir o aplicativo.

   ![Selecione a assinatura para atribuição](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Se você não vir a assinatura que está procurando, selecione **filtro de assinaturas globais**. Garanta que a assinatura desejada seja selecionada para o portal.

1. Selecione **IAM (Controle de acesso)** .
1. Selecione **Adicionar atribuição de função**.
1. Selecione a função que deseja atribuir ao aplicativo. Por exemplo, para permitir que o aplicativo execute ações como **reinicializar**, **Iniciar** e **parar** instâncias, selecione a função **colaborador** .  Leia mais sobre as [funções disponíveis](../../role-based-access-control/built-in-roles.md) por padrão, os aplicativos do Azure ad não são exibidos nas opções disponíveis. Para localizar seu aplicativo, pesquise o nome e selecione-o.

   ![Selecione a função a ser atribuída ao aplicativo](./media/howto-create-service-principal-portal/select-role.png)

1. Selecione **Salvar** para finalizar a atribuição da função. Você vê seu aplicativo na lista de usuários com uma função para esse escopo.

A entidade de serviço está configurada. Você pode começar a usá-lo para executar seus scripts ou aplicativos. Para gerenciar sua entidade de serviço (permissões, permissões de usuário consentidas, consulte quais usuários consentiram, revise as permissões, consulte informações de entrada e muito mais), vá para **aplicativos empresariais**.

A próxima seção mostra como obter valores necessários ao entrar de modo programático.

## <a name="get-tenant-and-app-id-values-for-signing-in"></a>Obter valores de ID do aplicativo e locatário para entrar

Ao entrar de forma programática, passe a ID de locatário com sua solicitação de autenticação e a ID do aplicativo.  Você também precisa de um certificado ou de uma chave de autenticação (descrita na seção a seguir). Para obter esses valores, use as seguintes etapas:

1. Selecione **Azure Active Directory**.
1. Em **Registros de aplicativo** no Azure AD, selecione seu aplicativo.
1. Copie a ID do diretório (locatário) e armazene-a no código do aplicativo.

    ![Copiar o diretório (ID do locatário) e armazene-o no código do aplicativo](./media/howto-create-service-principal-portal/copy-tenant-id.png)

    A ID do diretório (locatário) também pode ser encontrada na página Visão geral do diretório padrão.

1. Copie a **ID do aplicativo** e armazene-a no código do aplicativo.

   ![Copiar a ID (de cliente) do aplicativo](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="authentication-two-options"></a>Autenticação: duas opções

Há dois tipos de autenticação disponíveis para entidades de serviço: autenticação baseada em senha (segredo do aplicativo) e autenticação baseada em certificado. *É recomendável usar um certificado*, mas você também pode criar um segredo do aplicativo.

### <a name="option-1-upload-a-certificate"></a>Opção 1: carregar um certificado

Você pode usar um certificado existente se tiver um.  Opcionalmente, você pode criar um certificado autoassinado somente para *fins de teste*. Para criar um certificado autoassinado, abra o PowerShell e execute [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) com os seguintes parâmetros para criar o certificado no repositório de certificados do usuário em seu computador:

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Exporte esse certificado para um arquivo usando o snap-in [gerenciar certificado do usuário](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) do MMC acessível no painel de controle do Windows.

1. Selecione **executar** no menu **Iniciar** e digite **certmgr. msc**.

   A ferramenta Gerenciador de certificados para o usuário atual é exibida.

1. Para exibir seus certificados, em **certificados – usuário atual** no painel esquerdo, expanda o diretório **pessoal** .
1. Clique com o botão direito do mouse no certificado que você criou, selecione **todas as tarefas->exportar**.
1. Siga o assistente para exportação de certificados.  Não exporte a chave privada e exporte para um. Arquivo CER.

Para carregar o certificado:

1. Selecione **Azure Active Directory**.
1. Em **Registros de aplicativo** no Azure AD, selecione seu aplicativo.
1. Selecione **Certificados e segredos**.
1. Selecione **carregar certificado** e selecione o certificado (um certificado existente ou o certificado autoassinado que você exportou).

    ![Selecione carregar certificado e selecione aquele que você deseja adicionar](./media/howto-create-service-principal-portal/upload-cert.png)

1. Selecione **Adicionar**.

Depois de registrar o certificado com seu aplicativo no portal de registro de aplicativos, habilite o código do aplicativo cliente para usar o certificado.

### <a name="option-2-create-a-new-application-secret"></a>Opção 2: criar um novo segredo do aplicativo

Se você optar por não usar um certificado, poderá criar um novo segredo do aplicativo.

1. Selecione **Azure Active Directory**.
1. Em **Registros de aplicativo** no Azure AD, selecione seu aplicativo.
1. Selecione **Certificados e segredos**.
1. Selecione **Segredos do cliente > Novo segredo do cliente**.
1. Forneça uma descrição do segredo e uma duração. Ao terminar, selecione **Adicionar**.

   Depois de salvar o segredo do cliente, o valor do segredo do cliente é exibido. Copie esse valor porque você não poderá recuperar a chave posteriormente. Você fornecerá o valor da chave com a ID do aplicativo para entrar como o aplicativo. Armazene o valor da chave onde seu aplicativo possa recuperá-lo.

   ![Copiar o valor do segredo porque você não pode recuperá-lo mais tarde](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>Configurar políticas de acesso em recursos
Tenha em mente que talvez seja necessário configurar permissões adicionais em recursos que seu aplicativo precisa acessar. Por exemplo, você também deve [atualizar as políticas de acesso de um cofre de chaves](../../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) para dar ao aplicativo acesso a chaves, segredos ou certificados.

1. No <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>, navegue até o cofre de chaves e selecione **políticas de acesso**.
1. Selecione **Adicionar política de acesso** e, em seguida, selecione as permissões de chave, segredo e certificado que você deseja conceder ao seu aplicativo.  Selecione a entidade de serviço que você criou anteriormente.
1. Selecione **Adicionar** para adicionar a política de acesso e, em seguida, **salvar** para confirmar suas alterações.
    ![Adicionar política de acesso](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="next-steps"></a>Próximas etapas
* Saiba como [usar Azure PowerShell para criar uma entidade de serviço](howto-authenticate-service-principal-powershell.md).
* Para saber mais sobre como especificar políticas de segurança, consulte [controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/role-assignments-portal.md).
* Para obter uma lista de ações disponíveis que podem ser concedidas ou negadas a usuários, consulte [Operações do Provedor de Recursos do Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md).
* Para obter informações sobre como trabalhar com registros de aplicativo usando **Microsoft Graph**, consulte a referência de API de [aplicativos](/graph/api/resources/application) .
