---
title: Visão geral de isolamento de rede virtual e segurança
titleSuffix: Azure Machine Learning
description: Use uma rede virtual do Azure isolada com Azure Machine Learning para proteger os recursos do espaço de trabalho e ambientes de computação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 03/02/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, references_regions, contperf-fy21q1
ms.openlocfilehash: 1c3d9b286a8262efa126ba9c661c50dd88e78b64
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103573465"
---
# <a name="virtual-network-isolation-and-privacy-overview"></a>Visão geral de isolamento de rede virtual e privacidade

Neste artigo, você aprenderá a usar redes virtuais (VNets) para proteger a comunicação de rede no Azure Machine Learning. Este artigo usa um cenário de exemplo para mostrar como configurar uma rede virtual completa.

Este artigo é a parte um de uma série de cinco partes que explica como proteger um Azure Machine Learning fluxo de trabalho. É altamente recomendável que você leia este artigo de visão geral para entender os conceitos primeiro. 

Estes são os outros artigos desta série:

**1. visão geral da VNet**  >  [2. Proteja o espaço de trabalho](how-to-secure-workspace-vnet.md)  >  [3. Proteja o ambiente de treinamento](how-to-secure-training-vnet.md)  >  [4. Proteja o ambiente do inferência](how-to-secure-inferencing-vnet.md)  >  [5. Habilitar a funcionalidade do estúdio](how-to-enable-studio-virtual-network.md)

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tenha familiaridade com os seguintes tópicos:
+ [Redes Virtuais do Azure](../virtual-network/virtual-networks-overview.md)
+ [Rede IP](../virtual-network/public-ip-addresses.md)
+ [Link Privado do Azure](how-to-configure-private-link.md)
+ [NSG (grupos de segurança de rede)](../virtual-network/network-security-groups-overview.md)
+ [Firewalls de rede](../firewall/overview.md)
## <a name="example-scenario"></a>Cenário de exemplo

Nesta seção, você aprenderá como um cenário de rede comum é configurado para proteger Azure Machine Learning comunicação com endereços IP privados.

A tabela a seguir compara como os serviços acessam diferentes partes de uma rede Azure Machine Learning com uma VNet e sem uma VNet.

| Cenário | Workspace | Recursos associados | Ambiente de computação de treinamento | Ambiente de computação inferência |
|-|-|-|-|-|-|
|**Nenhuma rede virtual**| IP público | IP público | IP público | IP público |
|**Proteger recursos em uma rede virtual**| IP privado (ponto de extremidade privado) | IP público (ponto de extremidade de serviço) <br> **or** <br> IP privado (ponto de extremidade privado) | IP Privado | IP Privado  | 

* **Espaço de trabalho** -crie um ponto de extremidade privado de sua VNet para se conectar ao link privado no espaço de trabalho. O ponto de extremidade privado conecta o espaço de trabalho à vnet por meio de vários endereços IP privados.
* **Recurso associado** – Use pontos de extremidade de serviço ou pontos de extremidade privados para se conectar a recursos do espaço de trabalho como armazenamento do azure, Azure Key Vault e serviços de contêiner do Azure.
    * Os **pontos de extremidade de serviço** fornecem a identidade de sua rede virtual para o serviço do Azure. Depois de habilitar os pontos de extremidade de serviço em sua rede virtual, você pode adicionar uma regra de rede virtual para proteger os recursos de serviço do Azure para sua rede virtual. Os pontos de extremidade de serviço usam endereços IP públicos.
    * **Pontos de extremidade privados** são interfaces de rede que conectam com segurança a um serviço da plataforma Azure link privado. O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente o serviço em sua VNet.
* **Treinamento** de acesso de computação treinamentos de computação destinos como Azure Machine Learning instância de computação e Azure Machine Learning clusters de computação com segurança com endereços IP privados. 
* **Inferência Compute Access** – acesse os clusters de computação do AKS (serviços Kubernetess do Azure) com endereços IP privados.


As próximas cinco seções mostram como proteger o cenário de rede descrito acima. Para proteger sua rede, você deve:

1. Proteger o [**espaço de trabalho e os recursos associados**](#secure-the-workspace-and-associated-resources).
1. Proteja o [**ambiente de treinamento**](#secure-the-training-environment).
1. Proteja o [**ambiente inferência**](#secure-the-inferencing-environment).
1. Opcionalmente: [**habilitar a funcionalidade do estúdio**](#optional-enable-studio-functionality).
1. Defina [**as configurações de firewall**](#configure-firewall-settings).
1. Configurar a [resolução de nomes DNS](#custom-dns).
## <a name="secure-the-workspace-and-associated-resources"></a>Proteger o espaço de trabalho e os recursos associados

Use as etapas a seguir para proteger seu espaço de trabalho e os recursos associados. Essas etapas permitem que seus serviços se comuniquem na rede virtual.

1. Crie um [espaço de trabalho habilitado para vínculo privado](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint) para habilitar a comunicação entre a VNet e o espaço de trabalho.
1. Adicione os seguintes serviços à rede virtual usando um ponto _de_ __extremidade de serviço__ ou um __ponto de extremidade privado__. Você também deve permitir que os serviços confiáveis da Microsoft acessem esses serviços:
    
    | Serviço | Informações do ponto de extremidade | Permitir informações confiáveis |
    | ----- | ----- | ----- |
    | __Azure Key Vault__| [Ponto de extremidade de serviço](../key-vault/general/overview-vnet-service-endpoints.md)</br>[Ponto de extremidade privado](../key-vault/general/private-link-service.md) | [Permitir que os serviços confiáveis da Microsoft ignorem esse firewall](how-to-secure-workspace-vnet.md#secure-azure-key-vault) |
    | __Conta de Armazenamento do Azure__ | [Ponto de extremidade de serviço](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints)</br>[Ponto de extremidade privado](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints) | [Conceder acesso a serviços confiáveis do Azure](../storage/common/storage-network-security.md#grant-access-to-trusted-azure-services) |
    | __Registro de Contêiner do Azure__ | [Ponto de extremidade de serviço](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr)</br>[Ponto de extremidade privado](../container-registry/container-registry-private-link.md) | [Permitir serviços confiáveis](../container-registry/allow-access-trusted-services.md) |


![Diagrama de arquitetura mostrando como o espaço de trabalho e os recursos associados se comunicam entre si em pontos de extremidade de serviço ou pontos de extremidade privados dentro de uma VNet](./media/how-to-network-security-overview/secure-workspace-resources.png)

Para obter instruções detalhadas sobre como concluir essas etapas, consulte [proteger um Azure Machine Learning espaço de trabalho](how-to-secure-workspace-vnet.md). 

### <a name="limitations"></a>Limitações

Proteger seu espaço de trabalho e os recursos associados em uma rede virtual tem as seguintes limitações:
- O uso de um espaço de trabalho Azure Machine Learning com link privado não está disponível nas regiões do Azure governamental ou do Azure China 21Vianet.
- Todos os recursos devem estar atrás da mesma VNet. No entanto, as sub-redes na mesma VNet são permitidas.

## <a name="secure-the-training-environment"></a>Proteger o ambiente de treinamento

Nesta seção, você aprenderá a proteger o ambiente de treinamento no Azure Machine Learning. Você também aprende como o Azure Machine Learning conclui um trabalho de treinamento para entender como as configurações de rede funcionam em conjunto.

Para proteger o ambiente de treinamento, use as seguintes etapas:

1. Crie um Azure Machine Learning [instância de computação e cluster de computador na rede virtual](how-to-secure-training-vnet.md#compute-instance) para executar o trabalho de treinamento.
1. [Permita a comunicação de entrada do serviço de lote do Azure](how-to-secure-training-vnet.md#mlcports) para que o serviço de lote possa enviar trabalhos para seus recursos de computação. 

![Diagrama de arquitetura mostrando como proteger instâncias e clusters de computação gerenciados](./media/how-to-network-security-overview/secure-training-environment.png)

Para obter instruções detalhadas sobre como concluir essas etapas, consulte [proteger um ambiente de treinamento](how-to-secure-training-vnet.md). 

### <a name="example-training-job-submission"></a>Exemplo de envio de trabalho de treinamento 

Nesta seção, você aprenderá como o Azure Machine Learning se comunica com segurança entre os serviços para enviar um trabalho de treinamento. Isso mostra como todas as suas configurações funcionam em conjunto para proteger a comunicação.

1. O cliente carrega scripts de treinamento e dados de treinamento para contas de armazenamento que são protegidas com um serviço ou ponto de extremidade privado.

1. O cliente envia um trabalho de treinamento para o espaço de trabalho Azure Machine Learning por meio do ponto de extremidade privado.

1. Os serviços do lote do Azure recebem o trabalho do espaço de trabalho e enviam o trabalho de treinamento para o ambiente de computação por meio do balanceador de carga público provisionado com o recurso de computação. 

1. O recurso de computação recebe o trabalho e começa o treinamento. Os recursos de computação acessam contas de armazenamento seguro para baixar arquivos de treinamento e carregar a saída.

### <a name="limitations"></a>Limitações

- A instância de computação do Azure e os clusters de computação do Azure devem estar na mesma VNet, região e assinatura que o espaço de trabalho e seus recursos associados. 

## <a name="secure-the-inferencing-environment"></a>Proteger o ambiente inferência

Nesta seção, você aprenderá as opções disponíveis para proteger um ambiente inferência. Recomendamos que você use clusters do AKS (serviços Kubernetess do Azure) para implantações de produção em grande escala.

Você tem duas opções para clusters AKS em uma rede virtual:

- Implante ou anexe um cluster AKS padrão à sua VNet.
- Anexe um cluster AKS privado à sua VNet.

**Os clusters AKs padrão** têm um plano de controle com endereços IP públicos. Você pode adicionar um cluster AKS padrão à sua VNet durante a implantação ou anexar um cluster após sua criação.

Os **clusters AKs privados** têm um plano de controle, que só pode ser acessado por meio de IPS privados. Os clusters AKS privados devem ser anexados após a criação do cluster.

Para obter instruções detalhadas sobre como adicionar clusters padrão e privados, consulte [proteger um ambiente inferência](how-to-secure-inferencing-vnet.md). 

O diagrama de rede a seguir mostra um espaço de trabalho Azure Machine Learning protegido com um cluster AKS privado anexado à rede virtual.

![Diagrama de arquitetura que mostra como anexar um cluster AKS privado à rede virtual. O plano de controle AKS é colocado fora da VNet do cliente](./media/how-to-network-security-overview/secure-inferencing-environment.png)

### <a name="limitations"></a>Limitações
- Os clusters AKS devem pertencer à mesma VNet que o espaço de trabalho e seus recursos associados. 

## <a name="optional-enable-public-access"></a>Opcional: habilitar acesso público

Você pode proteger o espaço de trabalho por trás de uma VNet usando um ponto de extremidade privado e ainda permitir o acesso pela Internet pública. A configuração inicial é a mesma que [proteger o espaço de trabalho e os recursos associados](#secure-the-workspace-and-associated-resources). 

Depois de proteger o espaço de trabalho com um link privado, você [habilitará o acesso público](how-to-configure-private-link.md#enable-public-access). Depois disso, você pode acessar o espaço de trabalho da Internet pública e da VNet.

### <a name="limitations"></a>Limitações

- Se você usar o Azure Machine Learning Studio pela Internet pública, alguns recursos, como o designer, poderão falhar ao acessar seus dados. Esse problema ocorre quando os dados são armazenados em um serviço protegido por trás da VNet. Por exemplo, uma conta de armazenamento do Azure.
## <a name="optional-enable-studio-functionality"></a>Opcional: habilitar a funcionalidade do estúdio

[Proteger o espaço de trabalho](#secure-the-workspace-and-associated-resources)  >  [Proteger o ambiente](#secure-the-training-environment)  >  de treinamento [Proteger o ambiente inferência](#secure-the-inferencing-environment)  >  **Habilitar a funcionalidade**  >  do estúdio [Definir configurações de firewall](#configure-firewall-settings)

Se o armazenamento estiver em uma VNet, você deve primeiro executar etapas de configuração adicionais para habilitar a funcionalidade completa no [estúdio](overview-what-is-machine-learning-studio.md). Por padrão, o seguinte recurso está desabilitado:

* Visualizar dados no estúdio.
* Visualize dados no designer.
* Implante um modelo no designer.
* Envie um experimento do AutoML.
* Inicie um projeto de rotulagem.

Para habilitar a funcionalidade completa do estúdio dentro de uma VNet, consulte [usar o Azure Machine Learning Studio em uma rede virtual](how-to-enable-studio-virtual-network.md#configure-data-access-in-the-studio). O estúdio dá suporte a contas de armazenamento usando pontos de extremidade de serviço ou pontos de extremidade privados.

### <a name="limitations"></a>Limitações

O [rotulamento de dados assistido por ml](how-to-create-labeling-projects.md#use-ml-assisted-data-labeling) não dá suporte a contas de armazenamento padrão protegidas por trás de uma rede virtual. Você deve usar uma conta de armazenamento não padrão para rotular dados assistidos por ML. Observe que a conta de armazenamento não padrão pode ser protegida por trás da rede virtual. 

## <a name="configure-firewall-settings"></a>Definir configurações de firewall

Configure seu firewall para controlar o acesso aos seus recursos do Azure Machine Learning Workspace e à Internet pública. Embora seja recomendável o Firewall do Azure, você deve ser capaz de usar outros produtos de firewall para proteger sua rede. Se você tiver dúvidas sobre como permitir a comunicação por meio do firewall, consulte a documentação do firewall que você está usando.

Para obter mais informações sobre as configurações de firewall, consulte [usar o espaço de trabalho por trás de um firewall](how-to-access-azureml-behind-firewall.md).

## <a name="custom-dns"></a>DNS Personalizado

Se você precisar usar uma solução DNS personalizada para sua rede virtual, deverá adicionar registros de host para seu espaço de trabalho.

Para obter mais informações sobre os nomes de domínio e endereços IP necessários, consulte [como usar um espaço de trabalho com um servidor DNS personalizado](how-to-custom-dns.md).

## <a name="next-steps"></a>Próximas etapas

Este artigo faz parte de uma série de redes virtuais de cinco partes. Consulte o restante dos artigos para saber como proteger uma rede virtual:

* [Parte 2: visão geral da rede virtual](how-to-secure-workspace-vnet.md)
* [Parte 3: proteger o ambiente de treinamento](how-to-secure-training-vnet.md)
* [Parte 4: proteger o ambiente inferência](how-to-secure-inferencing-vnet.md)
* [Parte 5: habilitar a funcionalidade do estúdio](how-to-enable-studio-virtual-network.md)

Consulte também o artigo sobre como usar o [DNS personalizado](how-to-custom-dns.md) para a resolução de nomes.