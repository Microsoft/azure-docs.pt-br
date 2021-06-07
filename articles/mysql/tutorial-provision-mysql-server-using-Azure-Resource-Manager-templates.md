---
title: 'Tutorial: Criar um Banco de Dados do Azure para MySQL – modelo do Azure Resource Manager'
description: Este tutorial explica como provisionar e automatizar as implantações de um servidor do Banco de Dados do Azure para MySQL usando um modelo do Azure Resource Manager.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: json
ms.topic: tutorial
ms.date: 12/02/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: fd5fa4e09dd3f73aa7a8f044ded04d504bd2f3dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97705465"
---
# <a name="tutorial-provision-an-azure-database-for-mysql-server-using-azure-resource-manager-template"></a>Tutorial: Provisionar um servidor do Banco de Dados do Azure para MySQL usando modelos do Azure Resource Manager

A [API REST do Banco de Dados do Azure para MySQL](/rest/api/mysql/) permite que os engenheiros de DevOps automatizem e integrem o provisionamento, a configuração e as operações de servidores MySQL e bancos de dados gerenciados no Azure.  A API permite a criação, a enumeração, o gerenciamento e a exclusão de servidores MySQL e bancos de dados no serviço Banco de Dados do Azure para MySQL.

O Azure Resource Manager aproveita a API REST subjacente para declarar e programar os recursos do Azure necessários para implantações em escala, alinhando-os com a infraestrutura como um conceito de código. O modelo parametriza a configuração de nome de recurso do Azure, de SKU, de rede e de firewall, bem como as configurações, permitindo que sejam criadas uma vez e usadas várias vezes.  Os modelos do Azure Resource Manager podem ser criados com facilidade usando o [portal do Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) ou o [Visual Studio Code](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=CLI). Eles possibilitam o empacotamento de aplicativos, a padronização e a automação da implantação, que podem ser integrados no pipeline de CI/CD de DevOps.  Por exemplo, se pretender implantar rapidamente um Aplicativo Web com o back-end do Banco de Dados do Azure para MySQL, execute a implantação de ponta a ponta usando este [modelo de Início Rápido](https://azure.microsoft.com/resources/templates/101-webapp-managed-mysql/) da galeria do GitHub.

Neste tutorial, você usará um modelo do Azure Resource Manager e outros utilitários para aprender a:

> [!div class="checklist"]
> * Criar um servidor do Banco de Dados do Azure para MySQL com o Ponto de Extremidade de Serviço de VNET usando o modelo do Azure Resource Manager
> * Use a [ferramenta de linha de comando mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) para criar um banco de dados
> * Carregar dados de exemplo
> * Consultar dados
> * Atualizar dados

## <a name="prerequisites"></a>Pré-requisitos

Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-azure-database-for-mysql-server-with-vnet-service-endpoint-using-azure-resource-manager-template"></a>Criar um servidor do Banco de Dados do Azure para MySQL com o Ponto de Extremidade de Serviço de VNET usando o modelo do Azure Resource Manager

Para obter a referência de modelo JSON para um servidor do Banco de Dados do Azure para MySQL, acesse a referência de modelo de [servidores Microsoft.DBforMySQL](/azure/templates/microsoft.dbformysql/servers). Veja abaixo o modelo JSON de exemplo que pode ser usado para criar um servidor que executa o Banco de Dados do Azure para MySQL com o Ponto de Extremidade de Serviço de VNET.
```json
{
  "apiVersion": "2017-12-01",
  "type": "Microsoft.DBforMySQL/servers",
  "name": "string",
  "location": "string",
  "tags": "string",
  "properties": {
    "version": "string",
    "sslEnforcement": "string",
    "administratorLogin": "string",
    "administratorLoginPassword": "string",
    "storageProfile": {
      "storageMB": "string",
      "backupRetentionDays": "string",
      "geoRedundantBackup": "string"
    }
  },
  "sku": {
    "name": "string",
    "tier": "string",
    "capacity": "string",
    "family": "string"
  },
  "resources": [
    {
      "name": "AllowSubnet",
      "type": "virtualNetworkRules",
      "apiVersion": "2017-12-01",
      "properties": {
        "virtualNetworkSubnetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnetName'))]",
        "ignoreMissingVnetServiceEndpoint": true
      },
      "dependsOn": [
        "[concat('Microsoft.DBforMySQL/servers/', parameters('serverName'))]"
      ]
    }
  ]
}
```
Nessa solicitação, os valores que precisam ser personalizados são:
+   `name` – Especifique o nome do Servidor MySQL (sem o nome de domínio).
+   `location` – Especifique uma região válida de data center do Azure para o Servidor MySQL. Por exemplo, westus2.
+   `properties/version` – Especifique a versão do servidor MySQL a ser implantada. Por exemplo, 5.6 ou 5.7.
+   `properties/administratorLogin` – Especifique o logon de administrador do MySQL para o servidor. O nome de entrada do administrador não pode ser azure_superuser, admin, administrator, root, guest ou public.
+   `properties/administratorLoginPassword` – Especifique a senha para o usuário administrador do MySQL especificado acima.
+   `properties/sslEnforcement` – Especifique Habilitado/Desabilitado para habilitar/desabilitar sslEnforcement.
+   `storageProfile/storageMB` – Especifique o tamanho máximo de armazenamento provisionado necessário para o servidor em megabytes. Por exemplo, 5.120.
+   `storageProfile/backupRetentionDays` – Especifique o período de retenção de backup desejado em dias. Por exemplo, 7. 
+   `storageProfile/geoRedundantBackup` – Especifique Habilitado/Desabilitado, dependendo dos requisitos de Geo-DR.
+   `sku/tier` – Especifique a camada Basic, GeneralPurpose ou MemoryOptimized para a implantação.
+   `sku/capacity` – Especifique a capacidade de vCore. Os valores possíveis incluem 2, 4, 8, 16, 32 ou 64.
+   `sku/family` – Especifique Gen5 para escolher a geração de hardware para implantação do servidor.
+   `sku/name` – Especifique TierPrefix_family_capacity. Por exemplo, B_Gen5_1, GP_Gen5_16 e MO_Gen5_32. Confira a documentação sobre [tipos de preço](./concepts-pricing-tiers.md) para entender os valores válidos por região e por tipo.
+   `resources/properties/virtualNetworkSubnetId` – Especifique o identificador do Azure da sub-rede na VNET em que o servidor MySQL do Azure deverá ser colocado. 
+   `tags(optional)` – Especifique que marcas opcionais são pares chave-valor que você usará para categorizar os recursos de cobrança etc.

Se você pretende criar um modelo do Azure Resource Manager para automatizar as implantações do Banco de Dados do Azure para MySQL para sua organização, a recomendação é começar com o [modelo do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) de exemplo na Galeria do GitHub de Início Rápido do Azure primeiro e se basear nela. 

Caso não esteja familiarizado com os modelos do Azure Resource Manager e deseje experimentá-los, comece seguindo estas etapas:
+   Clone ou baixe o [modelo do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) de exemplo na galeria de Início Rápido do Azure.  
+   Modifique o azuredeploy.parameters.json para atualizar os valores de parâmetro de acordo com sua preferência e salve o arquivo. 
+   Use a CLI do Azure para criar o servidor MySQL do Azure usando os seguintes comandos

Você pode usar o Azure Cloud Shell no navegador ou Instalar a CLI do Azure em seu próprio computador para executar os blocos de código deste tutorial.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

```azurecli-interactive
az login
az group create -n ExampleResourceGroup  -l "West US2"
az deployment group create -g $ ExampleResourceGroup   --template-file $ {templateloc} --parameters $ {parametersloc}
```

## <a name="get-the-connection-information"></a>Obter informações de conexão
Para se conectar ao servidor, é preciso fornecer credenciais de acesso e informações do host.
```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

O resultado está no formato JSON. Anote o **fullyQualifiedDomainName** e o **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus2",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
 "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-mysql"></a>Conectar-se ao servidor usando mysql
Use a [ferramenta de linha de comando do mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) para estabelecer uma conexão com o servidor do Banco de Dados do Azure para MySQL. Neste exemplo, o comando é:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Criar um banco de dados vazio
Quando você estiver conectado ao servidor, crie um banco de dados em branco.
```sql
mysql> CREATE DATABASE mysampledb;
```

No prompt, execute o seguinte comando para mudar a conexão para esse banco de dados recém-criado:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Criar tabelas no banco de dados
Agora que você sabe como se conectar ao Banco de Dados do Azure para MySQL, conclua algumas tarefas básicas.

Primeiro, crie uma tabela e carregue-a com alguns dados. Vamos criar uma tabela que armazena informações de inventário.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Carregar dados nas tabelas
Agora que você tem uma tabela, insira alguns dados nela. Na janela do prompt de comando aberta, execute a consulta a seguir para inserir algumas linhas de dados.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Agora, você tem duas linhas de dados de exemplo na tabela criada anteriormente.

## <a name="query-and-update-the-data-in-the-tables"></a>Consultar e atualizar os dados nas tabelas
Execute a seguinte consulta para recuperar as informações da tabela do banco de dados.
```sql
SELECT * FROM inventory;
```

Também é possível atualizar os dados nas tabelas.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

A linha é atualizada à medida que você recupera os dados.
```sql
SELECT * FROM inventory;
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua o grupo de recursos, que excluirá os recursos no grupo de recursos.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal do Azure](https://portal.azure.com) pesquise e selecione **Grupos de recursos**.

2. Na lista grupo de recursos, escolha o nome do seu grupo de recursos.

3. Na página **Visão geral** do grupo de recursos, selecione **Excluir grupo de recursos**.

4. Na caixa de diálogo de confirmação, digite o nome do seu grupo de recursos e, em seguida, selecione **Excluir**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a:
> [!div class="checklist"]
> * Criar um servidor do Banco de Dados do Azure para MySQL com o Ponto de Extremidade de Serviço de VNET usando o modelo do Azure Resource Manager
> * Usar a ferramenta de linha de comando do MySQL para criar um banco de dados
> * Carregar dados de exemplo
> * Consultar dados
> * Atualizar dados

> [!div class="nextstepaction"]
> [Como conectar aplicativos ao Banco de Dados do Azure para MySQL](./howto-connection-string.md)