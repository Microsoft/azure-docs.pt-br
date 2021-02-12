---
title: Usar um firewall
titleSuffix: Azure Machine Learning
description: Controle o acesso a espaços de trabalho do Azure Machine Learning com firewalls do Azure. Saiba mais sobre os hosts que você deve permitir por meio do firewall.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/18/2020
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 0fcea6a44f5379ff3da5b348ae45486be6c2516a
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831307"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Usar o espaço de trabalho por trás de um firewall para Azure Machine Learning

Neste artigo, saiba como configurar o Firewall do Azure para controlar o acesso ao seu espaço de trabalho do Azure Machine Learning e à Internet pública. Para saber mais sobre como proteger Azure Machine Learning, consulte [segurança corporativa para Azure Machine Learning](concept-enterprise-security.md).

> [!WARNING]
> O acesso ao armazenamento de dados por trás de um firewall só tem suporte em experiências de código First. Não há suporte para o uso do [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) para acessar dados por trás de um firewall. Para trabalhar com o armazenamento de dados em uma rede privada com o estúdio, primeiro você deve [Configurar uma rede virtual](../virtual-network/quick-create-portal.md) e [conceder ao estúdio acesso aos dados armazenados dentro de uma rede virtual](how-to-enable-studio-virtual-network.md).

## <a name="azure-firewall"></a>Firewall do Azure

Ao usar o Firewall do Azure, use __DNAT (conversão de endereços de rede de destino)__ para criar regras de NAT para o tráfego de entrada. Para o tráfego de saída, crie regras de __rede__ e/ou __aplicativo__ . Essas coleções de regras são descritas mais detalhadamente em [o que são alguns conceitos de firewall do Azure](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts).

### <a name="inbound-configuration"></a>Configuração de entrada

Se você usar uma __instância de computação__ ou __cluster de computação__ Azure Machine Learning, adicione uma [UDRs (rotas definidas pelo usuário)](../virtual-network/virtual-networks-udr-overview.md) para a sub-rede que contém os recursos de Azure Machine Learning. Essa rota força o tráfego __dos__ endereços IP dos `BatchNodeManagement` recursos e `AzureMachineLearning` para o IP público de sua instância de computação e cluster de computação.

Essas UDRs permitem que o serviço de Lote se comunique com os nós de computação para agendamento de tarefas. Além disso, adicione o endereço IP para o serviço de Azure Machine Learning, pois isso é necessário para acessar instâncias de computação. Ao adicionar o IP para o serviço de Azure Machine Learning, você deve adicionar o IP para as regiões __primárias e secundárias__ do Azure. A região primária sendo aquela em que seu espaço de trabalho está localizado.

Para encontrar a região secundária, confira [garantir a continuidade dos negócios & recuperação de desastre usando regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md#azure-regional-pairs). Por exemplo, se seu serviço de Azure Machine Learning estiver no leste dos EUA 2, a região secundária será EUA Central. 

Use um dos seguintes métodos para obter uma lista de endereços IP do serviço de Lote e do serviço do Azure Machine Learning:

* Baixe os [Intervalos de IP do Azure e as Marcas de serviço](https://www.microsoft.com/download/details.aspx?id=56519) e pesquise `BatchNodeManagement.<region>` e `AzureMachineLearning.<region>` no arquivo, em que `<region>` é a sua região do Azure.

* Use o [CLI do Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) para baixar as informações. O exemplo a seguir baixa as informações de endereço IP e filtra as informações da região leste dos EUA 2 (primária) e região de EUA Central (secundária):

    ```azurecli-interactive
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
    # Get primary region IPs
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
    # Get secondary region IPs
    az network list-service-tags -l "Central US" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='centralus']"
    ```

    > [!TIP]
    > Se você estiver usando as regiões US-Virgínia, US-Arizona regiões ou China-East-2, esses comandos não retornarão endereços IP. Em vez disso, use um dos links a seguir para baixar uma lista de endereços IP:
    >
    > * [Intervalos de IP do Azure e marcas de serviço para o Azure governamental](https://www.microsoft.com/download/details.aspx?id=57063)
    > * [Intervalos de IP do Azure e marcas de serviço para o Azure China](https://www.microsoft.com//download/details.aspx?id=57062)

Quando você adicionar as UDRs, defina a rota para cada prefixo de endereço IP do Lote relacionado e defina __Próximo tipo de salto__ como __Internet__. A imagem a seguir mostra um exemplo dessa UDR no portal do Azure:

![Exemplo de uma UDR para um prefixo de endereço](./media/how-to-enable-virtual-network/user-defined-route.png)

> [!IMPORTANT]
> Os endereços IP podem mudar ao longo do tempo.

Para obter mais informações, consulte [Criar um pool de Lote do Azure em uma rede virtual](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="outbound-configuration"></a>Configuração de saída

1. Adicione __regras de rede__, __permitindo o tráfego__ de e __para__ as seguintes marcas de serviço:

    * AzureActiveDirectory
    * AzureMachineLearning
    * AzureResourceManager
    * Storage. Region
    * Keyvault. região
    * ContainerRegistry. Region

    Se você planeja usar as imagens padrão do Docker fornecidas pela Microsoft e habilitando dependências gerenciadas pelo usuário, você também deve adicionar as seguintes marcas de serviço:

    * MicrosoftContainerRegistry. Region
    * AzureFrontDoor.FirstParty

    Para entradas que contêm `region` , substitua pela região do Azure que você está usando. Por exemplo, `keyvault.westus`.

    Para o __protocolo__, selecione `TCP` . Para as __portas__ de origem e de destino, selecione `*` .

1. Adicione __regras de aplicativo__ para os seguintes hosts:

    > [!NOTE]
    > Essa não é uma lista completa dos hosts necessários para todos os recursos do Python na Internet, somente os mais usados. Por exemplo, se precisar de acesso a um repositório GitHub ou outro host, você deverá identificar e adicionar os hosts necessários para esse cenário.

    | **Nome do host** | **Finalidade** |
    | ---- | ---- |
    | **graph.windows.net** | Usado por Azure Machine Learning instância/cluster de computação. |
    | **anaconda.com**</br>**\*. anaconda.com** | Usado para instalar pacotes padrão. |
    | **\*. anaconda.org** | Usado para obter dados do repositório. |
    | **pypi.org** | Usado para listar as dependências do índice padrão, se houver, e o índice não é substituído pelas configurações do usuário. Se o índice for substituído, você também deverá permitir **\* . pythonhosted.org**. |
    | **cloud.r-project.org** | Usado ao instalar pacotes CRAN para desenvolvimento de R. |
    | **\*pytorch.org** | Usado por alguns exemplos com base em PyTorch. |
    | **\*. tensorflow.org** | Usado por alguns exemplos com base em Tensorflow. |

    Para __protocolo: porta__, selecione usar __http, https__.

    Para obter mais informações sobre como configurar regras de aplicativo, consulte [implantar e configurar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

1. Para restringir o acesso a modelos implantados no AKS (serviço kubernetes do Azure), consulte [restringir o tráfego de saída no serviço kubernetes do Azure](../aks/limit-egress-traffic.md).

## <a name="other-firewalls"></a>Outros firewalls

A orientação nesta seção é genérica, pois cada firewall tem sua própria terminologia e configurações específicas. Se você tiver dúvidas sobre como permitir a comunicação por meio do firewall, consulte a documentação do firewall que você está usando.

Se não estiver configurado corretamente, o firewall poderá causar problemas ao usar seu espaço de trabalho. Há uma variedade de nomes de host que são usados pelo espaço de trabalho Azure Machine Learning. As seções a seguir listam os hosts necessários para Azure Machine Learning.

### <a name="microsoft-hosts"></a>Hosts da Microsoft

Os hosts nesta seção são de propriedade da Microsoft e fornecem os serviços necessários para o funcionamento adequado do seu espaço de trabalho. As tabelas a seguir listam os nomes de host para as regiões do Azure Public, Azure governamental e Azure China 21Vianet.

**Hosts gerais do Azure**

| **Obrigatório para** | **Público do Azure** | **Azure Governamental** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.microsoftonline.com | login.microsoftonline.us | login.chinacloudapi.cn |
| Portal do Azure | management.azure.com | management.azure.us | management.azure.cn |
| Azure Resource Manager | management.azure.com | management.usgovcloudapi.net | management.chinacloudapi.cn |

**Hosts Azure Machine Learning**

| **Obrigatório para** | **Público do Azure** | **Azure Governamental** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | ml.azure.com | ml.azure.us | studio.ml.azure.cn |
| API |\*. azureml.ms | \*. ml.azure.us | \*. ml.azure.cn |
| Experimentação, histórico, hyperdrive, rotulagem | \*. experiments.azureml.net | \*. ml.azure.us | \*. ml.azure.cn |
| Gerenciamento de modelos | \*. modelmanagement.azureml.net | \*. ml.azure.us | \*. ml.azure.cn |
| Pipeline | \*. aether.ms | \*. ml.azure.us | \*. ml.azure.cn |
| Designer (serviço do estúdio) | \*. studioservice.azureml.com | \*. ml.azure.us | \*. ml.azure.cn |
| Notebook integrado | \*. notebooks.azure.net | \*. notebooks.usgovcloudapi.net |\*. notebooks.chinacloudapi.cn |
| Notebook integrado | \*. file.core.windows.net | \*. file.core.usgovcloudapi.net | \*. file.core.chinacloudapi.cn |
| Notebook integrado | \*.dfs.core.windows.net | \*. dfs.core.usgovcloudapi.net | \*. dfs.core.chinacloudapi.cn |
| Notebook integrado | \*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net | \*. blob.core.chinacloudapi.cn |
| Notebook integrado | graph.microsoft.com | graph.microsoft.us | graph.chinacloudapi.cn |
| Notebook integrado | \*. aznbcontent.net |  | |

**Azure Machine Learning instância de computação e hosts de cluster de computação**

| **Obrigatório para** | **Público do Azure** | **Azure Governamental** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Cluster/instância de computação | \*. batchai.core.windows.net | \*. batchai.core.usgovcloudapi.net |\*. batchai.ml.azure.cn |
| Cluster/instância de computação | graph.windows.net | graph.windows.net | graph.chinacloudapi.cn |
| Instância de computação | \*. instances.azureml.net | \*. instances.azureml.us | \*. instances.azureml.cn |
| Instância de computação | \*. instances.azureml.ms |  |  |

**Recursos associados usados pelo Azure Machine Learning**

| **Obrigatório para** | **Público do Azure** | **Azure Governamental** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Conta de Armazenamento do Azure | core.windows.net | core.usgovcloudapi.net | core.chinacloudapi.cn |
| Cofre de Chave do Azure | vault.azure.net | vault.usgovcloudapi.net | vault.azure.cn |
| Registro de Contêiner do Azure | azurecr.io | azurecr.us | azurecr.cn |
| Registro de Contêiner da Microsoft | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |


> [!TIP]
> Se você planeja usar a identidade federada, siga as [práticas recomendadas para proteger serviços de Federação do Active Directory (AD FS)](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs) artigo.

Além disso, use as informações em [túnel forçado](how-to-secure-training-vnet.md#forced-tunneling) para adicionar endereços IP para o `BatchNodeManagement` e o `AzureMachineLearning` .

Para obter informações sobre como restringir o acesso a modelos implantados no AKS (serviço kubernetes do Azure), consulte [restringir o tráfego de saída no serviço kubernetes do Azure](../aks/limit-egress-traffic.md).

### <a name="python-hosts"></a>Hosts do Python

Os hosts nesta seção são usados para instalar pacotes do Python. Eles são necessários durante o desenvolvimento, o treinamento e a implantação. 

> [!NOTE]
> Essa não é uma lista completa dos hosts necessários para todos os recursos do Python na Internet, somente os mais usados. Por exemplo, se precisar de acesso a um repositório GitHub ou outro host, você deverá identificar e adicionar os hosts necessários para esse cenário.

| **Nome do host** | **Finalidade** |
| ---- | ---- |
| **anaconda.com**</br>**\*. anaconda.com** | Usado para instalar pacotes padrão. |
| **\*. anaconda.org** | Usado para obter dados do repositório. |
| **pypi.org** | Usado para listar as dependências do índice padrão, se houver, e o índice não é substituído pelas configurações do usuário. Se o índice for substituído, você também deverá permitir **\* . pythonhosted.org**. |
| **\*pytorch.org** | Usado por alguns exemplos com base em PyTorch. |
| **\*. tensorflow.org** | Usado por alguns exemplos com base em Tensorflow. |

### <a name="r-hosts"></a>Hosts de R

Os hosts nesta seção são usados para instalar pacotes do R. Eles são necessários durante o desenvolvimento, o treinamento e a implantação.

> [!NOTE]
> Essa não é uma lista completa dos hosts necessários para todos os recursos de R na Internet, somente os mais usados. Por exemplo, se precisar de acesso a um repositório GitHub ou outro host, você deverá identificar e adicionar os hosts necessários para esse cenário.

| **Nome do host** | **Finalidade** |
| ---- | ---- |
| **cloud.r-project.org** | Usado ao instalar pacotes do CRAN. |

> [!IMPORTANT]
> Internamente, o SDK do R para Azure Machine Learning usa pacotes do Python. Portanto, você também deve permitir hosts Python por meio do firewall.
## <a name="next-steps"></a>Próximas etapas

* [Tutorial: Implantar e configurar o Firewall do Azure usando o portal do Azure](../firewall/tutorial-firewall-deploy-portal.md)
* [Proteger trabalhos de experimentação e inferência do Azure ML em uma Rede Virtual do Azure](how-to-network-security-overview.md)
