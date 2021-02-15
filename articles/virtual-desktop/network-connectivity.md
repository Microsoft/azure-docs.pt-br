---
title: Noções básicas sobre a conectividade de rede de área de trabalho virtual
titleSuffix: Azure
description: Saiba mais sobre a conectividade de rede de área de trabalho virtual do Windows
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: e4149864e16196b695d38a8c46ab5af835453412
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221203"
---
# <a name="understanding-windows-virtual-desktop-network-connectivity"></a>Noções básicas sobre a conectividade de rede de área de trabalho virtual

A área de trabalho virtual do Windows fornece a capacidade de hospedar sessões de cliente nos hosts de sessão em execução no Azure. A Microsoft gerencia partes dos serviços em nome do cliente e fornece pontos de extremidade seguros para conectar clientes e hosts de sessão. O diagrama a seguir fornece uma visão geral de alto nível das conexões de rede usadas pela área de trabalho virtual do Windows

:::image type="content" source="media/windows-virtual-desktop-network-connections.svg" alt-text="Diagrama de conexões de rede de área de trabalho virtual do Windows" lightbox="media/windows-virtual-desktop-network-connections.svg":::

## <a name="session-connectivity"></a>Conectividade de sessão

A área de trabalho virtual do Windows usa protocolo RDP (RDP) para fornecer recursos de exibição e entrada remotos em conexões de rede. O RDP foi lançado inicialmente com o Windows NT 4,0 Terminal Server Edition e estava evoluindo continuamente com todas as versões do Microsoft Windows e do Windows Server. Desde o início, o RDP foi desenvolvido para ser independente de sua pilha de transporte subjacente e, atualmente, ele dá suporte a vários tipos de transporte.

## <a name="reverse-connect-transport"></a>Transporte de conexão inversa

A área de trabalho virtual do Windows está usando o transporte de conexão reversa para estabelecer a sessão remota e para carregar o tráfego RDP. Ao contrário das implantações Serviços de Área de Trabalho Remota locais, o transporte de conexão reversa não usa um ouvinte TCP para receber conexões RDP recebidas. Em vez disso, ele está usando a conectividade de saída para a infraestrutura de área de trabalho virtual do Windows pela conexão HTTPS.

## <a name="session-host-communication-channel"></a>Canal de comunicação do host de sessão

Após a inicialização do host de sessão de área de trabalho virtual do Windows, o serviço carregador de agente de Área de Trabalho Remota estabelece o canal de comunicação persistente do agente de área de trabalho virtual do Windows. Esse canal de comunicação é colocado em camadas sobre uma conexão de TLS (segurança de camada de transporte) segura e serve como um barramento para troca de mensagens de serviço entre o host de sessão e a infraestrutura de área de trabalho virtual do Windows.

## <a name="client-connection-sequence"></a>Sequência de conexão do cliente

Sequência de conexão do cliente descrita abaixo:

1. Usar o usuário do cliente de área de trabalho virtual do Windows com suporte assina o espaço de trabalho virtual da área de trabalho
2. Azure Active Directory autentica o usuário e retorna o token usado para enumerar os recursos disponíveis para um usuário
3. O cliente passa o token para o serviço de assinatura do feed de área de trabalho virtual do Windows
4. O serviço de assinatura do feed de área de trabalho virtual do Windows valida o token
5. O serviço de assinatura do feed de área de trabalho virtual do Windows passa a lista de áreas de trabalho e RemoteApps disponíveis de volta para o cliente na forma de configuração de conexão digitalmente assinada
6. O cliente armazena a configuração de conexão de cada recurso disponível em um conjunto de arquivos. rdp
7. Quando um usuário seleciona o recurso para se conectar, o cliente usa o arquivo. rdp associado e estabelece a conexão TLS 1,2 segura com a instância do gateway de área de trabalho virtual do Windows mais próxima e passa as informações de conexão
8. O gateway de área de trabalho virtual do Windows valida a solicitação e solicita que o agente de área de trabalho virtual do Windows coordene a conexão
9. O agente de área de trabalho virtual do Windows identifica o host de sessão e usa o canal de comunicação persistente estabelecido anteriormente para inicializar a conexão
10. Área de Trabalho Remota pilha inicia a conexão TLS 1,2 com a mesma instância de gateway de área de trabalho virtual do Windows usada pelo cliente
11. Depois que o cliente e o host de sessão conectados ao gateway, o gateway começa a retransmitir os dados brutos entre os dois pontos de extremidade, isso estabelece o transporte de conexão inversa de base para o RDP
12. Depois que o transporte base é definido, o cliente inicia o handshake RDP

## <a name="connection-security"></a>Segurança da conexão

O TLS 1,2 é usado para todas as conexões iniciadas dos clientes e hosts de sessão para os componentes de infraestrutura de área de trabalho virtual do Windows. A área de trabalho virtual do Windows usa as mesmas codificações TLS 1,2 que o [Azure front door](../frontdoor/front-door-faq.md#what-are-the-current-cipher-suites-supported-by-azure-front-door). É importante garantir que os computadores cliente e os hosts de sessão possam usar essas codificações.
Para o transporte de conexão reversa, o cliente e o host de sessão se conectam ao gateway de área de trabalho virtual do Windows. Depois de estabelecer a conexão TCP, o cliente ou o host de sessão valida o certificado do gateway de área de trabalho virtual do Windows.
Depois de estabelecer o transporte base, o RDP estabelece uma conexão TLS aninhada entre o host de sessão e o cliente usando os certificados do host de sessão. Por padrão, o certificado usado para criptografia de RDP é gerado automaticamente pelo sistema operacional durante a implantação. Se desejar, os clientes podem implantar certificados gerenciados centralmente emitidos pela autoridade de certificação corporativa. Para obter mais informações sobre como configurar certificados, consulte a [documentação do Windows Server](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os requisitos de largura de banda para a área de trabalho virtual do Windows, confira [noções básicas sobre os requisitos de largura de banda protocolo RDP (RDP)](rdp-bandwidth.md)
* Para começar a usar a qualidade de serviço (QoS) para área de trabalho virtual do Windows, consulte [implementar a QoS (qualidade de serviço) para a área de trabalho virtual do Windows](rdp-quality-of-service-qos.md).
