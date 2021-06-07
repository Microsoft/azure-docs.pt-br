---
title: Classificar seus dados do SQL do Azure usando os rótulos do Azure Purview
description: Importar sua classificação do Azure Purview no Banco de Dados SQL do Azure e no Azure Synapse Analytics
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: azurepowershell
ms.topic: sample
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/17/2021
ms.openlocfilehash: 2eab7c535ff0c68da772e8a45ead12420734279c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101714894"
---
# <a name="classify-your-azure-sql-data-using-azure-purview-labels"></a>Classificar seus dados do SQL do Azure usando os rótulos do Azure Purview
[!INCLUDE[appliesto-sqldb-asa](../../azure-sql/includes/appliesto-sqldb-asa.md)]

Este documento descreve como adicionar rótulos do Azure Purview ao Banco de Dados SQL do Azure e ao Azure Synapse Analytics (antigo SQL DW).

## <a name="create-an-application"></a>Criar um aplicativo

1. No portal do Azure, abra o **Azure Active Directory**.
2. Em **Gerenciar**, selecione **Registro de aplicativo**.
3. Crie um aplicativo do Azure Active Directory selecionando **Novo Aplicativo**.
4. Insira um nome para seu aplicativo e selecione **Registrar**.
5. Depois que o aplicativo for criado, abra **Certificados e segredos** em **Gerenciador**.
6. Crie um segredo do cliente selecionando **Novo segredo do cliente** em **Segredos do cliente**.
7. Adicione uma descrição ao segredo do cliente, selecione um período de expiração e escolha **Adicionar**.
8. Mantenha o **valor** para uso futuro.

   > [!NOTE]
   > Depois que você fechar a página, o valor será mascarado. Você não poderá recuperar o valor do segredo do cliente se voltar à página. Será necessário gerar outro segredo do cliente.

9. Volte à página Visão geral do aplicativo recém-criado e copie os seguintes valores para uso futuro:
    1. ID do aplicativo (cliente)
    1. ID do diretório (locatário)

## <a name="provide-permissions-to-the-application"></a>Fornecer permissões ao aplicativo

1. No portal do Azure, procure **Contas do Purview**.
2. Selecione a conta do Purview em que os bancos de dados SQL e o Azure Synapse são classificados.
3. Abra **Controle de acesso (IAM)** e selecione **Adicionar**.

4. Selecione **Adicionar atribuição de função**.
5. Na seção **Função**, procure **Leitor de Dados do Purview** e selecione-o.
6. Na seção **Selecionar**, procure o aplicativo criado anteriormente, escolha-o e clique em **Salvar**.

## <a name="extract-the-classification-from-azure-purview"></a>Extrair a classificação do Azure Purview

1. Abra a sua conta do Purview e, na home page, procure o Banco de Dados SQL do Azure ou o Azure Synapse Analytics em que deseja copiar os rótulos.
2. Copie o qualifiedName em **Propriedades** e mantenha-o para uso futuro.
3. Abra o shell do PowerShell.

4. Copie um dos scripts abaixo de acordo com o tipo de ativo do SQL (Banco de Dados SQL do Azure ou Azure Synapse).
5. Preencha os parâmetros com os valores copiados acima:

   a. $TenantID: seção 1, etapa 9
   
   b. $ClientID: seção 1, etapa 9
   
   c. $SecretID: seção 1, etapa 8
   
   d. $purviewAccountName: seção 2, etapa 2
   
   e. $sqlDatabaseName: seção 3, etapa 2

6. Copie a saída do script para uso futuro.

### <a name="for-azure-sql-database"></a>Para o Banco de Dados SQL do Azure

```powershell
# Replace the values below with the relevant values for your environment
$TenantID = 'your_tenant_id'
$ClientID = 'your_client_id'
$SecretID = 'your_secret_id'
$purviewAccountName='purview_account_name'
$sqlDatabaseName="mssql://sql_server_name.database.windows.net/db_name"

###############################################################################
# Get an access accessToken, and build REST Request Header.
###############################################################################
$cmdletParameters = @{
  Method  = "POST"
  URI     = "https://login.microsoftonline.com/$TenantID/oauth2/token"
  Headers = @{
    "Content-Type" = 'application/x-www-form-urlencoded'
  }
  Body    = "client_id=$ClientID&client_secret=$SecretID&resource=https://purview.azure.net&grant_type=client_credentials"
}

$invokeResult = Invoke-RestMethod @cmdletParameters;
$accessToken = $invokeResult.access_token; 

$restRequestHeader = @{
  "authorization" = "Bearer $accessToken"
};

###############################################################################
# Get database entity.
###############################################################################
$getDatabaseEntityEntryEndpoint = "https://" + $purviewAccountName + ".catalog.purview.azure.com/api/atlas/v2/entity/uniqueAttribute/type/azure_sql_db?attr:qualifiedName=" + $sqlDatabaseName;
$cmdletParameters = @{
  Method      = "Get"
  Uri         = $getDatabaseEntityEntryEndpoint
  Headers     = $restRequestHeader
  ContentType = "application/json"        
};

$invokeResult =  Invoke-RestMethod @cmdletParameters;
$referredEntities = $invokeResult.referredEntities;
if ($null -eq $referredEntities) {
  Write-Output "No referred entities found under database entity!";
  exit;
}

###############################################################################
# Iterate database referred entities, find classified columns, and build T-SQL.
###############################################################################
foreach ($referredEntity in $referredEntities.psobject.Properties.GetEnumerator()) {
  $typeName = $referredEntity.Value.typeName;
  if ($null -eq $typeName -or $typeName -ne 'azure_sql_column') {
    continue;
  }
  
  $classifications = $referredEntity.Value.classifications;
  if ($null -eq $classifications) {
    continue;
  }
  
  foreach ($classification in $classifications.GetEnumerator())
  {
    if ($classification.typeName -notmatch 'Microsoft\.Label\.(?<labelId>.+)') { 
        continue;
    }

      $labelId = $Matches.labelId -replace "_", "-";

      $attributes = $referredEntity.Value.attributes;
      if ($null -eq $attributes) {
        continue;
      }
    
      $qualifiedName = $attributes.qualifiedName;
      if ($qualifiedName -notmatch '.+\.database\.windows\.net\/.+\/(?<schemaName>.+)\/(?<tableName>.+)#(?<columnName>.+)') {
        continue;
      }

      $schemaName = $Matches.schemaName;
      $tableName = $Matches.tableName;
      $columnName = $Matches.columnName;
      
      Write-Output "ADD SENSITIVITY CLASSIFICATION TO ${schemaName}.${tableName}.${columnName} WITH (LABEL='Azure Purview Label', LABEL_ID='${labelId}');";
  }
}
```

### <a name="for-azure-synapse-analytics"></a>Para o Azure Synapse Analytics

```powershell
# Replace the values below with the relevant values for your environment
$TenantID = 'your_tenant_id'
$ClientID = 'your_client_id'
$SecretID = 'your_secret_id'
$purviewAccountName='purview_account_name'
$dwDatabaseName="mssql://dw_server_name.database.windows.net/dw_name"

###############################################################################
# Get an access accessToken, and build REST Request Header.
###############################################################################
$cmdletParameters = @{
  Method  = "POST"
  URI     = "https://login.microsoftonline.com/$TenantID/oauth2/token"
  Headers = @{
    "Content-Type" = 'application/x-www-form-urlencoded'
  }
  Body    = "client_id=$ClientID&client_secret=$SecretID&resource=https://purview.azure.net&grant_type=client_credentials"
}

$invokeResult = Invoke-RestMethod @cmdletParameters;
$accessToken = $invokeResult.access_token; 

$restRequestHeader = @{
  "authorization" = "Bearer $accessToken"
};

###############################################################################
# Get database entity.
###############################################################################
$getDatabaseEntityEntryEndpoint = "https://" + $purviewAccountName + ".catalog.purview.azure.com/api/atlas/v2/entity/uniqueAttribute/type/azure_sql_dw?attr:qualifiedName=" + $dwDatabaseName;
$cmdletParameters = @{
  Method      = "Get"
  Uri         = $getDatabaseEntityEntryEndpoint
  Headers     = $restRequestHeader
  ContentType = "application/json"        
};

$invokeResult =  Invoke-RestMethod @cmdletParameters;
$referredEntities = $invokeResult.referredEntities;
if ($null -eq $referredEntities) {
  Write-Output "No referred entities found under database entity!";
  exit;
}

###############################################################################
# Iterate database referred entities, find classified columns, and build T-SQL.
###############################################################################
foreach ($referredEntity in $referredEntities.psobject.Properties.GetEnumerator()) {
  $typeName = $referredEntity.Value.typeName;
  if ($null -eq $typeName -or $typeName -ne 'azure_sql_dw_column') {
    continue;
  }
  
  $classifications = $referredEntity.Value.classifications;
  if ($null -eq $classifications) {
    continue;
  }
  
  foreach ($classification in $classifications.GetEnumerator())
  {
    if ($classification.typeName -notmatch 'Microsoft\.Label\.(?<labelId>.+)') { 
        continue;
    }

      $labelId = $Matches.labelId -replace "_", "-";

      $attributes = $referredEntity.Value.attributes;
      if ($null -eq $attributes) {
        continue;
      }
    
      $qualifiedName = $attributes.qualifiedName;
      if ($qualifiedName -notmatch '.+\.database\.windows\.net\/.+\/(?<schemaName>.+)\/(?<tableName>.+)#(?<columnName>.+)') {
        continue;
      }

      $schemaName = $Matches.schemaName;
      $tableName = $Matches.tableName;
      $columnName = $Matches.columnName;
      
      Write-Output "ADD SENSITIVITY CLASSIFICATION TO ${schemaName}.${tableName}.${columnName} WITH (LABEL='Azure Purview Label', LABEL_ID='${labelId}');";
  }
}
```

## <a name="run-the-t-sql-command-on-your-sql-asset"></a>Execute o comando T-SQL no ativo do SQL

1. Conecte-se ao Banco de Dados SQL do Azure ou ao Azure Synapse usando a ferramenta que preferir.
2. Execute o comando T-SQL copiado da seção anterior.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/).

Para obter mais informações sobre o Azure Purview, confira a [documentação do Azure Purview](../../purview/index.yml).