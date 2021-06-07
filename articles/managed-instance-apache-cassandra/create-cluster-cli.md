---
title: Início Rápido – Usar a CLI para criar um cluster da Instância Gerenciada do Azure para Apache Cassandra
description: Use este início rápido para criar um cluster da Instância Gerenciada do Azure para Apache Cassandra usando a CLI do Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/15/2021
ms.openlocfilehash: b719310a331044df363efcc6b79be323faf49247
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562096"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli-preview"></a>Início Rápido: criar um cluster da Instância Gerenciada do Azure para Apache Cassandra usando a CLI do Azure (versão prévia)

A Instância Gerenciada do Azure para Apache Cassandra fornece operações automatizadas de implantação e dimensionamento para datacenters do Apache Cassandra de software livre gerenciados. Esse serviço ajuda você a acelerar cenários híbridos e a reduzir a manutenção contínua.

> [!IMPORTANT]
> A Instância Gerenciada do Azure para Apache Cassandra está atualmente em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este início rápido demonstra como usar comandos da CLI do Azure para criar um cluster com uma Instância Gerenciada do Azure para Apache Cassandra. Ele também mostra como criar um datacenter e escalar ou reduzir os nós verticalmente dentro do datacenter.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md) com conectividade com o ambiente local ou auto-hospedado. Para saber mais sobre como conectar ambientes locais ao Azure, confira o artigo [Conectar uma rede local ao Azure](/azure/architecture/reference-architectures/hybrid-networking/).

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!IMPORTANT]
> Este artigo exige a CLI do Azure versão 2.17.1 ou posterior. Se você está usando o Azure Cloud Shell, a última versão já está instalada.

## <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Criar um cluster de instância gerenciada

1. Entre no [Portal do Azure](https://portal.azure.com/)

1. Defina sua ID da assinatura na CLI do Azure:

   ```azurecli-interactive
   az account set -s <Subscription_ID>
   ```

1. Em seguida, crie uma Rede Virtual com uma sub-rede dedicada em seu grupo de recursos:

   ```azurecli-interactive
   az network vnet create -n <VNet_Name> -l eastus2 -g <Resource_Group_Name> --subnet-name <Subnet Name>
   ```

1. Aplique algumas permissões especiais à Rede Virtual e à sub-rede, que são exigidas pela instância gerenciada. Use o comando `az role assignment create`, substituindo `<subscription ID>`, `<resource group name>`, `<VNet name>` e `<subnet name>` pelos valores apropriados:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > Os valores de `assignee` e `role` no comando anterior são valores fixos; insira-os exatamente como mencionados no comando. Deixar de fazer isso levará a erros ao criar o cluster. Se você encontrar erros ao executar este comando, talvez não tenha permissões para executá-lo; entre em contato com seu administrador para obter permissões.

1. Em seguida, crie o cluster na Rede Virtual recém-criada usando o comando [az managed-cassandra cluster create](/cli/azure/ext/cosmosdb-preview/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_cluster_create). Execute o seguinte comando no valor da variável `delegatedManagementSubnetId`:

   > [!NOTE]
   > O valor da variável `delegatedManagementSubnetId` que você fornecerá abaixo é exatamente o mesmo que o valor `--scope` fornecido no comando acima:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --initial-cassandra-admin-password $initialCassandraAdminPassword \
      --debug
   ```

1. Por fim, crie um datacenter para o cluster, com três nós usando o comando [az managed-cassandra datacenter create](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_create):

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter create \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --data-center-location $dataCenterLocation \
      --delegated-subnet-id $delegatedManagementSubnetId \
      --node-count 3 
   ```

1. Depois que o datacenter for criado, se quiser escalar ou reduzir verticalmente os nós no datacenter, execute o comando [az managed-cassandra datacenter update](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_update). Altere o valor do parâmetro `node-count` para o valor desejado:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter update \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --node-count 9 
   ```

## <a name="connect-to-your-cluster"></a>Conectar-se ao seu cluster

A Instância Gerenciada do Azure para Apache Cassandra não cria nós com endereços IP públicos. Para se conectar ao cluster do Cassandra recém-criado, você precisa criar outro recurso dentro da rede virtual. Esse recurso pode ser um aplicativo ou uma máquina virtual com a ferramenta de consulta de software livre [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) do Apache instalada. Você pode usar um [modelo do Resource Manager](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) para implantar uma máquina virtual Ubuntu. Após a implantação, use o SSH para se conectar ao computador e instale CQLSH, conforme mostrado nos seguintes comandos:

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="troubleshooting"></a>Solução de problemas

Se você encontrar um erro ao aplicar permissões à Rede Virtual, como *Não é possível localizar o usuário ou entidade de serviço no banco de dados de grafo para 'e5007d2c-4b13-4a74-9b6a-605d99f03501'* , poderá aplicar a mesma permissão manualmente no portal do Azure. Para aplicar permissões do portal, vá para o painel **Controle de acesso (IAM)** da sua rede virtual existente e adicione uma atribuição de função para "Azure Cosmos DB" à função de "Administrador de Rede". Se duas entradas aparecerem quando você pesquisar por "Azure Cosmos DB", adicione as duas entradas, conforme mostrado na seguinte imagem: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Aplicar permissões" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> A atribuição de função Azure Cosmos DB é usada somente para fins de implantação. A Instância Gerenciada do Azure para Apache Cassandra não tem nenhuma dependência de back-end no Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, você poderá usar o comando `az group delete` para remover o grupo de recursos, a instância gerenciada e todos os recursos relacionados:

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu como criar uma Instância Gerenciada do Azure para o cluster do Apache Cassandra usando a CLI do Azure. Você já pode começar a trabalhar com o cluster:

> [!div class="nextstepaction"]
> [Implantar um cluster do Apache Spark gerenciado com o Azure Databricks](deploy-cluster-databricks.md)