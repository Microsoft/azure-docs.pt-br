---
title: Copiar dados do HDFS usando o Azure Data Factory | Microsoft Docs
description: "Saiba como copiar dados de uma fonte HDFS local ou de nuvem para armazenamentos de dados de coletor com suporte, usando uma atividade de cópia em um pipeline do Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: 8db368017ef25603598ec92e8bbbb1fc69230145
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-and-to-hdfs-using-azure-data-factory"></a>Copiar dados de e para o HDFS usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-hdfs-connector.md)
> * [Versão 2 – Versão prévia](connector-hdfs.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para o HDFS. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está em GA (disponibilidade geral), consulte [Conector do HDFS na V1](v1/data-factory-hdfs-connector.md).


## <a name="supported-scenarios"></a>Cenários com suporte

Você pode copiar dados de um HDFS para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte, que funcionam como fontes/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector HDFS dá suporte a:

- Cópia de arquivos usando autenticação **Windows** (Kerberos) ou **Anônima**.
- Cópia de arquivos usando o protocolo **webhdfs** ou suporte à **DistCp interna**.
- Cópia de arquivos no estado em que se encontram ou análise/geração de arquivos com os [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Pré-requisitos

Para copiar dados de/para um HDFS que não esteja acessível publicamente, você precisará configurar um Integration Runtime auto-hospedado. Consulte o artigo [Integration Runtime auto-hospedado](concepts-integration-runtime.md) para saber mais detalhes.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com atividade de cópia usando o SDK do .NET, o SDK do Python, o Azure PowerShell, a API REST ou o modelo do Azure Resource Manager. Confira o [Tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao HDFS.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do HDFS:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **Hdfs**. | Sim |
| url |URL para o HDFS |Sim |
| authenticationType | Os valores permitidos são: **Anônima** ou **Windows**. <br><br> Para usar **autenticação Kerberos** com o conector HDFS, veja [esta seção](#use-kerberos-authentication-for-hdfs-connector) para configurar seu ambiente local adequadamente. |Sim |
| userName |Nome de usuário para a autenticação do Windows. |Sim (para a Autenticação do Windows) |
| Senha |Senha para a autenticação do Windows. Marque esse campo como SecureString. |Sim (para a Autenticação do Windows) |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime auto-hospedado ou o Integration Runtime do Azure (se seu armazenamento de dados estiver publicamente acessível). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

**Exemplo: usando a autenticação anônima**

```json
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
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
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre conjuntos de dados. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do HDFS.

Para copiar dados do HDFS, defina a propriedade type do conjunto de dados como **FileShare**. Há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **FileShare** |Sim |
| folderPath | Caminho para a pasta. Por exemplo: pasta/subpasta/ |Sim |
| fileName | Especifique o nome do arquivo no **folderPath** se você quiser copiar de um arquivo específico. Se você não especificar algum valor para essa propriedade, o conjunto de dados apontará para todos os arquivos na pasta como fontes. |Não |
| formato | Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída.<br/><br/>Se você quiser analisar arquivos com um formato específico, há suporte para os seguintes tipos de formatos de arquivo: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [Formato Json](supported-file-formats-and-compression-codecs.md#json-format), [Formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Formato Orc](supported-file-formats-and-compression-codecs.md#orc-format), e [Formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Não (somente para o cenário de cópia binária) |
| compactação | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Os tipos com suporte são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>Os níveis com suporte são **Ideal** e **O mais rápido**. |Não |

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
            "fileName": "myfile.csv.gz",
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

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte e pelo coletor HDFS.

### <a name="hdfs-as-source"></a>HDFS como fonte

Para copiar dados do HDFS, defina o tipo de fonte na atividade de cópia como **HdfsSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **HdfsSource** |Sim |
| recursiva | Indica se os dados são lidos recursivamente a partir das subpastas ou somente da pasta especificada.<br/>Os valores permitidos são: **true** (padrão), **false** | Não |
| distcpSettings | Grupo de propriedades ao usar DistCp de HDFS. | Não |
| resourceManagerEndpoint | O ponto de extremidade de YARN ResourceManager | Sim se usando DistCp |
| tempScriptPath | Um caminho de pasta usado para armazenar o script de comando temporário DistCp. O arquivo de script é gerado pelo ADF e será removido após a conclusão do trabalho de cópia. | Sim se usando DistCp |
| distcpOptions | Opções adicionais fornecidas ao comando DistCp. | Não |

**Exemplo: fonte HDFS na atividade de cópia usando UNLOAD**

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

Saiba mais sobre como usar o DistCp para copiar dados do HDFS com eficiência na próxima seção.

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Usar DistCp para copiar dados do HDFS

O [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) é uma ferramenta de linha de comando nativa do Hadoop para fazer cópia distribuída em um cluster Hadoop. Durante a execução de um comando Distcp, primeiro ele listará todos os arquivos a serem copiados, criará vários trabalhos de mapa no cluster Hadoop e cada trabalho de mapa fará a cópia binária da fonte para o coletor.

A atividade de cópia dá suporte ao uso do DistCp para copiar arquivos no estado em que se encontram para o Blob do Azure (incluindo [cópia em etapas](copy-activity-performance.md)) ou para o Azure Data Lake Store, caso em que ele poderá aproveitar totalmente o potencial do seu cluster, em vez de ser executado no Integration Runtime auto-hospedado. Ele proporcionará melhor taxa de transferência de cópia, especialmente se o cluster for muito avançado. Com base em sua configuração no Azure Data Factory, a atividade de cópia criará automaticamente um comando distcp, enviará ao cluster Hadoop e monitorará o status da cópia.

### <a name="prerequsites"></a>Pré-requisitos

Para usar o DistCp para copiar arquivos no estado em que se encontram do HDFS para o Blob do Azure (incluindo cópia em etapas) ou para o Azure Data Lake Store, verifique se seu cluster Hadoop atende aos requisitos abaixo:

1. Os serviços MapReduce e YARN estão habilitados.
2. A versão do YARN é 2.5 ou superior.
3. O servidor do HDFS está integrado com o armazenamento de dados de destino – Blob do Azure ou Azure Data Lake Store:

    - O FileSystem do Blob do Azure tem suporte nativo desde o Hadoop 2.7. Só é necessário especificar o caminho do jar na configuração do Hadoop env.
    - O FileSystem do Azure Data Lake Store está empacotado desde o Hadoop 3.0.0-alpha1. Se o cluster Hadoop for inferior a essa versão, você precisará importar manualmente os pacotes jar relacionados com ADLS (azure-datalake-store.jar) [deste](https://hadoop.apache.org/releases.html) local para o cluster e especificar o caminho do jar na configuração do Hadoop env.

4. Prepare uma pasta temporária no HDFS. Essa pasta temporária é usada para armazenar script de shell do DistCp, portanto ela ocupa espaço em nível de KB.
5. Verifique se a conta de usuário fornecida no serviço vinculado do HDFS tem permissão para: a) enviar aplicativo em YARN; b) criar subpasta e ler/gravar arquivos na pasta temporária acima.

### <a name="configurations"></a>Configurações

Abaixo está um exemplo da configuração de atividade de cópia para copiar dados do HDFS para o Blob do Azure usando o DistCp:

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromHDFSToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "HDFSDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "HdfsSource",
                "distcpSettings": {
                    "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                    "tempScriptPath": "/usr/hadoop/tempscript",
                    "distcpOptions": "-strategy dynamic -map 100"
                }
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Usar a autenticação Kerberos para o conector HDFS

Há duas opções para configurar o ambiente local para usar a autenticação Kerberos no conector HDFS. Você pode escolher a que melhor se adapta ao seu caso.
* Opção 1: [ingressar no computador do Integration Runtime auto-hospedado no realm Kerberos](#kerberos-join-realm)
* Opção 2: [habilitar a confiança mútua entre o domínio do Windows e o realm Kerberos](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Opção 1: ingressar no computador do Integration Runtime auto-hospedado no realm Kerberos

#### <a name="requirements"></a>Requisitos

* O computador do Integration Runtime auto-hospedado precisa ingressar no realm Kerberos e não pode ingressar em nenhum domínio do Windows.

#### <a name="how-to-configure"></a>Como configurar

**No computador do Integration Runtime auto-hospedado:**

1.  Execute o utilitário **Ksetup** para configurar o servidor e o realm KDC Kerberos.

    O computador deve ser configurado como um membro de um grupo de trabalho, uma vez que um realm Kerberos é diferente de um domínio do Windows. Para isso, defina o realm Kerberos e adicione um servidor KDC como se segue. Substitua *REALM.COM* pelo seu respectivo realm, conforme a necessidade.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Reinicie** o computador depois de executar esses 2 comandos.

2.  Verifique a configuração com o comando **Ksetup**. A saída deverá ser como a seguinte:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**No Azure Data Factory:**

* Configure o conector HDFS usando a **autenticação do Windows** com o nome da entidade de segurança e a senha Kerberos para se conectar à fonte de dados HDFS. Verifique a seção de [propriedades do Serviço Vinculado HDFS](#linked-service-properties) nos detalhes da configuração.

### <a name="kerberos-mutual-trust"></a>Opção 2: habilitar a confiança mútua entre o domínio do Windows e o realm Kerberos

#### <a name="requirements"></a>Requisitos

*   O computador do Integration Runtime auto-hospedado deve ingressar em um domínio do Windows.
*   Você precisa de permissão para atualizar as configurações do controlador de domínio.

#### <a name="how-to-configure"></a>Como configurar

> [!NOTE]
> Substitua REALM.COM e AD.COM no tutorial a seguir pelo seu próprio realm e controlador de domínio conforme a necessidade.

**No servidor KDC:**

1.  Edite a configuração do KDC no arquivo **krb5.conf** para permitir que o Domínio do Windows de confiança do KDC se refira ao modelo de configuração a seguir. Por padrão, a configuração está localizada em **/etc/krb5.conf**.

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

  **Reinicie** o serviço KDC após a configuração.

2.  Prepare uma entidade de segurança chamada **krbtgt/REALM.COM@AD.COM** no servidor KDC com o seguinte comando:

            Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3.  No arquivo de configuração de serviço HDFS **hadoop.security.auth_to_local**, adicione `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`.

**No controlador de domínio:**

1.  Execute os comandos **Ksetup** a seguir para adicionar uma entrada de realm:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Estabeleça uma relação de confiança do Domínio do Windows ao Realm Kerberos. [password] é a senha da entidade de segurança **krbtgt/REALM.COM@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Selecione o algoritmo de criptografia usado no Kerberos.

    1. Vá para Gerenciador de Servidores > Gerenciamento de Política de Grupo > Domínio > Objetos de Política de Grupo > Política de Domínio Padrão ou Ativa e Editar.

    2. Na janela pop-up **Editor de Gerenciamento de Política de Grupo**, vá para Configuração do Computador > Políticas > Configurações do Windows > Configurações de Segurança > Políticas Locais > Opções de Segurança e configure **Segurança da rede: Configurar tipos de criptografia permitidos para Kerberos**.

    3. Selecione o algoritmo de criptografia que você deseja usar ao se conectar ao KDC. Normalmente, você pode simplesmente selecionar todas as opções.

        ![Configuração dos tipos de criptografia para Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Use o comando **Ksetup** para especificar o algoritmo de criptografia a ser usado no REALM específico.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Crie o mapeamento entre a conta de domínio e a entidade de segurança Kerberos para usar a entidade de segurança Kerberos no Domínio do Windows.

    1. Inicie as Ferramentas administrativas > **Usuários e Computadores do Active Directory**.

    2. Configure recursos avançados clicando em **Exibir** > **Recursos Avançados**.

    3. Localize a conta para a qual deseja criar mapeamentos e clique com o botão direito do mouse para exibir **Mapeamentos de Nome** > clique na guia **Nomes Kerberos**.

    4. Adicione uma entidade de segurança do realm.

        ![Mapear identidade de segurança](media/connector-hdfs/map-security-identity.png)

**No computador do Integration Runtime auto-hospedado:**

* Execute os comandos **Ksetup** a seguir para adicionar uma entrada de realm.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**No Azure Data Factory:**

* Configure o conector HDFS usando a **autenticação do Windows** com a Conta de Domínio ou a Entidade de Segurança Kerberos para se conectar à fonte de dados HDFS. Verifique a seção de [propriedades do Serviço Vinculado HDFS](#linked-service-properties) nos detalhes da configuração.


## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).