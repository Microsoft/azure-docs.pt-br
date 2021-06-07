---
title: Lista de URL necessária para Área de Trabalho Virtual do Windows – Azure
description: Uma lista de URLs que você precisa desbloquear para garantir que sua implantação da Área de Trabalho Virtual do Windows funcione conforme o esperado.
author: Heidilohr
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: c937f9d75613b6550a2f05dd63a8b31dd83fe0b7
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445713"
---
# <a name="required-url-list"></a>Lista de URLs necessárias

Para implantar e usar a Área de Trabalho Virtual do Windows, você precisa desbloquear determinadas URLs para que suas VMs (máquinas virtuais) possam acessá-las a qualquer momento. Este artigo lista as URLs necessárias que você precisa desbloquear para que a Área de Trabalho Virtual do Windows funcione corretamente. 

>[!IMPORTANT]
>A Área de Trabalho Virtual do Windows não dá suporte a implantações que bloqueiam as URLs listadas neste artigo.

## <a name="virtual-machines"></a>Máquinas virtuais

As máquinas virtuais do Azure criadas para a Área de Trabalho Virtual do Windows precisam ter acesso às seguintes URLs na nuvem comercial do Azure:

|Endereço|Porta TCP de saída|Finalidade|Marca de serviço|
|---|---|---|---|
|*.wvd.microsoft.com|443|Tráfego de serviço|WindowsVirtualDesktop|
|gcs.prod.monitoring.core.windows.net|443|Tráfego de agente|AzureCloud|
|production.diagnostics.monitoring.core.windows.net|443|Tráfego de agente|AzureCloud|
|*xt.blob.core.windows.net|443|Tráfego de agente|AzureCloud|
|*eh.servicebus.windows.net|443|Tráfego de agente|AzureCloud|
|*xt.table.core.windows.net|443|Tráfego de agente|AzureCloud|
|*xt.queue.core.windows.net|443|Tráfego de agente|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Ativação do Windows|Internet|
|mrsglobalsteus2prod.blob.core.windows.net|443|Atualizações do agente e da pilha de SXS|AzureCloud|
|wvdportalstorageblob.blob.core.windows.net|443|Suporte do portal do Azure|AzureCloud|
| 169.254.169.254 | 80 | [Ponto de extremidade do Serviço de Metadados de Instância do Azure](../virtual-machines/windows/instance-metadata-service.md) | N/D |
| 168.63.129.16 | 80 | [Monitoramento de integridade do host da sessão](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | N/D |

>[!IMPORTANT]
>Agora, a Área de Trabalho Virtual do Windows dá suporte à marca FQDN. Para obter mais informações, confira [Usar o Firewall do Azure para proteger implantações da Área de Trabalho Virtual do Windows](../firewall/protect-windows-virtual-desktop.md).
>
>Recomendamos que você use marcas de FQDN ou marcas de serviço, em vez de URLs, para evitar problemas de serviço. As URLs e as marcas listadas correspondem apenas aos sites e aos recursos da Área de Trabalho Virtual do Windows. Não incluem URLs para outros serviços, como Azure Active Directory.

As máquinas virtuais do Azure criadas para a Área de Trabalho Virtual do Windows precisam ter acesso às seguintes URLs na nuvem comercial do Azure Government:

|Endereço|Porta TCP de saída|Finalidade|Marca de serviço|
|---|---|---|---|
|*.wvd.microsoft.us|443|Tráfego de serviço|WindowsVirtualDesktop|
|gcs.monitoring.core.usgovcloudapi.net|443|Tráfego de agente|AzureCloud|
|monitoring.core.usgovcloudapi.net|443|Tráfego de agente|AzureCloud|
|fairfax.warmpath.usgovcloudapi.net|443|Tráfego de agente|AzureCloud|
|*xt.blob.core.usgovcloudapi.net|443|Tráfego de agente|AzureCloud|
|*.servicebus.usgovcloudapi.net|443|Tráfego de agente|AzureCloud|
|*xt.table.core.usgovcloudapi.net|443|Tráfego de agente|AzureCloud|
|Kms.core.usgovcloudapi.net|1688|Ativação do Windows|Internet|
|mrsglobalstugviffx.blob.core.usgovcloudapi.net|443|Atualizações do agente e da pilha de SXS|AzureCloud|
|wvdportalstorageblob.blob.core.usgovcloudapi.net|443|Suporte do portal do Azure|AzureCloud|
| 169.254.169.254 | 80 | [Ponto de extremidade do Serviço de Metadados de Instância do Azure](../virtual-machines/windows/instance-metadata-service.md) | N/D |
| 168.63.129.16 | 80 | [Monitoramento de integridade do host da sessão](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | N/D |

A seguinte tabela lista as URLs opcionais às quais suas máquinas virtuais do Azure podem ter acesso:

|Endereço|Porta TCP de saída|Finalidade|Azure Gov|
|---|---|---|---|
|*.microsoftonline.com|443|Autenticação no Microsoft Online Services|login.microsoftonline.us|
|*.events.data.microsoft.com|443|Serviço de telemetria|Nenhum|
|www.msftconnecttest.com|443|Detecta se o sistema operacional está conectado à Internet|Nenhum|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|Nenhum|
|login.windows.net|443|Entrar nos Serviços Online da Microsoft, Microsoft 365|login.microsoftonline.us|
|*.sfx.ms|443|Atualizações do software cliente do OneDrive|oneclient.sfx.ms|
|*.digicert.com|443|Verificação de revogação do certificado|Nenhum|
|*.azure-dns.com|443|Resolução de DNS do Azure|Nenhum|
|*.azure-dns.net|443|Resolução de DNS do Azure|Nenhum|

>[!NOTE]
>No momento, a Área de Trabalho Virtual do Windows não tem uma lista de intervalos de endereços IP que você pode desbloquear para permitir o tráfego de rede. Neste momento, só damos suporte ao desbloqueio de URLs específicas.
>
>Se estiver usando um NGFW (Firewall de Próxima Geração), você precisará usar uma lista dinâmica feita especificamente para os IPs do Azure a fim de garantir que você possa se conectar.
>
>Para obter uma lista de URLs seguras relacionadas ao Office, incluindo as URLs obrigatórias relacionadas ao Azure Active Directory, confira [URLs e intervalos de endereços IP do Office 365](/office365/enterprise/urls-and-ip-address-ranges).
>
>Você precisa usar o caractere curinga (*) para URLs que envolvem tráfego de serviço. Se preferir não usar * para o tráfego relacionado ao agente, encontre as URLs sem curinga da seguinte forma:
>
>1. Registre suas máquinas virtuais no pool de hosts da Área de Trabalho Virtual do Windows.
>2. Abra o **Visualizador de eventos**, vá para **Logs do Windows** > **Aplicativo** > **WVD-Agent** e pesquise a ID de Evento 3701.
>3. Desbloqueie as URLs encontradas com a ID de Evento 3701. As URLs com a ID de Evento 3701 são específicas da região. Você precisará repetir o processo de desbloqueio com as URLs relevantes para cada região em que quiser implantar suas máquinas virtuais.

## <a name="remote-desktop-clients"></a>Clientes de área de trabalho remota

Os clientes da Área de Trabalho Remota que você usa precisam ter acesso às seguintes URLs:

|Endereço|Porta TCP de saída|Finalidade|Cliente(s)|Azure Gov|
|---|---|---|---|---|
|*.wvd.microsoft.com|443|Tráfego de serviço|Todos|*.wvd.microsoft.us|
|*.servicebus.windows.net|443|Solucionar problemas de dados|Todos|*.servicebus.usgovcloudapi.net|
|go.microsoft.com|443|FWLinks da Microsoft|Todos|Nenhum|
|aka.ms|443|Redutor de URL da Microsoft|Todos|Nenhum|
|docs.microsoft.com|443|Documentação|Todos|Nenhum|
|privacy.microsoft.com|443|Política de privacidade|Todos|Nenhum|
|query.prod.cms.rt.microsoft.com|443|Atualizações do cliente|Área de Trabalho do Windows|Nenhum|

>[!IMPORTANT]
>Abrir essas URLs é essencial para ter uma experiência do cliente confiável. Não há suporte ao bloqueio do acesso a essas URLs e isso afetará a funcionalidade do serviço.
>
>Essas URLs só correspondem a sites e recursos de clientes. Essa lista não inclui URLs para outros serviços, como Azure Active Directory. As URLs do Azure Active Directory podem ser encontradas com a ID 56 nas [URLs e intervalo de endereços IP do Office 365](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online).