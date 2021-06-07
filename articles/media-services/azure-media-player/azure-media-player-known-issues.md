---
title: Problemas conhecidos do Player de Mídia do Azure
description: A versão atual tem os problemas conhecidos a seguir.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.openlocfilehash: 9d2289f1cec0d3679b0e9400f75c75cf9fece60b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448806"
---
# <a name="known-issues"></a>Problemas conhecidos #

A versão atual tem os seguintes problemas conhecidos:

## <a name="azure-media-player"></a>Player de Mídia do Azure ##

- Codificadores configurados incorretamente podem causar problemas com a reprodução
- Fluxos com carimbos de data/hora maiores que 2^53 podem ter problemas de reprodução.
  - Mitigação: use escalas de tempo de vídeo de 90 kHz e de áudio de 44,1 kHz
- Não há reprodução automática em dispositivos móveis sem interação do usuário. Ela é bloqueada pela plataforma.
- Procurar perto de descontinuidades pode causar falha na reprodução.
- O download de apresentações grandes pode fazer com que a interface do usuário seja travada.
- Não é possível reproduzir automaticamente a mesma origem novamente após o término da apresentação.
  - Para reproduzir uma origem depois de finalizada, é necessário definir a origem novamente.
- Manifestos vazios podem causar problemas com o player.
  - Esse problema pode ocorrer ao iniciar uma transmissão ao vivo e não são encontradas partes suficientes no manifesto.
- A posição de reprodução pode estar fora da barra de pesquisa da interface do usuário.
- A ordenação de eventos não é consistente em todas as tecnologias.
- A propriedade em buffer não é consistente entre as tecnologias.
- nativeControlsForTouch deve ser false (padrão) para evitar "O objeto não dá suporte à propriedade nem ao método 'setControls'"
- Os pôsteres agora precisam ser URLs absolutas
- Problemas de estética menores podem ocorrer na interface do usuário ao usar o modo de alto contraste do dispositivo
- As URLs que contêm "%" ou "+" na cadeia de caracteres totalmente decodificada podem ter problemas ao definir a origem

## <a name="ad-insertion"></a>Inserção de anúncio ##

- Os anúncios podem ter problemas inseridos (sob demanda ou ao vivo) quando um bloqueador de anúncios está instalado no navegador
- Os dispositivos móveis podem ter problemas para reproduzir anúncios.
- Atualmente, os anúncios da Midroll em MP4 não têm suporte pelo Player de Mídia do Azure.

## <a name="azurehtml5js"></a>AzureHtml5JS ##

- Na janela DVR de conteúdo ao vivo, quando o conteúdo for concluído, a linha do tempo continuará crescendo até que você vá para a área ou atinja o final da apresentação.
- As apresentações ao vivo no Firefox com o EQM habilitado têm alguns problemas

- Os ativos que são somente áudio não serão reproduzidos por meio da tecnologia AzureHtml5JS.
  - Se você quiser reproduzir ativos sem áudio, poderá fazê-lo inserindo áudio em branco usando a [ferramenta do Gerenciador de Serviços de Mídia do Azure](https://aka.ms/amse)
  - Veja instruções sobre como inserir áudio silencioso [aqui](../previous/media-services-advanced-encoding-with-mes.md#silent_audio)

## <a name="flash"></a>Flash ##

- O conteúdo AES não é reproduzido na versão 30.0.0.134 do Flash devido a um bug na lógica de cache da Adobe. A Adobe corrigiu o problema e o liberou na versão 30.0.0.154
- Em falhas de tecnologia e http (como tempos limite de rede 404), o player levará mais tempo para ser recuperado do que nas outras tecnologias.
- Clicar na área do vídeo com a tecnologia flashSS não vai reproduzir/pausar o player.
- Se o usuário tiver o Flash instalado, mas não conceder permissão para carregá-lo no site, poderá ocorrer a rotação infinita. Isso ocorre porque o player acha que o plug-in está instalado e disponível e que o plug-in está executando o conteúdo. O código JavaScript foi enviado, mas as configurações do navegador bloquearam a execução do plug-in até que o usuário aceite o prompt para permitir o plug-in. Isso pode ocorrer em todos os navegadores.  

## <a name="silverlight"></a>Silverlight ##

- Recursos ausentes
- Em falhas de tecnologia e http (como tempos limite de rede 404), o player levará mais tempo para ser recuperado do que nas outras tecnologias.
- A reprodução do Safari e do Firefox no Mac com o Silverlight exige a definição explícita de `"http://` ou `https://` para a origem.
- Se uma API estiver ausente para essa tecnologia, ela geralmente retornará nulo.
- Se o usuário tiver o Flash instalado, mas não conceder permissão para carregá-lo no site, poderá ocorrer a rotação infinita. Isso ocorre porque o player acha que o plug-in está instalado e disponível e que o plug-in está executando o conteúdo. O código JavaScript foi enviado, mas as configurações do navegador bloquearam a execução do plug-in até que o usuário aceite o prompt para permitir o plug-in. Isso pode ocorrer em todos os navegadores.  

## <a name="native-html5"></a>Native HTML5 ##

- A tecnologia HTML5 está enviando apenas o evento canplaythrough para a primeira origem definida.
- Essa tecnologia dá suporte apenas ao que o navegador implementou.  Alguns recursos podem estar ausentes nessa tecnologia.  
- Se uma API estiver ausente para essa tecnologia, ela geralmente retornará nulo.
- Há problemas com legendas e subtítulos nesta tecnologia. Eles podem ou não estar disponíveis ou ser exibidos nessa tecnologia.
- Ter largura de banda limitada no cenário da tecnologia HLS/HTML5 resulta na reprodução de áudio sem vídeo.

### <a name="microsoft"></a>Microsoft ###

- No momento, a reprodução do IE8 não funciona devido à incompatibilidade com o ECMAScript 5
- No IE e em algumas versões do Edge, a tela inteira não pode ser inserida com tabulação para o botão e não é possível selecioná-la nem usar a tecla de atalho F/f.

## <a name="google"></a>Google ##

- Vários perfis de codificação no mesmo manifesto têm alguns problemas de reprodução no Chrome e não são recomendados.
- O Chrome não pode reproduzir HE-AAC com AzureHtml5JS. Acompanhe os detalhes no [rastreador de bugs](https://bugs.chromium.org/p/chromium/issues/detail?id=534301).
- No Chrome v58 em diante, o conteúdo do Widevine precisa ser carregado/reproduzido por meio do protocolo https://, caso contrário, a reprodução falhará.

## <a name="mozilla"></a>Mozilla ##

- A opção de fluxo de áudio exige que os fluxos de áudio tenham os mesmos dados privados do codec ao usar AzureHtml5JS. A plataforma Firefox requer isso.

## <a name="apple"></a>Apple ##

- O Safari no Mac geralmente habilita o modo de economia de energia por padrão com a configuração "Parar plug-ins para economizar energia", que bloqueia plug-ins como Flash e Silverlight quando eles acreditam que ele não está em favor ao usuário. Isso não bloqueia os plug-ins existentes, somente as funcionalidades. Considerando o techOrder padrão, isso pode causar problemas ao tentar reproduzir
  - Mitigação 1: se o player de vídeo for 'front and center' (dentro de um limite de 3000 x 3000 pixels começando no canto superior esquerdo do documento), ele ainda deverá ser reproduzido.
  - Mitigação 2: altere o techOrder para o Safari para ser ["azureHtml5JS", "html5"]. Essa mitigação tem a implicação de não obter todos os recursos disponíveis na tecnologia FlashSS.
- O conteúdo do PlayReady via Silverlight pode ter problemas de reprodução no Safari.
- O AES e o conteúdo de token restrito não são reproduzidos usando iOS e dispositivos Android mais antigos.
  - Para alcançar esse cenário, um proxy precisa ser adicionado ao seu serviço.
- A aparência padrão do telefone iOS é mostrada.
- A reprodução do iPhone sempre ocorre na tela inteira do player nativo.
  - Recursos, incluindo legendas, podem não persistir nesta reprodução não embutida.
- Quando a apresentação ao vivo terminar, os dispositivos iOS não encerrarão a apresentação corretamente.
- O iOS não permite funcionalidades DVR.
- A opção de fluxo de áudio do iOS só pode ser feita por meio da interface do usuário do player nativo do iOS e requer que fluxos de áudio tenham os mesmos dados privados do codec
- Versões mais antigas do Safari podem potencialmente ter problemas ao reproduzir transmissões ao vivo.

## <a name="older-android"></a>Android mais antigo ##

- O AES e o conteúdo de token restrito não são reproduzidos usando iOS e dispositivos Android mais antigos.
  - Para alcançar esse cenário, um proxy precisa ser adicionado ao seu serviço.

## <a name="next-steps"></a>Próximas etapas ##

- [Início rápido do Player de Mídia do Azure](azure-media-player-quickstart.md)
