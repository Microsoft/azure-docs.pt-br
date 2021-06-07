---
title: Recuperação de desastre para contas de integração
description: Configurar suas contas de integração e artefatos B2B com a recuperação de desastre entre regiões nos aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/10/2017
ms.openlocfilehash: 1e006dd690e9c008afada8a490da6c3238bc0791
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91565404"
---
# <a name="set-up-cross-region-disaster-recovery-for-integration-accounts-in-azure-logic-apps"></a>Configurar a recuperação de desastre entre regiões para contas de integração em aplicativos lógicos do Azure

As cargas de trabalho B2B envolvem transações de dinheiro como pedidos e faturas. Durante um evento de desastre, é essencial que uma empresa se recupere rapidamente para atender aos SLAs no nível de negócios definidos com seus parceiros. Este artigo demonstra como criar um plano de continuidade dos negócios para cargas de trabalho B2B. 

* Prontidão para recuperação de desastre 
* Alternar para a região secundária durante um evento de desastre 
* Voltar à região primária após um evento de desastre

## <a name="disaster-recovery-readiness"></a>Prontidão para recuperação de desastre  

1. Identificar uma região secundária e criar uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) na região secundária.

2. Adicione parceiros, esquemas e contratos para os fluxos de mensagens necessária em que o status da execução precisa ser replicado a conta de integração da região secundária.

   > [!TIP]
   > Verifique se há consistência na convenção de nomenclatura do artefato da conta de integração entre as regiões. 

3. Para efetuar pull do status da execução da região primária, crie um aplicativo lógico na região secundária. 

   Esse aplicativo lógico deve ter um *gatilho* e uma *ação*. 
   O gatilho deve se conectar à conta de integração da região primária e a ação deve se conectar à conta de integração da região secundária. 
   Com base no intervalo de tempo, o gatilho sonda a tabela de status de execução da região primária e efetua pull dos novos registros, se houver. A ação atualiza-os para uma conta de integração da região secundária. 
   Isso ajuda a obter status de runtime incremental da região primária para região secundária.

4. Continuidade de negócios na conta de integração de aplicativos lógicos destina-se para dar suporte baseado em protocolos B2B – X12, AS2 e EDIFACT. Para obter etapas detalhadas, selecione os respectivos links.

5. A recomendação é implantar todos os recursos da região primária em uma região secundária também. 

   Os recursos da região primária incluem o Banco de Dados SQL do Azure ou o Azure Cosmos DB, o Barramento de Serviço do Azure e os Hubs de Eventos do Azure usados para o mensagens, o Gerenciamento de API do Azure e o recursos dos Aplicativos Lógicos do Azure do Serviço de Aplicativo do Azure.   

6. Estabeleça uma conexão de uma região primária para uma região secundária. Para efetuar pull do status de execução de uma região primária, crie um aplicativo lógico em uma região secundária. 

   O aplicativo lógico deve ter um gatilho e uma ação. 
   O gatilho deve estar conectado a uma conta de integração da região primária. 
   A ação deve estar conectada a uma conta de integração da região secundária. 
   Com base no intervalo de tempo, o gatilho sonda a tabela de status de execução da região primária e efetua pull dos novos registros, se houver. 
   A ação atualiza-os para uma conta de integração da região secundária. 
   Esse processo ajuda a obter o status em runtime incremental da região primária para a região secundária.

A continuidade dos negócios em uma conta de integração dos Aplicativos Lógicos fornece suporte baseado nos protocolos B2B X12, AS2 e EDIFACT. Para obter etapas detalhadas sobre como usar X12 e AS2, consulte [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) e [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) neste artigo.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Fazer failover para uma região secundária durante um evento de desastre

Durante um evento de desastre, quando a região primária não está disponível, direcione o tráfego para a região secundária para continuidade de negócios. Uma região secundária ajuda uma empresa a recuperar as funções rapidamente para atender ao RPO/RTO definido por seus parceiros. Ela também minimiza os esforços de failover de uma região para outra. 

Há uma latência esperada durante a cópia de números de controle de uma região primária para uma região secundária. Para evitar o envio de números de controle gerados duplicados para parceiros durante um evento de desastre, a recomendação é aumentar os números de controle nos contratos da região secundária usando [cmdlets do PowerShell](/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0).

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Fazer failback para o evento pós-desastre em uma região primária

Para fazer failback em uma região primária quando ela estiver disponível, siga estas etapas:

1. Pare de aceitar mensagens de parceiros na região secundária.  

2. Aumente os números de controle gerados para todos os contratos da região primária usando [cmdlets do PowerShell](/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0).  

3. Direcione o tráfego da região secundária para a região primária.

4. Verifique se o aplicativo lógico criado na região secundária para efetuar pull do status de execução da região primária está habilitado.

## <a name="x12"></a>X12 

A continuidade dos negócios para documentos EDI X12 tem como base os números de controle:

> [!TIP]
> Você também pode usar o [modelo de início rápido de X12](https://azure.microsoft.com/resources/templates/201-logic-app-b2b-disaster-recovery-replication/) para criar aplicativos lógicos. Criar contas de integração primárias e secundárias são os pré-requisitos para usar o modelo. O modelo ajuda a criar dois aplicativos lógicos, um para números de controle recebidos e outro para números de controle gerados. As respectivas ações e os respectivos gatilhos são criados nos aplicativos lógicos, conectando o gatilho à conta de integração primária e a ação à conta de integração secundária.

**Pré-requisitos**

Para habilitar a recuperação de desastre para mensagens de entrada, selecione as configurações de verificação duplicadas nas configurações de recepção de contrato X12.

![Selecionar configurações de verificação de duplicadas](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Crie um [aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md) em uma região secundária.    

2. Pesquise **X12** e selecione **X12 – quando um número de controle é modificado**.   

   ![Pesquise por X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   O gatilho solicita que você estabeleça uma conexão com uma conta de integração. 
   O gatilho deve estar conectado a uma conta de integração da região primária.

3. Insira um nome de conexão, selecione sua *conta de integração da região primária* na lista e escolha **Criar**.   

   ![Captura de tela que mostra onde inserir um nome de conexão e selecionar sua conta de integração de região primária. ](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. A configuração **DateTime para iniciar a sincronização de números de controle** é opcional. A **Frequência** pode ser definida como **Dia**, **Hora**, **Minuto** ou **Segundo** com um intervalo.   

   ![Captura de tela que mostra a data e hora para iniciar a configuração de sincronização do número de controle.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Selecione **Nova etapa** > **Adicionar uma ação**.

   ![Captura de tela que mostra o botão nova etapa e a opção Adicionar uma ação.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. Pesquise em **X12** e selecione **X12 – adicionar ou atualizar números de controle**.   

   ![Adicionar ou atualizar números de controle](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Para conectar uma ação a uma conta de integração da região secundária, selecione **alterar conexão**  >  **Adicionar nova conexão** para obter uma lista das contas de integração disponíveis. Insira um nome de conexão, selecione sua *conta de integração da região secundária* na lista e escolha **Criar**. 

   ![Captura de tela que mostra onde adicionar um nome de conta de integração de região secundária.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Mude para entradas brutas clicando no ícone no canto superior direito.

   ![Captura de tela que mostra o ícone a ser selecionado para alternar para RAW.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Selecione o Corpo do seletor de conteúdo dinâmico e salve o aplicativo lógico.

   ![Campos de conteúdo dinâmico](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   Com base no intervalo de tempo, o gatilho sonda a tabela de números de controle recebidos da região primária e efetua pull dos novos registros. 
   A ação atualiza os registros na conta de integração da região secundária. 
   Se não houver nenhuma atualização, o status do gatilho será exibido como **Ignorado**.   

   ![Tabela de números de controle](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Com base no intervalo de tempo, o status em runtime incremental é replicado de uma região primária para uma região secundária. Durante um evento de desastre, quando a região primária não está disponível, direcione o tráfego para a região secundária para continuidade de negócios. 

## <a name="edifact"></a>EDIFACT 

A continuidade dos negócios para documentos EDI EDIFACT tem como base os números de controle.

**Pré-requisitos**

Para habilitar a recuperação de desastre para mensagens de entrada, selecione as configurações de verificação duplicadas nas configurações de recepção do contrato EDIFACT.

![Captura de tela que mostra as configurações de verificação duplicadas nas configurações de recebimento do seu contrato EDIFACT.](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Crie um [aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md) em uma região secundária.    

2. Pesquise **EDIFACT** e selecione **EDIFACT – quando um número de controle é modificado**.

   ![Pesquisar por EDIFACT](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   O gatilho solicita que você estabeleça uma conexão com uma conta de integração. 
   O gatilho deve estar conectado a uma conta de integração da região primária. 

3. Insira um nome de conexão, selecione sua *conta de integração da região primária* na lista e escolha **Criar**.    

   ![Nome da conta de integração da região primária](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. A configuração **DateTime para iniciar a sincronização de números de controle** é opcional. A **Frequência** pode ser definida como **Dia**, **Hora**, **Minuto** ou **Segundo** com um intervalo.    

   ![Captura de tela que mostra as configurações DateTime e Frequency.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Selecione **Nova etapa** > **Adicionar uma ação**.    

   ![Captura de tela que mostra onde selecionar Adicionar uma ação.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. Pesquise em **EDIFACT** e selecione **EDIFACT – adicionar ou atualizar números de controle**.   

   ![Captura de tela que mostra onde adicionar ou atualizar números de controle.](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Para conectar uma ação a uma conta de integração da região secundária, selecione **alterar conexão**  >  **Adicionar nova conexão** para obter uma lista das contas de integração disponíveis. Insira um nome de conexão, selecione sua *conta de integração da região secundária* na lista e escolha **Criar**.

   ![Crie um nome de conta de integração de região secundária.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Mude para entradas brutas clicando no ícone no canto superior direito.

   ![Captura de tela que realça o ícone para selecionar quando você deseja alternar para entradas brutas.](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Selecione o Corpo do seletor de conteúdo dinâmico e salve o aplicativo lógico.   

   ![Captura de tela que mostra o seletor de conteúdo dinâmico no qual você pode selecionar o corpo.](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   Com base no intervalo de tempo, o gatilho sonda a tabela de números de controle recebidos da região primária e efetua pull dos novos registros.
   A ação atualiza os registros na conta de integração da região secundária. 
   Se não houver nenhuma atualização, o status do gatilho será exibido como **Ignorado**.

   ![Tabela de números de controle](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Com base no intervalo de tempo, o status em runtime incremental é replicado de uma região primária para uma região secundária. Durante um evento de desastre, quando a região primária não está disponível, direcione o tráfego para a região secundária para continuidade de negócios. 

## <a name="as2"></a>AS2 

A continuidade dos negócios para documentos que usam o protocolo AS2 se baseia na ID da mensagem e no valor do MIC.

> [!TIP]
> Você também pode usar o [modelo de início rápido de AS2](https://github.com/Azure/azure-quickstart-templates/pull/3302) para criar aplicativos lógicos. Criar contas de integração primárias e secundárias são os pré-requisitos para usar o modelo. O modelo ajuda a criar um aplicativo lógico que tem um gatilho e uma ação. O aplicativo lógico cria uma conexão de um gatilho para uma conta de integração primária e uma ação para uma conta de integração secundária.

1. Crie um [aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md) na região secundária.  

2. Pesquise **AS2** e selecione **AS2 – quando um valor de MIC é criado**.   

   ![Pesquise por AS2](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   Um gatilho solicita que você estabeleça uma conexão com uma conta de integração. 
   O gatilho deve estar conectado a uma conta de integração da região primária. 
   
3. Insira um nome de conexão, selecione sua *conta de integração da região primária* na lista e escolha **Criar**.

   ![Captura de tela que mostra onde inserir um nome de conexão quando um valor de MIC é criado.](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. A configuração **DateTime para iniciar a sincronização de valor do MIC** é opcional. A **Frequência** pode ser definida como **Dia**, **Hora**, **Minuto** ou **Segundo** com um intervalo.   

   ![DateTime e Frequency](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Selecione **Nova etapa** > **Adicionar uma ação**.  

   ![Nova Etapa, Adicionar uma ação](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. Pesquise em **AS2** e selecione **AS2 – adicionar ou atualizar conteúdos de MIC**.  

   ![Adição ou atualização do MIC](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Para conectar uma ação a uma conta de integração secundária, selecione **alterar conexão**  >  **Adicionar nova conexão** para obter uma lista das contas de integração disponíveis. Insira um nome de conexão, selecione sua *conta de integração da região secundária* na lista e escolha **Criar**.

   ![Nome da conta de integração da região secundária](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Mude para entradas brutas clicando no ícone no canto superior direito.

   ![Mudar para entradas brutas](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Selecione o Corpo do seletor de conteúdo dinâmico e salve o aplicativo lógico.   

   ![Conteúdo dinâmico](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Com base no intervalo de tempo, o gatilho sonda a tabela da região primária e efetua pull dos novos registros. A ação atualiza-os para a conta de integração da região secundária. 
   Se não houver nenhuma atualização, o status do gatilho será exibido como **Ignorado**.  

   ![Tabela da região primária](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Com base no intervalo de tempo, o status em runtime incremental é replicado da região primária para a região secundária. Durante um evento de desastre, quando a região primária não está disponível, direcione o tráfego para a região secundária para continuidade de negócios. 

## <a name="next-steps"></a>Próximas etapas

[Monitorar mensagens B2B com os logs do Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)
