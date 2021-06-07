---
title: Início Rápido – Implantar um Cluster do Apache Spark Gerenciado com o Azure Databricks
description: Este início rápido mostra como implantar um cluster Apache Spark gerenciado com o Azure Databricks usando o portal do Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: fd0d5c5b73ae1fb1eae7f38a22913018555ebe11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101747294"
---
# <a name="quickstart-deploy-a-managed-apache-spark-cluster-preview-with-azure-databricks"></a>Início Rápido: implantar um Cluster do Apache Spark Gerenciado (versão prévia) com o Azure Databricks

A Instância Gerenciada do Azure para Apache Cassandra fornece operações automatizadas de implantação e dimensionamento para datacenters de software livre gerenciados do Apache Cassandra, acelerando cenários híbridos e reduzindo a manutenção contínua.

> [!IMPORTANT]
> A Instância Gerenciada do Azure para Apache Cassandra está atualmente em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este início rápido demonstra como usar o portal do Azure para criar um cluster do Apache Spark totalmente gerenciado dentro da Rede Virtual do Azure de sua Instância Gerenciada do Azure para o cluster do Apache Cassandra. Você criará o cluster do Spark no Azure Databricks. Posteriormente, você pode criar ou anexar notebooks ao cluster, ler dados de diferentes fontes de dados e analisar insights.

Você também pode aprender mais com as instruções detalhadas em [Implantando o Azure Databricks em sua Rede Virtual do Azure (Injeção de Rede Virtual)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

## <a name="create-an-azure-databricks-cluster"></a>Criar um cluster do Azure Databricks

Siga estas etapas para criar um cluster do Azure Databricks em uma Rede Virtual que tenha a Instância Gerenciada do Azure para Apache Cassandra:

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Na barra de navegação esquerda, localize **Grupos de recursos** e navegue até o grupo de recursos que contém a Rede Virtual na qual sua instância gerenciada está implantada.

1. Abra o recurso **Rede Virtual** e anote o **Espaço de endereço**:

    :::image type="content" source="./media/deploy-cluster-databricks/virtual-network-address-space.png" alt-text="Obter o espaço de endereço da Rede Virtual." border="true":::

1. No grupo de recursos, selecione **Adicionar** e pesquise pelo **Azure Databricks** no campo de pesquisa:

    :::image type="content" source="./media/deploy-cluster-databricks/databricks.png" alt-text="Pesquisar pelo Azure Databricks." border="true":::

1. Selecione **Criar** para criar uma conta do Azure Databricks:

    :::image type="content" source="./media/deploy-cluster-databricks/databricks-create.png" alt-text="Criar uma conta do Azure Databricks." border="true":::

1. Preencha os seguintes campos:

   * **Nome do workspace** – forneça um nome para seu workspace do Databricks.
   * **Região** – selecione a mesma região que sua Rede Virtual.
   * **Tipo de preço** – escolha entre Standard, Premium ou Avaliação. Para saber mais sobre essas camadas, confira [Página de preços do Databricks](https://azure.microsoft.com/pricing/details/databricks/).

    :::image type="content" source="./media/deploy-cluster-databricks/select-name.png" alt-text="Preencher o nome do workspace, a região e o tipo de preço da conta do Databricks." border="true":::

1. Em seguida, selecione a guia **Rede** e preencha os seguintes detalhes:

   * **Implantar o workspace do Azure Databricks em sua VNet (Rede Virtual)** – selecione **Sim**.
   * **Rede Virtual** – no menu suspenso, escolha a Rede Virtual na qual a instância gerenciada existe.
   * **Nome da sub-rede pública** – insira um nome para a sub-rede pública.
   * **Intervalo de CIDR de sub-rede pública** – insira um intervalo de IP para a sub-rede pública.
   * **Nome da sub-rede privada** – Insira um nome para a sub-rede privada.
   * **Intervalo de CIDR de sub-rede privada** – insira um intervalo de IP para a sub-rede privada.

   Para evitar colisões de intervalos, selecione intervalos mais altos. Se necessário, use uma [calculadora de sub-rede visual](https://www.fryguy.net/wp-content/tools/subnets.html) para dividir os intervalos:

   :::image type="content" source="./media/deploy-cluster-databricks/subnet-calculator.png" alt-text="Usar a calculadora de sub-rede da Rede Virtual." border="true":::

   A seguinte captura de tela mostra detalhes de exemplo no painel rede:

   :::image type="content" source="./media/deploy-cluster-databricks/subnets.png" alt-text="Especificar nomes de sub-rede pública e privada." border="true":::

1. Selecione **Examinar e criar** e escolha **Criar** para implantar o workspace.

1. **Inicie o workspace** após ele ser criado.

1. Você é redirecionado para o portal do Azure Databricks. No portal, selecione **Novo Cluster**.

1. No painel **Novo cluster**, aceite os valores padrão para todos os campos, exceto pelos seguintes:

   * **Nome do Cluster** – insira um nome para o cluster.
   * **Versão do Databricks Runtime** – selecione Scala 2.11 ou a versão inferior com suporte do Conector do Cassandra.

    :::image type="content" source="./media/deploy-cluster-databricks/spark-cluster.png" alt-text="Selecionar a versão do Databricks Runtime e o Cluster do Spark." border="true":::

1. Expanda **Opções avançadas** e adicione a configuração a seguir. Substitua os IPs e as credenciais do nó:

    ```java
    spark.cassandra.connection.host <node1 IP>,<node 2 IP>, <node IP>
    spark.cassandra.auth.password cassandra
    spark.cassandra.connection.port 9042
    spark.cassandra.auth.username cassandra
    spark.cassandra.connection.ssl.enabled true
    ```

1. Na guia **Bibliotecas**, instale a versão mais recente do conector do Spark para Cassandra (*spark-cassandra-connector*) e reinicie o cluster:

    :::image type="content" source="./media/deploy-cluster-databricks/connector.png" alt-text="Instalar o conector do Cassandra." border="true":::

## <a name="clean-up-resources"></a>Limpar os recursos

Caso não vá continuar usando esse cluster da instância gerenciada, exclua-o seguindo estas etapas:

1. No menu à esquerda do portal do Azure, selecione **Grupos de recursos**.
1. Na lista, selecione o grupo de recursos criado neste início rápido.
1. Na página **Visão geral** do grupo de recursos, selecione **Excluir grupo de recursos**.
3. Na próxima janela, insira o nome do grupo de recursos a ser excluído e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar um cluster do Apache Spark totalmente gerenciado dentro da Rede Virtual da sua Instância Gerenciada do Azure para o cluster do Apache Cassandra. Em seguida, você pode aprender a gerenciar os recursos do cluster e do datacenter: 

> [!div class="nextstepaction"]
> [Gerenciar recursos da Instância Gerenciada do Azure para Apache Cassandra usando a CLI do Azure](manage-resources-cli.md)

