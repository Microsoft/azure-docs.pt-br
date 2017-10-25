---
title: Diagnosticar a Conectividade Local por meio do Gateways de VPN com o Observador de Rede do Azure | Microsoft Docs
description: "Este artigo descreve como diagnosticar a conectividade local por meio do gateway de VPN com a solução de problemas de recursos do Observador de Rede do Azure."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 94658dfcf93e821e24cabb1f010f8dce0c014700
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>Diagnosticar a conectividade local por meio do Gateway de VPN

O Gateway de VPN do Azure permite criar a solução híbrida que atenderá à necessidade de uma conexão segura entre sua rede local e sua rede virtual do Azure. Como os requisitos são exclusivos, a escolha do dispositivo VPN local também é exclusiva. Atualmente, o Azure suporta [vários dispositivos VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) que são constantemente validados em parceria com os fornecedores de dispositivos. Examine as definições de configuração específicas do dispositivo antes de configurar seu dispositivo VPN local. Da mesma forma, o Gateway de VPN do Azure está configurado com um conjunto de [parâmetros IPsec suportados](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) que são usados para estabelecer conexões. Atualmente, não há formas de especificar ou selecionar uma combinação específica de parâmetros IPsec a partir do Gateway de VPN do Azure. Para estabelecer uma conexão bem-sucedida entre a rede local e o Azure, as configurações do dispositivo VPN local devem obedecer os parâmetros de IPsec prescritos pelo Gateway de VPN do Azure. Se as configurações estiverem incorretas, haverá perda da conectividade e, até então, a resolução desses problemas não era algo trivial e geralmente levava horas para identificar e corrigir o problema.

Com o recurso de solução de problemas do Observador de Rede do Azure, é possível diagnosticar problemas com seu Gateway e Conexões e, em poucos minutos, obter informações suficientes para tomar uma decisão informada para corrigir o problema.

## <a name="scenario"></a>Cenário

Você deseja configurar uma conexão site a site entre a rede local e o Azure usando o FortiGate como o Gateway de VPN local. Para obter esse cenário, a seguinte configuração seria necessária:

1. Gateway de Rede Virtual - O Gateway de VPN no Azure
1. Gateway de Rede Local - a representação do [Gateway de VPN (FortiGate) local](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) na nuvem do Azure
1. Conexão site a site (baseada em rota) – [Conexão entre o Gateway de VPN e o roteador local](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#createconnection)
1. [Configuração do FortiGate](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Orientações passo a passo detalhadas para definir uma configuração de Site a Site podem ser encontradas ao consultar: [Criar uma VNet com uma conexão Site a Site usando o Portal do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

Uma das etapas críticas da configuração é a definição dos parâmetros de comunicação do IPsec. Configurações incorretas levarão à perda da conectividade entre a rede local e o Azure. Atualmente, os Gateways de VPN do Azure são configurados para suportar os seguintes parâmetros de IPsec na Fase 1. Conforme mencionado anteriormente, observe que essas configurações não podem ser modificadas.  Como você pode ver na tabela a seguir, os algoritmos de criptografia compatíveis com o Gateway de VPN do Azure são AES128, AES256 e 3DES.

### <a name="ike-phase-1-setup"></a>Fase 1 da configuração IKE

| **Propriedade** | **PolicyBased** | **Gateway de VPN RouteBased e Standard ou de Alto Desempenho** |
| --- | --- | --- |
| Versão IKE |IKEv1 |IKEv2 |
| Grupo Diffie-Hellman |Grupo 2 (1024 bits) |Grupo 2 (1024 bits) |
| Método de autenticação |Chave Pré-Compartilhada |Chave Pré-Compartilhada |
| Algoritmos de criptografia |AES256 AES128 3DES |AES256 3DES |
| Algoritmo de hash |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Tempo de vida (tempo) da SA (associação de segurança) da fase 1 |28.800 segundos |10.800 segundos |

Como usuário, seria necessário configurar o FortiGate. Uma configuração de exemplo pode ser encontrada no [GitHub](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt). Sem saber, você configurou seu FortiGate para usar o SHA-512 como algoritmo de hash. Como esse algoritmo não é um algoritmo com suporte para conexões baseadas em política, a conexão VPN funcionará.

Esses problemas são difíceis de solucionar e as causas principais geralmente são não intuitivas. Nesse caso, você pode abrir um tíquete de suporte e obter ajuda para resolver o problema. Mas, com a API de solução de problemas do Observador de Rede do Azure, é possível identificar esses problemas sozinho.

## <a name="troubleshooting-using-azure-network-watcher"></a>Solução de problemas usando o Observador de Rede do Azure

Para diagnosticar a conexão, conecte-se ao Azure PowerShell e inicie o cmdlet `Start-AzureRmNetworkWatcherResourceTroubleshooting`. Você pode encontrar os detalhes sobre como usar esse cmdlet em [Conexões e Solução de Problemas do Gateway de Rede Virtual - PowerShell](network-watcher-troubleshoot-manage-powershell.md). Esse cmdlet pode levar vários minutos para ser concluído.

Após a conclusão do cmdlet, navegue até o local de armazenamento especificado no cmdlet para obter informações detalhadas sobre o problema e os logs. O Observador de Rede do Azure cria uma pasta zip que contém os seguintes arquivos de log:

![1][1]

Abra o arquivo chamado IKEErrors.txt e ele exibirá o seguinte erro que indica um problema com a definição incorreta da configuração do IKE local.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Você pode obter informações detalhadas sobre o erro em Scrubbed-wfpdiag.txt. Nesse caso, foi mencionado que havia uma `ERROR_IPSEC_IKE_POLICY_MATCH` que fez com que a conexão não funcionasse corretamente.

Outro erro de configuração comum é a especificação de chaves compartilhadas incorretas. Se nos exemplos anteriores, você tiver especificado diferentes chaves compartilhadas, o IKEErrors.txt mostrará o seguinte erro: `Error: Authentication failed. Check shared key`.

O recurso de solução de problemas do Observador de Rede do Azure permite diagnosticar e solucionar problemas da Conexão e do Gateway de VPN com a facilidade de um simples cmdlet do PowerShell. Atualmente, prestamos suporte ao diagnóstico das seguintes condições e estamos trabalhando para adicionar mais condições.

### <a name="gateway"></a>Gateway

| Tipo de Falha | Motivo | Registro|
|---|---|---|
| NoFault | Quando nenhum erro é detectado. |Sim|
| GatewayNotFound | Não é possível localizar o Gateway ou o Gateway não está provisionado. |Não|
| PlannedMaintenance |  A instância do gateway está em manutenção.  |Não|
| UserDrivenUpdate | Uma atualização de um usuário está em andamento. Isso pode ser uma operação de redimensionamento. | Não |
| VipUnResponsive | Não é possível acessar a instância primária do Gateway. Isso acontece quando a investigação de integridade falha. | Não |
| PlatformInActive | Há um problema com a plataforma. | Não|
| ServiceNotRunning | O serviço subjacente não está em execução. | Não|
| NoConnectionsFoundForGateway | Não existe Conexões no gateway. Isso é apenas um aviso.| Não|
| ConnectionsNotConnected | Nenhuma das Conexões está conectada. Isso é apenas um aviso.| Sim|
| GatewayCPUUsageExceeded | O uso de CPU do Gateway atual é > 95%. | Sim |

### <a name="connection"></a>Conexão

| Tipo de Falha | Motivo | Registro|
|---|---|---|
| NoFault | Quando nenhum erro é detectado. |Sim|
| GatewayNotFound | Não é possível localizar o Gateway ou o Gateway não está provisionado. |Não|
| PlannedMaintenance | A instância do gateway está em manutenção.  |Não|
| UserDrivenUpdate | Uma atualização de um usuário está em andamento. Isso pode ser uma operação de redimensionamento.  | Não |
| VipUnResponsive | Não é possível acessar a instância primária do Gateway. Isso acontece quando a investigação de integridade falha. | Não |
| ConnectionEntityNotFound | A configuração da Conexão está ausente. | Não |
| ConnectionIsMarkedDisconnected | A Conexão está marcado como "desconectada". |Não|
| ConnectionNotConfiguredOnGateway | O serviço subjacente não tem a Conexão configurada. | Sim |
| ConnectionMarkedStandy | O serviço subjacente está marcado como em espera.| Sim|
| Autenticação | Incompatibilidade de chave pré-compartilhada. | Sim|
| PeerReachability | O gateway correspondente não está acessível. | Sim|
| IkePolicyMismatch | O gateway de mesmo nível tem diretivas IKE que não são suportadas pelo Azure. | Sim|
| Erro WfpParse | Ocorreu um erro ao analisar o log WFP. |Sim|

## <a name="next-steps"></a>Próximas etapas

Saiba como verificar a conectividade do Gateway de VPN com o PowerShell e a Automação do Azure consultando [Monitorar gateways de VPN com a solução de problemas do Observador de Rede do Azure](network-watcher-monitor-with-azure-automation.md)

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png
