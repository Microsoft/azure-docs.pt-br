---
title: Solucionar problemas de conectividade do Link Privado do Azure
description: Orientações passo a passo para diagnosticar conectividade de link privado
services: private-link
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 45a7a146dd929408b50a0045fe2598726ee05505
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544302"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>Solucionar problemas de conectividade do Link Privado do Azure

Este artigo fornece orientações passo a passo para validar e diagnosticar a conectividade para a configuração do link privado do Azure.

Com o link privado do Azure, você pode acessar serviços de PaaS (plataforma como serviço) do Azure, como armazenamento do Azure, Azure Cosmos DB e banco de dados SQL do Azure e serviços hospedados de cliente ou parceiro do Azure por meio de um ponto de extremidade privado em sua rede virtual. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, o que elimina a exposição da Internet pública. Você também pode criar seu próprio serviço de vínculo privado em sua rede virtual e fornecê-lo de forma privada aos seus clientes.

Você pode habilitar o serviço que é executado atrás da camada Standard de Azure Load Balancer para acesso de link privado. Os consumidores do seu serviço podem criar um ponto de extremidade privado dentro de sua rede virtual e mapeá-lo para esse serviço para acessá-lo de forma privada.

Aqui estão os cenários de conectividade disponíveis com o link privado:

- Rede virtual da mesma região
- Redes virtuais emparelhadas de região
- Redes virtuais emparelhadas globalmente
- Cliente local sobre VPN ou circuitos do Azure ExpressRoute

## <a name="deployment-troubleshooting"></a>Solução de problemas de implantação

Examine as informações sobre como [desabilitar as políticas de rede no serviço de vínculo privado](./disable-private-link-service-network-policy.md) para ver os casos de solução de problemas em que você não pode selecionar o endereço IP de origem da sub-rede de sua escolha para o serviço de link privado.

Verifique se a configuração **privateLinkServiceNetworkPolicies** está desabilitada para a sub-rede da qual você está selecionando o endereço IP de origem.

## <a name="diagnose-connectivity-problems"></a>Diagnosticar problemas de conectividade

Se você tiver problemas de conectividade com a configuração de link privado, examine estas etapas para verificar se todas as configurações usuais estão conforme o esperado.

1. Examine a configuração do link privado navegando no recurso.

    a. Vá para o **centro de links privado**.

      ![Centro de links privado](./media/private-link-tsg/private-link-center.png)

    b. No painel esquerdo, selecione **serviços de vínculo privado**.

      ![Serviços de vínculo privado](./media/private-link-tsg/private-link-service.png)

    c. Filtre e selecione o serviço de link privado que você deseja diagnosticar.

    d. Examine as conexões do ponto de extremidade privado.
     - Verifique se o ponto de extremidade privado do qual você está procurando conectividade está listado com um estado de conexão **aprovado** .
     - Se o estado estiver **pendente**, selecione-o e aprove-o.

       ![Conexões de ponto de extremidade privado](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Vá para o ponto de extremidade privado do qual você está se conectando selecionando o nome. Verifique se o status da conexão é mostrado como **aprovado**.

       ![Visão geral da conexão de ponto de extremidade particular](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - Depois que ambos os lados forem aprovados, tente a conectividade novamente.

    e. Examine o **alias** na guia **visão geral** e na **ID do recurso** na guia **Propriedades** .
     - Verifique se as informações de **ID** do **alias** e do recurso correspondem ao **alias** e à **ID de recurso** que você está usando para criar um ponto de extremidade privado para esse serviço.

       ![Verificar informações de alias](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Verificar informações de ID do recurso](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. Examine as informações de **visibilidade** na guia **visão geral** .
     - Certifique-se de que sua assinatura esteja sob o escopo de **visibilidade** .

       ![Verificar informações de visibilidade](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g. Examine as informações do **balanceador de carga** na guia **visão geral** .
     - Você pode ir para o balanceador de carga selecionando o link do balanceador de carga.

       ![Verificar informações do balanceador de carga](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Verifique se as configurações do balanceador de carga estão configuradas de acordo com suas expectativas.
       - Examine a **configuração de IP de front-end**.
       - Examine os **pools de back-end**.
       - Examine **as regras de balanceamento de carga**.

       ![Verificar Propriedades do balanceador de carga](./media/private-link-tsg/pls-ilb-properties.png)

     - Verifique se o balanceador de carga está funcionando de acordo com as configurações anteriores.
       - Selecione uma VM em qualquer sub-rede que não seja a sub-rede onde o pool de back-end do balanceador de carga está disponível.
       - Tente acessar o front-end do balanceador de carga da VM anterior.
       - Se a conexão o fizer para o pool de back-end de acordo com as regras de balanceamento de carga, o balanceador de carga estará operacional.
       - Você também pode examinar a métrica do balanceador de carga por meio de Azure Monitor para ver se os dados estão fluindo pelo balanceador de carga.

1. Use [Azure monitor](../azure-monitor/overview.md) para ver se os dados estão fluindo.

    a. No recurso serviço de vínculo privado, selecione **métricas**.
     - Selecione **bytes** ou **bytes de saída**.
     - Veja se os dados estão fluindo quando você tenta se conectar ao serviço de link privado. Espere um atraso de aproximadamente 10 minutos.

       ![Verificar métricas do serviço de vínculo privado](./media/private-link-tsg/pls-metrics.png)

1. Entre em contato com a equipe de [suporte do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) se seu problema ainda não estiver resolvido e um problema de conectividade ainda existir.

## <a name="next-steps"></a>Próximas etapas

 * [Criar um serviço de vínculo privado (CLI)](./create-private-link-service-cli.md)
 * [Guia de solução de problemas do ponto de extremidade privado do Azure](troubleshoot-private-endpoint-connectivity.md)