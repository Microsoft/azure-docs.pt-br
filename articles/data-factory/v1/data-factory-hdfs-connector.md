---
title: Mover dados a partir do HDFS local | Microsoft Docs
description: Saiba mais sobre como mover dados do HDFS local usando o Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f28c7b94a9eb8131f0638a24a0d4b3cfccf062e5
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836286"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Mover dados do HDFS local usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-hdfs-connector.md)
> * [Versão 2 (versão atual)](../connector-hdfs.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Conector do HDFS na V2 ](../connector-hdfs.md).

Esse artigo explica como usar a Atividade de Cópia no Azure Data Factory para mover dados de um HDFS local. Ele se baseia no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

Você pode copiar dados de um HDFS para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de repositórios de dados com suporte como coletores da atividade de cópia, confira a tabela [Repositórios de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Atualmente, a data factory dá suporte apenas para a movimentação de dados de um HDFS local para outros armazenamentos de dados, mas não para a movimentação de dados de outros armazenamentos de dados para um HDFS local.

> [!NOTE]
> A Atividade de Cópia não exclui o arquivo de origem depois que ele é copiado com êxito para o destino. Se precisar excluir o arquivo de origem após uma cópia bem-sucedida, crie uma atividade personalizada para excluir o arquivo e use a atividade no pipeline. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Habilitando a conectividade
O serviço Data Factory dá suporte à conexão com HDFS local usando o Gateway de Gerenciamento de Dados. Consulte o artigo [movendo dados entre pontos locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre o Gateway de gerenciamento de dados e obter instruções passo a passo de como configurar o gateway. Use o gateway para se conectar ao HDFS, mesmo se ele estiver hospedado em uma VM IaaS do Azure.

> [!NOTE]
> Verifique se o Gateway de gerenciamento de dados pode acessar **TODOS** os [servidor de nó de nome]: [porta do nó de nome] e [servidores de nó de dados]:[porta do nó de dados] do cluster Hadoop. A [porta do nó de nome] padrão é 50070, e a [porta do nó de dados] padrão é 50075.

Embora você possa instalar o gateway no mesmo computador local ou a VM do Azure como o HDFS, recomendamos que você instale o gateway em um computador separado ou em uma VM IaaS do Azure separada. Ter o gateway em um computador separado reduz a contenção de recursos e aprimora o desempenho. Quando você instalar o gateway em um computador separado, o computador deverá ser capaz de acessar o computador com o HDFS.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que mova dados de uma origem HDFS usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de Cópia**. Consulte [Tutorial: criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

Você também pode usar as ferramentas abaixo para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **Modelo do Azure Resource Manager**, **API .NET** e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory.
2. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia.
3. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída.

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON.  Para obter um exemplo com definições de JSON para entidades do Data Factory que são usadas para copiar dados do armazenamento de dados do HDFS, consulte a seção [Exemplo de JSON: copiar dados do HDFS local para o Blob do Azure ](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) neste artigo.

As seções que se seguem fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory específicas ao HDFS:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
Um serviço vinculado vincula um armazenamento de dados a um data factory. Crie um serviço vinculado do tipo **Hdfs** para vincular um HDFS local ao seu data factory. A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do HDFS.

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| type |A propriedade type deve ser definida como: **Hdfs** |Sim |
| url |URL para o HDFS |Sim |
| authenticationType |Anônimo ou Windows. <br><br> Para usar **autenticação Kerberos** com o conector HDFS, veja [esta seção](#use-kerberos-authentication-for-hdfs-connector) para configurar seu ambiente local adequadamente. |Sim |
| userName |Nome de usuário para a autenticação do Windows. Para a autenticação Kerberos, especifique `<username>@<domain>.com`. |Sim (para a Autenticação do Windows) |
| password |Senha para a autenticação do Windows. |Sim (para a Autenticação do Windows) |
| gatewayName |O nome do gateway que o serviço Data Factory deve usar para se conectar ao HDFS. |Sim |
| encryptedCredential |[Novo AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) saída da credencial de acesso. |Não |

### <a name="using-anonymous-authentication"></a>Usando a autenticação anônima

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-windows-authentication"></a>Usando a autenticação do Windows

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```
## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, confira o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção typeProperties para o conjunto de dados do tipo **FileShare** (que inclui o conjunto de dados do HDFS) tem as propriedades a seguir

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| folderPath |Caminho para a pasta. Exemplo: `myfolder`<br/><br/>Use o caractere de escape ' \ ' para caracteres especiais na cadeia de caracteres. Por exemplo: para pasta\subpasta, especifique a pasta\\\\subpasta e para d:\pastadeexemplo, especifique d:\\\\pastadeexemplo.<br/><br/>Você pode combinar essa propriedade com **partitionBy** para ter caminhos de pastas com base na fatia de data/hora de início/término. |Sim |
| fileName |Especifique o nome do arquivo no **folderPath** se quiser que a tabela se refira a um arquivo específico na pasta. Se você não especificar algum valor para essa propriedade, a tabela apontará para todos os arquivos na pasta.<br/><br/>Quando o fileName não for especificado para um conjunto de dados de saída, o nome do arquivo gerado será no seguinte formato: <br/><br/>`Data.<Guid>.txt` (por exemplo:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| partitionedBy |partitionedBy pode usado para especificar um filename, folderPath dinâmico para dados de série temporal. Exemplo: folderPath parametrizado para cada hora dos dados. |Não |
| format | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída. |Não |
| compression | Especifique o tipo e o nível de compactação para os dados. Tipos compatíveis são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. Níveis compatíveis são: **Ideal** e **Mais Rápido**. Para saber mais, confira [File and compression formats in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support) (Formatos de arquivo e de compactação no Azure Data Factory). |Não |

> [!NOTE]
> filename e fileFilter não podem ser usados simultaneamente.

### <a name="using-partionedby-property"></a>Usando a propriedade partionedBy
Conforme mencionado na seção anterior, você pode especificar um nome de arquivo e um folderPath dinâmico para dados de série temporal com a propriedade **partitionedBy**, [funções do Data Factory e as variáveis do sistema](data-factory-functions-variables.md).

Confira os artigos [Criando conjuntos de dados](data-factory-create-datasets.md), [Agendamento e execução](data-factory-scheduling-and-execution.md) e [Criando pipelines](data-factory-create-pipelines.md) para saber mais sobre conjuntos de dados de série temporal, agendamentos e fatias.

#### <a name="sample-1"></a>Exemplo 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Nesse exemplo, {Slice} é substituído pelo valor da variável de sistema SliceStart do Data Factory no formato (AAAAMMDDHH) especificado. O SliceStart refere-se à hora de início da fatia. O folderPath é diferente para cada fatia. Por exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Exemplo 2:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
Neste exemplo, ano, mês, dia e hora do SliceStart são extraídos em variáveis separadas que são usadas pelas propriedades folderPath e fileName.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e políticas, estão disponíveis para todos os tipos de atividade.

Por outro lado, as propriedades disponíveis na seção typeProperties da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor.

Para a Atividade de Cópia quando a fonte for do tipo **FileSystemSource** , as propriedades a seguir estarão disponíveis na seção typeProperties:

**FileSystemSource** suporta as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursive |Indica se os dados são lidos recursivamente a partir das subpastas ou somente da pasta especificada. |True, False (padrão) |Não |

## <a name="supported-file-and-compression-formats"></a>Formatos de arquivo e de compactação com suporte
Consulte o artigo [Formatos de arquivo e de compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md) para obter detalhes.

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Exemplo JSON: copiar dados de um HDFS local para o Blob do Azure
Este exemplo mostra como copiar dados de um HDFS local para o Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** para qualquer uma das fontes declaradas [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure Data Factory.  

O exemplo fornece definições de JSON para as entidades de Data Factory a seguir. Você pode usar essas definições para criar um pipeline para copiar dados do HDFS para o armazenamento de BLOBs do Azure por meio [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [do Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

1. Um serviço vinculado do tipo [OnPremisesHdfs](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [FileShare](#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com a Atividade de Cópia que usa [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de um HDFS local para o blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

Como uma primeira etapa, configure o gateway de gerenciamento de dados. As instruções estão no artigo [Mover dados entre fontes locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) .

**Serviço vinculado ao HDFS:** este exemplo usa a autenticação do Windows. Confira a seção [Serviço vinculado ao HDFS](#linked-service-properties) para diferentes tipos de autenticação que você pode usar.

```JSON
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

**Serviço vinculado de armazenamento do Azure:**

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Conjunto de dados de entrada do HDFS:** esse conjunto de dados refere-se à pasta DataTransfer/UnitTest/. O pipeline copia todos os arquivos dessa pasta para o destino.

Configurar “external”: “true” informa ao serviço Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade no Data Factory.

```JSON
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Conjunto de dados de saída de Blob do Azure:**

Os dados são gravados em um novo blob a cada hora (frequência: horas, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```JSON
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Uma atividade de cópia em um pipeline com origem de Sistema de Arquivos e coletor Blob:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **FileSystemSource** e o tipo **sink** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **query** seleciona os dados na última hora para copiar.

```JSON
{
    "name": "pipeline",
    "properties":
    {
        "activities":
        [
            {
                "name": "HdfsToBlobCopy",
                "inputs": [ {"name": "InputDataset"} ],
                "outputs": [ {"name": "OutputDataset"} ],
                "type": "Copy",
                "typeProperties":
                {
                    "source":
                    {
                        "type": "FileSystemSource"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "policy":
                {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Usar a autenticação Kerberos para o conector HDFS
Há duas opções para configurar o ambiente local para usar a autenticação Kerberos no conector HDFS. Você pode escolher a que melhor se adapta ao seu caso.
* Opção 1: [fazer com que o computador do gateway ingresse no realm Kerberos](#kerberos-join-realm)
* Opção 2: [Habilitar a confiança mútua entre o domínio do Windows e o realm Kerberos](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Opção 1: fazer com que o computador do gateway ingresse no realm Kerberos

#### <a name="requirement"></a>Requisito:

* O computador do gateway precisa unir-se ao realm Kerberos e não pode ingressar em nenhum domínio do Windows.

#### <a name="how-to-configure"></a>Como configurar:

**No computador do gateway:**

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

#### <a name="requirement"></a>Requisito:
*   O computador do gateway deve ingressar em um domínio do Windows.
*   Você precisa de permissão para atualizar as configurações do controlador de domínio.

#### <a name="how-to-configure"></a>Como configurar:

> [!NOTE]
> Substitua REALM.COM e AD.COM no tutorial a seguir pelo seu próprio realm e controlador de domínio conforme a necessidade.

**No servidor KDC:**

1. Edite a configuração do KDC no arquivo **krb5.conf** para permitir que o Domínio do Windows de confiança do KDC se refira ao modelo de configuração a seguir. Por padrão, a configuração está localizada em **/etc/krb5.conf**.

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

2. Prepare uma entidade de segurança chamada **krbtgt/REALM.COM\@AD.COM** no servidor KDC com o seguinte comando:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. No arquivo de configuração de serviço HDFS **hadoop.security.auth_to_local**, adicione `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**No controlador de domínio:**

1.  Execute os comandos **Ksetup** a seguir para adicionar uma entrada de realm:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Estabeleça uma relação de confiança do Domínio do Windows ao Realm Kerberos. [password] é a senha para a entidade de segurança **krbtgt/REALM.COM\@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Selecione o algoritmo de criptografia usado no Kerberos.

    1. Vá para Gerenciador de Servidores > Gerenciamento de Política de Grupo > Domínio > Objetos de Política de Grupo > Política de Domínio Padrão ou Ativa e Editar.

    2. Na janela pop-up **Editor de Gerenciamento de Política de Grupo**, vá para Configuração do Computador > Políticas > Configurações do Windows > Configurações de Segurança > Políticas Locais > Opções de Segurança e defina **Segurança de rede: Configurar tipos de criptografia permitidos para Kerberos**.

    3. Selecione o algoritmo de criptografia que você deseja usar ao se conectar ao KDC. Normalmente, você pode simplesmente selecionar todas as opções.

        ![Configuração dos tipos de criptografia para Kerberos](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. Use o comando **Ksetup** para especificar o algoritmo de criptografia a ser usado no REALM específico.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Crie o mapeamento entre a conta de domínio e a entidade de segurança Kerberos para usar a entidade de segurança Kerberos no Domínio do Windows.

    1. Inicie as Ferramentas administrativas > **Usuários e Computadores do Active Directory**.

    2. Configure recursos avançados clicando em **Exibir** > **Recursos Avançados**.

    3. Localize a conta para a qual deseja criar mapeamentos e clique com o botão direito do mouse para exibir **Mapeamentos de Nome** > clique na guia **Nomes Kerberos**.

    4. Adicione uma entidade de segurança do realm.

        ![Mapear identidade de segurança](media/data-factory-hdfs-connector/map-security-identity.png)

**No computador do gateway:**

* Execute os comandos **Ksetup** a seguir para adicionar uma entrada de realm.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**No Azure Data Factory:**

* Configure o conector HDFS usando a **autenticação do Windows** com a Conta de Domínio ou a Entidade de Segurança Kerberos para se conectar à fonte de dados HDFS. Verifique a seção de [propriedades do Serviço Vinculado HDFS](#linked-service-properties) nos detalhes da configuração.

> [!NOTE]
> Para mapear colunas de conjunto de dados de origem para colunas do conjunto de dados de coletor, confira [Mapeando colunas de conjunto de dados no Azure Data Factory](data-factory-map-columns.md).


## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.
