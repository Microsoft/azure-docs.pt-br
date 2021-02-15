---
title: Diretrizes para experiências de ancoragem efetivas
description: Diretrizes e considerações para criar e localizar âncoras com eficiência usando âncoras espaciais do Azure.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: a6bc500516de7e554c38a335ea57519a39c8f602
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95487496"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Criar uma experiência de ancoragem eficaz usando âncoras espaciais do Azure

Este artigo fornece diretrizes e considerações para ajudá-lo a criar e localizar âncoras com eficiência usando âncoras espaciais do Azure.

## <a name="anchor-improvement-over-time"></a>Melhoria da âncora ao longo do tempo

Com as âncoras espaciais do Azure, cada vez que você localiza âncoras, tentamos melhorar a qualidade das futuras operações de localização. Fazemos isso usando os dados do ambiente coletados para aumentar as informações visuais nas âncoras que estamos procurando. Esse processo é executado nos bastidores e é uma otimização offline executada pelo serviço âncoras espaciais do Azure para otimizar o seu ambiente. Os dados adicionais coletados durante cada operação criam uma compreensão mais forte do ambiente. Isso melhora a qualidade e permite que você localize melhor âncoras por meio de alteração de ambiente, passagem de tempo e para usuários que procuram âncoras de diferentes ângulos e perspectivas.

## <a name="good-anchors"></a>Boas âncoras

Embora as âncoras espaciais do Azure tentem melhorar a qualidade das âncoras ao longo do tempo, também é importante investir tempo na conscientização ou na orientação dos usuários em sua experiência do usuário (UX) para criar boas âncoras. Ao investir na criação de boas âncoras antecipadamente, você ajuda os usuários finais a localizar âncoras de maneira confiável:

- Em diferentes dispositivos.
- Em vários momentos.
- Em condições de iluminação diferentes.
- Das perspectivas desejadas dentro do espaço.

## <a name="static-and-dynamic-locations"></a>Locais estáticos e dinâmicos

Parte da criação da experiência de ancoragem é escolher os locais. Os locais serão estáticos e definidos por um administrador do espaço? Ou eles serão dinâmicos e definidos pelo usuário?

Um gerente de loja de varejo pode querer uma experiência estática na loja para convencer os usuários a visitarem. Mas o desenvolvedor de um jogo de tabuleiro de realidade misturada provavelmente desejaria permitir que os usuários escolham onde jogar.

Para locais estáticos, você pode ensinar os administradores a gastar tempo ao organizar o espaço com boas âncoras.

Para locais dinâmicos, você deve pensar em como ensinar ou orientar os usuários em sua experiência de usuário para criar boas âncoras.

## <a name="stable-visual-features"></a>Recursos visuais estáveis

Os sistemas de controle visual usados na realidade mista e nos dispositivos de realidade aumentada contam com os recursos visuais do ambiente. Para obter a experiência mais confiável:

- *Crie* âncoras em locais que tenham recursos visuais estáveis (ou seja, recursos que não são alterados com frequência).

- *Não* crie âncoras em grandes superfícies em branco que não têm características de distinção.

- *Não* crie âncoras em materiais altamente refletidos.

- *Não* crie âncoras em superfícies nas quais o padrão se repete, como carpete ou papel de parede.

![Exemplos de um bom ambiente para âncoras e um ambiente ruim para âncoras](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Várias perspectivas de exibição

Ao criar uma âncora, pense nas pessoas que tentarão localizar mais tarde a âncora.

Considere, por exemplo, uma âncora no meio de uma sala que tem duas portas. Você provavelmente desejará permitir que os usuários insiram a sala de qualquer porta. Ao criar a âncora, você precisará verificar sua posição de ambos os doorways. Altere as perspectivas para capturar dados de ambiente em toda a âncora para que os usuários possam localizar a âncora de qualquer porta.

Em geral, ao criar uma âncora, digitalize-a das perspectivas das pessoas que tentarão localizá-la. Portanto, se você estiver colocando conteúdo virtual em um Sculpture externo, faz sentido percorrer o sculpture, ao mesmo tempo em que você cria a âncora. Se sua âncora estiver no canto de uma sala, haverá apenas uma direção para abordá-la. Ao criar essa âncora, você pode examiná-la apenas dessa perspectiva.

## <a name="multiple-anchors"></a>Várias âncoras

A iluminação pode fazer uma diferença nos recursos visuais que um aplicativo detecta. Âncoras criadas em luz natural forte podem ser difíceis de localizar em luz artificial e vice-versa.

Se você tiver esse problema, ele poderá ajudar a criar duas âncoras. No mesmo ponto, crie uma âncora no horário de verão e outra em luz artificial. Seu aplicativo pode então consultar ambas as âncoras. Quando a âncora estiver localizada, o aplicativo terá uma pose para a âncora.

Da mesma forma, em ambientes em que os recursos visuais mudam porque a maioria dos objetos se movem, várias âncoras podem ajudar. Quando uma âncora fica muito difícil de ser encontrada devido a alterações significativas no ambiente, você pode substituir a âncora por uma nova. Você pode fazer isso, por exemplo, em uma loja de varejo em que o layout é atualizado em intervalos de meses.

## <a name="targets-and-rooms"></a>Destinos e salas

Em muitos casos, uma âncora é um ponto de entrada para a experiência do seu aplicativo. Você desejará passar por essa etapa de forma rápida e confiável para que os usuários possam entrar em sua experiência. Gastar tempo em como os usuários encontrará suas âncoras é uma etapa de design importante. É útil pensar em encontrar âncoras em termos de dois cenários amplos: *destinos* e *salas*.

### <a name="targets"></a>Destinos

No cenário de destino, o local de uma âncora é bem conhecido. Por exemplo, em um aplicativo de pintura de realidade misturada fictícia, um usuário coloca uma tela virtual na parede. Ela instrui os outros usuários na sala a apontarem seus dispositivos no mesmo local do mural para localizar a âncora e começar a experiência.

Outro exemplo de um cenário de destino pode ser um sinal em uma cafeteria que lê, "procurar negociações". A cafeteria colocou uma âncora aqui. À medida que os usuários examinam o sinal, eles localizam a âncora e inserem a experiência de realidade aumentada para encontrar as ofertas em café.

No cenário de destino, as fotos podem ajudar. Se você mostrar aos usuários uma foto do destino pretendido em seu dispositivo, eles poderão identificar rapidamente o que examinar no mundo real. Por exemplo, você pode ajudar os usuários a chegarem na área geral de um destino pretendido usando GPS. Quando o usuário chega, seu aplicativo mostra uma foto do destino. O usuário procura o espaço, localiza o destino e verifica a âncora.

![Ilustração de uma âncora, mostrando uma foto do destino no dispositivo móvel de um usuário](./media/start-here-edit.png)

### <a name="rooms"></a>Salas

No cenário de sala, os usuários inserem um espaço simplesmente sabendo que há uma âncora aqui em algum lugar. Os usuários verificam o espaço com seu dispositivo e localizam rapidamente a âncora.

Essa experiência normalmente exige que você crie âncoras bem organizadas, conforme discutido em várias perspectivas de exibição. Se você examinou a sala de muitas perspectivas quando criou a âncora, os usuários podem verificar quase todos os lugares quando tentarem localizá-la.

![Ilustração de como um usuário pode digitalizar um espaço para encontrar uma âncora](./media/scan-room.png)

Essencialmente, você passa mais tempo verificando o espaço ao criar a âncora para que os usuários posteriores possam verificar e localizar a âncora rapidamente. Ao criar sua experiência, você precisará considerar essa compensação importante.

O exemplo do aplicativo de pintura de realidade misturada que discutimos anteriormente não funciona bem como um cenário de sala. Aqui, o usuário que coloca a âncora quer que outras pessoas ingressem na experiência rapidamente. Os usuários não querem esperar para iniciar a experiência até que a sala seja bem verificada. Como todos os usuários sabem exatamente onde apontar seu dispositivo para localizar as âncoras, este exemplo funciona melhor como um cenário de destino.

## <a name="anchor-location"></a>Local de âncora

Os sistemas de controle visual dependem dos recursos visuais em um ambiente. Quanto mais recursos visuais forem incluídos em uma verificação, maior será a probabilidade de encontrar uma âncora.

Siga as diretrizes gerais nesta seção para criar uma experiência do sistema que incentiva uma verificação útil do ambiente.

Primeiro, se o usuário não localizar uma âncora em alguns segundos, o aplicativo deverá encorajar os usuários a moverem o dispositivo para capturar mais perspectivas. O aplicativo também pode incentivar os usuários a se moverem em todo o ambiente para verificar a âncora de mais perspectivas. Quanto mais perspectivas de recursos forem verificadas pelo dispositivo, melhor, como ele aumentará a probabilidade de uma âncora estar localizada e também coletará mais dados de ambiente que serão usados para melhorar a qualidade da âncora.

Para cenários de destino, peça ao usuário para se mover pelo destino para exibi-lo de diferentes perspectivas. Em outras palavras, peça ao usuário para capturar o destino de novas perspectivas até que a âncora seja localizada.

Para cenários de sala, peça ao usuário para verificar a sala lentamente. Por exemplo, peça ao usuário para ativar a captura de 180 graus ou até mesmo 360 graus da sala. Ou peça ao usuário para exibir a sala de uma nova perspectiva.

O método mais significativo é examinar toda a sala. Uma verificação na sala captura mais recursos visuais do ambiente do que uma verificação de uma parede próxima, por exemplo. Uma verificação de uma parede próxima não capturará tantos recursos visuais úteis do ambiente.

Não é útil mover repetidamente o dispositivo do lado a lado ao procurar uma âncora. Isso simplesmente captura os mesmos pontos da mesma perspectiva.

## <a name="experience-tests"></a>Testes de experiência

Neste artigo, discutimos as diretrizes gerais. Com âncoras espaciais, você está escrevendo aplicativos que interagem com o mundo real. Por isso, você deve dedicar tempo para testar os cenários de âncora de seu aplicativo em ambientes reais. Isso é especialmente verdadeiro para ambientes que representam onde você espera que os usuários usem o aplicativo.
