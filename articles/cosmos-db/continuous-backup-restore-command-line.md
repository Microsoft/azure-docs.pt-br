---
title: Use a CLI do Azure para configurar o backup contínuo e a restauração pontual no Azure Cosmos DB.
description: Saiba como provisionar uma conta com o backup contínuo e restaurar dados usando a CLI do Azure.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9ea71dae746ac423e7b17b6235b4d5cd3e143cd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377322"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-cli"></a>Configurar e gerenciar o backup contínuo e a restauração pontual (versão prévia) usando a CLI do Azure
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> O recurso de restauração pontual (modo de backup contínuo) do Azure Cosmos DB está atualmente em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O recurso de restauração pontual do Azure Cosmos DB (versão prévia) ajuda você a se recuperar de uma alteração acidental em um contêiner para restaurar uma conta excluída, um banco de dados ou um contêiner ou para restaurar para qualquer região (na qual os backups existiam). O modo de backup contínuo permite que você restaure para qualquer momento nos últimos 30 dias.

Este artigo descreve como provisionar uma conta com backup contínuo e restaurar dados usando a CLI do Azure.

## <a name="install-azure-cli"></a><a id="install"></a>Instalar a CLI do Azure

1. Instale a versão mais recente da CLI do Azure

   * Instale a versão mais recente da [CLI do Azure](/cli/azure/install-azure-cli) ou uma versão superior a 2.17.1.
   * Se você já tiver instalado a CLI, execute o comando `az upgrade` para atualizar para a versão mais recente. Este comando funcionará apenas com uma versão da CLI superior a 2.11. Se você tiver uma versão anterior, use o link acima para instalar a versão mais recente.

1. Instale a extensão da CLI `cosmosdb-preview`.

   * Os comandos de restauração pontual estão disponíveis na extensão `cosmosdb-preview`.
   * Você pode instalar essa extensão executando o seguinte comando: `az extension add --name cosmosdb-preview`
   * Você pode desinstalar essa extensão executando o seguinte comando: `az extension remove --name cosmosdb-preview`

1. Entrar e selecione sua assinatura

   * Entre na sua conta do Azure com o comando `az login`.
   * Selecione a assinatura necessária usando o comando `az account set -s <subscriptionguid>`.

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>Provisionar uma conta da API do SQL com backup contínuo

Para provisionar uma conta da API do SQL com backup contínuo, o argumento extra `--backup-policy-type Continuous` deve ser passado com o comando de provisionamento normal. O seguinte comando é um exemplo de uma conta de gravação de uma região chamada `pitracct2` com a política de backup contínuo criada na região *Oeste dos EUA* no grupo de recursos *myrg*:

```azurecli-interactive

az cosmosdb create \
  --name pitracct2 \
  --resource-group myrg \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="provision-an-azure-cosmos-db-api-for-mongodb-account-with-continuous-backup"></a><a id="provision-mongo-api"></a>Provisionar uma conta da API do Azure Cosmos DB para MongoDB com backup contínuo

O seguinte comando mostra um exemplo de uma conta de gravação de uma região chamada `pitracct3` com a política de backup contínuo criada na região *Oeste dos EUA* no grupo de recursos *myrg*:

```azurecli-interactive

az cosmosdb create \
  --name pitracct3 \
  --kind MongoDB \
  --resource-group myrg \
  --server-version "3.6" \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="trigger-a-restore-operation-with-cli"></a><a id="trigger-restore"></a>Disparar uma operação de restauração com a CLI

A maneira mais simples de disparar uma restauração é emitindo o comando restore com o nome da conta de destino, a conta de origem, a localização, o grupo de recursos, o carimbo de data/hora (em UTC) e, opcionalmente, os nomes do banco de dados e do contêiner. Veja abaixo alguns exemplos para disparar a operação de restauração:

1. Crie uma conta do Azure Cosmos DB restaurando uma conta existente.

   ```azurecli-interactive

   az cosmosdb restore \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --resource-group MyResourceGroup \
    --location "West US"

   ```

2. Crie uma conta do Azure Cosmos DB restaurando somente os bancos de dados e contêineres selecionados de uma conta de banco de dados existente.

   ```azurecli-interactive

   az cosmosdb restore \
    --resource-group MyResourceGroup \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --location "West US" \
    --databases-to-restore name=MyDB1 collections=collection1 collection2 \
    --databases-to-restore name=MyDB2 collections=collection3 collection4

   ```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Enumerar os recursos restauráveis da API do SQL

Os comandos de enumeração descritos abaixo ajudam a descobrir os recursos que estão disponíveis para restauração em vários carimbos de data/hora. Além disso, eles também fornecem um feed de eventos importantes da conta restaurável, do banco de dados e dos recursos de contêiner.

**Listar todas as contas que podem ser restauradas na assinatura atual**

Execute o seguinte comando da CLI para listar todas as contas que podem ser restauradas na assinatura atual

```azurecli-interactive
az cosmosdb restorable-database-account list --account-name "pitrbb"
```

A resposta inclui todas as contas de banco de dados (tanto as dinâmicas e como as excluídas) que podem ser restauradas e as regiões das quais elas podem ser restauradas:

```json
{
    "accountName": "pitrbb",
    "apiType": "Sql",
    "creationTime": "2021-01-08T23:34:11.095870+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865",
    "identity": null,
    "location": "West US",
    "name": "7133a59a-d1c0-4645-a699-6e296d6ac865",
    "restorableLocations": [
      {
        "creationTime": "2021-01-08T23:34:11.095870+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "f02df26b-c0ec-4829-8bef-3482d36e6230"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

Assim como há o `CreationTime` ou o `DeletionTime` para a conta, há também há um `CreationTime` ou um `DeletionTime` para a região. Esses tempos permitem que você escolha a região correta e um intervalo de tempo válido para realizar a restauração nessa região.

**Listar todas as versões de bancos de dados em uma conta de banco de dados dinâmica**

A listagem de todas as versões de bancos de dados permite que você escolha o banco de dados correto em um cenário em que o horário real da criação do banco de dados é desconhecido.

Execute o comando da CLI a seguir para listar todas as versões de bancos de dados. Esse comando só funciona em contas ativas. Os parâmetros `instanceId` e `location` são obtidos por meio das propriedades `name` e `location` na resposta do comando `az cosmosdb restorable-database-account list`. O atributo instanceId também é uma propriedade da conta do banco de dados de origem que está sendo restaurada:

```azurecli-interactive
az cosmosdb sql restorable-database list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US"
```

Agora, essa saída de comando mostra quando um banco de dados foi criado e excluído.

```json
[
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/40e93dbd-2abe-4356-a31a-35567b777220",
    ..
    "name": "40e93dbd-2abe-4356-a31a-35567b777220",
    "resource": {
      "database": {
        "id": "db1"
      },
      "eventTimestamp": "2021-01-08T23:27:25Z",
      "operationType": "Create",
      "ownerId": "db1",
      "ownerResourceId": "YuZAAA=="
    },
    ..
  },
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/243c38cb-5c41-4931-8cfb-5948881a40ea",
    ..
    "name": "243c38cb-5c41-4931-8cfb-5948881a40ea",
    "resource": {
      "database": {
        "id": "spdb1"
      },
      "eventTimestamp": "2021-01-08T23:25:25Z",
      "operationType": "Create",
      "ownerId": "spdb1",
      "ownerResourceId": "OIQ1AA=="
    },
   ..
  }
]
```

**Listar todas as versões de contêineres SQL de um banco de dados em uma conta de banco de dados ativa**

Use o comando a seguir para listar todas as versões de contêineres SQL. Esse comando só funciona em contas ativas. O parâmetro `databaseRid` é o `ResourceId` do banco de dados que você deseja restaurar. Ele é o valor do atributo `ownerResourceid` encontrado na resposta do comando `az cosmosdb sql restorable-database list`.

```azurecli-interactive
az cosmosdb sql restorable-container list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "OIQ1AA==" \
    --location "West US"
```

Essa saída de comando mostra a inclusão da lista de operações executadas em todos os contêineres dentro desse banco de dados:

```json
[
  {
    ...
    
      "eventTimestamp": "2021-01-08T23:25:29Z",
      "operationType": "Replace",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
...
  },
  {
    ...
      "eventTimestamp": "2021-01-08T23:25:26Z",
      "operationType": "Create",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
  },
]
```

**Localizar bancos de dados ou contêineres que podem ser restaurados em qualquer carimbo de data/hora fornecido**

Use o comando a seguir para obter a lista de bancos de dados ou contêineres que podem ser restaurados em qualquer carimbo de data/hora fornecido. Esse comando só funciona em contas ativas.

```azurecli-interactive

az cosmosdb sql restorable-resource list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US" \
  --restore-location "West US" \  
  --restore-timestamp "2021-01-10 T01:00:00+0000"

```

```json
[
  {
    "collectionNames": [
      "procol1",
      "procol2"
    ],
    "databaseName": "db1"
  },
  {
    "collectionNames": [
      "procol3",
       "spcol1"
    ],
    "databaseName": "spdb1"
  }
]
```

## <a name="enumerate-restorable-resources-for-mongodb-api-account"></a><a id="enumerate-mongodb-api"></a>Enumerar recursos restauráveis para a conta da API do MongoDB

Os comandos de enumeração descritos abaixo ajudam a descobrir os recursos que estão disponíveis para restauração em vários carimbos de data/hora. Além disso, eles também fornecem um feed de eventos importantes da conta restaurável, do banco de dados e dos recursos de contêiner. Assim como na API do SQL, você pode usar o comando `az cosmosdb`, mas com o `mongodb` como parâmetro, em vez de `sql`. Esses comandos só funcionam em contas ativas.

**Listar todas as versões de bancos de dados mongodb em uma conta de banco de dados ativa**

```azurecli-interactive
az cosmosdb mongodb restorable-database list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US"
```

**Listar todas as versões das coleções mongodb de um banco de dados em uma conta de banco de dados ativa**

```azurecli-interactive
az cosmosdb mongodb restorable-collection list \
    --instance-id "<InstanceID>" \
    --database-rid "AoQ13r==" \
    --location "West US"
```

**Listar todos os recursos de uma conta de banco de dados mongodb que estão disponíveis para restauração em um determinado carimbo de data/hora e região**

```azurecli-interactive
az cosmosdb mongodb restorable-resource list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US" \
    --restore-location "West US" \
    --restore-timestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>Próximas etapas

* Configure e gerencie o backup contínuo usando o [portal do Azure](continuous-backup-restore-portal.md), o [PowerShell](continuous-backup-restore-powershell.md) ou o [Azure Resource Manager](continuous-backup-restore-template.md).
* [Modelo de recurso do modo de backup contínuo](continuous-backup-restore-resource-model.md)
* [Gerencie as permissões](continuous-backup-restore-permissions.md) necessárias para restaurar dados com o modo de backup contínuo.
