---
title: Melhores práticas para utilizar o Microsoft Azure Data Lake Storage Gen2 | Microsoft Docs
description: Aprenda as melhores práticas sobre ingestão de dados, segurança de data e desempenho relacionadas ao uso do Azure Data Lake Storage Gen2 (anteriormente conhecido como Azure Data Lake Store)
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: article
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: c1a298584b2444d52f84c0e599462bc26c63a898
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302632"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Melhores práticas para utilizar o Microsoft Azure Data Lake Storage Gen2

Neste artigo, você aprenderá sobre as melhores práticas e considerações para trabalhar com o Azure Data Lake Storage Gen2. Este artigo fornece informações sobre segurança, desempenho, resiliência e monitoramento do Data Lake Storage Gen2. Antes do Data Lake Storage Gen2, trabalhar com Big Data em serviços como o Microsoft Azure HDInsight era realmente complexo. Era necessário fragmentar dados em várias contas de Armazenamento de Blobs para que o armazenamento de petabyte e o desempenho ideal nessa escala pudessem ser alcançados. Com o Data Lake Storage Gen2, a maioria dos limites rígidos para tamanho e desempenho foi removida. No entanto, ainda há algumas considerações que este artigo aborda para que seja possível obter o melhor desempenho com o Data Lake Storage Gen2.

## <a name="security-considerations"></a>Considerações sobre segurança

O Azure Data Lake Storage Gen2 oferece controles de acesso POSIX para grupos e entidades de serviço do Microsoft Azure AD (Azure Active Directory). Esses controles de acesso podem ser configurados para arquivos e diretórios existentes. Os controles de acesso também podem ser utilizados para criar permissões padrão que podem ser aplicadas automaticamente a novos arquivos ou diretórios. Mais detalhes sobre ACLs do Data Lake Storage Gen2 estão disponíveis em [Controle de acesso no Azure Data Lake Storage Gen2](storage-data-lake-storage-access-control.md).

### <a name="use-security-groups-versus-individual-users"></a>Usar grupos de segurança versus usuários individuais

Ao trabalhar com Big Data no Data Lake Storage Gen2, é provável que uma entidade de serviço seja usada para permitir que serviços como o Azure HDInsight trabalhem com os dados. No entanto, poderá haver casos em que usuários individuais também precisarão ter acesso aos dados. Em todos os casos, considere utilizar os [grupos de segurança](../common/storage-auth-aad.md) do Azure Active Directory, em vez de atribuir usuários individuais a diretórios e arquivos.

Quando permissões são atribuídas a um grupo de segurança, adicionar ou remover usuários do grupo não exigirá atualizações para o Data Lake Storage Gen2. Isso também ajuda a garantir que você não ultrapasse o número máximo de entradas de controle de acesso por ACL (lista de controle de acesso). Atualmente, esse número é 32, (incluindo as quatro ACLs de estilo POSIX que sempre estão associadas a cada arquivo e diretório): o usuário proprietário, o grupo proprietário, a máscara e outros. Cada diretório pode ter dois tipos de ACL, a ACL de acesso e a ACL padrão, com um total de 64 entradas de controle de acesso. Para obter mais informações sobre essas ACLs, confira [Controle de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="security-for-groups"></a>Segurança para grupos

Quando você ou seus usuários precisam acessar dados em uma conta de armazenamento com namespace hierárquico habilitado, é melhor usar grupos de segurança do Azure Active Directory. Alguns grupos recomendados para começar podem ser **ReadOnlyUsers**, **WriteAccessUsers**e **FullAccessUsers** para a raiz do sistema de arquivos e até mesmo separá-los para subdiretórios de chave. Se houver outros grupos de usuários antecipados que poderão ser adicionados posteriormente, mas ainda não foram identificados, será possível criar grupos de segurança falsos que tenham acesso a determinadas pastas. Usar grupo de segurança garante que você possa evitar tempos de processamento longos ao atribuir novas permissões a milhares de arquivos.

### <a name="security-for-service-principals"></a>Segurança para entidades de serviço

As entidades de serviço do Azure Active Directory normalmente são usadas por serviços como o Azure Databricks para acessar dados no Data Lake Storage Gen2. Para muitos clientes, uma única entidade de serviço de Azure Active Directory pode ser adequada e pode ter permissões completas na raiz do sistema de arquivos de Data Lake Storage Gen2. Outros clientes podem exigir múltiplos clusters com diferentes entidades de serviço onde um cluster tenha acesso total aos dados e outro cluster com apenas acesso de leitura. 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>Habilitar o firewall do Data Lake Storage Gen2 com acesso a serviços do Azure

Um Data Lake Storage Gen2 dá suporte para a opção de ativar um firewall e limitar acesso apenas para serviços do Azure, o que é recomendado para limitar o vetor de ataques externos. O Firewall pode ser habilitado em uma conta de armazenamento no Portal do Azure através das opções **Firewall** > **Habilitar Firewall (ON)**  > **Permitir acesso à serviços do Azure**.

Para acessar sua conta de armazenamento do Azure Databricks, implante Azure Databricks em sua rede virtual e, em seguida, adicione essa rede virtual ao firewall. Consulte [configurar redes virtuais e firewalls de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

## <a name="resiliency-considerations"></a>Considerações de resiliência

Ao arquivar um sistema com Data Lake Storage Gen2 ou qualquer serviço de nuvem, considere os requisitos de disponibilidade e como responder a possíveis interrupções no serviço. Um problema pode ser localizado na instância específica ou mesmo região inteira, portanto, ter um plano para ambos é importante. Dependendo dos SLAs do objetivo do tempo de recuperação e o objetivo de ponto de recuperação para a carga de trabalho, você poderá escolher uma estratégia mais ou menos agressiva para alta disponibilidade e recuperação de desastre.

### <a name="high-availability-and-disaster-recovery"></a>Alta disponibilidade e recuperação de desastres

A HA (alta disponibilidade) e a DR (recuperação de desastre) às vezes podem ser combinadas em conjunto, embora cada uma tenha uma estratégia ligeiramente diferente, especialmente quando se trata de dados. O Data Lake Storage Gen2 já processa replicação de três vezes nos bastidores para oferecer proteção contra falhas de hardware localizadas. Além disso, outras opções de replicação como o ZRS aprimoram a HA, enquanto o GRS e o RA-GRS aprimoram a DR. Ao construir um plano para HA, no caso de uma interrupção do serviço, a carga de trabalho precisa acessar os dados mais recentes o mais rápido possível, alternando para uma instância separada replicada localmente ou em uma nova região.

Em uma estratégia DR, para preparar-se para o evento improvável de uma falha catastrófica de uma região, também é importante que os dados sejam replicados para uma região diferente usando replicação de GRS ou RA-GRS. Você também deve considerar os requisitos para casos de borda, como corrupção de dados, em que você deseja criar instantâneos periódicos para fazer fallback. Dependendo da importância e do tamanho dos dados, considere acumular instantâneos de delta de 1, 6 e 24 horas, de acordo com as tolerâncias de risco.

Para resiliência de dados com o Data Lake Storage Gen2, é recomendável realizar a replicação geográfica de seus dados usando um GRS ou RA-GRS que satisfaça seus requisitos de HA/DR. Além disso, considere maneiras para o aplicativo usando o Data Lake Storage Gen2 fazer failover automaticamente na região secundária usando de disparadores de monitoramento ou duração de tentativas com falha ou, pelo menos, enviar uma notificação aos administradores para intervenção manual. Tenha em mente que há uma compensação de failover versus espera de um serviço para retornar online.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Use o Distcp para movimentação de dados entre dois locais

Breve para cópia distribuída, a DistCp é uma ferramenta de linha de comando Linux que vem com Hadoop e fornece movimentação de dados distribuídos entre dois locais. Os dois locais podem ser Data Lake Storage Gen2, HDFS ou S3. Esta ferramenta usa trabalhos MapReduce em um cluster Hadoop (por exemplo, HDInsight) para escalar horizontalmente todos os nós. A Distcp é considerada a maneira mais rápida de mover big data sem dispositivos de compactação de rede especiais. A Distcp também fornece uma opção para atualizar apenas deltas entre dois locais, trata repetições automáticas, assim como com dimensionamento dinâmico de computação. Essa abordagem é incrivelmente eficiente quando trata-se de replicar elementos como tabelas de Hive/Spark que podem ter muitos arquivos grandes em um único diretório e você quer copiar somente sobre os dados modificados. Por esses motivos, a Distcp é a ferramenta mais recomendada para copiar dados entre armazenamentos de Big Data.

Trabalhos de cópia podem ser disparados por fluxos de trabalho do Apache Oozie usando frequência ou gatilhos de dados, bem como trabalhos Cron do Linux. Para trabalhos de replicação intensiva, é recomendável ativar um cluster Hadoop do HDInsight separado que pode ser ajustado e escalado especificamente para os trabalhos de cópia. Isso garante que os trabalhos de cópia não interfiram com trabalhos críticos. Se estiver executando replicação em uma frequência suficientemente ampla, o cluster poderá até ser inativado entre cada trabalho. Se estiver fazendo failover para região secundária, verifique se outro cluster também está ativado na região secundária para replicar novos dados de volta para a conta do Data Lake Storage Gen2 principal quando ele retornar. Para exemplos de utilização da Distcp, confira [Usar Distcp para copiar dados entre o Azure Storage Blob e o Data Lake Storage Gen2](../blobs/data-lake-storage-use-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Usar Azure Data Factory para agendar trabalhos de cópia

O Azure Data Factory também pode ser utilizado para agendar trabalhos de cópia usando uma Atividade de Cópia e, além disso, pode ser configurado em uma frequência através do Assistente de Cópia. Tenha em mente que o Azure Data Factory possui um limite de DMUs (unidades de movimentação de dados na nuvem) e, eventualmente, eleva a taxa de transferência/computação para cargas de trabalho de dados grandes. Além disso, o Azure Data Factory atualmente não oferece atualizações delta entre as contas do Data Lake Storage Gen2, de modo que diretórios como as tabelas de Hive exigiriam uma cópia completa para serem replicadas. Veja o [artigo sobre o data factory](../../data-factory/load-azure-data-lake-storage-gen2.md) para obter mais informações sobre como copiar com o Data Factory.

## <a name="monitoring-considerations"></a>Considerações de monitoramento

O Data Lake Storage Gen2 fornece métricas no portal do Azure sob a conta do Data Lake Storage Gen2 e no Azure Monitor. A disponibilidade do Data Lake Storage Gen2 é exibida no portal do Azure. Para obter a disponibilidade mais atualizada de uma conta do Data Lake Storage Gen2, será necessário executar os próprios testes sintéticos para validar a disponibilidade. Outras métricas, como a utilização de armazenamento total, solicitações de leitura/gravação e entrada/saída estão disponíveis para serem aproveitadas por aplicativos de monitoramento e também podem disparar alertas quando os limites (por exemplo, a latência média ou o número de erros por minuto) forem excedidos.

## <a name="directory-layout-considerations"></a>Considerações de layout de diretório

Ao descarregar dados em um Data Lake, é importante planejar previamente a estrutura dos dados, de modo que a segurança, o particionamento e o processamento possam ser utilizados efetivamente. Muitas das recomendações a seguir são aplicáveis a todas as cargas de trabalho de Big Data. Toda carga de trabalho tem requisitos diferentes sobre como os dados são consumidos, mas, a seguir, estão alguns layouts comuns a serem considerados ao trabalhar com cenários de lote e IoT.

### <a name="iot-structure"></a>Estrutura de IoT

Nas cargas de trabalho de IoT, pode haver uma grande quantidade de dados sendo descarregados no armazenamento de dados que abrange diversos produtos, dispositivos, organizações e clientes. É importante planejar previamente o layout do diretório para organização, segurança e processamento eficiente dos dados para consumidores de downstream. Um modelo geral a considerar pode ser o layout a seguir:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Por exemplo, a telemetria de descarregamento para um motor de avião no Reino Unido pode parecer com a seguinte estrutura:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Há uma razão importante para colocar a data no final da estrutura de diretórios. Se você quiser bloquear certas regiões ou assunto para usuários/grupos, então, você poderá fazê-lo com as permissões POSIX. Caso contrário, se houvesse necessidade de restringir um determinado grupo de segurança para exibição apenas dos dados do Reino Unido ou certos planos, com a estrutura da data na frente, seria necessária uma permissão separada para vários diretórios em cada diretório de hora. Além disso, ter a estrutura de data na frente aumentaria exponencialmente o número de diretórios com o passar do tempo.

### <a name="batch-jobs-structure"></a>Estrutura de trabalhos em lotes

De um alto nível, uma abordagem comumente usada no processamento em lotes é descarregar dados em um diretório "in". Então, quando os dados forem processados, coloque os novos dados em um diretório "out" para processos downstream consumir. Essa estrutura de diretório é visto às vezes para trabalhos que requerem processamento em arquivos individuais e talvez não exigiam um processamento massivamente paralelo em conjuntos de dados grandes. Como a estrutura de IoT recomendada acima, uma boa estrutura de diretório tem os diretórios de nível pai para elementos como região e assuntos (por exemplo, organização, produto/produtor). Essa estrutura ajuda a proteger os dados em sua organização e um melhor gerenciamento dos dados em suas cargas de trabalho. Além disso, considere a data e hora na estrutura para permitir uma melhor organização, pesquisas filtradas, segurança e automação no processamento. O nível de granularidade para a estrutura da data é determinado pelo intervalo, no qual os dados são carregados ou processados, como por hora, diariamente ou mesmo mensalmente.

Às vezes, o processamento de arquivos não tem êxito devido dados corrompidos ou formatos inesperados. Nesses casos, a estrutura de diretório pode beneficiar-se de uma pasta **/bad** para mover os arquivos para uma inspeção adicional. O trabalho em lotes também pode manipular o relatório ou a notificação desses arquivos *incorretos* para intervenção manual. Considere a estrutura de modelo a seguir:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Por exemplo, uma empresa de marketing que recebe extratos de dados diários de atualizações de cliente dos seus clientes na América do Norte. Ele pode parecer com o seguinte snippet de código antes e depois de ser processado:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

No caso comum de dados em lotes que estão sendo processados diretamente em bancos de dados, como Hive ou Bancos de Dados SQL tradicionais, não há necessidade de uma pasta **/in** ou **/out**, uma vez que a saída já entra em uma pasta separada para a tabela de Hive ou banco de dados externo. Por exemplo, os extratos diários dos clientes descarregariam em suas respectivas pastas e a orquestração tanto pelo Azure Data Factory, Apache Oozie ou pelo Apache Airflow dispararia um trabalho do Hive ou Spark diário para processar e gravar os dados em uma tabela de Hive.
