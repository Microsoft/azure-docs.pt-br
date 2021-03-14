---
title: Copiar dados do HDFS usando Azure Data Factory
description: Saiba como copiar dados de uma nuvem ou fonte de HDFS local para armazenamentos de dados de coletor com suporte usando a atividade de cópia em um pipeline de Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: jingwang
ms.openlocfilehash: 3ee1b1f48d91ba1245c0173d2e00a20778932d35
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367077"
---
# <a name="copy-data-from-the-hdfs-server-by-using-azure-data-factory"></a>Copiar dados do servidor HDFS usando Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-hdfs-connector.md)
> * [Versão atual](connector-hdfs.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como copiar dados do servidor de Sistema de Arquivos Distribuído do Hadoop (HDFS). Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

O conector HDFS tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com a [origem com suporte e a matriz de coletor](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Excluir atividade](delete-activity.md)

Especificamente, o conector HDFS dá suporte a:

- Copiar arquivos usando o *Windows* (Kerberos) ou autenticação *anônima* .
- Copiar arquivos usando o protocolo *webhdfs* ou o suporte *interno a DistCp* .
- Copiar arquivos como está ou analisando ou gerando arquivos com os [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Verifique se o tempo de execução de integração pode acessar *todos* os [servidor de nó de nome]: [porta do nó de nome] e [servidores de nó de dados]: [porta do nó de dados] do cluster Hadoop. A [porta do nó de nome] padrão é 50070 e a [porta do nó de dados] padrão é 50075.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao HDFS.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do HDFS:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade *Type* deve ser definida como *HDFS*. | Sim |
| url |A URL para o HDFS |Sim |
| authenticationType | Os valores permitidos são *Anonymous* ou *Windows*. <br><br> Para configurar seu ambiente local, consulte a seção [usar autenticação Kerberos para o conector HDFS](#use-kerberos-authentication-for-the-hdfs-connector) . |Sim |
| userName |O nome de usuário para a autenticação do Windows. Para a autenticação Kerberos, especifique **\<username> @ \<domain> . com**. |Sim (para autenticação do Windows) |
| password |A senha da autenticação do Windows. Marque este campo como uma SecureString para armazená-lo com segurança em seu data factory ou [faça referência a um segredo armazenado em um cofre de chaves do Azure](store-credentials-in-key-vault.md). |Sim (para a Autenticação do Windows) |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Para saber mais, consulte a seção [pré-requisitos](#prerequisites) . Se o Integration Runtime não for especificado, o serviço usará o Azure Integration Runtime padrão. |Não |

**Exemplo: usando a autenticação anônima**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: usando a autenticação do Windows**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades que estão disponíveis para definir conjuntos de os, consulte [DataSets in Azure data Factory](concepts-datasets-linked-services.md). 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As propriedades a seguir têm suporte para HDFS em `location` configurações no conjunto de entrada baseado em formato:

| Propriedade   | Descrição                                                  | Obrigatório |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A propriedade *Type* em `location` no DataSet deve ser definida como *HdfsLocation*. | Sim      |
| folderPath | O caminho para a pasta. Se você quiser usar um curinga para filtrar a pasta, ignore essa configuração e especifique o caminho nas configurações de origem da atividade. | Não       |
| fileName   | O nome do arquivo sob o folderPath especificado. Se você quiser usar um curinga para filtrar arquivos, ignore essa configuração e especifique o nome do arquivo nas configurações de origem da atividade. | Não       |

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa de seções e propriedades que estão disponíveis para definir atividades, consulte [pipelines e atividades no Azure data Factory](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem do HDFS.

### <a name="hdfs-as-source"></a>HDFS como fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As propriedades a seguir têm suporte para HDFS em `storeSettings` configurações na fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Obrigatório                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A propriedade *Type* em `storeSettings` deve ser definida como **HdfsReadSettings**. | Sim                                           |
| ***Localize os arquivos a serem copiados*** |  |  |
| OPÇÃO 1: caminho estático<br> | Copie da pasta ou do caminho do arquivo especificado no conjunto de um. Se quiser copiar todos os arquivos de uma pasta, especifique também `wildcardFileName` como `*`. |  |
| OPÇÃO 2: curinga<br>- wildcardFolderPath | O caminho da pasta com caracteres curinga para filtrar as pastas de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único). Use `^` para escapar se o nome real da pasta tiver um curinga ou esse caractere de escape dentro de. <br>Para obter mais exemplos, consulte [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). | Não                                            |
| OPÇÃO 2: curinga<br>- wildcardFileName | O nome do arquivo com caracteres curinga no folderPath/wildcardFolderPath especificado para filtrar os arquivos de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere); use `^` para escapar se o nome real do arquivo tiver um caractere curinga ou este caracter de escape dentro.  Para obter mais exemplos, consulte [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). | Sim |
| OPÇÃO 3: uma lista de arquivos<br>- fileListPath | Indica a cópia de um conjunto de arquivos especificado. Aponte para um arquivo de texto que inclui uma lista de arquivos que você deseja copiar (um arquivo por linha, com o caminho relativo para o caminho configurado no conjunto de um).<br/>Ao usar essa opção, não especifique o nome do arquivo no conjunto de um. Para obter mais exemplos, consulte [exemplos de lista de arquivos](#file-list-examples). |Não |
| ***Configurações adicionais*** |  | |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Quando `recursive` é definido como *true* e o coletor é um armazenamento baseado em arquivo, uma pasta ou subpasta vazia não é copiada ou criada no coletor. <br>Os valores permitidos são *true* (padrão) e *false*.<br>Essa propriedade não se aplica quando você configura `fileListPath`. |Não |
| deleteFilesAfterCompletion | Indica se os arquivos binários serão excluídos do repositório de origem após a movimentação com êxito para o repositório de destino. A exclusão do arquivo é por arquivo, portanto, quando a atividade de cópia falhar, você verá que alguns arquivos já foram copiados para o destino e excluídos da origem, enquanto outros ainda permanecem no repositório de origem. <br/>Esta propriedade só é válida no cenário de cópia de arquivos binários. O valor padrão: false. |Não |
| modifiedDatetimeStart    | Os arquivos são filtrados com base no atributo *modificado pela última vez*. <br>Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de `modifiedDatetimeStart` a `modifiedDatetimeEnd` . A hora é aplicada ao fuso horário UTC no formato *2018-12-01T05:00:00Z*. <br> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de valores.  Quando `modifiedDatetimeStart` tem um valor de DateTime, mas `modifiedDatetimeEnd` é nulo, significa que os arquivos cujo último atributo modificado é maior ou igual ao valor de DateTime são selecionados.  Quando `modifiedDatetimeEnd` tem um valor de DateTime, mas `modifiedDatetimeStart` é nulo, significa que os arquivos cujo último atributo modificado é menor que o valor de data e hora são selecionados.<br/>Essa propriedade não se aplica quando você configura `fileListPath`. | Não                                            |
| modifiedDatetimeEnd      | Mesmo que acima.  
| enablePartitionDiscovery | Para arquivos que são particionados, especifique se deseja analisar as partições do caminho do arquivo e adicioná-las como colunas de origem adicionais.<br/>Os valores permitidos são **false** (padrão) e **true**. | Não                                            |
| partitionRootPath | Quando a descoberta de partição estiver habilitada, especifique o caminho raiz absoluto para ler as pastas particionadas como colunas de dados.<br/><br/>Se não for especificado, por padrão,<br/>-Quando você usa o caminho do arquivo no conjunto de programas ou na lista de arquivos na origem, o caminho raiz da partição é o caminho configurado no conjunto de um.<br/>-Quando você usa o filtro de pasta curinga, o caminho raiz da partição é o subcaminho antes do primeiro caractere curinga.<br/><br/>Por exemplo, supondo que você configure o caminho no conjunto de um como "raiz/pasta/ano = 2020/mês = 08/dia = 27":<br/>-Se você especificar o caminho raiz da partição como "raiz/pasta/ano = 2020", a atividade de cópia irá gerar mais duas colunas `month` e `day` com o valor "08" e "27", respectivamente, além das colunas dentro dos arquivos.<br/>-Se o caminho raiz da partição não for especificado, nenhuma coluna extra será gerada. | Não                                            |
| maxConcurrentConnections | O número de conexões que podem se conectar ao armazenamento de armazenamento simultaneamente. Especifique um valor somente quando desejar limitar a conexão simultânea com o armazenamento de dados. | Não                                            |
| ***Configurações de DistCp*** |  | |
| distcpSettings | O grupo de propriedades a ser usado ao usar o HDFS DistCp. | Não |
| resourceManagerEndpoint | O ponto de extremidade YARN (ainda outro negociador de recursos) | Sim, se estiver usando DistCp |
| tempScriptPath | Um caminho de pasta que é usado para armazenar o script de comando Temp DistCp. O arquivo de script é gerado pelo Data Factory e será removido depois que o trabalho de cópia for concluído. | Sim, se estiver usando DistCp |
| distcpOptions | Opções adicionais fornecidas ao comando DistCp. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HdfsReadSettings",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e arquivo

Esta seção descreve o comportamento resultante se você usar um filtro curinga com o caminho da pasta e o nome do arquivo.

| folderPath | fileName             | recursiva | Estrutura da pasta de origem e resultado do filtro (os arquivos em **negrito** são recuperados) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (vazio, usar padrão) | false     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*`  | (vazio, usar padrão) | true      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*`  | `*.csv`              | false     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*`  | `*.csv`              | true      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |

### <a name="file-list-examples"></a>Exemplos de lista de arquivos

Esta seção descreve o comportamento resultante do uso de um caminho de lista de arquivos na origem da atividade de cópia. Ele pressupõe que você tem a seguinte estrutura de pasta de origem e deseja copiar os arquivos que estão em negrito:

| Exemplo de estrutura de origem                                      | Conteúdo em FileListToCopy.txt                             | Configuração de Azure Data Factory                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadados<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **No conjunto de os:**<br>- Caminho da pasta: `root/FolderA`<br><br>**Na fonte da atividade de cópia:**<br>- Caminho da lista de arquivos: `root/Metadata/FileListToCopy.txt` <br><br>O caminho da lista de arquivos aponta para um arquivo de texto no mesmo armazenamento de dados que inclui uma lista de arquivos que você deseja copiar (um arquivo por linha, com o caminho relativo para o caminho configurado no conjunto de dados). |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Usar DistCp para copiar dados do HDFS

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) é uma ferramenta de linha de comando nativa do Hadoop para fazer uma cópia distribuída em um cluster Hadoop. Quando você executa um comando no DistCp, ele lista primeiro todos os arquivos a serem copiados e, em seguida, cria vários trabalhos de mapa no cluster Hadoop. Cada trabalho de mapa faz uma cópia binária da origem para o coletor.

A atividade de cópia dá suporte ao uso de DistCp para copiar arquivos como se encontra no armazenamento de BLOBs do Azure (incluindo [cópia em etapas](copy-activity-performance.md)) ou em um Azure data Lake Store. Nesse caso, o DistCp pode aproveitar a potência do seu cluster em vez de ser executado no tempo de execução de integração auto-hospedado. O uso do DistCp fornece uma melhor taxa de transferência de cópia, especialmente se o cluster for muito eficiente. Com base na configuração em seu data factory, a atividade de cópia constrói automaticamente um comando DistCp, envia-o para o cluster Hadoop e monitora o status da cópia.

### <a name="prerequisites"></a>Pré-requisitos

Para usar o DistCp para copiar arquivos da HDFS para o armazenamento de BLOBs do Azure (incluindo cópia em etapas) ou o Azure data Lake Store, verifique se o cluster Hadoop atende aos seguintes requisitos:

* Os serviços MapReduce e YARN estão habilitados.  
* A versão YARN é 2,5 ou posterior.  
* O servidor HDFS é integrado ao armazenamento de dados de destino: **armazenamento de BLOBs do Azure** ou **Azure data Lake Store (ADLS Gen1)**: 

    - O FileSystem do Blob do Azure tem suporte nativo desde o Hadoop 2.7. Você só precisa especificar o caminho JAR na configuração do ambiente do Hadoop.
    - O FileSystem do Azure Data Lake Store está empacotado desde o Hadoop 3.0.0-alpha1. Se a versão do cluster do Hadoop for anterior à versão, você precisará importar manualmente os pacotes JAR relacionados ao Azure Data Lake Store (Azure-datalake-Store. jar) para o cluster [aqui](https://hadoop.apache.org/releases.html)e especificar o caminho do arquivo JAR na configuração do ambiente do Hadoop.

* Prepare uma pasta temporária no HDFS. Essa pasta Temp é usada para armazenar um script de shell DistCp, portanto, ele ocupará o espaço em nível de KB.
* Verifique se a conta de usuário fornecida no serviço vinculado do HDFS tem permissão para:
   * Envie um aplicativo no YARN.
   * Crie uma subpasta e leia/grave arquivos na pasta Temp.

### <a name="configurations"></a>Configurações

Para ver as configurações e exemplos relacionados ao DistCp, vá para a seção [HDFS como fonte](#hdfs-as-source) .

## <a name="use-kerberos-authentication-for-the-hdfs-connector"></a>Usar a autenticação Kerberos para o conector HDFS

Há duas opções para configurar o ambiente local para usar a autenticação Kerberos para o conector HDFS. Você pode escolher a que melhor se adapta à sua situação.
* Opção 1: [ingressar em um computador de tempo de execução de integração auto-hospedado no realm do Kerberos](#kerberos-join-realm)
* Opção 2: [habilitar a confiança mútua entre o domínio do Windows e o realm do Kerberos](#kerberos-mutual-trust)

Para qualquer opção, certifique-se de ativar o webhdfs para o cluster Hadoop:

1. Crie a entidade de segurança HTTP e keytab para webhdfs.

    > [!IMPORTANT]
    > A entidade de segurança HTTP Kerberos deve começar com "**http/**" de acordo com a especificação de http SPNEGO Kerberos. Saiba mais [aqui](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#HDFS_Configuration_Options).

    ```bash
    Kadmin> addprinc -randkey HTTP/<namenode hostname>@<REALM.COM>
    Kadmin> ktadd -k /etc/security/keytab/spnego.service.keytab HTTP/<namenode hostname>@<REALM.COM>
    ```

2. Opções de configuração do HDFS: Adicione as três propriedades a seguir no `hdfs-site.xml` .
    ```xml
    <property>
        <name>dfs.webhdfs.enabled</name>
        <value>true</value>
    </property>
    <property>
        <name>dfs.web.authentication.kerberos.principal</name>
        <value>HTTP/_HOST@<REALM.COM></value>
    </property>
    <property>
        <name>dfs.web.authentication.kerberos.keytab</name>
        <value>/etc/security/keytab/spnego.service.keytab</value>
    </property>
    ```

### <a name="option-1-join-a-self-hosted-integration-runtime-machine-in-the-kerberos-realm"></a><a name="kerberos-join-realm"></a>Opção 1: ingressar em um computador de tempo de execução de integração auto-hospedado no realm do Kerberos

#### <a name="requirements"></a>Requisitos

* O computador do Integration Runtime de hospedagem interna precisa ingressar no realm do Kerberos e não pode ingressar em nenhum domínio do Windows.

#### <a name="how-to-configure"></a>Como configurar

**No servidor do KDC:**

Crie uma entidade de segurança para Azure Data Factory usar e especifique a senha.

> [!IMPORTANT]
> O nome de usuário não deve conter o nome do host.

```bash
Kadmin> addprinc <username>@<REALM.COM>
```

**No computador do Integration Runtime de hospedagem interna:**

1.  Execute o utilitário Ksetup para configurar o realm e servidor KDC (Centro de Distribuição de Chaves) do Kerberos.

    O computador deve ser configurado como um membro de um grupo de trabalho, porque um realm Kerberos é diferente de um domínio do Windows. Você pode obter essa configuração definindo o realm do Kerberos e adicionando um servidor KDC executando os comandos a seguir. Substitua *realm.com* pelo seu próprio nome de realm.

    ```cmd
    C:> Ksetup /setdomain REALM.COM
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    ```

    Depois de executar esses comandos, reinicie o computador.

2.  Verifique a configuração com o `Ksetup` comando. A saída deverá ser como a seguinte:

    ```cmd
    C:> Ksetup
    default realm = REALM.COM (external)
    REALM.com:
        kdc = <your_kdc_server_address>
    ```

**Em seu data factory:**

* Configure o conector HDFS usando a autenticação do Windows junto com seu nome de entidade de segurança e senha Kerberos para se conectar à fonte de dados HDFS. Para obter detalhes de configuração, verifique a seção [Propriedades do serviço vinculado do HDFS](#linked-service-properties) .

### <a name="option-2-enable-mutual-trust-between-the-windows-domain-and-the-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>Opção 2: habilitar a confiança mútua entre o domínio do Windows e o realm do Kerberos

#### <a name="requirements"></a>Requisitos

*   O computador de tempo de execução de integração auto-hospedado deve ingressar em um domínio do Windows.
*   Você precisa de permissão para atualizar as configurações do controlador de domínio.

#### <a name="how-to-configure"></a>Como configurar

> [!NOTE]
> Substitua REALM.COM e AD.COM no tutorial a seguir com seu próprio nome de realm e controlador de domínio.

**No servidor do KDC:**

1. Edite a configuração do KDC no arquivo *krb5. conf* para permitir que o KDC confie no domínio do Windows fazendo referência ao modelo de configuração a seguir. Por padrão, a configuração está localizada em */etc/krb5.conf*.

   ```config
   [logging]
    default = FILE:/var/log/krb5libs.log
    kdc = FILE:/var/log/krb5kdc.log
    admin_server = FILE:/var/log/kadmind.log
            
   [libdefaults]
    default_realm = REALM.COM
    dns_lookup_realm = false
    dns_lookup_kdc = false
    ticket_lifetime = 24h
    renew_lifetime = 7d
    forwardable = true
            
   [realms]
    REALM.COM = {
     kdc = node.REALM.COM
     admin_server = node.REALM.COM
    }
   AD.COM = {
    kdc = windc.ad.com
    admin_server = windc.ad.com
   }
            
   [domain_realm]
    .REALM.COM = REALM.COM
    REALM.COM = REALM.COM
    .ad.com = AD.COM
    ad.com = AD.COM
            
   [capaths]
    AD.COM = {
     REALM.COM = .
    }
    ```

   Depois de configurar o arquivo, reinicie o serviço KDC.

2. Prepare uma entidade de segurança chamada *krbtgt/realm. com \@ AD.com* no servidor KDC com o seguinte comando:

    ```cmd
    Kadmin> addprinc krbtgt/REALM.COM@AD.COM
    ```

3. No arquivo de configuração de serviço do HDFS *hadoop.security.auth_to_local*, adicione `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**No controlador de domínio:**

1.  Execute os seguintes `Ksetup` comandos para adicionar uma entrada de realm:

    ```cmd
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
    ```

2.  Estabeleça a confiança do domínio do Windows para o realm do Kerberos. [password] é a senha para a entidade de segurança *krbtgt/REALM.COM\@AD.COM*.

    ```cmd
    C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /password:[password]
    ```

3.  Selecione o algoritmo de criptografia que é usado no Kerberos.

    a. Selecione **Gerenciador do servidor**  >  **política de grupo**  >  **domínio**  >  de gerenciamento **política de grupo objetos**  >  **padrão ou política de domínio ativo** e, em seguida, selecione **Editar**.

    b. No painel **Editor de gerenciamento de política de grupo** , selecione **configuração do computador**  >  **políticas**  >  **configurações do Windows** configurações  >  de **segurança**  >  **políticas locais**  >  **Opções de segurança** e configure **segurança de rede: configurar tipos de criptografia permitidos para Kerberos**.

    c. Selecione o algoritmo de criptografia que você deseja usar ao se conectar ao servidor KDC. Você pode selecionar todas as opções.

    ![Captura de tela do painel "segurança de rede: configurar tipos de criptografia permitidos para Kerberos"](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    d. Use o `Ksetup` comando para especificar o algoritmo de criptografia a ser usado no realm especificado.

    ```cmd
    C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96
    ```

4.  Crie o mapeamento entre a conta de domínio e a entidade de segurança Kerberos, para que você possa usar a entidade de segurança Kerberos no domínio do Windows.

    a. Selecione **Ferramentas administrativas**  >  **Active Directory usuários e computadores**.

    b. Configure os recursos avançados selecionando **Exibição** > **Recursos Avançados**.

    c. No painel **recursos avançados** , clique com o botão direito do mouse na conta para a qual você deseja criar mapeamentos e, no painel **mapeamentos de nome** , selecione a guia **nomes Kerberos** .

    d. Adicione uma entidade de segurança do realm.

       ![O painel "mapeamento de identidade de segurança"](media/connector-hdfs/map-security-identity.png)

**No computador do Integration Runtime de hospedagem interna:**

* Execute os comandos a seguir `Ksetup` para adicionar uma entrada de realm.

   ```cmd
   C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
   C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

**Em seu data factory:**

* Configure o conector HDFS usando a autenticação do Windows junto com sua conta de domínio ou entidade de segurança Kerberos para se conectar à fonte de dados HDFS. Para obter detalhes de configuração, consulte a seção [Propriedades do serviço vinculado do HDFS](#linked-service-properties) .

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para obter informações sobre propriedades de atividade de pesquisa, consulte [atividade de pesquisa em Azure data Factory](control-flow-lookup-activity.md).

## <a name="delete-activity-properties"></a>Excluir propriedades da atividade

Para obter informações sobre as propriedades de atividade de exclusão, consulte [excluir atividade no Azure data Factory](delete-activity.md).

## <a name="legacy-models"></a>Modelos herdados

>[!NOTE]
>Os modelos a seguir ainda têm suporte como são para compatibilidade com versões anteriores. É recomendável que você use o novo modelo abordado anteriormente, porque a interface do usuário de criação do Azure Data Factory mudou para gerar o novo modelo.

### <a name="legacy-dataset-model"></a>Modelo de conjunto de dados herdado

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade *Type* do conjunto de conjuntos deve ser definida como *FileShare* |Sim |
| folderPath | O caminho para a pasta. Há suporte para um filtro curinga. Os curingas permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere); use `^` para escapar se o nome real do arquivo tiver um caractere curinga ou se ele estiver dentro de. <br/><br/>Exemplos: rootfolder/subfolder/; veja mais exemplos em [Exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). |Sim |
| fileName |  O nome ou filtro de curinga para os arquivos sob o "folderPath" especificado. Se você não especificar um valor para essa propriedade, o conjunto de dados apontará para todos os arquivos na pasta. <br/><br/>Para filtro, curingas permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere).<br/>– Exemplo 1: `"fileName": "*.csv"`<br/>– Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` para escapar se o nome real da pasta tiver um curinga ou esse caractere de escape dentro de. |Não |
| modifiedDatetimeStart | Os arquivos são filtrados com base no atributo *modificado pela última vez*. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de `modifiedDatetimeStart` a `modifiedDatetimeEnd` . A hora é aplicada ao fuso horário UTC no formato *2018-12-01T05:00:00Z*. <br/><br/> Lembre-se de que o desempenho geral da movimentação de dados será afetado ao habilitar essa configuração quando você quiser aplicar um filtro de arquivo a um grande número de arquivos. <br/><br/> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de valores.  Quando `modifiedDatetimeStart` tem um valor de DateTime, mas `modifiedDatetimeEnd` é nulo, significa que os arquivos cujo último atributo modificado é maior ou igual ao valor de DateTime são selecionados.  Quando `modifiedDatetimeEnd` tem um valor de DateTime, mas `modifiedDatetimeStart` é nulo, significa que os arquivos cujo último atributo modificado é menor que o valor de data e hora são selecionados.| Não |
| modifiedDatetimeEnd | Os arquivos são filtrados com base no atributo *modificado pela última vez*. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de `modifiedDatetimeStart` a `modifiedDatetimeEnd` . A hora é aplicada ao fuso horário UTC no formato *2018-12-01T05:00:00Z*. <br/><br/> Lembre-se de que o desempenho geral da movimentação de dados será afetado ao habilitar essa configuração quando você quiser aplicar um filtro de arquivo a um grande número de arquivos. <br/><br/> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de valores.  Quando `modifiedDatetimeStart` tem um valor de DateTime, mas `modifiedDatetimeEnd` é nulo, significa que os arquivos cujo último atributo modificado é maior ou igual ao valor de DateTime são selecionados.  Quando `modifiedDatetimeEnd` tem um valor de DateTime, mas `modifiedDatetimeStart` é nulo, significa que os arquivos cujo último atributo modificado é menor que o valor de data e hora são selecionados.| Não |
| format | Se você quiser copiar arquivos no estado em que se encontram entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída.<br/><br/>Se você quer analisar arquivos com um formato específico, os seguintes tipos de formato de arquivo têm suporte: *TextFormat*, *JsonFormat*, *AvroFormat*, *OrcFormat*, *ParquetFormat*. Defina a propriedade *type* sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Formato JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formato Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Formato ORC](supported-file-formats-and-compression-codecs-legacy.md#orc-format) e [Formato Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Não (somente para o cenário de cópia binária) |
| compactação | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Os tipos com suporte são: *gzip*, *deflate*, *bzip2* e *ZipDeflate*.<br/>Níveis compatíveis são: *Ideal* e *Mais Rápido*. |Não |

>[!TIP]
>Para copiar todos os arquivos em uma pasta, especifique **folderPath** somente.<br>Para copiar um único arquivo com um nome especificado, especifique **FolderPath** com a parte da **pasta e o nome do** arquivo com o nome.<br>Para copiar um subconjunto de arquivos em uma pasta, especifique **folderPath** com parte da pasta e **fileName** com filtro curinga.

**Exemplo:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Modelo de origem da atividade de cópia herdada

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade *Type* da fonte da atividade de cópia deve ser definida como *HdfsSource*. |Sim |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Quando recursivo é definido como *true* e o coletor é um armazenamento baseado em arquivo, uma pasta ou subpasta vazia não será copiada ou criada no coletor.<br/>Os valores permitidos são *true* (padrão) e *false*. | Não |
| distcpSettings | O grupo de propriedades quando você estiver usando o HDFS DistCp. | Não |
| resourceManagerEndpoint | O ponto de extremidade do Gerenciador de recursos YARN | Sim, se estiver usando DistCp |
| tempScriptPath | Um caminho de pasta que é usado para armazenar o script de comando Temp DistCp. O arquivo de script é gerado pelo Data Factory e será removido depois que o trabalho de cópia for concluído. | Sim, se estiver usando DistCp |
| distcpOptions | Opções adicionais são fornecidas para o comando DistCp. | Não |
| maxConcurrentConnections | O número de conexões que podem se conectar ao armazenamento de armazenamento simultaneamente. Especifique um valor somente quando desejar limitar a conexão simultânea com o armazenamento de dados. | Não |

**Exemplo: origem do HDFS na atividade de cópia usando DistCp**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
