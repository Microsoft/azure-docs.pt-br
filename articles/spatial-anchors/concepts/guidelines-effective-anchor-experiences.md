---
title: Diretrizes para experiências de âncora eficiente que usam âncoras espacial do Azure | Microsoft Docs
description: Diretrizes e considerações para criar e localizar as âncoras com eficiência usando âncoras espacial do Azure.
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9e77dcd96ffa0fbd57aa0ed1b4f857279ca768a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565986"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Criar uma experiência de âncora eficaz usando âncoras espacial do Azure

Este artigo fornece diretrizes e considerações para ajudá-lo efetivamente criar e localizam âncoras usando âncoras espacial.

## <a name="good-anchors"></a>Boa âncoras

Âncoras espaciais ajuda a criar âncoras BOM. É importante investir tempo para treinar ou orientando os usuários em sua experiência de usuário (UX) criar âncoras BOM. Ao investir na criação de BOM âncoras com antecedência, você pode ajudar a usuários finais encontre as âncoras de forma confiável:

- Em diferentes dispositivos.
- Em vários momentos.
- Em condições de iluminação diferentes.
- Das perspectivas desejadas dentro do espaço.

## <a name="static-and-dynamic-locations"></a>Locais estáticos e dinâmicos

Parte da criação a experiência de âncora é escolha dos locais. Os locais será estática e ser definida por um administrador do espaço? Ou, eles serão definidos pelo usuário e dinâmico?

Um gerente de loja de varejo pode ser uma experiência na loja estática para convencer usuários a visitar. Mas o desenvolvedor de um jogo de tabuleiro de realidade misturada provavelmente iria querer permitir que os usuários a escolher onde executar.

Para locais estáticos, você pode ensinar os administradores de gastar tempo coletando o espaço com boa âncoras.

Para locais dinâmicos, você deve pensar sobre como você ensina ou orientar os usuários em sua experiência do usuário para criar âncoras BOM.

## <a name="stable-visual-features"></a>Recursos do visual estáveis

Sistemas de controle Visual é usado em realidade misturada e dispositivos de realidade aumentada dependem de recursos do visual do ambiente. Para obter a experiência mais confiável:  

- *Fazer* criar âncoras em locais que têm recursos visuais estáveis (ou seja, recursos que não são alterados com frequência).

- *Não* criar âncoras em grandes superfícies em branco que não têm características de nenhuma distinção.

- *Não* criar âncoras em materiais altamente refletivas.

- *Não* criar âncoras em superfícies em que o padrão se repete, como carpete ou o papel de parede.

![Exemplos de um ambiente de BOM para âncoras e um ambiente incorreto para âncoras](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Várias perspectivas de exibição

Ao criar uma âncora, pense sobre as pessoas que posteriormente tentar localizar a âncora.

Considere, por exemplo, uma âncora no meio de uma sala que tem duas portas. É provável que você deseja permitir que os usuários entrar na sala de qualquer porta. Ao criar a âncora, será preciso verificar sua posição de ambas as entradas de acesso. Alterar o perspectivas para capturar dados de ambiente em torno da âncora para que os usuários possam localizar a âncora de qualquer porta.

Em geral, ao criar uma âncora, examiná-lo a partir das perspectivas das pessoas que tentarem localizá-lo. Portanto, se você estiver colocando conteúdo virtual sobre uma escultura ao ar livre, faz sentido andando com escultura, durante a verificação, conforme você cria a âncora. Se for sua âncora no canto de uma sala, há apenas uma direção de abordá-lo partir. Ao criar essa âncora, você pode examiná-lo apenas a partir dessa perspectiva.

## <a name="multiple-anchors"></a>Várias âncoras

Iluminação pode fazer uma diferença nos recursos do visual detecta um aplicativo. Âncoras criadas na luz natural forte podem ser difícil localizar no luz artificial e vice-versa.  

Se você tiver esse problema, ele pode ajudar a criar as duas âncoras. No mesmo lugar, crie uma âncora no horário de verão e outro no luz artificial. Seu aplicativo, em seguida, pode consultar as duas âncoras. Quando a âncora for localizada, o aplicativo terá uma pose para a âncora. 

Da mesma forma, em ambientes onde os recursos visuais alterar como a maioria dos objetos movidos, várias âncoras podem ajudar. Quando uma âncora se torna muito difícil encontrar devido a alterações significativas no ambiente, você pode substituir a âncora com um novo. Você pode fazer isso, por exemplo, em uma loja de varejo em que o layout é atualizado a cada poucos meses.

## <a name="targets-and-rooms"></a>Destinos e salas

Em muitos casos, uma âncora é um ponto de entrada para a experiência do seu aplicativo. Você vai querer dar por essa etapa rapidamente e com confiança para que os usuários podem inserir sua experiência. Como os usuários encontrarão seu âncoras gastar tempo é uma etapa de design importantes. É útil pensar sobre como localizar as âncoras em termos de dois cenários amplos: *destinos* e *salas*.

### <a name="targets"></a>Destinos

No cenário de destino, o local de uma âncora é bem conhecido. Por exemplo, em um aplicativo de pintura de realidade misturada fictícia, um usuário coloca uma tela virtual na parede. Ela instrui os outros usuários na sala para apontar os seus dispositivos no mesmo local na parede para localizar a âncora e começar a experiência.  

Outro exemplo de um cenário de destino pode ser um sinal em uma cafeteria que se lê "Verificação de negócios". O café e colocou uma âncora aqui. Conforme os usuários verificar o sinal, eles localize a âncora e insira a experiência de realidade aumentada para localizar as negociações no café.

O cenário de destino, fotos podem ajudar. Se você mostrar aos usuários uma foto do destino pretendido em seu dispositivo, eles podem rapidamente identificar que deve ser examinado no mundo real. Por exemplo, você pode ajudar os usuários chegam dentro da área geral de um destino pretendido usando GPS. Quando o usuário chega, seu aplicativo mostra uma foto do destino. O usuário procura ao redor de espaço, localiza o destino e examina a âncora.

![Ilustração de uma âncora, mostrando uma foto do destino em um dispositivo de usuário móvel](./media/start-here-edit.png)

### <a name="rooms"></a>salas

No cenário de espaço, os usuários inserem um espaço simplesmente saber que há uma âncora aqui em algum lugar. Os usuários verificar o espaço com o seu dispositivo e localizar rapidamente a âncora.

Essa experiência normalmente requer que você criar âncoras bem estruturadas, conforme discutido em várias perspectivas de exibição. Se você tiver examinado na sala de muitas perspectivas quando criou a âncora, os usuários podem analisar quase em qualquer lugar ao tentarem localizá-lo.

![Ilustração de como um usuário pode digitalizar uma sala para localizar uma âncora](./media/scan-room.png)

Essencialmente, você gasta mais tempo que o espaço de verificação quando você cria a âncora de modo que posteriormente os usuários podem examinar e localize a âncora rapidamente. Conforme você cria sua experiência, você precisará considerar essa compensação importante.

O exemplo de aplicativo de realidade misturada pintura que discutimos anteriormente não funciona bem como um cenário de sala. Aqui, o usuário que coloca a âncora queira que outras pessoas a experiência de ingresso rapidamente. Os usuários não querem esperar para iniciar a experiência até que o espaço também é verificado. Como todos os usuários sabem exatamente onde apontar o seu dispositivo para localizar as âncoras, este exemplo funciona melhor como um cenário de destino.

## <a name="anchor-location"></a>Local de ancoragem

Sistemas de controle Visual contam com os recursos visuais em um ambiente. Os recursos mais visuais que inclui uma verificação, maior a probabilidade de encontrar uma âncora.

Siga as diretrizes gerais nesta seção para criar uma experiência do usuário que incentiva uma verificação útil do ambiente.

Primeiro, se o usuário não localizar uma âncora em poucos segundos, o aplicativo deve incentivar os usuários para mover o dispositivo para capturar mais perspectivas. O aplicativo também pode incentivar os usuários movam a mesmos em todo o ambiente para verificar a âncora de perspectivas mais. As perspectivas de recurso mais que o dispositivo detecta, melhor.

Para cenários de destino, peça ao usuário para mover em torno do destino para exibi-lo de diferentes perspectivas. Em outras palavras, peça ao usuário capturar o destino de novas perspectivas, até que a âncora está localizada.

Para cenários de sala, peça ao usuário para digitalizar lentamente na sala. Por exemplo, peça ao usuário ativar para capturar 180 graus ou até mesmo de 360 graus da sala. Ou peça ao usuário para exibir o espaço de uma perspectiva de novo. 

O método mais significativo é verificar na sala. Uma verificação na sala captura mais recursos do visual do ambiente de que uma verificação de uma parede próxima, por exemplo. Uma verificação de uma parede próxima não captura quantos recursos visuais úteis do ambiente.

Não é útil para repetidamente mover o dispositivo de lado a lado ao procurar uma âncora. Isso simplesmente captura os mesmos pontos da perspectiva do mesmo.

## <a name="experience-tests"></a>Testes de experiência

Neste artigo, discutimos diretrizes gerais. Com âncoras espacial, você está escrevendo aplicativos que interagem com o mundo real. Por causa disso, você deve dedicar tempo para cenários de âncora do seu aplicativo de teste em ambientes reais. Isso é especialmente verdadeiro para ambientes que representam de onde você espera que os usuários a usar o aplicativo.
