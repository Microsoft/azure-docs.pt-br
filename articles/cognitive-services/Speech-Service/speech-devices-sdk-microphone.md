---
title: Recomendações de matriz do microfone do SDK de dispositivos de fala – serviço de fala
titleSuffix: Azure Cognitive Services
description: Recomendações de matriz do microfone do SDK de dispositivos de fala. As geometrias de matriz a seguir são recomendadas para uso com a pilha de áudio da Microsoft. A localização de fontes de som e a rejeição do ruído de ambiente são aprimoradas com um número maior de microfones com dependências em aplicativos específicos, cenários de usuário e o fator forma de dispositivo.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: b110e9ddc42d07c8356eb25b9ea2294e470d5fdc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558959"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Recomendações de matriz do microfone do SDK de dispositivos de fala

Neste artigo, você aprenderá a criar uma matriz de microfone para o SDK de dispositivos de fala.

O SDK dos dispositivos de fala funciona melhor com uma matriz de microfone que foi projetada de acordo com as diretrizes a seguir, incluindo a geometria do microfone e a seleção de componentes. As diretrizes também são fornecidas em considerações elétricas e de integração.

## <a name="microphone-geometry"></a>Geometria do microfone

As geometrias de matriz a seguir são recomendadas para uso com a pilha de áudio da Microsoft. A localização de fontes de som e a rejeição do ruído de ambiente são aprimoradas com um número maior de microfones com dependências em aplicativos específicos, cenários de usuário e o fator forma de dispositivo.

|          | Matriz circular    |       |  Matriz linear              |                |
|----------|-------------------|-------------------|----------------|----------------|
|          |<img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/>|<img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/>|<img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/>|<img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/>|
| \#Mics  | 7                 | 4                 | 4              | 2              |
| Geometria | 6 externa, 1 Center, RADIUS = 42,5 mm, com espaçamento uniforme| 3 externo, 1 Center, RADIUS = 42,5 mm, com espaçamento uniforme | Comprimento = 120 mm, espaçamento = 40 mm | Espaçamento = 40 mm |

Os canais de microfone devem ser ordenados de acordo com a numeração representada para cada matriz acima, aumentando de 0.  A pilha de áudio da Microsoft exigirá um fluxo de referência adicional de reprodução de áudio para executar o cancelamento de eco.

## <a name="component-selection"></a>Seleção de componentes

Os componentes do microfone devem ser selecionados para reproduzir com precisão um sinal sem ruído e distorção.

As propriedades recomendadas ao selecionar microfones são:

| Parâmetro                         | Recomendado                       |
|-----------------------------------|-----------------------------------|
| SNR                               | \>= 65 dB (sinal de 1 kHz 94 dBSPL, ruído de peso)   |
| Correspondência de amplitude                | ± 1 dB @ 1 kHz                     |
| Correspondência de fase                    | ± 2 ° @ 1 kHz                       |
| Ponto de sobrecarga acústica (AOP)     | \>= 120 dBSPL (THD = 10%)          |
| Taxa de bits                          | Mínimo de 24 bits                    |
| Taxa de amostragem                     | Mínimo de 16 kHz\*                   |
| Resposta de frequência                | ± 3 dB, 200-8000 Hz máscara flutuante\*|
| Confiabilidade                       | Intervalo de temperatura de armazenamento-40 ° c a 70 ° c<br />Intervalo de temperatura operacional-20 ° c a 55 ° c  |

*\*Taxas de amostragem mais altas ou intervalos de frequência "mais largos" podem ser necessários para aplicativos de comunicação de alta qualidade (VoIP)*

Uma boa seleção de componente deve ser emparelhada com boa integração de Electroacoustic para evitar o emparelhamento do desempenho dos componentes usados. Casos de uso exclusivos também podem exigir requisitos adicionais (por exemplo: intervalos de temperatura operacionais).

## <a name="microphone-array-integration"></a>Integração de matriz de microfone

O desempenho da matriz de microfone quando integrada a um dispositivo será diferente da especificação do componente. É importante garantir que os microfones sejam bem combinados após a integração. Portanto, o desempenho do dispositivo medido após qualquer lucro ou EQ fixo deve atender às seguintes recomendações:

|  Parâmetro        |    Recomendado |
|--------------------|----------------------------------------------------|
|  SNR                 | \>63 dB (sinal de 1 kHz 94 dBSPL, ruído de peso) |
|  Sensibilidade de saída  | -26 dBFS/PA @ 1 kHz (recomendado) |
|  Correspondência de amplitude  | ± 2 dB, 200-8000 Hz |
| THD%\*                 | ≤ 1%, 200-8000 Hz, 94 dBSPL, 5º pedido |
|  Resposta de frequência  | ± 6 dB, 200-8000 Hz máscara flutuante\*\* |

*\*\*Um alto-falante de distorção é necessário para medir THD (por exemplo, Neumann KH120)*

*\*\*Intervalos de frequência "mais largos" podem ser necessários para aplicativos de comunicação de alta qualidade (VoIP)*

## <a name="speaker-integration-recommendations"></a>Recomendações de integração do palestrante

Como o cancelamento de eco é necessário para dispositivos de reconhecimento de fala que contêm alto-falantes, são fornecidas recomendações adicionais para a integração e a seleção de palestrante.

| Parâmetro                         | Recomendado                       |
|-----------------------------------|-----------------------------------|
| Considerações sobre linearidade          | Nenhum processamento não linear após a referência do alto-falante, caso contrário, é necessário um fluxo de referência de auto-retorno baseado em hardware  |
| Loopback do orador                  | Fornecido via WASAPI, APIs privadas, plug-in ALSA personalizado (Linux) ou fornecido por meio do canal de firmware      |
| THD%                              | 3ª Octave bandas no mínimo quinto pedido, 70 a reprodução do dBA @ 0,8 m ≤ 6,3%, 315-500 Hz ≤ 5%, 630-5000 Hz                 |
| Retornar acoplamento a microfones      | \>-10 dB TCLw usando ITU-T G. 122 anexo B. 4 método, normalizado para nível de Mic<br />TCLw = TCLwmeasured \+ (sensibilidade de saída de destino de nível medido)<br />TCLw = TCLwmeasured \+ (nível medido-(-26)) |

## <a name="integration-design-architecture"></a>Arquitetura de design de integração

As seguintes diretrizes para arquitetura são necessárias ao integrar microfones a um dispositivo:

| Parâmetro                         | Recomendação                    |
|-----------------------------------|-----------------------------------|
| Similaridade de porta do MIC               | Todas as portas de microfone têm o mesmo comprimento na matriz    |
| Dimensões da porta do MIC               | Tamanho da porta a partir de 0,8-1,0 mm. Comprimento da porta/diâmetro \< da porta 2              |
| Lacre do MIC                       | Lacrando gaskets uniformemente implementado na pilha. Taxa \> de compactação de 70% recomendada para gaskets de espuma     |
| Confiabilidade do MIC                   | A malha deve ser usada para evitar a poeira e a entrada (entre PCB para microfones portados inferiores e o selo da tampa Gasket/superior)  |
| Isolamento do MIC                     | Gaskets de borracha e vibração desacoplando por meio da estrutura, particularmente para isolar os caminhos de vibração devido aos alto-falantes integrados      |
| Relógio de amostragem                    | O áudio do dispositivo deve estar livre de tremulação e saídas com baixa descompasso    |
| Capacidade de registro                 | O dispositivo deve ser capaz de registrar fluxos brutos individuais de canal simultaneamente |
| USB                               | Todos os dispositivos de entrada de áudio USB devem definir descritores de acordo com a [especificação de Rev3 de dispositivos de áudio USB](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) |
| Geometria do microfone               | Os drivers devem implementar os descritores de [geometria da matriz de microfone](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) corretamente  |
| Descoberta                   | Os dispositivos não devem ter nenhum hardware, firmware ou algoritmos de processamento de áudio não linear baseados em software de terceiros, que sejam descobertos, de/para o dispositivo|
| Formato de captura                    | Os formatos de captura devem usar uma taxa de amostragem mínima de 16 kHz e uma profundidade de 24 bits recomendada      |

## <a name="electrical-architecture-considerations"></a>Considerações sobre arquitetura elétrica

Quando aplicável, as matrizes podem estar conectadas a um host USB (como um SoC que executa a pilha de áudio da Microsoft) e interfaces para os serviços de fala ou outros aplicativos.

Componentes de hardware, como a conversão de PDM a TDM, devem garantir que o intervalo dinâmico e SNR dos microfones sejam preservados dentro de reamostragens.

A classe de áudio USB de alta velocidade 2,0 deve ter suporte em qualquer MCUs de áudio para fornecer a largura de banda necessária para até sete canais em taxas de exemplo mais altas e profundidades de bits.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o SDK de dispositivos de fala](speech-devices-sdk.md)
