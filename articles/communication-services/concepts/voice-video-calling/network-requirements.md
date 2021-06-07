---
title: Preparar a rede da sua organização para os Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre os requisitos de rede para a chamada de voz e vídeo dos Serviços de Comunicação do Azure
author: nmurav
manager: jken
services: azure-communication-services
ms.author: nmurav
ms.date: 3/23/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 22bf8af89771e57621a0d8851ca31bb750b7cda2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108342"
---
# <a name="ensure-high-quality-media-in-azure-communication-services"></a>Garantir uma mídia de alta qualidade nos Serviços de Comunicação do Azure

Este documento fornece uma visão geral dos fatores e das melhores práticas que devem ser consideradas ao criar experiências de comunicação de multimídia de alta qualidade com os Serviços de Comunicação do Azure.

## <a name="factors-that-affect-media-quality-and-reliability"></a>Fatores que afetam a qualidade e a confiabilidade da mídia

Há muitos fatores diferentes que contribuem para a qualidade de mídia em tempo real dos Serviços de Comunicação do Azure (compartilhamento de áudio, vídeo e aplicativos). Isso inclui configurações de dispositivo, host, firewall, largura de banda e qualidade da rede.


### <a name="network-quality"></a>Qualidade da rede

A qualidade da mídia em tempo real por IP é significativamente afetada pela qualidade da conectividade da rede subjacente, mas especialmente pela quantidade de:
* **Latência**. Esse é o tempo necessário para obter um pacote IP do ponto A para o ponto B na rede. Esse atraso na propagação da rede é determinado pela distância física entre os dois pontos e por qualquer sobrecarga adicional gerada pelos dispositivos pelos quais o tráfego flui. A latência é medida como RTT (tempo de ida e volta) ou unidirecional.
* **Perda de pacote**. Um percentual de pacotes que são perdidos em determinado período. A perda de pacotes afeta diretamente a qualidade do áudio: de pequenos pacotes perdidos individuais sem praticamente nenhum impacto a perdas de intermitência back-to-back que causam o corte completo do áudio.
* **Tremulação de chegada entre pacotes ou simplesmente tremulação**. Essa é a alteração média no atraso entre pacotes sucessivos. Os Serviços de Comunicação do Azure podem se adaptar a alguns níveis de tremulação por meio do buffer. Somente quando a tremulação exceder o buffer, um participante perceberá os efeitos dela.

### <a name="network-bandwidth"></a>Largura de banda da rede

Verifique se a rede está configurada para dar suporte à largura de banda exigida pelas sessões de mídia simultâneas dos Serviços de Comunicação do Azure e por outros aplicativos de negócios. Testar o caminho de rede de ponta a ponta em busca de gargalos de largura de banda é essencial para a implantação bem-sucedida da sua solução multimídia dos Serviços de Comunicação.

Veja abaixo os requisitos de largura de banda para os SDKs do JavaScript:

|Largura de banda|Cenários|
|:--|:--|
|40 Kbps|Chamada de áudio ponto a ponto|
|500 Kbps|Chamada de áudio ponto a ponto e compartilhamento de tela|
|500 Kbps|Chamada de vídeo de qualidade ponto a ponto 360p em 30fps|
|1,2 Mbps|Chamada de vídeo de qualidade HD ponto a ponto com a resolução de HD 720p em 30fps|
|500 Kbps|Chamada de vídeo em grupo 360p em 30fps|
|1,2 Mbps|Chamada de vídeo em grupo HD com a resolução de HD 720p em 30fps| 

Veja abaixo os requisitos de largura de banda para os SDKs nativos do Android e do iOS:

|Largura de banda|Cenários|
|:--|:--|
|30 Kbps|Chamada de áudio ponto a ponto |
|130 Kbps|Chamada de áudio ponto a ponto e compartilhamento de tela|
|500 Kbps|Chamada de vídeo de qualidade ponto a ponto 360p em 30fps|
|1,2 Mbps|Chamada de vídeo de qualidade HD ponto a ponto com a resolução de HD 720p em 30fps|
|1.5 Mbps|Chamada de vídeo de qualidade HD ponto a ponto com a resolução de HD 1080p em 30fps |
|500 Kbps/1 Mbps|Chamada de vídeo em grupo|
|1 Mbps/2 Mbps|Chamada de vídeo em grupo HD (vídeos de 540p em uma tela de 1080p)|

### <a name="firewalls-configuration"></a>Configuração dos firewalls

As conexões dos Serviços de Comunicação do Azure exigem conectividade com a Internet para portas específicas e endereços IP a fim de fornecer experiências multimídia de alta qualidade. Sem acesso a essas portas e esses endereços IP, os Serviços de Comunicação do Azure ainda podem funcionar. No entanto, a experiência ideal é fornecida quando as portas e os intervalos de IP recomendados estão abertos.

| Categoria | Intervalos de IP ou FQDN | Portas | 
| :-- | :-- | :-- |
| Tráfego de mídia | [Intervalo de endereços IP de nuvem pública do Azure](https://www.microsoft.com/download/confirmation.aspx?id=56519) | UDP 3478 a 3481, portas TCP 443 |
| Sinalização, telemetria e registro| *.skype.com, *.microsoft.com, *.azure.net, *.azureedge.net, *.office.com, *.trouter.io | TCP 443, 80 |

### <a name="network-optimization"></a>Otimização da rede

As tarefas a seguir são opcionais e não são necessárias para distribuir os Serviços de Comunicação do Azure. Use estas diretrizes para otimizar o desempenho da rede e dos Serviços de Comunicação do Azure ou se você souber que tem algumas limitações de rede.
O ideal é otimizá-lo ainda mais se:
* Os Serviços de Comunicação do Azure são executados lentamente (talvez você tenha largura de banda insuficiente)
* As chamadas continuam sendo removidas (talvez devido a bloqueadores de proxy ou firewall)
* As chamadas são estáticas e cortadas ou têm vozes semelhantes a robôs (talvez devido à tremulação ou a perda de pacotes)

| Tarefa de otimização da rede | Detalhes |
| :-- | :-- |
| Planeje a sua rede | Nesta documentação, você poderá encontrar requisitos mínimos referente a chamadas para sua rede. Veja o [exemplo do Teams para planejar sua rede](https://docs.microsoft.com/microsoftteams/tutorial-network-planner-example) |
| Resolução de nomes externa | Verifique se todos os computadores que executam os SDKs dos Serviços de Comunicação do Azure podem resolver consultas DNS externas para descobrir os serviços fornecidos pelos Serviços de Comunicação do Azure e que os seus firewalls não estão impedindo o acesso. Verifique se os SDKs podem resolver os endereços *.skype.com, *.microsoft.com, *.azure.net, *.azureedge.net, *.office.com, *.trouter.io  |
| Manter a persistência da sessão | Verifique se o firewall não altera os endereços NAT (conversão de endereços de rede) mapeados ou as portas para UDP
Validar o tamanho do pool da NAT | Valide o tamanho do pool da NAT (conversão de endereços de rede) necessário para a conectividade do usuário. Quando vários usuários e dispositivos acessam os Serviços de Comunicação do Azure usando a [NAT (conversão de endereços de rede) ou o PAT (conversão de endereços de porta)](https://docs.microsoft.com/office365/enterprise/nat-support-with-office-365), verifique se os dispositivos ocultos protegidos pelos endereços IP publicamente roteáveis não excedem o número compatível. Verifique se os endereços IP públicos adequados são atribuídos aos pools da NAT para impedir o esgotamento de porta. O esgotamento de porta contribuirá com a incapacidade de conexão dos dispositivos e dos usuários internos aos Serviços de Comunicação do Azure |
| Diretrizes de prevenção e detecção de intrusões | Se o seu ambiente tem um IDS/IPS (sistema de prevenção ou de [detecção de intrusões](https://docs.microsoft.com/azure/network-watcher/network-watcher-intrusion-detection-open-source-tools)) implantado para uma camada extra de segurança das conexões de saída, permita todas as URLs dos Serviços de Comunicação do Azure |
| Configurar a VPN de túnel dividido | Recomendamos que você forneça um caminho alternativo para o tráfego do Teams que ignore a VPN (rede virtual privada), normalmente conhecida como [VPN de túnel dividido](https://docs.microsoft.com/windows/security/identity-protection/vpn/vpn-routing). O túnel dividido significa que o tráfego para os Serviços de Comunicação do Azure não passa pela VPN, mas, em vez disso, vai diretamente para o Azure. Ignorar a VPN terá um impacto positivo na qualidade da mídia e reduzirá a carga dos dispositivos VPN e da rede da organização. Para implementar uma VPN de túnel dividido, trabalhe com seu fornecedor de VPN. Outros motivos pelos quais recomendamos ignorar a VPN: <ul><li> As VPNs normalmente não foram projetadas nem configuradas para dar suporte à mídia em tempo real.</li><li> As VPNs também podem não dar suporte ao UDP (que é necessário para os Serviços de Comunicação do Azure)</li><li>As VPNs também introduzem uma camada extra de criptografia no tráfego de mídia que já está criptografado.</li><li>A conectividade com os Serviços de Comunicação do Azure pode não ser eficiente devido ao tráfego de hairpinning por meio de um dispositivo VPN.</li></ul>|
| Implementar a QoS | [Use a QoS (Qualidade de Serviço)](https://docs.microsoft.com/microsoftteams/qos-in-teams) para configurar a priorização de pacotes. Isso vai aprimorar a qualidade da chamada e ajudar você a monitorar e solucionar problemas de qualidade da chamada. A QoS deve ser implementada em todos os segmentos de uma rede gerenciada. Mesmo quando uma rede tiver sido provisionada adequadamente para largura de banda, a QoS fornecerá mitigação de risco em caso de eventos de rede não previstos. Com a QoS, o tráfego de voz é priorizado para que esses eventos não previstos não prejudiquem a qualidade. | 
| Otimizar o Wi-Fi | Semelhante à VPN, as redes Wi-Fi não foram necessariamente projetadas nem configuradas para dar suporte à mídia em tempo real. Planejar ou otimizar uma rede Wi-Fi para dar suporte aos Serviços de Comunicação do Azure é uma consideração importante para uma implantação de alta qualidade. Considere estes fatores: <ul><li>Implemente a QoS ou o protocolo WMM (WiFi Multimedia) para garantir que o tráfego de mídia esteja sendo priorizado adequadamente nas redes Wi-Fi.</li><li>Planeje e otimize as bandas de Wi-Fi e o posicionamento do ponto de acesso. O intervalo de 2,4 GHz pode fornecer uma experiência adequada dependendo do posicionamento do ponto de acesso, mas os pontos de acesso geralmente são afetados por outros dispositivos de consumo que operam nesse intervalo. O intervalo de 5 GHz é mais adequado para a mídia em tempo real devido ao intervalo denso, porém, exige mais pontos de acesso para obter cobertura suficiente. Os pontos de extremidade também precisam dar suporte a esse intervalo e ser configurados para aproveitar essas bandas de acordo.</li><li>Se você está usando redes Wi-Fi de banda dupla, considere a possibilidade de implementar o direcionamento de banda. O direcionamento de banda é uma técnica implementada por fornecedores de Wi-Fi para influenciar os clientes de banda dupla a usar o intervalo de 5 GHz.</li><li>Quando os pontos de acesso do mesmo canal estão muito próximos, eles podem causar a sobreposição de sinais e uma competição não intencional, resultando em uma experiência de usuário degradada. Verifique se os pontos de acesso próximos uns dos outros estão em canais que não se sobreponham.</li></ul> Cada fornecedor de rede sem fio tem recomendações próprias para implantar a solução sem fio. Consulte seu fornecedor de Wi-Fi para obter diretrizes específicas.|



### <a name="operating-system-and-browsers-for-javascript-sdks"></a>Sistema operacional e navegadores (para SDKs do JavaScript)

Os SDKs de voz/vídeo dos Serviços de Comunicação do Azure dão suporte a alguns sistemas operacionais e navegadores.
Saiba mais sobre os sistemas operacionais e os navegadores aos quais os SDKs de chamada dão suporte na [documentação conceitual de chamadas](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features).

## <a name="next-steps"></a>Próximas etapas

Os seguintes documentos podem ser do seu interesse:

- Saiba mais sobre as [bibliotecas de chamada](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features)
- Saiba mais sobre a [Arquitetura de cliente/servidor](https://docs.microsoft.com/azure/communication-services/concepts/client-and-server-architecture)
- Saiba mais sobre [Topologias do fluxo de chamadas](https://docs.microsoft.com/azure/communication-services/concepts/call-flows)
