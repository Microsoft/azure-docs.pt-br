---
title: Implantar a rede de contêiner de rede virtual do Azure | Microsoft Docs
description: Saiba como implantar o plug-in CNI (interface de rede de contêiner de rede virtual) do Azure para clusters kubernetes.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/18/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: b95b3cfdf8fea6e31015d945566803569b4ba064
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222914"
---
# <a name="deploy-the-azure-virtual-network-container-network-interface-plug-in"></a>Implantar o plug-in do adaptador de rede de contêiner da Rede Virtual do Azure

O plug-in de CNI (adaptador de rede de contêiner) de Rede Virtual do Azure é instalado em uma máquina virtual do Azure e traz recursos de rede virtual para contêineres do Docker e Pods do Kubernetes. Para saber mais sobre o plug-in, consulte [Habilitar contêineres para usar os recursos de Rede Virtual do Azure](container-networking-overview.md). Além disso, o plug-in pode ser usado com o AKS (Serviço de Kubernetes do Azure) escolhendo a opção [Rede Avançada](../aks/configure-azure-cni.md?toc=%2fazure%2fvirtual-network%2ftoc.json), que coloca automaticamente os contêineres do AKS em uma rede virtual.

## <a name="deploy-plug-in-for-acs-engine-kubernetes-cluster"></a>Implantar o plug-in para o cluster do Kubernetes do Mecanismo de ACS

O mecanismo de ACS implanta um cluster de Kubernetes com um modelo do Azure Resource Manager. A configuração do cluster é especificada em um arquivo JSON que é passado para a ferramenta ao gerar o modelo. Para saber mais sobre a lista completa de configurações de cluster com suporte e suas descrições, consulte [Microsoft Azure mecanismo de serviço de contêiner-definição de cluster](https://github.com/Azure/acs-engine/blob/master/docs/clusterdefinition.md). O plug-in é o plug-in de rede padrão para clusters criados usando o Mecanismo de ACS. As definições de configuração de rede a seguir são importantes ao configurar o plug-in:

  | Configuração                              | Descrição                                                                                                           |
  |--------------------------------------|------------------------------------------------------------------------------------------------------                 |
  | firstConsecutiveStaticIP             | O endereço IP é alocado para o nó Mestre. Essa é uma configuração obrigatória.                                     |
  | clusterSubnet em kubernetesConfig | CIDR da sub-rede da rede virtual em que o cluster é implantado e da qual os endereços IP são alocados aos Pods   |
  | vnetSubnetId em masterProfile     | Especifica a ID de recurso do Azure Resource Manager da sub-rede em que o cluster deve ser implantado                    |
  | vnetCidr                             | CIDR da rede virtual em que o cluster é implantado                                                             |
  | max-Pods em kubeletConfig         | Número máximo de Pods em cada máquina virtual de agente. Para o plug-in, o padrão é 30. Você pode especificar até 250  |

### <a name="example-configuration"></a>Configuração de exemplo

O exemplo de json a seguir é para um cluster com as seguintes propriedades:
-   1 nó Mestre e 2 nós de Agente 
-   É implantado em uma sub-rede denominada *KubeClusterSubnet* (10.0.0.0/20), com nós Mestre e de Agente residindo nela.

```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
        "clusterSubnet": "10.0.0.0/20" --> Subnet allocated for the cluster
      }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "ACSKubeMaster",
      "vmSize": "Standard_A2",
      "vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<Vnet Name>/subnets/KubeClusterSubnet",
      "firstConsecutiveStaticIP": "10.0.1.50", --> IP address allocated to the Master node
"vnetCidr": "10.0.0.0/16" --> Virtual network address space
    },
    "agentPoolProfiles": [
      {
        "name": "k8sagentpoo1",
        "count": 2,
        "vmSize": "Standard_A2_v2",
"vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<VNet Name>/subnets/KubeClusterSubnet",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
    "linuxProfile": {
      "adminUsername": "KubeServerAdmin",
      "ssh": {
        "publicKeys": [
          {…}
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "dd438987-aa12-4754-b47d-375811889714",
      "secret": "azure123"
    }
  }
}
```

## <a name="deploy-plug-in-for-a-kubernetes-cluster"></a>Implantar o plug-in para o cluster do Kubernetes

Conclua as seguintes etapas para instalar o plug-in em todas as máquinas virtuais do Azure em um cluster do Kubernetes:

1. [Baixe e instale o plug-in](#download-and-install-the-plug-in).
2. Pré-aloque um pool de endereços IP de rede virtual em cada máquina virtual do qual os endereços IP serão atribuídos aos Pods. Todas as máquinas virtuais do Azure vêm com um endereço IP privado de rede virtual primário em cada adaptador de rede. O pool de endereços IP de Pods é adicionado como os endereços secundários (*ipconfigs*) no adaptador de rede de máquina virtual, usando uma das seguintes opções:

   - **CLI**: [atribuir vários endereços IP usando a CLI do Azure](virtual-network-multiple-ip-addresses-cli.md)
   - **PowerShell**: [atribuir vários endereços IP usando o PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
   - **Portal**: [atribuir vários endereços IP usando o portal do Azure](virtual-network-multiple-ip-addresses-portal.md)
   - **Modelo do Azure Resource Manager**: [atribuir vários endereços IP usando modelos](./template-samples.md)

   Certifique-se de adicionar endereços IP suficientes para todos os Pods que você espera ativar na máquina virtual.

3. Selecione o plug-in para o fornecimento de rede para seu cluster passando para o Kubelet a opção de linha de comando `–network-plugin=cni` durante a criação do cluster. O Kubernetes, por padrão, procura o plug-in e o arquivo de configuração nos diretórios em que eles já estão instalados.
4. Se você quiser que seus Pods acessem a Internet, adicione a seguinte regra *iptables* às máquinas virtuais do Linux ao tráfego de Internet de Nat de origem. No exemplo a seguir, o intervalo de IP especificado é 10.0.0.0/8.

   ```bash
   iptables -t nat -A POSTROUTING -m iprange ! --dst-range 168.63.129.16 -m
   addrtype ! --dst-type local ! -d 10.0.0.0/8 -j MASQUERADE
   ```

   O tráfego de NAT de regras que não é destinado aos intervalos de IP especificados. A suposição é que todo o tráfego fora dos intervalos anteriores é tráfego da Internet. Você pode optar por especificar os intervalos de IP da rede virtual da máquina virtual, como de redes virtuais emparelhadas e redes locais.

   Máquinas virtuais do Windows automaticamente originam o tráfego de NAT que tem um destino fora da sub-rede à qual a máquina virtual pertence. Não é possível especificar intervalos IP personalizados.

Depois de concluir as etapas anteriores, os Pods ativados nas máquinas virtuais do Agente do Kubernetes recebem automaticamente endereços IP privados da rede virtual.

## <a name="deploy-plug-in-for-docker-containers"></a>Implantar o plug-in para contêineres do Docker

1. [Baixe e instale o plug-in](#download-and-install-the-plug-in).
2. Crie contêineres do Docker com o seguinte comando:

   ```
   ./docker-run.sh \<container-name\> \<container-namespace\> \<image\>
   ```

Os contêineres automaticamente começam a receber endereços IP do pool alocado. Se quiser balancear a carga do tráfego para os contêineres do Docker, eles deverão ser colocados atrás de um balanceador de carga de software e você deverá configurar uma investigação do balanceador de carga, da mesma forma que cria uma política e investiga uma máquina virtual.

### <a name="cni-network-configuration-file"></a>Arquivo de configuração de rede CNI

O arquivo de configuração de rede CNI é descrito no formato JSON. Ele está, por padrão, presente na `/etc/cni/net.d` para Linux e `c:\cni\netconf` para Windows. O arquivo especifica a configuração do plug-in e é diferente para o Windows e para o Linux. O json a seguir é um arquivo de configuração do Linux de exemplo, seguido por uma explicação para algumas das configurações de chave. Você não precisa fazer nenhuma alteração no arquivo:

```json
{
       "cniVersion":"0.3.0",
       "name":"azure",
       "plugins":[
          {
             "type":"azure-vnet",
             "mode":"bridge",
             "bridge":"azure0",
             "ipam":{
                "type":"azure-vnet-ipam"
             }
          },
          {
             "type":"portmap",
             "capabilities":{
                "portMappings":true
             },
             "snat":true
          }
       ]
}
```

#### <a name="settings-explanation"></a>Explicação das configurações

- **cniVersion**: os plug-ins de CNI da Rede Virtual do Azure são compatíveis com as versões 0.3.0 e 0.3.1 da [especificação de CNI](https://github.com/containernetworking/cni/blob/master/SPEC.md).
- **name**: nome da rede. Esta propriedade pode ser definida para qualquer valor exclusivo.
- **type**: nome do plug-in de rede. Definido como *azure-vnet*.
- **mode**: modo operacional. Esse campo é opcional. O único modo compatível é "bridge". Para saber obter mais informações, consulte os [modos operacionais](https://github.com/Azure/azure-container-networking/blob/master/docs/network.md).
- **bridge**: nome da ponte que será usada para conectar contêineres a uma rede virtual. Esse campo é opcional. Se omitido, o plug-in escolhe automaticamente um nome exclusivo, com base no índice de interface mestre.
- **ipam type**: nome do plug-in de IPAM. Sempre definido como *azure-vnet-ipam*.

## <a name="download-and-install-the-plug-in"></a>Baixar e instalar o plug-in

Baixe o plug-in do [GitHub](https://github.com/Azure/azure-container-networking/releases). Baixe a versão mais recente para a plataforma que você está usando:

- **Linux**: [Azure-vnet-CNI-linux-amd64- \<version no.\> . tgz](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-linux-amd64-v1.0.12-rc3.tgz)
- **Windows**: [Azure-vnet-CNI-Windows-AMD64- \<version no.\> . zip](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-windows-amd64-v1.0.12-rc3.zip)

Copie o script de instalação para [Linux](https://github.com/Azure/azure-container-networking/blob/master/scripts/install-cni-plugin.sh) ou [Windows](https://github.com/Azure/azure-container-networking/blob/master/scripts/Install-CniPlugin.ps1) em seu computador. Salve o script em um diretório `scripts` no seu computador e nomeie o arquivo como `install-cni-plugin.sh` para Linux ou `install-cni-plugin.ps1` para Windows. Para instalar o plug-in, execute o script apropriado para sua plataforma, especificando a versão do plug-in que você está usando. Por exemplo, você pode especificar *v1.0.12-rc3*:

   ```bash
   \$scripts/install-cni-plugin.sh [version]
   ```

   ```powershell
   scripts\\ install-cni-plugin.ps1 [version]
   ```

O script instala o plug-in em `/opt/cni/bin` para Linux e `c:\cni\bin` para Windows. Os plug-ins instalados vêm com um arquivo de configuração de rede simples que funciona após a instalação. Ele não precisa ser atualizado. Para saber mais sobre as configurações no arquivo, consulte [Arquivo de configuração de rede CNI](#cni-network-configuration-file).