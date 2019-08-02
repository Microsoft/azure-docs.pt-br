---
title: Executar experimentos e inferências em uma rede virtual
titleSuffix: Azure Machine Learning service
description: Execute experimentos de aprendizado de máquina e inferência com segurança dentro de uma rede virtual do Azure. Saiba como criar destinos de computação para treinamento do modelo e como inferir dentro de uma rede virtual. Saiba mais sobre os requisitos para redes virtuais seguras, como exigir portas de entrada e saída.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 07/10/2019
ms.openlocfilehash: 412eaac2f82a6d09761dcac53192916df215831f
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358780"
---
# <a name="securely-run-experiments-and-inference-inside-an-azure-virtual-network"></a>Executar experimentos e inferências com segurança dentro de uma rede virtual do Azure

Neste artigo, você aprenderá a executar seus experimentos e a inferência dentro de uma rede virtual. Uma rede virtual atua como um limite de segurança, isolando os recursos do Azure da Internet pública. Além disso, é possível ingressar em uma rede virtual do Azure na rede local. Ele permite treinar seus modelos com segurança e acessar seus modelos implantados para inferência. A inferência, ou a Pontuação do modelo, é a fase em que o modelo implantado é usado para previsão, mais comumente em dados de produção.

O Serviço do Azure Machine Learning depende de outros serviços do Azure para recursos de computação. Os recursos de computação (destinos de computação) são usados para treinar e implantar modelos. Esses destinos de computação podem ser criados dentro de uma rede virtual. Por exemplo, é possível usar a Máquina Virtual de Ciência de Dados para treinar um modelo e implantar o modelo no AKS (Serviço de Kubernetes do Azure). Para obter mais informações sobre redes virtuais, consulte a [visão geral da rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="prerequisites"></a>Pré-requisitos

Este documento pressupõe que você esteja familiarizado com redes virtuais do Azure e rede IP em geral. Este documento também pressupõe que você criou uma rede virtual e uma sub-rede para usar com seus recursos de computação. Se você não estiver familiarizado com as redes virtuais do Azure, leia os seguintes artigos para saber mais sobre o serviço:

* [Endereçamento IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)
* [Grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview)
* [Início Rápido: Criar uma rede virtual](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
* [Filtrar tráfego](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="storage-account-for-your-workspace"></a>Conta de armazenamento para o seu espaço de trabalho

> [!IMPORTANT]
> A __conta de armazenamento padrão__ para seu serviço de Azure Machine Learning pode ser colocada em uma rede virtual __somente durante__a experimentação.
>
> Para __contas de armazenamento não padrão para experimentação__ou se você estiver usando uma conta de armazenamento para __inferência__, deverá ter __acesso irrestrito à conta de armazenamento__.
> 
> Se você não tiver certeza se modificou essas configurações, consulte __Alterar a regra de acesso de rede padrão__ em [Configurar redes virtuais e firewalls do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security). Use as etapas para permitir o acesso de todas as redes durante a inferência ou a pontuação de modelo.

Para usar Coloque a conta de armazenamento do Azure padrão para o espaço de trabalho em uma rede virtual, use as seguintes etapas:

1. Criar uma computação de experimentação ex. Computação do Machine Learning atrás de uma rede virtual ou anexar uma computação de experimentação ao espaço de trabalho ex. Cluster HDInsight ou máquina virtual. Para obter mais informações, consulte as seções [usar computação do Machine Learning](#use-machine-learning-compute) e [usar uma máquina virtual ou cluster HDInsight](#use-a-virtual-machine-or-hdinsight-cluster) neste documento
2. Vá para o armazenamento anexado ao espaço de trabalho. ![Imagem do portal do Azure mostrando o armazenamento do Azure que está anexado ao espaço de trabalho do serviço de Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)
3. Na página armazenamento do Azure, selecione __firewalls e redes virtuais__. ![Imagem da seção portal do Azure mostrando firewalls e redes virtuais na página de armazenamento do Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)
4. Na página __firewalls e redes virtuais,__ selecione as seguintes entradas:
    - Selecione __Redes selecionadas__.
    - Em __redes virtuais__, selecione __Adicionar rede virtual existente__ para adicionar a rede virtual em que a computação de experimentação reside. (Consulte a etapa 1.)
    - Selecione __permitir que os serviços confiáveis da Microsoft acessem esta conta de armazenamento__.
![Imagem do portal do Azure mostrando a página firewalls e redes virtuais no armazenamento do Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png) 

5. Ao executar o experimento, no código de experimentação, altere a configuração de execução para usar o armazenamento de BLOBs:
    ```python
    run_config.source_directory_data_store = "workspaceblobstore"
    ```
    
## <a name="key-vault-for-your-workspace"></a>Cofre de chaves para seu espaço de trabalho
Key Vault instância associada ao espaço de trabalho é usada pelo serviço Azure Machine Learning para armazenar credenciais de vários tipos:
* A cadeia de conexão da conta de armazenamento associada
* Senhas para instâncias do repositório de contêiner do Azure
* Cadeias de conexão para armazenamentos de dados. 

Para usar Azure Machine Learning recursos de experimentação com Key Vault por trás de uma rede virtual, siga as etapas abaixo:
1. Vá para o Key Vault associado ao espaço de trabalho. ![Imagem do portal do Azure mostrando Key Vault associado ao espaço de trabalho do serviço de Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)
2. Na página Key Vault, selecione firewalls e a seção __redes virtuais__ . ![Imagem da seção portal do Azure mostrando firewalls e redes virtuais na página Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)
3. Na página __firewalls e redes virtuais,__ selecione as seguintes entradas:
    - Selecione __Redes selecionadas__.
    - Em __redes virtuais__, selecione __Adicionar redes virtuais existentes__ para adicionar a rede virtual em que a computação de experimentação reside.
    - Selecione __permitir que os serviços confiáveis da Microsoft ignorem esse firewall__.
![Imagem da página portal do Azure mostrando firewalls e redes virtuais em Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png) 


## <a name="use-machine-learning-compute"></a>Usar a Computação do Machine Learning

Para usar a Computação do Azure Machine Learning em uma rede virtual, use as seguintes informações sobre requisitos de rede:

- A rede virtual deve estar na mesma assinatura e região que o espaço de trabalho do Serviço do Azure Machine Learning.

- A sub-rede especificada para o cluster da Computação do Machine Learning deve ter endereços IP não atribuídos suficientes para acomodar o número de VMs destinadas ao cluster. Se a sub-rede não tem endereços IP não atribuídos suficientes, o cluster é alocado parcialmente.

- Se você planeja proteger a rede virtual restringindo o tráfego, deixe algumas portas abertas para o serviço de Computação do Machine Learning. Para obter mais informações, consulte [Portas necessárias](#mlcports).

- Verifique se as políticas de segurança, os bloqueios na assinatura da rede virtual ou o grupo de recursos restringem permissões para gerenciar a rede virtual.

- Se você for colocar vários clusters de Computação do Machine Learning em uma rede virtual, talvez seja necessário solicitar um aumento de cota para um ou mais de seus recursos.

    A Computação do Machine Learning aloca automaticamente recursos de rede adicionais no grupo de recursos que contém a rede virtual. Para cada cluster de Computação do Machine Learning, o Serviço do Azure Machine Learning aloca os seguintes recursos:

    - Um grupo de segurança de rede (NSG)

    - Um endereço IP público

    - Um balanceador de carga

  Esses recursos são limitados pelas [cotas de recursos](https://docs.microsoft.com/azure/azure-subscription-service-limits) da assinatura.

### <a id="mlcports"></a> Portas obrigatórias

Atualmente, a Computação do Machine Learning usa o serviço de Lote do Azure para provisionar VMs na rede virtual especificada. A sub-rede deve permitir a comunicação de entrada do serviço Lote. Essa comunicação é usada para agendar execuções nos nós de Computação do Machine Learning e para comunicação com o Armazenamento do Azure e outros recursos. O Lote adiciona NSGs no nível de NICs (adaptadores de rede) que estão anexadas às VMs. Esses NSGs configuraram automaticamente as regras de entrada e saída para permitir o tráfego a seguir:

- Tráfego TCP de entrada nas portas 29876 e 29877 de uma __marca de serviço__ de __BatchNodeManagement__.

    ![Imagem do portal do Azure mostrando uma regra de entrada usando a marca de serviço BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)
 
- adicional Tráfego TCP de entrada na porta 22 para permitir o acesso remoto. Essa porta só será necessária se você quiser se conectar usando SSH no IP público.
 
- Tráfego de saída em qualquer porta para a rede virtual.

- Tráfego de saída em qualquer porta para a internet. 

Tenha cuidado se você for modificar ou adicionar regras de entrada/saída nos NSGs configurados para Lote. Se um NSG bloquear a comunicação com os nós de computação, os serviços de Computação do Machine Learning definem o estado dos nós de computação como inutilizáveis.

Não é necessário especificar NSGs no nível de sub-rede porque o Lote configura seus próprios NSGs. No entanto, se a sub-rede especificada tiver associado NSGs e/ou um firewall, configure as regras de segurança de entrada e saída conforme mencionado anteriormente. 

A captura de tela a seguir mostra como a configuração da regra NSG procura na portal do Azure:

![Captura de tela de regras de NSG de entrada para Computação do Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Captura de tela de regras de NSG de saída para Computação do Machine Learning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a>Limitando a conectividade de saída da rede virtual

Se você não quiser usar as regras de saída padrão e desejar limitar o acesso de saída de sua rede virtual, siga as etapas abaixo:

- Negar conexão de Internet de saída usando as regras NSG 

- Limite o tráfego de saída para o armazenamento do Azure (usando a __marca de serviço__ de __Storage. Region_Name__ ex. Storage. Eastus), registro de contêiner do Azure (usando a __marca de serviço__ de __AzureContainerRegistry. Region_Name__ ex. AzureContainerRegistry. Eastus) e serviço de Azure Machine Learning (usando a __marca de serviço__ de __AzureMachineLearning__)

A captura de tela a seguir mostra como a configuração da regra NSG procura na portal do Azure:

![Captura de tela de regras de NSG de saída para Computação do Machine Learning](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)

### <a name="user-defined-routes-for-forced-tunneling"></a>Rotas definidas pelo usuário para o túnel forçado

Se você estiver usando o túnel forçado com Azure Machine Learning computação, deverá adicionar [UDR (rotas definidas pelo usuário)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) à sub-rede que contém o recurso de computação.

* Uma rota definida pelo usuário para cada endereço IP usado pelo serviço de lote do Azure na região onde os recursos existem. Esses UDRs permitem que o serviço de lote se comunique com nós de computação para o agendamento de tarefas. Para obter uma lista dos endereços IP do serviço de lote, entre em contato com o suporte do Azure.

* O tráfego de saída para o armazenamento do Azure (especificamente, `<account>.table.core.windows.net`as `<account>.queue.core.windows.net`URLs do `<account>.blob.core.windows.net`formulário, e) não deve ser bloqueado pelo dispositivo de rede local.

Quando você adiciona as rotas definidas pelo usuário, defina a rota para cada prefixo de endereço IP do lote relacionado e defina o __tipo do próximo salto__ como __Internet__. A imagem a seguir mostra um exemplo desse UDR no portal do Azure:

![Rota definida pelo usuário de exemplo para um prefixo de endereço](./media/how-to-enable-virtual-network/user-defined-route.png)

Para obter mais informações, consulte o artigo [criar um pool do lote do Azure em uma rede virtual](../../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling) .

### <a name="create-machine-learning-compute-in-a-virtual-network"></a>Criar Computação do Machine Learning em uma rede virtual

Para criar um cluster de Computação do Machine Learning usando o portal do Azure, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com), selecione o workspace de Serviço do Azure Machine Learning.

1. Na seção __Aplicativo__, selecione __Computação__. Em seguida, selecione __Adicionar computação__. 

    ![Como adicionar uma computação ao Serviço do Azure Machine Learning](./media/how-to-enable-virtual-network/add-compute.png)

1. Para configurar esse recurso de computação para usar uma rede virtual, use estas opções:

    - __Configuração de rede__: Selecione __Avançado__.

    - __Grupo de recursos__: Selecione o grupo de recursos que contém a rede virtual.

    - __Rede virtual__: Selecione a rede virtual que contém a sub-rede.

    - __Sub-rede__: Selecione a sub-rede a ser usada.

   ![Uma captura de tela mostrando configurações da rede virtual para computação do Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Também é possível criar um cluster de Computação do Machine Learning, usando o SDK do Azure Machine Learning. O código a seguir cria um novo cluster de Computação do Machine Learning na sub-rede `default` de uma rede virtual chamada `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to complete, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

Após concluir o processo de criação, você poderá treinar o modelo usando o cluster. Para obter mais informações, consulte [Selecionar e usar um destino de computação para treinamento](how-to-set-up-training-targets.md).

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Usar uma máquina virtual ou um cluster HDInsight

Para usar uma máquina virtual ou um cluster Azure HDInsight em uma rede virtual com workspace, siga estas etapas:

> [!IMPORTANT]
> O Serviço do Azure Machine Learning dá suporte apenas a máquinas virtuais executando Ubuntu.

1. Crie um cluster de VM ou HDInsight usando o portal do Azure ou a CLI do Azure e coloque-o em uma rede virtual do Azure. Para obter mais informações, consulte um dos seguintes documentos:
    * [Criar e gerenciar redes virtuais do Azure para VMs do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Estender HDInsight usando uma rede virtual do Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network) 

1. Para permitir que o Serviço do Azure Machine Learning comunique-se com a porta SSH na VM ou no cluster, será necessário configurar uma entrada de origem para o NSG. A porta SSH geralmente é a porta 22. Para permitir o tráfego dessa origem, use as seguintes informações:

    * __Fonte__: selecione __Marca de Serviço__.

    * __Marca de serviço de origem__: Selecione __AzureMachineLearning__.

    * __Intervalos de portas de origem__: Selecione __*__ .

    * __Destino__: Selecione __Qualquer__.

    * __Intervalos de portas de destino__: Selecione __22__.

    * __Protocolo__: Selecione __Qualquer__.

    * __Ação__: selecione __Permitir__.

   ![Captura de tela das regras de entrada para fazer experimentação em um cluster HDInsight ou VM dentro de uma rede virtual](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Mantenha as regras de saída padrão para o NSG. Para obter mais informações, consulte as regras de segurança padrão em [Grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Se você não quiser usar as regras de saída padrão e desejar limitar o acesso de saída de sua rede virtual, consulte [limitando a conectividade de saída da rede virtual](#limiting-outbound-from-vnet)
    
1. Anexe a VM ou o cluster do HDInsight ao seu espaço de trabalho do Serviço do Azure Machine Learning. Para obter mais informações, consulte [Configurar destinos de computação para treinamento do modelo](how-to-set-up-training-targets.md).

## <a name="use-azure-kubernetes-service"></a>Usar o Serviço de Kubernetes do Azure

> [!IMPORTANT]
> Antes de prosseguir com as etapas, verifique os pré-requisitos e planeje o endereçamento IP do cluster. Para obter mais informações, consulte [Configurar rede avançada no Serviço de Kubernetes do Azure](https://docs.microsoft.com/azure/aks/configure-advanced-networking).
> 
>
> Mantenha as regras de saída padrão para o NSG. Para obter mais informações, consulte as regras de segurança padrão em [Grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).
>
> O Serviço de Kubernetes do Azure e a rede virtual do Azure devem estar na mesma região.

Para adicionar o Serviço de Kubernetes do Azure em uma rede virtual do workspace, siga estas etapas no portal do Azure:

1. Certifique-se de que o grupo NSG que controla a rede virtual tenha a regra de entrada habilitada para o serviço Azure Machine Learning usando a __marca de serviço__ de __AzureMachineLearning__

    ![Como adicionar uma computação ao Serviço do Azure Machine Learning](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)     
 
1. No [portal do Azure](https://portal.azure.com), selecione o workspace de Serviço do Azure Machine Learning.

1. Na seção __Aplicativo__, selecione __Computação__. Em seguida, selecione __Adicionar computação__. 

    ![Como adicionar uma computação ao Serviço do Azure Machine Learning](./media/how-to-enable-virtual-network/add-compute.png)

1. Para configurar esse recurso de computação para usar uma rede virtual, use estas opções:

    - __Configuração de rede__: Selecione __Avançado__.

    - __Grupo de recursos__: Selecione o grupo de recursos que contém a rede virtual.

    - __Rede virtual__: Selecione a rede virtual que contém a sub-rede.

    - __Sub-rede__: Escolher a sub-rede.

    - __Intervalo de endereços do Serviço de Kubernetes__: Selecione o intervalo de endereços do serviço do Kubernetes. Esse intervalo de endereços usa um intervalo IP de notação CIDR para definir os endereços IP disponíveis para o cluster. Não deverá sobrepor-se a nenhum intervalo de IP de sub-rede. Por exemplo: 10.0.0.0/16.

    - __Endereço IP do serviço DNS do Kubernetes__: selecione o endereço IP do serviço DNS do Kubernetes. Esse endereço IP é atribuído ao serviço DNS do Kubernetes. É necessário estar dentro do intervalo de endereços de serviço do Kubernetes. Por exemplo: 10.0.0.10.

    - __Endereço da ponte Docker__: selecione o endereço da ponte Docker. Esse endereço IP é atribuído à ponte Docker. Não deverá estar em nenhum intervalo de IP de sub-rede ou intervalo de endereços de serviço do Kubernetes. Por exemplo:  172.17.0.1/16.

   ![Serviço do Azure Machine Learning: Configurações de rede virtual de Computação do Machine Learning](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Verifique se o grupo NSG que controla a rede virtual tem a regra de entrada habilitada para o ponto de extremidade de Pontuação para que ele possa ser chamado de fora da rede virtual

    ![Como adicionar uma computação ao Serviço do Azure Machine Learning](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)

    > [!TIP]
    > Se você já tiver um cluster do AKS em uma rede virtual, poderá anexá-lo ao workspace. Para obter mais informações, consulte [Como implantar no AKS](how-to-deploy-to-aks.md).

Também é possível usar o **SDK do Azure Machine Learning** para adicionar o Serviço de Kubernetes do Azure a uma rede virtual. O código a seguir cria uma nova instância do Serviço de Kubernetes do Azure na sub-rede `default` de uma rede virtual nomeada `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

Quando o processo de criação for concluído, você poderá inferência/Pontuação em um cluster AKS por trás de uma rede virtual. Para obter mais informações, consulte [Como implantar no AKS](how-to-deploy-to-aks.md).

## <a name="next-steps"></a>Próximas etapas

* [Configurar ambientes de treinamento](how-to-set-up-training-targets.md)
* [Onde implantar modelos](how-to-deploy-and-where.md)
* [Implantar modelos com SSL em segurança](how-to-secure-web-service.md)

