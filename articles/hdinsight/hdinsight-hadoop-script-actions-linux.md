---
title: Desenvolver ações de script para personalizar os clusters de HDInsight do Azure
description: Saiba como usar scripts Bash para personalizar os clusters do HDInsight. Ações de script permitem executar scripts durante ou após a criação do cluster para alterar as configurações de cluster ou instalar software adicional.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: 66132a2a6a7b5b89bca0767efe7c194ca3dec051
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60590788"
---
# <a name="script-action-development-with-hdinsight"></a>Desenvolvimento de ação de script com o HDInsight

Saiba como personalizar o cluster HDInsight usando scripts Bash. Ações de script são uma maneira de personalizar o HDInsight durante ou após a criação do cluster.

## <a name="what-are-script-actions"></a>O que são ações de script

As ações de script são scripts Bash que o Azure executa em nós de cluster para fazer alterações de configuração ou para instalar software. A Ação de Script é executada como raiz e concede direitos de acesso completo a nós do cluster.

As ações de script podem ser aplicadas por meio dos seguintes métodos:

| Use este método para aplicar um script... | Durante a criação do cluster... | Em um cluster em execução... |
| --- |:---:|:---:|
| Portal do Azure |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| CLI clássica do Azure |&nbsp; |✓ |
| SDK do .NET do HDInsight |✓ |✓ |
| Modelo do Azure Resource Manager |✓ |&nbsp; |

Para saber mais sobre o uso desses métodos para aplicar ações de script, confira [Personalizar clusters HDInsight usando ações de script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Práticas recomendadas para o desenvolvimento de scripts

Ao desenvolver um script personalizado para um cluster HDInsight, há várias práticas recomendadas para se considerar:

* [Direcionar para a versão do Apache Hadoop](#bPS1)
* [Direcionar para a versão do sistema operacional](#bps10)
* [Fornecer links estáveis para recursos de script](#bPS2)
* [Usar os recursos pré-compilados](#bPS4)
* [Certifique-se de que o script de personalização do cluster seja idempotente](#bPS3)
* [Garantir alta disponibilidade da arquitetura de cluster](#bPS5)
* [Configurar os componentes personalizados para usar armazenamento de Blob do Azure](#bPS6)
* [Gravar informações para STDOUT e STDERR](#bPS7)
* [Salvar arquivos como ASCII com terminações de linha LF](#bps8)
* [Usar a lógica de repetição para recuperar-se de erros transitórios](#bps9)

> [!IMPORTANT]  
> Ações de script devem ser concluídas em 60 minutos ou o processo falha. Durante o provisionamento de nó, o script é executado simultaneamente com outros processos de instalação e configuração. A competição por recursos, como tempo de CPU ou largura de banda rede, pode fazer com que o script leve mais tempo para ser concluído comparado ao seu tempo de conclusão no ambiente de desenvolvimento.

### <a name="bPS1"></a>Direcionar para a versão do Apache Hadoop

Diferentes versões do HDInsight têm diferentes versões de componentes e serviços do Hadoop instaladas. Se seu script espera uma versão específica de um serviço ou componente, você deve usar somente o script com a versão do HDInsight que inclui os componentes necessários. Você pode encontrar informações sobre versões dos componentes incluídos com o HDInsight usando o documento [Controle de versão de componente do HDInsight](hdinsight-component-versioning.md) .

### <a name="checking-the-operating-system-version"></a>Verificando a versão do sistema operacional

Versões diferentes do HDInsight contam com versões específicas do Ubuntu. Pode haver diferenças entre as versões de sistema operacional, que você deve verificar em seu script. Por exemplo, talvez seja necessário instalar um binário que esteja vinculado à versão do Ubuntu.

Para verificar a versão do sistema operacional, use `lsb_release`. Por exemplo, o script de exemplo a seguir demonstra como fazer referência a um arquivo tar específico dependendo da versão do SO:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
```

### <a name="bps10"></a> A versão do sistema operacional de destino

O HDInsight baseado em Linux baseia-se na distribuição do Ubuntu do Linux. Versões diferentes do HDInsight contam com versões diferentes do Ubuntu, o que pode afetar o comportamento do seu script. Por exemplo, HDInsight 3.4 e versões mais recentes são baseados em versões do Ubuntu que usam o Upstart. As versões 3.5 e posteriores se baseiam no Ubuntu 16.04, que usa Systemd. O Systemd e Upstart contam com comandos diferentes, portanto seu script deve ser escrito para funcionar com ambos.

Outra diferença importante entre o HDInsight 3.4 e o 3.5 é que o `JAVA_HOME` agora aponta para o Java 8. O código a seguir demonstra como determinar se o script está sendo executado no Ubuntu 14 ou 16:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
...
if [[ $OS_VERSION == 16* ]]; then
    echo "Using systemd configuration"
    systemctl daemon-reload
    systemctl stop webwasb.service    
    systemctl start webwasb.service
else
    echo "Using upstart configuration"
    initctl reload-configuration
    stop webwasb
    start webwasb
fi
...
if [[ $OS_VERSION == 14* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
elif [[ $OS_VERSION == 16* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
fi
```

Você pode encontrar o script completo que contém esses snippets de código em https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh.

Para a versão do Ubuntu, que é usada pelo HDInsight, consulte o documento [Versão do componente HDInsight](hdinsight-component-versioning.md).

Para entender as diferenças entre Systemd e Upstart, consulte [Systemd para usuários do Upstart](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="bPS2"></a>Fornecer links estáveis para recursos de script

O script e recursos associados devem permanecer disponíveis durante o tempo de vida do cluster. Esses recursos serão necessários se novos nós forem adicionados ao cluster durante as operações de dimensionamento.

A melhor prática é baixar e arquivar tudo em uma conta de Armazenamento do Azure em sua assinatura.

> [!IMPORTANT]  
> A conta de armazenamento usada deve ser a conta de armazenamento padrão para o cluster ou então em um contêiner público somente leitura em qualquer outra conta de armazenamento.

Por exemplo, os exemplos fornecidos pela Microsoft são armazenados na conta de armazenamento [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/). Esse local é um contêiner público somente leitura mantido pela equipe do HDInsight.

### <a name="bPS4"></a>Usar os recursos pré-compilados

Para reduzir o tempo necessário de execução do script, evite operações que compilem recursos do código-fonte. Por exemplo, pré-compile recursos e armazene-os em um blob da conta de Armazenamento do Azure no mesmo data center que o HDInsight.

### <a name="bPS3"></a>Certifique-se de que o script de personalização do cluster seja idempotente

Os scripts devem ser idempotentes. Se o script for executado várias vezes, ele deverá retornar o cluster ao mesmo estado a cada vez.

Por exemplo, um script que modifica os arquivos de configuração não deve adicionar entradas duplicadas se executado várias vezes.

### <a name="bPS5"></a>Garantir alta disponibilidade da arquitetura de cluster

Os clusters HDInsight baseados em Linux fornecem dois nós de cabeçalho que estão ativos dentro do cluster; as ações de script são executadas em ambos esses nós. Se os componentes de que instalação esperam apenas um nó principal, não instale os componentes em ambos os nós de cabeçalho.

> [!IMPORTANT]  
> Os serviços fornecidos como parte do HDInsight são projetados para failover entre os dois nós de cabeçalho, conforme necessário. Essa funcionalidade não se estende a componentes personalizados instalados por meio de ações de script. Se você precisar de alta disponibilidade para componentes personalizados, você deverá implementar seu próprio mecanismo de failover.

### <a name="bPS6"></a>Configurar os componentes personalizados para usar armazenamento de Blob do Azure

Os componentes que você instala no cluster podem ter uma configuração padrão que usa o armazenamento HDFS (Sistema de Arquivos Distribuído do Apache Hadoop). O HDInsight usa o Armazenamento de Blobs do Azure ou o Azure Data Lake Storage como o repositório padrão. Ambos fornecem um sistema de arquivos compatível com HDFS que faz os dados persistirem mesmo que o cluster seja excluído. Talvez você precise configurar os componentes instalados para usar o WASB ou ADL em vez de HDFS.

Para a maioria das operações, você não precisa especificar o sistema de arquivos. Por exemplo, a seguir copia o arquivo hadoop common.jar do sistema de arquivos local para o armazenamento de cluster:

```bash
hdfs dfs -put /usr/hdp/current/hadoop-client/hadoop-common.jar /example/jars/
```

Neste exemplo, o comando `hdfs` usa o armazenamento de cluster padrão de modo transparente. Para algumas operações, talvez seja necessário especificar o URI. Por exemplo, `adl:///example/jars` para Azure Data Lake Storage Gen1, `abfs:///example/jars` para Data Lake Storage Gen2 ou `wasb:///example/jars` para Azure Storage.

### <a name="bPS7"></a>Gravar informações para STDOUT e STDERR

HDInsight registra em log a saída do script que é gravada para STDOUT e STDERR. Você pode exibir essas informações usando a interface do usuário da Web do Ambari.

> [!NOTE]  
> O Apache Ambari só estará disponível se o cluster for criado com êxito. Se você usar uma ação de script durante a criação do cluster e a criação falhar, confira a seção de solução de problemas [Personalizar clusters HDInsight usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) para conhecer outras maneiras de acessar informações registradas em log.

A maioria dos pacotes de instalação e utilitários já grava informações para STDOUT e STDERR; no entanto, talvez você queira adicionar registro em log adicional. Para enviar texto para STDOUT, use `echo`. Por exemplo: 

```bash
echo "Getting ready to install Foo"
```

Por padrão, `echo` envia a cadeia de caracteres para STDOUT. Para direcioná-lo para STDERR, adicione `>&2` antes de `echo`. Por exemplo: 

```bash
>&2 echo "An error occurred installing Foo"
```

Isso redireciona as informações gravadas em STDOUT para STDERR (2) em vez disso. Para obter mais informações sobre redirecionamento de E/S, consulte [ https://www.tldp.org/LDP/abs/html/io-redirection.html ](https://www.tldp.org/LDP/abs/html/io-redirection.html).

Para saber mais sobre exibição de informações registradas em log por ações de script, confira [Personalizar clusters HDInsight usando ação de script](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

### <a name="bps8"></a> Salvar arquivos como ASCII com terminações de linha LF

Scripts de Bash devem ser armazenados com formato ASCII, com linhas terminadas em LF. Os arquivos que são armazenados como UTF-8 ou usam CRLF como o terminação de linha podem falhar com o seguinte erro:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="bps9"></a> Usar a lógica de repetição para recuperar-se de erros transitórios

Ao baixar arquivos de instalação de pacotes usando apt-get ou outras ações que transmitem dados pela Internet, a ação pode falhar devido a erros de rede temporários. Por exemplo, o recurso remoto com o qual você está se comunicando pode estar em processo de failover para um nó de backup.

Para tornar o script resistente a erros transitórios, você pode implementar lógica de repetição. A função a seguir demonstra como implementar a lógica de repetição. Ela tenta realizar a operação novamente três vezes antes de falhar.

```bash
#retry
MAXATTEMPTS=3

retry() {
    local -r CMD="$@"
    local -i ATTMEPTNUM=1
    local -i RETRYINTERVAL=2

    until $CMD
    do
        if (( ATTMEPTNUM == MAXATTEMPTS ))
        then
                echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                return 1
        else
                echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                sleep $(( RETRYINTERVAL ))
                ATTMEPTNUM=$ATTMEPTNUM+1
        fi
    done
}
```

Os exemplos a seguir demonstram como usar essa função.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helpermethods"></a>Métodos auxiliares para scripts personalizados

Os métodos auxiliares da Ação de Script são utilitários que podem ser usados durante a gravação de scripts personalizados. Esses métodos estão contidos no script [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh). Use o seguinte para baixá-los e usá-los como parte do script:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

Os auxiliares a seguir, disponíveis para uso em seu script:

| Uso do auxiliar | DESCRIÇÃO |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Baixa um arquivo da URI de origem para o caminho de arquivo especificado. Por padrão, ele não substitui um arquivo existente. |
| `untar_file TARFILE DESTDIR` |Extrai um arquivo tar (usando `-xf`) para o diretório de destino. |
| `test_is_headnode` |Se executado em um nó de cabeçalho do cluster, retorna 1. Caso contrário, 0. |
| `test_is_datanode` |Se o nó atual é um nó de dados (de trabalho), retorna 1; caso contrário, 0. |
| `test_is_first_datanode` |Se o nó atual é o primeiro nó de dados (de trabalho, chamado workernode0), retorna 1; caso contrário, retorna 0. |
| `get_headnodes` |Retorna o nome de domínio totalmente qualificado dos nós de cabeçalho no cluster. Os nomes são delimitados por vírgula. Uma cadeia de caracteres vazia retorna em caso de erro. |
| `get_primary_headnode` |Obtém o nome de domínio totalmente qualificado do nó de cabeçalho primário. Uma cadeia de caracteres vazia retorna em caso de erro. |
| `get_secondary_headnode` |Obtém o nome de domínio totalmente qualificado do nó de cabeçalho secundário. Uma cadeia de caracteres vazia retorna em caso de erro. |
| `get_primary_headnode_number` |Obtém o sufixo numérico do nó de cabeçalho primário. Uma cadeia de caracteres vazia retorna em caso de erro. |
| `get_secondary_headnode_number` |Obtém o sufixo numérico do nó de cabeçalho secundário. Uma cadeia de caracteres vazia retorna em caso de erro. |

## <a name="commonusage"></a>Padrões comuns de uso

Esta seção fornece orientações sobre como implementar alguns dos padrões comuns de uso que você pode encontrar ao escrever seu próprio script personalizado.

### <a name="passing-parameters-to-a-script"></a>Passando parâmetros para um script

Em alguns casos, seu script pode exigir parâmetros. Por exemplo, a senha do administrador para o cluster talvez seja necessária ao usar a API REST do Ambari.

Os parâmetros passados para o script são conhecidos como *parâmetros posicionais* e são atribuídos a `$1` para o primeiro parâmetro, `$2` para o segundo e assim por diante. `$0` contém o nome do próprio script.

Valores passados para o script como parâmetros devem ser inseridos entre aspas simples ('). Isso garante que o valor passado seja tratado como um literal.

### <a name="setting-environment-variables"></a>Configurando variáveis de ambiente

Definir uma variável de ambiente é uma ação realizada pela seguinte instrução:

    VARIABLENAME=value

Em que VARIABLENAME é o nome da variável. Para acessar a variável, use `$VARIABLENAME`. Por exemplo, para atribuir um valor fornecido por um parâmetro posicional como uma variável de ambiente denominada PASSWORD, use a seguinte instrução:

    PASSWORD=$1

O acesso subsequente às informações poderia, então, usar `$PASSWORD`.

Variáveis de ambiente definidas no script existem somente dentro do escopo do script. Em alguns casos, talvez seja necessário adicionar variáveis de ambiente de todo o sistema que persistirão após a conclusão do script. Para adicionar variáveis de ambiente de todo o sistema, adicione a variável a `/etc/environment`. Por exemplo, a instrução a seguir retornará `HADOOP_CONF_DIR`:

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Acesso a locais onde estão armazenados os scripts personalizados

Scripts usados para personalizar um cluster devem ser armazenados em um dos seguintes locais:

* Uma __conta de armazenamento do Azure__ associada ao cluster.

* Um __conta de armazenamento adicional__ associada ao cluster.

* Um URI __que pode ser lido publicamente__. Por exemplo, uma URL para dados armazenados no OneDrive, Dropbox ou outro serviço de hospedagem de arquivos.

* Uma __conta do Azure Data Lake Storage__ que está associada com o cluster do HDInsight. Para obter mais informações sobre como usar o Azure Data Lake Storage com HDInsight, consulte o [Início Rápido: Configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

    > [!NOTE]  
    > A entidade de serviço que HDInsight usa para acessar o Data Lake Storage deve ter acesso de leitura para o script.

Recursos usados pelo script também devem estar publicamente disponíveis.

Armazenar os arquivos em uma conta de Armazenamento do Azure ou Azure Data Lake Storage fornece acesso rápido, pois ambos estão dentro da rede do Azure.

> [!NOTE]  
> O formato de URI usado para referenciar o script difere dependendo do serviço que está sendo usado. Para contas de armazenamento associadas ao cluster HDInsight, use `wasb://` ou `wasbs://`. Para URIs lidas publicamente, use `http://` ou `https://`. Para o Data Lake Storage, use `adl://`.

## <a name="deployScript"></a>Lista de verificação para implantação de uma ação de script

Aqui estão as etapas que realizamos para se preparar para implantar um script:

* Coloque os arquivos que contêm os scripts personalizados em um local que seja acessível pelos nós de cluster durante a implantação. Por exemplo, o armazenamento padrão para o cluster. Arquivos também podem ser armazenados nos serviços de hospedagem legíveis publicamente.
* Verifique se o script está idempotente. Isso permite que o script seja executado várias vezes no mesmo nó.
* Use um diretório de arquivos temporário, /tmp, para manter os arquivos baixados usados pelos scripts e em seguida limpá-los, depois de os scripts terem sido executados.
* Se as configurações de nível de SO ou arquivos de configuração de serviço do Hadoop forem alterados, será conveniente reiniciar os serviços do HDInsight.

## <a name="runScriptAction"></a>Como executar uma Ação de Script

Você pode usar ações de script para personalizar os clusters HDInsight usando os seguintes métodos:

* Portal do Azure
* Azure PowerShell
* Modelos do Azure Resource Manager
* O SDK .NET do HDInsight.

Para obter mais informações sobre como usar cada método, consulte [Como usar a ação de script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="sampleScripts"></a>Exemplos de script personalizado

A Microsoft fornece scripts de exemplo para instalar componentes em um cluster HDInsight. Consulte os links a seguir para obter mais ações de script de exemplo.

* [Instalar e usar o Hue em clusters HDInsight](hdinsight-hadoop-hue-linux.md)
* [Instalar e usar o Apache Giraph em clusters do HDInsight](hdinsight-hadoop-giraph-install-linux.md)

## <a name="troubleshooting"></a>solução de problemas

Estes são erros que você pode encontrar ao usar scripts desenvolvidos por você:

**Erro**: `$'\r': command not found`. Às vezes, seguido por `syntax error: unexpected end of file`.

*Causa*: Este erro é gerado quando as linhas em um script terminam com CRLF. Os sistemas UNIX esperam apenas LF como terminação da linha.

Esse problema ocorre geralmente quando o script é criado em um ambiente Windows, já que CRLF é uma terminação de linha comum para muitos editores de texto no Windows.

*Resolução*: Se for uma opção em seu editor de texto, selecione o formato Unix ou LF para terminação de linha. Você também pode usar os seguintes comandos em um sistema Unix para alterar o CRLF para um LF:

> [!NOTE]  
> Os comandos a seguir são a grosso modo equivalentes, no sentido que ambos devem alterar as terminações de linha CRLF para LF. Selecione um deles com base nos utilitários disponíveis no sistema.

| Comando | Observações |
| --- | --- |
| `unix2dos -b INFILE` |O backup do arquivo original é feito com uma extensão .BAK |
| `tr -d '\r' < INFILE > OUTFILE` |OUTFILE contém uma versão apenas com terminações LF |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Modifica o arquivo diretamente |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |OUTFILE contém uma versão apenas com terminações LF. |

**Erro**: `line 1: #!/usr/bin/env: No such file or directory`.

*Causa*: Este erro ocorre quando o script foi salvo como UTF-8 com uma BOM (Marca de Ordem de Byte).

*Resolução*: Salve o arquivo como ASCII ou UTF-8, sem nenhuma BOM. Você também pode usar o seguinte comando em um sistema Linux ou Unix para criar um arquivo sem a BOM:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Substitua `INFILE` com o arquivo que contém a BOM. `OUTFILE` deve ser um novo nome de arquivo contendo o script sem a BOM.

## <a name="seeAlso"></a>Próximas etapas

* Saiba como [Personalizar os clusters HDInsight usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md)
* Use a [referência do SDK do .NET do HDInsight](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight) para saber mais sobre a criação de aplicativos .NET que gerenciam o HDInsight
* Use a [API REST do HDInsight](https://msdn.microsoft.com/library/azure/mt622197.aspx) para aprender a usar o REST para executar ações de gerenciamento em clusters HDInsight.
