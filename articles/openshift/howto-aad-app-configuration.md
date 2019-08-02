---
title: Integração do Active Directory do Azure para Azure Red Hat OpenShift | Microsoft Docs
description: Saiba como criar um grupo de segurança do Azure AD e o usuário para testar aplicativos em seu cluster do Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
manager: jeconnoc
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/13/2019
ms.openlocfilehash: 00609905d09f8d414660c21805c6efca5eb30843
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67669399"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Integração do Active Directory do Azure para Azure Red Hat OpenShift

Se você ainda não criou um locatário do Azure Active Directory (Azure AD), siga as instruções em [criar um locatário do AD do Azure para Azure Red Hat OpenShift](howto-create-tenant.md) antes de continuar com estas instruções.

Microsoft Azure Red Hat OpenShift precisa de permissões para executar tarefas em nome do seu cluster. Se sua organização ainda não tiver um usuário do AD do Azure, grupo de segurança do Azure AD ou um registro de aplicativo do Azure AD para usar como a entidade de serviço, siga estas instruções para criá-los.

## <a name="create-a-new-azure-active-directory-user"></a>Criar um novo usuário do Azure Active Directory

No [portal do Azure](https://portal.azure.com), certifique-se de que seu locatário aparece em seu nome de usuário no canto superior direito do portal:

![Captura de tela do portal de locatário listado no canto superior direito](./media/howto-create-tenant/tenant-callout.png) se o locatário errado for exibido, clique em seu nome de usuário no canto superior direito, clique **mudar diretório**e selecione o locatário correto do **todos os Diretórios** lista.

Crie um novo usuário de administrador global do Active Directory do Azure para entrar no seu cluster do Azure Red Hat OpenShift.

1. Vá para o [os usuários de todos os usuários](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) folha.
2. Clique em **+ novo usuário** para abrir o **usuário** painel.
3. Insira um **nome** para este usuário.
4. Criar uma **nome de usuário** com base no nome do locatário que você criou, com `.onmicrosoft.com` acrescentado ao final. Por exemplo, `yourUserName@yourTenantName.onmicrosoft.com`. Anote esse nome de usuário. Você precisará dela para entrar no seu cluster.
5. Clique em **função de diretório** para abrir o painel de função de diretório e selecione **Administrador Global** e, em seguida, clique em **Okey** na parte inferior do painel.
6. No **usuário** painel, clique em **Mostrar senha** e registre a senha temporária. Depois de entrar pela primeira vez, você será solicitado para redefini-lo.
7. Na parte inferior do painel, clique em **criar** para criar o usuário.

## <a name="create-an-azure-ad-security-group"></a>Criar um grupo de segurança do Azure AD

Para conceder acesso de administrador de cluster, as associações em um grupo de segurança do Azure AD são sincronizadas para os OpenShift "osa-cliente-os administradores do grupo". Se não especificado, sem acesso de administrador de cluster será concedido.

1. Abra o [grupos do Active Directory do Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) folha.
2. Clique em **+ novo grupo**.
3. Forneça um nome de grupo e uma descrição.
4. Definir **tipo de grupo** à **segurança**.
5. Definir **tipo de associação** à **atribuído**.

    Adicione o usuário do AD do Azure que você criou na etapa anterior para esse grupo de segurança.

6. Clique em **membros** para abrir o **selecionar membros** painel.
7. Na lista de membros, selecione o usuário do AD do Azure que você criou acima.
8. Na parte inferior do portal, clique em **selecionar** e, em seguida **criar** para criar o grupo de segurança.

    Anote o valor da ID de grupo.

9. Quando o grupo é criado, você verá ele na lista de todos os grupos. Clique no novo grupo.
10. Na página que aparece, anote o **ID do objeto**. Vamos nos referir a esse valor como `GROUPID` no [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) tutorial.

## <a name="create-an-azure-ad-app-registration"></a>Criar um registro de aplicativo do Azure AD

Você pode criar automaticamente um cliente de registro de aplicativo do Azure Active Directory (Azure AD) como parte da criação do cluster, omitindo o `--aad-client-app-id` sinalizar para o `az openshift create` comando. Este tutorial mostra como criar o registro do aplicativo do Azure AD para fins de integridade.

Se sua organização ainda não tiver um registro de aplicativo do Azure Active Directory (Azure AD) a ser usado como uma entidade de serviço, siga estas instruções para criar um.

1. Abra o [folha registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) e clique em **+ novo registro**.
2. No **registrar um aplicativo** painel, insira um nome para seu registro de aplicativo.
3. Certifique-se de que, em **suporte para tipos de conta** que **contas neste diretório organizacional apenas** está selecionado. Essa é a opção mais segura.
4. Adicionaremos um URI de redirecionamento mais tarde quando soubermos o URI do cluster. Clique o **registrar** botão para criar o registro de aplicativo do Azure AD.
5. Na página que aparece, anote o **ID do aplicativo (cliente)** . Vamos nos referir a esse valor como `APPID` no [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) tutorial.

![Captura de tela da página de objeto de aplicativo](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>Criar um segredo do cliente

Gere um segredo do cliente para autenticar seu aplicativo ao Azure Active Directory.

1. No **Manage** seção da página de registros do aplicativo, clique em **certificados e segredos**.
2. Sobre o **certificados e segredos** painel, clique em **+ novo segredo do cliente**.  O **adicionar um segredo do cliente** painel será exibido.
3. Fornecer um **descrição**.
4. Definir **Expires** para a duração que você preferir, por exemplo **em 2 anos**.
5. Clique em **Add** e o valor da chave será exibido na **segredos de cliente** seção da página.
6. Copie o valor da chave. Vamos nos referir a esse valor como `SECRET` no [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) tutorial.

![Captura de tela do painel de certificados e segredos](./media/howto-create-tenant/create-key.png)

Para obter mais informações sobre objetos de aplicativo do Azure, consulte [aplicativo e objetos de entidade de serviço no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

Para obter detalhes sobre como criar um novo aplicativo do Azure AD, consulte [registrar um aplicativo com o ponto de extremidade do Active Directory do Azure v1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

## <a name="add-api-permissions"></a>Adicionar permissões de API

1. No **Manage** seção clique **permissões de API**.
2. Clique em **adicionar a permissão** e selecione **Azure Active Directory Graph** , em seguida, **permissões delegadas**
3. Expandir **usuário** na lista abaixo e verifique se **Read** está habilitado.
4. Role para cima e selecione **permissões de aplicativo**.
5. Expandir **diretório** na lista abaixo e habilitar **Directory.ReadAll**
6. Clique em **adicionar permissões** para aceitar as alterações.
7. O painel de permissões de API agora deve mostrar ambos *Read* e *Directory.ReadAll*. Observe o aviso na **consentimento do administrador necessário** coluna lado *Directory.ReadAll*.
8. Se você for o *administrador da assinatura do Azure*, clique em **conceder consentimento do administrador para *nome da assinatura***  abaixo. Se você não o *administrador da assinatura do Azure*, solicitar o consentimento do administrador.
![Captura de tela do painel de permissões de API. Permissões Read e Directory.ReadAll adicionadas, o consentimento do administrador necessária para Directory.ReadAll](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> Sincronização do grupo de administradores de cluster funcionará apenas depois que recebeu consentimento. Você verá um círculo verde com uma marca de seleção e uma mensagem de "concedido para *o nome da assinatura*" no *consentimento do administrador necessário* coluna.

Para obter detalhes sobre como gerenciar administradores e outras funções, consulte [adicionar ou alterar os administradores de assinatura do Azure](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator).

## <a name="resources"></a>Recursos

* [Aplicativos e objetos de entidade de serviço no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
* [Início Rápido: Registrar um aplicativo com o ponto de extremidade do Azure Active Directory v 1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="next-steps"></a>Próximas etapas

Se você cumpriu todos os [pré-requisitos do Azure Red Hat OpenShift](howto-setup-environment.md), você estará pronto para criar seu primeiro cluster!

Experimente o tutorial:
> [!div class="nextstepaction"]
> [Criar um cluster do Red Hat OpenShift no Azure](tutorial-create-cluster.md)
