---
title: Azure NetApp Files com a solução VMware do Azure
description: Use Azure NetApp Files com VMs de solução do Azure VMware para migrar e sincronizar dados entre servidores locais, VMs de solução do Azure VMware e infraestruturas de nuvem.
ms.topic: how-to
ms.date: 01/20/2021
ms.openlocfilehash: b4ffea23606e1829fbf713488f08bc5618180072
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430055"
---
# <a name="azure-netapp-files-with-azure-vmware-solution"></a>Azure NetApp Files com a solução VMware do Azure

Neste artigo, percorreremos as etapas de integração de Azure NetApp Files com cargas de trabalho baseadas em soluções VMware do Azure. O sistema operacional convidado será executado dentro de máquinas virtuais (VMs) que acessam volumes Azure NetApp Files. 

## <a name="azure-netapp-files-overview"></a>Visão geral de Azure NetApp Files

[Azure NetApp files](../azure-netapp-files/azure-netapp-files-introduction.md) é um serviço de terceiros do Azure para migração e execução de cargas de trabalho de arquivo empresarial mais exigentes na nuvem, incluindo bancos de dados, SAP e aplicativos de computação de alto desempenho, sem alterações de código.

### <a name="features"></a>Recursos
(Serviços em que Azure NetApp Files são usados.)

- **Conexões de Active Directory**: Azure NetApp files oferece suporte a [Active Directory Domain Services e Azure Active Directory Domain Services](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#decide-which-domain-services-to-use).

- **Protocolo de compartilhamento**: o Azure NetApp files dá suporte a protocolos SMB (Server Message Protocol) e NFS (sistema de arquivos de rede). Esse suporte significa que os volumes podem ser montados no cliente Linux e podem ser mapeados no cliente Windows.

- **Solução do Azure VMware**: compartilhamentos de Azure NetApp files podem ser montados de VMs que são criadas no ambiente da solução Azure VMware.

Azure NetApp Files está disponível em muitas regiões do Azure e dá suporte à replicação entre regiões. Para obter informações sobre Azure NetApp Files métodos de configuração, consulte [hierarquia de armazenamento do Azure NetApp files](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md).

## <a name="reference-architecture"></a>Arquitetura de referência

O diagrama a seguir ilustra uma conexão por meio do Azure ExpressRoute para uma nuvem privada da solução Azure VMware. Ele mostra o uso de um compartilhamento de Azure NetApp Files, montado em VMs de solução do Azure VMware, sendo acessado pelo ambiente da solução VMware do Azure.

![Diagrama mostrando os arquivos da NetApp para a arquitetura da solução Azure VMware.](media/net-app-files/net-app-files-topology.png)

Este artigo aborda instruções para configurar, testar e verificar o volume de Azure NetApp Files como um compartilhamento de arquivos para VMs de solução do Azure VMware. Nesse cenário, usamos o protocolo NFS. Azure NetApp Files e a solução VMware do Azure são criadas na mesma região do Azure.

## <a name="prerequisites"></a>Pré-requisitos 

> [!div class="checklist"]
> * Assinatura do Azure com Azure NetApp Files habilitado
> * Sub-rede para Azure NetApp Files
> * VM do Linux na solução VMware do Azure
> * VMs do Windows na solução VMware do Azure

## <a name="regions-supported"></a>Regiões com suporte

A lista de regiões com suporte pode ser encontrada em [produtos do Azure por região](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all).

## <a name="verify-pre-configured-azure-netapp-files"></a>Verificar Azure NetApp Files pré-configurado 

Siga as instruções detalhadas nos artigos a seguir para criar e montar Azure NetApp Files volumes em VMs de solução do Azure VMware.

- [Criar uma conta do NetApp](../azure-netapp-files/azure-netapp-files-create-netapp-account.md)
- [Configurar um pool de capacidade](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
- [Criar um volume SMB para o Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)
- [Criar um volume NFS para o Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes.md)
- [Delegar uma sub-rede ao Azure NetApp Files](../azure-netapp-files/azure-netapp-files-delegate-subnet.md)

As etapas a seguir incluem a verificação do Azure NetApp Files pré-configurado criado no nível de serviço do Azure no Azure NetApp Files Premium.

1. Na portal do Azure, em **armazenamento**, selecione **Azure NetApp files**. Uma lista de seus Azure NetApp Files configurados será mostrada. 

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Captura de tela mostrando a lista de Azure NetApp Files pré-configuradas."::: 

2. Selecione uma conta de arquivos do NetApp configurada para exibir suas configurações. Por exemplo, selecione **contoso-anf2**. 

3. Selecione **pools de capacidade** para verificar o pool configurado. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Captura de tela mostrando opções para exibir pools de capacidade e volumes de uma conta de arquivos do NetApp configurada.":::

    A página pools de capacidade é aberta mostrando a capacidade e o nível de serviço. Neste exemplo, o pool de armazenamento é configurado como 4 TiB com um nível de serviço Premium.

4. Selecione **volumes** para exibir os volumes criados no pool de capacidade. (Consulte a captura de tela anterior.)

5. Selecione um volume para exibir sua configuração.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Captura de tela mostrando os volumes criados no pool de capacidade.":::

    Uma janela é aberta mostrando os detalhes de configuração do volume.

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Captura de tela mostrando os detalhes de configuração de um volume.":::

    Você pode ver que o volume anfvolume, com um tamanho de 200 GiB, foi criado no pool de capacidade anfpool1 e exportado como um compartilhamento de arquivos NFS por meio de 10.22.3.4:/ANFVOLUME. Um IP privado da VNet (rede virtual) do Azure foi criado para Azure NetApp Files e o caminho do NFS para montar na VM. Para obter informações sobre Azure NetApp Files desempenho de volume relativo ao tamanho ("cota"), consulte [considerações de desempenho para Azure NetApp files](../azure-netapp-files/azure-netapp-files-performance-considerations.md). 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>Verificar mapeamento de compartilhamento de VM de solução Azure VMware pré-configurada

Antes de apresentar a acessibilidade do compartilhamento de Azure NetApp Files a uma VM de solução do Azure VMware, é importante entender o mapeamento de compartilhamento SMB e NFS. Somente depois de configurar os volumes SMB ou NFS, eles podem ser montados conforme documentado aqui.

- Compartilhamento SMB: Crie uma conexão de Active Directory antes de implantar um volume SMB. Os controladores de domínio especificados devem ser acessíveis pela sub-rede delegada do Azure NetApp Files para uma conexão bem-sucedida. Depois que o Active Directory estiver configurado na conta de Azure NetApp Files, ele será exibido como um item selecionável durante a criação de volumes SMB.

- Compartilhamento NFS: Azure NetApp Files contribui para criar os volumes usando NFS ou protocolo duplo (NFS e SMB). O consumo de capacidade de um volume conta contra a capacidade provisionada do pool desse volume. O NFS pode ser montado no servidor Linux usando as linhas de comando ou entradas/etc/fstab.

## <a name="use-cases-of-azure-netapp-files-with-azure-vmware-solution"></a>Casos de uso de Azure NetApp Files com a solução VMware do Azure

A seguir, temos apenas alguns casos de uso atraentes Azure NetApp Files. 
- Gerenciamento de perfil de horizonte
- Gerenciamento de perfil Citrix
- Gerenciamento de perfil de Serviços de Área de Trabalho Remota
- Compartilhamentos de arquivos na solução VMware do Azure

## <a name="next-steps"></a>Próximas etapas

Depois de integrar Azure NetApp Files com suas cargas de trabalho de solução do Azure VMware, talvez você queira saber mais sobre:

- [Limites de recursos para Azure NetApp files](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits).
- [Diretrizes para Azure NetApp files planejamento de rede](../azure-netapp-files/azure-netapp-files-network-topologies.md).
- [Replicação entre regiões de volumes Azure NetApp files](../azure-netapp-files/cross-region-replication-introduction.md). 
- [Perguntas frequentes sobre Azure NetApp files](../azure-netapp-files/azure-netapp-files-faqs.md).
