---
title: Tutorial – Adicionar a autenticação a um aplicativo Web no Serviço de Aplicativo do Azure | Azure
description: Neste tutorial, você aprenderá a habilitar a autenticação e a autorização para um aplicativo Web em execução no Serviço de Aplicativo do Azure. Limitar o acesso ao aplicativo Web para os usuários na sua organização.
services: active-directory, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: a8bd2ef1348692bf57f7e5cb7b6606cfcfd324fe
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905563"
---
# <a name="tutorial-add-authentication-to-your-web-app-running-on-azure-app-service"></a>Tutorial: Adicionar a autenticação ao seu aplicativo Web em execução no Serviço de Aplicativo do Azure

Saiba como habilitar a autenticação para seu aplicativo Web em execução no Serviço de Aplicativo do Azure e limitar o acesso a usuários na sua organização.

:::image type="content" source="./media/scenario-secure-app-authentication-app-service/web-app-sign-in.svg" alt-text="Diagrama que mostra a entrada do usuário." border="false":::

O Serviço de Aplicativo fornece suporte interno à autenticação e à autorização, de modo que você possa conectar usuários e acessar dados, gravando pouco ou nenhum código no aplicativo Web. O uso do módulo de autenticação/autorização do Serviço de Aplicativo não é necessário, mas ajuda a simplificar a autenticação e a autorização para o aplicativo. Este artigo mostra como proteger seu aplicativo Web com o módulo de autenticação/autorização do Serviço de Aplicativo usando o Azure AD (Azure Active Directory) como o provedor de identidade.

O módulo de autenticação/autorização é habilitado e configurado por meio do portal do Azure e das configurações do aplicativo. Não há necessidade de SDKs, idiomas específicos ou alterações no código do aplicativo. Há suporte para uma variedade de provedores de identidade, que incluem o Azure AD, a conta Microsoft, o Facebook, o Google e o Twitter. Quando o módulo de autorização/autenticação está habilitado, toda solicitação HTTP de entrada passa por ele antes de ser manipulada pelo código do aplicativo. Para obter mais informações, confira [Autenticação e autorização no Serviço de Aplicativo do Azure](overview-authentication-authorization.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Configurar a autenticação para o aplicativo Web.
> * Limitar o acesso ao aplicativo Web para os usuários na sua organização.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-publish-a-web-app-on-app-service"></a>Criar e publicar um aplicativo Web no Serviço de Aplicativo

Para este tutorial, você precisará ter um aplicativo Web implantado no Serviço de Aplicativo. Você pode usar um aplicativo Web existente ou pode seguir o [Início Rápido do ASP.NET Core](quickstart-dotnetcore.md) para criar e publicar um aplicativo Web no Serviço de Aplicativo.

Independentemente de você criar um aplicativo Web ou usar um existente, anote o nome do aplicativo Web e o nome do grupo de recursos no qual o aplicativo Web é implantado. Você precisará desses nomes ao longo deste tutorial. Ao longo deste tutorial, os nomes de exemplo em procedimentos e capturas de tela contêm *SecureWebApp*.

## <a name="configure-authentication-and-authorization"></a>Configurar a autenticação e a autorização

Agora você tem um aplicativo Web em execução no Serviço de Aplicativo. Em seguida, habilite a autenticação e autorização para o aplicativo Web. Você usará o Azure AD como o provedor de identidade. Para obter mais informações, confira [Configurar a autenticação do Azure AD para o aplicativo do Serviço de Aplicativo](configure-authentication-provider-aad.md).

No menu do [portal do Azure](https://portal.azure.com), selecione **Grupos de recursos** ou pesquise e selecione **Grupos de recursos** em qualquer página.

Clique em **grupos de recursos** e selecione o grupo de recursos. Em **Visão geral**, selecione a página de gerenciamento do aplicativo.

:::image type="content" alt-text="Captura de tela que mostra a seleção da página de gerenciamento do aplicativo." source="./media/scenario-secure-app-authentication-app-service/select-app-service.png":::

No menu esquerdo do aplicativo, selecione **Autenticação/Autorização** e habilite a Autenticação do Serviço de Aplicativo selecionando **Ativado**.

Em **Ação a tomar quando a solicitação não está autenticada**, selecione **Entrar com o Azure Active Directory**.

Em **Provedores de Autenticação**, selecione **Azure Active Directory**. Selecione **Expresso** e aceite as configurações padrão para criar um aplicativo do Active Directory. Selecione **OK**.

:::image type="content" alt-text="Captura de tela que mostra a autenticação Expresso." source="./media/scenario-secure-app-authentication-app-service/configure-authentication.png":::

Na página **Autenticação/Autorização**, selecione **Salvar**.

Quando você vir a notificação com a mensagem `Successfully saved the Auth Settings for <app-name> App`, atualize a página do portal.

Agora você tem um aplicativo protegido pela autenticação e a autorização do Serviço de Aplicativo.

## <a name="verify-limited-access-to-the-web-app"></a>Verificar o acesso limitado ao aplicativo Web

Quando você habilitou o módulo de autenticação/autorização do Serviço de Aplicativo, um registro de aplicativo foi criado no locatário do Azure AD. O registro do aplicativo tem o mesmo nome de exibição que o aplicativo Web. Para verificar as configurações, selecione **Azure Active Directory** no menu do portal e selecione **Registros de aplicativo**. Selecione o registro do aplicativo criado anteriormente. Na visão geral, verifique se a opção **Tipos de conta compatíveis** está definida como **Somente para minha organização**.

:::image type="content" alt-text="Captura de tela que mostra a verificação do acesso." source="./media/scenario-secure-app-authentication-app-service/verify-access.png":::

Para verificar se o acesso ao seu aplicativo está limitado aos usuários da sua organização, inicie um navegador no modo anônimo ou particular e acesse `https://<app-name>.azurewebsites.net`. Você deverá ser direcionado para uma página de entrada segura, confirmando que os usuários não autenticados não têm permissão de acesso ao site. Entre como um usuário em sua organização para obter acesso ao site. Você também pode iniciar um novo navegador e tentar entrar usando uma conta pessoal para confirmar se os usuários fora da organização não têm acesso.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você concluiu este tutorial e não precisa mais do aplicativo Web nem dos recursos associados, [limpe os recursos que você criou](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
>
> * Configurar a autenticação para o aplicativo Web.
> * Limitar o acesso ao aplicativo Web para os usuários na sua organização.

> [!div class="nextstepaction"]
> [O serviço de aplicativo acessa o armazenamento](scenario-secure-app-access-storage.md)
