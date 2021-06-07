---
title: Criar um pool do Lote do Azure sem endereços IP públicos
description: Saiba como criar um pool sem endereços IP públicos
author: pkshultz
ms.topic: how-to
ms.date: 12/9/2020
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: 806e85fca0a509d56e248fc7779fba0f0a59a61d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97007663"
---
# <a name="create-an-azure-batch-pool-without-public-ip-addresses"></a>Criar um pool do Lote do Azure sem endereços IP públicos

Ao criar um pool do lote do Azure, você pode provisionar o pool de configuração de máquina virtual sem um endereço IP público. Este artigo explica como configurar um pool do lote sem endereços IP públicos.

## <a name="why-use-a-pool-without-public-ip-addresses"></a>Por que usar um pool sem endereços IP públicos?

Por padrão, todos os nós de computação em um pool de configuração de máquina virtual do lote do Azure recebem um endereço IP público. Esse endereço é usado pelo serviço de lote para agendar tarefas e para comunicação com nós de computação, incluindo acesso de saída à Internet.

Para restringir o acesso a esses nós e reduzir a capacidade de descoberta desses nós da Internet, você pode provisionar o pool sem endereços IP públicos.

> [!IMPORTANT]
> O suporte para pools sem endereços IP públicos no lote do Azure está atualmente em visualização pública para as seguintes regiões: França central, Ásia Oriental, Oeste EUA Central, Sul EUA Central, oeste dos EUA 2, leste dos EUA, Europa Setentrional, leste dos EUA 2, EUA Central, Europa Ocidental, norte EUA Central, oeste dos EUA, leste da Austrália, leste do Japão, oeste do Japão.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

- **Autenticação**. Para usar um pool sem endereços IP públicos dentro de uma [rede virtual](./batch-virtual-network.md), a API do cliente de lote deve usar a autenticação Azure Active Directory (AD). O suporte ao Lote do Azure para o Azure AD está documentado em [Autenticar soluções do serviço Lote com o Active Directory](batch-aad-auth.md). Se você não estiver criando o pool em uma rede virtual, a autenticação do Azure AD ou a autenticação baseada em chave poderão ser usadas.

- **Uma rede virtual do Azure**. Se você estiver criando seu pool em uma [rede virtual](batch-virtual-network.md), siga estes requisitos e configurações. Para preparar uma rede virtual com uma ou mais sub-redes com antecedência, você pode usar o portal do Azure, o Azure PowerShell, a interface de linha de comando (CLI) do Azure ou outros métodos.
  - A VNet deve estar na mesma assinatura e região da conta do Lote que você usa para criar o pool.
  - A sub-rede especificada para o pool deve ter endereços IP não atribuídos suficientes para acomodar o número de VMs direcionadas para o pool, ou seja, a soma das propriedades `targetDedicatedNodes` e `targetLowPriorityNodes` do pool. Se a sub-rede não tiver endereços IP não atribuídos suficientes, o pool alocará parcialmente os nós de computação e ocorrerá um erro de redimensionamento.
  - Você deve desabilitar o serviço de vínculo privado e as políticas de rede do ponto de extremidade. Isso pode ser feito usando CLI do Azure: ```az network vnet subnet update --vnet-name <vnetname> -n <subnetname> --resouce-group <resourcegroup> --disable-private-endpoint-network-policies --disable-private-link-service-network-policies```

> [!IMPORTANT]
> Para cada nó de 100 ou de baixa prioridade dedicado, o lote aloca um serviço de link privado e um balanceador de carga. Esses recursos são limitados pelas [cotas de recursos](../azure-resource-manager/management/azure-subscription-service-limits.md) da assinatura. Para grandes pools, talvez seja necessário [solicitar um aumento de cota](batch-quota-limit.md#increase-a-quota) para um ou mais desses recursos. Além disso, nenhum bloqueio de recurso deve ser aplicado a qualquer recurso criado pelo lote, pois isso impede a limpeza de recursos como resultado de ações iniciadas pelo usuário, como a exclusão de um pool ou o redimensionamento para zero.

## <a name="current-limitations"></a>Limitações atuais

1. Os pools sem endereços IP públicos devem usar a configuração de máquina virtual e não a configuração de serviços de nuvem.
1. A [configuração do ponto de extremidade personalizado](pool-endpoint-configuration.md) para nós de computação do lote não funciona com pools sem endereços IP públicos.
1. Como não há endereços IP públicos, você não pode [usar seus próprios endereços IP públicos especificados](create-pool-public-ip.md) com esse tipo de pool.

## <a name="create-a-pool-without-public-ip-addresses-in-the-azure-portal"></a>Criar um pool sem endereços IP públicos no portal do Azure

1. Navegue até sua conta do Lote no portal do Azure.
1. Na janela **configurações** à esquerda, selecione **pools**.
1. Na janela **pools** , selecione **Adicionar**.
1. Na janela **Adicionar pool**, selecione a opção que você deseja usar na lista suspensa **Tipo de imagem**.
1. Selecione o **Publicador/oferta/SKU** correto da sua imagem.
1. Especifique as configurações necessárias restantes, incluindo o **tamanho do nó**, os **nós dedicados de destino** e os **nós de baixa prioridade**, bem como as configurações opcionais desejadas.
1. Opcionalmente, selecione uma rede virtual e uma sub-rede que você deseja usar. Essa rede virtual deve estar no mesmo grupo de recursos que o pool que você está criando.
1. Em **tipo de provisionamento de endereço IP**, selecione **NoPublicIPAddresses**.

![Captura de tela do botão Adicionar pool com NoPublicIPAddresses selecionado.](./media/batch-pool-no-public-ip-address/create-pool-without-public-ip-address.png)

## <a name="use-the-batch-rest-api-to-create-a-pool-without-public-ip-addresses"></a>Usar a API REST do lote para criar um pool sem endereços IP públicos

O exemplo a seguir mostra como usar a [API REST do lote do Azure](/rest/api/batchservice/pool/add) para criar um pool que usa endereços IP públicos.

### <a name="rest-api-uri"></a>URI da API REST

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

### <a name="request-body"></a>Corpo da solicitação

```json
"pool": {
     "id": "pool2",
     "vmSize": "standard_a1",
     "virtualMachineConfiguration": {
          "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.040-LTS"
          },
     "nodeAgentSKUId": "batch.node.ubuntu 16.04"
     }
     "networkConfiguration": {
          "subnetId": "/subscriptions/<your_subscription_id>/resourceGroups/<your_resource_group>/providers/Microsoft.Network/virtualNetworks/<your_vnet_name>/subnets/<your_subnet_name>",
          "publicIPAddressConfiguration": {
               "provision": "NoPublicIPAddresses"
          }
     },
     "resizeTimeout": "PT15M",
     "targetDedicatedNodes": 5,
     "targetLowPriorityNodes": 0,
     "taskSlotsPerNode": 3,
     "taskSchedulingPolicy": {
          "nodeFillType": "spread"
     },
     "enableAutoScale": false,
     "enableInterNodeCommunication": true,
     "metadata": [
    {
      "name": "myproperty",
      "value": "myvalue"
    }
       ]
}
```

## <a name="outbound-access-to-the-internet"></a>Acesso de saída à Internet

Em um pool sem endereços IP públicos, suas máquinas virtuais não poderão acessar a Internet pública, a menos que você configure a configuração de rede adequadamente, como usando [NAT de rede virtual](../virtual-network/nat-overview.md). Observe que o NAT só permite o acesso de saída à Internet a partir das máquinas virtuais na rede virtual. Os nós de computação criados em lote não poderão ser acessados publicamente, pois não têm endereços IP públicos associados.

Outra maneira de fornecer conectividade de saída é usar uma UDR (rota definida pelo usuário). Isso permite rotear o tráfego para um computador proxy que tenha acesso à Internet pública.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como [criar pools em uma rede virtual](batch-virtual-network.md).
- Saiba como [usar pontos de extremidade privados com contas do lote](private-connectivity.md).
