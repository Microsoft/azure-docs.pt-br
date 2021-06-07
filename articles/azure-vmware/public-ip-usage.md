---
title: Como usar a funcionalidade de IP público na solução VMware do Azure
description: Este artigo explica como usar a funcionalidade de IP público na WAN virtual do Azure.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 794e24e531d464adf58d5a06b5a411ada18c4a60
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023649"
---
# <a name="how-to-use-the-public-ip-functionality-in-azure-vmware-solution"></a>Como usar a funcionalidade de IP público na solução VMware do Azure

O IP público é um novo recurso na conectividade da solução Azure VMware. Ele torna os recursos, como servidores Web, VMs (máquinas virtuais) e hosts acessíveis por meio de uma rede pública. 

Você habilita o acesso público à Internet de duas maneiras. 

- Os aplicativos podem ser hospedados e publicados no balanceador de carga do gateway de aplicativo para tráfego HTTP/HTTPS.
- Publicado por meio de recursos de IP público na WAN virtual do Azure.

Como parte da implantação de nuvem privada da solução Azure VMware, após habilitar a funcionalidade de IP público, os componentes necessários com a automação são criados e habilitados:

-  WAN Virtual

-  Hub WAN virtual com conectividade de ExpressRoute

-  Serviços de firewall do Azure com IP público

Este artigo fornece detalhes sobre como você pode usar a funcionalidade de IP público na WAN virtual.

## <a name="prerequisites"></a>Pré-requisitos

- Ambiente da solução VMware do Azure
- Um servidor Webem execução no ambiente da solução VMware do Azure.
- Um novo intervalo de IP não sobreposto para a implantação do Hub WAN virtual, normalmente um `/24` .

## <a name="reference-architecture"></a>Arquitetura de referência

:::image type="content" source="media/public-ip-usage/public-ip-architecture-diagram.png" alt-text="Diagrama de arquitetura de IP público" border="false" lightbox="media/public-ip-usage/public-ip-architecture-diagram.png":::

O diagrama de arquitetura mostra um servidor Web hospedado no ambiente de solução VMware do Azure e configurado com endereços IP privados RFC1918.  O serviço Web é disponibilizado para a Internet por meio da funcionalidade de IP público de WAN virtual.  O IP público normalmente é um NAT de destino traduzido no firewall do Azure. Com as regras do DNAT, a política de firewall traduz as solicitações de endereço IP público para um endereço privado (servidor WebServer) com uma porta.

As solicitações de usuário atingem o firewall em um IP público que, por sua vez, é convertido para o IP privado usando regras DNAT no firewall do Azure. O Firewall verifica a tabela NAT e, se a solicitação corresponder a uma entrada, ela encaminha o tráfego para o endereço e a porta traduzidos no ambiente da solução VMware do Azure.

O servidor Web recebe a solicitação e responde com as informações ou a página solicitada ao firewall e, em seguida, o firewall encaminha as informações para o usuário no endereço IP público.

## <a name="test-case"></a>Caso de teste
Nesse cenário, você publicará o servidor webiis na Internet. Use o recurso IP público na solução VMware do Azure para publicar o site em um endereço IP público.  Você também configurará as regras de NAT no firewall e acessará o recurso de solução do Azure VMware (VMs com um servidor Web) com o IP público.

>[!TIP]
>Para habilitar o tráfego de saída, você deve definir a configuração de segurança > tráfego de Internet para o **Firewall do Azure**.

## <a name="deploy-virtual-wan"></a>Implantar uma WAN Virtual

1. Entre no portal do Azure e, em seguida, pesquise e selecione **solução Azure VMware**.

1. Selecione a nuvem privada da solução Azure VMware.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-resource.png" alt-text="Captura de tela da nuvem privada da solução Azure VMware." border="true" lightbox="media/public-ip-usage/avs-private-cloud-resource.png":::

1. Em **gerenciar**, selecione **conectividade**.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-manage-menu.png" alt-text="Captura de tela da seção conectividade." border="true" lightbox="media/public-ip-usage/avs-private-cloud-manage-menu.png":::

1. Selecione a guia **IP público** e, em seguida, selecione **Configurar**.

   :::image type="content" source="media/public-ip-usage/connectivity-public-ip-tab.png" alt-text="Captura de tela que mostra onde começar a configurar o IP público" border="true" lightbox="media/public-ip-usage/connectivity-public-ip-tab.png":::

1. Aceite os valores padrão ou altere-os e, em seguida, selecione **criar**.

   - Grupo de recursos de WAN virtual

   - Nome da WAN virtual

   - Bloco de endereço de Hub virtual (usando novo intervalo de IP não sobreposto)

   - Número de IPs públicos (1-100)

Leva cerca de uma hora para concluir a implantação de todos os componentes. Essa implantação só precisa ocorrer uma vez para dar suporte a todos os IPs públicos futuros para esse ambiente de solução do Azure VMware.  

>[!TIP]
>Você pode monitorar o status na área de **notificação** . 

## <a name="view-and-add-public-ip-addresses"></a>Exibir e adicionar endereços IP públicos

Podemos verificar e adicionar mais endereços IP públicos seguindo as etapas abaixo.

1. No portal do Azure, procure e selecione **Firewall**.

1. Selecione um firewall implantado e selecione **visitar o Gerenciador de firewall do Azure para configurar e gerenciar esse firewall**.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="Captura de tela que mostra a opção de configurar e gerenciar o firewall" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. Selecione **hubs virtuais protegidos** e, na lista, selecione um hub virtual.

   :::image type="content" source="media/public-ip-usage/select-virtual-hub.png" alt-text="Captura de tela do Gerenciador de firewall" lightbox="media/public-ip-usage/select-virtual-hub.png":::

1. Na página Hub virtual, selecione **configuração de IP público** e para adicionar mais endereço IP público e, em seguida, selecione **Adicionar**. 

   :::image type="content" source="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png" alt-text="Captura de tela de como adicionar uma configuração de IP público no firewall Manager" border="true" lightbox="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png":::

1. Forneça o número de IPs necessários e selecione **Adicionar**.

   :::image type="content" source="media/public-ip-usage/add-number-of-ip-addresses-required.png" alt-text="Captura de tela para adicionar um número especificado de configurações de IP público" border="true":::


## <a name="create-firewall-policies"></a>Criar políticas de firewall

Depois que todos os componentes forem implantados, você poderá vê-los no grupo de recursos adicionado. A próxima etapa é adicionar uma política de firewall.

1. No portal do Azure, procure e selecione **Firewall**.

1. Selecione um firewall implantado e selecione **visitar o Gerenciador de firewall do Azure para configurar e gerenciar esse firewall**.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="Captura de tela que mostra a opção de configurar e gerenciar o firewall" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. Selecione **políticas de firewall do Azure** e, em seguida, selecione **criar política de firewall do Azure**.

   :::image type="content" source="media/public-ip-usage/create-firewall-policy.png" alt-text="Captura de tela de como criar uma política de firewall no Gerenciador de firewall" border="true" lightbox="media/public-ip-usage/create-firewall-policy.png":::

1. Na guia **noções básicas** , forneça os detalhes necessários e selecione **Avançar: configurações de DNS**. 

1. Na guia **DNS** , selecione **desabilitar** e, em seguida, selecione **Avançar: regras**.

1. Selecione **Adicionar uma coleção de regras**, forneça os detalhes abaixo e selecione **Adicionar** e, em seguida, **Avançar: inteligência contra ameaças**.

   -  Name
   -  Tipo de coleção de regras-DNAT
   -  Prioridade
   -  Ação de coleção de regras – permitir
   -  Nome da regra
   -  Tipo de origem- **IPAddress**
   -  Original **\***
   -  Protocolo – **TCP**
   -  Porta de destino – **80**
   -  Tipo de destino – **endereço IP**
   -  Destino – **endereço IP público**
   -  Endereço traduzido – **endereço IP privado do servidor Web da solução do Azure VMware**
   -  Porta traduzida- **porta do servidor Web da solução Azure VMware**

1. Deixe o valor padrão e, em seguida, selecione **Avançar: hubs**.

1. Selecione **associar Hub virtual**.

1. Selecione um Hub na lista e selecione **Adicionar**.

   :::image type="content" source="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png" alt-text="Captura de tela que mostra os hubs selecionados que serão convertidos em hubs virtuais protegidos." border="true" lightbox="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png":::

1. Selecione **Avançar: Marcas**. 

1. Adicional Crie pares de nome e valor para categorizar seus recursos. 

1. Selecione **Avançar: revisar + criar** e, em seguida, selecione **criar**.

## <a name="limitations"></a>Limitações

Você pode ter 100 IPs públicos por nuvem privada.

## <a name="next-steps"></a>Próximas etapas

Agora que você já abordou como usar a funcionalidade de IP público na solução VMware do Azure, talvez queira saber mais sobre:

- Usando endereços IP públicos com a [Wan virtual do Azure](../virtual-wan/virtual-wan-about.md).
- [Criando um túnel IPSec na solução VMware do Azure](create-ipsec-tunnel.md).
