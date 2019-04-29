---
title: Relações de âncora e a localização de maneira em âncoras espacial do Azure | Microsoft Docs
description: Saiba mais sobre o modelo conceitual por trás de relações de âncora. Saiba mais para conectar-se as âncoras de dentro de um espaço e para usar a API mais próximos para atender a um cenário de localização de forma.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 619cd051eccce3434469ae909f69496a254d0d9a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565116"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Relações de âncora e a localização de maneira em âncoras espacial do Azure

Usando relações de âncora, você pode criar âncoras conectadas em um espaço e, em seguida, fazer perguntas como essas:

* Há âncoras nas proximidades?
* A distância na qual eles são?

## <a name="examples"></a>Exemplos

Você pode usar âncoras conectadas em casos como esse:

* Precisa de um trabalho concluir uma tarefa que envolve a visitar vários locais em um factory industrial. O factory tem âncoras espaciais em cada local. Um aplicativo móvel ou HoloLens ajuda a orientar o trabalho de um local para o próximo. O aplicativo primeiro solicita as âncoras espaciais mais próximas e orienta o trabalho para o próximo local. Visualmente, o aplicativo mostra a direção geral e a distância para o próximo local.

* Um museu cria âncoras espaciais exibe público. Juntas, essas âncoras formam um exibe pública do essential do Museu tour de uma hora. Em uma exibição pública, os visitantes podem abrir o aplicativo de realidade mista do Museu em seu dispositivo móvel. Em seguida, eles apontam sua câmera de telefone em todo o espaço para ver a direção geral e a distância para as outras telas públicas sobre o tour. À medida que um usuário orienta em direção a uma exibição pública, o aplicativo atualiza a direção geral e a distância para ajudar a orientar o usuário.

## <a name="set-up-way-finding"></a>Configurar a localização de forma

Uso de um aplicativo que usa a direção da linha de visão e a distância entre as âncoras de fornecer orientações *localização de maneira*. Localização de maneira é diferente da navegação de folheio de curva. No painel de navegação detalhados por sua vez, os usuários são guiados em torno de paredes, por meio de portas e entre andares. Com a localização de forma, o usuário obtém as dicas sobre a direção geral do destino. Mas a inferência de tipos ou conhecimento do espaço também ajuda o usuário navegue pela estrutura para o destino.

Para criar uma experiência de localização de forma, primeiro prepare um espaço para a experiência e desenvolver um aplicativo que os usuários irão interagir com. Estas são as etapas conceituais:

1. **Planejamento do espaço de**: Decida quais locais dentro do espaço será parte da experiência de localização de forma. Em nossos cenários, o supervisor de fábrica ou o coordenador de tour Museu pode decidir quais locais para incluir na experiência de localização de forma.
2. **Conectar-se as âncoras**: Visite locais escolhidos para criar âncoras espaciais. Você pode fazer isso em um modo de administrador do aplicativo do usuário final ou em um aplicativo diferente inteiramente. Você vai se conectar ou relacionar cada âncora aos outros. O serviço mantém essas relações.
3. **Iniciar a experiência do usuário final**: Usuários executem o aplicativo para localizar uma âncora, que pode ser em qualquer um dos locais escolhidos. O design geral deve determinar os locais onde os usuários podem inserir a experiência.
4. **Encontrar nas proximidades âncoras**: Depois que o usuário encontra uma âncora, o aplicativo pode solicitar nas proximidades âncoras. Esse procedimento retorna uma pose entre o dispositivo e essas âncoras.
5. **Orientar o usuário**: O aplicativo pode usar a pose a cada um dessas âncoras para fornecer orientações sobre a direção de geral do usuário e a distância. Por exemplo, o feed no aplicativo de câmera pode mostrar um ícone e uma seta para representar cada possível destino, como mostra a imagem a seguir.
6. **Refinar as diretrizes**: Como os usuários se deslocam, o aplicativo periodicamente pode calcular uma novo pose entre o dispositivo e a âncora de destino. O aplicativo continua a refinar as dicas de orientações que ajudam o usuário chegam ao destino.

    ![Um exemplo de como um aplicativo pode mostrar diretrizes de localização de forma](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Conectar-se as âncoras

Para criar uma experiência de localização de forma, primeiro você precisa colocar as âncoras nos locais escolhidos. Nesta seção, vamos supor que administrador do aplicativo já foi concluída para esse trabalho.

### <a name="connect-anchors-in-a-single-session"></a>Conectar-se as âncoras em uma única sessão

Para conectar-se as âncoras de:

1. Ir para o primeiro local e crie âncora um usando um CloudSpatialAnchorSession.
2. Ir para o segundo local. A plataforma subjacente do MR/AR controla o movimento.
3. Crie âncora B usando o mesmo CloudSpatialAnchorSession. Âncoras A e B agora estão conectadas. O serviço de âncoras espacial mantém essa relação.
4. Continue o procedimento para as âncoras restantes.

### <a name="connect-anchors-in-multiple-sessions"></a>Conectar-se as âncoras em várias sessões

Você pode conectar âncoras espaciais ao longo de várias sessões. Usando esse método, você pode criar e conecte algumas âncoras ao mesmo tempo e posteriormente criar e conectar-se mais âncoras. 

Para conectar-se as âncoras ao longo de várias sessões:

1. O aplicativo cria alguns âncoras em um CloudSpatialAnchorSession. 
2. Em um momento diferente, o aplicativo localiza um dessas âncoras (por exemplo, A de âncora) por meio de um novo CloudSpatialAnchorSession.
3. Ir para um novo local. A plataforma subjacente de realidade misturada ou realidade aumentada controla o movimento.
4. Crie âncora C usando o mesmo CloudSpatialAnchorSession. Âncoras A, B e C agora estão conectadas. O serviço de âncoras espacial mantém essa relação.

Você pode continuar este procedimento para âncoras mais e mais sessões ao longo do tempo.

### <a name="verify-anchor-connections"></a>Verifique as conexões de âncora

O aplicativo pode verificar que as duas âncoras estejam conectadas emitindo uma consulta para âncoras próximas. Quando o resultado da consulta contém a âncora de destino, a conexão de âncora é verificado. Se as âncoras não estiverem conectadas, o aplicativo pode tentar conectar-se novamente. 

Aqui estão algumas razões âncoras falhar para se conectar:

* A realidade misturada ou realidade aumentada plataforma subjacente perdidos acompanhamento durante o processo de conexão âncoras.
* Devido a um erro de rede durante a comunicação com o serviço âncoras espacial, a conexão de âncora não pôde ser persistente.

### <a name="find-sample-code"></a>Encontrar código de exemplo

Para localizar o código de exemplo que mostra como conectar-se as âncoras e fazer nas proximidades de consultas, consulte [aplicativos de exemplo espacial âncoras](https://github.com/Azure/azure-spatial-anchors-samples).
