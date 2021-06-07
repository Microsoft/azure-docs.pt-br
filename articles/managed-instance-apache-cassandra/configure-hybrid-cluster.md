---
title: Início Rápido – Configurar um cluster híbrido com a Instância Gerenciada do Azure para Apache Cassandra
description: Este início rápido mostra como configurar um cluster híbrido com a Instância Gerenciada do Azure para Apache Cassandra.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: b022bff9db87c248881cd18cc21569aaef8f404a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562113"
---
# <a name="quickstart-configure-a-hybrid-cluster-with-azure-managed-instance-for-apache-cassandra-preview"></a>Início Rápido: configurar um cluster híbrido com a Instância Gerenciada do Azure para Apache Cassandra (versão prévia)

A Instância Gerenciada do Azure para Apache Cassandra fornece operações automatizadas de implantação e dimensionamento para datacenters Apache Cassandra de software livre gerenciados. Esse serviço ajuda você a acelerar cenários híbridos e a reduzir a manutenção contínua.

> [!IMPORTANT]
> A Instância Gerenciada do Azure para Apache Cassandra está atualmente em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este início rápido demonstra como usar os comandos da CLI do Azure para configurar um cluster híbrido. Se tiver datacenters existentes em um ambiente local ou auto-hospedado, você poderá usar a Instância Gerenciada do Azure para Apache Cassandra para adicionar outros datacenters a esse cluster e mantê-los.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Este artigo exige a CLI do Azure versão 2.12.1 ou posterior. Se você está usando o Azure Cloud Shell, a última versão já está instalada.

* [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md) com conectividade com o ambiente local ou auto-hospedado. Para saber mais sobre como conectar ambientes locais ao Azure, confira o artigo [Conectar uma rede local ao Azure](/azure/architecture/reference-architectures/hybrid-networking/).

## <a name="configure-a-hybrid-cluster"></a><a id="create-account"></a>Configurar um cluster híbrido

1. Entre no [portal do Azure](https://portal.azure.com/) e navegue até recurso de Rede Virtual.

1. Abra a guia **Sub-redes** e crie uma sub-rede. Para saber mais sobre os campos no formulário **Adicionar sub-rede**, confira o artigo sobre a [Rede Virtual](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet):

   :::image type="content" source="./media/configure-hybrid-cluster/subnet.png" alt-text="Adicionar uma sub-rede à Rede Virtual." lightbox="./media/configure-hybrid-cluster/subnet.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/subnet.png) -->

1. Agora, aplicaremos algumas permissões especiais à VNet e à sub-rede, exigidas pela Instância Gerenciada do Cassandra, usando a CLI do Azure. Use o comando `az role assignment create`, substituindo `<subscription ID>`, `<resource group name>`, `<VNet name>` e `<subnet name>` pelos valores apropriados:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > Os valores `assignee` e `role` no comando anterior são identificadores de função e de princípio de serviço fixos, respectivamente.

1. Em seguida, configuraremos os recursos para nosso cluster híbrido. Como você já tem um cluster, o nome do cluster aqui será apenas um recurso lógico para identificar o nome do cluster existente. Use o nome do cluster existente ao definir as variáveis `clusterName` e `clusterNameOverride` no script a seguir. Você precisa também dos nós semente, dos certificados do cliente público (se tiver configurado uma chave pública/privada no ponto de extremidade do Cassandra) e dos certificados gossip do cluster existente.

   > [!NOTE]
   > O valor da variável `delegatedManagementSubnetId` que você fornecerá abaixo é exatamente o mesmo que o valor `--scope` fornecido no comando acima:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster-legal-name'
   clusterNameOverride='cassandra-hybrid-cluster-illegal-name'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>'
    
   # You can override the cluster name if the original name is not legal for an Azure resource:
   # overrideClusterName='ClusterNameIllegalForAzureResource'
   # the default cassandra version will be v3.11
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --external-seed-nodes 10.52.221.2,10.52.221.3,10.52.221.4
      --client-certificates 'BEGIN CERTIFICATE-----\n...PEM format..\n-----END CERTIFICATE-----','BEGIN CERTIFICATE-----\n...PEM format...\n-----END CERTIFICATE-----' \
      --external-gossip-certificates 'BEGIN CERTIFICATE-----\n...PEM format 1...\n-----END CERTIFICATE-----','BEGIN CERTIFICATE-----\n...PEM format 2...\n-----END CERTIFICATE-----'
   ```

    > [!NOTE]
    > Você deve saber onde seus certificados público e/ou gossip existentes são mantidos. Se não tiver certeza, execute `keytool -list -keystore <keystore-path> -rfc -storepass <password>` para imprimir os certificados. 

1. Após a criação do recurso de cluster, execute o seguinte comando para obter os detalhes de configuração do cluster:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
    
   az managed-cassandra cluster show \
       --cluster-name $clusterName \
       --resource-group $resourceGroupName \
   ```

1. O comando anterior retorna informações sobre o ambiente da instância gerenciada. Você precisará dos certificados gossip para que possa instalá-los nos nós do datacenter existente. A seguinte captura de tela mostra a saída do comando anterior e o formato dos certificados:

   :::image type="content" source="./media/configure-hybrid-cluster/show-cluster.png" alt-text="Obter os detalhes do certificado do cluster." lightbox="./media/configure-hybrid-cluster/show-cluster.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-cluster.png) -->

1. Em seguida, crie um datacenter no cluster híbrido. Substitua os valores das variáveis pelos detalhes do cluster:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter create \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName \
       --data-center-location $dataCenterLocation \
       --delegated-subnet-id $delegatedManagementSubnetId \
       --node-count 9 
   ```

1. Agora que o datacenter foi criado, execute o comando de mostrar o datacenter para exibir seus detalhes:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
    
   az managed-cassandra datacenter show \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName 
   ```

1. A saída do comando anterior são os nós semente do novo datacenter. Adicione os nós semente do novo datacenter à configuração do datacenter existente no arquivo *cassandra.yaml*. E instale os certificados gossip da instância gerenciada que você coletou anteriormente:

   :::image type="content" source="./media/configure-hybrid-cluster/show-datacenter.png" alt-text="Obter detalhes do datacenter." lightbox="./media/configure-hybrid-cluster/show-datacenter.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-datacenter.png) -->

    > [!NOTE]
    > Se quiser adicionar mais datacenters, você poderá repetir as etapas acima, mas só precisará dos nós semente. 

1. Por fim, use a seguinte consulta CQL para atualizar a estratégia de replicação em cada keyspace para incluir todos os datacenters do cluster:

   ```bash
   ALTER KEYSPACE "ks" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3};
   ```
   Você também precisa atualizar as tabelas de senhas:

   ```bash
    ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3}
   ```

## <a name="troubleshooting"></a>Solução de problemas

Se você encontrar um erro ao aplicar permissões à Rede Virtual, como *Não é possível localizar o usuário ou entidade de serviço no banco de dados de grafo para 'e5007d2c-4b13-4a74-9b6a-605d99f03501'* , poderá aplicar a mesma permissão manualmente no portal do Azure. Para aplicar permissões do portal, vá para o painel **Controle de acesso (IAM)** da sua rede virtual existente e adicione uma atribuição de função para "Azure Cosmos DB" à função de "Administrador de Rede". Se duas entradas aparecerem quando você pesquisar por "Azure Cosmos DB", adicione as duas entradas, conforme mostrado na seguinte imagem: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Aplicar permissões" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> A atribuição de função Azure Cosmos DB é usada somente para fins de implantação. A Instância Gerenciada do Azure para Apache Cassandra não tem nenhuma dependência de back-end no Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Limpar os recursos

Caso não vá continuar usando esse cluster da instância gerenciada, exclua-o seguindo estas etapas:

1. No menu à esquerda do portal do Azure, selecione **Grupos de recursos**.
1. Na lista, selecione o grupo de recursos criado neste início rápido.
1. Na página **Visão geral** do grupo de recursos, selecione **Excluir grupo de recursos**.
3. Na próxima janela, insira o nome do grupo de recursos a ser excluído e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar um cluster híbrido usando a CLI do Azure e a Instância Gerenciada do Azure para Apache Cassandra. Você já pode começar a trabalhar com o cluster.

> [!div class="nextstepaction"]
> [Gerenciar recursos da Instância Gerenciada do Azure para Apache Cassandra usando a CLI do Azure](manage-resources-cli.md)
