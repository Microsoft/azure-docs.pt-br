---
title: Galeria de Plug-ins do Player de Mídia do Azure
description: Este artigo contém uma lista de plug-ins disponíveis para o Player de Mídia do Azure.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.openlocfilehash: d958d93731f38b4b553bca94f48c740c1b75cfd5
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449828"
---
# <a name="azure-media-player-plugin-gallery"></a>Galeria de Plug-ins do Player de Mídia do Azure #

## <a name="plugins"></a>Plug-ins ##

| Nome do plug-in                         | URL da demonstração                    | Código-fonte                | Descrição    |
|-------------------------------------|-----------------------------|----------------------------|----------------|
| Recursos Adicionais                 | | | |
| **Novo!** AMP360Video                | [Demonstração](http://www.babylonjs.com/demos/amp360video/)                        | [GitHub](https://github.com/BabylonJS/Extensions/tree/master/Amp360Video)                     | O plug-in permite visualizar vídeo em 360 graus em Amp em seu desktop ou em dispositivos incompatíveis com VR. A documentação completa está disponível [aqui](https://doc.babylonjs.com/extensions/amp360video): |
|  Sprite Tip                         | [Demonstração](https://www.smwcentral.net/?p=section&a=details&id=10301)                        | [GitHub](https://github.com/RickShahid/SpriteTip)                    | Plug-in AMP (Player de Mídia do Azure) para renderização de linha do tempo de sprites de uma imagem de miniatura gerada por MES (Media Encoder Standard) dos AMS (Serviços de Mídia do Azure). |
| Sobreposição de Diagnóstico                 | [Demonstração](https://openidconnectweb.azurewebsites.net/Diagnoverlay.html)                        | [GitHub](https://github.com/willzhan/diagnoverlay)                     | Este plug-in exibe: Todos os parâmetros principais, estatísticas de vídeo, todos os eventos no ciclo de vida da reprodução de vídeo e, se protegido, as informações de proteção de DRM, como a ID de chave e as URLs de aquisição de licença.                                                                                                                                                                      |
| Taxa de quadros e calculadora de código de tempo | Nenhuma demonstração disponível | [GitHub](https://github.com/mconverti/media-services-javascript-azure-media-player-framerate-timecode-calculator-plugin)                     | Esse plug-in calcula a taxa de quadros de vídeo com base nas caixas `tfhd`/`trun` de MP4 do primeiro fragmento de vídeo MPEG-DASH, analisa o valor de escala de tempo do manifesto do cliente MPEG-DASH e também fornece uma forma de gerar o código de tempo para um determinado período absoluto do Player (bem como fornece o tempo absoluto do player, desde que o código de tempo seja fornecido) |
| <strike>Velocidade de reprodução</strike>                      | [Demonstração](https://azure-samples.github.io/media-services-javascript-Azure-Media-Player-playback-rate-plugin/)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Esse plug-in permite que os visualizadores controlem a velocidade do vídeo. *Observe que essa funcionalidade está automaticamente disponível no AMP v2.0.0 ou posterior, mas desabilitada por padrão.* Para saber como habilitá-la, confira nossos exemplos [aqui](https://github.com/Azure-Samples/azure-media-player-samples) |
| Dica de Tempo ao Focalizar                      | [Demonstração](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Exibe uma dica de tempo sobre a barra de progresso ao focalizar com o mouse para busca precisa de tempo. *Observação: Esse plug-in já está integrado ao AMP*, mas se você estiver interessado em ver como ele é programado, sinta-se livre para dar uma olhada.                                                                                                                 |
| Sobreposição de Título                       | [Demonstração](https://azure-samples.github.io/media-services-javascript-azure-media-player-title-overlay-plugin)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-title-overlay-plugin)                     | Sobrepõe título de vídeo configurável na tela |
| Marcadores de Linha do Tempo                    | [Demonstração](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timelinemarkers/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-timeline-markers-plugin)                     | Esse plug-in usa uma matriz de tempos e sobrepõe pequenos marcadores na barra de progresso nesses tempos específicos. |
| Análise                           | | | |
| Application Insights                | [Postagem no blog](https://azure.microsoft.com/blog/player-analytics-azure-media-player-plugin/)                   | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-application-insights-plugin)                     | Plug-in que controla suas métricas do player e faz a portabilidade delas para o Power BI para uma representação gráfica intuitiva da experiência que seus visualizadores têm com o player. |
| Google Analytics                    | N/D                         | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-google-analytics-plugin)                     | Plug-in Google Analytics para o Player de Mídia do Azure |
| Diagnósticos                         | | | |
| Saída de Diagnóstico                  | [Demonstração](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/diagnosticslogger/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-diagnostic-logger-plugin)                     | Esse plug-in gera uma matriz de diagnóstico do seu player. Para vê-lo em ação, vá para o link demonstração e abra o console do JavaScript. |
| Facilidade de Acesso                      | | | |
| Ampliar                             | [Demonstração](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/zoom/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-zoom-plugin)                     | Esse plug-in exibe uma escala de zoom que pode ser arrastada na tela dos jogadores para que os visualizadores possam ampliar seu conteúdo |
| Legendas ao vivo                       | [Postagem no blog do Azure](https://azure.microsoft.com/blog/live-real-time-captions-with-azure-media-services-and-player/),[postagem da SubPly](http://www.subply.com/en/Products/AzureLiveCaptions.htm) | N/D | *Veja a postagem para obter mais informações.* Fluxo de trabalho de ponta a ponta criado para o plug-in de legendagem ao vivo criado para o Player de Mídia do Azure; clique na linha mais à esquerda para ir para o site da SubPly e saiba mais sobre a solução |
| Teclas de Atalho                            | <strike>[Demonstração](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)</strike>                        | <strike>[GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-hot-keys-plugin)</strike>                     | O plug-in de teclas de atalho permite que os visualizadores controlem vários aspectos do Player com combinações de plug-ins genéricas como F para tela inteira, M para mudo e teclas de direção para controle de barra de progresso. *Observação: Esse plug-in já foi integrado ao AMP, mas sinta-se livre para usá-lo como um recurso* |
| Redes sociais                              | | | |
| Compartilhar                               | [Demonstração](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/share/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-social-share-plugin)                     | Esse plug-in adiciona um botão de compartilhamento à barra de controle do Player para que seus visualizadores possam compartilhar o vídeo que estão assistindo com os próprios amigos por meio do Facebook, Twitter ou LinkedIn. |

## <a name="next-steps"></a>Próximas etapas ##

- [Início rápido do Player de Mídia do Azure](azure-media-player-quickstart.md)
