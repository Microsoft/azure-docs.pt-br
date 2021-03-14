---
title: Requisitos de rede
description: Requisitos de rede e práticas recomendadas de rede para uma experiência ideal
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: fd8686cc396d5fcee20590fbac8bccaf187b024d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735940"
---
# <a name="network-requirements"></a>Requisitos de rede

Uma conexão de rede estável e de baixa latência com um datacenter do Azure é essencial para uma boa experiência do usuário no Azure Remote Rendering. Condições de rede precárias podem resultar em quedas de conexão, hologramas instáveis, tremendo ou “saltando”, e em um retardo perceptível ao atualizar o grafo de cena do lado do servidor.

## <a name="guidelines-for-network-connectivity"></a>Diretrizes para conectividade de rede

Os requisitos de rede exatos dependem do seu caso de uso específico, como o número e a frequência de modificações no grafo de cena remota, assim como a complexidade da exibição renderizada, mas há várias diretrizes para garantir que sua experiência seja a melhor possível:

* Sua conectividade com a Internet precisa dar suporte a pelo menos **40 Mbps de downstream** e **5 Mbps de upstream** consistentemente para uma única sessão de usuário do Azure Remote Rendering, supondo que não haja tráfego de concorrência na rede. Recomendamos taxas mais altas para melhores experiências. 
* **Wi-Fi** é o tipo de rede recomendado, pois dá suporte a uma conexão de baixa latência, alta banda e estável. Algumas redes móveis apresentam tremulação que pode levar a uma experiência ruim. 
* Usar a **banda Wi-Fi de 5 GHz** geralmente produzirá resultados melhores do que a banda Wi-Fi de 2,4 GHz, embora ambas devam funcionar.
* Se houver outras redes Wi-Fi próximas, evite usar canais Wi-Fi em uso por essas outras redes. Você pode usar ferramentas de verificação de rede como [WifiInfoView](https://www.nirsoft.net/utils/wifi_information_view.html) para verificar se os canais que sua rede Wi-Fi usa estão livres de tráfego concorrente.
* **Evite estritamente o uso de repetidores Wi-Fi** ou o encaminhamento de LAN sobre powerline.
* **Evite tráfegos intensos com concorrência de largura de banda** – como vídeo ou streaming de jogos – na mesma rede Wi-Fi.
* Se você tiver vários dispositivos no mesmo ponto de acesso, os requisitos aumentarão de forma correspondente. Se você tiver vários pontos de acesso em um ambiente, balancear a carga de dispositivos entre os pontos de acesso, para que eles sejam distribuídos uniformemente.
* Ter uma **boa intensidade de sinal Wi-Fi** é essencial. Se possível, permaneça perto do seu ponto de acesso Wi-Fi e evite obstáculos entre o dispositivo cliente e os pontos de acesso.
* Certifique-se de que você sempre se conecte ao **datacenter do Azure** mais próximo para sua [região](regions.md). Quanto mais próximo o datacenter, menor a latência de rede, que tem um grande efeito na estabilidade do holograma.

> [!NOTE]
> A largura de banda downstream é consumida principalmente por streaming de vídeo, que, por sua vez, é dividido entre as informações de cor e de profundidade (60 Hz, estéreo).

## <a name="network-performance-tests"></a>Testes de desempenho de rede

Se você quiser ter um entendimento inicial sobre se a qualidade de sua conectividade de rede é suficiente para executar o Azure Remote Rendering, existem ferramentas online que você pode usar. É altamente recomendável executar essas ferramentas online em um laptop razoavelmente poderoso conectado à mesma Wi-Fi que o dispositivo no qual você está planejando executar seu aplicativo cliente do Azure Remote Rendering. Os resultados obtidos da execução dos testes em um celular ou HoloLens2 geralmente são menos úteis, pois eles mostraram uma variação significativa em dispositivos de ponto de extremidade de baixa potência. O local no qual você coloca o laptop deve estar aproximadamente no mesmo local onde você espera usar o dispositivo que executa o aplicativo cliente do Azure Remote Rendering.

Aqui estão algumas etapas simples para um teste rápido de sua conectividade de rede:

1. **Execute uma ferramenta de teste de rede como www.speedtest.net para obter dados sobre a latência geral e a largura de banda de upstream/downstream de sua conexão de rede.**
Escolha um servidor mais próximo de você e execute o teste. Embora o servidor não seja o datacenter do Azure ao qual o Azure Remote Rendering se conectará, os dados resultantes ainda serão úteis para entender o desempenho da conexão de Internet e de Wi-Fi.
   * **Requisito mínimo** para o Azure Remote Rendering: Aprox. 40 Mbps downstream e 5 Mbps upstream.
   * **Recomendado** para o Azure Remote Rendering: Aprox. 100 Mbps downstream e 10 Mbps upstream.
É recomendável executar o teste várias vezes e considerar os piores resultados.
1. **Use uma ferramenta como www.azurespeed.com que mede a latência para datacenters do Azure**. Selecione o datacenter do Azure compatível com o Azure Remote Rendering que está mais próximo de você (consulte [regiões com suporte](regions.md)) e execute um **teste de latência**. Se houver variação nos números exibidos, espere os resultados estabilizarem.
   * **Requisito mínimo** para renderização remota do Azure: a latência deve ser de forma consistente menor que 80 ms.
   * **Recomendado** para renderização remota do Azure: a latência deve ser consistentemente menor que 40 MS.

Embora a baixa latência não seja uma garantia de que o Azure Remote Rendering funcionará bem em sua rede, normalmente vimos que ela tem um bom desempenho em situações em que esses testes tiveram um resultado positivo.
Se você estiver encontrando artefatos como hologramas instáveis, tremendo ou “saltando” ao executar o Azure Remote Rendering, consulte o [guia de solução de problemas](../resources/troubleshoot.md).

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Renderizar um modelo com o Unity](../quickstarts/render-model.md)
