---
title: Configurar as chaves gerenciadas pelo cliente para a API do Azure para FHIR
description: Recurso Bring Your Own Key com suporte na API do Azure para FHIR por meio do Cosmos DB
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: ginle
ms.openlocfilehash: daa71a6df0ad412823736b3ee094cfd3945af492
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220842"
---
# <a name="configure-customer-managed-keys-at-rest"></a>Configurar chaves gerenciada pelo cliente em repouso

Quando você criar uma conta da API do Azure para FHIR, os seus dados serão criptografados usando as chaves gerenciadas pela Microsoft por padrão. Agora, você pode adicionar uma segunda camada de criptografia nos dados usando a própria chave escolhida e gerenciada por conta própria.

No Azure, isso normalmente é feito usando uma chave de criptografia no Azure Key Vault do cliente. O SQL do Azure, o Armazenamento do Azure e o Cosmos DB são alguns exemplos que oferecem essa funcionalidade hoje em dia. A API do Azure para FHIR aproveita esse suporte do Cosmos DB. Ao criar uma conta, você terá a opção de especificar um URI de chave do Azure Key Vault. Essa chave será passada para o Cosmos DB quando a conta do BD for provisionada. Quando uma solicitação FHIR é feita, o Cosmos DB busca a sua chave e a usa para criptografar/descriptografar os dados. Para começar, você pode ver os seguintes links:

- [Registrar o provedor de recursos do Azure Cosmos DB para a sua assinatura do Azure](../../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Configurar sua instância do Azure Key Vault](../../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [Adicionar uma política de acesso à sua instância do Azure Key Vault](../../cosmos-db/how-to-setup-cmk.md#add-access-policy)
- [Gerar uma chave no Azure Key Vault](../../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="using-azure-portal"></a>Usando o Portal do Azure

Ao criar a conta da API do Azure para FHIR no portal do Azure, você poderá ver uma opção de configuração "Criptografia de Dados" em "Configurações do Banco de Dados" na guia "Configurações Adicionais". Por padrão, a opção de chave gerenciada pelo serviço será escolhida. 

Você pode escolher a chave no KeyPicker:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="KeyPicker":::

Ou pode especificar a sua chave do Azure Key Vault aqui selecionando a opção "Chave gerenciada pelo cliente". Você pode inserir o URI de chave aqui:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Criar a API do Azure para FHIR":::

Para contas do FHIR existentes, você pode ver a opção de criptografia de chave (chave gerenciada pelo cliente ou pelo serviço) na folha "Banco de Dados", conforme descrito abaixo. A opção de configuração não poderá ser modificada depois de ser escolhida. No entanto, você pode modificar e atualizar a sua chave.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Backup de banco de dados":::

Além disso, você pode criar uma versão da chave especificada, após a qual os seus dados serão criptografados com a nova versão sem nenhuma interrupção do serviço. Você também pode remover o acesso à chave para remover o acesso aos dados. Quando a chave estiver desabilitada, as consultas resultarão em um erro. Se a chave for habilitada novamente, as consultas terão sucesso novamente.




## <a name="using-azure-powershell"></a>Usando o PowerShell do Azure

Com o URI de chave do Azure Key Vault, você pode configurar a CMK por meio do PowerShell executando o comando do PowerShell abaixo:

```powershell
New-AzHealthcareApisService
    -Name "myService"
    -Kind "fhir-R4"
    -ResourceGroupName "myResourceGroup"
    -Location "westus2"
    -CosmosKeyVaultKeyUri "https://<my-vault>.vault.azure.net/keys/<my-key>"
```

## <a name="using-azure-cli"></a>Usando a CLI do Azure

Assim como ocorre com o método do PowerShell, você pode configurar a CMK transmitindo o URI de chave do Azure Key Vault no parâmetro `key-vault-key-uri` e executando o comando da CLI abaixo: 

```azurecli-interactive
az healthcareapis service create
    --resource-group "myResourceGroup"
    --resource-name "myResourceName"
    --kind "fhir-R4"
    --location "westus2"
    --cosmos-db-configuration key-vault-key-uri="https://<my-vault>.vault.azure.net/keys/<my-key>"

```
## <a name="using-azure-resource-manager-template"></a>Usar o modelo do Azure Resource Manager

Com o URI de chave do Azure Key Vault, você pode configurar a CMK transmitindo-a para a propriedade **keyVaultKeyUri** no objeto **properties**.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "services_myService_name": {
            "defaultValue": "myService",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.HealthcareApis/services",
            "apiVersion": "2020-03-30",
            "name": "[parameters('services_myService_name')]",
            "location": "westus2",
            "kind": "fhir-R4",
            "properties": {
                "accessPolicies": [],
                "cosmosDbConfiguration": {
                    "offerThroughput": 400,
                    "keyVaultKeyUri": "https://<my-vault>.vault.azure.net/keys/<my-key>"
                },
                "authenticationConfiguration": {
                    "authority": "https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db47",
                    "audience": "[concat('https://', parameters('services_myService_name'), '.azurehealthcareapis.com')]",
                    "smartProxyEnabled": false
                },
                "corsConfiguration": {
                    "origins": [],
                    "headers": [],
                    "methods": [],
                    "maxAge": 0,
                    "allowCredentials": false
                }
            }
        }
    ]
}
```

Além disso, implante o modelo com o seguinte script do PowerShell:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a configurar chaves gerenciadas pelo cliente inativas usando o portal do Azure, o PowerShell, a CLI e o modelo do Resource Manager. Confira a seção de perguntas frequentes do Azure Cosmos DB para encontrar outras perguntas que você pode ter: 
 
>[!div class="nextstepaction"]
>[Cosmos DB: como configurar CMK](../../cosmos-db/how-to-setup-cmk.md#frequently-asked-questions)