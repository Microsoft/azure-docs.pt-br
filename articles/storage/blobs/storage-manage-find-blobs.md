---
title: Gerenciar e localizar dados de blob do Azure com marcas de índice de BLOB (versão prévia)
description: Saiba como usar marcas de índice de BLOB para categorizar, gerenciar e consultar objetos de BLOB.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/19/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: klaasl
ms.custom: references_regions
ms.openlocfilehash: 4f84c3c2f6fc671a8cb6ac70313361540e3dd815
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95523273"
---
# <a name="manage-and-find-azure-blob-data-with-blob-index-tags-preview"></a>Gerenciar e localizar dados de blob do Azure com marcas de índice de BLOB (versão prévia)

À medida que os conjuntos de dados aumentam, encontrar um objeto específico em um Sea of data pode ser difícil. As marcas de índice de blob fornecem recursos de gerenciamento e descoberta de dados usando atributos de marca de índice de chave-valor. Você pode categorizar e localizar objetos em um único contêiner ou em todos os contêineres em sua conta de armazenamento. À medida que os requisitos de dados mudam, os objetos podem ser categorizados dinamicamente atualizando suas marcas de índice. Os objetos podem permanecer no local com sua organização de contêiner atual.

As marcas de índice de blob permitem que você:

- Categorizar dinamicamente seus BLOBs usando marcas de índice de chave-valor
- Localizar rapidamente BLOBs marcados específicos em uma conta de armazenamento inteira
- Especificar comportamentos condicionais para APIs de blob com base na avaliação de marcas de índice
- Usar marcas de índice para controles avançados em recursos como [Gerenciamento de ciclo de vida de blob](storage-lifecycle-management-concepts.md)

Considere um cenário em que você tenha milhões de BLOBs em sua conta de armazenamento, acessados por vários aplicativos diferentes. Você deseja localizar todos os dados relacionados de um único projeto. Você não tem certeza do que está no escopo, pois os dados podem ser distribuídos em vários contêineres com diferentes convenções de nomenclatura. No entanto, seus aplicativos carregam todos os dados com marcas com base em seu projeto. Em vez de Pesquisar por milhões de BLOBs e comparar nomes e propriedades, você pode usar `Project = Contoso` como seus critérios de descoberta. O índice de blob filtrará todos os contêineres em toda a conta de armazenamento para localizar e retornar rapidamente apenas o conjunto de blobs de 50 de `Project = Contoso` .

Para começar com exemplos de como usar o índice de BLOB, consulte [usar marcas de índice de BLOB para gerenciar e localizar dados](storage-blob-index-how-to.md).

## <a name="blob-index-tags-and-data-management"></a>Rótulos de índice de BLOB e gerenciamento de dados

Os prefixos de nome de contêiner e blob são categorizações unidimensionais. As marcas de índice de blob permitem a categorização multidimensional para [tipos de dados de BLOB (bloco, acréscimo ou página)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs). A categorização multidimensional é indexada nativamente pelo armazenamento de BLOBs do Azure para que você possa localizar seus dados rapidamente.

Considere os cinco BLOBs a seguir em sua conta de armazenamento:

- *Container1/transaction.csv*
- *container2/campaign.docx*
- *fotos/bannerphoto.png*
- *arquivos mortos/concluídos/2019review.pdf*
- *logs/2020/01/01/logfile.txt*


Esses BLOBs são separados usando um prefixo de *contêiner/pasta virtual/nome do blob*. Você pode definir um atributo de marca de índice de `Project = Contoso` nesses cinco BLOBs para categorizá-los ao mesmo tempo em que mantém sua organização de prefixo atual. A adição de marcas de índice elimina a necessidade de mover dados expondo a capacidade de filtrar e localizar dados usando o índice.

## <a name="setting-blob-index-tags"></a>Definindo marcas de índice de BLOB

Marcas de índice de blob são atributos de chave-valor que podem ser aplicados a objetos novos ou existentes em sua conta de armazenamento. Você pode especificar marcas de índice durante o processo de carregamento usando o [blob Put](/rest/api/storageservices/put-blob), [colocar lista de blocos](/rest/api/storageservices/put-block-list)ou operações de [copiar blob](/rest/api/storageservices/copy-blob) e o `x-ms-tags` cabeçalho opcional. Se você já tiver BLOBs em sua conta de armazenamento, chame [definir marcas de blob](/rest/api/storageservices/set-blob-tags) passando um documento XML formatado com as marcas de índice no corpo da solicitação.

> [!IMPORTANT]
> A configuração de marcas de índice de blob pode ser executada pelo [proprietário de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) e por qualquer pessoa com uma assinatura de acesso compartilhado que tenha permissão para acessar as marcas do blob (a `t` permissão SAS).
>
> Além disso, os usuários do RBAC com a `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` permissão podem executar essa operação.

Você pode aplicar uma única marca em seu blob para descrever quando seus dados concluíram o processamento.

> "processedDate" = ' 2020-01-01 '

Você pode aplicar várias marcas em seu blob para que sejam mais descritivas dos dados.

> "Projeto" = ' contoso '  
> "Classified" = ' true '  
> "Status" = ' não processado '  
> "Priority" = ' 01 '

Para modificar os atributos de marca de índice existentes, recupere os atributos de marca existentes, modifique os atributos de marca e substitua pela operação [definir marcas de blob](/rest/api/storageservices/set-blob-tags) . Para remover todas as marcas de índice do blob, chame a `Set Blob Tags` operação sem atributos de marca especificados. Como as marcas de índice de blob são um subrecurso para o conteúdo de dados de BLOB, o `Set Blob Tags` não modifica nenhum conteúdo subjacente e não altera a hora da última modificação ou eTag do blob. Você pode criar ou modificar marcas de índice para todos os blobs de base atuais e versões anteriores. No entanto, as marcas em instantâneos ou BLOBs com exclusão reversível não podem ser modificadas.

Os seguintes limites se aplicam a marcas de índice de blob:

- Cada blob pode ter até 10 marcas de índice de BLOB
- As chaves de marca devem estar entre um e 128 caracteres
- Os valores de marca devem estar entre zero e 256 caracteres
- Chaves e valores de marca diferenciam maiúsculas de minúsculas
- As chaves e valores de marca dão suporte apenas a tipos de dados de cadeia de caracteres. Quaisquer números, datas, horas ou caracteres especiais são salvos como cadeias
- As chaves e os valores de marca devem aderir às seguintes regras de nomenclatura:
  - Caracteres alfanuméricos:
    - **a** a **z** (letras minúsculas)
    - **A** a **Z** (letras maiúsculas)
    - **0** a **9** (números)
  - Caracteres especiais válidos: espaço, mais, menos, período, dois-pontos, igual, sublinhado, barra invertida ( ` +-.:=_/` )

## <a name="getting-and-listing-blob-index-tags"></a>Obtendo e listando marcas de índice de BLOB

As marcas de índice de blob são armazenadas como um subrecurso ao lado dos dados de BLOB e podem ser recuperadas independentemente do conteúdo de dados de blob subjacente. As marcas de índice de BLOB para um único blob podem ser recuperadas com a operação [obter marcas de blob](/rest/api/storageservices/get-blob-tags) . A operação [listar BLOBs](/rest/api/storageservices/list-blobs) com o `include:tags` parâmetro também retornará todos os BLOBs dentro de um contêiner, juntamente com suas marcas de índice de BLOB.

> [!IMPORTANT]
> Obter e listar marcas de índice de blob pode ser executado pelo [proprietário de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) e por qualquer pessoa com uma assinatura de acesso compartilhado que tenha permissão para acessar as marcas do blob (a `t` permissão SAS).
>
> Além disso, os usuários do RBAC com a `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` permissão podem executar essa operação.

Para qualquer blob com pelo menos uma marca de índice de BLOB, o `x-ms-tag-count` é retornado nas operações [listar BLOBs](/rest/api/storageservices/list-blobs), [obter blob](/rest/api/storageservices/get-blob)e [obter propriedades de blob](/rest/api/storageservices/get-blob-properties) , indicando a contagem de marcas de índice no BLOB.

## <a name="finding-data-using-blob-index-tags"></a>Localizando dados usando marcas de índice de BLOB

O mecanismo de indexação expõe seus atributos de chave-valor em um índice multidimensional. Depois de definir as marcas de índice, elas existem no BLOB e podem ser recuperadas imediatamente. Pode levar algum tempo antes que o índice de blob seja atualizado. Depois que o índice de blob for atualizado, você poderá usar os recursos nativos de consulta e descoberta oferecidos pelo armazenamento de BLOBs.

A operação [Localizar blobs por marcas](/rest/api/storageservices/find-blobs-by-tags) permite que você obtenha um conjunto filtrado de BLOBs cujas marcas de índice correspondem a uma determinada expressão de consulta. `Find Blobs by Tags` dá suporte à filtragem em todos os contêineres em sua conta de armazenamento ou você pode fazer o escopo da filtragem para apenas um único contêiner. Como todos os valores e chaves de marca de índice são cadeias de caracteres, os operadores relacionais usam uma classificação lexicográfica.

> [!IMPORTANT]
> Localizar dados usando marcas de índice de blob pode ser executado pelo [proprietário de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) e por qualquer pessoa com uma assinatura de acesso compartilhado que tenha permissão para localizar blobs por marcas (a permissão de `f` SAS).
>
> Além disso, os usuários do RBAC com a `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` permissão podem executar essa operação.

Os critérios a seguir se aplicam à filtragem de índice de blob:

- As chaves de marca devem ser colocadas entre aspas duplas (")
- Os valores de marcação e os nomes de contêiner devem ser colocados entre aspas simples (')
- O caractere @ só é permitido para filtragem em um nome de contêiner específico (por exemplo, `@container = 'ContainerName'` )
- Os filtros são aplicados com a classificação lexicográfica em cadeias de caracteres
- As mesmas operações de intervalo colaterais na mesma chave são inválidas (por exemplo, `"Rank" > '10' AND "Rank" >= '15'` )
- Ao usar REST para criar uma expressão de filtro, os caracteres devem ser codificados em URI

A tabela abaixo mostra todos os operadores válidos para `Find Blobs by Tags` :

|  Operador  |  Descrição  | Exemplo |
|------------|---------------|---------|
|     =      |     Igual a     | `"Status" = 'In Progress'` |
|     >      |  Maior que | `"Date" > '2018-06-18'` |
|     >=     |  Maior ou igual | `"Priority" >= '5'` |
|     <      |  Menor que   | `"Age" < '32'` |
|     <=     |  Inferior ou igual  | `"Company" <= 'Contoso'` |
|    AND     |  And lógico  | `"Rank" >= '010' AND "Rank" < '100'` |
| @container | Escopo para um contêiner específico | `@container = 'videofiles' AND "status" = 'done'` |

> [!NOTE]
> Esteja familiarizado com a ordenação de lexicográfica ao configurar e consultar marcas.
>
> - Os números são classificados antes das letras. Os números são classificados com base no primeiro dígito.
> - As letras maiúsculas são classificadas antes das letras minúsculas.
> - Os símbolos não são padrão. Alguns símbolos são classificados antes de valores numéricos. Outros símbolos são classificados antes ou depois das letras.

## <a name="conditional-blob-operations-with-blob-index-tags"></a>Operações de blob condicional com marcas de índice de BLOB

Nas versões REST 2019-10-10 e superiores, a maioria das [APIs de serviço blob](/rest/api/storageservices/operations-on-blobs) agora oferece suporte a um cabeçalho condicional, `x-ms-if-tags` , de modo que a operação só terá sucesso se a condição de índice de blob especificada for atendida. Se a condição não for atendida, você obterá `error 412: The condition specified using HTTP conditional header(s) is not met` .

O `x-ms-if-tags` cabeçalho pode ser combinado com os outros cabeçalhos condicionais http existentes (If-Match, If-None-Match e assim por diante). Se vários cabeçalhos condicionais forem fornecidos em uma solicitação, todos eles deverão ser avaliados como verdadeiros para que a operação tenha sucesso. Todos os cabeçalhos condicionais são efetivamente combinados com AND lógico.

A tabela abaixo mostra os operadores válidos para operações condicionais:

|  Operador  |  Descrição  | Exemplo |
|------------|---------------|---------|
|     =      |     Igual a     | `"Status" = 'In Progress'` |
|     <>     |   Diferente   | `"Status" <> 'Done'` |
|     >      |  Maior que | `"Date" > '2018-06-18'` |
|     >=     |  Maior ou igual | `"Priority" >= '5'` |
|     <      |  Menor que   | `"Age" < '32'` |
|     <=     |  Inferior ou igual  | `"Company" <= 'Contoso'` |
|    AND     |  And lógico  | `"Rank" >= '010' AND "Rank" < '100'` |
|     OU     | OR lógico   | `"Status" = 'Done' OR "Priority" >= '05'` |

> [!NOTE]
> Há dois operadores adicionais, não iguais e lógicos ou, que são permitidos no cabeçalho condicional `x-ms-if-tags` para operações de BLOB, mas não existem na `Find Blobs by Tags` operação.

## <a name="platform-integrations-with-blob-index-tags"></a>Integrações de plataforma com marcas de índice de BLOB

Marcas de índice de BLOB não apenas ajudam a categorizar, gerenciar e Pesquisar em seus dados de BLOB, mas também fornecem integração com outros recursos de armazenamento de BLOBs, como o [Gerenciamento do ciclo de vida](storage-lifecycle-management-concepts.md).

### <a name="lifecycle-management"></a>Gerenciamento do ciclo de vida

Usando o `blobIndexMatch` como um filtro de regras no gerenciamento do ciclo de vida, você pode mover dados para camadas mais frias ou excluir dados com base nas marcas de índice aplicadas aos seus BLOBs. Você pode ser mais granular em suas regras e mover ou excluir somente BLOBs se eles corresponderem aos critérios de marcas especificados.

Você pode definir uma correspondência de índice de blob como um conjunto de filtros autônomo em uma regra de ciclo de vida para aplicar ações em dados marcados. Ou você pode combinar um prefixo e um índice de BLOB para corresponder a conjuntos de dados mais específicos. A especificação de vários filtros em uma regra de ciclo de vida aplica-se uma operação AND lógica. A ação será aplicada somente se *todos os* critérios de filtro corresponderem.

A seguinte regra de gerenciamento do ciclo de vida de exemplo se aplica a blobs de blocos em um contêiner chamado *videofiles*. As camadas de regra são BLOBs para armazenamento de arquivo morto somente se os dados corresponderem aos critérios de marca de índice de blob de `"Status" == 'Processed' AND "Source" == 'RAW'` .

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Exemplo de regra de correspondência de índice de BLOB para gerenciamento de ciclo de vida no portal do Azure](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

# <a name="json"></a>[JSON](#tab/json)

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "ArchiveProcessedSourceVideos",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "tierToArchive": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Status",
                            "op": "==",
                            "value": "Processed"
                        },
                        {
                            "name": "Source",
                            "op": "==",
                            "value": "RAW"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ],
                    "prefixMatch": [
                        "videofiles/"
                    ]
                }
            }
        }
    ]
}
```

---

## <a name="permissions-and-authorization"></a>Permissões e autorização

Você pode autorizar o acesso a marcas de índice de BLOB usando uma das seguintes abordagens:

- Usando o Azure RBAC (controle de acesso baseado em função) para conceder permissões a uma entidade de segurança do Azure Active Directory (Azure AD). Use o Azure AD para obter segurança e facilidade de uso superiores. Para obter mais informações sobre como usar o Azure AD com operações de BLOB, consulte [autorizar o acesso a BLOBs e filas usando o Azure Active Directory](../common/storage-auth-aad.md).
- Usando uma SAS (assinatura de acesso compartilhado) para delegar acesso ao índice de BLOB. Para obter mais informações sobre assinaturas de acesso compartilhado, confira [Conceder acesso limitado a recursos de Armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](../common/storage-sas-overview.md).
- Usando as chaves de acesso da conta para autorizar operações com chave compartilhada. Para obter mais informações, consulte [Autorizar com Chave Compartilhada](/rest/api/storageservices/authorize-with-shared-key).

As marcas de índice de blob são um subrecurso para os dados de BLOB. Um usuário com permissões ou um token SAS para ler ou gravar BLOBs pode não ter acesso às marcas de índice de BLOB.

### <a name="role-based-access-control"></a>Controle de acesso baseado em função

Os chamadores usando uma [identidade do Azure ad](../common/storage-auth-aad.md) podem receber as seguintes permissões para operar em marcas de índice de BLOB.

| Operações de marca de índice de BLOB                                          | Ação do RBAC do Azure                                                             |
|--------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [Definir marcas de BLOB](/rest/api/storageservices/set-blob-tags)           | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/marcas/gravação    |
| [Obter marcas de BLOB](/rest/api/storageservices/get-blob-tags)           | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/marcas/leitura     |
| [Localizar blobs por marcas](/rest/api/storageservices/find-blobs-by-tags) | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/filtro/ação |

Permissões adicionais, separadas dos dados de blob subjacentes, são necessárias para operações de marca de índice. A função de [proprietário de dados de blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) recebe permissões para todas as três operações de marca de índice de BLOB. O [leitor de dados de blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) só recebe permissões para `Find Blobs by Tags` `Get Blob Tags` operações e.

### <a name="sas-permissions"></a>Permissões de SAS

Os chamadores que usam uma [SAS (assinatura de acesso compartilhado)](../common/storage-sas-overview.md) podem receber permissões no escopo para operar em marcas de índice de BLOB.

#### <a name="blob-sas"></a>SAS do blob

As permissões a seguir podem ser concedidas em uma SAS de BLOB para permitir o acesso a marcas de índice de BLOB. Somente permissões de leitura e gravação de BLOB não são suficientes para permitir a leitura ou gravação de suas marcas de índice.

| Permissão | Símbolo de URI | Operações permitidas                |
|------------|------------|-----------------------------------|
| Marcas de índice |     t      | Obter e definir marcas de índice para um blob |

#### <a name="container-sas"></a>SAS do contêiner

As permissões a seguir podem ser concedidas em uma SAS de contêiner para permitir a filtragem em marcas de BLOB. A `Blob List` permissão não é suficiente para permitir a filtragem de BLOBs por suas marcas de índice.

| Permissão | Símbolo de URI | Operações permitidas         |
|------------|------------|----------------------------|
| Marcas de índice |     f      | Localizar BLOBs com marcas de índice |

## <a name="choosing-between-metadata-and-blob-index-tags"></a>Escolhendo entre as marcas de índice de BLOB e metadados

As marcas de índice de BLOB e os metadados fornecem a capacidade de armazenar propriedades de valor de chave, definidas pelo usuário, junto com um recurso de BLOB. Ambos podem ser recuperados e definidos diretamente, sem retornar ou alterar o conteúdo do blob. É possível usar as marcas de índice e de metadados.

Somente as marcas de índice são indexadas automaticamente e tornam-se pesquisáveis pelo serviço de armazenamento de blob nativo. Os metadados não podem ser indexados nativamente ou pesquisados. Você deve usar um serviço separado, como [Azure Search](../../search/search-blob-ai-integration.md). Marcas de índice de blob têm permissões adicionais para leitura, filtragem e gravação que são separadas dos dados de blob subjacentes. Os metadados usam as mesmas permissões que o blob e são retornados como cabeçalhos HTTP pelas operações [obter blob](/rest/api/storageservices/get-blob) e [obter propriedades de blob](/rest/api/storageservices/get-blob-properties) . Marcas de índice de blob são criptografadas em repouso usando uma [chave gerenciada pela Microsoft](../common/storage-service-encryption.md). Os metadados são criptografados em repouso usando a mesma chave de criptografia especificada para dados de BLOB.

A tabela a seguir resume as diferenças entre as marcas de índice de BLOB e metadados:

|              |   Metadados   |   Marcas de índice de BLOB  |
|--------------|--------------|--------------------|
| **Limites**      | Nenhum limite numérico, total de 8 KB, não diferencia maiúsculas de minúsculas | 10 marcas por blob máx, 768 bytes por marca, diferencia maiúsculas de minúsculas |
| **Atualizações**    | Não permitido na camada de arquivo, `Set Blob Metadata` substitui todos os metadados existentes, `Set Blob Metadata` altera a hora da última modificação do blob | Permitido para todas as camadas de acesso, `Set Blob Tags` substitui todas as marcas existentes, `Set Blob Tags` não altera a hora da última modificação do blob |
| **Armazenamento**     | Armazenados com os dados do blob | Subrecurso dos dados do blob |
| **Indexação & consulta** | Deve usar um serviço separado, como Azure Search | Recursos de indexação e consulta incorporados ao armazenamento de BLOBs |
| **Criptografia** | Criptografado em repouso com a mesma chave de criptografia usada para dados de BLOB | Criptografado em repouso com uma chave de criptografia gerenciada pela Microsoft |
| **Preços** | O tamanho dos metadados está incluído nos custos de armazenamento de um blob | Custo fixo por marca de índice |
| **Resposta de cabeçalho** | Metadados retornados como cabeçalhos em `Get Blob` e `Get Blob Properties` | Contagem de marcas retornada por `Get Blob` ou `Get Blob Properties` , marcas retornadas somente por `Get Blob Tags` e `List Blobs` |
| **Permissões**  | Permissões de leitura ou gravação para dados de BLOB se estende aos metadados | Permissões adicionais são necessárias para ler, filtrar ou gravar marcas de índice |
| **Nomenclatura** | Os nomes de metadados devem aderir às regras de nomenclatura para identificadores C# | As marcas de índice de blob dão suporte a um intervalo mais amplo de caracteres alfanuméricos |

## <a name="pricing"></a>Preços

O preço do índice de blob está em visualização pública e está sujeito a alterações para disponibilidade geral. Você é cobrado pelo número médio mensal de marcas de índice em uma conta de armazenamento. Não há custo para o mecanismo de indexação. Solicitações para `Set Blob Tags` , `Get Blob Tags` e `Find Blobs by Tags` são cobradas de acordo com seus respectivos tipos de operação. Consulte [preços do blob de blocos para saber mais](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability-and-storage-account-support"></a>Disponibilidade regional e suporte à conta de armazenamento

As marcas de índice de blob estão disponíveis somente em contas Uso Geral v2 (GPv2) com o namespace hierárquico (HNS) desabilitado. Não há suporte para contas do Uso Geral (GPV1), mas você pode atualizar qualquer conta do GPv1 para uma conta do GPv2.

Não há suporte para marcas de índice em contas de armazenamento Premium. Para saber mais sobre as contas de armazenamento, confira [Visão geral da conta de armazenamento do Azure](../common/storage-account-overview.md).

Na visualização pública, as marcas de índice de blob estão disponíveis apenas nas seguintes regiões:

- Canadá Central
- Leste do Canadá
- França Central
- Sul da França

Para começar, consulte [usar marcas de índice de BLOB para gerenciar e localizar dados](storage-blob-index-how-to.md).

> [!IMPORTANT]
> Você deve registrar sua assinatura antes de poder usar a visualização do índice de BLOB em suas contas de armazenamento. Consulte a seção [condições e problemas conhecidos](#conditions-and-known-issues) deste artigo.

### <a name="register-your-subscription-preview"></a>Registrar sua assinatura (versão prévia)

Como as marcas de índice de blob estão apenas em visualização pública, você precisará registrar sua assinatura antes de poder usar o recurso. Para enviar uma solicitação, execute os seguintes comandos da CLI ou do PowerShell.

#### <a name="register-by-using-powershell"></a>Registrar-se usando o PowerShell

```powershell
Register-AzProviderFeature -FeatureName BlobIndex -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

#### <a name="register-by-using-azure-cli"></a>Registrar usando CLI do Azure

```azurecli
az feature register --namespace Microsoft.Storage --name BlobIndex
az provider register --namespace 'Microsoft.Storage'
```

## <a name="conditions-and-known-issues"></a>Condições e problemas conhecidos

Esta seção descreve os problemas e condições conhecidos na visualização pública de marcas de índice de BLOB. Esse recurso não deve ser usado para cargas de trabalho de produção até alcançar a disponibilidade geral (GA), pois o comportamento pode ser alterado.

- Para visualização, você deve primeiro registrar sua assinatura antes de poder usar o índice de BLOB para sua conta de armazenamento nas regiões de visualização.
- Somente contas GPv2 têm suporte na visualização. As contas do datalake Gen2 do blob, BlockBlobStorage e do HNS habilitadas não têm suporte. Não haverá suporte para contas do GPv1.
- O carregamento de blobs de página com marcas de índice não mantém as marcas. Defina as marcas depois de carregar um blob de páginas.
- Quando a filtragem estiver no escopo de um único contêiner, o `@container` só poderá ser passado se todas as marcas de índice na expressão de filtro forem verificações de igualdade (chave = valor).
- Ao usar o operador Range com a `AND` condição, você só pode especificar o mesmo nome de chave de marca de índice ( `"Age" > '013' AND "Age" < '100'` ).
- Não há suporte para controle de versão e índice de BLOB. As marcas de índice de blob são preservadas para versões, mas não são passadas para o mecanismo de índice de BLOB.
- Não há API para determinar se as marcas de índice estão indexadas.
- Não há suporte para o failover de conta. O índice de blob pode não ser atualizado corretamente após o failover.
- O gerenciamento do ciclo de vida oferece suporte a verificações de igualdade com correspondência de índice de BLOB.
- `Copy Blob` não copia marcas de índice de blob do blob de origem para o novo BLOB de destino. Você pode especificar as marcas que deseja aplicar ao blob de destino durante a operação de cópia.
- `Copy Blob` (Cópia assíncrona) de outra conta de armazenamento com marcas aplicadas no blob de destino faz com que o mecanismo de índice de BLOB não retorne o blob e suas marcas no conjunto de filtros. Usar `Copy Blob` da URL (cópia de sincronização).
- As marcas são persistidas na criação do instantâneo. No entanto, a promoção de um instantâneo não tem suporte e pode resultar em um conjunto de marcas vazio.

## <a name="faq"></a>Perguntas frequentes

**O índice de BLOBs pode me ajudar a filtrar e consultar conteúdo dentro de meus BLOBs?**

Não, se você precisar pesquisar em seus dados de BLOB, use a aceleração de consulta ou o Azure Search.

**Há requisitos em valores de marca de índice?**

Marcas de índice de blob dão suporte apenas a tipos de dados de cadeia de caracteres e a consulta retorna resultados com a ordenação lexicográfica. Para números, zero Pad o número. Para datas e horas, armazene como um formato compatível com ISO 8601.

**Marcas de índice de BLOB e marcas de Azure Resource Manager estão relacionadas?**

Não, as marcas do Gerenciador de recursos ajudam a organizar os recursos do plano de controle, como assinaturas, grupos de recursos e contas de armazenamento. Marcas de índice fornecem gerenciamento de BLOB e descoberta no plano de dados.

## <a name="next-steps"></a>Próximas etapas

Para obter um exemplo de como usar o índice de BLOB, consulte [usar o índice de BLOB para gerenciar e localizar dados](storage-blob-index-how-to.md).

Saiba mais sobre o [Gerenciamento do ciclo de vida](storage-lifecycle-management-concepts.md) e defina uma regra com correspondência de índice de BLOB.