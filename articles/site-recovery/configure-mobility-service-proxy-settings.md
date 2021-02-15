---
title: Definir configurações de proxy do serviço de mobilidade para a recuperação de desastre do Azure para o Azure | Microsoft Docs
description: Fornece detalhes sobre como configurar o serviço de mobilidade quando os clientes usam um proxy em seu ambiente de origem.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 429ffcab147142ae2e96de13b7c9e1e5ee1ac7ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86133229"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>Definir configurações de proxy do serviço de mobilidade para a recuperação de desastre do Azure para o Azure

Este artigo fornece orientação sobre como personalizar as configurações de rede na VM (máquina virtual) do Azure de destino quando você estiver replicando e recuperando VMs do Azure de uma região para outra, usando [Azure site Recovery](site-recovery-overview.md).

A finalidade deste documento é fornecer etapas para definir as configurações de proxy para Azure Site Recovery serviço de mobilidade no cenário de recuperação de desastre do Azure para o Azure. 

Os proxies são gateways de rede que permitem/proíbem conexões de rede a pontos de extremidade. Normalmente, um proxy é um computador fora do computador cliente que tenta acessar os pontos de extremidade de rede. Uma lista de bypass permite que o cliente faça conexões diretamente com os pontos de extremidade sem passar pelo proxy. Um nome de usuário e senha podem ser definidos opcionalmente para um proxy por administradores de rede para que somente clientes autenticados possam usar o proxy. 

## <a name="before-you-start"></a>Antes de começar

Saiba como o Site Recovery fornece recuperação de desastre para [esse cenário](azure-to-azure-architecture.md).
Entenda as [diretrizes de rede](azure-to-azure-about-networking.md) quando estiver replicando e recuperando VMs do Azure de uma região para outra, usando [Azure site Recovery](site-recovery-overview.md).
Verifique se o seu proxy está configurado adequadamente com base nas necessidades da sua organização.

## <a name="configure-the-mobility-service"></a>Configurar o serviço de mobilidade

O serviço de mobilidade dá suporte apenas a proxies não autenticados. Ele fornece duas maneiras de inserir detalhes de proxy para comunicação com pontos de extremidade de Site Recovery. 

### <a name="method-1-auto-detection"></a>Método 1: detecção automática

O serviço de mobilidade detecta automaticamente as configurações de proxy das configurações do ambiente ou das configurações do IE (somente Windows) durante habilitar a replicação. 

- Sistema operacional Windows: durante a habilitação da replicação, o serviço de mobilidade detecta as configurações de proxy conforme configurado no Internet Explorer para usuário do sistema local. Para configurar o proxy para a conta sistema local, um administrador pode usar o PsExec para iniciar um prompt de comando e, em seguida, o Internet Explorer. 
- Sistema operacional Windows: as configurações de proxy são definidas como variáveis de ambiente http_proxy e no_proxy. 
- SO Linux: as configurações de proxy são definidas em/etc/profile ou/etc/environment como variáveis de ambiente http_proxy no_proxy. 
- As configurações de proxy detectadas automaticamente são salvas no arquivo de configuração de proxy do serviço de mobilidade ProxyInfo. conf 
- Local padrão de ProxyInfo. conf 
    - Windows: C:\ProgramData\Microsoft Azure site Recovery\Config\ProxyInfo.conf 
    - Linux:/usr/local/InMage/config/ProxyInfo.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>Método 2: fornecer configurações personalizadas de proxy de aplicativo

Nesse caso, o cliente fornece configurações de proxy de aplicativo personalizadas no arquivo de configuração do serviço de mobilidade ProxyInfo. conf. Esse método permite que os clientes forneçam proxy somente para o serviço de mobilidade ou um proxy diferente para Azure Site Recovery serviço de mobilidade do que um proxy (ou nenhum proxy) para o restante dos aplicativos no computador.

## <a name="proxy-template"></a>Modelo de proxy
ProxyInfo. conf contém o seguinte modelo [proxy] address = http://1.2.3.4 Port = 5678 BypassList = hypervrecoverymanager. WindowsAzure. com, login. microsoftonline. com, BLOB. Core. Windows. net. O BypassList não dá suporte a curingas como ' *. windows.net ', mas o windows.net é bom o suficiente para ignorar. 

## <a name="next-steps"></a>Próximas etapas:
- Leia as [diretrizes de rede](./azure-to-azure-about-networking.md) para replicar VMs do Azure.
- Implante a recuperação de desastres [replicando as VMs do Azure](./azure-to-azure-quickstart.md).
