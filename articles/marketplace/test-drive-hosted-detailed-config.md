---
title: Configuração detalhada para um test drive hospedado no Azure Marketplace
description: Explicação das etapas de configuração para um test drive hospedado no Marketplace comercial
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 11/06/2020
ms.openlocfilehash: 88779f67a2fa9b18f0177a1459b32c672343bb57
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96462952"
---
# <a name="detailed-configuration-for-hosted-test-drives"></a>Configuração detalhada para unidades de teste hospedadas

Este artigo descreve como configurar um test drive hospedado para o Dynamics 365 para o envolvimento do cliente ou o Dynamics 365 para operações.

## <a name="configure-for-dynamics-365-customer-engagement"></a>Configurar para o compromisso com o cliente do Dynamics 365

1. Entre no [Partner Center](https://partner.microsoft.com/).
2. Se você não puder acessar o link acima, será necessário enviar uma solicitação [aqui](https://appsource.microsoft.com/partners/list-an-app) para publicar seu aplicativo. Depois de examinarmos a solicitação, você receberá acesso para iniciar o processo de publicação.
3. Encontre uma oferta existente **do dynamics 365 for Customer Engagement** ou crie uma nova oferta **Dynamics 365 for Customer Engagement** .
4. Marque a caixa de seleção **habilitar um test drive** e selecione um **tipo de Test Drive** (consulte o marcador abaixo) e, em seguida, selecione **salvar**.

    [![Marque a caixa de seleção ' habilitar um test drive '.](media/test-drive/enable-test-drive-check-box.png)](media/test-drive/enable-test-drive-check-box.png#lightbox)

    - **Tipo de Test Drive** – escolha **Microsoft Hosted (Dynamics 365 para compromisso com o cliente & PowerApps)**. Isso indica que a Microsoft hospedará e manterá o serviço que executa o provisionamento e desprovisionamento test drive usuário.

5. Conceda Microsoft AppSource permissão para provisionar e desprovisionar test drive usuários em seu locatário usando [estas instruções](./test-drive-azure-subscription-setup.md). Nesta etapa, você irá gerar a **ID de aplicativo Azure ad** e **aplicativo Azure ad** valores de chave mencionados abaixo.
6. Preencha esses campos na página de **configuração técnica do Test Drive** .

    [![A página de configuração técnica do test drive.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Máximo de unidades de teste simultâneas** – o número de usuários simultâneos que podem ter um test drive ativo em execução ao mesmo tempo. Cada usuário consumirá uma licença do Dynamics enquanto seu test drive estiver ativo, portanto, verifique se você tem pelo menos essas muitas licenças do Dynamics disponíveis para test drive usuários. Recomendamos 3 a 5.
    - **Duração do teste de unidade** – o número de horas que o test drive do usuário estará ativo. Depois que o tempo tiver expirado, o usuário será desprovisionado do seu locatário. Recomendamos 2-24 horas, dependendo da complexidade do seu aplicativo. O usuário sempre poderá solicitar outra test drive se ficar sem tempo e quiser acessar a test drive novamente.
    - **URL da instância** – a URL à qual o usuário Test Drive será enviado quando iniciar o Test Drive. Normalmente, essa é a URL da instância do Dynamics 365 na qual seu aplicativo e os dados de exemplo estão instalados. Valor de exemplo: `https://testdrive.crm.dynamics.com` .
    - **URL da API Web da instância** – a URL da API da Web para sua instância do Dynamics 365. Recupere esse valor entrando em sua instância do Microsoft Dynamics 365 e navegando até **definindo** a  >    >    >  **API Web da instância** de recursos do desenvolvedor de personalização e copie o endereço (URL). Valor de exemplo:

        :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="Um exemplo de API Web de instância.":::

    - **Nome da função** – o nome da função de segurança personalizada do Dynamics 365 criada para Test Drive ou você pode usar uma função existente. Uma nova função deve ter os privilégios mínimos necessários adicionados à função para entrar em uma instância do engajamento do cliente. Consulte os [privilégios mínimos necessários para entrar no Microsoft Dynamics 365](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365). Essa é a função que será atribuída aos usuários durante seus test drive. Valor de exemplo: `testdriverole` .
    
        > [!IMPORTANT]
        > Verifique se a verificação do grupo de segurança não foi adicionada. Isso permite que o usuário seja sincronizado com a instância do engajamento do cliente.

    - **ID de locatário Azure Active Directory** – a ID do locatário do Azure para sua instância do Dynamics 365. Para recuperar esse valor, entre no portal do Azure e navegue até **Azure Active Directory**  >  **Propriedades** e copie a ID do diretório. Valor de exemplo: 172f988bf-86f1-41AF-91ab-2d7cd01112341.
    - **Nome do locatário do Azure Active Directory** – o nome do locatário do Azure para sua instância do Dynamics 365. Use o formato `<tenantname>.onmicrosoft.com`. Valor de exemplo: `testdrive.onmicrosoft.com` .
    - **ID do aplicativo Azure Active Directory** – a ID do aplicativo Azure Active Directory (AD) que você criou na etapa 5. Valor de exemplo: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Active Directory segredo do cliente do aplicativo** – segredo para o aplicativo do Azure ad criado na etapa 5. Valor de exemplo: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .

7. Forneça os detalhes de listagem do Marketplace. Selecione **idioma** para ver outros campos obrigatórios.

    [![A página de detalhes de listagem do Marketplace.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Descrição** – uma visão geral do seu Test Drive. Esse texto será mostrado ao usuário enquanto o test drive estiver sendo provisionado. Esse campo dá suporte a HTML, caso você queira fornecer conteúdo formatado.
    - **Manual do usuário** – um manual de usuário do PDF que ajuda a Test Drive os usuários a entender como usar seu aplicativo.
    - **Vídeo de demonstração do Test Drive** – um vídeo que demonstra seu aplicativo (opcional).

## <a name="configure-for-dynamics-365-operations"></a>Configurar para operações do Dynamics 365

2. Se você não puder acessar o link acima, será necessário enviar uma solicitação [aqui](https://appsource.microsoft.com/partners/list-an-app) para publicar seu aplicativo. Depois de examinarmos a solicitação, você receberá acesso para iniciar o processo de publicação.
3. Encontre uma oferta existente **do dynamics 365 for Operations** ou crie uma nova oferta **do Dynamics 365 for Operations** .
4. Marque a caixa de seleção **habilitar um test drive** e selecione um **tipo de Test Drive** (consulte o marcador abaixo) e, em seguida, selecione **salvar**.

    [![Marque a caixa de seleção ' habilitar um test drive '.](media/test-drive/enable-test-drive-check-box-operations.png)](media/test-drive/enable-test-drive-check-box-operations.png#lightbox)

    - **Tipo de Test Drive** – escolha **Dynamics 365 para a opção de operações** . Isso significa que a Microsoft hospedará e manterá o serviço que executa o provisionamento e desprovisionamento test drive usuário.

5. Conceda Microsoft AppSource permissão para provisionar e desprovisionar test drive usuários em seu locatário usando [estas instruções](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md). Nesta etapa, você irá gerar a **ID de aplicativo Azure ad** e **aplicativo Azure ad** valores de chave mencionados abaixo.
6. Preencha esses campos na página de **configuração técnica do Test Drive** .

    [![A página de configuração técnica do Marketplace.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Máximo de unidades de teste simultâneas** – o número de usuários simultâneos que podem ter um test drive ativo em execução ao mesmo tempo. Cada usuário consumirá uma licença do Dynamics enquanto seu test drive estiver ativo, portanto, verifique se você tem pelo menos essas muitas licenças do Dynamics disponíveis para test drive usuários. Recomendamos 3 a 5.
    - **Duração do teste de unidade** – o número de horas que o test drive do usuário estará ativo. Depois que o tempo tiver expirado, o usuário será desprovisionado do seu locatário. Recomendamos 2-24 horas, dependendo da complexidade do seu aplicativo. O usuário sempre poderá solicitar outra test drive se ficar sem tempo e quiser acessar a test drive novamente.
    - **URL da instância** – a URL à qual o usuário Test Drive será enviado quando iniciar o Test Drive. Normalmente, essa é a URL da instância do Dynamics 365 na qual seu aplicativo e os dados de exemplo estão instalados. Valor de exemplo: `https://testdrive.crm.dynamics.com` .
    - **ID de locatário Azure Active Directory** – a ID do locatário do Azure para sua instância do Dynamics 365. Para recuperar esse valor, entre no portal do Azure e navegue até **Azure Active Directory**  >  **Propriedades** e copie a ID do diretório. Valor de exemplo: 172f988bf-86f1-41AF-91ab-2d7cd01112341.
    - **Nome do locatário do Azure Active Directory** – o nome do locatário do Azure para sua instância do Dynamics 365. Use o formato `<tenantname>.onmicrosoft.com`. Valor de exemplo: `testdrive.onmicrosoft.com` .
    - **ID do aplicativo Azure Active Directory** – a ID do aplicativo Azure Active Directory (AD) que você criou na etapa 5. Valor de exemplo: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Active Directory segredo do cliente do aplicativo** – segredo para o aplicativo do Azure ad criado na etapa 5. Valor de exemplo: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .
    - **Entidade legal de avaliação** – forneça uma entidade legal para atribuir um usuário de avaliação. Você pode criar um novo em [criar ou modificar uma entidade legal](/dynamicsax-2012/appuser-itpro/create-or-modify-a-legal-entity).
    - **Nome da função** – o nome da AoT (árvore de objetos de aplicativo) da função de segurança personalizada do Dynamics 365 que você criou para Test Drive. Essa é a função que será atribuída aos usuários durante seus test drive.

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="A página de configuração de segurança.":::

7. Forneça os detalhes de listagem do Marketplace. Selecione **idioma** para ver outros campos obrigatórios.

    [![A página de detalhes de listagem do Marketplace.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Descrição** – uma visão geral do seu Test Drive. Esse texto será mostrado ao usuário enquanto o test drive estiver sendo provisionado. Esse campo dá suporte a HTML, caso você queira fornecer conteúdo formatado.
    - **Manual do usuário** – um manual de usuário do PDF que ajuda a Test Drive os usuários a entender como usar seu aplicativo.
    - **Vídeo de demonstração do Test Drive** – um vídeo que demonstra seu aplicativo (opcional).

<!--
## Next steps

- [Set up your Azure subscription](test-drive-azure-subscription-setup.md) -->