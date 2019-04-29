---
title: Melhores práticas para utilizar o Microsoft Azure Data Lake Storeage Gen1 | Microsoft Docs
description: Aprenda as melhores práticas sobre ingestão de dados, segurança de data e desempenho relacionadas ao uso do Azure Data Lake Storeage Gen1 (anteriormente conhecido como Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: sachins
ms.openlocfilehash: 50d0ed644b5afa744e8bce478199079fd4fb7432
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60878936"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen1"></a>Melhores práticas para utilizar o Microsoft Azure Data Lake Storeage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Neste artigo, você aprenderá sobre as melhores práticas e considerações para trabalhar com o Azure Data Lake Storage Gen1. Este artigo fornece informações sobre segurança, desempenho, resiliência e monitoramento do Data Lake Storage Gen1. Antes do Data Lake Storage Gen1, trabalhar com Big Data em serviços como o Microsoft Azure HDInsight era realmente complexo. Era necessário fragmentar dados em várias contas de Armazenamento de Blobs para que o armazenamento de petabyte e o desempenho ideal nessa escala pudessem ser alcançados. Com o Data Lake Storage Gen1, a maioria dos limites rígidos para tamanho e desempenho foi removida. No entanto, ainda há algumas considerações que este artigo abrange para que seja possível obter o melhor desempenho com o Data Lake Storage Gen1.

## <a name="security-considerations"></a>Considerações de segurança

O Azure Data Lake Storage Gen1 oferece controles de acesso POSIX e auditoria detalhada para usuários, grupos e entidades de serviço do Microsoft Azure AD (Azure Active Directory). Esses controles de acesso podem ser configurados para arquivos e pastas existentes. Os controles de acesso também podem ser utilizados para criar padrões que podem ser aplicados a novos arquivos ou pastas. Quando as permissões forem definidas para pastas existentes e objetos secundários, as permissões deverão ser propagadas recursivamente em cada objeto. Se houver um grande número de arquivos, a propagação das permissões poderá demorar muito tempo. O tempo escolhido pode variar entre 30 e 50 objetos processados por segundo. Portanto, planeje a estrutura de pasta e os grupos de usuários adequadamente. Caso contrário, atrasos e problemas imprevistos poderão ocorrer ao trabalhar com os dados.

Suponha que você tenha uma pasta com 100.000 objetos filho. Se você aceitar o limite inferior de 30 objetos processados por segundo, atualizar a permissão para toda a pasta poderá demorar uma hora. Mais detalhes sobre ACLs do Data Lake Storage Gen1 estão disponíveis em [Controle de acesso no Azure Data Lake Storage Gen1](data-lake-store-access-control.md). Para melhorar o desempenho ao atribuir ACLs de recursivamente, você pode utilizar a Ferramenta de Linha de Comando do Azure Data Lake. A ferramenta cria vários threads e lógica de navegação recursiva para aplicar rapidamente ACLs a milhões de arquivos. A ferramenta está disponível para Linux e Windows, e a [documentação](https://github.com/Azure/data-lake-adlstool) e os [downloads](https://aka.ms/adlstool-download) para essa ferramenta podem ser encontrados no GitHub. Essas mesmas melhorias de desempenho podem ser habilitadas por suas próprias ferramentas escritas com Data Lake Storage Gen1 [.NET](data-lake-store-data-operations-net-sdk.md) e [Java](data-lake-store-get-started-java-sdk.md) SDKs.

### <a name="use-security-groups-versus-individual-users"></a>Usar grupos de segurança versus usuários individuais

Ao trabalhar com Big Data no Data Lake Storage Gen1, é provável que uma entidade de serviço seja usada para permitir que serviços como o Azure HDInsight trabalhem com os dados. No entanto, poderá haver casos em que usuários individuais também precisarão ter acesso aos dados. Nesses casos, será necessário utilizar os [grupos de segurança](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) do Azure Active Directory, em vez de atribuir usuários individuais a pastas e arquivos.

Quando permissões são atribuídas a um grupo de segurança, adicionar ou remover usuários do grupo não exigirá atualizações para o Data Lake Storage Gen1. Isso também ajuda a garantir que você não exceda o limite de [32 ACLs de acesso e padrão](../azure-subscription-service-limits.md#data-lake-store-limits) (isso inclui as quatro ACLs de estilo POSIX que sempre estão associadas a cada arquivo e pasta: [o usuário proprietário](data-lake-store-access-control.md#the-owning-user), [o grupo proprietário](data-lake-store-access-control.md#the-owning-group), [a máscara](data-lake-store-access-control.md#the-mask) e outros).

### <a name="security-for-groups"></a>Segurança para grupos

Conforme discutido, quando os usuários precisarem acessar o Data Lake Storage Gen1, será melhor usar os grupos de segurança do Azure Active Directory. Alguns grupos recomendados para iniciar podem ser **ReadOnlyUsers**, **WriteAccessUsers** e **FullAccessUsers** para a raiz da conta e, inclusive separadas para as principais subpastas. Se houver outros grupos de usuários antecipados que poderão ser adicionados posteriormente, mas ainda não foram identificados, será possível criar grupos de segurança falsos que tenham acesso a determinadas pastas. Usar grupo de segurança garante que futuramente não seja necessário um tempo de processamento longo para atribuir novas permissões a milhares de arquivos.

### <a name="security-for-service-principals"></a>Segurança para entidades de serviço

As entidades de serviço do Azure Active Directory normalmente são usadas por serviços como o Azure HDInsight para acessar dados no Data Lake Storage Gen1. Dependendo dos requisitos de acesso em várias cargas de trabalho, poderá haver algumas considerações para garantir a segurança dentro e fora da organização. Para muitos clientes, uma única entidade de serviço do Azure Active Directory poderá ser adequada e ter permissões completas na raiz da conta do Data Lake Storage Gen1. Outros clientes podem exigir múltiplos clusters com diferentes entidades de serviço onde um cluster tenha acesso total aos dados e outro cluster com apenas acesso de leitura. Tal como acontece com os grupos de segurança, quando uma conta do Data Lake Storage Gen1 é criada, convém criar uma entidade de serviço para cada cenário antecipado (ler, gravar, completar).

### <a name="enable-the-data-lake-storage-gen1-firewall-with-azure-service-access"></a>Habilitar o firewall do Data Lake Storage Gen1 com acesso à serviços do Azure

Um Data Lake Storage Gen1 dá suporte para a opção de ativar um firewall e limitar acesso apenas para serviços do Azure, o que é recomendado para um vetor de ataque menor de intrusões externas. O Firewall pode ser habilitado na conta do Data Lake Storage Gen1 no portal do Azure usando as opções **Firewall** > **Habilitar Firewall (ON)** > **Permitir acesso a serviços do Azure**.

![Configurações de firewall no Data Lake Storage Gen1](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "Configurações de firewall no Data Lake Storage Gen1")

Quando o firewall estiver habilitado, apenas os serviços do Azure, como HDInsight, Data Factory, SQL Data Warehouse etc., terão acesso ao Data Lake Storage Gen1. Devido à conversão de endereços de rede interna utilizadas pelo Azure, o firewall do Data Lake Storage Gen1 não fornece suporte para restringir serviços por IP específicos e é destinado apenas para restrições de pontos de extremidade fora do Azure, como locais.

## <a name="performance-and-scale-considerations"></a>Considerações de desempenho e escala

Um dos recursos mais avançados do Data Lake Storage Gen1 é que ele remove os limites rígidos na taxa de transferência de dados. A remoção dos limites permite aos clientes aumentar o tamanho de dados e acompanhar os requisitos de desempenho sem a necessidade de fragmentar os dados. Uma das considerações mais importantes para otimizar o desempenho do Data Lake Storage Gen1 é que ele apresenta melhor desempenho quando há paralelismo.

### <a name="improve-throughput-with-parallelism"></a>Melhorar a taxa de transferência com paralelismo

Considere atribuir de 8 a 12 threads por núcleo para obter melhor taxa de transferência de leitura/gravação. Isso é devido ao bloqueio de leituras/gravações em um único thread, e mais threads podem permitir maior concorrência na VM. Para garantir que os níveis estejam íntegros e o paralelismo possa ser aumentado, certifique-se de monitorar a utilização da CPU da VM.

### <a name="avoid-small-file-sizes"></a>Evitar tamanhos de arquivo pequenos

As permissões POSIX e a auditoria no Data Lake Storage Gen1 vêm com uma sobrecarga que fica aparente ao trabalhar com vários arquivos pequenos. Como uma melhor prática, você deve trabalhar em lotes os dados em arquivos maiores, em comparação à gravação de milhares ou milhões de arquivos pequenos para o Data Lake Storage Gen1. Evitar tamanhos de arquivos pequenos pode ter múltiplos benefícios, tais como:

* Reduzir as verificações de autenticação em vários arquivos
* Conexões de arquivos abertos reduzidas
* Cópia/replicação mais rápida
* Menos arquivos para processar ao atualizar as permissões POSIX do Azure Data Lake Storage Gen1

Dependendo de quais serviços e cargas de trabalho estão usando os dados, um bom tamanho a ser considerado para arquivos é 256 MB ou maior. Se os tamanhos dos arquivos não puderem ser em lote ao descarregar no Data Lake Storage Gen1, você poderá ter um trabalho de compactação separado que combine esses arquivos em arquivos maiores. Para obter mais informações e recomendações sobre o tamanho dos arquivos e organizar os dados no Data Lake Storage Gen1, veja [Estrutura o conjunto de dados](data-lake-store-performance-tuning-guidance.md#structure-your-data-set).

### <a name="large-file-sizes-and-potential-performance-impact"></a>Tamanhos de arquivos grandes e o impacto no desempenho

Embora o Data Lake Storage Gen1 dê suporte a arquivos grandes de até petabytes, para otimizar o desempenho e, dependendo do processo de leitura dos dados, não é indicado ultrapassar 2 GB em média. Por exemplo, ao usar **Distcp** para copiar dados entre diferentes contas de armazenamento e locais, os arquivos serão o melhor nível de granularidade usado para determinar as tarefas do mapa. Portanto, se você estiver copiando 10 arquivos de 1 TB cada, serão alocados 10 mapeadores no máximo. Além disso, se houver muitos arquivos com mapeadores atribuídos, inicialmente os mapeadores irão trabalhar em paralelo para mover arquivos grandes. No entanto, como o trabalho começa a reduzir, apenas alguns mapeadores permanecerão alocados e será possível ficar paralisado com um único mapeador atribuído a um arquivo grande. A Microsoft apresentou melhorias para Distcp resolver esse problema nas futuras versões de Hadoop.

Outro exemplo a ser considerado é o uso do Azure Data Lake Analytics com Data Lake Storage Gen1. Dependendo do processamento feito pelo extrator, alguns arquivos que não podem ser divididos (por exemplo, XML, JSON) poderão afetar o desempenho quando forem superiores a 2 GB. Nos casos em que os arquivos podem ser divididos por um extrator (por exemplo, CSV), os arquivos grandes são preferenciais.

### <a name="capacity-plan-for-your-workload"></a>Plano de capacidade para carga de trabalho

O Azure Data Lake Storage Gen1 remove as restrições de limitação de E/S de hardware que são impostas às contas de Armazenamento de Blobs. No entanto, ainda há limites suaves que precisam ser considerados. As restrições de limitação de entrada/saída atendem às necessidades da maioria dos cenários. Se a carga de trabalho precisar aumentar os limites, trabalhe com o suporte da Microsoft. Além disso, observe os limites durante o estágio de prova de conceito, de modo que as restrições de limitação de E/S não ocorram durante a produção. Se isso acontecer, poderá exigir a espera de um aumento manual da equipe de engenharia da Microsoft. Se ocorrer limitação de E/S, o Azure Data Lake Storage Gen1 retornará um código de erro de 429 e, preferencialmente, deverá ser repetido com uma política de retirada exponencial apropriada.

### <a name="optimize-writes-with-the-data-lake-storage-gen1-driver-buffer"></a>Otimizar "gravações" com buffer de driver do Data Lake Storage Gen1

Para otimizar o desempenho e reduzir IOPS ao gravar no Data Lake Storage Gen1 usando e Hadoop, execute operações de gravação o mais próximo possível do tamanho do buffer do driver do Data Lake Storage Gen1. Tente não exceder o tamanho do buffer antes da liberação, como, por exemplo, ao transmitir por streaming usando cargas de trabalho de streaming do Apache Storm ou Spark. Ao gravar para o Data Lake Storage Gen1 do HDInsight/Hadoop, é importante saber se o Data Lake Storage Gen1 tem um driver com um buffer de 4 MB. Como muitos drivers de sistema de arquivos, esse buffer pode ser manualmente liberado antes de alcançar o tamanho de 4 MB. Caso contrário, ele será imediatamente liberado para armazenamento se a próxima gravação exceder o tamanho máximo do buffer. Sempre que possível, você deverá evitar uma saturação ou insuficiência significativa do buffer durante política de liberação/sincronização por janela de tempo ou contagem.

## <a name="resiliency-considerations"></a>Considerações de resiliência

Ao arquivar um sistema com Data Lake Storage Gen1 ou qualquer serviço de nuvem, considere os requisitos de disponibilidade e como responder a possíveis interrupções no serviço. Um problema pode ser localizado na instância específica ou mesmo região inteira, portanto, ter um plano para ambos é importante. Dependendo das SLAS do **objetivo do tempo de recuperação** e o **objetivo de ponto de recuperação** para a carga de trabalho, você poderá escolher uma estratégia mais ou menos agressiva para alta disponibilidade e recuperação de desastre.

### <a name="high-availability-and-disaster-recovery"></a>Alta disponibilidade e recuperação de desastres

A HA (alta disponibilidade) e a DR (recuperação de desastre) às vezes podem ser combinadas em conjunto, embora cada uma tenha uma estratégia ligeiramente diferente, especialmente quando se trata de dados. O Data Lake Storage Gen1 já processa replicação de três vezes nos bastidores para oferecer proteção contra falhas de hardware localizadas. No entanto, como a replicação entre regiões não está compilada, você próprio deverá gerenciá-la. Ao construir um plano para HA, no caso de uma interrupção do serviço, a carga de trabalho precisa acessar os dados mais recentes o mais rápido possível, alternando para uma instância separada replicada localmente ou em uma nova região.

Em uma estratégia DR, para preparar-se para o evento improvável de uma falha catastrófica de uma região, também é importante que os dados sejam replicados para uma região diferente. Esses dados podem inicialmente ser os mesmos que os dados de HA replicados. No entanto, você também deve considerar os requisitos para casos de borda, como corrupção de dados, em que você deseja criar instantâneos periódicos para fazer fallback. Dependendo da importância e do tamanho dos dados, considere acumular instantâneos de delta de 1, 6 e 24 horas no repositório local e/ou secundário, de acordo com as tolerâncias de risco.

Para a resiliência de dados com o Data Lake Storage Gen1, é recomendável realizar a replicação geográfica dos seus dados para uma região separada com uma frequência que satisfaça os requisitos de HA/DR, preferencialmente a cada hora. Essa frequência de replicação minimiza movimentos de dados em massa que podem ter necessidades de transferência competitivas com o sistema principal e um melhor RPO (objetivo de ponto de recuperação). Além disso, considere maneiras para o aplicativo usando o Data Lake Storage Gen1 fazer failover automaticamente na conta secundária usando de disparadores de monitoramento ou duração de tentativas com falha ou, pelo menos, enviar uma notificação aos administradores para intervenção manual. Tenha em mente que há uma compensação de failover versus espera de um serviço para retornar online. Se os dados não tiverem concluído a replicação, um failover pode causar perda de dados, inconsistência ou mesclagem complexa dos dados.

A seguir, são apresentadas as três principais opções recomendadas para orquestrar a replicação entre as contas do Data Lake Storage Gen1 e as principais diferenças entre cada uma delas.

|  |Distcp  |Fábrica de dados do Azure  |AdlCopy  |
|---------|---------|---------|---------|
|**Limites de escala**     | Limitado por nós de trabalho        | Limitado por unidades de Movimentação de Dados de Nuvem        | Limitado por unidades do Analytics        |
|**Oferece suporte à cópia deltas**     |   Sim      | Não         | Não          |
|**Orquestração interna**     |  Não (utilize trabalhos cron ou ventilação excessiva Oozie)       | Sim        | Não (utilize a Automação do Azure ou o Agendador de Tarefas do Windows)         |
|**Com suporte para sistemas de arquivos**     | ADL, HDFS, WASB, S3, GS, CFS        |Vários, consulte [Conectores](../data-factory/connector-azure-blob-storage.md).         | ADL para ADL, WASB para ADL (mesma região somente)        |
|**Suporte SO**     |Qualquer SO executando Hadoop         | N/D          | Windows 10         |

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Use o Distcp para movimentação de dados entre dois locais

Breve para cópia distribuída, a Distcp é uma ferramenta de linha de comando Linux que vem com Hadoop e fornece movimentação de dados distribuídos entre dois locais. Os dois locais podem ser Data Lake Storage Gen1, HDFS, WASB ou S3. Esta ferramenta usa trabalhos MapReduce em um cluster Hadoop (por exemplo, HDInsight) para escalar horizontalmente todos os nós. A Distcp é considerada a maneira mais rápida de mover big data sem dispositivos de compactação de rede especiais. A Distcp também fornece uma opção para atualizar apenas deltas entre dois locais, trata repetições automáticas, assim como com dimensionamento dinâmico de computação. Essa abordagem é incrivelmente eficiente quando trata-se de replicar elementos como tabelas de Hive/Spark que podem ter muitos arquivos grandes em um único diretório e você quer copiar somente sobre os dados modificados. Por esses motivos, a Distcp é a ferramenta mais recomendada para copiar dados entre armazenamentos de Big Data.

Trabalhos de cópia podem ser disparados por fluxos de trabalho do Apache Oozie usando frequência ou gatilhos de dados, bem como trabalhos Cron do Linux. Para trabalhos de replicação intensiva, é recomendável ativar um cluster Hadoop do HDInsight separado que pode ser ajustado e escalado especificamente para os trabalhos de cópia. Isso garante que os trabalhos de cópia não interfiram com trabalhos críticos. Se estiver executando replicação em uma frequência suficientemente ampla, o cluster poderá até ser inativado entre cada trabalho. Se estiver fazendo failover para região secundária, verifique se outro cluster também está ativado na região secundária para replicar novos dados de volta para a conta do Data Lake Storage Gen1 principal quando ele retornar. Para exemplos de utilização da Distcp, confira [Usar Distcp para copiar dados entre o Azure Storage Blob e o Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Usar Azure Data Factory para agendar trabalhos de cópia

O Azure Data Factory também pode ser utilizado para agendar trabalhos de cópia usando uma **Atividade de Cópia** e, além disso, pode ser configurado em uma frequência através do **Assistente de Cópia**. Tenha em mente que o Azure Data Factory possui um limite de DMUs (unidades de movimentação de dados na nuvem) e, eventualmente, eleva a taxa de transferência/computação para cargas de trabalho de dados grandes. Além disso, o Azure Data Factory atualmente não oferece atualizações delta entre as contas do Data Lake Storage Gen1, de modo que pastas como as tabelas de Hive exigiriam uma cópia completa para serem replicadas. Consulte o [Guia de ajuste da atividade de cópia](../data-factory/copy-activity-performance.md) para obter mais informações sobre como copiar com o Data Factory.

### <a name="adlcopy"></a>AdlCopy

AdlCopy é uma ferramenta de linha de comando do Windows que permite copiar dados entre duas contas do Data Lake Storage Gen1 somente dentro da mesma região. A ferramenta AdlCopy fornece uma opção autônoma ou a opção de usar uma conta do Azure Data Lake Analytics para executar o trabalho de cópia. Embora tenha sido originalmente criada para cópias sob demanda em oposição a uma replicação robusta, a ferramenta fornece outra opção para fazer cópias distribuídas nas contas do Data Lake Storage Gen1 dentro da mesma região. Por confiabilidade, é recomendável usar a opção Premium do Data Lake Analytics para qualquer carga de trabalho de produção. A versão autônoma pode retornar respostas ocupadas e tem escala limitada e monitoramento.

Como a Distcp, a AdlCopy precisa ser orquestrada por algo como a Automação do Azure ou o Agendador de Tarefas do Windows. Tal como acontece com o Data Factory, a AdlCopy não tem suporte para copiar apenas arquivos atualizados, mas recopia e substitui os arquivos existentes. Para obter mais informações e exemplos de utilização de AdlCopy, veja [Copiar dados do Azure Storage Blobs para o Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="monitoring-considerations"></a>Considerações de monitoramento

O Data Lake Storage Gen1 fornece auditoria e logs de diagnóstico detalhados. O Data Lake Storage Gen1 fornece algumas métricas básicas no portal do Azure sob a conta do Data Lake Storage Gen1 e no Azure Monitor. A disponibilidade do Data Lake Storage Gen1 é exibida no portal do Azure. No entanto, essa métrica é atualizada a cada sete minutos e não pode ser consultada através de uma API publicamente exposta. Para obter a disponibilidade mais atualizada de uma conta do Data Lake Storage Gen1, será necessário executar os próprios testes sintéticos para validar a disponibilidade. Outras métricas, como a utilização total de armazenamento, solicitações de leitura/gravação e entrada/saída podem demorar até 24 horas para atualizar. Assim, métricas mais atualizadas devem ser calculadas manualmente através de ferramentas de linha de comando Hadoop ou agregando informações de logs. A maneira mais rápida de obter a utilização de armazenamento mais recente é executar esse comando HDFS a partir de um nó de cluster Hadoop (por exemplo, nó de cabeçalho):

    hdfs dfs -du -s -h adl://<adlsg1_account_name>.azuredatalakestore.net:443/

### <a name="export-data-lake-storage-gen1-diagnostics"></a>Exportar diagnóstico do Data Lake Storage Gen1

Uma das maneiras mais rápidas de obter acesso aos logs pesquisáveis do Data Lake Storage Gen1 é habilitar o envio de logs ao **Log Analytics** na folha **Diagnóstico** da conta do Data Lake Storage Gen1. Isso fornece acesso imediato aos logs de entrada com filtros de conteúdo e hora, juntamente com opções de alerta (email/webhook) disparadas dentro de intervalos de 15 minutos. Para obter instruções, veja [Acessar logs de diagnóstico do Azure Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md).

Para obter mais alertas em tempo real e mais controle sobre onde descarregar logs, considere exportar logs para o EventHub do Microsoft Azure, onde o conteúdo pode ser analisado individualmente ou em uma janela de tempo para enviar notificações em tempo real para uma fila. Um aplicativo separado, como um [Aplicativo Lógico App](../connectors/connectors-create-api-azure-event-hubs.md) pode consumir e comunicar alertas para o canal apropriado, além de enviar métricas para ferramentas de monitoramento como NewRelic, Datadog ou AppDynamics. Alternativamente, se você estiver usando uma ferramenta de terceiros, como o ElasticSearch, poderá exportar os logs para o Armazenamento de Blobs e usar o [plug-in do Azure Logstash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) para consumir os dados no Elasticsearch, Kibana e pilha Logstash (ELK).

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Ativar log de nível de depuração no HDInsight

Se o envio de logs do Data Lake Storage Gen1 não estiver ativado, o Azure HDInsight também fornecerá uma maneira de ativar o [log no lado do cliente para Data Lake Storage Gen1](data-lake-store-performance-tuning-mapreduce.md) por meio do log4j. Você deve definir a seguinte propriedade em **Ambari** > **YARN** > **Configuração** > **Configurações avançadas yarn-log4j**:

    log4j.logger.com.microsoft.azure.datalake.store=DEBUG

Quando a propriedade estiver configurada e os nós forem reiniciados, o diagnóstico do Data Lake Storage Gen1 será gravado nos logs YARN nos nós (/tmp/\<user\>/yarn.log) e detalhes importantes, como erros ou limitação (código de erro HTTP 429), poderão ser monitorados. Esta mesma informação também pode ser monitorada em logs do Azure Monitor ou onde os logs são enviados para o [diagnóstico](data-lake-store-diagnostic-logs.md) folha da conta do Data Lake armazenamento Gen1. É recomendável ter pelo menos o log do lado do cliente ativado ou usar a opção de envio de logs com o Data Lake Storage Gen1 para visibilidade operacional e depuração mais fácil.

### <a name="run-synthetic-transactions"></a>Executar transações sintéticas

Atualmente, a métrica de disponibilidade de serviços do Data Lake Storage Gen1 no portal do Azure tem uma janela de atualização de 7 minutos. Além disso, não é possível consultar usando uma API exposta publicamente. Portanto, é recomendável compilar um aplicativo básico que faça transações sintéticas para o Data Lake Storage Gen1, que poderá fornecer até a disponibilidade de minutos. Um exemplo pode ser a criação de um WebJob, Aplicativo Lógico ou Aplicativo Azure Function para executar uma leitura, criar e atualizar o Data Lake Storage Gen1 e enviar os resultados para a solução de monitoramento. As operações podem ser feitas em uma pasta temporária e, em seguida, excluídas após o teste, que podem ser executadas a cada 30-60 segundos, dependendo dos requisitos.

## <a name="directory-layout-considerations"></a>Considerações de layout de diretório

Ao descarregar dados em um Data Lake, é importante planejar previamente a estrutura dos dados, de modo que a segurança, o particionamento e o processamento possam ser utilizados efetivamente. Muitas das recomendações a seguir podem ser utilizadas, seja com o Azure Data Lake Storage Gen1, Armazenamento de Blobs ou HDFS. Toda carga de trabalho tem requisitos diferentes sobre como os dados são consumidos, mas, a seguir, estão alguns layouts comuns a serem considerados ao trabalhar com cenários de lote e IoT.

### <a name="iot-structure"></a>Estrutura de IoT

Nas cargas de trabalho de IoT, pode haver uma grande quantidade de dados sendo descarregados no armazenamento de dados que abrange diversos produtos, dispositivos, organizações e clientes. É importante planejar previamente o layout do diretório para organização, segurança e processamento eficiente dos dados para consumidores de downstream. Um modelo geral a considerar pode ser o layout a seguir:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Por exemplo, a telemetria de descarregamento para um motor de avião no Reino Unido pode parecer com a seguinte estrutura:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Há uma razão importante para colocar a data no final da estrutura de pastas. Se você quiser bloquear certas regiões ou assunto para usuários/grupos, então, você poderá fazê-lo com as permissões POSIX. Caso contrário, se houvesse necessidade de restringir um determinado grupo de segurança para exibição apenas dos dados do Reino Unido ou certos planos, com a estrutura da data na frente, seria necessária uma permissão separada para várias pastas em cada pasta de hora. Além disso, ter a estrutura de data na frente aumentaria exponencialmente o número de pastas com o passar do tempo.

### <a name="batch-jobs-structure"></a>Estrutura de trabalhos em lotes

De um alto nível, uma abordagem comumente usada no processamento em lotes é descarregar dados em uma pasta "in". Então, quando os dados forem processados, coloque os novos dados em uma pasta "out" para processos downstream consumir. Essa estrutura de diretório é visto às vezes para trabalhos que requerem processamento em arquivos individuais e talvez não exigiam um processamento massivamente paralelo em conjuntos de dados grandes. Como a estrutura de IoT recomendada acima, uma boa estrutura de diretório tem as pastas de nível pai para elementos como região e assuntos (por exemplo, organização, produto/produtor). Essa estrutura ajuda a proteger os dados em sua organização e um melhor gerenciamento dos dados em suas cargas de trabalho. Além disso, considere a data e hora na estrutura para permitir uma melhor organização, pesquisas filtradas, segurança e automação no processamento. O nível de granularidade para a estrutura da data é determinado pelo intervalo, no qual os dados são carregados ou processados, como por hora, diariamente ou mesmo mensalmente.

Às vezes, o processamento de arquivos não tem êxito devido dados corrompidos ou formatos inesperados. Nesses casos, a estrutura de diretório pode beneficiar-se de uma pasta **/bad** para mover os arquivos para uma inspeção adicional. O trabalho em lotes também pode manipular o relatório ou a notificação desses arquivos *incorretos* para intervenção manual. Considere a estrutura de modelo a seguir:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Por exemplo, uma empresa de marketing que recebe extratos de dados diários de atualizações de cliente dos seus clientes na América do Norte. Ele pode parecer com o seguinte snippet de código antes e depois de ser processado:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

No caso comum de dados em lotes que estão sendo processados diretamente em bancos de dados, como Hive ou Bancos de Dados SQL tradicionais, não há necessidade de uma pasta **/in** ou **/out**, uma vez que a saída já entra em uma pasta separada para a tabela de Hive ou banco de dados externo. Por exemplo, os extratos diários dos clientes descarregariam em suas respectivas pastas e a orquestração tanto pelo Azure Data Factory, Apache Oozie ou pelo Apache Airflow dispararia um trabalho do Hive ou Spark diário para processar e gravar os dados em uma tabela de Hive.

## <a name="next-steps"></a>Próximas etapas

* [Visão Geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Controle de acesso no Azure Data Lake Storage Gen1](data-lake-store-access-control.md)
* [Segurança no Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)
* [Ajustando o Azure Data Lake Storage Gen1 para desempenho](data-lake-store-performance-tuning-guidance.md)
* [Diretrizes de ajuste do desempenho para uso do Spark para HDInsight com Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-spark.md)
* [Diretrizes de ajuste do desempenho para uso do Hive para HDInsight com Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-hive.md)
* [Criar clusters do HDInsight com o Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
