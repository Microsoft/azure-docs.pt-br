---
title: Topologias de fluxo de chamadas nos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre as topologias de fluxo de chamadas dos Serviços de Comunicação do Azure.
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 526e3a1e4eeb6ef6a31a33498241d9a7443cca35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103490629"
---
# <a name="call-flow-topologies"></a>Topologias de fluxo de chamadas
Este artigo descreve as topologias de fluxo de chamadas dos Serviços de Comunicação do Azure. Este é um ótimo artigo a ser examinado se você é um cliente empresarial que integra os Serviços de Comunicação em uma rede gerenciada por você. Para obter uma introdução aos fluxos de chamadas dos Serviços de Comunicação, acesse a [documentação conceitual dos fluxos de chamadas](./call-flows.md).

## <a name="background"></a>Tela de fundo

### <a name="network-concepts"></a>Conceitos de rede

Antes de examinar as topologias de fluxo de chamadas, definiremos alguns termos que são mencionados em todo o documento.

Uma **rede do cliente** contém os segmentos de rede gerenciados por você. Isso pode incluir redes com e sem fio no seu escritório ou entre escritórios, data centers e provedores de serviços de Internet.

Uma rede do cliente geralmente tem vários perímetros de rede com firewalls e/ou servidores proxy que impõem as políticas de segurança da sua organização. Recomendamos fazer uma [avaliação de rede abrangente](/microsoftteams/3-envision-evaluate-my-environment) para garantir o desempenho ideal e a qualidade da sua solução de comunicação.

A **rede dos Serviços de Comunicação** é o segmento de rede que dá suporte aos Serviços de Comunicação do Azure. Essa rede é gerenciada pela Microsoft e é distribuída em todo o mundo com bordas próximas à maioria das redes dos clientes. Essa rede é responsável pela retransmissão de transporte, pelo processamento de mídia para chamadas em grupo e por outros componentes que dão suporte à comunicação de mídia avançada em tempo real.

### <a name="types-of-traffic"></a>Tipos de tráfego

Os Serviços de Comunicação foram criados principalmente em dois tipos de tráfego: **mídia em tempo real** e **sinalização**.

A **mídia em tempo real** é transmitida por meio do protocolo RTP. Esse protocolo dá suporte à transmissão de dados de áudio, vídeo e compartilhamento de tela. Esses dados são sensíveis a problemas de latência de rede. Embora seja possível transmitir uma mídia em tempo real usando TCP ou HTTP, recomendamos usar o UDP como o protocolo de camada de transporte para dar suporte a experiências de usuário final de alto desempenho. O conteúdo de mídia transmitido por RTP é protegido por meio do SRTP.

Os usuários da sua solução dos Serviços de Comunicação se conectarão aos serviços nos respectivos dispositivos cliente. A comunicação entre esses dispositivos e seus servidores é processada com a **sinalização**. Por exemplo: há suporte para a inicialização de chamadas e o chat em tempo real por meio da sinalização entre os dispositivos e o seu serviço. A maior parte do tráfego de sinalização usa a REST HTTPS, porém, em alguns cenários, o SIP pode ser usado como um protocolo de tráfego de sinalização. Embora esse tipo de tráfego seja menos sensível à latência, a sinalização de baixa latência proporcionará aos usuários da sua solução uma experiência agradável para o usuário final.

### <a name="interoperability-restrictions"></a>Restrições de interoperabilidade

A mídia que flui pelos Serviços de Comunicação é restrita da seguinte maneira:

**Retransmissões de mídia de terceiros.** Não há suporte para a interoperabilidade com retransmissões de mídia de terceiros. Se um dos pontos de extremidade de mídia for os Serviços de Comunicação, a mídia só poderá percorrer as retransmissões de mídia nativas da Microsoft, incluindo aquelas que dão suporte ao Microsoft Teams e ao Skype for Business.

Um SBC (controlador de borda da sessão) de terceiros no limite com a PSTN deve encerrar o fluxo RTP/RTCP, protegido usando o SRTP, e não o retransmitir para o próximo salto. Se você retransmitir o fluxo para o próximo salto, ele não poderá ser compreendido.

**Servidores proxy SIP de terceiros.** Uma caixa de diálogo do SIP de sinalização dos Serviços de Comunicação com um SBC e/ou um gateway de terceiros pode percorrer os proxies SIP nativos da Microsoft (assim como o Teams). Não há suporte para a interoperabilidade com proxies SIP de terceiros.

**B2BUA (ou SBC) de terceiros.** O fluxo de mídia bidirecional dos Serviços de Comunicação na PSTN é encerrado por um SBC de terceiros. Não há suporte para a interoperabilidade com um SBC de terceiros na rede dos Serviços de Comunicação (em que um SBC de terceiros faz a mediação de dois pontos de extremidade dos Serviços de Comunicação).

### <a name="unsupported-technologies"></a>Tecnologias sem suporte

**Rede VPN.** Os Serviços de Comunicação não dão suporte à transmissão de mídia por VPNs. Se os usuários estiverem usando clientes VPN, o cliente deverá dividir e rotear o tráfego de mídia em uma conexão não VPN, conforme especificado em [Como permitir que a mídia do Lync ignore um túnel VPN.](https://techcommunity.microsoft.com/t5/skype-for-business-blog/enabling-lync-media-to-bypass-a-vpn-tunnel/ba-p/620210)

*Observação. Embora o título indique o Lync, ele é aplicável ao Teams e aos Serviços de Comunicação do Azure.*

**Modeladores de pacotes.** Não há suporte para dispositivos de captura, inspeção ou modelagem de pacote. Eles podem degradar a qualidade de maneira significativa.

### <a name="call-flow-principles"></a>Princípios do fluxo de chamadas

Há quatro princípios gerais que fundamentam os fluxos de chamadas dos Serviços de Comunicação:

* **O primeiro participante de uma chamada em grupo dos Serviços de Comunicação determina a região em que a chamada é hospedada**. Há exceções a essa regra em algumas topologias, que são descritas abaixo.
* **O ponto de extremidade de mídia usado para dar suporte a uma chamada dos Serviços de Comunicação é selecionado com base nas necessidades de processamento de mídia** e não é afetado pelo número de participantes em uma chamada. Por exemplo, uma chamada ponto a ponto pode usar um ponto de extremidade de mídia na nuvem para processar a mídia para transcrição ou gravação, enquanto uma chamada com dois participantes pode não usar nenhum ponto de extremidade de mídia. As chamadas em grupo usarão um ponto de extremidade de mídia para fins de mixagem e roteamento. Esse ponto de extremidade é selecionado com base na região em que a chamada está hospedada. O tráfego de mídia enviado de um cliente para o ponto de extremidade de mídia pode ser roteado diretamente ou usar uma retransmissão de transporte no Azure se as restrições de firewall de rede do cliente exigem isso.
* **O tráfego de mídia para chamadas ponto a ponto usa a rota mais direta disponível**, supondo que a chamada não precise de um ponto de extremidade de mídia na nuvem. A rota preferencial é direcionada para o par remoto (cliente). Se uma rota direta não estiver disponível, uma ou mais retransmissões de transporte retransmitirão o tráfego. O tráfego de mídia não deve percorrer os servidores que funcionam como modeladores de pacotes, servidores VPN ou outras funções que podem atrasar o processamento e degradar a experiência do usuário final.
* **O tráfego de sinalização sempre vai para qualquer servidor mais próximo do usuário.**

Para saber mais sobre os detalhes sobre o caminho de mídia escolhido, veja a [documentação conceitual dos fluxos de chamadas](./call-flows.md).


## <a name="call-flows-in-various-topologies"></a>Fluxos de chamadas em várias topologias

### <a name="communication-services-internet"></a>Serviços de Comunicação (Internet)

Essa topologia é usada pelos clientes que usam os Serviços de Comunicação na nuvem sem nenhuma implantação local, como a interface SIP. Nessa topologia, o tráfego nos Serviços de Comunicação flui pela Internet.

:::image type="content" source="./media/call-flows/detailed-flow-general.png" alt-text="Topologia dos Serviços de Comunicação do Azure.":::

*Figura 1 – Topologia dos Serviços de Comunicação*

A direção das setas no diagrama acima reflete a direção da inicialização da comunicação que afeta a conectividade nos perímetros da empresa. No caso do UDP para mídia, os primeiros pacotes podem fluir na direção inversa, mas esses pacotes podem ser bloqueados até que os pacotes na outra direção estejam fluindo.

Descrições de fluxo:
* Fluxo 2* – Representa um fluxo iniciado por um usuário na rede do cliente para a Internet como parte da experiência dos Serviços de Comunicação do usuário. Exemplos desses fluxos incluem a transmissão de mídia de DNS e ponto a ponto.
* Fluxo 2 – Representa um fluxo iniciado por um usuário móvel remoto dos Serviços de Comunicação com a VPN para a rede do cliente.
* Fluxo 3 – Representa um fluxo iniciado por um usuário móvel remoto dos Serviços de Comunicação para os pontos de extremidade dos Serviços de Comunicação.
* Fluxo 4 – Representa um fluxo iniciado por um usuário na rede do cliente para os Serviços de Comunicação.
* Fluxo 5 – Representa um fluxo de mídia ponto a ponto entre um usuário dos Serviços de Comunicação e outro na rede do cliente.
* Fluxo 6 – Representa um fluxo de mídia ponto a ponto entre um usuário móvel remoto dos Serviços de Comunicação e outro usuário móvel remoto dos Serviços de Comunicação pela Internet.

### <a name="use-case-one-to-one"></a>Caso de uso: Um para um

As chamadas individuais usam um modelo comum no qual o chamador obterá um conjunto de candidatos que consistem em endereços IP/portas, incluindo candidatos de localização, retransmissão e reflexivos (endereço IP público do cliente, conforme visto pela retransmissão). O chamador envia esses candidatos para a parte chamada; a parte chamada também obtém um conjunto semelhante de candidatos e os envia ao chamador. As mensagens de verificação de conectividade do STUN são usadas para descobrir quais caminhos de mídia do chamador/da parte chamada funcionam e o melhor caminho de trabalho é escolhido. A mídia (ou seja, pacotes RTP/RTCP protegidos usando o SRTP) é enviada por meio do par de candidato selecionado. A retransmissão de transporte é implantada como parte dos Serviços de Comunicação do Azure.

Se os candidatos de endereço IP local/porta ou os candidatos reflexivos tiverem conectividade, o caminho direto entre os clientes (ou por meio de um NAT) será selecionado para a mídia. Se os clientes estiverem na rede do cliente, o caminho direto deverá ser selecionado. Isso exige conectividade UDP direta na rede do cliente. Se os clientes forem usuários de nuvem nômades, dependendo da NAT/do firewall, a mídia poderá usar a conectividade direta.

Se um cliente for interno na rede do cliente e um cliente for externo (por exemplo, um usuário de nuvem móvel), será improvável que a conectividade direta entre os candidatos locais ou reflexivos esteja funcionando. Nesse caso, uma opção é usar um dos candidatos de retransmissão de transporte de um dos clientes (por exemplo, o cliente interno obteve um candidato de retransmissão da retransmissão de transporte no Azure; o cliente externo precisa conseguir enviar os pacotes STUN/RTP/RTCP para a retransmissão de transporte). Outra opção é o cliente interno enviar para o candidato de retransmissão obtido pelo cliente de nuvem móvel. Embora a conectividade UDP para mídia seja altamente recomendada, há suporte para TCP.

**Etapas de alto nível:**
1.  o Usuário A dos Serviços de Comunicação resolve o DNS (nome de domínio) da URL usando o Fluxo 2.
2.  O Usuário A aloca uma porta de retransmissão de mídia na retransmissão de transporte do Teams usando o Fluxo 4.
3.  O Usuário A dos Serviços de Comunicação envia um "convite" com os candidatos do ICE usando o Fluxo 4 para os Serviços de Comunicação.
4.  Os Serviços de Comunicação notificam o Usuário B usando o Fluxo 4.
5.  O Usuário B aloca uma porta de retransmissão de mídia na retransmissão de transporte do Teams usando o Fluxo 4.
6.  O Usuário B envia a "resposta" com os candidatos do ICE usando o Fluxo 4, que é encaminhado novamente para o Usuário A usando o Fluxo 4.
7.  O Usuário A e o Usuário B invocam testes de conectividade do ICE e o melhor caminho de mídia disponível é selecionado (confira os diagramas abaixo para conhecer os vários casos de uso).
8.  Os dois usuários enviam a telemetria para os Serviços de Comunicação por meio do Fluxo 4.

### <a name="customer-network-intranet"></a>Rede do cliente (intranet)

:::image type="content" source="./media/call-flows/one-to-one-internal.png" alt-text="Fluxo de tráfego na rede do cliente.":::

*Figura 2 – Na rede do cliente*

Na Etapa 7, o Fluxo 5 da mídia ponto a ponto é selecionado.

Essa transmissão de mídia é bidirecional. A direção do Fluxo 5 indica que um lado inicia a comunicação da perspectiva de conectividade. Nesse caso, não importa qual direção é usada, porque os dois pontos de extremidade estão na rede do cliente.

### <a name="customer-network-to-external-user-media-relayed-by-teams-transport-relay"></a>Rede do cliente para usuário externo (mídia retransmitida pela retransmissão de transporte do Teams)

:::image type="content" source="./media/call-flows/one-to-one-via-relay.png" alt-text="Fluxo de chamada individual por meio de uma retransmissão.":::

*Figura 3 – Rede do cliente para usuário externo (mídia retransmitida pela retransmissão de transporte do Azure)*

Na Etapa 7, o Fluxo 4 (da rede do cliente para os Serviços de Comunicação) e o Fluxo 3 (de um usuário móvel remoto dos Serviços de Comunicação para os Serviços de Comunicação do Azure) estão selecionados.

Esses fluxos são retransmitidos pela retransmissão de transporte do Teams no Azure.

Essa transmissão de mídia é bidirecional. A direção indica qual lado inicia a comunicação da perspectiva de conectividade. Nesse caso, esses fluxos são usados para sinalização e mídia, usando diferentes protocolos de transporte e endereços.

### <a name="customer-network-to-external-user-direct-media"></a>Rede do cliente para usuário externo (mídia direta)

:::image type="content" source="./media/call-flows/one-to-one-with-extenal.png" alt-text="Fluxo de chamada individual com um usuário externo.":::

*Figura 4 – Rede do cliente para usuário externo (mídia direta)*

Na etapa 7, o Fluxo 2 (da rede do cliente para o par do cliente pela Internet) está selecionado.

A mídia direta com um usuário móvel remoto (não retransmitida pelo Azure) é opcional. Em outras palavras, você pode bloquear esse caminho para impor um caminho de mídia por meio de uma retransmissão de transporte no Azure.

Essa transmissão de mídia é bidirecional. A direção do Fluxo 2 para o usuário móvel remoto indica que um lado inicia a comunicação da perspectiva de conectividade.

### <a name="vpn-user-to-internal-user-media-relayed-by-teams-transport-relay"></a>Usuário de VPN para usuário interno (mídia retransmitida pela retransmissão de transporte do Teams)

:::image type="content" source="./media/call-flows/vpn-to-internal-via-relay.png" alt-text="Fluxo de chamada individual com um usuário de VPN pela retransmissão.":::

*Figura 5 – Usuário de VPN para usuário interno (mídia retransmitida pela Retransmissão do Azure)*

A sinalização entre a VPN e a rede do cliente usa o Fluxo 2*. A sinalização entre a rede do cliente e o Azure usa o Fluxo 4. No entanto, a mídia ignora a VPN e é roteada usando os Fluxos 3 e 4 por meio da Retransmissão de Mídia do Azure.

### <a name="vpn-user-to-internal-user-direct-media"></a>Usuário de VPN para usuário interno (mídia direta)

:::image type="content" source="./media/call-flows/vpn-to-internal-direct-media.png" alt-text="Fluxo de chamada individual (usuário interno) com uma VPN com mídia direta":::

*Figura 6 – Usuário de VPN para usuário interno (mídia direta)*

A sinalização entre a VPN e a rede do cliente usa o Fluxo 2. A sinalização entre a rede do cliente e o Azure usa o fluxo 4. No entanto, a mídia ignora a VPN e é roteada usando o fluxo 2 da rede do cliente para a Internet.

Essa transmissão de mídia é bidirecional. A direção do Fluxo 2 para o usuário móvel remoto indica que um lado inicia a comunicação da perspectiva de conectividade.

### <a name="vpn-user-to-external-user-direct-media"></a>Usuário de VPN para usuário externo (mídia direta)

:::image type="content" source="./media/call-flows/vpn-user-to-external-user.png" alt-text="Fluxo de chamada individual (usuário externo) com uma VPN com mídia direta":::

*Figura 7 – Usuário de VPN para usuário externo (mídia direta)*

A sinalização entre o usuário de VPN para a rede do cliente usa o Fluxo 2* e o Fluxo 4 para o Azure. No entanto, a mídia ignora a VPN e é roteada usando o Fluxo 6.

Essa transmissão de mídia é bidirecional. A direção do Fluxo 6 para o usuário móvel remoto indica que um lado inicia a comunicação da perspectiva de conectividade.

### <a name="use-case-communication-services-client-to-pstn-through-communication-services-trunk"></a>Caso de uso: cliente dos Serviços de Comunicação para PSTN por meio do Tronco dos Serviços de Comunicação

Os Serviços de Comunicação permitem fazer e receber chamadas na PSTN (Rede Telefônica Pública Comutada). Se o tronco da PSTN estiver conectado com números de telefone fornecidos pelos Serviços de Comunicação, não haverá nenhum requisito de conectividade especial para esse caso de uso. Caso deseje conectar seu tronco da PSTN local aos Serviços de Comunicação do Azure, use a interface SIP (disponível no CY2021).

:::image type="content" source="./media/call-flows/acs-to-pstn.png" alt-text="Chamada individual com um participante da PSTN":::

*Figura 8 – Usuário dos Serviços de Comunicação para a PSTN por meio do Tronco do Azure*

Nesse caso, a sinalização e a mídia da rede do cliente para o Azure usam o Fluxo 4.

### <a name="use-case-communication-services-group-calls"></a>Caso de uso: chamadas em grupo dos Serviços de Comunicação

O serviço de compartilhamento de áudio/vídeo/tela (VBSS) faz parte dos Serviços de Comunicação do Azure. Ele tem um endereço IP público que precisa ser acessado pela rede do cliente e ser acessível em um cliente de nuvem nômade. Cada cliente/ponto de extremidade precisa conseguir se conectar ao serviço.

Os clientes internos obterão candidatos locais, reflexivos e de retransmissão da mesma forma descrita para as chamadas individuais. Os clientes enviarão esses candidatos para o serviço em um convite. O serviço não usa uma retransmissão, pois tem um endereço IP acessível publicamente. Portanto, ele responde com o candidato de endereço IP local dele. O cliente e o serviço verificarão a conectividade da mesma forma descrita para as chamadas individuais.

:::image type="content" source="./media/call-flows/acs-group-calls.png" alt-text="Chamada em grupo do OACS":::

*Figura 9 – Chamadas em grupo dos Serviços de Comunicação*

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução a chamadas](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Os seguintes documentos podem ser do seu interesse:

- Saiba mais sobre os [tipos de chamada](../concepts/voice-video-calling/about-call-types.md)
- Saiba mais sobre a [Arquitetura de cliente/servidor](./client-and-server-architecture.md)