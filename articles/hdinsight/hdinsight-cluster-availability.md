---
title: Como monitorar a disponibilidade do cluster com Ambari e o Azure Monitor registra em log
description: Saiba como usar o Ambari e registra em log do Azure Monitor para monitorar a disponibilidade e integridade do cluster.
keywords: monitoramento, ambari, monitor, o log analytics, alerta, disponibilidade e integridade
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: tyfox
ms.openlocfilehash: 195999ba685828042fc958e8aed7e67bad694657
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786555"
---
# <a name="how-to-monitor-cluster-availability-with-ambari-and-azure-monitor-logs"></a>Como monitorar a disponibilidade do cluster com Ambari e o Azure Monitor registra em log

Os clusters de HDInsight incluem o Apache Ambari, que fornece informações de integridade em um relance e alertas predefinidos, bem como integração de logs do Azure Monitor, que fornece logs e métricas que podem ser consultadas, bem como alertas configuráveis.

Este documento mostra como usar essas ferramentas para monitorar seu cluster e percorre alguns exemplos para configurar um alerta do Ambari, taxa de disponibilidade do nó de monitoramento e criação de um alerta do Monitor do Azure que é acionado quando uma pulsação não foi recebida de um ou mais nós em cinco horas.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>painel

O painel do Ambari pode ser acessado clicando o **Ambari doméstica** link na **painéis do Cluster** seção da folha visão geral do HDInsight no portal do Azure conforme mostrado abaixo. Como alternativa, ele pode ser acessado digitando a seguinte URL em um navegador [https://\<clustername\>. azurehdinsight.net](https://clustername.azurehdinsight.net/)

![Exibição de portal de recursos do HDInsight](media/hdinsight-cluster-availability/portal-overview.png)

Em seguida, você será solicitado um nome de usuário de logon de cluster e a senha. Insira as credenciais você escolheu ao criar o cluster.

Em seguida, você será levado ao painel do Ambari, que contém os widgets que mostram um punhado de métricas para lhe dar uma rápida visão geral da integridade do seu cluster HDInsight. Esses widgets mostram métricas como o número de DataNodes (nós de trabalho) e JournalNodes (nó do zookeeper), ao vivo NameNodes (nós de cabeçalho) de tempo de atividade, como bem métricas específicas para determinados tipos de cluster, como o tempo de atividade de ResourceManager do YARN para clusters Spark e Hadoop.

![Painel do Ambari](media/hdinsight-cluster-availability/ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Hosts – Exibir status do nó individual

Você também pode exibir informações de status de nós individuais. Clique o **Hosts** tab para exibir uma lista de todos os nós no cluster e ver informações básicas sobre cada nó. A marca de seleção verde à esquerda de cada nome de nó indica que todos os componentes estão funcionando no nó. Se um componente estiver inativo em um nó, você verá um triângulo de alerta vermelho em vez da marca de seleção verde.

![Exibição de hosts do Ambari](media/hdinsight-cluster-availability/ambari-hosts.png)

Você pode clicar na **nome** de um nó para exibir métricas mais detalhadas do host para um nó específico. Essa exibição mostra a status/disponibilidade de cada componente individual.

![Exibição de nó único de hosts do Ambari](media/hdinsight-cluster-availability/ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Alertas do Ambari

Ambari também oferece vários alertas configuráveis que podem fornecer notificação de determinados eventos. Quando os alertas são disparados, eles são mostrados no canto superior esquerdo do Ambari em uma notificação de vermelho que contém o número de alertas. Clicar desse selo mostra uma lista de alertas atuais.

![Contagem de alertas do Ambari](media/hdinsight-cluster-availability/ambari-alerts.png)

Para exibir uma lista de definições de alertas e seus status, clique o **alertas** guia, conforme mostrado abaixo.

![Exibição de definições de alertas do Ambari](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari oferece muitos alertas predefinidos, relacionadas à disponibilidade, incluindo:

| Nome do alerta                        | DESCRIÇÃO                                                                                                                                                                           |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Resumo de integridade DataNode           | Esse nível de serviço de alerta é disparada se não houver DataNodes não íntegro                                                                                                                |
| Integridade da disponibilidade alta NameNode | Esse nível de serviço de alerta é disparada se o Active Directory NameNode ou NameNode em espera não estiver executando.                                                                              |
| Porcentagem JournalNodes disponíveis    | Este alerta é disparado se o número de baixo JournalNodes do cluster for maior que o limite crítico configurado. Ele agrega os resultados das verificações de processo JournalNode. |
| Porcentagem DataNodes disponíveis       | Este alerta é disparado se o número de baixo DataNodes do cluster for maior que o limite crítico configurado. Ele agrega os resultados das verificações de processo DataNode.       |

Uma lista completa de Ambari alertas que monitoram ajuda a disponibilidade de um cluster pode ser encontrada [aqui](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui),

Para exibir detalhes de um alerta ou modificar critérios, clique no **nome** do alerta. Levar **resumo de integridade DataNode** como exemplo. Você pode ver uma descrição do alerta, bem como os critérios específicos que irão disparar um alerta de 'aviso' ou 'crítico' e o intervalo de verificação para os critérios. Para editar a configuração, clique o **editar** botão no canto superior direito da caixa de configuração.

![Configuração de alertas do Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Aqui, você pode editar a descrição e, mais importante, a verificação de intervalo e limites para alertas de aviso ou críticos.

![Exibição de edição de configuração de alertas do Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

Neste exemplo, você poderia fazer DataNodes não íntegro 2 disparam um alerta crítico e 1 não íntegro DataNode disparar somente um aviso. Clique em **salvar** quando você terminar edição.

### <a name="email-notifications"></a>Notificações por email

Você também pode configurar notificações por email para alertas do Ambari. Para fazer isso, quando on a **alertas** , clique o **ações** botão no canto superior esquerdo, em seguida, **gerenciar notificações.**

![Ação de notificações de gerenciar Ambari](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Uma caixa de diálogo para gerenciar notificações de alerta será aberta. Clique o **+** na parte inferior da caixa de diálogo e preencha os campos obrigatórios para fornecer o Ambari com o email detalhes do servidor do qual enviar emails.

> [!TIP]
> Configurando o Ambari notificações por email podem ser uma boa maneira de receber alertas em um só lugar, ao gerenciar vários clusters de HDInsight.

## <a name="azure-monitor-logs-integration"></a>Integração de logs do Azure Monitor

O Azure Monitor registra dados de habilita gerados por vários recursos, como clusters de HDInsight, para ser coletados e agregados em um só lugar para obter uma experiência unificada de monitoramento.

Como pré-requisito, você precisará de um espaço de trabalho de análise de Log para armazenar os dados coletados. Se você não tiver criado uma, você pode seguir as instruções aqui: [Criar um espaço de trabalho do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Habilitar a integração de logs do HDInsight do Azure Monitor

Na página do recurso de cluster HDInsight no portal do, clique o **Operations Management Suite** folha. Em seguida, clique em **habilitar** e selecione seu espaço de trabalho do Log Analytics na lista suspensa.

![Folha de HDInsight Operations Management Suite](media/hdinsight-cluster-availability/portal-enable-oms.png)

### <a name="query-metrics-and-logs-tables-in-the-logs-blade"></a>Consultar tabelas de logs e métricas na folha de logs

Depois que a integração de log do Azure Monitor está habilitada (Isso pode levar alguns minutos), navegue até sua **espaço de trabalho do Log Analytics** recursos e clique no **Logs** folha

![Folha de logs de espaço de trabalho do log Analytics](media/hdinsight-cluster-availability/portal-logs.png)

O **Logs** folha lista diversos exemplos de consultas, como:

| Nome da consulta                      | DESCRIÇÃO                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Disponibilidade de computadores hoje mesmo    | O número de computadores a cada hora de envio de logs, de gráfico                     |
| Pulsações de lista                 | Listar todas as pulsações do computador da última hora                           |
| Última pulsação de cada computador | Mostrar a última pulsação enviada por cada computador                             |
| Computadores não disponíveis           | Listar todos os computadores conhecidos que não enviou uma pulsação nas últimas 5 horas |
| Taxa de disponibilidade               | Calcular a taxa de disponibilidade de cada computador conectado                |

Por exemplo, execute as **taxa de disponibilidade** exemplo de consulta clicando **executar** nessa consulta, conforme mostrado na captura de tela acima. Isso mostrará a taxa de disponibilidade de cada nó no cluster como uma porcentagem. Se você tiver habilitado a vários clusters de HDInsight enviar métricas para o mesmo espaço de trabalho do Log Analytics, você verá a taxa de disponibilidade para todos os nós nesses clusters exibidos.

![Log Analytics workspace logs folha 'taxa de disponibilidade' exemplo de consulta](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE] 
> Taxa de disponibilidade é medida em um período de 24 horas, portanto, o cluster será necessário ser executado pelo menos 24 horas antes de você ver as taxas de disponibilidade precisos.

Você pode fixar esta tabela em um painel compartilhado clicando **Pin** no canto superior direito. Se você não tiver qualquer graváveis painéis compartilhados, você pode ver como criar uma aqui: [Criar e compartilhar painéis no portal do Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard).

### <a name="azure-monitor-alerts"></a>Alertas do Azure Monitor

Você também pode configurar alertas do Azure Monitor que irá disparar quando o valor de uma métrica ou os resultados de uma consulta atendem a determinadas condições. Por exemplo, vamos criar um alerta para enviar um email quando um ou mais nós ainda não enviou uma pulsação nas cinco horas (ou seja, é provável que não esteja disponível).

Dos **Logs** folha, execute o **computadores não disponíveis** exemplo de consulta clicando em **executar** nessa consulta, conforme mostrado abaixo.

![Log Analytics workspace logs folha 'computadores não disponíveis' exemplo de consulta](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Se todos os nós estiverem disponíveis, essa consulta deve retornar 0 resultados por enquanto. Clique em **nova regra de alerta** para começar a configurar o alerta para esta consulta.

![Log Analytics workspace nova regra de alerta](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Há três componentes a um alerta: o *resource* para o qual criar a regra (o espaço do Log Analytics neste caso), o *condição* para disparar o alerta e o *grupos de ação*  que determinam o que acontecerá quando o alerta for disparado.

Clique o **título da condição**, conforme mostrado abaixo, para terminar de configurar a lógica de sinal.

![Condição de regra de alerta](media/hdinsight-cluster-availability/portal-condition-title.png)

Isso abrirá o **lógica de sinal configurar** folha.

Defina as **lógica de alerta** seção da seguinte maneira:

*Com base em: Número de resultados, condição: Maior que limite: 0.*

Como esta consulta retorna apenas nós não está disponíveis como resultado, se o número de resultados é cada vez maior que 0, o alerta deve ser disparado.

No **avaliadas com base no** seção, defina as **período** e **frequência** com base em quantas vezes você deseja verificar para nós não está disponíveis.

Observe que, com a finalidade deste alerta, você desejará Certifique-se de **período = frequência.** Para obter mais informações sobre o período, frequência e outros parâmetros de alerta podem ser encontradas [aqui](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Clique em **feito** quando tiver terminado de configurar a lógica de sinal.

![Regra de alerta configurar lógica de sinal](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Se você ainda não tiver um grupo de ação existente, clique em **criar novo** sob o **grupos de ação** seção.

![Novo grupo de ação de regra de alerta](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Isso abrirá o **adicionar grupo de ações** folha. Escolha uma **nome do grupo de ação**, **nome curto**, **assinatura**, e **grupo de recursos.** Sob o **ações** , escolha um **nome da ação** e selecione **Email/SMS/Push/voz** como o **tipo de ação.**

> [!NOTE]
> Há várias outras ações que um alerta pode disparar além de uma Email/SMS/Push/voz, como uma função do Azure, aplicativo lógico, Webhook, ITSM e Runbook de automação. [Saiba Mais.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Isso abrirá o **Email/SMS/Push/voz** folha. Escolha uma **nome** para o destinatário **verificar** o **Email** caixa e, em seguida, digite um endereço de email ao qual você deseja que o alerta seja enviado. Clique em **Okey** na **Email/SMS/Push/voz** folha, em seguida, no **adicionar grupo de ações** folha para terminar de configurar o grupo de ações.

![Regra de alerta adicionar grupo de ações](media/hdinsight-cluster-availability/portal-add-action-group.png)

Depois de fechar esses blades, você deverá ver seu grupo de ação listado sob o **grupos de ação** seção. Por fim, conclua o **detalhes do alerta** seção digitando um **nome da regra de alerta** e **descrição** e escolhendo uma **gravidade**.
Clique em **criar regra de alerta** para concluir.

![Criar regra de alerta de término](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> A capacidade de especificar **gravidade** é uma ferramenta poderosa que pode ser usada ao criar vários alertas. Por exemplo, você poderia criar um alerta para disparar um aviso (grav 1) se um único nó principal ficar inativo e outro alerta que gera crítico (Sev 0) no evento improvável de que os dois nós de cabeçalho ficam inativos.

Quando a condição para este alerta é atendida, o alerta será acionado e você receberá um email com os detalhes do alerta como este:

![Email de alerta do Monitor do Azure](media/hdinsight-cluster-availability/alert-email.png)

Você também pode exibir todos os alertas que tiverem sido disparados, agrupados por severidade, indo para o **alertas** folha no seu **espaço de trabalho do Log Analytics**.

![Alertas de espaço de trabalho de análise de log](media/hdinsight-cluster-availability/portal-alerts.png)

Clicar em um agrupamento de gravidade (ou seja, **grav 1,** conforme destacado acima) mostrará os registros para todos os alertas de que gravidade que tiverem sido disparados como abaixo:

![Alertas do log Analytics workspace grav 1](media/hdinsight-cluster-availability/portal-alerts-sev-1.png)

## <a name="next-steps"></a>Próximas etapas
- [Disponibilidade e confiabilidade dos clusters do Apache Hadoop no HDInsight](hdinsight-high-availability-linux.md)
