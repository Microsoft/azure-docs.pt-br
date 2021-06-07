---
title: Visão geral da implantação do Gerenciador de Firewall do Azure
description: Conheça as etapas de implantação de alto nível necessárias para o Gerenciador de Firewall do Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: ceb6e84b31067f7289b9e003a4fb273ce717de33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079091"
---
# <a name="azure-firewall-manager-deployment-overview"></a>Visão geral da implantação do Gerenciador de Firewall do Azure

Há mais de uma forma de implantar o Gerenciador de Firewall do Azure, mas o processo geral a seguir é recomendado.

## <a name="general-deployment-process"></a>Processo de implantação geral

### <a name="hub-virtual-networks"></a>Redes virtuais de hub

1.  Criar uma política de firewall

    - Criar uma nova política
<br>*or*<br>
    - Derivar uma política de base e personalizar uma política local
<br>*or*<br>
    - Importar regras de um Firewall do Azure existente. Verifique se você removeu as regras NAT de políticas que devem ser aplicadas a vários firewalls
1. Criar sua arquitetura de hub e spoke
   - Criar uma rede virtual de hub usando o Gerenciador de Firewall do Azure e redes virtuais spoke emparelhadas usando o emparelhamento de rede virtual
<br>*or*<br>
    - Criar uma rede virtual e adicionar conexões de rede virtual e redes virtuais spoke emparelhadas a ela usando o emparelhamento de rede virtual

3. Selecione provedores de segurança e associe uma política de firewall. Atualmente, somente o Firewall do Azure é um provedor compatível.

   - Isso é feito enquanto você cria uma rede virtual de hub
<br>*or*<br>
    - Converta uma rede virtual existente em uma rede virtual de hub. Também é possível converter várias redes virtuais.

4. Configure as Rotas Definidas Pelo Usuário para rotear o tráfego para o firewall de rede virtual de hub.


### <a name="secured-virtual-hubs"></a>Hubs virtuais seguros

1. Criar sua arquitetura de hub e spoke

   - Crie um hub virtual protegido usando o Gerenciador de Firewall do Azure e adicione conexões de rede virtual.<br>*or*<br>
   - Crie um hub de WAN virtual do Azure e adicione conexões de rede virtual.
2. Selecionar os provedores de segurança

   - Feito durante a criação de um hub virtual seguro.<br>*or*<br>
   - Converta um Hub de WAN Virtual existente a um Hub Virtual Seguro.
3. Crie uma política de firewall e associe-a ao seu hub

   - Aplicável somente se estiver usando o Firewall do Azure.
   - As políticas de SECaaS (segurança como serviço) de terceiros são configuradas por meio da experiência de gerenciamento de parceiros.
4. Definir configurações de rota para rotear o tráfego para o Hub protegido

   - Encaminhe facilmente o tráfego para o hub seguro para filtragem e registro sem as Rotas Definidas pelo Usuário (UDR) nas redes virtuais spoke usando a página de Configuração de rota de hub virtual seguro.

> [!NOTE]
> - Você não pode ter mais de um hub por WAN virtual por região. Mas você pode adicionar várias WANs virtuais na região para conseguir isso.
> - Não é possível ter espaços IP sobrepostos para hubs em uma vWAN.
> - As conexões de VNet do Hub devem estar na mesma região que o hub.
>
> Para descobrir mais problemas conhecidos, confira [O que é o Gerenciador de Firewall do Azure?](overview.md#known-issues)

## <a name="convert-virtual-networks"></a>Converter redes virtuais

As seguintes informações se aplicarão se você converter uma rede virtual existente a uma rede virtual do hub:

- Se a rede virtual tiver um Firewall do Azure existente, você selecionará uma Política de Firewall a ser associada ao firewall existente. O status de provisionamento do firewall será atualizado enquanto a política de firewall substituir as regras de firewall. Durante o estado de provisionamento, o firewall continua processando o tráfego e não tem nenhum tempo de inatividade. Você pode importar regras existentes para uma Política de Firewall usando o Gerenciador de Firewall ou o Azure PowerShell.
- Se a rede virtual não tiver um Firewall do Azure associado, um firewall será implantado e a Política de Firewall será associada ao novo firewall.

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Proteger sua rede na nuvem com o Gerenciador de Firewall do Azure usando o portal do Azure](secure-cloud-network.md)