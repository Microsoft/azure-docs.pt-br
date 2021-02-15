---
title: Criar e anexar o serviço kubernetes do Azure
titleSuffix: Azure Machine Learning
description: Saiba como criar um novo cluster do serviço kubernetes do Azure por meio de Azure Machine Learning ou como anexar um cluster AKS existente ao seu espaço de trabalho.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 10/02/2020
ms.openlocfilehash: 97042479419cbc9d634a06e2dcbcccf9c8fde62b
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99560466"
---
# <a name="create-and-attach-an-azure-kubernetes-service-cluster"></a>Criar e anexar um cluster do serviço kubernetes do Azure

Azure Machine Learning pode implantar modelos de aprendizado de máquina treinados no serviço kubernetes do Azure. No entanto, você deve primeiro __criar__ um cluster do AKS (serviço kubernetes do Azure) do seu espaço de trabalho do Azure ml ou __anexar__ um cluster AKs existente. Este artigo fornece informações sobre como criar e anexar um cluster.

## <a name="prerequisites"></a>Pré-requisitos

- Um Workspace do Azure Machine Learning. Para obter mais informações, consulte [criar um Azure Machine Learning espaço de trabalho](how-to-manage-workspace.md).

- A [extensão CLI do Azure para o serviço Machine Learning](reference-azure-machine-learning-cli.md), o [SDK do Azure Machine Learning Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)ou a [extensão Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

- Se você planeja usar uma rede virtual do Azure para proteger a comunicação entre seu espaço de trabalho do Azure ML e o cluster AKS, leia o [isolamento de rede durante o treinamento & artigo inferência](./how-to-network-security-overview.md) .

## <a name="limitations"></a>Limitações

- Se você precisar de um **SLB (Standard Load Balancer)** implantado em seu cluster em vez de um Load Balancer básico (BLB), crie um cluster no portal do AKS/CLI/SDK e **anexe** -o ao espaço de trabalho AML.

- Se você tiver um Azure Policy que restringe a criação de endereços IP públicos, a criação do cluster AKS falhará. O AKS requer um IP público para o [tráfego de saída](../aks/limit-egress-traffic.md). O artigo tráfego de saída também fornece orientação para bloquear o tráfego de saída do cluster por meio do IP público, com exceção de alguns nomes de domínio totalmente qualificados. Há duas maneiras de habilitar um IP público:
    - O cluster pode usar o IP público criado por padrão com o BLB ou SLB, ou
    - O cluster pode ser criado sem um IP público e, em seguida, um IP público é configurado com um firewall com uma rota definida pelo usuário. Para obter mais informações, consulte [Personalizar a saída do cluster com uma rota definida pelo usuário](../aks/egress-outboundtype.md).
    
    O plano de controle AML não se comunica com esse IP público. Ele se comunica com o plano de controle AKS para implantações. 

- Se você **anexar** um cluster AKs, que tem um [intervalo de IP autorizado habilitado para acessar o servidor de API](../aks/api-server-authorized-ip-ranges.md), habilite os intervalos de IP do plano de controle AML para o cluster AKs. O plano de controle AML é implantado em regiões emparelhadas e implanta pods de inferência no cluster AKS. Sem acesso ao servidor de API, o pods de inferência não pode ser implantado. Use os [intervalos de IP](https://www.microsoft.com/download/confirmation.aspx?id=56519) para ambas as [regiões emparelhadas](../best-practices-availability-paired-regions.md) ao habilitar os intervalos de IP em um cluster AKs.

    Os intervalos de IP autorizados só funcionam com Standard Load Balancer.

- Ao **anexar** um cluster AKs, ele deve estar na mesma assinatura do Azure que seu espaço de trabalho do Azure Machine Learning.

- Se você quiser usar um cluster AKS privado (usando o link privado do Azure), deverá primeiro criar o cluster e, em seguida, **anexá** -lo ao espaço de trabalho. Para obter mais informações, consulte [criar um cluster particular do serviço kubernetes do Azure](../aks/private-clusters.md).

- O nome de computação do cluster AKS deve ser exclusivo no seu espaço de trabalho do Azure ML.
    - O nome é obrigatório e deve ter entre 3 e 24 caracteres.
    - Os caracteres válidos são letras maiúsculas e minúsculas, dígitos e caractere.
    - O nome deve começar com uma letra.
    - O nome deve ser exclusivo em todas as computações existentes em uma região do Azure. Você verá um alerta se o nome escolhido não for exclusivo.
   
 - Se você quiser implantar modelos em nós de **GPU** ou nós **FPGA** (ou qualquer SKU específico), deverá criar um cluster com o SKU específico. Não há suporte para a criação de um pool de nós secundário em um cluster existente e a implantação de modelos no pool de nós secundário.
 
- Ao criar ou anexar um cluster, você pode selecionar se deseja criar o cluster para __desenvolvimento/teste__ ou __produção__. Se você quiser criar um cluster AKS para __desenvolvimento__, __validação__ e __teste__ em vez de produção, defina a __finalidade do cluster__ como __dev-Test__. Se você não especificar a finalidade do cluster, um cluster de __produção__ será criado. 

    > [!IMPORTANT]
    > Um cluster de __desenvolvimento/teste__ não é adequado para o tráfego de nível de produção e pode aumentar os tempos de inferência. Os clusters de desenvolvimento/teste também não garantem a tolerância a falhas.

- Ao criar ou anexar um cluster, se o cluster for usado para __produção__, ele deverá conter pelo menos 12 __CPUs virtuais__. O número de CPUs virtuais pode ser calculado multiplicando-se o __número de nós__ no cluster pelo __número de núcleos__ fornecido pelo tamanho da VM selecionado. Por exemplo, se você usar um tamanho de VM de "Standard_D3_v2", que tem 4 núcleos virtuais, deverá selecionar 3 ou maior como o número de nós.

    Para um cluster de __desenvolvimento/teste__ , recolocamos pelo menos duas CPUs virtuais.

- O SDK do Azure Machine Learning não fornece suporte para dimensionar um cluster AKS. Para dimensionar os nós no cluster, use a interface do usuário para o cluster AKS no Azure Machine Learning Studio. Você só pode alterar a contagem de nós, não o tamanho da VM do cluster. Para obter mais informações sobre como dimensionar os nós em um cluster AKS, consulte os seguintes artigos:

    - [Dimensionar manualmente a contagem de nós em um cluster AKS](../aks/scale-cluster.md)
    - [Configurar o dimensionamento de cluster em AKS](../aks/cluster-autoscaler.md)

- __Não atualize diretamente o cluster usando uma configuração de YAML__. Enquanto os serviços Kubernetess do Azure dão suporte a atualizações por meio da configuração do YAML, Azure Machine Learning implantações substituirão as alterações. Os únicos dois campos YAML que não serão substituídos são __limites de solicitação__ e __CPU e memória__.

## <a name="azure-kubernetes-service-version"></a>Versão do Serviço de Kubernetes do Azure

O serviço kubernetes do Azure permite que você crie um cluster usando uma variedade de versões do kubernetes. Para obter mais informações sobre as versões disponíveis, consulte [versões do kubernetes com suporte no serviço kubernetes do Azure](../aks/supported-kubernetes-versions.md).

Ao **criar** um cluster do serviço kubernetes do Azure usando um dos seguintes métodos, você *não tem uma opção na versão* do cluster que é criada:

* Azure Machine Learning Studio ou a seção Azure Machine Learning do portal do Azure.
* Extensão de Machine Learning para CLI do Azure.
* SDK do Azure Machine Learning.

Esses métodos de criação de um cluster AKS usam a versão __padrão__ do cluster. *A versão padrão muda ao longo do tempo* à medida que novas versões do kubernetes se tornam disponíveis.

Ao **anexar** um cluster AKs existente, damos suporte a todas as versões do AKS com suporte no momento.

> [!NOTE]
> Pode haver casos de borda em que você tem um cluster mais antigo que não tem mais suporte. Nesse caso, a operação de anexação retornará um erro e listará as versões com suporte no momento.
>
> Você pode anexar versões de **Visualização** . A funcionalidade de visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. O suporte para o uso de versões de visualização pode ser limitado. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="available-and-default-versions"></a>Versões padrão e disponíveis

Para localizar as versões de AKS disponíveis e padrão, use o comando [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) [AZ AKs Get-Versions](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_versions). Por exemplo, o comando a seguir retorna as versões disponíveis na região oeste dos EUA:

```azurecli-interactive
az aks get-versions -l westus -o table
```

A saída desse comando é semelhante ao texto a seguir:

```text
KubernetesVersion    Upgrades
-------------------  ----------------------------------------
1.18.6(preview)      None available
1.18.4(preview)      1.18.6(preview)
1.17.9               1.18.4(preview), 1.18.6(preview)
1.17.7               1.17.9, 1.18.4(preview), 1.18.6(preview)
1.16.13              1.17.7, 1.17.9
1.16.10              1.16.13, 1.17.7, 1.17.9
1.15.12              1.16.10, 1.16.13
1.15.11              1.15.12, 1.16.10, 1.16.13
```

Para localizar a versão padrão que é usada ao **criar** um cluster por meio de Azure Machine Learning, você pode usar o `--query` parâmetro para selecionar a versão padrão:

```azurecli-interactive
az aks get-versions -l westus --query "orchestrators[?default == `true`].orchestratorVersion" -o table
```

A saída desse comando é semelhante ao texto a seguir:

```text
Result
--------
1.16.13
```

Se você quiser **verificar programaticamente as versões disponíveis**, use a API REST dos [orquestradores de lista de cliente do serviço de contêiner](/rest/api/container-service/container%20service%20client/listorchestrators) . Para localizar as versões disponíveis, examine as entradas onde `orchestratorType` está `Kubernetes` . As `orchestrationVersion` entradas associadas contêm as versões disponíveis que podem ser **anexadas** ao seu espaço de trabalho.

Para localizar a versão padrão que é usada ao **criar** um cluster por meio de Azure Machine Learning, localize a entrada onde `orchestratorType` é `Kubernetes` e `default` é `true` . O `orchestratorVersion` valor associado é a versão padrão. O trecho JSON a seguir mostra uma entrada de exemplo:

```json
...
 {
        "orchestratorType": "Kubernetes",
        "orchestratorVersion": "1.16.13",
        "default": true,
        "upgrades": [
          {
            "orchestratorType": "",
            "orchestratorVersion": "1.17.7",
            "isPreview": false
          }
        ]
      },
...
```

## <a name="create-a-new-aks-cluster"></a>Criar um cluster do AKS

**Tempo estimado**: aproximadamente 10 minutos.

Criar ou anexar um cluster AKS é um processo de uma vez para seu espaço de trabalho. Você pode reutilizar esse cluster para várias implantações. Se você excluir o cluster ou o grupo de recursos que o contém, deverá criar um novo cluster na próxima vez que precisar implantá-lo. Você pode ter vários clusters AKS anexados ao seu espaço de trabalho.

O exemplo a seguir demonstra como criar um novo cluster AKS usando o SDK e a CLI:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

# Example configuration to use an existing virtual network
# prov_config.vnet_name = "mynetwork"
# prov_config.vnet_resourcegroup_name = "mygroup"
# prov_config.subnet_name = "default"
# prov_config.service_cidr = "10.0.0.0/16"
# prov_config.dns_service_ip = "10.0.0.10"
# prov_config.docker_bridge_cidr = "172.17.0.1/16"

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Para obter mais informações sobre as classes, os métodos e os parâmetros usados neste exemplo, consulte os seguintes documentos de referência:

* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?preserve-view=true&view=azure-ml-py)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](/python/api/azureml-core/azureml.core.compute.computetarget?preserve-view=true&view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computetarget?preserve-view=true&view=azure-ml-py#wait-for-completion-show-output-false-)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks
```

Para obter mais informações, consulte a referência do [AZ ml computetarget Create AKs](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) .

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para obter informações sobre como criar um cluster AKS no portal, consulte [criar destinos de computação no Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="attach-an-existing-aks-cluster"></a>Anexar um cluster AKS existente

**Tempo estimado:** Aproximadamente 5 minutos.

Se você já tiver o cluster AKS em sua assinatura do Azure, poderá usá-lo com seu espaço de trabalho.

> [!TIP]
> O cluster AKS existente pode estar em uma região do Azure que não seja seu espaço de trabalho Azure Machine Learning.


> [!WARNING]
> Não crie vários anexos simultâneos para o mesmo cluster AKS do seu espaço de trabalho. Por exemplo, anexar um cluster AKS a um espaço de trabalho usando dois nomes diferentes. Cada novo anexo interromperá os anexos existentes anteriores.
>
> Se você quiser anexar novamente um cluster AKS, por exemplo, para alterar o TLS ou outra configuração de cluster, primeiro você deve remover o anexo existente usando [AksCompute. Detach ()](/python/api/azureml-core/azureml.core.compute.akscompute?preserve-view=true&view=azure-ml-py#detach--).

Para obter mais informações sobre como criar um cluster AKS usando o CLI do Azure ou o portal, consulte os seguintes artigos:

* [Criar um cluster do AKS (CLI)](/cli/azure/aks?bc=%2fazure%2fbread%2ftoc.json&preserve-view=true&toc=%2fazure%2faks%2fTOC.json&view=azure-cli-latest#az-aks-create)
* [Criar um cluster AKS (Portal)](../aks/kubernetes-walkthrough-portal.md?preserve-view=true&view=azure-cli-latest)
* [Criar um cluster AKS (modelo ARM nos modelos de início rápido do Azure)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

O exemplo a seguir demonstra como anexar um cluster AKS existente ao seu espaço de trabalho:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)

# Wait for the attach process to complete
aks_target.wait_for_completion(show_output = True)
```

Para obter mais informações sobre as classes, os métodos e os parâmetros usados neste exemplo, consulte os seguintes documentos de referência:

* [AksCompute.attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?preserve-view=true&view=azure-ml-py)
* [AksCompute. Attach](/python/api/azureml-core/azureml.core.compute.computetarget?preserve-view=true&view=azure-ml-py#attach-workspace--name--attach-configuration-)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para anexar um cluster existente usando a CLI, você precisa obter a ID de recurso do cluster existente. Para obter esse valor, use o comando a seguir. Substitua `myexistingcluster` pelo nome do seu cluster AKs. Substitua `myresourcegroup` pelo grupo de recursos que contém o cluster:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Esse comando retorna um valor semelhante ao texto a seguir:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Para anexar o cluster existente ao seu espaço de trabalho, use o comando a seguir. Substituir pelo `aksresourceid` valor retornado pelo comando anterior. Substituir `myresourcegroup` pelo grupo de recursos que contém seu espaço de trabalho. Substituir `myworkspace` pelo nome do espaço de trabalho.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Para obter mais informações, consulte a referência de [AKs AZ ml computetarget Attach](/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) .

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para obter informações sobre como anexar um cluster AKS no portal, consulte [criar destinos de computação no Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="create-or-attach-an-aks-cluster-with-tls-termination"></a>Criar ou anexar um cluster AKS com terminação TLS
Ao [criar ou anexar um cluster AKs](how-to-create-attach-kubernetes.md), você pode habilitar a terminação de TLS com os objetos de configuração **[AksCompute.provisioning_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#&preserve-view=trueprovisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** e **[AksCompute.attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#&preserve-view=trueattach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** . O método retorna um objeto de configuração que tem um método **Enable_ssl** , e você pode usar **Enable_ssl** método para habilitar o TLS.

O exemplo a seguir mostra como habilitar o encerramento de TLS com a geração e configuração automáticas de certificado TLS usando o certificado da Microsoft nos bastidores.
```python
   from azureml.core.compute import AksCompute, ComputeTarget
   
   # Enable TLS termination when you create an AKS cluster by using provisioning_config object enable_ssl method

   # Leaf domain label generates a name using the formula
   # "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
   # where "######" is a random series of characters
   provisioning_config.enable_ssl(leaf_domain_label = "contoso")
   
   # Enable TLS termination when you attach an AKS cluster by using attach_config object enable_ssl method

   # Leaf domain label generates a name using the formula
   # "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
   # where "######" is a random series of characters
   attach_config.enable_ssl(leaf_domain_label = "contoso")


```
O exemplo a seguir mostra como habilitar a terminação TLS com o certificado personalizado e o nome de domínio personalizado. Com o domínio e certificado personalizados, você deve atualizar seu registro DNS para apontar para o endereço IP do ponto de extremidade de pontuação, consulte [atualizar seu DNS](how-to-secure-web-service.md#update-your-dns)

```python
   from azureml.core.compute import AksCompute, ComputeTarget

   # Enable TLS termination with custom certificate and custom domain when creating an AKS cluster
   
   provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    
   # Enable TLS termination with custom certificate and custom domain when attaching an AKS cluster

   attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")


```
>[!NOTE]
> Para obter mais informações sobre como proteger a implantação de modelo no cluster AKS, consulte [usar o TLS para proteger um serviço Web por meio de Azure Machine Learning](how-to-secure-web-service.md)

## <a name="create-or-attach-an-aks-cluster-to-use-internal-load-balancer-with-private-ip"></a>Criar ou anexar um cluster AKS para usar o Load Balancer interno com IP privado
Ao criar ou anexar um cluster AKS, você pode configurar o cluster para usar um Load Balancer interno. Com um Load Balancer interno, pontos de extremidade de Pontuação para suas implantações para AKS usarão um IP privado dentro da rede virtual. Os trechos de código a seguir mostram como configurar um Load Balancer interno para um cluster AKS.
```python
   
   from azureml.core.compute.aks import AksUpdateConfiguration
   from azureml.core.compute import AksCompute, ComputeTarget
   
   # When you create an AKS cluster, you can specify Internal Load Balancer to be created with provisioning_config object
   provisioning_config = AksCompute.provisioning_configuration(load_balancer_type = 'InternalLoadBalancer')

   # when you attach an AKS cluster, you can update the cluster to use internal load balancer after attach
   aks_target = AksCompute(ws,"myaks")

   # Change to the name of the subnet that contains AKS
   subnet_name = "default"
   # Update AKS configuration to use an internal load balancer
   update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
   aks_target.update(update_config)
   # Wait for the operation to complete
   aks_target.wait_for_completion(show_output = True)
   
   
```
>[!IMPORTANT]
> Azure Machine Learning não dá suporte à terminação de TLS com Load Balancer interno. O Load Balancer interno tem um IP privado e esse IP privado pode estar em outra rede e o certificado poderá ser recused. 

>[!NOTE]
> Para obter mais informações sobre como proteger o ambiente inferência, consulte [proteger um ambiente de inferência de Azure Machine Learning](how-to-secure-inferencing-vnet.md)

## <a name="detach-an-aks-cluster"></a>Desanexar um cluster AKS

Para desanexar um cluster do seu espaço de trabalho, use um dos seguintes métodos:

> [!WARNING]
> Usar o Azure Machine Learning Studio, o SDK ou a extensão CLI do Azure para o aprendizado de máquina para desanexar um cluster AKS não **exclui o cluster AKs**. Para excluir o cluster, consulte [usar o CLI do Azure com AKs](../aks/kubernetes-walkthrough.md#delete-the-cluster).

# <a name="python"></a>[Python](#tab/python)

```python
aks_target.detach()
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para desanexar o cluster existente ao seu espaço de trabalho, use o comando a seguir. Substitua `myaks` pelo nome ao qual o cluster AKs está anexado ao seu espaço de trabalho. Substituir `myresourcegroup` pelo grupo de recursos que contém seu espaço de trabalho. Substituir `myworkspace` pelo nome do espaço de trabalho.

```azurecli
az ml computetarget detach -n myaks -g myresourcegroup -w myworkspace
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

No Azure Machine Learning Studio, selecione __computação__, __clusters de inferência__ e o cluster que você deseja remover. Use o link __desanexar__ para desanexar o cluster.

---

## <a name="troubleshooting"></a>Solução de problemas
### <a name="update-the-cluster"></a>Atualizar o cluster

As atualizações para Azure Machine Learning componentes instalados em um cluster do serviço kubernetes do Azure devem ser aplicadas manualmente. 

Você pode aplicar essas atualizações desanexando o cluster do espaço de trabalho Azure Machine Learning e anexando novamente o cluster ao espaço de trabalho. Se o TLS estiver habilitado no cluster, será necessário fornecer o certificado TLS/SSL e a chave privada ao anexar novamente o cluster. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

Se você não tiver mais o certificado TLS/SSL e a chave privada, ou se estiver usando um certificado gerado pelo Azure Machine Learning, poderá recuperar os arquivos antes de desanexar o cluster conectando-se ao cluster usando `kubectl` e recuperando o segredo `azuremlfessl` .

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes armazena os segredos no formato codificado em base-64. Você precisará de base-64 decodificar `cert.pem` os `key.pem` componentes e dos segredos antes de fornecer a eles `attach_config.enable_ssl` . 

### <a name="webservice-failures"></a>Falhas de WebService

Muitas falhas de WebService no AKS podem ser depuradas conectando-se ao cluster usando `kubectl` . Você pode obter o `kubeconfig.json` para um cluster AKs executando

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="next-steps"></a>Próximas etapas

* [Usar o RBAC do Azure para autorização kubernetes](../aks/manage-azure-rbac.md)
* [Como e onde implantar um modelo](how-to-deploy-and-where.md)
* [Implantar um modelo em um cluster do serviço kubernetes do Azure](how-to-deploy-azure-kubernetes-service.md)