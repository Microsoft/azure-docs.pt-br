---
title: Acessibilidade do Player de Mídia do Azure
description: Saiba mais sobre as configurações de acessibilidade do Player de Mídia do Azure.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.openlocfilehash: 26e25f5391524020ef7968ba627997ea16a80921
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449895"
---
# <a name="accessibility"></a>Acessibilidade #

O Player de Mídia do Azure trabalha com funcionalidades do leitor de tela, como o Narrador do Windows e o VoiceOver do Apple OSX/iOS. Marcas alternativas estão disponíveis para os botões de interface do usuário, e o leitor de tela pode ler essas marcas alternativas quando o usuário navega até elas. Outras configurações podem ser definidas no nível do sistema operacional.

## <a name="captions-and-subtitles"></a>Legendas e subtítulos ##

Atualmente, o Player de Mídia do Azure dá suporte a legendas apenas em eventos por demanda com o formato WebVTT e eventos ao vivo que usam o CEA-708. Atualmente, não há suporte ao formato TTML. As legendas e subtítulos permitem que o player atenda às necessidades e capacite um público mais amplo, incluindo pessoas com dificuldades de audição ou aqueles que desejam ler o texto simultaneamente em um idioma diferente. As legendas também aumentam a participação nos vídeos, melhoram a compreensão e permitem a exibição do vídeo em ambientes que não admitam sons altos, como ambientes de trabalho.  

## <a name="high-contrast-mode"></a>Modo de alto contraste ##

A interface do usuário padrão no Player de Mídia do Azure é compatível com a maioria dos modos de exibição de alto contraste do dispositivo/do navegador. As configurações podem ser definidas no nível do sistema operacional.

## <a name="mobility-options"></a>Opções de mobilidade ##

### <a name="tabbing-focus"></a>Foco de tabulação ###

O foco de tabulação, fornecido por padrões gerais de HTML, está disponível no Player de Mídia do Azure. Para habilitar o foco de guia, adicione `tabindex=0` (ou outro valor se você entender como a ordem de tabulação é afetada em HTML) ao `<video>` HTML da seguinte maneira: `<video ... tabindex=0>...</video>`. Em algumas plataformas, o foco dos controles só poderá estar presente se os controles estiverem visíveis e se a plataforma der suporte a essas funcionalidades.

Depois que o foco de tabulação estiver habilitado, o usuário final poderá navegar no player de vídeo e controlá-lo com eficiência sem depender do mouse. Cada menu de contexto ou elemento controlável pode ser acessado com o botão de tabulação e selecionado com ENTER ou com a barra de espaços. A seleção de ENTER ou da barra de espaços em um menu de contexto o expandirá, de modo que o usuário final possa continuar com a tabulação para seleção de um item de menu. Depois de ter o contexto do item que deseja selecionar, clique em ENTER ou na barra de espaços novamente para concluir a seleção.

### <a name="hotkeys"></a>Teclas de atalho ###

O Player de Mídia do Azure dá suporte ao controle por meio da tecla de atalho do teclado. Em um navegador da Web, a única maneira de controlar o elemento de vídeo subjacente é ter o foco no player. Quando houver um foco no player, a tecla de atalho poderá controlar a funcionalidade do player.  A tabela abaixo descreve as várias teclas de atalho e o respectivo comportamento associado:

| Tecla de atalho              | Comportamento                                                                |
|----------------------|-------------------------------------------------------------------------|
| F/f                  | O player entrará no modo de tela inteira ou sairá dele                                  |
| M/m                  | O volume do player será desativado/ativado                                          |
| Seta para cima e para baixo    | O volume do player será aumentado/diminuído                                    |
| Seta para a esquerda e para a direita | O progresso do vídeo aumentará/diminuirá                                  |
| 0, 1, 2, 3, 4, 5, 6, 7, 8, 9  | O progresso do vídeo será alterado para 0%\- 90%, dependendo da tecla pressionada |
| Clique em Ação         | O vídeo será reproduzido/colocado em pausa                                                   |

## <a name="next-steps"></a>Próximas etapas

<!---Some context for the following links goes here--->
- [Início rápido do Player de Mídia do Azure](azure-media-player-quickstart.md)