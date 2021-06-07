---
title: Guia de início rápido – Criar uma Instância Gerenciada do Azure para o cluster do Apache Cassandra a partir do portal do Azure
description: Este guia de início rápido mostra como criar uma Instância Gerenciada do Azure para o cluster do Apache Cassandra usando o portal do Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: cb555eefb19b5db7ed7eb0792a813c295a4bf38b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588606"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal-preview"></a>Guia de início rápido: criar uma Instância Gerenciada do Azure para o cluster do Apache Cassandra a partir do portal do Azure (versão prévia)
 
A Instância Gerenciada do Azure para Apache Cassandra fornece operações automatizadas de implantação e dimensionamento para datacenters open-source gerenciados do Apache Cassandra, acelerando cenários híbridos e reduzindo a manutenção contínua.

> [!IMPORTANT]
> A Instância Gerenciada do Azure para Apache Cassandra está atualmente em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este guia de início rápido demonstra como usar o portal do Azure para criar uma Instância Gerenciada do Azure para o cluster do Apache Cassandra.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-managed-instance-cluster"></a><a id="create-account"></a>Criar um cluster de instância gerenciada

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Na barra de pesquisa, procure **Instância Gerenciada para o Apache Cassandra** e selecione o resultado.

   :::image type="content" source="./media/create-cluster-portal/search-portal.png" alt-text="Pesquise Instância Gerenciada do Azure para o Apache Cassandra." lightbox="./media/create-cluster-portal/search-portal.png" border="true":::

1. Clique no botão **Criar Instância Gerenciada para o cluster do Apache Cassandra**.

   :::image type="content" source="./media/create-cluster-portal/create-cluster.png" alt-text="Crie o cluster." lightbox="./media/create-cluster-portal/create-cluster.png" border="true":::

1. No painel **Criar Instância Gerenciada para o Apache Cassandra**, insira os seguintes detalhes:

   * **Assinatura** – na lista suspensa, selecione sua assinatura do Azure.
   * **Grupo de recursos** – especifique se deseja criar um novo grupo de recursos ou usar um existente. Um grupo de recursos é um contêiner que mantém os recursos relacionados a uma solução do Azure. Para obter mais informações, confira o artigo de visão geral do [Grupo de Recursos do Azure](../azure-resource-manager/management/overview.md).
   * **Nome do cluster** – insira um nome para o cluster.
   * **Local** – local em que o cluster será implantado.
   * **SKU** – o tipo de SKU do cluster.
   * **Nº de nós** – número de nós em um cluster. Esses nós atuam como réplicas dos dados.
   * **Senha inicial do administrador do Cassandra** – senha usada para criar o cluster.
   * **Confirmar senha de administrador do Cassandra** – insira novamente sua senha.

    > [!NOTE]
    > Durante a versão prévia pública, você pode criar o cluster de instância gerenciada nas regiões *Leste dos EUA, Oeste dos EUA, Leste dos EUA 2, Oeste dos EUA 2, EUA Central, Centro-Sul dos EUA, Norte da Europa, Oeste da Europa, Sudeste da Ásia e Leste da Austrália*.

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="Preencha o formulário de criação do cluster." lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

1. Depois, selecione a guia **Rede**.

1. No painel **Rede**, escolha os nomes da **Rede Virtual** e da **Sub-rede**. Você pode selecionar uma Rede Virtual existente ou criar uma.

   :::image type="content" source="./media/create-cluster-portal/networking.png" alt-text="Configure os detalhes da rede." lightbox="./media/create-cluster-portal/networking.png" border="true":::

1. Caso tenha criado uma VNet na última etapa, pule para a Etapa 8. Caso tenha selecionado uma VNet existente, antes de criar o cluster, você precisará aplicar algumas permissões especiais à Rede Virtual e à sub-rede. Para fazer isso, use o comando `az role assignment create`, substituindo `<subscription ID>`, `<resource group name>`, `<VNet name>` e `<subnet name>` pelos valores apropriados:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > Os valores de `assignee` e `role` no comando anterior são valores fixos; insira-os exatamente como mencionados no comando. Deixar de fazer isso levará a erros ao criar o cluster. Se você encontrar erros ao executar este comando, talvez não tenha permissões para executá-lo; entre em contato com seu administrador para obter permissões.

1. Agora que você concluiu as etapas da rede, clique em **Revisar + criar** > **Criar**

    > [!NOTE]
    > Pode levar até 15 minutos para o cluster ser criado.

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="Resumo da revisão para criar o cluster." lightbox="./media/create-cluster-portal/review-create.png" border="true":::


1. Depois de concluir a implantação, verifique o grupo de recursos para ver o cluster de instância gerenciada recém-criado:

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="Página de visão geral após a criação do cluster." lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. Para navegar pelos nós de cluster, vá até o painel Rede Virtual que você usou para criar o cluster e abra o painel **Visão geral** para exibi-los:

   :::image type="content" source="./media/create-cluster-portal/resources.png" alt-text="Exibir os recursos de cluster." lightbox="./media/create-cluster-portal/resources.png" border="true":::


## <a name="connecting-to-your-cluster"></a>Como se conectar ao cluster

A Instância Gerenciada do Azure para o Apache Cassandra não cria nós com endereços IP públicos, portanto, para se conectar ao seu cluster recém-criado do Cassandra, você precisará criar outro recurso dentro da VNet. Esse recurso pode ser um aplicativo ou uma máquina virtual com a ferramenta de consulta open-source [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) do Apache instalada. Você pode usar um [modelo](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) para implantar uma máquina virtual do Ubuntu. Depois de implantada, use o SSH para se conectar à máquina e instale o CQLSH usando os comandos abaixo:

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

Caso não vá continuar usando esse cluster da instância gerenciada, exclua-o seguindo estas etapas:

1. No menu à esquerda do portal do Azure, selecione **Grupos de recursos**.
1. Na lista, selecione o grupo de recursos criado neste início rápido.
1. Na página **Visão geral** do grupo de recursos, selecione **Excluir grupo de recursos**.
1. Na próxima janela, insira o nome do grupo de recursos a ser excluído e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu como criar uma Instância Gerenciada do Azure para o cluster do Apache Cassandra usando o portal do Azure. Você já pode começar a trabalhar com o cluster:

> [!div class="nextstepaction"]
> [Implantar um cluster do Apache Spark gerenciado com o Azure Databricks](deploy-cluster-databricks.md)
