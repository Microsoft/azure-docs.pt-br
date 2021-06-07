---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 37917e0ed663675677f1d0452b5796120ca2694e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001704"
---
### <a name="azure-storage-linked-service"></a>Serviço vinculado de armazenamento do Azure
O **serviço vinculado do Azure Storage** permite que você vincule uma conta de armazenamento do Azure ao Azure Data Factory usando a **chave de conta**, o que oferece ao data factory acesso global ao Azure Storage. A tabela a seguir fornece a descrição para elementos JSON específicas para o serviço de Armazenamento do Azure vinculado.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type |A propriedade type deve ser definida como: **AzureStorage** |Sim |
| connectionString |Especifique as informações necessárias para se conectar ao armazenamento do Azure para a propriedade connectionString. |Sim |

Para obter informações sobre como recuperar as chaves de acesso da conta de armazenamento, confira [Gerenciar chaves de acesso da conta de armazenamento](../articles/storage/common/storage-account-keys-manage.md).

**Exemplo:**  

```json
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

### <a name="azure-storage-sas-linked-service"></a>Serviço vinculado de SAS de armazenamento do Azure
Uma SAS (Assinatura de Acesso Compartilhado) fornece acesso delegado aos recursos da sua conta de armazenamento. Isso permite conceder a um cliente permissões limitadas a objetos na sua conta de armazenamento por um período especificado e com um conjunto determinado de permissões, sem precisar compartilhar suas chaves de acesso de conta. A SAS é um URI que engloba em seus parâmetros de consulta todas as informações necessárias para o acesso autenticado a um recurso de armazenamento. Para acessar recursos de armazenamento com a SAS, o cliente só precisa passar a SAS ao construtor apropriado ou ao método apropriado. Para obter mais informações sobre SAS, confira [Conceder acesso limitado a recursos de Armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](../articles/storage/common/storage-sas-overview.md).

> [!IMPORTANT]
> O Azure Data Factory agora dá suporte somente a **SAS do serviço**, mas não SAS de conta. Observe que a URL de SAS que pode ser gerada no portal do Azure ou no Gerenciador de Armazenamento é uma SAS de conta, que não tem suporte.

> [!TIP]
> Você pode executar abaixo comandos do PowerShell para gerar uma SAS do Serviço para sua conta de armazenamento (substitua os espaços reservados e conceda a permissão necessária): `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

O serviço vinculado de SAS de armazenamento do Azure permite que você vincule uma conta de armazenamento do Azure ao Azure Data Factory usando uma SAS (Assinatura de Acesso Compartilhado). Isso fornece ao data factory acesso restrito/acesso total, com limite de tempo/recursos específicos (blob/contêiner) no armazenamento. A tabela a seguir fornece a descrição para elementos JSON específicos para o Serviço vinculado de SAS de armazenamento do Azure. 

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type |A propriedade type deve ser definida como: **AzureStorageSas** |Sim |
| sasUri |Especificar o URI de Assinatura de Acesso Compartilhado para os recursos de Armazenamento do Azure, como blob, contêiner ou tabela.  |Sim |

**Exemplo:**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<Specify SAS URI of the Azure Storage resource>"
        }
    }
}
```

Ao criar um **URI de SAS**, considerando o seguinte:  

* Defina as **permissões** apropriadas de leitura/gravação em objetos com base em como o serviço vinculado (leitura, gravação, leitura/gravação) será usado no data factory.
* Defina o **Tempo de expiração** adequadamente. Certifique-se de que o acesso aos objetos de Armazenamento do Azure não expira dentro do período ativo do pipeline.
* O URI deve ser criado no contêiner/blob à direita ou no nível de tabela com base na necessidade. Um URI de SAS para um blob do Azure permite que o serviço Data Factory acesse o blob específico. Um URI de SAS para um contêiner de blob do Azure permite que o serviço Data Factory faça iteração por meio dos blobs nesse contêiner. Se você precisar fornecer acesso para mais/menos objetos posteriormente, ou atualizar o URI de SAS, lembre-se de atualizar o serviço vinculado com o novo URI.   

