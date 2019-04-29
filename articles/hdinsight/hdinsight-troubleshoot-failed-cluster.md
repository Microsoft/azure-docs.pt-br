---
title: Solucionar problemas de um cluster de HDInsight lento ou falhando - HDInsight do Azure
description: Diagnosticar e solucionar problemas de um cluster HDInsight lento ou falhando.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: 785eac065e10c64b99839ab8667e9b613f62aeb9
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764031"
---
# <a name="troubleshoot-a-slow-or-failing-hdinsight-cluster"></a>Solucionar problemas de um cluster HDInsight lento ou falhando

Se um cluster HDInsight está em execução lenta ou falhando com um código de erro, há várias opções de solução de problemas. Se os trabalhos estão demorando mais tempo do que o esperado ou se os tempos de resposta estão geralmente lentos, pode haver falhas de upstream do seu cluster, como os serviços em que o cluster é executado. No entanto, a causa mais comum desses gargalos é dimensionamento insuficiente. Quando você cria um novo cluster HDInsight, selecionar as devidas [tamanhos de máquina virtual](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters).

Para diagnosticar um cluster lento ou falhando, colete informações sobre todos os aspectos do ambiente, como Serviços do Azure associados, configuração de cluster e informações de execução do trabalho. Um diagnóstico útil é tentar reproduzir o estado de erro em outro cluster.

* Etapa 1: Colete dados sobre o problema.
* Etapa 2: Valide o ambiente de cluster do HDInsight.
* Etapa 3: Exiba a integridade do cluster.
* Etapa 4: Examine as versões e a pilha de ambiente.
* Etapa 5: Examine os arquivos de log do cluster.
* Etapa 6: Verifique as definições de configuração.
* Etapa 7: Reproduza a falha em um cluster diferente.

## <a name="step-1-gather-data-about-the-issue"></a>Etapa 1: Coletar dados sobre o problema

O HDInsight fornece várias ferramentas que você pode usar para identificar e solucionar problemas com clusters. As etapas a seguir orientam você através dessas ferramentas e fornecem sugestões para identificar o problema.

### <a name="identify-the-problem"></a>Identificar o problema

Para ajudar a identificar o problema, considere as seguintes perguntas:

* O que eu esperava que acontecesse? Em vez disso, o que aconteceu?
* Quanto tempo o processo levou para ser executado? Em quanto tempo ele deveria ser executado?
* Minhas tarefas são sempre executadas lentamente neste cluster? A execução foi mais rápida em um outro cluster?
* Quando este problema ocorreu pela primeira vez? Com que frequência isso tem ocorrido?
* Houve alguma alteração na minha configuração do cluster?

### <a name="cluster-details"></a>Detalhes do cluster

Informações importantes do cluster incluem:

* Nome do cluster.
* A região do cluster - verificar [interrupções de região](https://azure.microsoft.com/status/).
* Tipo e versão do cluster HDInsight.
* Tipo e número de instâncias do HDInsight especificado para os nós de cabeçalho e de trabalho.

O Portal do Azure pode fornecer essas informações:

![Informações do HDInsight do Portal do Azure](./media/hdinsight-troubleshoot-failed-cluster/portal.png)

Você também pode usar [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Outra opção é usar o PowerShell. Para obter mais informações, consulte [Gerenciar clusters Apache Hadoop no HDInsight com o Azure PowerShell](hdinsight-administer-use-powershell.md).

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>Etapa 2: Validar o ambiente do cluster HDInsight

Cada cluster HDInsight baseia-se em vários serviços do Azure e em softwares de código-fonte aberto como Apache HBase e Apache Spark. Os clusters HDInsight também podem chamar em outros serviços do Azure, como as Redes Virtuais do Azure.  Uma falha do cluster pode ser causada por qualquer um dos serviços em execução no cluster ou por um serviço externo.  Uma alteração de configuração do serviço do cluster também pode causar falha no cluster.

### <a name="service-details"></a>Detalhes do serviço

* Verifique as versões de lançamento da biblioteca de código-fonte aberto.
* Verificar se há [interrupções de serviço do Azure](https://azure.microsoft.com/status/).  
* Verifique se há limites de uso do serviço do Azure. 
* Verifique a configuração de sub-rede da rede Virtual do Azure.  

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Exibir definições de configuração do cluster com a interface de usuário do Ambari

O Apache Ambari fornece gerenciamento e monitoramento de um cluster HDInsight com uma interface do usuário da Web e uma API REST. O Ambari está incluído nos clusters HDInsight com base em Linux. Selecione o **Painel do Cluster** na página do HDInsight no Portal do Azure.  Selecione o **painel do cluster HDInsight** para abrir a interface de usuário do Ambari e insira as credenciais de logon do cluster.  

![Interface do usuário do Ambari](./media/hdinsight-troubleshoot-failed-cluster/ambari-ui.png)

Para abrir uma lista de modos de exibição do serviço, selecione **Modos de exibição do Ambari** na página do Portal do Azure.  Essa lista depende de quais bibliotecas estão instaladas. Por exemplo, você poderá ver o Gerenciador de Fila do YARN, o Modo de Exibição do Hive e o Modo de Exibição do Tez.  Selecione um link de serviço para ver informações de configuração e serviço.

#### <a name="check-for-azure-service-outages"></a>Verificar interrupções de serviço do Azure

O HDInsight depende de vários serviços do Azure. Ele executa servidores virtuais no Azure HDInsight, armazena dados e scripts no Armazenamento de Blobs do Azure ou no Azure Data Lake Storage, e indexa arquivos de log no Armazenamento de Tabelas do Azure. Interrupções nesses serviços, embora raros, podem causar problemas no HDInsight. Se você encontrar lentidão inesperada ou falhas no cluster, verifique o [Painel de Status do Azure](https://azure.microsoft.com/status/). O status de cada serviço é listado por região. Verifique a região do seu cluster e também regiões para todos os serviços relacionados.

#### <a name="check-azure-service-usage-limits"></a>Verifique os limites de uso do serviço do Azure

Se você estiver iniciando um cluster grande ou iniciou simultaneamente vários clusters, um cluster pode falhar caso você tenha excedido o limite do serviço do Azure. Os limites de serviço variam, dependendo de sua assinatura do Azure. Para saber mais, confira [Assinatura e limites de serviço, cotas e restrições do Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits).
Você pode solicitar que a Microsoft aumente o número de recursos de HDInsight disponíveis (por exemplo, núcleos de VMs e instâncias de VM) com uma [solicitação de aumento de cota de núcleos do Gerenciador de Recursos](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

#### <a name="check-the-release-version"></a>Verificar a versão de lançamento

Compare a versão de cluster com a versão mais recente do HDInsight. Cada versão do HDInsight inclui aprimoramentos, como novos aplicativos, recursos, patches e correções de bugs. O problema que está afetando o seu cluster pode ter sido corrigido na versão mais recente. Se possível, execute novamente o cluster usando a versão mais recente do HDInsight e de bibliotecas associadas como Apache HBase e Apache Spark, entre outras.

#### <a name="restart-your-cluster-services"></a>Reiniciar os serviços de cluster

Se houver lentidão no cluster, considere reiniciar os serviços por meio da interface do usuário do Ambari ou da CLI clássica do Azure. O cluster pode estar encontrando erros transitórios e reiniciar é a maneira mais rápida de estabilizar seu ambiente e possivelmente melhorar o desempenho.

## <a name="step-3-view-your-clusters-health"></a>Etapa 3: Verificar a integridade do cluster

Os clusters HDInsight são compostos de tipos diferentes de nós executados em instâncias de máquina virtual. Cada nó pode ser monitorado para privação de recursos, problemas de conectividade de rede e outros problemas que podem reduzir a velocidade do cluster. Cada cluster contém dois nós principais e a maioria dos tipos de cluster contém uma combinação de nós de trabalho e de borda. 

Para obter uma descrição dos vários nós que cada tipo de cluster usa, consulte [Configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka e muito mais](hdinsight-hadoop-provision-linux-clusters.md).

As seções a seguir descrevem como verificar a integridade de cada nó e do cluster geral.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>Obter um instantâneo da integridade de cluster usando o painel de interface do usuário do Ambari

O [painel de interface de usuário do Ambari](#view-cluster-configuration-settings-with-the-ambari-ui) (`https://<clustername>.azurehdinsight.net`) fornece uma visão geral da integridade do cluster, como tempo de atividade, memória, rede e uso da CPU, uso do disco HDFS e assim por diante. Use a Seção de hosts do Ambari para exibir recursos em um nível de host. Você também pode interromper e reiniciar serviços.

### <a name="check-your-webhcat-service"></a>Verificar seu serviço WebHCat

Um cenário comum para falha nos trabalhos no Apache Hive, Apache Pig ou Apache Sqoop é uma falha com o serviço do [WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md) (ou *Templeton*). O WebHCat é uma interface REST para execução de trabalho remota, como o Hive, Pig e MapReduce. O WebHCat converte as solicitações de envio de trabalho em aplicativos de YARN do Apache Hadoop e retorna um status derivado do status do aplicativo YARN.  As seções a seguir descrevem códigos comuns de status HTTP do WebHCat.

#### <a name="badgateway-502-status-code"></a>BadGateway (código de status 502)

Essa é uma mensagem genérica de nós de gateway e é o código de status de falha mais comum. Uma causa possível para isso é o serviço WebHCat estar inativo no nó principal ativo. Para verificar essa possibilidade, use o seguinte comando CURL:

```bash
curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

O Ambari exibe um alerta que mostra os hosts em que o serviço WebHCat está inativo. Você pode tentar fazer backup do serviço WebHCat reiniciando o serviço em seu host.

![Reiniciar o Servidor WebHCat](./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat.png)

Se um servidor WebHCat ainda não aparecer, verifique o log de operações de mensagens de falha. Para obter mais informações, verifique os arquivos `stderr` e `stdout` referenciados no nó.

#### <a name="webhcat-times-out"></a>Tempo limite do WebHCat

Um Gateway de HDInsight expira respostas que levam mais de dois minutos, retornando `502 BadGateway`. O WebHCat consulta serviços do YARN para status de trabalhos, e se o YARN demora mais que dois minutos para responder, a solicitação pode expirar.

Nesse caso, examine os seguintes logs no `/var/log/webhcat` diretório:

* **webhcat.log** é o log de log4j nos quais o servidor grava logs
* **webhcat console.log** é o stdout do servidor quando iniciado
* **webhcat-console-Error.log** é o stderr do processo do servidor

> [!NOTE]  
> Cada `webhcat.log` é rodado diariamente, gerando arquivos denominados `webhcat.log.YYYY-MM-DD`. Selecione o arquivo apropriado para o intervalo de tempo que você está investigando.

As seções a seguir descrevem algumas das possíveis causas de expiração de tempo limite do WebHCat.

##### <a name="webhcat-level-timeout"></a>Tempo limite do nível do WebHCat

Quando o WebHCat está sob carga, com mais de 10 soquetes abertos, leva mais tempo para estabelecer novas conexões de soquete, o que pode resultar na expiração do tempo limite. Para listar as conexões de rede de e para o WebHCat, use `netstat` no nó principal ativado atual:

```bash
netstat | grep 30111
```

30111 é a porta em que o WebHCat escuta. O número de soquetes abertos deve ser inferior a 10.

Se não houver nenhum soquete aberto, o comando anterior não produz nenhum resultado. Para verificar se o Templeton está ativo e escutando na porta 30111, use:

```bash
netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>Tempo limite do nível de YARN

O Templeton chama o YARN para executar trabalhos, e a comunicação entre o Templeton e o YARN pode fazer com que um tempo limite seja atingido.

No nível do YARN, há dois tipos de tempos limite:

1. Enviar um trabalho do YARN pode levar tempo suficiente para fazer com que um tempo limite expire.

    Se você abrir o `/var/log/webhcat/webhcat.log` arquivo de log e pesquisar "trabalho em fila", você poderá ver várias entradas em que o tempo de execução é excessivamente longo (> 2000 ms), com entradas mostrando tempos de espera crescentes.

    O tempo para os trabalhos em fila continua a aumentar porque a taxa de envio de novos trabalhos é maior do que a taxa de conclusão de trabalhos antigos. Quando a memória de YARN atinge o nível de 100% de uso, a *fila joblauncher* não pode usar emprestada a capacidade da *fila padrão*. Portanto, nenhum novo trabalho pode ser aceito na fila de joblauncher. Esse comportamento pode fazer com que o tempo de espera se torne cada vez maior, causando um erro de tempo limite que é geralmente seguido por muitos outros.

    A imagem a seguir mostra a fila joblauncher com um uso excessivo de 714.4%. Isso é aceitável desde que ainda haja capacidade livre para ser emprestada na fila padrão. No entanto, quando o cluster é totalmente utilizado e a memória do YARN está na capacidade máxima de 100%, novos trabalhos devem aguardar, fazendo com os tempos limite expirem.

    ![Fila de joblauncher](./media/hdinsight-troubleshoot-failed-cluster/joblauncher-queue.png)

    Há duas maneiras de resolver esse problema: reduzindo a velocidade de envio de novos trabalhos ou aumentando a velocidade de consumo de trabalhos antigos, aumentando o cluster.

2. O processamento do YARN pode levar algum tempo, o que pode fazer com os tempos limite expirem.

    * Listar todos os trabalhos: Essa é uma chamada demorada. Essa chamada enumera os aplicativos do ResourceManager do YARN e para cada aplicativo concluído, obtém o status do JobHistoryServer do YARN. Com números maiores de trabalhos, essa chamada pode expirar o tempo limite.

    * Listar trabalhos com mais de sete dias: O HDInsight do JobHistoryServer do YARN está configurado para reter informações de trabalhos concluídos por sete dias (`mapreduce.jobhistory.max-age-ms` valor). Tentar enumerar resultados de trabalhos limpos em um tempo limite.

Para diagnosticar esses problemas:

1. Determine o intervalo de tempo UTC para solucionar problemas
2. Selecione os `webhcat.log` arquivo(s) apropriado(s)
3. Procure por mensagens de AVISO e ERRO durante esse período

#### <a name="other-webhcat-failures"></a>Outras falhas do WebHCat

1. Código de status HTTP 500

    Na maioria dos casos em que o WebHCat retorna o código de status HTTP 500, a mensagem de erro contém detalhes sobre a falha. Caso contrário, examine `webhcat.log` as mensagens de AVISO e ERRO.

2. Falhas de trabalho

    Pode haver casos onde as interações com o WebHCat são bem-sucedidas, mas os trabalhos estão falhando.

    O Templeton coleta a saída do console de trabalho como `stderr` em `statusdir`, o que geralmente é útil para solução de problemas. `stderr` contém o identificador do aplicativo YARN da consulta real.

## <a name="step-4-review-the-environment-stack-and-versions"></a>Etapa 4: Revisar as versões e a pilha de ambiente

A página da **Pilha e Versão** da interface de usuário do Ambari fornece informações sobre configuração dos serviços de cluster e histórico de versão do serviço.  Versões incorretas da biblioteca de serviço do Hadoop podem ser uma causa de falha no cluster.  Na interface de usuário do Ambari, selecione o menu **Admin** e, em seguida, **Pilhas e Versões**.  Selecione a guia **Versões** na página para ver as informações de versão do serviço:

![Pilha e Versões](./media/hdinsight-troubleshoot-failed-cluster/stack-versions.png)

## <a name="step-5-examine-the-log-files"></a>Etapa 5: Examinar os arquivos de log

Há muitos tipos de logs que são gerados a partir dos muitos serviços e componentes que compõem um cluster HDInsight. [Arquivos de log do WebHCat](#check-your-webhcat-service) são descritos anteriormente. Há vários outros arquivos de log úteis que você pode investigar para reduzir problemas com seu cluster, conforme descrito nas seções a seguir.

* Os clusters HDInsight consistem de vários nós, a maioria dos quais é responsável por executar trabalhos enviados. Os trabalhos são executados simultaneamente, mas os arquivos de log só podem exibir os resultados linearmente. O HDInsight executa novas tarefas, encerrando outras que não foram concluídas antes. Todos essa atividade é registrada nos arquivos `stderr` e `syslog`.

* Os arquivos de log de ação de script mostram erros ou mudanças inesperadas de configuração durante o processo de criação do cluster.

* Os logs de etapa do Hadoop identificam trabalhos do Hadoop iniciados como parte de uma etapa que contém erros.

### <a name="check-the-script-action-logs"></a>Verifique os logs de ação de script

As [ações de script](hdinsight-hadoop-customize-cluster-linux.md) do HDInsight executam scripts no cluster manualmente ou quando especificado. Por exemplo, as ações de script podem ser usadas para instalar um software adicional no cluster ou alterar definições de configuração dos valores padrão. Verificar os logs de ação de script pode fornecer informações sobre erros ocorridos durante a instalação e configuração de cluster.  Você pode visualizar o status de uma ação de script selecionando o botão **ops** na interface do usuário do Ambari ou acessando os logs da conta de armazenamento padrão.

Os logs de ação de script permanecem no `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` diretório.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>Exibir logs de HDInsight usando os Links Rápidos do Ambari

A interface do usuário do HDInsight Ambari inclui uma série de seções de **Links rápidos**.  Para acessar os links de log para um serviço específico em seu cluster HDInsight, abra a interface de usuário do Ambari para seu cluster e, em seguida, selecione o link do serviço na lista à esquerda. Selecione os **Links rápidos** suspensos, em seguida, o nó do HDInsight de interesse e, em seguida, selecione o link para seu log associado.

Por exemplo, para logs HDFS:

![Links rápidos do Ambari para Arquivos de log](./media/hdinsight-troubleshoot-failed-cluster/quick-links.png)

### <a name="view-hadoop-generated-log-files"></a>Exibir arquivos de log gerados no Hadoop

Um cluster HDInsight gera logs que são gravados nas tabelas do Azure e no Armazenamento de Blobs do Azure. O YARN cria seus próprios logs de execução. Para obter mais informações, consulte [Gerenciar logs para um cluster HDInsight](hdinsight-log-management.md#access-the-hadoop-log-files).

### <a name="review-heap-dumps"></a>Examinar os despejos de heap

Os despejos de heap contêm um instantâneo da memória do aplicativo, incluindo os valores das variáveis no momento, que são úteis para diagnosticar problemas que ocorrem no tempo de execução. Para obter mais informações, consulte [Habilitar despejos de heap para serviços de Apache Hadoop em HDInsight com base em Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md).

## <a name="step-6-check-configuration-settings"></a>Etapa 6: Verificar as definições de configuração

Os clusters HDInsight são pré-configurados com configurações padrão para serviços relacionados como Hadoop, Hive, HBase e assim por diante. Dependendo do tipo do cluster, de sua configuração de hardware, do número de nós, dos tipos de trabalhos que você está executando e dos dados com que você está trabalhando (e de como esses dados estão sendo processados), talvez você precise otimizar sua configuração.

Para obter instruções detalhadas de como otimizar configurações de desempenho para a maioria dos cenários, consulte [Otimizar configurações de cluster com o Apache Ambari](hdinsight-changing-configs-via-ambari.md). Ao usar o Spark, consulte [Otimizar desempenho de trabalhos do Apache Spark](spark/apache-spark-perf.md). 

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>Etapa 7: Reproduzir a falha em um cluster diferente

Para ajudar a diagnosticar a origem de um erro do cluster, inicie um novo cluster com a mesma configuração e reenvie uma por uma as etapas do trabalho. Verifique os resultados de cada etapa antes de processar a próxima. Esse método oferece a oportunidade de corrigir e executar novamente uma única etapa com falha. Esse método também tem a vantagem de carregar os dados de entrada apenas uma vez.

1. Crie um novo cluster de teste com a mesma configuração do cluster com falha.
2. Envie a primeira etapa de trabalho para o cluster de teste.
3. Quando a etapa concluir o processamento, verifique se há erros nos arquivos de log de etapa. Conecte-se ao nó principal do cluster de teste e visualize os arquivos de log. Os arquivos de log de etapa aparecem somente depois que a etapa é executada por algum tempo, se é concluída, ou se falha.
4. Se a primeira etapa for bem-sucedida, execute a próxima etapa. Se houver erros, investigue o erro nos arquivos de log. Se foi um erro em seu código, faça a correção e execute novamente a etapa.
5. Continue até que todas as etapas sejam executadas sem erros.
6. Quando você terminar a depuração do cluster de teste, exclua-o.

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar clusters HDInsight usando a interface do usuário da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Analisar Logs do HDInsight](hdinsight-debug-jobs.md)
* [Entrada de aplicativo do Access Apache Hadoop YARN no HDInsight baseado em Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Habilitar despejos de heap para serviços do Apache Hadoop no HDInsight baseado em Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Problemas conhecidos para o cluster do Apache Spark no HDInsight](hdinsight-apache-spark-known-issues.md)
