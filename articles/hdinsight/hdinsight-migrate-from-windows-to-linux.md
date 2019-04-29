---
title: Migrar do HDInsight baseado em Windows para o HDInsight baseado em Linux - Azure
description: Saiba como migrar de um cluster HDInsight baseado no Windows para um cluster HDInsight baseado no Linux.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: hrasheed
ms.openlocfilehash: 49f55416cb9224736acd7b8ac5eac5b6c5ba5979
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766710"
---
# <a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Migrar de um cluster HDInsight baseado no Windows para um cluster baseado em Linux

Esse documento fornece detalhes sobre as diferenças entre o HDInsight no Windows e Linux. Ele também oferece orientação sobre como migrar cargas de trabalho existentes para um cluster com base em Linux.

Embora o HDInsight baseado no Windows forneça uma maneira fácil de usar o Apache Hadoop na nuvem, você poderá precisar migrar para um cluster baseado em Linux. Por exemplo, para aproveitar as ferramentas e tecnologias baseadas em Linux que são necessárias para sua solução. Muitos itens no ecossistema do Hadoop são desenvolvidos em sistemas baseados em Linux e podem não estar disponíveis para uso com o HDInsight baseado no Windows. Muitos livros, vídeos e outros materiais de treinamento presumem que você está usando um sistema Linux ao trabalhar com Hadoop.

> [!NOTE]  
> Os clusters HDInsight usam o Ubuntu LTS (suporte de longo prazo) como o sistema operacional para os nós no cluster. Para obter informações sobre a versão do Ubuntu disponível com o HDInsight, além de outras informações de controle de versão do componente, confira [Versões de componente do HDInsight](hdinsight-component-versioning.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migration-tasks"></a>Tarefas de migração

Veja a seguir o fluxo de trabalho geral da migração.

![Diagrama do fluxo de trabalho da migração](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1. Leia cada seção deste documento para entender as alterações que podem ser necessárias ao migrar.

2. Crie um cluster baseado em Linux como um ambiente de teste/controle de qualidade. Para saber mais sobre como criar um cluster baseado em Linux, confira [Criar clusters Hadoop baseados em Linux em HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

3. Copie trabalhos, fontes de dados e coletores existentes para o novo ambiente.

4. Execute testes de validação para garantir que os trabalhos funcionem conforme o esperado no novo cluster.

Depois de verificar se tudo está funcionando conforme o esperado, agende o tempo de inatividade para a migração. Durante esse tempo de inatividade, execute as seguintes tarefas:

1. Faça backup de dados transitórios armazenados localmente em nós do cluster. Por exemplo, se você tiver dados armazenados diretamente em um nó principal.

2. Exclua o cluster baseado no Windows.

3. Crie um cluster baseado no Linux usando o mesmo repositório de dados padrão que o cluster baseado no Windows usava. O cluster do Linux pode continuar trabalhando nos dados de produção existentes.

4. Importe o backup de todos os dados transitórios.

5. Inicie os trabalhos/continue processando usando o novo cluster.

### <a name="copy-data-to-the-test-environment"></a>Copie os dados para o ambiente de teste.

Há vários métodos para copiar os dados e trabalhos. No entanto, os dois abordados nesta seção são os métodos mais simples para mover os arquivos diretamente para um cluster de teste.

#### <a name="hdfs-copy"></a>Cópia do HDFS

Use as etapas a seguir para copiar os dados do cluster de produção para o cluster de teste. Essas etapas usam o utilitário `hdfs dfs` incluído no HDInsight.

1. Encontre as informações sobre a conta de armazenamento e o contêiner padrão do cluster existente. O seguinte exemplo usa o PowerShell para recuperar estas informações:

    ```powershell
    $clusterName="Your existing HDInsight cluster name"
    $clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
    write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
    write-host "Default container: $clusterInfo.DefaultStorageContainer"
    ```

2. Para criar um ambiente de teste, siga as etapas descritas no documento Criar clusters baseados em Linux no HDInsight. Pare antes de criar o cluster e escolha **Configuração Opcional**.

3. Na seção Configuração Opcional, selecione **Contas de Armazenamento Vinculadas**.

4. Escolha **Adicionar uma chave de armazenamento**e, quando solicitado, escolha a conta de armazenamento que foi retornada pelo script do PowerShell na etapa 1. Clique em **Selecionar** em cada seção. E por último, crie o cluster.

5. Assim que o cluster tiver sido criado, conecte-o usando **SSH.** Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

6. Na sessão SSH, use o comando a seguir para copiar arquivos da conta de armazenamento vinculada para a nova conta de armazenamento padrão. Substitua CONTÊINER pelas informações de contêiner retornadas pelo PowerShell. Substitua __CONTA__ pelo nome da conta. Substitua o caminho para dados pelo caminho para um arquivo de dados.

    ```bash
    hdfs dfs -cp wasb://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location
    ```

    > [!NOTE]  
    > Se a estrutura de diretório que contém os dados não existir no ambiente de teste, é possível criá-la usando o seguinte comando:

    ```bash
    hdfs dfs -mkdir -p /new/path/to/create
    ```

    A opção `-p` permite a criação de todos os diretórios no caminho.

#### <a name="direct-copy-between-blobs-in-azure-storage"></a>Cópia direta entre blobs do Armazenamento do Azure

Como alternativa, talvez seja conveniente usar o cmdlet `Start-AzStorageBlobCopy` do Azure PowerShell para copiar blobs entre contas de armazenamento fora do HDInsight. Para saber mais, confira a seção Como gerenciar blobs do Azure em Usando o Azure PowerShell com o Armazenamento do Azure.

## <a name="client-side-technologies"></a>Tecnologias do lado do cliente

As tecnologias do lado do cliente, como os [cmdlets do Azure PowerShell](/powershell/azureps-cmdlets-docs), [CLI do Azure Classic](../cli-install-nodejs.md) ou o [.NET SDK do Apache Hadoop](https://hadoopsdk.codeplex.com/) continuam a trabalhar em clusters baseados no Linux. Essas tecnologias dependem de APIs REST, que são as mesmas em ambos os tipos de sistema operacional do cluster.

## <a name="server-side-technologies"></a>Tecnologias do lado do servidor

A tabela a seguir fornece orientações de como migrar componentes do lado servidor específicos do Windows.

| Se estiver usando esta tecnologia... | Execute esta ação... |
| --- | --- |
| **PowerShell** (scripts do lado do servidor, incluindo as Ações de Script usadas durante a criação do cluster) |Reescreva-os como scripts Bash. Para as Ações de Script, confira [Personalizar clusters HDInsight baseados em Linux usando as Ações de Script](hdinsight-hadoop-customize-cluster-linux.md) e [Desenvolvimento de ação de script com o HDInsight](hdinsight-hadoop-script-actions-linux.md). |
| **CLI do Azure Classic** (scripts do lado do servidor) |Embora a CLI Clássica do Azure esteja disponível no Linux, ela não vem pré-instalada nos nós principais do cluster do HDInsight. Para obter mais informações sobre como instalar a CLI Clássica do Azure, consulte [Introdução à CLI do Azure Classic](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli). |
| **Componentes do .NET** |O .NET tem suporte no HDInsight do Linux por meio do [Mono](https://mono-project.com). Para obter mais informações, consulte [Migrar soluções .NET para HDInsight baseado em Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md). |
| **Componentes do Win32 ou de outra tecnologia exclusiva do Windows** |As diretrizes dependem do componente ou da tecnologia. Você poderá encontrar uma versão compatível com o Linux. Caso contrário, você deve encontrar uma solução alternativa ou regenerar esse componente. |

> [!IMPORTANT]  
> O SDK de gerenciamento de HDInsight não é totalmente compatível com o Mono. Não o use como parte das soluções que são implantadas no cluster do HDInsight.

## <a name="cluster-creation"></a>Criação do cluster

Esta seção fornece informações sobre as diferenças na criação do cluster.

### <a name="ssh-user"></a>Usuário do SSH

Os clusters do HDInsight baseados em Linux usam o protocolo **SSH (Secure Shell)** para fornecer acesso remoto aos nós do cluster. Ao contrário dos clusters baseados em Área de Trabalho Remota para Windows, a maioria dos clientes SSH não fornecem uma experiência gráfica ao usuário. Em vez disso, os clientes SSH fornecem uma linha de comando que permite executar comandos no cluster. Alguns clientes (como a [MobaXterm](https://mobaxterm.mobatek.net/)) fornecem um navegador gráfico de sistema de arquivos além da linha de comando remota.

Durante a criação do cluster, você deve fornecer um usuário SSH e uma **senha** ou um **certificado de chave pública** para autenticação.

É recomendável usar um certificado de chave pública, pois é mais seguro do que usar uma senha. A autenticação de certificado funciona gerando um par de chaves pública/privada assinada e fornecendo a chave pública na criação do cluster. Ao se conectar ao servidor usando o SSH, a chave privada no cliente fornece autenticação para a conexão.

Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="cluster-customization"></a>Personalização do cluster

**Ações de Script** usadas com clusters baseados em Linux devem ser escritas no script Bash. Clusters baseados em Linux podem usar ações de script durante ou após a criação do cluster. Para obter mais informações, confira [Personalizar clusters HDInsight baseados em Linux usando as Ação de Script](hdinsight-hadoop-customize-cluster-linux.md) e [Desenvolvimento de ação de script com o HDInsight](hdinsight-hadoop-script-actions-linux.md).

Outro recurso de personalização é a **inicialização**. Em clusters Windows, esse recurso permite especificar a localização das bibliotecas adicionais para uso com o Hive. Após a criação do cluster, essas bibliotecas são automaticamente disponibilizadas para uso com as consultas do Hive sem a necessidade de usar o `ADD JAR`.

O recurso Inicialização para clusters baseados em Linux não fornece essa funcionalidade. Em vez disso, use a ação de script documentada em [Adicionar bibliotecas do Apache Hive durante a criação do cluster](hdinsight-hadoop-add-hive-libraries.md).

### <a name="virtual-networks"></a>Redes Virtuais

Os clusters HDInsight baseados em Windows funcionam apenas com Rede Virtuais Clássicas, enquanto os clusters HDInsight baseados em Linux exigem Redes Virtuais do Resource Manager. Se você tiver recursos em uma Rede Virtual Clássica à qual o cluster HDInsight Linux deve se conectar, confira [Conectando redes virtuais clássicas a novas redes virtuais](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

Para saber mais sobre os requisitos de configuração, confira [Estender recursos do HDInsight usando uma Rede virtual](hdinsight-extend-hadoop-virtual-network.md).

## <a name="management-and-monitoring"></a>Gerenciamento e monitoramento

Muitas das interfaces de usuário que você pode ter usado com HDInsight baseado em Windows, a interface de usuário do Yarn ou do Histórico de Trabalhos, estão disponíveis por meio do Apache Ambari. Além disso, o Modo de Exibição do Hive do Ambari fornece uma maneira de executar consultas Hive usando o navegador da Web. A interface do usuário da Web do Ambari está disponível em clusters baseados em Linux em https://CLUSTERNAME.azurehdinsight.net.

Para saber mais sobre como trabalhar com o Ambari, confira os seguintes documentos:

* [Apache Ambari Web](hdinsight-hadoop-manage-ambari.md)
* [API REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Alertas do Ambari

O Ambari tem um sistema de alerta que pode informar a você sobre problemas potenciais com o cluster. Os alertas aparecem como entradas vermelhas ou amarelas na interface de usuário do Ambari Web. No entanto, você também pode recuperá-las por meio da API REST.

> [!IMPORTANT]  
> Os alertas do Ambari indicam que *pode* haver um problema, e não que *há* um problema. Por exemplo, você pode receber um alerta de que HiveServer2 não pode ser acessado, mesmo que consiga acessá-lo normalmente.
>
> Vários alertas são implementados como consultas baseadas em intervalo em um serviço e esperam uma resposta por um período específico. Portanto, o alerta não significa necessariamente que o serviço está inativo, apenas que ele não retornou resultados dentro do tempo esperado.

## <a name="file-system-locations"></a>Locais do sistema de arquivos

O sistema de arquivos do cluster Linux é disposto de modo diferente dos clusters HDInsight baseados em Windows. Use a tabela a seguir para encontrar arquivos usados com frequência.

| Preciso encontrar... | Está localizado em... |
| --- | --- |
| Configuração |`/etc`. Por exemplo, `/etc/hadoop/conf/core-site.xml` |
| Arquivos de log |`/var/logs` |
| HDP (Hortonworks Data Platform) |`/usr/hdp`.Há dois diretórios localizados aqui: um que é a versão atual do HDP e `current`. O diretório `current` contém links simbólicos para arquivos e diretórios localizados no diretório do número de versão. O diretório `current` é fornecido como uma maneira conveniente de acessar arquivos do HDP, já que o número de versão muda conforme a versão do HDP é atualizada. |
| hadoop-streaming.jar |`/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

De modo geral, se você souber o nome do arquivo, será possível usar o comando a seguir em uma sessão do SSH para encontrar o caminho do arquivo:

    find / -name FILENAME 2>/dev/null

Você também pode usar caracteres curinga com o nome do arquivo. Por exemplo, `find / -name *streaming*.jar 2>/dev/null` retorna o caminho para os arquivos jar que contêm a palavra “transmissão” como parte do nome do arquivo.

## <a name="apache-hive-apache-pig-and-mapreduce"></a>Apache Hive, Apache Pig e MapReduce

Cargas de trabalho do Pig e do MapReduce são muito semelhantes em clusters baseados em Linux. No entanto, os clusters HDInsight baseados em Linux podem ser criados usando versões mais recentes do Hadoop, Hive e Pig. Essas diferenças de versão podem introduzir alterações no funcionamento das suas soluções existentes. Para obter mais informações sobre a versão dos componentes incluída no HDInsight, consulte [Controle de versão do componente do HDInsight](hdinsight-component-versioning.md).

O HDInsight baseado em Linux não oferece a funcionalidade de área de trabalho remota. Em vez disso, você pode usar o SSH para se conectar remotamente aos nós principais do cluster. Para obter mais informações, consulte um dos seguintes documentos:

* [Usar o Apache Hive com SSH](hdinsight-hadoop-use-hive-ssh.md)
* [Usar o Apache Pig com SSH](hadoop/apache-hadoop-use-pig-ssh.md)
* [Usar o MapReduce com o SSH](hadoop/apache-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive

> [!IMPORTANT]  
> Se você usar um metastore do Hive externo, deverá fazer backup do metastore antes de usá-lo com o HDInsight baseado em Linux. O HDInsight baseado em Linux está disponível com versões mais recentes do Hive, o que pode trazer incompatibilidades com metastores criados por versões anteriores.

A tabela a seguir fornece instruções para migrar as cargas de trabalho do Hive.

| Com base no Windows, uso... | Com base no Linux... |
| --- | --- |
| **Editor de Hive** |[Modo de Exibição do Apache Hive no Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;` para habilitar o Tez |O Apache Tez é o mecanismo de execução padrão para clusters baseados em Linux, de modo que a instrução set não é mais necessária. |
| Funções C# definidas pelo usuário | Para obter informações sobre como validar componentes C# com o HDInsight baseado em Linux, consulte [Migrar soluções .NET para HDInsight baseado em Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| Arquivos ou scripts CMD no servidor invocados como parte de um trabalho do Hive |Scripts Bash |
| `hive` na Área de Trabalho Remota |Use o [Apache Hive Beeline](hadoop/apache-hadoop-use-hive-beeline.md) ou o [Apache Hive em uma sessão do SSH](hdinsight-hadoop-use-hive-ssh.md) |

### <a name="pig"></a>Pig

| Com base no Windows, uso... | Com base no Linux... |
| --- | --- |
| Funções C# definidas pelo usuário | Para obter informações sobre como validar componentes C# com o HDInsight baseado em Linux, consulte [Migrar soluções .NET para HDInsight baseado em Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| Arquivos ou scripts CMD no servidor invocados como parte de um trabalho do Pig |Scripts Bash |

### <a name="mapreduce"></a>MapReduce

| Com base no Windows, uso... | Com base no Linux... |
| --- | --- |
| Componentes mapeador e redutor de C# | Para obter informações sobre como validar componentes C# com o HDInsight baseado em Linux, consulte [Migrar soluções .NET para HDInsight baseado em Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| Arquivos ou scripts CMD no servidor invocados como parte de um trabalho do Hive |Scripts Bash |

## <a name="apache-oozie"></a>Apache Oozie

> [!IMPORTANT]  
> Se você usar um metastore do Oozie externo, deverá fazer backup do metastore antes de usá-lo com o HDInsight baseado em Linux. O HDInsight baseado em Linux está disponível com versões mais recentes do Oozie, o que pode trazer incompatibilidades com metastores criados por versões anteriores.

Fluxos de trabalho do Oozie permitem realizar ações de shell. Ações de shell usam o shell padrão do sistema operacional para executar comandos de linha de comando. Se você tiver fluxos de trabalho do Oozie que dependem do shell do Windows, reescreva os fluxos de trabalho para dependerem do ambiente de shell do Linux (Bash). Para obter mais informações sobre como usar ações de shell com Oozie, consulte [Extensão da ação de shell do Oozie](https://oozie.apache.org/docs/3.3.0/DG_ShellActionExtension.html).

Se você tiver um fluxo de trabalho que usa um aplicativo C#, valide esses aplicativos em um ambiente do Linux. Para obter mais informações, consulte [Migrar soluções .NET para HDInsight baseado em Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="storm"></a>Storm

| Com base no Windows, uso... | Com base no Linux... |
| --- | --- |
| Painel de Controle do Storm |O Painel Storm não está disponível. Confira [Implantar e Gerenciar as topologias do Apache Storm no HDInsight baseado em Linux](storm/apache-storm-deploy-monitor-topology-linux.md) para saber como enviar topologias |
| Interface do Usuário do Storm |A interface do usuário do Storm está disponível em https://CLUSTERNAME.azurehdinsight.net/stormui |
| Visual Studio para criar, implantar e gerenciar topologias híbridas ou do C# |O Visual Studio pode ser usado para criar, implantar e gerenciar topologias do C# (SCP.NET) ou híbridas em Storm no HDInsight baseado em Linux. Ele só pode ser usado com clusters criados após 28/10/2016. |

## <a name="apache-hbase"></a>HBase no Apache

Em clusters baseados em Linux, o znode pai do HBase é `/hbase-unsecure`. Defina esse valor na configuração dos aplicativos cliente Java que usam a API nativa do Java para HBase.

Confira [Build a Java-based Apache HBase application](hbase/apache-hbase-build-java-maven-linux.md) (Criar aplicativo HBase baseado em Java) para obter um exemplo de cliente que defina esse valor.

## <a name="spark"></a>Spark

Os clusters Spark estavam disponíveis em clusters do Windows durante a visualização. O Spark GA só está disponível em clusters baseados em Linux. Não há caminho de migração de um cluster da versão prévia do Spark baseado em Windows para um cluster de lançamento baseado em Linux.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="azure-data-factory-custom-net-activities"></a>Atividades personalizadas do .NET no Azure Data Factory

Atualmente, atividades personalizadas do .NET no Azure Data Factory não são compatíveis com clusters HDInsight baseados em Linux. Sendo assim, você deve usar um dos métodos a seguir para implementar atividades personalizadas como parte do seu pipeline ADF.

* Execute atividades do .NET no pool do Lote do Azure. Veja a seção Usar serviço vinculado do Lote do Azure em [Usar atividades personalizadas em um pipeline do Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md)
* Implemente a atividade como uma atividade do MapReduce. Para obter mais informações, consulte [Invocar programas do MapReduce por meio do Data Factory](../data-factory/transform-data-using-hadoop-map-reduce.md).

### <a name="line-endings"></a>Terminações de linha

No geral, as terminações de linha em sistemas baseados no Windows usam CRLF, enquanto os sistemas baseados no Linux usam LF. Talvez seja necessário modificar os produtores de dados existentes e consumidores para trabalhar com LF.

Por exemplo, usar o Azure PowerShell para consultar o HDInsight em um cluster baseado no Windows retorna dados com CRLF. A mesma consulta com um cluster baseado em Linux retorna LF. Teste para ver se a terminação de linha causa um problema na solução antes de migrar para um cluster baseado em Linux.

Sempre use LF como a linha final para scripts que são executados em nós de cluster. Caso use CRLF, você poderá ver erros ao executar os scripts em um cluster baseado em Linux.

Se os scripts não contiverem cadeias com caracteres CR inseridos, será possível fazer uma alteração em massa nas terminações de linha usando um dos métodos a seguir:

* **Antes de carregar para o cluster**: Use as seguintes instruções do PowerShell para alterar as terminações de linha de CRLF para LF antes de carregar o script para o cluster.

    ```powershell
    $original_file ='c:\path\to\script.py'
    $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
    [IO.File]::WriteAllText($original_file, $text)
    ```

* **Depois de carregar para o cluster**: Use o seguinte comando em uma sessão SSH para o cluster do Linux para modificar o script.

    ```bash
    hdfs dfs -get wasb:///path/to/script.py oldscript.py
    tr -d '\r' < oldscript.py > script.py
    hdfs dfs -put -f script.py wasb:///path/to/script.py
    ```

## <a name="next-steps"></a>Próximas etapas

* [Saiba como criar clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Usar SSH para se conectar ao HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Gerenciar um cluster baseado em Linux usando o Apache Ambari](hdinsight-hadoop-manage-ambari.md)
