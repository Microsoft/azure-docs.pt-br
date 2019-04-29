---
title: Criar um espaço de trabalho do Databricks do Azure em uma rede Virtual
description: Este artigo descreve como implantar o Azure Databricks em sua rede virtual.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 295b64b10f9f78ca6224d60fb84c6d1310aaa42e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770482"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-a-virtual-network"></a>Início Rápido: Criar um espaço de trabalho do Databricks do Azure em uma rede Virtual

Neste início rápido mostra como criar um espaço de trabalho do Databricks do Azure em uma rede virtual. Você também criará um cluster do Apache Spark dentro desse espaço de trabalho.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No portal do Azure, selecione **criar um recurso** > **Networking** > **rede Virtual**.

2. Sob **criar rede virtual**, aplique as seguintes configurações: 

    |Configuração|Valor sugerido|DESCRIÇÃO|
    |-------|---------------|-----------|
    |NOME|databricks-quickstart|Selecione um nome para sua rede virtual.|
    |Espaço de endereço|10.1.0.0/16|Intervalo de endereços da rede virtual na notação CIDR.|
    |Assinatura|\<Sua assinatura\>|Selecione a assinatura do Azure que você deseja usar.|
    |Grupo de recursos|databricks-quickstart|Selecione **criar novo** e insira um novo nome de grupo de recursos para sua conta.|
    |Local padrão|\<Selecione a região mais próxima de seus usuários\>|Selecione uma localização geográfica onde você pode hospedar sua rede virtual. Use a localização mais próxima dos usuários.|
    |Nome da sub-rede|padrão|Selecione um nome para a sub-rede padrão em sua rede virtual.|
    |Intervalo de endereços da sub-rede|10.1.0.0/24|O intervalo de endereços da sub-rede na notação CIDR. Ele deve estar contido no espaço de endereço da rede virtual. O intervalo de endereços de uma sub-rede que está em uso não pode ser editado.|

    ![Criar uma rede virtual no portal do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Depois que a implantação for concluída, navegue até sua rede virtual e selecione **espaço de endereço** sob **configurações**. Na caixa que diz *adicionar intervalo de endereços adicional*, insira `10.179.0.0/16` e selecione **salvar**.

    ![Espaço de endereço de rede virtual do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Criar um workspace do Azure Databricks

1. No portal do Azure, selecione **criar um recurso** > **Analytics** > **Databricks**.

2. Sob **serviço do Azure Databricks**, aplique as seguintes configurações:

    |Configuração|Valor sugerido|DESCRIÇÃO|
    |-------|---------------|-----------|
    |Nome do workspace|databricks-quickstart|Selecione um nome para seu espaço de trabalho do Databricks do Azure.|
    |Assinatura|\<Sua assinatura\>|Selecione a assinatura do Azure que você deseja usar.|
    |Grupo de recursos|databricks-quickstart|Selecione o mesmo grupo de recursos que você usou para a rede virtual.|
    |Local padrão|\<Selecione a região mais próxima de seus usuários\>|Escolha o mesmo local que sua rede virtual.|
    |Camada de preços|Escolha entre Standard ou Premium.|Para obter mais informações sobre tipos de preço, consulte o [página de preços do Databricks](https://azure.microsoft.com/pricing/details/databricks/).|
    |Implantar espaço de trabalho do Databricks do Azure em sua rede Virtual|Sim|Essa configuração permite que você implante um espaço de trabalho do Databricks do Azure em sua rede virtual.|
    |Rede Virtual|databricks-quickstart|Selecione a rede virtual que você criou na seção anterior.|
    |Nome da sub-rede pública|público-subrede|Use o nome da sub-rede pública padrão.|
    |Intervalo de CIDR da sub-rede pública|10.179.64.0/18|Intervalo CIDR para essa sub-rede deve estar entre /18 e /26.|
    |Nome da sub-rede privada|private-subnet|Use o nome de sub-rede privada padrão.|
    |Intervalo de CIDR da sub-rede privada|10.179.0.0/18|Intervalo CIDR para essa sub-rede deve estar entre /18 e /26.|

    ![Criar um espaço de trabalho do Azure Databricks no portal do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. Depois que a implantação for concluída, navegue até o recurso do Azure Databricks. Observe que o emparelhamento de rede virtual está desabilitado. Além disso, observe o grupo de recursos e o grupo de recursos gerenciado na página de visão geral. 

    ![Visão geral do Azure Databricks no portal do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    O grupo de recursos gerenciado contém o local físico da conta de armazenamento (DBFS), trabalho-sg (grupo de segurança de rede), os trabalhadores-vnet (rede virtual). Também é o local onde as máquinas virtuais, o disco, o endereço IP e o adaptador de rede serão criados. Esse grupo de recursos está bloqueado por padrão. No entanto, quando um cluster é iniciado na rede virtual, uma Interface de rede é criada entre a rede virtual trabalhadores no grupo de recursos gerenciado e a rede virtual "hub".

    ![Grupo de recursos gerenciados do Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

## <a name="create-a-cluster"></a>Criar um cluster

> [!NOTE]
> Para usar uma conta gratuita para criar o cluster do Azure Databricks, antes de criar o cluster, vá até o seu perfil e altere sua assinatura para **pré-pago**. Para saber mais, confira [Conta gratuita do Azure](https://azure.microsoft.com/free/).

1. Retorne ao seu serviço do Azure Databricks e selecione **inicializar espaço de trabalho** sobre o **visão geral** página.

2. Selecione **Clusters** > **+ criar Cluster**. Em seguida, crie um nome de cluster, como *databricks-guia de início rápido-cluster*e aceite as configurações padrão restantes. Selecione **Criar Cluster**.

    ![Criar cluster do Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. Depois que o cluster está em execução, retorne ao grupo de recursos gerenciado no portal do Azure. Observe as novas máquinas virtuais, discos, endereço IP e interfaces de rede. Uma interface de rede é criada em cada uma das sub-redes com endereços IP públicas e privadas.  

    ![Grupo de recursos gerenciados do Azure Databricks após a criação do cluster](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Retorne ao seu espaço de trabalho do Databricks do Azure e selecione o cluster que você criou. Em seguida, navegue até a **executores** guia o **IU do Spark** página. Observe que os endereços para o driver e executores estão no intervalo de sub-rede privada. Neste exemplo, o driver é 10.179.0.6 e executores são 10.179.0.4 e 10.179.0.5. Seus endereços IP podem ser diferentes.

    ![Executores de interface do usuário do Databricks Spark do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Limpar recursos

Depois de terminar o artigo, você poderá encerrar o cluster. Para isso, no workspace do Azure Databricks, no painel esquerdo, selecione **Clusters**. No cluster que deseja encerrar, mova o cursor sobre o botão de reticências na coluna **Ações** e selecione o ícone **Terminar**. Isso interrompe o cluster.

Se você não encerrar o cluster manualmente, ele será interrompido automaticamente, desde que você tenha selecionado a caixa de seleção **Terminar depois de \_\_ minutos de inatividade** ao criar o cluster. Nesse caso, o cluster é interrompido automaticamente se ficar inativo durante o tempo especificado.

Se você não quiser reutilizar o cluster, você pode excluir o grupo de recursos que você criou no portal do Azure.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um cluster Spark no Azure Databricks que você implantou a uma rede virtual. Avance para o próximo artigo para saber como consultar um contêiner do Docker do Linux do SQL Server na rede virtual usando o JDBC de um bloco de anotações do Azure Databricks.  

> [!div class="nextstepaction"]
>[Consulta de um contêiner de Docker do Linux do SQL Server em uma rede virtual de um bloco de anotações do Azure Databricks](vnet-injection-sql-server.md)
