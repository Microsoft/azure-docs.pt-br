---
title: "Adicionar tolerância a falhas na Atividade de Cópia do Azure Data Factory ignorando linhas incompatíveis | Microsoft Docs"
description: "Saiba como adicionar tolerância a falhas na Atividade de Cópia do Azure Data Factory ignorando linhas incompatíveis durante a cópia"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7a8c866106f7e2c2538a9cf6c44cb34ddfaa2887
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>Adicionar tolerância a falhas na Atividade de Cópia ignorando linhas incompatíveis

A [Atividade de Cópia](data-factory-data-movement-activities.md) do Azure Data Factory oferece duas maneiras de manipular linhas incompatíveis ao copiar dados entre os armazenamentos de dados da origem e do coletor:

- Você pode anular e fazer com que a atividade de cópia falhe quando dados incompatíveis forem encontrados (comportamento padrão).
- Você pode continuar copiando todos os dados adicionando tolerância a falhas e ignorando linhas de dados incompatíveis. Além disso, é possível registrar em log as linhas incompatíveis no armazenamento de Blobs do Azure. Em seguida, examine o log para saber a causa da falha, corrija os dados na origem de dados e repita a atividade de cópia.

## <a name="supported-scenarios"></a>Cenários com suporte
A Atividade de Cópia dá suporte a três cenários para detectar, ignorar e registrar em log dados incompatíveis:

- **Incompatibilidade entre o tipo de dados da origem e o tipo nativo do coletor**

    Por exemplo: copiar dados de um arquivo CSV no armazenamento de Blobs em um banco de dados SQL com uma definição de esquema que contenha três colunas do tipo **INT**. As linhas do arquivo CSV que contêm dados numéricos, como `123,456,789`, são copiadas com êxito no armazenamento de coletores. No entanto, as linhas que contêm valores não numéricos, como `123,456,abc`, são detectadas como incompatíveis e ignoradas.

- **Incompatibilidade no número de colunas entre a origem e o coletor**

    Por exemplo: copiar dados de um arquivo CSV no armazenamento de Blobs em um banco de dados SQL com uma definição de esquema que contém seis colunas. As linhas do arquivo CSV que contêm seis colunas são copiadas com êxito no armazenamento do coletor. As linhas do arquivo CSV que contêm mais ou menos de seis colunas são detectadas como incompatíveis e ignoradas.

- **Violação de chave primária ao gravar em banco de dados relacional**

    Por exemplo: copiar dados de um servidor SQL em um banco de dados SQL. Uma chave primária é definida no banco de dados SQL do coletor, mas nenhuma chave primária é definida no SQL Server de origem. As linhas duplicadas que existem na origem não podem ser copiadas no coletor. A Atividade de Cópia copia apenas a primeira linha dos dados de origem no coletor. As linhas da origem subsequentes que contêm o valor de chave primária duplicado são detectadas como incompatíveis e ignoradas.

## <a name="configuration"></a>Configuração
O seguinte exemplo fornece uma definição de JSON que configura a omissão de linhas incompatíveis na Atividade de Cópia:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },         
    "enableSkipIncompatibleRow": true,           
    "redirectIncompatibleRowSettings": {
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| **enableSkipIncompatibleRow** | Habilite ou não a omissão das linhas incompatíveis durante a cópia. | True <br/>False (padrão) | Não |
| **redirectIncompatibleRowSettings** | Um grupo de propriedades que poderá ser especificado quando você desejar registrar as linhas incompatíveis. | &nbsp; | Não |
| **linkedServiceName** | O serviço vinculado do Armazenamento do Azure para armazenar o log que contém as linhas ignoradas. | O nome de um serviço vinculado [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service), que se refere à instância de armazenamento que você deseja usar para armazenar o arquivo de log. | Não |
| **path** | O caminho do arquivo de log que contém as linhas ignoradas. | Especifique o caminho de armazenamento de Blobs que você deseja usar para registrar em log os dados incompatíveis. Se você não fornecer um caminho, o serviço criará um contêiner para você. | Não |

## <a name="monitoring"></a>Monitoramento
Depois que a execução da atividade de cópia for concluída, você verá o número de linhas ignoradas na seção de monitoramento:

![Monitorar linhas incompatíveis ignoradas](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Se você configurar para registrar em log as linhas incompatíveis, será possível encontrar o arquivo de log neste caminho: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` No arquivo de log, você poderá ver as linhas que foram ignoradas e a causa raiz da incompatibilidade.

Os dados originais e o erro correspondente são registrados no arquivo. Um exemplo do conteúdo do arquivo de log é o seguinte:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a Atividade de Cópia do Azure Data Factory, confira [Mover dados usando a Atividade de Cópia](data-factory-data-movement-activities.md).