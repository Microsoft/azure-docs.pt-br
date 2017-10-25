### <a name="azure-storage-linked-service"></a>Serviço vinculado de armazenamento do Azure
O **serviço vinculado do Azure Storage** permite que você vincule uma conta de armazenamento do Azure ao Azure Data Factory usando a **chave de conta**, o que oferece ao data factory acesso global ao Azure Storage. A tabela a seguir fornece a descrição para elementos JSON específicas para o serviço de Armazenamento do Azure vinculado.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type |A propriedade type deve ser definida como: **AzureStorage** |Sim |
| connectionString |Especifique as informações necessárias para se conectar ao armazenamento do Azure para a propriedade connectionString. |Sim |

Consulte o seguinte artigo para obter as etapas para exibir/copiar a chave de conta para um armazenamento do Azure: [Exibir, copiar e regenerar as chaves de acesso de armazenamento](../articles/storage/common/storage-create-storage-account.md#manage-your-storage-account).

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
Uma SAS (Assinatura de Acesso Compartilhado) fornece acesso delegado aos recursos da sua conta de armazenamento. Isso permite conceder a um cliente permissões limitadas a objetos na sua conta de armazenamento por um período especificado e com um conjunto determinado de permissões, sem precisar compartilhar suas chaves de acesso de conta. A SAS é um URI que engloba em seus parâmetros de consulta todas as informações necessárias para o acesso autenticado a um recurso de armazenamento. Para acessar recursos de armazenamento com a SAS, o cliente só precisa passar a SAS ao construtor apropriado ou ao método apropriado. Para obter informações detalhadas sobre SAS, consulte [Assinaturas de Acesso Compartilhado: entendendo o modelo SAS](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)

> [!IMPORTANT]
> O Azure Data Factory agora dá suporte somente a **SAS do serviço**, mas não SAS de conta. Consulte os [Tipos de Assinaturas de Acesso Compartilhado](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) para obter detalhes sobre esses dois tipos e como criá-los. Observe que a URL de SAS que pode ser gerada no portal do Azure ou no Gerenciador de Armazenamento é uma SAS de conta, que não tem suporte.
> 

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

