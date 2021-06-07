---
title: Gerenciar logs para um cluster HDInsight - HDInsight do Azure
description: Determine os tipos, tamanhos e as políticas de retenção para arquivos de log de atividade de HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/05/2020
ms.openlocfilehash: 20a7a7392070a6f888add630398516b2f872cd7d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865478"
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>Gerenciar logs para um cluster HDInsight

Um cluster HDInsight produz uma variedade de arquivos de log. Por exemplo, Apache Hadoop e serviços relacionados, como o Apache Spark geram logs de execução do trabalho detalhados. Gerenciamento de arquivos de log é parte da manutenção de um cluster HDInsight íntegro. Também pode haver requisitos normativos para arquivamento de log.  Devido ao número e tamanho dos arquivos de log, otimizar o armazenamento de log e o arquivamento ajuda o gerenciamento de custos do serviço.

Gerenciar logs de cluster HDInsight inclui reter informações sobre todos os aspectos do ambiente de cluster. Essas informações incluem todos os respectivos logs de serviço do Azure, configuração de cluster, informações de execução do trabalho, qualquer estado de erro e outros dados, conforme necessário.

Etapas comuns de gerenciamento de log de HDInsight são:

* Etapa 1: Determinar as políticas de retenção de log
* Etapa 2: Gerenciar logs de configuração de versões de serviço de cluster
* Etapa 3: Gerenciar arquivos de log de execução de trabalho de cluster
* Etapa 4: Calcular custos e tamanhos de armazenamento de volume de log
* Etapa 5: Determinar os processos e as políticas de arquivos de log

## <a name="step-1-determine-log-retention-policies"></a>Etapa 1: Determinar as políticas de retenção de log

A primeira etapa na criação de uma estratégia de gerenciamento de log de cluster HDInsight é obter informações sobre cenários de negócios e requisitos de armazenamento do histórico de execução de trabalho.

### <a name="cluster-details"></a>Detalhes do cluster

Os seguintes detalhes de cluster são úteis para ajudar a coletar informações em sua estratégia de gerenciamento de log. Reúna essas informações de todos os clusters HDInsight que você criou em uma conta específica do Azure.

* Nome do cluster
* Região do cluster e zona de disponibilidade do Azure
* Estado do cluster, incluindo detalhes da última alteração de estado
* Tipo e número de instâncias do HDInsight especificado para o mestre, núcleo e nós de tarefa

Você pode obter a maioria dessas informações de nível superior usando o portal do Azure.  Como alternativa, você pode usar [CLI do Azure](/cli/azure/) para obter informações sobre seus clusters HDInsight:

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Você também pode usar o PowerShell para exibir essas informações.  Para obter mais informações, confira [Gerenciar clusters Apache Hadoop no HDInsight usando o Azure PowerShell](hdinsight-administer-use-powershell.md).

### <a name="understand-the-workloads-running-on-your-clusters"></a>Entender as cargas de trabalho em execução em seus clusters

É importante entender os tipos de carga de trabalho em execução em seu(s) cluster(s) HDInsight para elaborar estratégias apropriadas para cada tipo de log.

* As cargas de trabalho são experimentais (por exemplo, desenvolvimento ou teste) ou de qualidade de produção?
* Com que frequência as cargas de trabalho de qualidade de produção normalmente são executadas?
* Há alguma carga de trabalho com uso intensivo de recursos e/ou de longa execução?
* Alguma das cargas de trabalho usa um conjunto complexo de serviços do Hadoop para o qual vários tipos de logs são produzidos?
* Alguma das cargas de trabalho possui requisitos de linhagem de execução normativos associados?

### <a name="example-log-retention-patterns-and-practices"></a>Práticas e padrões de retenção de log de exemplo

* Considere a possibilidade de manter o controle de linhagem de dados adicionando um identificador para cada entrada de log ou por meio de outras técnicas. Isso permite rastrear a origem dos dados e da operação e seguir os dados em cada etapa para entender sua consistência e a validade.

* Considere como você pode coletar logs de cluster ou de mais de um cluster e agrupá-los para fins como auditoria, monitoramento, planejamento e alertas. Você pode usar uma solução personalizada para acessar e fazer o download dos arquivos de log regularmente, combiná-los e analisá-los para fornecer uma exibição de painel. Você também pode agregar recursos adicionais para alertas de segurança ou de detecção de falha. Você pode criar esses utilitários usando o PowerShell, o HDInsight SDKs ou código que acessa o modelo de implantação clássico do Azure.

* Considere se uma solução ou um serviço de monitoramento seria um benefício útil. O Microsoft System Center fornece um [pacote de gerenciamento de HDInsight](https://systemcenter.wiki/?Get_ManagementPackBundle=Microsoft.HDInsight.mpb&FileMD5=10C7D975C6096FFAA22C84626D211259). Use também ferramentas de terceiros, como o Apache Chukwa e o Ganglia, para coletar e centralizar os logs. Muitas empresas oferecem serviços para monitorar soluções de Big Data baseadas em Hadoop, por exemplo: Centerity, Compuware APM, Sematext SPM e Zettaset Orchestrator.

## <a name="step-2-manage-cluster-service-versions-and-view-logs"></a>Etapa 2: gerenciar versões de serviço de cluster e exibir logs

Um cluster HDInsight típico usa vários serviços e pacotes de software de código aberto (como o Apache HBase, Apache Spark e assim por diante). Para algumas cargas de trabalho, como bioinformática, você talvez precise manter o histórico de log de configuração de serviço além dos logs de execução do trabalho.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Exibir definições de configuração do cluster com a interface de usuário do Ambari

O Apache Ambari simplifica o gerenciamento, configuração e monitoramento de um cluster HDInsight, fornecendo uma interface do usuário da Web e uma API REST. O Ambari está incluído nos clusters HDInsight com base em Linux. Selecione o painel **painel do cluster** na página portal do Azure HDInsight para abrir a página de link painéis do **cluster** .  Em seguida, selecione o **painel do cluster HDInsight** para abrir a interface do usuário do Ambari.  Suas credenciais de logon do cluster serão solicitadas.

Para abrir uma lista de modos de exibição do serviço, selecione o painel de **exibições do Ambari** na página do portal do Azure para HDInsight.  Essa lista varia, dependendo das bibliotecas que você instalou.  Por exemplo, você poderá ver o Gerenciador de Fila do YARN, o Modo de Exibição do Hive e o Modo de Exibição do Tez.  Selecione qualquer link de serviço para ver as informações de serviço e configuração.  A página **Pilha e Versão** da interface do usuário do Ambari fornece informações sobre a configuração de serviços de cluster e o histórico de versão do serviço. Para navegar nessa seção da interface do usuário do Ambari, selecione o menu **Admin** e, em seguida, **Pilhas e Versões**.  Selecione a guia **Versões** para ver as informações de versão do serviço.

:::image type="content" source="./media/hdinsight-log-management/ambari-stack-versions.png" alt-text="Pilha e versões do administrador do Apache Ambari":::

Usando a interface do usuário do Ambari, você pode fazer o download da configuração para um (ou todos) os serviços executados em um host específico (ou nó) no cluster.  Selecione o menu **Hosts** e, em seguida, o link para o host de interesse. Na página do host, selecione o botão **Ações do Host** e, em seguida, **Fazer o download das configurações de cliente**.

:::image type="content" source="./media/hdinsight-log-management/download-client-configs.png" alt-text="Configurações do cliente host de download do Apache Ambari":::

### <a name="view-the-script-action-logs"></a>Exibir os logs de ação de script

As [ações de script](hdinsight-hadoop-customize-cluster-linux.md) do HDInsight executam scripts em um cluster, manualmente ou quando especificado. Por exemplo, as ações de script podem ser usadas para instalar um software adicional no cluster ou alterar definições de configuração dos valores padrão. Os logs de ação de script podem fornecer informações sobre erros ocorridos durante a instalação do cluster e também alterações de definições de configuração que poderiam afetar a disponibilidade e o desempenho do cluster.  Para ver o status de uma ação de script, selecione o botão **ops** na sua interface do usuário do Ambari ou acesse os logs de status na conta de armazenamento padrão. Os logs de armazenamento estão disponíveis em `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE`.

### <a name="view-ambari-alerts-status-logs"></a>Exibir logs de status de alertas do Ambari

O Apache Ambari grava as alterações de status de alerta no `ambari-alerts.log` . O caminho completo é `/var/log/ambari-server/ambari-alerts.log` . Para habilitar a depuração para o log, altere uma propriedade em `/etc/ambari-server/conf/log4j.properties.` alteração e, em seguida, na entrada em `# Log alert state changes` de:

```
log4j.logger.alerts=INFO,alerts

to

log4j.logger.alerts=DEBUG,alerts
```

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>Etapa 3: Gerenciar os arquivos de log de execução de trabalho de cluster

A próxima etapa é examinar os arquivos de log de execução do trabalho para os vários serviços.  Os serviços podem incluir Apache HBase, Apache Spark e muitos outros. Um cluster Hadoop produz um grande número de logs detalhados, portanto, determinar quais logs são úteis (e quais não são) pode ser demorado.  Compreender o sistema de log é importante para o gerenciamento de destino dos arquivos de log.  A imagem a seguir é um arquivo de log de exemplo.

:::image type="content" source="./media/hdinsight-log-management/hdi-log-file-example.png" alt-text="Exemplo de saída do arquivo de log do HDInsight":::

### <a name="access-the-hadoop-log-files"></a>Acessar os arquivos de log do Hadoop

O HDInsight armazena seus arquivos de log no sistema de arquivos de cluster e no armazenamento do Azure. Você pode examinar os arquivos de log no cluster abrindo uma conexão [SSH](hdinsight-hadoop-linux-use-ssh-unix.md) para o cluster e navegando no sistema de arquivos ou usando o portal de status do Hadoop yarn no servidor do nó de cabeçalho remoto. Você pode examinar os arquivos de log no armazenamento do Azure usando qualquer uma das ferramentas que podem acessar e baixar dados do armazenamento do Azure. Os exemplos são [AzCopy](../storage/common/storage-use-azcopy-v10.md), [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer)e o Visual Studio Gerenciador de servidores. Você também pode usar o PowerShell e as bibliotecas de cliente de armazenamento do Azure ou os SDKs do .NET do Azure, para acessar dados no Armazenamento de Blobs do Azure.

O Hadoop executa o trabalho das tarefas como *tentativas de tarefas* em vários nós no cluster. O HDInsight pode iniciar tentativas de tarefa especulativa, encerrando as outras tentativas de tarefa que não são concluídas primeiro. Isso gera atividade significativa que é registrada para o controlador, stderr e arquivos syslog log em interrupções. Além disso, várias tentativas de tarefa estão em execução simultaneamente, mas um arquivo de log só pode exibir os resultados linearmente.

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>Os logs do HDInsight gravados no Armazenamento de Blobs do Azure

Os clusters HDInsight são configurados para gravar logs de tarefas para uma conta de armazenamento de blobs do Azure para qualquer trabalho que é enviado usando os cmdlets do PowerShell do Azure ou as APIs de envio de trabalho .NET.  Se você enviar trabalhos por meio do SSH para o cluster, as informações de log de execução serão armazenadas nas tabelas do Azure conforme discutido na seção anterior.

Além dos arquivos de log do núcleo gerados pelo HDInsight, os serviços instalados como o YARN também geram arquivos de log de execução do trabalho.  O número e o tipo de arquivos de log depende dos serviços instalados.  Os serviços comuns são Apache HBase, Apache Spark e assim por diante.  Investigue os arquivos de execução de log do trabalho para cada serviço para entender de modo geral os arquivos de log disponíveis no seu cluster.  Cada serviço tem seus próprios métodos exclusivos de log e locais para armazenar arquivos de log.  Por exemplo, os detalhes para acessar os arquivos de log do serviço mais comuns (de YARN) são discutidos na seção a seguir.

### <a name="hdinsight-logs-generated-by-yarn"></a>Os logs HDInsight gerados pelo YARN

O YARN agrega logs em todos os contêineres em um nó de trabalho e armazena esses logs como um arquivo de log agregado por nó de trabalho. Esse log é armazenado no sistema de arquivos padrão após a conclusão de um aplicativo. Seu aplicativo deve usar centenas ou milhares de contêineres, mas logs para todos os contêineres que são executados em um nó único de trabalhado sempre são agregados a um único arquivo. Há apenas um log por nó de trabalho usado pelo seu aplicativo. A agregação de Log é habilitada por padrão em clusters HDInsight versão 3.0 e superior. Logs agregados estão localizados no armazenamento padrão do cluster.

```
/app-logs/<user>/logs/<applicationId>
```

Os logs agregados não são diretamente legíveis, pois são gravados em um formato binário TFile indexado pelo contêiner. Use as ferramentas dos logs ResourceManager do YARN ou CLI para exibir esses logs como texto sem formatação para os aplicativos ou contêineres de interesse.

#### <a name="yarn-cli-tools"></a>Ferramentas CLI do YARN

Para usar as ferramentas CLI do YARN, você deve primeiro se conectar ao cluster HDInsight usando o SSH. Especifique as informações `<applicationId>`, `<user-who-started-the-application>`, `<containerId>`, e `<worker-node-address>` durante a execução desses comandos. É possível exibir esses logs como texto sem formatação com um dos seguintes comandos:

```bash
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>IU do ResourceManager YARN

A interface de usuário ResourceManager do YARN é executada no nó de cabeçalho do cluster e pode ser acessada pela interface do usuário web do Ambari. Execute as etapas a seguir para exibir os logs do YARN:

1. Em um navegador web, navegue até `https://CLUSTERNAME.azurehdinsight.net`. Substitua CLUSTERNAME com o nome do cluster HDInsight.
2. Na lista de serviços à esquerda da página, selecione YARN.
3. Na lista suspensa Links rápidos, selecione um dos nós principais do cluster e selecione **logs do ResourceManager**. Você verá uma lista de links para logs do YARN.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>Etapa 4: Calcular custos e tamanhos de armazenamento de volume de log

Depois de concluir as etapas anteriores, você tem uma compreensão dos tipos e os volumes de arquivos de log que seus clusters HDInsight estão produzindo.

Em seguida, analise o volume de dados de log nos locais de armazenamento de chave de registro em um período de tempo. Por exemplo, você pode analisar o volume e o crescimento em períodos de 30-60-90 dias.  Registre as informações em uma planilha ou use outras ferramentas como o Visual Studio, o Azure Storage Explorer ou o Power Query para Excel. ```

Agora você tem informações suficientes para criar uma estratégia de gerenciamento de log para os logs de chave.  Use sua planilha (ou ferramenta de sua escolha) para calcular o crescimento de tamanho dos logs e os custos de armazenamento de logs futuros do Azure.  Considere também quaisquer requisitos de retenção de log para o conjunto de logs que você está examinando.  Agora você pode reprever os custos de armazenamento de log futuros, depois de determinar quais arquivos de log podem ser excluídos (se houver) e quais logs devem ser retidos e arquivados em um armazenamento do Azure menos dispendioso.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>Etapa 5: Determinar os processos e as políticas de arquivos de log

Depois de determinar quais arquivos de log podem ser excluídos, você pode ajustar os parâmetros de registro de log em muitos serviços Hadoop para excluir automaticamente arquivos de log após um período de tempo especificado.

Para alguns arquivos de log, você pode usar um método de arquivamento de arquivos de log mais barato. Para Azure Resource Manager logs de atividades, você pode explorar essa abordagem usando o portal do Azure.  Configure o arquivamento dos logs do Gerenciador de recursos selecionando o link **log de atividades** no portal do Azure para sua instância do HDInsight.  No topo da página de pesquisa de Log de atividades, selecione o item de menu **Exportar** para abrir o painel **Exportar log de atividades**.  Preencha a assinatura, região, se deve ser exportado para uma conta de armazenamento e o número de dias para manter os logs. Nesse mesmo painel, você também pode indicar se deve ser exportado para um hub de eventos.

:::image type="content" source="./media/hdinsight-log-management/hdi-export-log-files.png" alt-text="Visualização de portal do Azure exportar log de atividades":::

Como alternativa, você pode gerar script de arquivamento de log com o PowerShell.  Para obter um exemplo de script do PowerShell, consulte [Arquivar logs de Automação do Azure para o Armazenamento de Blobs do Azure](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8).

### <a name="accessing-azure-storage-metrics"></a>Acessando as métricas do armazenamento do Azure

O armazenamento do Azure pode ser configurado para registrar operações de armazenamento e acesso. Você pode usar esses logs muito detalhados para monitoramento e planejamento de capacidade e para solicitações de auditoria para o armazenamento. As informações registradas incluem detalhes de latência, permitindo que você monitorar e ajustar o desempenho de suas soluções.
Você pode usar o SDK do .NET para Hadoop para examinar os arquivos de log gerados para o armazenamento do Azure que contém os dados de um cluster HDInsight.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>Controlar o tamanho e o número de índices de backup de arquivos de log antigos

Para controlar o tamanho e o número de arquivos de log retidos, defina as seguintes propriedades do `RollingFileAppender`:

* `maxFileSize` é o tamanho crítico do arquivo, acima do qual o arquivo é revertido. O valor padrão é 10 MB.
* `maxBackupIndex` especifica o número de arquivos de backup a ser criado, padrão 1.

### <a name="other-log-management-techniques"></a>Outras técnicas de gerenciamento de log

Para evitar a indisponibilidade de espaço em disco, você pode usar algumas ferramentas do sistema operacional, como o [logrotate](https://linux.die.net/man/8/logrotate) , para gerenciar a manipulação de arquivos de log. Você pode configurar `logrotate` para executar diariamente, comprimir arquivos de log e remover arquivos antigos. Seu método depende dos seus requisitos, como quanto tempo manter os arquivos de log em nós locais.  

Você também pode verificar se o log de depuração está habilitado para um ou mais serviços, o que aumenta o tamanho do log de saída.  

Para coletar os logs de todos os nós para um local central, você pode criar um fluxo de dados, como a inclusão de todas as entradas de log em Solr.

## <a name="next-steps"></a>Próximas etapas

* [Monitoramento e prática de registro em log para HDInsight](/previous-versions/msp-n-p/dn749790(v=pandp.10))
* [Acessar Apache Hadoop logs de aplicativo do YARN no HDInsight baseado em Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Como controlar o tamanho dos arquivos de log para vários componentes do Apache Hadoop](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)
