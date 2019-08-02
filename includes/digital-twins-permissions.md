---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 06/28/2019
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: 324f41055cf333081f308a3ff533ff7df6b33038
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "67479152"
---
>[!NOTE]
>Esta seção fornece instruções para o [novo registro do aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Se você ainda tiver o registro de aplicativo nativo herdado, poderá usá-lo, desde que seja compatível. Além disso, se por algum motivo a nova forma de registro de aplicativo não estiver funcionando em sua configuração, você poderá tentar criar um aplicativo Azure Active Directory nativo legado. Leia [Registrar seu aplicativo Gêmeos Digitais do Azure com o legado do Azure Active Directory](../articles/digital-twins/how-to-use-legacy-aad.md) para obter mais instruções. 

1. No [portal do Azure](https://portal.azure.com), abra **Azure Active Directory** no painel esquerdo e, em seguida, abra o painel **Registros de aplicativo**. Selecione o botão **Novo registro**.

    ![Aplicativo registrado](./media/digital-twins-permissions/aad-app-register.png)

1. Dê um nome amigável para esse registro de aplicativo na caixa **Nome**. Na seção **URI de Redirecionamento (opcional)** , selecione **Cliente público (móvel e da área de trabalho)** na lista suspensa à esquerda e insira `https://microsoft.com` na caixa de texto à direita. Selecione **Registrar**.

    ![Criar painel](./media/digital-twins-permissions/aad-app-reg-create.png)

1. Para certificar-se de que [o aplicativo está registrado como um *aplicativo nativo*](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), abra o painel **Autenticação** para o registro do aplicativo e role para baixo nesse painel. Na seção **Tipo de cliente padrão**, escolha **Sim** para **Tratar o aplicativo como um cliente público**. 

    ![Nativo padrão](./media/digital-twins-permissions/aad-app-default-native.png)

1.  Abra o painel **Visão geral** do seu aplicativo registrado e copie os valores das entidades a seguir para um arquivo temporário. Você usará esses valores para configurar seu aplicativo de exemplo nas seções a seguir.

    - **ID do Aplicativo (cliente)**
    - **ID do Diretório (locatário)**

    ![ID do aplicativo do Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-app-id.png)

1. Abra o painel **Permissões de API** para o registro do aplicativo. Selecione o botão **Adicionar uma permissão**. No painel **Solicitar permissões de API**, selecione a guia **APIs que a minha organização usa** e pesquise **Azure Smart Spaces**. Selecione a API **Azure Smart Spaces Service**.

    ![API de Pesquisa](./media/digital-twins-permissions/aad-app-search-api.png)

1. A API selecionada é exibida como **Gêmeos Digitais do Azure** no mesmo painel **Solicitar permissões de API**. Selecione a lista suspensa **Ler (1)** e a caixa de seleção **Read.Write**. Selecione o botão **Adicionar permissões**.

    ![Adicionar permissões de API](./media/digital-twins-permissions/aad-app-req-permissions.png)

1. Dependendo das configurações da sua organização, talvez você precise seguir etapas adicionais para conceder acesso de administrador a essa API. Entre em contato com seu administrador para obter mais informações. Assim que o acesso de administrador for aprovado, a coluna **CONSENTIMENTO DO ADMINISTRADOR NECESSÁRIO** no painel de **Permissões de API** será mostrada da seguinte forma para as suas APIs:

    ![Adicionar permissões de API](./media/digital-twins-permissions/aad-app-admin-consent.png)

