---
title: Sobre a rede na recuperação de desastre da VM do Azure com o Azure Site Recovery
description: Fornece uma visão geral da rede para a replicação de VMs do Azure usando o Azure Site Recovery.
services: site-recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/13/2020
ms.author: harshacs
ms.openlocfilehash: b9fdaf8a0791570ecee402442c5faefe2f70a22b
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370433"
---
# <a name="about-networking-in-azure-vm-disaster-recovery"></a>Sobre a rede na recuperação de desastre da VM do Azure



Este artigo fornece diretrizes de rede para quando você está replicando e recuperando VMs do Azure de uma região para outra usando o [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Antes de começar

Saiba como o Site Recovery fornece recuperação de desastre para [esse cenário](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Infraestrutura de rede típica

O diagrama a seguir ilustra um ambiente típico do Azure para aplicativos em execução em VMs do Azure:

![Diagrama que descreve um ambiente típico do Azure para aplicativos em execução em VMs do Azure.](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Caso você esteja usando o Azure ExpressRoute ou uma conexão VPN na rede local com o Azure, o ambiente terá a seguinte aparência:

![ambiente do cliente](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

As redes geralmente são protegidas usando firewalls e NSGs (grupos de segurança de rede). As marcas de serviço devem ser usadas para controlar a conectividade de rede. NSGs deve permitir várias marcas de serviço para controlar a conectividade de saída.

>[!IMPORTANT]
> O uso de um proxy autenticado para controlar a conectividade de rede não é compatível com o Site Recovery e a replicação não pode ser habilitada.

>[!NOTE]
>- A filtragem baseada em endereço IP não deve ser executada para controlar a conectividade de saída.
>- Azure Site Recovery endereços IP não devem ser adicionados na tabela de roteamento do Azure para controlar a conectividade de saída.

## <a name="outbound-connectivity-for-urls"></a>Conectividade de saída para URLs

Se você está usando um proxy de firewall baseado em URL para controlar a conectividade de saída, permita estas URLs do Site Recovery:

**URL** | **Detalhes**
--- | ---
*.blob.core.windows.net | Necessário para que os dados possam ser gravados para a conta de armazenamento de cache da região de origem da VM. Se você souber todas as contas de armazenamento em cache para suas VMs, poderá permitir o acesso às URLs de conta de armazenamento específicas (ex: cache1.blob.core.windows.net e cache2.blob.core.windows.net) em vez de *. blob.core.windows.net
login.microsoftonline.com | Necessário para autorização e autenticação para as URLs do serviço de recuperação de Site.
*.hypervrecoverymanager.windowsazure.com | Necessário para que a comunicação de serviço de recuperação de Site possa ocorrer da VM.
*.servicebus.windows.net | Necessário para que os dados de monitoramento e diagnóstico de recuperação de Site possam ser gravados da VM.
*.vault.azure.net | Permite o acesso para habilitar a replicação para máquinas virtuais habilitadas para ADE por meio do portal
*. automation.ext.azure.com | Permite habilitar a atualização automática do agente de mobilidade para um item replicado por meio do portal

## <a name="outbound-connectivity-using-service-tags"></a>Conectividade de saída usando marcas de serviço

Ao usar o NSG para controlar a conectividade de saída, essas marcas de serviço precisam ser permitidas.

- Para as contas de armazenamento na região de origem:
    - Crie uma [marcação de serviço de armazenamento](../virtual-network/network-security-groups-overview.md#service-tags) com base na regra NSG para a região de origem.
    - Permita esses endereços para que os dados possam ser gravados da VM para a conta de armazenamento de cache.
- Criar uma [marca de serviço do Azure Active Directory (AAD)](../virtual-network/network-security-groups-overview.md#service-tags) com base em regra NSG para permitir o acesso a todos os endereços IP correspondente para o AAD
- Crie uma regra de NSG baseada em marca de serviço EventsHub para a região de destino, permitindo o acesso ao monitoramento de Site Recovery.
- Crie uma regra de NSG baseada em marca de serviço do AzureSiteRecovery para permitir acesso ao Site Recovery Service em qualquer região.
- Crie uma regra de NSG baseada em marca de serviço do AzureKeyVault. Isso é necessário apenas para habilitar a replicação de máquinas virtuais habilitadas para ADE por meio do Portal.
- Crie uma regra de NSG baseada em marca de serviço do GuestAndHybridManagement. Isso é necessário apenas para habilitar a atualização automática do agente de mobilidade para um item replicado por meio do Portal.
- É recomendável que você crie as regras de NSG necessárias em um NSG de teste e verifique se não há nenhum problema antes de criar as regras em um NSG de produção.

## <a name="example-nsg-configuration"></a>Exemplo de Configuração do NSG

Este exemplo mostra como configurar regras de NSG para uma VM a ser replicada.

- Se você está usando regras de NSG para controlar a conectividade de saída, use regras de "Permitir HTTPS de saída" para a port:443 para todos os intervalos de endereços IP necessários.
- O exemplo presume que o local de origem da VM é "Leste dos EUA" e que o local de destino é "EUA Central".

### <a name="nsg-rules---east-us"></a>Regras de NSG – Leste dos EUA

1. Crie uma regra de segurança HTTPS (443) de saída para "Storage.EastUS" no NSG conforme mostrado na captura de tela abaixo.

      ![A captura de tela mostra Adicionar regra de segurança de saída para um grupo de segurança de rede para o ponto de armazenamento leste U S.](./media/azure-to-azure-about-networking/storage-tag.png)

2. Crie uma regra de segurança HTTPS (443) de saída para "AzureActiveDirectory" no NSG conforme mostrado na captura de tela abaixo.

      ![A captura de tela mostra Adicionar regra de segurança de saída para um grupo de segurança de rede para o Azure A D.](./media/azure-to-azure-about-networking/aad-tag.png)

3. Semelhante às regras de segurança acima, crie a regra de segurança HTTPS de saída (443) para "EventHub. Centralus" no NSG que corresponde ao local de destino. Isso permite o acesso ao monitoramento de Site Recovery.

4. Crie uma regra de segurança HTTPS (443) de saída para "AzureSiteRecovery" no NSG. Isso permite o acesso ao Site Recovery Service em qualquer região.

### <a name="nsg-rules---central-us"></a>Regras de NSG – EUA Central

Essas regras são necessárias para que a replicação possa ser ativada da região de destino para a região de origem após o failover:

1. Crie uma regra de segurança HTTPS (443) de saída para "Storage.CentralUS" no NSG.

2. Crie uma regra de segurança HTTPS (443) de saída para "AzureActiveDirectory" no NSG.

3. Semelhante às regras de segurança acima, crie a regra de segurança HTTPS de saída (443) para "EventHub. Eastus" no NSG que corresponde ao local de origem. Isso permite o acesso ao monitoramento de Site Recovery.

4. Crie uma regra de segurança HTTPS (443) de saída para "AzureSiteRecovery" no NSG. Isso permite o acesso ao Site Recovery Service em qualquer região.

## <a name="network-virtual-appliance-configuration"></a>Configuração da solução de virtualização de rede

Se estiver usando NVAs (soluções de virtualização de rede) para controlar o tráfego de saída de rede das VMs, a solução poderá ficar limitada se todo o tráfego da replicação passar pela NVA. Recomendamos criar um ponto de extremidade de serviço de rede em sua rede virtual para "Armazenamento", para que o tráfego da replicação não acesse a NVA.

### <a name="create-network-service-endpoint-for-storage"></a>Criar ponto de extremidade de serviço de rede para Armazenamento
É possível criar um ponto de extremidade de serviço de rede em sua rede virtual para "Armazenamento" para que o tráfego da replicação não saia do limite do Azure.

- Selecione sua Rede Virtual do Azure e clique em 'Pontos de extremidade de serviço'

    ![storage-endpoint](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Clique em 'Adicionar' e a guia 'Adicionar pontos de extremidade de serviço' será aberta
- Selecione 'Microsoft.Storage' em 'Serviço' e as sub-redes necessárias no campo 'Sub-redes' e clique em 'Adicionar'

>[!NOTE]
>Não restrinja o acesso à rede virtual às suas contas de armazenamento usadas para a ASR. Você deve permitir o acesso de 'Todas as redes'

### <a name="forced-tunneling"></a>Túnel forçado

Você pode substituir a rota de sistema padrão do Azure para o prefixo de endereço 0.0.0.0/0 com um [rota personalizados](../virtual-network/virtual-networks-udr-overview.md#custom-routes) e desviar o tráfego VM para uma solução de virtualização de rede (NVA) local, mas essa configuração não é recomendada para a recuperação de Site replicação. Se você estiver usando rotas personalizadas, deverá [criar um ponto de extremidade de serviço de rede virtual](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) em sua rede virtual para "Armazenamento" para que o tráfego de replicação não saia do limite do Azure.

## <a name="next-steps"></a>Próximas etapas
- Inicie a proteção de suas cargas de trabalho ao [replicar máquinas virtuais do Azure](./azure-to-azure-quickstart.md).
- Saiba mais sobre [retenção de endereço IP](site-recovery-retain-ip-azure-vm-failover.md) para failover de máquina virtual do Azure.
- Saiba mais sobre a recuperação de desastre de [máquinas virtuais do Azure com o ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).