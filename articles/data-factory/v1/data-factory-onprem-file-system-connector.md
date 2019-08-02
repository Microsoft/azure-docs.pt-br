---
title: Copiar dados bidirecionalmente em um sistema de arquivos usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados bidirecionalmente em um sistema de arquivos local usando o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: ce19f1ae-358e-4ffc-8a80-d802505c9c84
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4d3816eebe85f01301c770a50a618142bcbfbb21
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839962"
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Copiar dados bidirecionalmente em um sistema de arquivos local usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-onprem-file-system-connector.md)
> * [Versão 2 (versão atual)](../connector-file-system.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Conector do Sistema de Arquivos na V2](../connector-file-system.md).


Esse artigo explica como usar a Atividade de Cópia no Azure Data Factory para copiar dados de/para um sistema de arquivos local. Ele se baseia no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Cenários com suporte
Você pode copiar dados **de um sistema de arquivos local** para os seguintes armazenamentos de dados:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Você pode copiar dados dos seguintes armazenamentos de dados **para um sistema de arquivos local**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> A Atividade de Cópia não exclui o arquivo de origem depois que ele é copiado com êxito para o destino. Se precisar excluir o arquivo de origem após uma cópia bem-sucedida, crie uma atividade personalizada para excluir o arquivo e use a atividade no pipeline.

## <a name="enabling-connectivity"></a>Habilitando a conectividade
O Data Factory dá suporte à conexão de e para um sistema de arquivos local por meio do **Gateway de Gerenciamento de Dados**. Você deve instalar o Gateway de Gerenciamento de Dados no seu ambiente local para o serviço Data Factory para se conectar a qualquer armazenamento de dados local com suporte, incluindo o sistema de arquivos. Para saber mais sobre o Gateway de Gerenciamento de Dados e para obter instruções passo a passo sobre como configurar o gateway, confira [Mover dados entre fontes locais e a nuvem com o Gateway de Gerenciamento de Dados](data-factory-move-data-between-onprem-and-cloud.md). Com exceção do Gateway de Gerenciamento de Dados, não é necessário instalar nenhum outro binário para se comunicar de e para o sistema de arquivos local. Você deve instalar e usar o Gateway de gerenciamento de dados, mesmo se o sistema de arquivos estiver em uma VM IaaS do Azure. Para obter informações detalhadas sobre o gateway, consulte [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md).

Para usar um compartilhamento de arquivos do Linux, instale o [Samba](https://www.samba.org/) no servidor Linux e instale o Gateway de Gerenciamento de Dados em um servidor Windows. Não há suporte para a instalação do Gateway de Gerenciamento de Dados em um servidor Linux.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que mova dados bidirecionalmente de/para um sistema de arquivos usando ferramentas/APIs diferentes.

A maneira mais fácil de criar um pipeline é usar o **Assistente de Cópia**. Consulte [Tutorial: criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

Você também pode usar as ferramentas abaixo para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo do Resource Manager**, **.NET API**, e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar uma **data factory**. Um data factory pode conter um ou mais pipelines.
2. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory. Por exemplo, se você estiver copiando dados de um Armazenamento de Blobs do Azure para um sistema de arquivos local, crie dois serviços vinculados para vincular seu sistema de arquivos local e a Conta de Armazenamento do Azure para seu data factory. Para propriedades do serviço vinculado que são específicas para um sistema de arquivos local, consulte a seção de [propriedades do serviço vinculado](#linked-service-properties).
3. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. No exemplo mencionado na última etapa, você cria um conjunto de dados para especificar o contêiner de blobs e a pasta que contém os dados de entrada. E você cria outro conjunto de dados para especificar o nome do arquivo e da pasta (opcional) no sistema de arquivos. Para propriedades de conjunto de dados específicas do sistema de arquivos local, consulte a seção de [propriedades do conjunto de dados](#dataset-properties).
4. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, você usa BlobSource como fonte e FileSystemSink como coletor para a atividade de cópia. De modo similar, se estiver copiando de um sistema de arquivos local para o Armazenamento de Blobs do Azure, você usará FileSystemSource e BlobSink na atividade de cópia. Para propriedades da atividade de cópia específicas do sistema de arquivos local, consulte a seção de [propriedades da atividade de cópia](#copy-activity-properties). Para obter detalhes sobre como usar um armazenamento de dados como uma origem ou um coletor, clique no link na seção anterior para o armazenamento de dados.

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON.  Para obter exemplos com definições de JSON das entidades do Data Factory usadas para copiar dados de/para um sistema de arquivos, confira a seção [Exemplos de JSON](#json-examples-for-copying-data-to-and-from-file-system) deste artigo.

As seções que se seguem fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory específicas do sistema de arquivos:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
Você pode vincular um sistema de arquivos local ao Azure Data Factory com o serviço vinculado do **Servidor de Arquivos Local**. A tabela a seguir fornece descrições dos elementos JSON específicos para o serviço vinculado do Servidor de Arquivos Local.

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| type |Verifique se a propriedade de tipo foi definida como **OnPremisesFileServer**. |Sim |
| host |Especifica o caminho raiz da pasta que você deseja copiar. Use o caractere de escape ‘ \ ’ para caracteres especiais na cadeia de caracteres. Confira [Definições de conjunto de dados e serviço vinculado de exemplo](#sample-linked-service-and-dataset-definitions) para obter exemplos. |Sim |
| userid |Especifique a ID do usuário que tem acesso ao servidor. |Não (se você escolher encryptedcredential) |
| password |Especifique a senha para o usuário (userid). |Não (se você escolher encryptedcredential |
| encryptedCredential |Especifique as credenciais criptografadas que você pode obter executando o cmdlet New-AzDataFactoryEncryptValue. |Não (se você optar por especificar userid e password em texto sem formatação) |
| gatewayName |Especifica o nome do gateway que o Data Factory deve usar para se conectar ao servidor de arquivos local. |Sim |


### <a name="sample-linked-service-and-dataset-definitions"></a>Definições de conjunto de dados e serviço vinculado de exemplo
| Cenário | Host em definição de serviço vinculado | folderPath em definição de conjunto de dados |
| --- | --- | --- |
| Pasta local no computador do Gateway de Gerenciamento de Dados <br/><br/>Exemplos: D:\\\* ou D:\pasta\subpasta\\* |D:\\\\ (para o Gateway de Gerenciamento de Dados 2.0 e versões posteriores) <br/><br/> localhost (para versões anteriores do Gateway de Gerenciamento de Dados 2.0) |.\\\\ ou pasta\\\\subpasta (para o Gateway de Gerenciamento de Dados 2.0 e versões posteriores) <br/><br/>D:\\\\ ou D:\\\\pasta\\\\subpasta (para a versão de gateway abaixo de 2.0) |
| Pasta compartilhada remota: <br/><br/>Exemplos: \\\\meuservidor\\compartilhar\\\* ou \\\\meuservidor\\compartilhar\\pasta\\subpasta\\* |\\\\\\\\meuservidor\\\\compartilhar |.\\\\ ou pasta\\\\subpasta |

>[!NOTE]
>Durante a criação via interface do usuário, não é necessário inserir barras invertidas duplas (`\\`) para escape, como é feito via JSON, especifique a barra invertida simples.

### <a name="example-using-username-and-password-in-plain-text"></a>Exemplo: Usando username e password em texto sem formatação

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

### <a name="example-using-encryptedcredential"></a>Exemplo: Usando encryptedcredential

```JSON
{
  "Name": " OnPremisesFileServerLinkedService ",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "D:\\",
      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
      "gatewayName": "mygateway"
    }
  }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados.

A seção typeProperties é diferente para cada tipo de conjunto de dados. Ela fornece informações, como o local e o formato dos dados no armazenamento de dados. A seção typeProperties para o conjunto de dados do tipo **FileShare** tem as propriedades a seguir:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| folderPath |Especifica o subcaminho para a pasta. Use o caractere de escape ‘\' para caracteres especiais na cadeia de caracteres. O filtro curinga não é suportado. Confira [Definições de conjunto de dados e serviço vinculado de exemplo](#sample-linked-service-and-dataset-definitions) para obter exemplos.<br/><br/>Você pode combinar essa propriedade com **partitionBy** para ter caminhos de pastas com base na fatia de data/hora de início/término. |Sim |
| fileName |Especifique o nome do arquivo no **folderPath** se quiser que a tabela se refira a um arquivo específico na pasta. Se você não especificar algum valor para essa propriedade, a tabela apontará para todos os arquivos na pasta.<br/><br/>Quando **fileName** não for especificado para um conjunto de dados de saída e **preserveHierarchy** não for especificado em um coletor de atividade, o nome do arquivo gerado está no seguinte formato: <br/><br/>`Data.<Guid>.txt` (Exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| fileFilter |Especifique um filtro a ser usado para selecionar um subconjunto de arquivos no folderPath em vez de todos os arquivos. <br/><br/>Os valores permitidos são: `*` (vários caracteres) e `?` (um único caractere).<br/><br/>Exemplo 1: "fileFilter": "*.log"<br/>Exemplo 2: "fileFilter": 2014-1-?.txt"<br/><br/>Observe que fileFilter é aplicável a um conjunto de dados FileShare de entrada. |Não |
| partitionedBy |Você pode usar partitionedBy para especificar um folderPath/fileName dinâmico para dados de série temporal. Um exemplo é folderPath parametrizado para cada hora dos dados. |Não |
| format | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída. |Não |
| compression | Especifique o tipo e o nível de compactação para os dados. Tipos compatíveis são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. Níveis compatíveis são: **Ideal** e **Mais Rápido**. confira [Formatos de arquivo e de compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

> [!NOTE]
> Você não pode usar fileName e fileFilter simultaneamente.

### <a name="using-partitionedby-property"></a>Usando a propriedade partitionedBy
Conforme mencionado na seção anterior, você pode especificar um nome de arquivo e um folderPath dinâmico para dados de série temporal com a propriedade **partitionedBy**, [funções do Data Factory e as variáveis do sistema](data-factory-functions-variables.md).

Confira [Criando conjuntos de dados](data-factory-create-datasets.md), [Agendamento e execução](data-factory-scheduling-and-execution.md) e [Criando pipelines](data-factory-create-pipelines.md) para saber mais detalhes sobre conjuntos de dados de série temporal, agendamentos e fatias.

#### <a name="sample-1"></a>Exemplo 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Neste exemplo, {Slice} é substituído pelo valor da variável de sistema SliceStart do Data Factory no formato (AAAAMMDDHH). SliceStart se refere à hora de início da fatia. O folderPath é diferente para cada fatia. Por exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

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

Neste exemplo, ano, mês, dia e hora do SliceStart são extraídos em variáveis separadas, que são usadas pelas propriedades folderPath e fileName.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, conjuntos de dados de entrada e saída, e políticas, estão disponíveis para todos os tipos de atividade. Por outro lado, as propriedades disponíveis na seção **typeProperties** da atividade variam de acordo com cada tipo de atividade.

Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor. Se você estiver movendo dados de um sistema de arquivos local, defina o tipo de origem na atividade de cópia como **FileSystemSource**. De modo similar, se você estiver movendo dados para um sistema de arquivos local, defina o tipo de coletor na atividade de cópia como **FileSystemSink**. Esta seção fornece uma lista das propriedades às quais FileSystemSource e FileSystemSink dão suporte.

**FileSystemSource** suporta as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursive |Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. |True, False (padrão) |Não |

**FileSystemSink** dá suporte às seguintes propriedades:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| copyBehavior |Define o comportamento de cópia quando a origem é BlobSource ou FileSystem. |**PreserveHierarchy:** Preserva a hierarquia de arquivos na pasta de destino. Ou seja, o caminho relativo do arquivo de origem para a pasta de origem é o mesmo que o caminho relativo do arquivo de destino para a pasta de destino.<br/><br/>**FlattenHierarchy:** Todos os arquivos da pasta de origem são criados no primeiro nível da pasta de destino. Os arquivos de destino são criados com um nome gerado automaticamente.<br/><br/>**MergeFiles:** Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo/nome do blob for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, ele será um nome de arquivo gerado automaticamente. |Não |

### <a name="recursive-and-copybehavior-examples"></a>exemplos de recursive e copyBehavior
Esta seção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores nas propriedades recursive e copyBehavior.

| valor de recursive | valor de copyBehavior | Comportamento resultante |
| --- | --- | --- |
| true |preserveHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 é criada com a mesma estrutura de origem:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 |
| true |flattenHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo5 |
| true |mergeFiles |Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos de Arquivo1 + Arquivo2 + Arquivo3 + Arquivo4 + Arquivo5 são mesclados em um arquivo com um nome de arquivo gerado automaticamente. |
| false |preserveHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>A Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não é selecionada. |
| false |flattenHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/><br/>A Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não é selecionada. |
| false |mergeFiles |Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos de Arquivo1 + Arquivo2 são mesclados em um arquivo com um nome de arquivo gerado automaticamente.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para o Arquivo1<br/><br/>A Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não é selecionada. |

## <a name="supported-file-and-compression-formats"></a>Formatos de arquivo e de compactação com suporte
Consulte o artigo [Formatos de arquivo e de compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md) para obter detalhes.

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>Exemplos JSON para cópia de dados do sistema de arquivos
Os exemplos a seguir fornecem as definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [do Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados entre um sistema de arquivos local e o Armazenamento de Blobs do Azure. No entanto, você pode copiar dados *diretamente* de qualquer uma das fontes para qualquer um dos receptores listados em [Supported sources and sinks](data-factory-data-movement-activities.md#supported-data-stores-and-formats) (Fontes e coletores com suporte) usando a Atividade de Cópia no Azure Data Factory.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Exemplo: Copiar dados de um sistema de arquivos local para um Armazenamento de Blobs do Azure
Este exemplo mostra como copiar dados de um sistema de arquivos local para um Armazenamento de Blobs do Azure. O exemplo tem as seguintes entidades do Data Factory:

* Um serviço vinculado do tipo [OnPremisesFileServer](#linked-service-properties).
* Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [FileShare](#dataset-properties).
* Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [pipeline](data-factory-create-pipelines.md) com a Atividade de Cópia que usa [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo a seguir copia dados de uma série temporal do sistema de arquivos local para o Armazenamento de Blobs do Azure a cada hora. As propriedades JSON que são usadas nestes exemplos são descritas nas seções após os exemplos.

Como uma primeira etapa, configure o Gateway de Gerenciamento de Dados conforme as instruções em [Mover dados entre fontes locais e a nuvem com o Gateway de Gerenciamento de Dados](data-factory-move-data-between-onprem-and-cloud.md).

**Serviço vinculado do Servidor de Arquivos Local:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

É recomendável usar a propriedade **encryptedCredential** em vez das propriedades **userid** e **password**. Confira [File Server linked service](#linked-service-properties) (Serviço vinculado do Servidor de Arquivos) para obter detalhes sobre este serviço vinculado.

**Serviço vinculado de armazenamento do Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Conjunto de dados de entrada do sistema de arquivos local:**

Dados são coletados de um novo arquivo a cada hora. As propriedades folderPath e fileName são determinadas com base na hora de início da fatia.

A configuração `"external": "true"` informa ao Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade nele.

```JSON
{
  "name": "OnpremisesFileSystemInput",
  "properties": {
    "type": " FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Conjunto de dados do Armazenamento de Blobs do Azure:**

Dados são gravados em um novo blob a cada hora (frequência: horas, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e hora da hora de início.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Uma atividade de cópia em um pipeline com origem de Sistema de Arquivos e coletor de Blob:**

O pipeline contém uma atividade de cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo de **fonte** está definido como **FileSystemSource** e o tipo de **coletor** está definido como **BlobSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T19:00:00",
    "description":"Pipeline for copy activity",
    "activities":[
      {
        "name": "OnpremisesFileSystemtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "OnpremisesFileSystemInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "FileSystemSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Exemplo: Copiar dados do Banco de Dados SQL do Azure pra o sistema de arquivos local
O exemplo a seguir mostra:

* Um serviço vinculado do tipo [AzureSqlDatabase.](data-factory-azure-sql-connector.md#linked-service-properties)
* Um serviço vinculado do tipo [OnPremisesFileServer](#linked-service-properties).
* Um conjunto de dados de entrada do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
* Um conjunto de dados de saída do tipo [FileShare](#dataset-properties).
* Um pipeline com a atividade de cópia que usa [SqlSource](data-factory-azure-sql-connector.md##copy-activity-properties) e [FileSystemSink](#copy-activity-properties).

O exemplo copia os dados de série temporal de uma tabela SQL do Azure para um sistema de arquivos local a cada hora. As propriedades JSON que são usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado para o Banco de Dados SQL do Azure:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```

**Serviço vinculado do Servidor de Arquivos Local:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

É recomendável usar a propriedade **encryptedCredential** em vez de usar as propriedades **userid** e **password**. Confira [File System linked service](#linked-service-properties) (Serviço vinculado do Sistema de Arquivos) para obter detalhes sobre este serviço vinculado.

**Conjunto de dados de entrada do SQL Azure:**

O exemplo supõe que você criou uma tabela "MyTable" no SQL Azure e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal.

A configuração ``“external”: ”true”`` informa ao Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade nele.

```JSON
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Conjunto de dados de saída do sistema de arquivos local:**

Dados são copiados para um novo arquivo a cada hora. folderPath e fileName para o blob são determinados com base na hora de início da fatia.

```JSON
{
  "name": "OnpremisesFileSystemOutput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Uma atividade de cópia em um pipeline com origem SQL e coletor de Sistema de Arquivos:**

O pipeline contém uma atividade de cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo de **fonte** está definido como **SqlSource** e o tipo de **coletor** está definido como **FileSystemSink**. A consulta SQL que é especificada para a propriedade **SqlReaderQuery** seleciona os dados da última hora a serem copiados.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T20:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoOnPremisesFile",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "OnpremisesFileSystemOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "FileSystemSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 3,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

Você também pode mapear colunas do conjunto de dados de origem para colunas do conjunto de dados de coletor na definição da atividade de cópia. Para obter detalhes, confira [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md) (Mapear colunas de conjunto de dados no Azure Data Factory).

## <a name="performance-and-tuning"></a>Desempenho e ajuste
 Para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory e diversas maneiras para otimizá-la, confira [Copy Activity performance and tuning guide](data-factory-copy-activity-performance.md) (Guia de desempenho e ajuste da Atividade de Cópia).
