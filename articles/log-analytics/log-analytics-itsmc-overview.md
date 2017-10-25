---
title: "Conector de Gerenciamento de Serviço de TI no Azure Log Analytics | Microsoft Docs"
description: "Use o Conector de Gerenciamento de Serviço de TI para monitorar e gerenciar os itens de trabalho de ITSM de forma centralizada no Azure Log Analytics e para resolver problemas rapidamente."
services: log-analytics
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: v-jysur
ms.openlocfilehash: 411d6103852cbf534d3c420d5ea7b2146df5164e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="centrally-manage-itsm-work-items-using-it-service-management-connector-preview"></a>Gerenciar itens de trabalho de ITSM de forma centralizada usando o Conector de Gerenciamento de Serviço de TI (Visualização)

![Símbolo do Conector de Gerenciamento do Serviço de TI](./media/log-analytics-itsmc/itsmc-symbol.png)

O Conector de Gerenciamento de Serviço de TI fornece uma integração bidirecional entre um produto ou serviço de ITSM (Gerenciamento de Serviço de TI) e o Log Analytics.  Por essa conexão, você pode criar incidentes, alertas ou eventos no produto de ITSM com base em alertas ou em registros de log do Log Analytics. O conector também importa dados, como incidentes e solicitações de alteração do produto de ITSM para o OMS Log Analytics.

Com o Conector de Gerenciamento de Serviço de TI, você pode:

  - Integre alertas operacionais às suas práticas de gerenciamento de incidentes na ferramenta de ITSM de sua escolha.
    - Crie itens de trabalho (como alertas, eventos, incidentes) no ITSM por meio de alertas do OMS e de pesquisa de logs.
    - Crie itens de trabalho com base em seus alertas do Log de Atividades do Azure por meio de ação de ITSM em grupos de ações. 
  
  - Unifique os dados de monitoramento, de log e de gerenciamento de serviço usados pela sua organização.
    - Correlacione o incidente e altere dados de solicitação de suas ferramentas de ITSM com os dados de log relevantes no espaço de trabalho do Log Analytics.   
    - Exiba painéis de alto nível para obter uma visão geral sobre incidentes, solicitações de alteração e sistemas afetados.
    - Escreva consultas do Log Analytics para obter informações sobre os dados de gerenciamento de serviço.
      
## <a name="adding-the-it-service-management-connector-solution"></a>Adicionando a solução Conector de Gerenciamento de Serviço de TI

Adicione a solução Conector de Gerenciamento de Serviço de TI ao seu espaço de trabalho do Log Analytics, usando o processo descrito em [Adicionar soluções de gerenciamento de Log Analytics do Azure a seu espaço de trabalho](log-analytics-add-solutions.md).

O bloco Conector de Gerenciamento de Serviço de TI como visto na Galeria de soluções:

![bloco do conector](./media/log-analytics-itsmc/itsmc-solutions-tile.png)

Após a adição bem-sucedida, você verá o Conector de Gerenciamento de Serviço de TI em **OMS** > **Configurações** > **Fontes Conectadas.**

![ITSMC conectado](./media/log-analytics-itsmc/itsmc-overview-solution-in-connected-sources.png)

> [!NOTE]

> Por padrão, o Conector de Gerenciamento do Serviço de TI atualiza os dados da conexão uma vez em cada 24 horas. Para atualizar os dados da conexão instantaneamente para as edições ou atualizações do modelo que você fizer, clique no botão Atualizar exibido ao lado de sua conexão.

 ![Atualização do ITSMC](./media/log-analytics-itsmc/itsmc-connection-refresh.png)


## <a name="configuring-the-connection-with-your-itsm-software"></a>Configurando a conexão com seu software de ITSM

A solução Conector de Gerenciamento de Serviço de TI dá suporte a conexões com o **System Center Service Manager**, o **ServiceNow**, o **Provance** e o **Cherwell**. Configure sua conexão com

- [SCSM (System Center Service Manager)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-oms)

- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-oms)

- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-oms)  

- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-oms)

## <a name="using-the-solution"></a>Usando a solução

Depois que você configurar o Conector de Gerenciamento de Serviço de TI com os detalhes do software de ITSM, o Conector iniciará a coleta de dados do produto/serviço de ITSM conectado. Dependendo do número de incidentes e das solicitações de alteração no produto/serviço de ITSM, a sincronização inicial será concluída em alguns minutos. 

> [!NOTE]
> - Os dados importados do produto de ITSM pela solução Conector de Gerenciamento de Serviço de TI aparecem no Log Analytics como registros de log do tipo **ServiceDesk_CL**.
> - O registro de log contém um campo chamado **ServiceDeskWorkItemType_s**, que é um incidente ou uma solicitação de alteração, os dois tipos de dados importados do produto de ITSM

## <a name="data-synced-from-itsm-product"></a>Dados sincronizados do produto de ITSM
Os incidentes e as solicitações de alteração são sincronizados do produto de ITSM para o espaço de trabalho do Log Analytics. As seguintes informações mostram exemplos de dados coletados pelo conector de Gerenciamento de Serviço de TI:

> [!NOTE]
> Dependendo do tipo de item de trabalho importado para o Log Analytics, **ServiceDesk_CL** contém os seguintes campos:

**Item de trabalho:** **Incidentes**  
ServiceDeskWorkItemType_s="Incident"

**Campos**

- ServiceDeskConnectionName
- ID da Central de Serviços
- Estado
- Urgência
- Impacto
- Prioridade
- Escalonamento
- Criado por
- Resolvido por
- Fechado por
- Fonte
- Atribuído a
- Categoria
- Title
- Descrição
- Data de criação
- Data de fechamento
- Data de resolução
- Data da última modificação
- Computador


**Item de trabalho:** **Solicitações de alteração**

ServiceDeskWorkItemType_s="ChangeRequest"

**Campos**
- ServiceDeskConnectionName
- ID da Central de Serviços
- Criado por
- Fechado por
- Fonte
- Atribuído a
- Title
- Tipo
- Categoria
- Estado
- Escalonamento
- Status do conflito
- Urgência
- Prioridade
- Risco
- Impacto
- Atribuído a
- Data de criação
- Data de fechamento
- Data da última modificação
- Data de solicitação
- Data de início prevista
- Data de término prevista
- Data de início do trabalho
- Data de término do trabalho
- Descrição
- Computador

## <a name="output-data-for-a-servicenow-incident"></a>Dados de saída de um incidente do ServiceNow

| Campo do OMS | Campo do ITSM |
|:--- |:--- |
| ServiceDeskId_s| Número |
| IncidentState_s | Estado |
| Urgency_s |Urgência |
| Impact_s |Impacto|
| Priority_s | Prioridade |
| CreatedBy_s | Aberto por |
| ResolvedBy_s | Resolvido por|
| ClosedBy_s  | Fechado por |
| Source_s| Tipo de contato |
| AssignedTo_s | Atribuído a  |
| Category_s | Categoria |
| Title_s|  Descrição breve |
| Description_s|  Observações |
| CreatedDate_t|  Aberto |
| ClosedDate_t| closed|
| ResolvedDate_t|Resolvido|
| Computador  | Item de configuração |

## <a name="output-data-for-a-servicenow-change-request"></a>Dados de saída para uma solicitação de alteração do ServiceNow

| Campo do OMS | Campo do ITSM |
|:--- |:--- |
| ServiceDeskId_s| Número |
| CreatedBy_s | Solicitado por |
| ClosedBy_s | Fechado por |
| AssignedTo_s | Atribuído a  |
| Title_s|  Descrição breve |
| Type_s|  Tipo |
| Category_s|  Categoria |
| CRState_s|  Estado|
| Urgency_s|  Urgência |
| Priority_s| Prioridade|
| Risk_s| Risco|
| Impact_s| Impacto|
| RequestedDate_t  | Solicitado por data |
| ClosedDate_t | Data de fechamento |
| PlannedStartDate_t  |     Data de início planejada |
| PlannedEndDate_t  |   Data de término planejada |
| WorkStartDate_t  | Data de início real |
| WorkEndDate_t | Data de término real|
| Description_s | Descrição |
| Computador  | Item de Configuração |

**Tela de exemplo do Log Analytics para dados de ITSM:**

![Tela do Log Analytics](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

## <a name="it-service-management-connector--integration-with-other-oms-solutions"></a>Conector de Gerenciamento de Serviço de TI – integração com outras soluções do OMS

Atualmente, o Conector de Gerenciamento de Serviço de TI dá suporte à integração com a solução Mapa do Serviço.

O Mapa do Serviço descobre automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Ele permite que você exiba os seus servidores da maneira como pensa neles – como sistemas interconectados que fornecem serviços essenciais. O Mapa do Serviço mostra conexões entre servidores, processos e portas em qualquer arquitetura conectada a TCP sem nenhuma configuração necessária além da instalação de um agente. Mais informações: [Mapa do Serviço](../operations-management-suite/operations-management-suite-service-map.md).

Se você também usa a solução Mapa do Serviço, é possível exibir os itens de suporte técnico de serviço criados nas soluções de ITSM, conforme é mostrado no exemplo a seguir:

![Integração do ServiceMap](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)
## <a name="create-itsm-work-items-for-oms-alerts"></a>Criar itens de trabalho de ITSM para alertas do OMS

Com a solução Conector de ITSM em vigor, você pode configurar alertas do OMS para disparar a criação de itens de trabalho em sua ferramenta de ITSM conectada, da seguinte maneira:

1. Na janela **Pesquisa de Logs**, execute uma consulta da pesquisa de logs para exibir os dados. Os resultados da consulta são a fonte de itens de trabalho.
2. Em **Pesquisa de Logs**, clique em **Alerta** para abrir a página **Adicionar Regra de Alerta**.

    ![Tela do Log Analytics](./media/log-analytics-itsmc/itsmc-work-items-for-oms-alerts.png)

3. Na janela **Adicionar Regra de Alerta**, forneça os detalhes necessários para **Nome**, **Gravidade**, **Consulta de pesquisa** e **Critérios de alerta** (medição Janela de Tempo/Métrica).
4. Selecione **Sim** para **Ações de ITSM**.
5. Selecione a conexão de ITSM na lista **Selecionar Conexão**.
6. Forneça os detalhes conforme necessário.
7. Para criar um item de trabalho separado para cada entrada de log desse alerta, marque a caixa de seleção **Criar itens de trabalho individuais para cada entrada de log**.

    Ou

    deixe essa caixa de seleção desmarcada para criar apenas um item de trabalho para qualquer quantidade de entradas de log nesse alerta.

7. Clique em **Salvar**.

O alerta do OMS que você criou pode ser visto em **Configurações**>**Alertas**. Os itens de trabalho da conexão de ITSM correspondentes são criados quando o critério do alerta especificado é atendido.

## <a name="create-itsm-work-items-from-oms-logs"></a>Criar itens de trabalho de ITSM com base em logs do OMS

Você também pode criar itens de trabalho nas fontes de ITSM conectadas diretamente de um registro de log da seguinte maneira:

1. Na **Pesquisa de Logs**, pesquise os dados necessários, selecione os detalhes e clique em **Criar item de trabalho**.

    A janela **Criar item de trabalho de ITSM** é exibida:

    ![Tela do Log Analytics](media/log-analytics-itsmc/itsmc-work-items-from-oms-logs.png)

2.   Adicione os seguintes detalhes:

  - **Título do Item de trabalho**: título do item de trabalho.
  - **Descrição do Item de trabalho**: descrição do novo item de trabalho.
  - **Computador Afetado**: nome do computador no qual esses dados de logs foram encontrados.
  - **Selecionar Conexão**: conexão de ITSM na qual você deseja criar esse item de trabalho.
  - **Item de trabalho**: tipo de item de trabalho.

3. Para usar um modelo de item de trabalho existente para um incidente, clique em **Sim** na opção **Gerar item de trabalho com base no modelo** e, em seguida, clique em **Criar**.

    Ou,

    Clique em **Não** se desejar fornecer valores personalizados.

4. Forneça os valores apropriados nas caixas de texto **Tipo de Contato**, **Impacto**, **Urgência**, **Categoria** e **Subcategoria** e clique em **Criar**.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Criar itens de trabalho de ITSM por meio de alertas do Azure
O Conector de ITSM agora está integrado com os grupos de ações. Os [Grupos de Ações](../monitoring-and-diagnostics/monitoring-action-groups.md) fornecem uma maneira modular e reutilizável de disparo de ações para alertas do Azure. A ação de ITSM nos grupos de ações cria itens de trabalho em seu produto de ITSM usando uma solução de Conector de ITSM existente.

1. No Portal do Azure, clique em **Monitorar**
2. No painel esquerdo, clique em **Grupos de Ação**

    ![Grupos de Ação](media/log-analytics-itsmc/ActionGroups.png)

3. Forneça um **nome** e um **ShortName** para o grupo de ações. Selecione o **Grupo de Recursos** e a **Assinatura** em que deseja que o grupo de ações seja criado.

    ![Detalhes dos grupos de ações](media/log-analytics-itsmc/ActionGroupsDetail.png)

4. Na lista Ações, selecione **ITSM** na lista suspensa de **Tipo de Ação**. Forneça um **Nome** para a ação e clique em **Editar detalhes**.


5. Selecione a **Assinatura** em que o espaço de trabalho do Log Analytics está localizado. Selecione a **Conexão**, ou seja, o nome do Conector de ITSM seguido pelo nome do espaço de trabalho. Por exemplo, "MyITSMMConnector(MyWorkspace)."

    ![Detalhes da ação de ITSM](./media/log-analytics-itsmc/ITSMActionDetails.png)

6. Selecione o tipo **Item de Trabalho** na lista suspensa.
7. Escolha usar um modelo existente ou preencha os campos necessários para seu produto de ITSM.
8. Clique em **OK**

Ao criar/editar uma regra de alerta do Azure, use um grupo de ações que tenha uma ação de ITSM. Quando o alerta for disparado, o item de trabalho será criado na ferramenta de ITSM. 

>[!NOTE]
>Atualmente, apenas alertas do log de atividades dão suporte para a ação de ITSM. Para outros alertas do Azure, essa ação é inoperante.
>


## <a name="troubleshoot-itsm-connections-in-oms"></a>Solucionar problemas de conexões de ITSM no OMS
1.  Se a conexão falhar na interface do usuário de origem conectada, com uma mensagem **Erro ao salvar conexão**, execute as seguintes etapas:
 - Para conexões com o ServiceNow, o Cherwell e o Provance,
    - Verifique se você digitou corretamente o nome de usuário, a senha, a ID do cliente e o segredo do cliente para cada uma das conexões.
    - verifique se você tem privilégios suficientes no produto de ITSM correspondente para fazer a conexão.
 - Para conexões do Service Manager,
     - verifique se o aplicativo Web está implantado com êxito e se a conexão híbrida está criada. Para verificar se a conexão está estabelecida com êxito com o computador do Service Manager local, visite a URL do aplicativo Web conforme detalhado na documentação para fazer a [conexão híbrida](log-analytics-itsmc-connections.md#configure-the-hybrid-connection).
     
2.  Se os dados do ServiceNow não estiverem sendo sincronizados com o Log Analytics, verifique se a instância do ServiceNow não está em suspensão. As instâncias de desenvolvimento do ServiceNow, às vezes, entram em suspensão quando ficam ociosas por um longo período. Caso contrário, relate o problema.
3.  Se os alertas do OMS são disparados mas os itens de trabalho não são criados no produto de ITSM ou se os itens de configuração não são criados/vinculados aos itens de trabalho ou para obter qualquer outra informação genérica, procure nos seguintes locais:
 -  **Solução Conector de Gerenciamento de Serviço de TI**: a solução mostra um resumo de itens de trabalho/conexões/computadores etc. Clique no bloco que mostra o **Status do Conector**, que o levará à **Pesquisa de Logs** com a consulta relevante. Examine os registros de log com LogType_S como ERROR para obter mais informações.
 - Ou exiba os erros e as informações relacionadas diretamente na página **Pesquisa de Logs** usando a consulta *Type=ServiceDeskLog_CL*.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Solucionar problemas de implantação do aplicativo Web do Service Manager
1.  Se você enfrentar problemas com a implantação de aplicativo Web, verifique se você tem permissões suficientes na assinatura mencionada para criar/implantar recursos.
2.  Se você receber um erro **"A referência de objeto não está definida para a instância de um objeto"** ao executar o [script](log-analytics-itsmc-service-manager-script.md), verifique se você inseriu valores válidos na seção **Configuração do Usuário**.
3.  Se você não conseguir criar o namespace de retransmissão do barramento de serviço, certifique-se de que o provedor de recursos necessário está registrado na assinatura. Se ele não estiver registrado, crie manualmente o namespace de retransmissão do barramento de serviço usando o Portal do Azure. Você também pode criá-lo ao [criar a conexão híbrida](log-analytics-itsmc-connections.md#configure-the-hybrid-connection) do Portal do Azure.


## <a name="contact-us"></a>Fale conosco

Em caso de dúvidas ou comentários sobre o Conector de Gerenciamento de Serviço de TI, entre em contato conosco pelo email [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Próximas etapas
[Adicionar produtos/serviços de ITSM ao Conector de Gerenciamento de Serviço de TI](log-analytics-itsmc-connections.md).
