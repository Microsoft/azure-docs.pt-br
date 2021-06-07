---
title: Configurar o serviço de QnA Maker-QnA Maker
description: Este documento descreve as configurações avançadas para seus recursos de QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 3c6f75eafad51c99f60b78ce49862d2488d5926f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102220108"
---
# <a name="configure-qna-maker-resources"></a>Configurar recursos do QnA Maker

O usuário pode configurar QnA Maker para usar um recurso de pesquisa cognitiva diferente. Eles também podem definir as configurações do serviço de aplicativo se estiverem usando QnA Maker GA.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

### <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>Configurar QnA Maker para usar diferentes recursos de Pesquisa Cognitiva

Se você criar um serviço QnA e suas dependências (como pesquisa) por meio do portal, um serviço de pesquisa será criado para você e vinculado ao serviço de QnA Maker. Depois que esses recursos forem criados, você poderá atualizar a configuração do serviço de aplicativo para usar um serviço de pesquisa existente anteriormente e remover o que você acabou de criar.

O recurso de **serviço de aplicativo** do QnA Maker usa o recurso pesquisa cognitiva. Para alterar o recurso de Pesquisa Cognitiva usado pelo QnA Maker, você precisa alterar a configuração no portal do Azure.

1. Obtenha a **chave de administração** e o **nome** do pesquisa cognitiva recurso que você deseja que QnA Maker use.

1. Entre no [portal do Azure](https://portal.azure.com) e localize o serviço de **aplicativo** associado ao recurso de QnA Maker. Ambos têm o mesmo nome.

1. Selecione **configurações** e **configuração**. Isso exibirá todas as configurações existentes para o serviço de aplicativo do QnA Maker.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela de portal do Azure mostrando as definições de configuração do serviço de aplicativo](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Altere os valores para as seguintes chaves:

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. Para usar as novas configurações, você precisa reiniciar o serviço de aplicativo. Selecione **visão geral** e, em seguida, selecione **reiniciar**.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela de portal do Azure reiniciar o serviço de aplicativo após a alteração das definições de configuração](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Se você criar um serviço QnA por meio de modelos de Azure Resource Manager, poderá criar todos os recursos e controlar a criação do serviço de aplicativo para usar um serviço de pesquisa existente.

Saiba mais sobre como definir as [configurações do aplicativo](../../../app-service/configure-common.md#configure-app-settings)do serviço de aplicativo.

### <a name="get-the-latest-runtime-updates"></a>Obter as atualizações de tempo de execução mais recentes

O tempo de execução do QnAMaker faz parte da instância do serviço de Azure App que é implantada quando você [cria um serviço QnAMaker](./set-up-qnamaker-service-azure.md) no portal do Azure. Atualizações são feitas periodicamente para o runtime. A instância do serviço de aplicativo QnA Maker está no modo de atualização automática após a versão da extensão do site de abril de 2019 (versão 5 +). Essa atualização foi projetada para cuidar do tempo de inatividade ZERO durante as atualizações.

Você pode verificar a versão atual em https://www.qnamaker.ai/UserSettings . Se sua versão for anterior à versão 5. x, você deverá reiniciar o serviço de aplicativo para aplicar as atualizações mais recentes:

1. Vá para o serviço QnAMaker (grupo de recursos) no [portal do Azure](https://portal.azure.com).

    > [!div class="mx-imgBorder"]
    > ![Grupo de recursos do Azure do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selecione a instância do serviço de aplicativo e abra a seção **visão geral** .

    > [!div class="mx-imgBorder"]
    > ![Instância do serviço de aplicativo QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. Reinicie o serviço de aplicativo. O processo de atualização deve ser concluído em alguns segundos. Quaisquer aplicativos dependentes ou bots que usam esse serviço QnAMaker não estarão disponíveis para os usuários finais durante esse período de reinicialização.

    ![Reinicialização da instância do serviço de aplicativo QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

### <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>Definir a configuração de ociosidade do serviço de aplicativo para evitar o tempo limite

O serviço de aplicativo, que serve o tempo de execução de previsão de QnA Maker para uma base de dados de conhecimento publicada, tem uma configuração de tempo limite de ociosidade, que assume o tempo limite automaticamente se o serviço estiver ocioso. Por QnA Maker, isso significa que sua API generateAnswer de tempo de execução de previsão ocasionalmente expira após períodos sem nenhum tráfego.

Para manter o aplicativo de ponto de extremidade de previsão carregado mesmo quando não houver tráfego, defina o ocioso como sempre ativo.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Pesquise e selecione o serviço de aplicativo do QnA Maker recurso. Ele terá o mesmo nome que o recurso QnA Maker, mas terá um **tipo** diferente de serviço de aplicativo.
1. Localize **as configurações** e selecione **configuração**.
1. No painel configuração, selecione **configurações gerais** e, em seguida, localizar **AlwaysOn** e selecione **ativado** como o valor.

    > [!div class="mx-imgBorder"]
    > ![No painel configuração, selecione * * configurações gerais * *, em seguida, localize * * Always on * * e selecione * * em * * como o valor.](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. Selecione **salvar** para salvar a configuração.
1. Você será perguntado se deseja reiniciar o aplicativo para usar a nova configuração. Selecione **Continuar**.

Saiba mais sobre como definir as [configurações gerais](../../../app-service/configure-common.md#configure-general-settings)do serviço de aplicativo.

### <a name="business-continuity-with-traffic-manager"></a>Continuidade dos negócios com o Gerenciador de tráfego

O principal objetivo do plano de continuidade de negócios é criar um ponto de extremidade resiliente da base de conhecimento que garantiria a total ausência de tempo de inatividade para o bot ou aplicativo que a estivesse consumindo.

> [!div class="mx-imgBorder"]
> ![Plano de backup do QnA Maker](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

A ideia de alto nível como representada acima é a seguinte:

1. Configurar dois [serviços QnA Maker](set-up-qnamaker-service-azure.md) em paralelo nas [regiões emparelhadas do Azure](../../../best-practices-availability-paired-regions.md).

1. [Faça backup](../../../app-service/manage-backup.md) de seu serviço de aplicativo de QnA Maker primário e [restaure](../../../app-service/web-sites-restore.md) -o na instalação secundária. Isso garantirá que ambas as configurações funcionem com o mesmo nome de host e chaves.

1. Mantenha os índices primário e secundário do Azure Search sincronizados. Use o exemplo do GitHub [aqui](https://github.com/pchoudhari/QnAMakerBackupRestore) para ver como fazer backup-restaurar índices do Azure.

1. Fazer backup do Application Insights usando [exportação contínua](../../../azure-monitor/app/export-telemetry.md).

1. Depois que as pilhas primárias e secundárias forem configuradas, use o [gerenciador de tráfego](../../../traffic-manager/traffic-manager-overview.md) para configurar os dois pontos de extremidade e configurar um método de roteamento.

1. Você precisaria criar uma TLS (segurança de camada de transporte), anteriormente conhecida como protocolo SSL (SSL), certificado para o ponto de extremidade do Gerenciador de tráfego. [Associe o certificado TLS/SSL](../../../app-service/configure-ssl-bindings.md) nos serviços de aplicativo.

1. Por fim, use o ponto de extremidade do gerenciador de tráfego em seu bot ou aplicativo.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

### <a name="configure-qna-maker-managed-preview-service-to-use-different-cognitive-search-resource"></a>Configurar QnA Maker serviço gerenciado (versão prévia) para usar diferentes recursos de Pesquisa Cognitiva

Se você criar um serviço QnA gerenciado (versão prévia) e suas dependências (como pesquisa) por meio do portal, um serviço de pesquisa será criado para você e vinculado ao serviço gerenciado (versão prévia) QnA Maker. Depois que esses recursos forem criados, você poderá atualizar o serviço de pesquisa na guia **configuração** .

1. Vá para o serviço QnA Maker gerenciado (versão prévia) no portal do Azure.

1. Selecione **configuração** e selecione o serviço de pesquisa cognitiva do Azure que você deseja vincular ao serviço QnA Maker gerenciado (versão prévia).

    ![Captura de tela de QnA Maker página de configuração gerenciada (versão prévia)](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-configuration.png)

1. Clique em **Save** (Salvar).

> [!NOTE]
> Se você alterar o serviço de Azure Search associado ao QnA Maker, você perderá o acesso a todas as bases de dados de conhecimento já presentes nela. Certifique-se de exportar as bases de dados de conhecimento existentes antes de alterar o serviço Azure Search.

---
