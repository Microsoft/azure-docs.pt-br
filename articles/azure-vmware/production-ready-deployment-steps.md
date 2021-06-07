---
title: Planejando a implantação da Solução VMware no Azure
description: Este artigo descreve um fluxo de trabalho de implantação da Solução VMware no Azure.  O resultado final é um ambiente pronto para a criação e a migração da VM (máquina virtual).
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 2ded5d706ab71b3880633cd324fb366d0a1bccbe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584628"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>Planejando a implantação da Solução VMware no Azure

Este artigo oferece o processo de planejamento para identificar e coletar as informações que você usará durante a implantação. Ao planejar sua implantação, lembre-se de documentar as informações coletadas para facilitar a referência durante a implantação.

As etapas descritas neste início rápido fornecem um ambiente pronto para produção para a criação e migração de VMs (máquinas virtuais). 

>[!IMPORTANT]
>Antes de criar o recurso da Solução VMware no Azure, siga o artigo [Como habilitar o recurso da Solução VMware no Azure](enable-azure-vmware-solution.md) para enviar um tíquete de suporte para alocar seus hosts. Depois que a equipe de suporte receber sua solicitação, levará até cinco dias úteis para confirmar a solicitação e alocar os hosts. Se você tiver uma nuvem privada da Solução VMware no Azure existente e quiser mais hosts alocados, deverá passar pelo mesmo processo. 

## <a name="subscription"></a>Subscription

Identifique a assinatura que você planeja usar para implantar a Solução VMware no Azure.  Você pode criar uma assinatura ou reutilizar uma existente.

>[!NOTE]
>A assinatura precisa ser associada a um plano do Azure do Provedor de Soluções de Nuvem ou o Contrato Enterprise da Microsoft. Para obter mais informações, confira [Como habilitar o recurso de Solução VMware no Azure](enable-azure-vmware-solution.md).

## <a name="resource-group"></a>Resource group

Identifique o grupo de recursos que você deseja usar para a Solução VMware no Azure.  Em geral, um grupo de recursos é criado especificamente para a Solução VMware no Azure, mas você pode usar um grupo de recursos existente.

## <a name="region"></a>Região

Identifique a região que você deseja que a Solução VMware no Azure seja implantada.  Para obter mais informações, veja o [Guia de produtos do Azure disponíveis por região](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware).

## <a name="resource-name"></a>Nome do recurso

Defina o nome do recurso que você usará durante a implantação.  O nome do recurso é um nome amigável e descritivo que você dará à nuvem privada da Solução VMware no Azure.

>[!IMPORTANT]
>O nome não pode ultrapassar 40 caracteres. Se o nome exceder esse limite, você não poderá criar endereços IP públicos para uso com a nuvem privada. 

## <a name="size-hosts"></a>Dimensionar hosts

Identifique o tamanho dos hosts que você deseja usar ao implantar a Solução VMware no Azure.  Para obter uma lista completa, confira a documentação [Nuvens privadas e clusters da Solução VMware no Azure](concepts-private-clouds-clusters.md#hosts).

## <a name="number-of-clusters-and-hosts"></a>Número de clusters e hosts

A primeira implantação de Solução VMware no Azure que você realizar consistirá em uma nuvem privada contendo apenas um cluster. Para a implantação, você precisará definir o número de hosts que deseja implantar no primeiro cluster.

>[!NOTE]
>O número mínimo de hosts por clusters é três, e o máximo é 16. O número máximo de clusters por nuvem privada é quatro. 

Para obter mais informações, confira a documentação [Nuvem privada e clusters da Solução VMware no Azure](concepts-private-clouds-clusters.md#clusters).

>[!TIP]
>Você sempre poderá estender o cluster e adicionar mais clusters posteriormente se precisar exceder o número da implantação inicial.

## <a name="ip-address-segment-for-private-cloud-management"></a>Segmento de endereço IP para gerenciamento de nuvem privada

A primeira etapa do planejamento da implantação é a segmentação de IP. A Solução VMware no Azure requer uma rede CIDR /22. Esse espaço de endereços é gravado em segmentos de rede menores (sub-redes) e é usado para segmentos de gerenciamento de Soluções VMware no Azure, incluindo vCenter, funcionalidade vMotion, NSX-T e HCX do VMware. A visualização abaixo destaca onde esse segmento será usado.

O bloco de endereço dessa rede CIDR /22 não deverá se sobrepor a nenhum segmento de rede existente que você já tenha no local ou no Azure.

**Exemplo:** 10.0.0.0/22

Para obter um detalhamento de como a rede CIDR /22 é dividida por [Lista de verificação de planejamento de rede](tutorial-network-checklist.md#routing-and-subnet-considerations) de nuvem privada.

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Identificar – segmento de endereço IP" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>Segmento de endereço IP para cargas de trabalho de máquina virtual

Assim como em qualquer ambiente VMware, as máquinas virtuais precisam se conectar a um segmento de rede. Na Solução VMware no Azure, há dois tipos de segmentos, segmentos estendidos L2 estendidos (discutidos posteriormente) e segmentos de rede NSX-T. À medida que a implantação de produção da Solução VMware no Azure se expande, há muitas vezes uma combinação de segmentos L2 estendidos de segmentos de rede locais e de NSX-T local. Para planejar a implantação inicial, na Solução VMware no Azure, identifique um segmento de rede (rede IP). Essa rede não pode se sobrepor a nenhum segmento de rede local nem dentro do restante do Azure e não pode estar dentro do segmento de rede /22 definido anteriormente.

Esse segmento de rede é usado principalmente para fins de teste durante a implantação inicial.

>[!NOTE]
>Essa rede ou redes não serão necessárias durante a implantação. Elas são criadas como uma etapa pós-implantação.
  
**Exemplo:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Identificar – Segmento de endereço IP para cargas de trabalho de máquina virtual" border="false":::     

## <a name="optional-extend-your-networks"></a>(Opcional) Estender as redes

Você pode estender segmentos de rede locais para a Solução VMware no Azure e, se quiser fazer isso, identifique essas redes agora.  

Tenha em mente que:

- Se você planeja estender redes locais, essas redes devem se conectar a um [vDS (vSphere Distributed Switch)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) no ambiente do VMware local.  
- Se as redes que você deseja estender estão em um [vSphere Standard Switch](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html), elas não podem ser estendidas.

>[!NOTE]
>Essas redes são estendidas como uma etapa final da configuração, não durante a implantação.

## <a name="attach-azure-virtual-network-to-azure-vmware-solution"></a>Anexar Rede Virtual do Azure à Solução VMware no Azure

Para fornecer conectividade à Solução VMware no Azure, um ExpressRoute é criado da nuvem privada da Solução VMware no Azure para um gateway de rede virtual do ExpressRoute.

Você pode usar um gateway de rede virtual do ExpressRoute *existente* OU *novo*.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Identidade – Rede Virtual do Azure para anexar a Solução VMware no Azure" border="false":::

### <a name="use-an-existing-expressroute-virtual-network-gateway"></a>Usar um gateway de rede virtual do ExpressRoute existente

Se você planeja usar um gateway de rede virtual do ExpressRoute *existente*, o circuito ExpressRoute da Solução VMware no Azure será estabelecido como uma etapa pós-implantação. Nesse caso, deixe o campo **Rede Virtual** em branco.

Como recomendação geral, é aceitável usar um gateway de rede virtual do ExpressRoute existente. Para fins de planejamento, anote qual gateway de rede virtual do ExpressRoute você usará e então passe para a próxima etapa.

### <a name="create-a-new-expressroute-virtual-network-gateway"></a>Criar um gateway de rede virtual do ExpressRoute

Quando você criar um gateway de rede virtual do ExpressRoute *novo*, você poderá usar uma Rede Virtual do Azure existente ou criar uma.  

- Para uma rede virtual do Azure existente:
   1. Identifique uma Rede Virtual do Azure em que não haja gateways de rede virtual do ExpressRoute pré-existentes.
   2. Antes da implantação, crie um [GatewaySubnet](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet) na Rede Virtual do Azure.

- Para uma nova Rede Virtual do Azure, você pode criá-la com antecedência ou durante a implantação. Selecione o link **Criar** na lista **Rede Virtual**.

A imagem abaixo mostra a tela de implantação **Criar uma nuvem privada** com o campo **Rede Virtual** realçado.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-deployment-screen-vnet-circle.png" alt-text="Captura de tela da tela de implantação da Solução VMware no Azure com o campo Rede Virtual realçada.":::

>[!NOTE]
>Qualquer rede virtual que vai ser usada ou criada pode ser vista por seu ambiente local e pela Solução VMware no Azure, portanto, verifique se qualquer segmento IP que você usar nessa rede virtual e nas sub-redes não se sobrepõem.

## <a name="vmware-hcx-network-segments"></a>Segmentos de rede do HCX da VMware

O HCX da VMware é uma tecnologia incluída na Solução VMware no Azure. Os principais casos de uso da VMware HCX são para migrações de carga de trabalho e recuperação de desastres. Se você planeja usar algum destes cenários, é melhor planejar a rede agora.   Caso contrário, ignore e continue na próxima etapa.

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="next-steps"></a>Próximas etapas
Agora que você reuniu e documentou as informações necessárias, passe para a próxima seção para criar a nuvem privada da Solução VMware no Azure.

> [!div class="nextstepaction"]
> [Implantar a Solução VMware no Azure](deploy-azure-vmware-solution.md)
