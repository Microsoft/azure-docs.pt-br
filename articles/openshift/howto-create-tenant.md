---
title: Criar um locatário do Azure AD para o Azure Red Hat OpenShift
description: Veja como criar um locatário Azure Active Directory (Azure AD) para hospedar seu cluster Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: b98f02adeb850f16127658c7d02d44754512e216
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635002"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Criar um locatário do Azure AD para o Azure Red Hat OpenShift

> [!IMPORTANT]
> O Azure Red Hat OpenShift 3,11 será desativado em 30 de junho de 2022. O suporte para a criação de novos clusters do Azure Red Hat OpenShift 3,11 continua até 30 de novembro de 2020. Após a aposentadoria, os clusters restantes do Azure Red Hat OpenShift 3,11 serão desligados para evitar vulnerabilidades de segurança.
> 
> Siga este guia para [criar um cluster do Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Se você tiver dúvidas específicas, [entre em contato conosco](mailto:arofeedback@microsoft.com).

Microsoft Azure o Red Hat OpenShift requer um locatário do [Azure Active Directory (Azure AD)](../active-directory/develop/quickstart-create-new-tenant.md) no qual criar o cluster. Um *locatário* é uma instância dedicada do Azure AD que um desenvolvedor de organização ou de aplicativo recebe quando cria uma relação com a Microsoft inscrevendo-se no Azure, Microsoft Intune ou Microsoft 365. Cada locatário do Azure AD é distinto e separado de outros locatários do Azure AD e tem suas próprias identidades corporativas e de estudante e registros de aplicativo.

Se você ainda não tiver um locatário do Azure AD, siga estas instruções para criar um.

## <a name="create-a-new-azure-ad-tenant"></a>Criar um novo locatário do Azure Active Directory

Para criar um locatário:

1. Entre no [portal do Azure](https://portal.azure.com/) usando a conta que você deseja associar ao cluster do Azure Red Hat OpenShift.
2. Abra a [folha Azure Active Directory](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) para criar um novo locatário (também conhecido como um novo *Azure Active Directory*).
3. Forneça um **nome de organização**.
4. Forneça um **nome de domínio inicial**. Isso terá *onmicrosoft.com* anexado a ele. Você pode reutilizar o valor para o *nome da organização* aqui.
5. Escolha um país ou região onde o locatário será criado.
6. Clique em **Criar**.
7. Depois que o locatário do Azure AD for criado, selecione o link **clique aqui para gerenciar seu novo diretório** . O novo nome do locatário deve ser exibido no canto superior direito do portal do Azure:  

    ![Captura de tela do portal mostrando o nome do locatário no canto superior direito][tenantcallout]  

8. Anote a ID do *locatário* para que você possa especificar posteriormente onde criar seu cluster do Azure Red Hat OpenShift. No portal, agora você deve ver a folha de visão geral de Azure Active Directory para seu novo locatário. Selecione **Propriedades** e copie o valor para sua **ID de diretório**. Iremos nos referir a esse valor como `TENANT` no tutorial [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) .

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Recursos

Confira [Azure Active Directory documentação](../active-directory/index.yml) para obter mais informações sobre [locatários do Azure ad](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="next-steps"></a>Próximas etapas

Saiba como criar uma entidade de serviço, gerar um segredo do cliente e uma URL de retorno de chamada de autenticação e criar um novo usuário Active Directory para testar aplicativos no cluster do Azure Red Hat OpenShift.

[Criar um usuário e objeto de aplicativo do Azure AD](howto-aad-app-configuration.md)