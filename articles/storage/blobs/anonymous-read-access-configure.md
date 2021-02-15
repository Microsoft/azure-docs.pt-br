---
title: Configurar acesso de leitura público anônimo para contêineres e blobs
titleSuffix: Azure Storage
description: Saiba como permitir ou impedir o acesso anônimo a dados de BLOB para a conta de armazenamento. Defina a configuração de acesso público do contêiner para tornar contêineres e blobs disponíveis para acesso anônimo.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/03/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: feac7b890c973b1541c5362f860432687082953f
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533869"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>Configurar acesso de leitura público anônimo para contêineres e blobs

O armazenamento do Azure dá suporte ao acesso de leitura público anônimo opcional para contêineres e blobs. Por padrão, o acesso anônimo aos seus dados nunca é permitido. A menos que você habilite explicitamente o acesso anônimo, todas as solicitações para um contêiner e seus BLOBs devem ser autorizadas. Quando você define a configuração de nível de acesso público de um contêiner para permitir acesso anônimo, os clientes podem ler dados nesse contêiner sem autorizar a solicitação.

> [!WARNING]
> Quando um contêiner é configurado para acesso público, qualquer cliente pode ler dados nesse contêiner. O acesso público apresenta um risco de segurança potencial, portanto, se o seu cenário não exigir, a Microsoft recomenda que você o desproíba para a conta de armazenamento. Para obter mais informações, consulte [impedir o acesso de leitura público anônimo a contêineres e blobs](anonymous-read-access-prevent.md).

Este artigo descreve como configurar o acesso de leitura público anônimo para um contêiner e seus BLOBs. Para obter informações sobre como acessar dados de blob anonimamente de um aplicativo cliente, consulte [acessar contêineres públicos e blobs anonimamente com o .net](anonymous-read-access-client.md).

## <a name="about-anonymous-public-read-access"></a>Sobre o acesso de leitura público anônimo

O acesso público aos seus dados é sempre proibido por padrão. Há duas configurações separadas que afetam o acesso público:

1. **Permitir acesso público para a conta de armazenamento.** Por padrão, uma conta de armazenamento permite que um usuário com as permissões apropriadas habilite o acesso público a um contêiner. Os dados de BLOB não estão disponíveis para acesso público, a menos que o usuário tenha a etapa adicional para definir explicitamente a configuração de acesso público do contêiner.
1. **Defina a configuração de acesso público do contêiner.** Por padrão, a configuração de acesso público de um contêiner é desabilitada, o que significa que a autorização é necessária para cada solicitação para o contêiner ou seus dados. Um usuário com as permissões apropriadas pode modificar a configuração de acesso público de um contêiner para habilitar o acesso anônimo somente se o acesso anônimo for permitido para a conta de armazenamento.

A tabela a seguir resume como ambas as configurações juntos afetam o acesso público para um contêiner.

| Configuração de acesso público | O acesso público está desabilitado para um contêiner (configuração padrão) | O acesso público para um contêiner é definido como contêiner | Acesso público um contêiner é definido como blob |
|--|--|--|--|
| O acesso público não é permitido para a conta de armazenamento | Não há acesso público a nenhum contêiner na conta de armazenamento. | Não há acesso público a nenhum contêiner na conta de armazenamento. A configuração da conta de armazenamento substitui a configuração do contêiner. | Não há acesso público a nenhum contêiner na conta de armazenamento. A configuração da conta de armazenamento substitui a configuração do contêiner. |
| O acesso público é permitido para a conta de armazenamento (configuração padrão) | Não há acesso público a esse contêiner (configuração padrão). | O acesso público é permitido para este contêiner e seus BLOBs. | O acesso público é permitido para BLOBs neste contêiner, mas não para o próprio contêiner. |

## <a name="allow-or-disallow-public-read-access-for-a-storage-account"></a>Permitir ou impedir acesso de leitura público para uma conta de armazenamento

Por padrão, uma conta de armazenamento é configurada para permitir que um usuário com as permissões apropriadas habilite o acesso público a um contêiner. Quando o acesso público é permitido, um usuário com as permissões apropriadas pode modificar a configuração de acesso público de um contêiner para habilitar o acesso público anônimo aos dados nesse contêiner. Os dados de blob nunca estão disponíveis para acesso público, a menos que o usuário tenha a etapa adicional para definir explicitamente a configuração de acesso público do contêiner.

Tenha em mente que o acesso público a um contêiner é sempre desativado por padrão e deve ser configurado explicitamente para permitir solicitações anônimas. Independentemente da configuração na conta de armazenamento, seus dados nunca estarão disponíveis para acesso público, a menos que um usuário com permissões apropriadas aceite essa etapa adicional para habilitar o acesso público no contêiner.

A despermissão de acesso público para a conta de armazenamento impede o acesso anônimo a todos os contêineres e blobs nessa conta. Quando o acesso público não é permitido para a conta, não é possível definir a configuração de acesso público para um contêiner para permitir o acesso anônimo. Para maior segurança, a Microsoft recomenda que você não permita o acesso público para suas contas de armazenamento, a menos que seu cenário exija que os usuários acessem os recursos de blob anonimamente.

> [!IMPORTANT]
> A despermissão de acesso público para uma conta de armazenamento substitui as configurações de acesso público para todos os contêineres nessa conta de armazenamento. Quando o acesso público não é permitido para a conta de armazenamento, qualquer solicitação anônima futura para essa conta falhará. Antes de alterar essa configuração, não se esqueça de entender o impacto em aplicativos cliente que podem estar acessando dados em sua conta de armazenamento anonimamente. Para obter mais informações, consulte [impedir o acesso de leitura público anônimo a contêineres e blobs](anonymous-read-access-prevent.md).

Para permitir ou impedir o acesso público para uma conta de armazenamento, configure a propriedade **AllowBlobPublicAccess** da conta. Essa propriedade está disponível para todas as contas de armazenamento que são criadas com o modelo de implantação Azure Resource Manager. Para obter mais informações, consulte [visão geral da conta de armazenamento](../common/storage-account-overview.md).

A propriedade **AllowBlobPublicAccess** não é definida para uma conta de armazenamento por padrão e não retorna um valor até que você a defina explicitamente. A conta de armazenamento permite acesso público quando o valor da propriedade é **nulo** ou **verdadeiro**.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Para permitir ou impedir o acesso público para uma conta de armazenamento no portal do Azure, siga estas etapas:

1. Navegue até sua conta de armazenamento no portal do Azure.
1. Localize a definição de **configuração** em **configurações**.
1. Defina **acesso público de blob** como **habilitado** ou **desabilitado**.

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Captura de tela mostrando como permitir ou não o acesso público de BLOB para a conta":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para permitir ou impedir o acesso público para uma conta de armazenamento com o PowerShell, instale [Azure PowerShell versão 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) ou posterior. Em seguida, configure a propriedade **AllowBlobPublicAccess** para uma conta de armazenamento nova ou existente.

O exemplo a seguir cria uma conta de armazenamento e define explicitamente a propriedade **AllowBlobPublicAccess** como **true**. Em seguida, ele atualiza a conta de armazenamento para definir a propriedade **AllowBlobPublicAccess** como **false**. O exemplo também recupera o valor da propriedade em cada caso. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with AllowBlobPublicAccess set to true (or null).
New-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -Location $location `
    -SkuName Standard_GRS
    -AllowBlobPublicAccess $false

# Read the AllowBlobPublicAccess property for the newly created storage account.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowBlobPublicAccess

# Set AllowBlobPublicAccess set to false
Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -AllowBlobPublicAccess $false

# Read the AllowBlobPublicAccess property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowBlobPublicAccess
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para permitir ou impedir o acesso público para uma conta de armazenamento com CLI do Azure, instale CLI do Azure versão 2.9.0 ou posterior. Para obter mais informações, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Em seguida, configure a propriedade **allowBlobPublicAccess** para uma conta de armazenamento nova ou existente.

O exemplo a seguir cria uma conta de armazenamento e define explicitamente a propriedade **allowBlobPublicAccess** como **true**. Em seguida, ele atualiza a conta de armazenamento para definir a propriedade **allowBlobPublicAccess** como **false**. O exemplo também recupera o valor da propriedade em cada caso. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --allow-blob-public-access true

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowBlobPublicAccess \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --allow-blob-public-access false

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowBlobPublicAccess \
    --output tsv
```

# <a name="template"></a>[Modelo](#tab/template)

Para permitir ou impedir o acesso público para uma conta de armazenamento com um modelo, crie um modelo com a propriedade **AllowBlobPublicAccess** definida como **true** ou **false**. As etapas a seguir descrevem como criar um modelo no portal do Azure.

1. Na portal do Azure, escolha **criar um recurso**.
1. Em **Pesquisar no Marketplace**, digite **implantação de modelo** e pressione **Enter**.
1. Escolha **implantação de modelo (implantar usando modelos personalizados) (versão prévia)**, escolha **criar** e, em seguida, escolha **criar seu próprio modelo no editor**.
1. No editor de modelo, Cole o JSON a seguir para criar uma nova conta e definir a propriedade **AllowBlobPublicAccess** como **true** ou **false**. Lembre-se de substituir os espaços reservados entre parênteses pelos seus próprios valores.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'template')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "allowBlobPublicAccess": false
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. Salve o modelo.
1. Especifique o parâmetro do grupo de recursos e escolha o botão **revisar + criar** para implantar o modelo e criar uma conta de armazenamento com a propriedade **allowBlobPublicAccess** configurada.

---

> [!NOTE]
> A despermissão de acesso público para uma conta de armazenamento não afeta os sites estáticos hospedados nessa conta de armazenamento. O contêiner **$Web** sempre é acessível publicamente.
>
> Depois de atualizar a configuração de acesso público para a conta de armazenamento, pode levar até 30 segundos antes que a alteração seja totalmente propagada.

Permitir ou não permitir acesso público ao blob requer a versão 2019-04-01 ou posterior do provedor de recursos de armazenamento do Azure. Para obter mais informações, consulte [API REST do provedor de recursos de armazenamento do Azure](/rest/api/storagerp/).

Os exemplos nesta seção mostraram como ler a propriedade **AllowBlobPublicAccess** da conta de armazenamento para determinar se o acesso público atualmente é permitido ou não. Para saber mais sobre como verificar se a configuração de acesso público de uma conta está configurada para impedir o acesso anônimo, consulte [corrigir acesso público anônimo](anonymous-read-access-prevent.md#remediate-anonymous-public-access).

## <a name="set-the-public-access-level-for-a-container"></a>Definir o nível de acesso público para um contêiner

Para conceder aos usuários anônimos acesso de leitura a um contêiner e seus BLOBs, primeiro permita o acesso público para a conta de armazenamento e, em seguida, defina o nível de acesso público do contêiner. Se o acesso público for negado para a conta de armazenamento, você não poderá configurar o acesso público para um contêiner.

Quando o acesso público é permitido para uma conta de armazenamento, você pode configurar um contêiner com as seguintes permissões:

- **Sem acesso de leitura público:** O contêiner e seus BLOBs podem ser acessados somente com uma solicitação autorizada. Essa opção é o padrão para todos os novos contêineres.
- **Acesso de leitura público somente para BLOBs:** Os BLOBs dentro do contêiner podem ser lidos por solicitação anônima, mas os dados do contêiner não estão disponíveis anonimamente. Os clientes não podem enumerar os blobs no contêiner.
- **Acesso de leitura público para contêiner e seus BLOBs:** Os dados de contêiner e BLOB podem ser lidos por solicitação anônima, exceto para configurações de permissões de contêiner e metadados de contêiner. Os clientes podem enumerar os blobs no contêiner por meio solicitação anônima, mas não podem enumerar os contêineres na conta de armazenamento.

Você não pode alterar o nível de acesso público para um blob individual. O nível de acesso público é definido somente no nível de contêiner. Você pode definir o nível de acesso público do contêiner ao criar o contêiner ou pode atualizar a configuração em um contêiner existente.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Para atualizar o nível de acesso público para um ou mais contêineres existentes no portal do Azure, siga estas etapas:

1. Navegue até a visão geral da sua conta de armazenamento na portal do Azure.
1. Em **serviço blob** na folha do menu, selecione **contêineres**.
1. Selecione os contêineres para os quais você deseja definir o nível de acesso público.
1. Use o botão **alterar nível de acesso** para exibir as configurações de acesso público.
1. Selecione o nível de acesso público desejado na lista suspensa **nível de acesso público** e clique no botão OK para aplicar a alteração aos contêineres selecionados.

    ![Captura de tela mostrando como definir o nível de acesso público no portal](./media/anonymous-read-access-configure/configure-public-access-container.png)

Quando o acesso público não é permitido para a conta de armazenamento, o nível de acesso público de um contêiner não pode ser definido. Se você tentar definir o nível de acesso público do contêiner, verá que a configuração está desabilitada porque o acesso público não é permitido para a conta.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="A captura de tela mostrando que a configuração do nível de acesso público do contêiner é bloqueada quando o acesso público não é permitido":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para atualizar o nível de acesso público para um ou mais contêineres com o PowerShell, chame o comando [set-AzStorageContainerAcl](/powershell/module/az.storage/set-azstoragecontaineracl) . Autorize essa operação passando sua chave de conta, uma cadeia de conexão ou uma assinatura de acesso compartilhado (SAS). A operação [definir ACL de contêiner](/rest/api/storageservices/set-container-acl) que define o nível de acesso público do contêiner não oferece suporte à autorização com o Azure AD. Para obter mais informações, consulte [permissões para chamar operações de BLOB e de dados de fila](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

O exemplo a seguir cria um contêiner com acesso público desabilitado e, em seguida, atualiza a configuração de acesso público do contêiner para permitir o acesso anônimo ao contêiner e a seus BLOBs. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```powershell
# Set variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Get context object.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

# Create a new container with public access setting set to Off.
$containerName = "<container>"
New-AzStorageContainer -Name $containerName -Permission Off -Context $ctx

# Read the container's public access setting.
Get-AzStorageContainerAcl -Container $containerName -Context $ctx

# Update the container's public access setting to Container.
Set-AzStorageContainerAcl -Container $containerName -Permission Container -Context $ctx

# Read the container's public access setting.
Get-AzStorageContainerAcl -Container $containerName -Context $ctx
```

Quando o acesso público não é permitido para a conta de armazenamento, o nível de acesso público de um contêiner não pode ser definido. Se você tentar definir o nível de acesso público do contêiner, o armazenamento do Azure retornará um erro indicando que o acesso público não é permitido na conta de armazenamento.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para atualizar o nível de acesso público para um ou mais contêineres com CLI do Azure, chame o comando [AZ Storage container Set Permission](/cli/azure/storage/container#az-storage-container-set-permission) . Autorize essa operação passando sua chave de conta, uma cadeia de conexão ou uma assinatura de acesso compartilhado (SAS). A operação [definir ACL de contêiner](/rest/api/storageservices/set-container-acl) que define o nível de acesso público do contêiner não oferece suporte à autorização com o Azure AD. Para obter mais informações, consulte [permissões para chamar operações de BLOB e de dados de fila](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

O exemplo a seguir cria um contêiner com acesso público desabilitado e, em seguida, atualiza a configuração de acesso público do contêiner para permitir o acesso anônimo ao contêiner e a seus BLOBs. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```azurecli-interactive
az storage container create \
    --name <container-name> \
    --account-name <account-name> \
    --resource-group <resource-group>
    --public-access off \
    --account-key <account-key> \
    --auth-mode key

az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key> \
    --auth-mode key

az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key

az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key> \
    --auth-mode key
```

Quando o acesso público não é permitido para a conta de armazenamento, o nível de acesso público de um contêiner não pode ser definido. Se você tentar definir o nível de acesso público do contêiner, o armazenamento do Azure retornará um erro indicando que o acesso público não é permitido na conta de armazenamento.

# <a name="template"></a>[Modelo](#tab/template)

N/D

---

## <a name="check-the-public-access-setting-for-a-set-of-containers"></a>Verificar a configuração de acesso público para um conjunto de contêineres

É possível verificar quais contêineres em uma ou mais contas de armazenamento estão configuradas para acesso público, listando os contêineres e verificando a configuração de acesso público. Essa abordagem é uma opção prática quando uma conta de armazenamento não contém um grande número de contêineres ou quando você está verificando a configuração em um pequeno número de contas de armazenamento. No entanto, o desempenho poderá ser prejudicado se você tentar enumerar um grande número de contêineres.

O exemplo a seguir usa o PowerShell para obter a configuração de acesso público para todos os contêineres em uma conta de armazenamento. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>Próximas etapas

- [Impedir acesso de leitura público anônimo a contêineres e blobs](anonymous-read-access-prevent.md)
- [Acesse contêineres públicos e blobs anonimamente com o .NET](anonymous-read-access-client.md)
- [Autorizar acesso ao Armazenamento do Microsoft Azure](../common/storage-auth.md)
