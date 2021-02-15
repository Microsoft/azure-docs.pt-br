---
title: Player de Mídia do Azure problemas conhecidos
description: A versão atual tem os seguintes problemas conhecidos.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 05/11/2020
ms.openlocfilehash: a31c089971b7e70e70c5906480deb2b17c197b9f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87043611"
---
# <a name="known-issues"></a>Problemas conhecidos #

A versão atual tem os seguintes problemas conhecidos:

## <a name="azure-media-player"></a>Player de Mídia do Azure ##

- Os codificadores configurados incorretamente podem causar problemas com a reprodução
- Fluxos com carimbos de data/hora maiores que 2 ^ 53 podem ter problemas de reprodução.
  - Mitigação: usar vídeo de 90-kHz e escalas de bits de 44,1 kHz
- Não há reprodução automática em dispositivos móveis sem interação do usuário. Ele é bloqueado pela plataforma.
- Procurar perto de descontinuidades pode causar falha na reprodução.
- O download de grandes apresentações pode fazer com que a interface do usuário seja travada.
- Não é possível reproduzir automaticamente a mesma origem novamente após o término da apresentação.
  - Para reproduzir uma fonte depois de finalizada, é necessário definir a origem novamente.
- Os manifestos vazios podem causar problemas com o Player.
  - Esse problema pode ocorrer ao iniciar uma transmissão ao vivo e não são encontradas partes suficientes no manifesto.
- Posição de reprodução talvez fora da SeekBar da interface do usuário.
- A ordenação de eventos não é consistente em todas as tecnologias.
- A propriedade armazenada em buffer não é consistente entre os técnicos.
- nativeControlsForTouch deve ser false (padrão) para evitar "o objeto não dá suporte à propriedade ou ao método ' setcontrols '"
- Os cartazes agora devem ser URLs absolutas
- Problemas de estética menores podem ocorrer na interface do usuário ao usar o modo de alto contraste do dispositivo
- As URLs que contêm "%" ou "+" na cadeia de caracteres totalmente decodificada podem ter problemas ao definir a origem

## <a name="ad-insertion"></a>Inserção de anúncio ##

- Os anúncios podem ter problemas inseridos (sob demanda ou ao vivo) quando um bloqueador de AD é instalado no navegador
- Os dispositivos móveis podem ter problemas para reproduzir anúncios.
- Atualmente, os anúncios Midroll MP4 não têm suporte pelo Player de Mídia do Azure.

## <a name="azurehtml5js"></a>AzureHtml5JS ##

- Na janela DVR de conteúdo ao vivo, quando o conteúdo for concluído, a linha do tempo continuará crescendo até que você vá para a área ou atinja o final da apresentação.
- As apresentações ao vivo no Firefox com o MSE habilitado têm alguns problemas

- Os ativos que são somente áudio não serão reproduzidos por meio do AzureHtml5JS Tech.
  - Se você quiser reproduzir ativos sem áudio, poderá fazê-lo inserindo áudio em branco usando a [ferramenta do Gerenciador de serviços de mídia do Azure](https://aka.ms/amse)
  - Instruções sobre como inserir áudio silencioso podem ser encontradas [aqui](../previous/media-services-advanced-encoding-with-mes.md#silent_audio)

## <a name="flash"></a>Flash ##

- O conteúdo AES não é reproduzido na versão 30.0.0.134 do Flash devido a um bug na lógica de cache da Adobe. A Adobe corrigiu o problema e o liberou no 30.0.0.154
- Falhas de Tech e http (como tempos limite de rede 404), o Player levará mais tempo para ser recuperado do que outros técnicos.
- Clique em área de vídeo com a tecnologia flashss não vai reproduzir/pausar o Player.
- Se o usuário tiver o Flash instalado, mas não conceder permissão para carregá-lo no site, poderá ocorrer a rotação infinita. Isso ocorre porque o Player pensa que o plug-in está instalado e disponível e pensa que o plug-in está executando o conteúdo. O código JavaScript foi enviado, mas as configurações do navegador bloquearam a execução do plug-in até que o usuário aceite o prompt para permitir o plug-in. Isso pode ocorrer em todos os navegadores.  

## <a name="silverlight"></a>Silverlight ##

- Recursos ausentes
- Falhas de Tech e http (como tempos limite de rede 404), o Player levará mais tempo para ser recuperado do que outros técnicos.
- O Safari e o Firefox na reprodução de Mac com o Silverlight exigem a definição explícita `"http://` ou `https://` para a origem.
- Se uma API estiver ausente para essa tecnologia, ela geralmente retornará NULL.
- Se o usuário tiver o Flash instalado, mas não conceder permissão para carregá-lo no site, poderá ocorrer a rotação infinita. Isso ocorre porque o Player pensa que o plug-in está instalado e disponível e pensa que o plug-in está executando o conteúdo. O código JavaScript foi enviado, mas as configurações do navegador bloquearam a execução do plug-in até que o usuário aceite o prompt para permitir o plug-in. Isso pode ocorrer em todos os navegadores.  

## <a name="native-html5"></a>HTML5 nativo ##

- O HTML5 Tech está enviando apenas o evento canplaythrough para a origem do primeiro conjunto.
- Essa tecnologia dá suporte apenas ao que o navegador implementou.  Alguns recursos podem estar ausentes nessa tecnologia.  
- Se uma API estiver ausente para essa tecnologia, ela geralmente retornará NULL.
- Há problemas com legendas e legendas nesta tecnologia. Eles podem ou não estar disponíveis ou podem ser exibidos nessa tecnologia.
- Ter largura de banda limitada no cenário técnico HLS/HTML5 resulta na reprodução de áudio sem vídeo.

### <a name="microsoft"></a>Microsoft ###

- A reprodução do IE8 não funciona atualmente devido à incompatibilidade com o ECMAScript 5
- No IE e em algumas versões do Edge, a tela inteira não pode ser inserida com Tab para o botão e selecioná-la ou usando a tecla de atalho F/f.

## <a name="google"></a>Google ##

- Vários perfis de codificação no mesmo manifesto têm alguns problemas de reprodução no Chrome e não são recomendados.
- O Chrome não pode reproduzir-AAC com AzureHtml5JS. Siga os detalhes no [rastreador de bugs](https://bugs.chromium.org/p/chromium/issues/detail?id=534301).
- A partir do Chrome V58, o conteúdo do Widevine deve ser carregado/reproduzido por meio do protocolo https://, caso contrário, a reprodução falhará.

## <a name="mozilla"></a>Mozilla ##

- A opção de fluxo de áudio exige que os fluxos de áudio tenham os mesmos dados privados do codec ao usar AzureHtml5JS. A plataforma Firefox requer isso.

## <a name="apple"></a>Apple ##

- O Safari no Mac geralmente habilita o modo de economia de energia por padrão com a configuração "parar plug-ins para economizar energia", que bloqueia plug-ins como Flash e Silverlight quando eles acreditam que ele não está em favor ao usuário. Esse bloco não bloqueia as funcionalidades existentes do plug-in. Considerando o techOrder padrão, isso pode causar problemas ao tentar reproduzir
  - Mitigação 1: se o player de vídeo for ' front and Center ' (dentro de um limite de 3000 x 3000 pixels começando no canto superior esquerdo do documento), ele ainda deverá ser reproduzido.
  - Mitigação 2: altere o techOrder para o Safari para ser ["azureHtml5JS", "HTML5"]. Essa mitigação tem implicação de não obter todos os recursos disponíveis na tecnologia Flashss.
- O conteúdo do PlayReady via Silverlight pode ter problemas de reprodução no Safari.
- O AES e o conteúdo de token restrito não são reproduzidos usando iOS e dispositivos Android mais antigos.
  - Para alcançar esse cenário, um proxy deve ser adicionado ao seu serviço.
- A aparência padrão do telefone iOS é mostrada por meio do.
- a reprodução do iPhone sempre ocorre na tela inteira do jogador nativo.
  - Recursos, incluindo legendas, podem não persistir nesta reprodução não embutida.
- Quando a apresentação ao vivo terminar, os dispositivos iOS não encerrarão a apresentação corretamente.
- o iOS não permite recursos DVR.
- a opção de fluxo de áudio do iOS só pode ser feita por meio da interface do usuário do Player nativo do iOS e requer fluxos de áudio para ter os mesmos dados privados do codec
- Versões mais antigas do Safari podem potencialmente ter problemas ao reproduzir fluxos ao vivo.

## <a name="older-android"></a>Android mais antigo ##

- O AES e o conteúdo de token restrito não são reproduzidos usando iOS e dispositivos Android mais antigos.
  - Para alcançar esse cenário, um proxy deve ser adicionado ao seu serviço.

## <a name="next-steps"></a>Próximas etapas ##

- [Início rápido do Player de Mídia do Azure](azure-media-player-quickstart.md)
