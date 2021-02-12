---
title: Arquitetura de recuperação de desastres do servidor físico no Azure Site Recovery
description: Este artigo fornece uma visão geral dos componentes e da arquitetura usados durante a recuperação de desastres de servidores físicos locais para o Azure com o serviço Azure Site Recovery.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 966636e269043d81912b552711635f34b412f22f
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654720"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Servidor físico para a arquitetura de recuperação de desastre do Azure

Este artigo descreve a arquitetura e os processos usados quando você faz a replicação, o failover e a recuperação de servidores Windows e Linux físicos entre um site local e o Azure usando o serviço [Azure Site Recovery](site-recovery-overview.md).

## <a name="architectural-components"></a>Componentes de arquitetura

A tabela e o gráfico a seguir fornecem uma exibição de alto nível dos componentes usados para replicação de servidor físico para o Azure.

| **Componente** | **Requisito** | **Detalhes** |
| --- | --- | --- |
| **Azure** | Uma assinatura do Azure e uma rede do Azure. | Os dados replicados de computadores físicos locais são armazenados em Azure Managed disks. As VMs do Azure são criadas com os dados replicados quando você faz um failover do local para o Azure. As VMs do Azure se conectam à rede virtual do Azure quando são criadas. |
| **Servidor de processo** | Instalado por padrão juntamente com o servidor de configuração. | Atua como um gateway de replicação. Recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para o Armazenamento do Azure.<br/><br/> O servidor em processo também instala o serviço de Mobilidade nos servidores que você deseja replicar.<br/><br/> À medida que a implantação cresce, você pode adicionar outros servidores de processo separados para lidar com volumes maiores de tráfego de replicação. |
| **Servidor de destino mestre** | Instalado por padrão juntamente com o servidor de configuração. | Lida com os dados de replicação durante o failback do Azure.<br/><br/> Para grandes implantações, você pode adicionar um servidor de destino mestre separado adicional para failback. |
| **Servidores replicados** | O serviço de Mobilidade é instalado em cada servidor que você deseja replicar. | É recomendável que você permita a instalação automática do servidor em processo. Ou, você pode instalar o serviço manualmente ou usar um método de implantação automatizado, como Configuration Manager. |

**Arquitetura do físico para o Azure**

![Componentes](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="set-up-outbound-network-connectivity"></a>Configurar a conectividade de rede de saída

Para que Site Recovery funcionem conforme o esperado, você precisa modificar a conectividade de rede de saída para permitir que seu ambiente seja replicado.

> [!NOTE]
> O Site Recovery não dá suporte ao uso de um proxy de autenticação para controlar a conectividade de rede.

### <a name="outbound-connectivity-for-urls"></a>Conectividade de saída para URLs

Caso esteja usando um proxy de firewall baseado em URL para controlar a conectividade de saída, permita acesso a estas URLs:

| **Nome**                  | **Comercial**                               | **Governo**                                 | **Descrição** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Armazenamento                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Permite que os dados sejam gravados da VM para a conta de armazenamento de cache da região de origem. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Fornece autorização e autenticação para as URLs do serviço Site Recovery. |
| Replicação               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Permite que a VM se comunique com o serviço Site Recovery. |
| Barramento de Serviço               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Permite que a VM grave o monitoramento do Site Recovery e os dados de diagnóstico. |


## <a name="replication-process"></a>Processo de replicação

1. Configure a implantação, incluindo os componentes locais e os componentes do Azure. No cofre dos Serviços de Recuperação, você especifica a origem e o destino da replicação, configura o servidor de configuração, cria uma política de replicação e habilita a replicação.
1. Os computadores são replicados usando a política de replicação e uma cópia inicial dos dados do servidor é replicada para o armazenamento do Azure.
1. Após a conclusão da replicação inicial, a replicação de alterações delta para o Azure é iniciada. As alterações controladas para um computador são mantidas em um arquivo com a extensão _. HRL_ .
   - Os computadores se comunicam com o servidor de configuração na porta HTTPS 443 de entrada, para o gerenciamento de replicação.
   - Os computadores enviam dados de replicação para o servidor de processo na porta HTTPS 9443 de entrada (pode ser modificado).
   - O servidor de configuração orquestra o gerenciamento de replicação com o Azure pela porta HTTPS 443 de saída.
   - O servidor de processo recebe dados de computadores de origem, otimiza-os e criptografa-os e envia-os para o armazenamento do Azure pela porta HTTPS 443 de saída.
   - Se você habilitar a consistência de várias VMs, as máquinas virtuais no grupo de replicação se comunicarão entre si pela porta 20004. Várias VMS serão usadas se você agrupar vários computadores em grupos de replicação que compartilham pontos de recuperação consistentes com o aplicativo e com falhas ao realizar o failover. Esses grupos serão úteis se os computadores estiverem executando a mesma carga de trabalho e precisarem ser consistentes.
1. O tráfego é replicado para pontos de extremidade públicos do armazenamento do Azure, pela Internet. Como alternativa, você pode usar o [emparelhamento público](../expressroute/about-public-peering.md) do Azure ExpressRoute.

   > [!NOTE]
   > A replicação não tem suporte em uma VPN site a site de um site local ou um [emparelhamento privado](concepts-expressroute-with-site-recovery.md#on-premises-to-azure-replication-with-expressroute)do Azure ExpressRoute.

**Processo de replicação do físico para o Azure**

![Processo de replicação](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processo de failover e failback

Depois que a replicação é configurada, você pode executar uma análise de recuperação de desastre (failover de teste) para verificar se tudo funciona conforme o esperado. Em seguida, você pode fazer failover e failback conforme necessário. Considere os itens a seguir:

- Não há suporte para failover planejado.
- O failback para uma VM VMware local é necessário. Você precisa de uma infraestrutura do VMware local, mesmo quando você Replica servidores físicos locais para o Azure.
- Faça failover de uma única máquina ou crie planos de recuperação para fazer failover de várias máquinas virtuais juntas.
- Ao executar um failover, as VMs do Azure são criadas com base nos dados replicados no Armazenamento do Azure.
- Depois que o failover inicial é disparado, você o confirma para começar a acessar a carga de trabalho da VM do Azure.
- Quando o site primário local estiver disponível novamente, você poderá executar o failback.
- Configure uma infraestrutura de failback que inclua:
  - **Servidor de processo temporário no Azure**: para fazer failback do Azure, você configura uma VM do Azure para atuar como um servidor de processo, para lidar com a replicação do Azure. Você pode excluir essa VM após a conclusão do failback.
  - **Conexão VPN**: para fazer failback, você precisa de uma conexão VPN (ou Azure ExpressRoute) da rede do Azure para o site local.
  - **Servidor de destino mestre separado**: por padrão, o failback é tratado pelo servidor de destino mestre que foi instalado com o servidor de configuração na VM do VMware local. Se você precisar realizar o failback de grandes volumes de tráfego, deverá configurar um servidor de destino mestre local separado.
  - **Política de failback**: para replicar volta para seu site local, você precisa de uma política de failback. A política foi criada automaticamente quando você criou sua política de replicação do local para o Azure.
  - **Infraestrutura do VMware**: para fazer failback, você precisa de uma infraestrutura do VMware. Você não pode realizar o failback para um servidor físico.
- Depois que os componentes estiverem em vigor, o failback ocorrerá em três estágios:
  - **Estágio 1**: proteger novamente as VMs do Azure para que elas sejam replicadas do Azure de volta para as VMs do VMware locais.
  - **Estágio 2**: executar um failover para o site local.
  - **Estágio 3**: após o failback das cargas de trabalho, você reabilitará a replicação.

**Failback de VMware do Azure**

![Failback](./media/physical-azure-architecture/enhanced-failback.png)

## <a name="next-steps"></a>Próximas etapas

Para configurar a recuperação de desastre para servidores físicos no Azure, consulte o [Guia de instruções](physical-azure-disaster-recovery.md).
