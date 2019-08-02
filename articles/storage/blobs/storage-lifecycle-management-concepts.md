---
title: Gerenciando o ciclo de vida do armazenamento do Azure
description: Aprenda a criar regras de política de ciclo de vida para fazer a transição dos dados antigos para os níveis Hot to Cool e Archive.
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mhopkins
ms.reviewer: yzheng
ms.subservice: common
ms.openlocfilehash: 6902bf73707dc749da76cd32fe48911fcc88ba1e
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305718"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Gerenciar o ciclo de vida do armazenamento de BLOBs do Azure

Conjuntos de dados têm ciclos de vida exclusivos. No início do ciclo de vida, as pessoas geralmente acessam alguns dados. Mas a necessidade de acesso cai drasticamente à medida que os dados envelhecem. Alguns dados permanecem ociosos na nuvem e raramente são acessados depois de armazenados. Alguns dados expiram dias ou meses após a criação, enquanto outros conjuntos de dados são lidos e modificados ativamente durante seu tempo de vida. O gerenciamento do ciclo de vida do armazenamento de BLOBs do Azure oferece uma política avançada baseada em regras para contas de armazenamento de BLOBs e GPv2. Use a política para fazer a transição de seus dados para as camadas de acesso apropriadas ou expirá-los ao final do ciclo de vida dos dados.

A política de gerenciamento do ciclo de vida permite:

- Fazer a transição dos blobs para uma camada de armazenamento mais esporádico (frequente para esporádico, frequente para arquivos ou esporádico para arquivos), a fim de otimizar o desempenho e os custos
- Excluir os blobs no final dos respectivos ciclos de vida
- Definir regras a serem executadas uma vez por dia no nível da conta de armazenamento
- Aplicar regras a contêineres ou a um subconjunto de blobs (usando prefixos como filtros)

Considere um cenário em que os dados recebem acesso frequente durante os estágios iniciais do ciclo de vida, mas apenas ocasionalmente após duas semanas. Após o primeiro mês, o conjunto de dados raramente é acessado. Nesse cenário, o armazenamento frequente é melhor durante os estágios iniciais. O armazenamento frio é mais apropriado para acesso ocasional. O armazenamento de arquivos é a melhor opção de camada depois que os dados ficam em um mês. Ajustando as camadas de armazenamento em relação à idade dos dados, você pode criar as opções de armazenamento menos dispendiosas para suas necessidades. Para conseguir essa transição, as regras de política de gerenciamento do ciclo de vida estão disponíveis para mover os dados antigos para camadas mais frias.

## <a name="storage-account-support"></a>Suporte da conta de armazenamento

A política de gerenciamento do ciclo de vida está disponível com contas do Uso Geral v2 (GPv2), contas de armazenamento de BLOBs e contas de armazenamento de blob de blocos Premium. No portal do Azure, você pode atualizar uma conta de Uso Geral (GPv1) existente para uma conta do GPv2. Para saber mais sobre as contas de armazenamento, confira [Visão geral da conta de armazenamento do Azure](../common/storage-account-overview.md).  

## <a name="pricing"></a>Preços

O recurso de gerenciamento do ciclo de vida é gratuito. O custo de operação normal para as chamadas à API [Listar Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) e [Definir Camada de Blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) é cobrado dos clientes. A operação de exclusão é gratuita. Para obter mais informações sobre preços, confira [Preços do Blob de Blocos](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability"></a>Disponibilidade regional

O recurso de gerenciamento do ciclo de vida está disponível em todas as regiões globais do Azure e do Azure governamental.

## <a name="add-or-remove-a-policy"></a>Adicionar ou remover uma política

Você pode adicionar, editar ou remover uma política usando qualquer um dos seguintes métodos:

* [Portal do Azure](https://portal.azure.com)
* [PowerShell do Azure](https://github.com/Azure/azure-powershell/releases)
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [APIs REST](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

Este artigo mostra como gerenciar a política usando os métodos do portal e do PowerShell.  

> [!NOTE]
> Se você habilitar as regras de firewall para sua conta de armazenamento, as solicitações de gerenciamento do ciclo de vida poderão ser bloqueadas. Desbloqueie essas solicitações fornecendo exceções. O bypass necessário são: `Logging,  Metrics,  AzureServices`. Para obter mais informações, confira a seção Exceções em [Configurar firewalls e redes virtuais](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

### <a name="azure-portal"></a>Portal do Azure

Há duas maneiras de adicionar uma política por meio do portal do Azure. 

* [Exibição de lista de portal do Azure](#azure-portal-list-view)
* [Exibição de código portal do Azure](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Exibição de lista de portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Selecione **todos os recursos** e, em seguida, selecione sua conta de armazenamento.

3. Em **serviço blob**, selecione **Gerenciamento de ciclo de vida** para exibir ou alterar suas regras.

4. Selecione a guia **exibição de lista** .

5. Selecione **Adicionar regra** e preencha os campos de formulário do **conjunto de ações** . No exemplo a seguir, os BLOBs são movidos para o armazenamento frio, caso não tenham sido modificados por 30 dias.

   ![Página conjunto de ações de gerenciamento do ciclo de vida no portal do Azure](media/storage-lifecycle-management-concepts/lifecycle-management-action-set.png)

6. Selecione **conjunto de filtros** para adicionar um filtro opcional. Em seguida, selecione **procurar** para especificar um contêiner e uma pasta pela qual filtrar.

   ![Página conjunto de filtros de gerenciamento do ciclo de vida no portal do Azure](media/storage-lifecycle-management-concepts/lifecycle-management-filter-set-browse.png)

8. Selecione **examinar + adicionar** para examinar as configurações de política.

9. Selecione **Adicionar** para adicionar a nova política.

#### <a name="azure-portal-code-view"></a>Exibição de código portal do Azure
1. Entre no [Portal do Azure](https://portal.azure.com).

2. Selecione **todos os recursos** e, em seguida, selecione sua conta de armazenamento.

3. Em **serviço blob**, selecione **Gerenciamento de ciclo de vida** para exibir ou alterar sua política.

4. O JSON a seguir é um exemplo de uma política que pode ser colada na guia **exibição de código** .

   ```json
   {
     "rules": [
       {
         "name": "ruleFoo",
         "enabled": true,
         "type": "Lifecycle",
         "definition": {
           "filters": {
             "blobTypes": [ "blockBlob" ],
             "prefixMatch": [ "container1/foo" ]
           },
           "actions": {
             "baseBlob": {
               "tierToCool": { "daysAfterModificationGreaterThan": 30 },
               "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
               "delete": { "daysAfterModificationGreaterThan": 2555 }
             },
             "snapshot": {
               "delete": { "daysAfterCreationGreaterThan": 90 }
             }
           }
         }
       }
     ]
   }
   ```

5. Clique em **Salvar**.

6. Para obter mais informações sobre este exemplo de JSON, consulte as seções [política](#policy) e [regras](#rules) .

### <a name="powershell"></a>PowerShell

O script do PowerShell a seguir pode ser usado para adicionar uma política à sua conta de armazenamento. A `$rgname` variável deve ser inicializada com o nome do grupo de recursos. A `$accountName` variável deve ser inicializada com o nome da conta de armazenamento.

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery

#Initialize the following with your resource group and storage account names
$rgname = ""
$accountName = ""

#Create a new action object
$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete -daysAfterModificationGreaterThan 2555
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToArchive -daysAfterModificationGreaterThan 90
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToCool -daysAfterModificationGreaterThan 30
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -SnapshotAction Delete -daysAfterCreationGreaterThan 90

# Create a new filter object
# PowerShell automatically sets BlobType as “blockblob” because it is the only available option currently
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd

#Create a new rule object
#PowerShell automatically sets Type as “Lifecycle” because it is the only available option currently
$rule1 = New-AzStorageAccountManagementPolicyRule -Name Test -Action $action -Filter $filter

#Set the policy
$policy = Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1
```

## <a name="azure-resource-manager-template-with-lifecycle-management-policy"></a>Modelo de Azure Resource Manager com a política de gerenciamento de ciclo de vida

Você pode definir o gerenciamento do ciclo de vida usando modelos de Azure Resource Manager. Aqui está um modelo de exemplo para implantar uma conta de armazenamento RA-GRS GPv2 com uma política de gerenciamento do ciclo de vida.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "storageAccountName": "[uniqueString(resourceGroup().id)]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-04-01",
      "sku": {
        "name": "Standard_RAGRS"
      },
      "kind": "StorageV2",
      "properties": {
        "networkAcls": {}
      }
    },
    {
      "name": "[concat(variables('storageAccountName'), '/default')]",
      "type": "Microsoft.Storage/storageAccounts/managementPolicies",
      "apiVersion": "2019-04-01",
      "dependsOn": [
        "[variables('storageAccountName')]"
      ],
      "properties": {
        "policy": {...}
      }
    }
  ],
  "outputs": {}
}
```

## <a name="policy"></a>Política

Uma política de gerenciamento do ciclo de vida é uma coleção de regras em um documento JSON:

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```

Uma política é uma coleção de regras:

| Nome do parâmetro | Tipo de parâmetro | Observações |
|----------------|----------------|-------|
| `rules`        | Uma matriz de objetos de regra | Pelo menos uma regra é necessária em uma política. Você pode definir até 100 regras em uma política.|

Cada regra na política tem vários parâmetros:

| Nome do parâmetro | Tipo de parâmetro | Observações | Obrigatório |
|----------------|----------------|-------|----------|
| `name`         | Cadeia de caracteres |Um nome de regra pode incluir até 256 caracteres alfanuméricos. A regra de nome diferencia maiúsculas de minúsculas.  Ela deve ser exclusiva em uma política. | verdadeiro |
| `enabled`      | Boolean | Um booliano opcional para permitir que uma regra seja temporariamente desabilitada. O valor padrão será true se não estiver definido. | False | 
| `type`         | Um valor de enumeração | O tipo válido atual é `Lifecycle`. | verdadeiro |
| `definition`   | Um objeto que define a regra de ciclo de vida | Cada definição é composta por um conjunto de filtros e um conjunto de ações. | verdadeiro |

## <a name="rules"></a>Regras

Cada definição de regra inclui um conjunto de filtros e um conjunto de ações. O [conjunto de filtros](#rule-filters) limita as ações de regras a determinado conjunto de objetos em um contêiner ou a nomes de objetos. O [conjunto de ações](#rule-actions) aplica-se a camada ou excluir ações para o conjunto filtrado de objetos.

### <a name="sample-rule"></a>Regra de exemplo

A regra de exemplo a seguir filtra a conta para executar as ações em objetos que `container1` existem dentro e `foo`começa com.  

- Colocar o blob na camada esporádica 30 dias após a última modificação
- Colocar o blob na camada de arquivos 90 dias após a última modificação
- Excluir o blob 2.555 dias (sete anos) após a última modificação
- Excluir instantâneos de blob 90 dias após a criação do instantâneo

```json
{
  "rules": [
    {
      "name": "ruleFoo",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
            "delete": { "daysAfterModificationGreaterThan": 2555 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>Filtros de regra

Os filtros limitam as ações de regra a um subconjunto de blobs na conta de armazenamento. Se mais de um filtro for definido, um `AND` lógico será executado em todos os filtros.

Os filtros incluem:

| Nome do filtro | Tipo do filtro | Observações | Obrigatório |
|-------------|-------------|-------|-------------|
| blobTypes   | Uma matriz de valores de enumeração predefinidos. | A versão atual dá `blockBlob`suporte ao. | Sim |
| prefixMatch | Uma matriz de cadeias de caracteres para prefixos a serem correspondidos. Cada regra pode definir até 10 prefixos. Uma cadeia de caracteres de prefixo deve começar com um nome de contêiner. Por exemplo, se você quiser corresponder a todos os BLOBs `https://myaccount.blob.core.windows.net/container1/foo/...` em para uma regra, o prefixMatch `container1/foo`será. | Se você não definir prefixMatch, a regra se aplicará a todos os BLOBs na conta de armazenamento.  | Não |

### <a name="rule-actions"></a>Ações de regra

As ações são aplicadas aos BLOBs filtrados quando a condição de execução é atendida.

O gerenciamento do ciclo de vida dá suporte a camadas e exclusão de BLOBs e exclusão de instantâneos de BLOB. Defina, pelo menos, uma ação para cada regra em blobs ou instantâneos de blob.

| Action        | Blob base                                   | Instantâneo      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Dá suporte aos blobs atualmente presentes na camada frequente         | Sem suporte |
| tierToArchive | Dá suporte aos blobs atualmente presentes na camada frequente ou esporádica | Sem suporte |
| delete        | Com suporte                                   | Com suporte     |

>[!NOTE]
>Se você definir mais de uma ação no mesmo blob, o gerenciamento do ciclo de vida aplicará a ação mais barata ao blob. Por exemplo, a ação `delete` é mais barata do que a ação `tierToArchive`. A ação `tierToArchive` é mais barata do que a ação `tierToCool`.

As condições de execução se baseiam na idade. Os blobs base usam a hora da última modificação para acompanhar a idade, enquanto os instantâneos de blob usam a hora de criação do instantâneo para executar a mesma tarefa.

| Condição de execução de ação             | Valor de condição                          | DESCRIÇÃO                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| daysAfterModificationGreaterThan | Valor inteiro que indica a idade em dias | A condição para ações de blob de base     |
| daysAfterCreationGreaterThan     | Valor inteiro que indica a idade em dias | A condição para ações de instantâneo de BLOB |

## <a name="examples"></a>Exemplos

Os exemplos a seguir demonstram como tratar cenários comuns com as regras de política de ciclo de vida.

### <a name="move-aging-data-to-a-cooler-tier"></a>Mover dados antigos para uma camada mais fria

Este exemplo mostra como fazer a transição de blobs de blocos prefixados com `container1/foo` ou `container2/bar`. A política faz a transição de blobs que não foram modificados há mais de 30 dias para o armazenamento esporádico e de blobs não modificados há mais de 90 dias para a camada de arquivos:

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo", "container2/bar" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="archive-data-at-ingest"></a>Arquivar dados em ingestão

Alguns dados permanecem ociosos na nuvem e raramente ou nunca são acessados depois de armazenados. A política de ciclo de vida a seguir é configurada para arquivar dados após sua ingestão. Este exemplo faz a transição de blobs de blocos na conta de `archivecontainer` armazenamento dentro do contêiner para uma camada de arquivo morto. A transição é realizada agindo em BLOBs 0 dias após a hora da última modificação:

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

### <a name="expire-data-based-on-age"></a>Expirar os dados com base na idade

Espera-se que alguns dados expirem dias ou meses após a criação. Configure uma política de gerenciamento do ciclo de vida para expirar os dados por exclusão com base na idade deles. O exemplo a seguir mostra uma política que exclui todos os blobs de bloco com mais de 365 dias.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-old-snapshots"></a>Excluir instantâneos antigos

Para dados que são modificados e acessados regularmente durante seu ciclo de vida, instantâneos geralmente são usados para controlar versões mais antigas dos dados. Você pode criar uma política que exclui os instantâneos antigos com base na idade do instantâneo. A idade de instantâneo é determinada avaliando-se a hora de criação do instantâneo. Esta regra de política exclui instantâneos blobs de blocos no contêiner `activedata` com 90 dias ou mais após a criação do instantâneo.

```json
{
  "rules": [
    {
      "name": "snapshotRule",
      "enabled": true,
      "type": "Lifecycle",
    "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "activedata" ]
        },
        "actions": {
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="faq"></a>Perguntas Frequentes

**Eu criei uma nova política, por que as ações não são executadas imediatamente?**  
A plataforma executa a política de ciclo de vida uma vez por dia. Depois de configurar uma política, pode levar até 24 horas para que algumas ações sejam executadas pela primeira vez.  

**Eu reidratar manualmente um blob arquivado, como impedir que ele fosse movido de volta para a camada de arquivamento temporariamente?**  
Quando um blob é movido de uma camada de acesso para outra, sua hora da última modificação não é alterada. Se você reidratar manualmente um blob arquivado na camada quente, ele seria movido de volta para a camada de arquivo pelo mecanismo de gerenciamento do ciclo de vida. Desabilite a regra que afeta esse blob temporariamente para impedir que ele seja arquivado novamente. Copie o blob para outro local se ele precisar permanecer na camada quente permanentemente. Habilite novamente a regra quando o blob puder ser movido com segurança de volta para a camada de arquivo morto. 


## <a name="next-steps"></a>Próximas etapas

Saiba como recuperar dados após uma exclusão acidental:

- [Exclusão reversível para BLOBs de armazenamento do Azure](../blobs/storage-blob-soft-delete.md)
