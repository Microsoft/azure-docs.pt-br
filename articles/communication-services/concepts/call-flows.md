---
title: Fluxos de chamadas nos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre os fluxos de chamadas nos Serviços de Comunicação do Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 410f8ab4de0d93262647cbc07e0792cd39f7a844
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593630"
---
# <a name="call-flows"></a>Fluxos de chamadas

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

A seção a seguir fornece uma visão geral dos fluxos de chamada nos Serviços de Comunicação do Azure. Os fluxos de sinalização e de mídia dependem dos tipos de chamadas que os usuários estão fazendo. Exemplos de tipos de chamada incluem VoIP de um para um, PSTN de um para um e chamadas em grupo contendo uma combinação de VoIP e participantes conectados a PSTN. Examine os [Tipos de chamada](./voice-video-calling/about-call-types.md).

## <a name="about-signaling-and-media-protocols"></a>Sobre protocolos de mídia e sinalização

Quando você estabelece uma chamada em grupo ou ponto a ponto, dois protocolos são usados em segundo plano: o HTTP (REST) para sinalização e o SRTP para mídia. 

A sinalização entre as bibliotecas de cliente ou entre as bibliotecas de cliente e os Controladores de Sinalização dos Serviços de Comunicação é administrada com HTTP REST (TLS). Para o Tráfego de Mídia em Tempo Real (RTP), o protocolo UDP é preferencial. Se o uso do UDP for impedido pelo firewall, a biblioteca de clientes usará o protocolo TCP para a mídia. 

Vamos examinar os protocolos de sinalização e de mídia em vários cenários. 

## <a name="call-flow-cases"></a>Casos de fluxo de chamadas

### <a name="case-1-voip-where-a-direct-connection-between-two-devices-is-possible"></a>Caso 1: VoIP no qual uma conexão direta entre dois dispositivos é possível

Em chamadas de vídeo ou VoIP de um para um, o tráfego prefere o caminho mais direto. "Caminho direto" significa que, se duas bibliotecas de cliente puderem alcançar umas às outras diretamente, elas estabelecerão uma conexão direta. Geralmente, isso é possível quando duas bibliotecas de cliente estão na mesma sub-rede (por exemplo, em uma sub-rede 192.168.1.0/24) ou quando os dois dispositivos residem em sub-redes que podem ver umas às outras (as bibliotecas de cliente na sub-rede 10.10.0.0/16 e 192.168.1.0/24 estão dentro do alcance uma da outra).

:::image type="content" source="./media/call-flows/about-voice-case-1.png" alt-text="Diagrama que mostra uma chamada VOIP Direta entre os usuários e os Serviços de Comunicação.":::

### <a name="case-2-voip-where-a-direct-connection-between-devices-is-not-possible-but-where-connection-between-nat-devices-is-possible"></a>Caso 2: VoIP no qual uma conexão direta entre dispositivos não é possível, mas a conexão entre dispositivos NAT é possível

Se dois dispositivos estiverem localizados em sub-redes que estiverem fora do alcance uma da outra (por exemplo, Alice trabalha em uma cafeteria e Pedro trabalha de casa), mas a conexão entre os dispositivos NAT for possível, as bibliotecas de clientes do lado do cliente estabelecerão a conectividade por meio de dispositivos NAT. 

Para Alice, ele será o NAT da cafeteria e, para Pedro, será o NAT da casa dele. O dispositivo da Alice enviará o endereço externo do NAT dela e o do Pedro fará o mesmo. As bibliotecas de cliente aprendem os endereços externos de um serviço STUN (Utilitários Transversais de Sessão para NAT) que os Serviços de Comunicação do Azure fornecem gratuitamente. A lógica que manipula o handshake entre a Alice e o Pedro é inserida nas bibliotecas de clientes fornecidas pelos Serviços de Comunicação do Azure. (Você não precisa de nenhuma configuração adicional)

:::image type="content" source="./media/call-flows/about-voice-case-2.png" alt-text="Diagrama que mostra uma chamada VOIP que utiliza uma conexão STUN.":::

### <a name="case-3-voip-where-neither-a-direct-nor-nat-connection-is-possible"></a>Caso 3: VoIP no qual nem uma conexão direta nem uma conexão NAT é possível

Se um ou ambos os dispositivos cliente estiverem protegidos por um NAT simétrico, será necessário que um serviço de nuvem separado retransmita a mídia entre as duas bibliotecas de clientes. Esse serviço é chamado TURN (Atravessamento Usando Retransmissões ao redor de NAT) e também é fornecido pelos Serviços de Comunicação. Os Serviços de Comunicação que chamam a biblioteca de clientes usam automaticamente os serviços TURN com base nas condições de rede detectadas. O uso do serviço TURN da Microsoft é cobrado separadamente.

:::image type="content" source="./media/call-flows/about-voice-case-3.png" alt-text="Diagrama que mostra uma chamada VOIP que utiliza uma conexão TURN.":::
 
### <a name="case-4-group-calls-with-pstn"></a>Caso 4: Chamadas em grupo com PSTN

Tanto a sinalização quanto a mídia para Chamadas PSTN usam o recurso de telefonia dos Serviços de Comunicação do Azure. Esse recurso é interconectado com outras operadoras.

O tráfego de mídia PSTN é transmitido por meio de um componente chamado Processador de Mídia.

:::image type="content" source="./media/call-flows/about-voice-pstn.png" alt-text="Diagrama que mostra uma Chamada em Grupo PSTN com os Serviços de Comunicação.":::

> [!NOTE]
> Para aqueles familiarizados com o processamento de mídia, nosso Processador de Mídia também é um Agente do Usuário Back-to-Back, conforme definido em [RFC 3261 SIP: protocolo SIP](https://tools.ietf.org/html/rfc3261), o que significa que ele pode converter codecs ao administrar chamadas entre as redes da Microsoft e da Operadora. O Controlador de Sinalização dos Serviços de Comunicação do Azure é a implementação da Microsoft de um Proxy SIP de acordo com o mesmo RFC.

Chamadas em grupo, mídia e sinalização sempre são transmitidas por meio do back-end dos Serviços de Comunicação do Azure. O áudio e/ou vídeo de todos os participantes é mixado no componente de Processador de Mídia. Todos os membros de uma chamada em grupo enviam os fluxos de áudio e/ou vídeo para o processador de mídia, que retorna fluxos de mídia mixados.

O RTP (protocolo em tempo real) padrão para chamadas em grupo é o protocolo UDP.

> [!NOTE]
> O Processador de Mídia pode atuar como uma MCU (Unidade de Controle de Multipontos) ou SFU (Unidade de Encaminhamento Seletivo)

:::image type="content" source="./media/call-flows/about-voice-group-calls.png" alt-text="Diagrama que mostra o fluxo do processo de mídia de UDP nos Serviços de Comunicação.":::

Se a biblioteca de clientes não puder usar o UDP para mídia devido a restrições de firewall, será feita uma tentativa de usar o protocolo TCP. Observe que o componente de Processador de Mídia requer o UDP, portanto, quando isso acontece, o serviço TURN dos Serviços de Comunicação será adicionado à chamada em grupo para converter TCP em UDP. Os preços de TURN serão incorridos nesse caso, a menos que as funcionalidades de TURN sejam desabilitadas manualmente.

:::image type="content" source="./media/call-flows/about-voice-group-calls-2.png" alt-text="Diagrama que mostra o fluxo do processo de mídia de TCP nos Serviços de Comunicação.":::

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução a chamadas](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Os seguintes documentos podem ser do seu interesse:

- Saiba mais sobre os [tipos de chamada](../concepts/voice-video-calling/about-call-types.md)
- Saiba mais sobre a [Arquitetura de cliente/servidor](./client-and-server-architecture.md)
