---
title: Configurar uma assinatura do Azure Marketplace para unidades de teste hospedadas
description: Explica como configurar uma assinatura do Azure Marketplace para o Dynamics 365 para o envolvimento do cliente e o Dynamics 365 para unidades de teste de operações
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 03/16/2020
ms.openlocfilehash: a7f12891bf394e54ee46c60598536faed1731202
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600863"
---
# <a name="set-up-an-azure-marketplace-subscription-for-hosted-test-drives"></a>Configurar uma assinatura do Azure Marketplace para unidades de teste hospedadas

Este artigo explica como configurar uma assinatura do Azure Marketplace e o **dynamics 365 para o envolvimento do cliente** ou o **Dynamics 365 para** o ambiente de operações para unidades de teste.

## <a name="set-up-for-dynamics-365-for-customer-engagement"></a>Configurar o Dynamics 365 for Customer Engagement

1. Entre no [portal do Azure](https://portal.azure.com/) com uma conta de administrador.
2. Verifique se você está no locatário associado à instância de test drive do Dynamics 365 passando o mouse sobre o ícone da sua conta no canto superior direito. Se você não estiver no locatário correto, selecione o ícone de conta para alternar para o locatário correto.

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="Selecionando o locatário correto.":::

3. Verifique se a licença do **plano de envolvimento do cliente do Dynamics 365** está disponível.

    [![Verificando a licença do plano.](media/test-drive/check-plan-license.png)](media/test-drive/check-plan-license.png#lightbox)

4. Criar um aplicativo Azure Active Directory (AD) no Azure. O AppSource usará esse aplicativo para provisionar e desprovisionar o test drive usuário em seu locatário.
    1. No painel de filtro, selecione **Azure Active Directory**.
    2. Selecione **Registros do Aplicativo**.

        [![Selecionando registros do aplicativo.](media/test-drive/app-registrations.png)](media/test-drive/app-registrations.png#lightbox)

    3. Selecione **Novo registro**.
    4. Forneça um nome de aplicativo apropriado.

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="Registrando um aplicativo.":::

    5. Em tipos de conta com suporte, selecione **conta em qualquer diretório da organização e contas pessoais da Microsoft**.
    6. Selecione **criar** e aguarde até que seu aplicativo seja criado.
    7. Depois que o aplicativo for criado, observe a **ID do aplicativo** exibida na tela Visão geral. Você precisará desse valor posteriormente ao configurar seu test drive.
    8. Em **gerenciar aplicativo**, selecione **permissões de API**.
    9. Selecione **Adicionar uma permissão** e, em seguida, **Microsoft Graph API**.
    10. Selecione a categoria de permissão de **aplicativo** e as permissões **User. ReadWrite. All**, **Directory. Read. All** e **Directory. ReadWrite. All** .

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="Definindo as permissões do aplicativo.":::

    11. Depois que a permissão for adicionada, selecione **conceder consentimento de administrador para a Microsoft**.
    12. No alerta de mensagem, selecione **Sim**.

        [![Mostra as permissões de aplicativo concedidas com êxito.](media/test-drive/api-permissions-confirmation-customer.png)](media/test-drive/api-permissions-confirmation-customer.png#lightbox)

    13. Para gerar um segredo para o Aplicativo Azure AD:
        1. Em **gerenciar aplicativo**, selecione **certificado e segredos**.
        2. Em segredos do cliente, selecione **novo segredo do cliente**.
        3. Insira uma descrição, como *Test Drive*, e selecione uma duração apropriada. A test drive será interrompida quando essa chave expirar e, nesse ponto, você precisará gerar e fornecer AppSource uma nova chave.
        4. Selecione **Adicionar** para gerar o segredo do aplicativo do Azure. Copie esse valor, pois ele ficará oculto assim que você lave essa folha. Você precisará desse valor posteriormente ao configurar seu test drive.

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="Adicionando um segredo do cliente.":::

5. Adicione a função de entidade de serviço ao aplicativo para permitir que o aplicativo do Azure AD remova usuários do seu locatário do Azure.
    1. Abra um prompt de comando do PowerShell de nível administrativo.
    2. Install-Module MSOnline (Execute este comando se o MSOnline não estiver instalado).
    3. Connect-MsolService (isso exibirá uma janela pop-up; entre com o locatário da organização criado recentemente).
    4. $applicationId = **<YOUR_APPLICATION_ID>**.
    5. $sp = Get-MsolServicePrincipal-AppPrincipalId $applicationId.
    6. Add-MsolRoleMember-RoleObjectId fe930be7-5E62-47db-91af-98c3a49a38b1-RoleMemberObjectId $sp. ObjectId-RoleMemberType de entidade de segurança.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="Entrar em sua conta.":::

6. Adicione o aplicativo do Azure criado acima como um usuário de aplicativo à sua instância do test drive CRM.
    1. Adicione um novo usuário no **Azure Active Directory**. Somente os valores de **nome** e nome de **usuário** (pertencentes ao mesmo locatário) são necessários para criar esse usuário, deixe os outros campos como padrão. Copie o valor do nome de usuário.
    2. Entre na **instância do CRM** e selecione **configuração**  >    >  **usuários** de segurança.
    3. Altere a exibição para **usuários do aplicativo**.

        :::image type="content" source="./media/test-drive/application-users.png" alt-text="Definindo informações de conta para um usuário.":::

    4. Adicionar um novo usuário (verifique se o formulário é para o usuário do aplicativo).
    5. Insira o mesmo nome de usuário nos campos de **email primário** e **nome de usuários** . Adicione o **ApplicationId do Azure** na **ID do aplicativo**.
    6. Forneça qualquer **nome completo**.
    7. Selecione **Salvar**.
    8. Selecione **gerenciar funções**.
    9. Atribua uma função de segurança personalizada ou OOB que contenha privilégios de leitura, gravação e atribuição de função, como *administrador do sistema*.

        :::image type="content" source="./media/test-drive/security-roles-selection.png" alt-text="Selecionando os privilégios da função.":::

    10. Além disso, habilite o **Act em nome de outro privilégio de usuário** .
    11. Atribua ao usuário do aplicativo a função de segurança personalizada que você criou para seu test drive.

## <a name="set-up-for-dynamics-365-for-operations"></a>Configurar para o Dynamics 365 for Operations

1. Entre no [portal do Azure](https://portal.azure.com/) com uma conta de administrador.
2. Verifique se você está no locatário associado à instância de test drive do Dynamics 365 passando o mouse sobre o ícone da sua conta no canto superior direito. Se você não estiver no locatário correto, selecione o ícone de conta para alternar para o locatário correto.

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="Selecionando o locatário correto.":::

3. Crie um Aplicativo Azure AD no Azure. O AppSource usará esse aplicativo para provisionar e desprovisionar o test drive usuário em seu locatário.
    1. No painel de filtro, selecione **Azure Active Directory**.
    2. Selecione **Registros do Aplicativo**.

        :::image type="content" source="./media/test-drive/app-registrations.png" alt-text="Selecionando um registro de aplicativo.":::

    3. Selecione **Novo registro**.
    4. Forneça um nome de aplicativo apropriado.

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="Registrando um aplicativo.":::

    5. Em tipos de conta com suporte, selecione **conta em qualquer diretório da organização e contas pessoais da Microsoft**.
    6. Selecione **criar** e aguarde até que seu aplicativo seja criado.
    7. Depois que o aplicativo for criado, observe a **ID do aplicativo** exibida na tela Visão geral. Você precisará desse valor posteriormente ao configurar seu test drive.
    8. Em **gerenciar aplicativo**, selecione **permissões de API**.
    9. Selecione **Adicionar uma permissão** e, em seguida, **Microsoft Graph API**.
    10. Selecione a categoria de permissão de **aplicativo** e as permissões **Directory. Read. All** e **Directory. ReadWrite. All** .

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="Definindo permissões de aplicativo.":::

    11. Selecione **adicionar permissão**.
    12. Depois que a permissão for adicionada, selecione **conceder consentimento de administrador para a Microsoft**.
    13. No alerta de mensagem, selecione **Sim**.

        [![Mostra que as permissões do aplicativo foram concedidas com êxito.](media/test-drive/api-permissions-confirmation-operations.png)](media/test-drive/api-permissions-confirmation-operations.png#lightbox)

    14. Para gerar um segredo para o Aplicativo Azure AD:
        1. Em **gerenciar aplicativo**, selecione **certificado e segredos**.
        2. Em segredos do cliente, selecione **novo segredo do cliente**.
        3. Insira uma descrição, como *Test Drive*, e selecione uma duração apropriada. A test drive será interrompida quando essa chave expirar e, nesse ponto, você precisará gerar e fornecer AppSource uma nova chave.
        4. Selecione **Adicionar** para gerar o segredo do aplicativo do Azure. Copie esse valor, pois ele ficará oculto assim que você lave essa folha. Você precisará desse valor posteriormente ao configurar seu test drive.

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="Adicionando um segredo do cliente.":::

4. Adicione a função de entidade de serviço ao aplicativo para permitir que o aplicativo do Azure AD remova usuários do seu locatário do Azure.
    1. Abra um prompt de comando do PowerShell de nível administrativo.
    2. Install-Module MSOnline (Execute este comando se o MSOnline não estiver instalado).
    3. Connect-MsolService (isso exibirá uma janela pop-up; entre com o locatário da organização criado recentemente).
    4. $applicationId = **<YOUR_APPLICATION_ID>**.
    5. $sp = Get-MsolServicePrincipal-AppPrincipalId $applicationId.
    6. Add-MsolRoleMember-RoleObjectId fe930be7-5E62-47db-91af-98c3a49a38b1-RoleMemberObjectId $sp. ObjectId-RoleMemberType de entidade de segurança.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="Entrar em sua conta.":::

5. Agora, adicione o aplicativo acima ao **Dynamics 365 para operações** para permitir que o aplicativo gerencie usuários.
    1. Localize sua instância **do Dynamics 365 for Operations** .
    2. No canto superior esquerdo, clique no menu de três linhas (altat).
    3. Selecione **Administração do sistema**.
    4. Selecione **Azure Active Directory aplicativos**.
    5. Selecione **+ Novo**.
    6. Insira a **ID do cliente do aplicativo do Azure ad** que irá executar as ações em nome de.

    > [!NOTE]
    > A ID de usuário em cujo nome as ações serão executadas (normalmente o administrador do sistema da instância ou um usuário que tem privilégios para adicionar outros usuários).

    [![A ID de usuário em cujo nome as ações serão executadas (normalmente o administrador do sistema da instância ou um usuário que tem privilégios para adicionar outros usuários).](media/test-drive/system-admin-user-id.png)](media/test-drive/system-admin-user-id.png#lightbox)
<!--
## Next steps

- [Commercial marketplace partner and customer usage attribution](azure-partner-customer-usage-attribution.md) -->
