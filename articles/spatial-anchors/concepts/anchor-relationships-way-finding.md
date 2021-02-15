---
title: Relacionamentos de Âncora e descoberta de caminho
description: Saiba mais sobre o modelo conceitual por trás de relações de ancoragem. Aprenda a conectar âncoras dentro de um espaço e a usar a API próxima para atender a um cenário de descoberta de formas.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: ce79fe88b33659241a226af7bbb7a966ede41abb
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2020
ms.locfileid: "97696158"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Relações de âncora e descoberta de formas em âncoras espaciais do Azure

Usando relações de ancoragem, você pode criar âncoras conectadas em um espaço e, em seguida, fazer perguntas como estas:

* Há âncoras próximas?
* O que há de longe?

## <a name="examples"></a>Exemplos

Você pode usar âncoras conectadas em casos como estes:

* Um trabalhador precisa concluir uma tarefa que envolve a visita de vários locais em uma fábrica industrial. A fábrica tem âncoras espaciais em cada local. Um HoloLens ou aplicativo móvel ajuda a orientar o trabalhador de um local para o outro. Primeiro, o aplicativo solicita as âncoras espaciais próximas e, em seguida, orienta o trabalho para o próximo local. O aplicativo mostra visualmente a direção geral e a distância para o próximo local.

* Um museu cria âncoras espaciais em exibições públicas. Juntas, essas âncoras formam um tour de uma hora das exibições públicas essenciais do Museu. Em uma exibição pública, os visitantes podem abrir o aplicativo de realidade misturada do Museu em seus dispositivos móveis. Em seguida, eles apontam sua câmera de telefone em torno do espaço para ver a direção geral e a distância para as outras exibições públicas no Tour. Como um usuário se aproxima de uma exibição pública, o aplicativo atualiza a direção geral e a distância para ajudar a orientar o usuário.

## <a name="set-up-way-finding"></a>Configurar como encontrar

Um aplicativo que usa a direção da linha de visão e a distância entre âncoras para fornecer orientação é usar *a descoberta de formas*. A descoberta de formas é diferente da navegação ativar/desativar. Na navegação ativada por ativação, os usuários são guiados em relação às paredes, por meio de portas e entre os andares. Com a descoberta de formas, o usuário obtém dicas sobre a direção geral do destino. Mas a inferência ou o conhecimento do espaço também ajuda o usuário a navegar pela estrutura até o destino.

Para criar uma experiência de detecção de formas, primeiro Prepare um espaço para a experiência e desenvolva um aplicativo com o qual os usuários irão interagir. Estas são as etapas conceituais:

1. **Planejar o espaço**: decida quais locais dentro do espaço serão parte da experiência de descoberta de formas. Em nossos cenários, o supervisor de fábrica ou o coordenador de Tour do Museu pode decidir quais locais devem ser incluídos na experiência de descoberta.
2. **Conectar âncoras**: visite os locais escolhidos para criar âncoras espaciais. Você pode fazer isso em um modo de administrador do aplicativo de usuário final ou em um aplicativo diferente inteiramente. Você se conectará ou relacionará cada âncora às outras. O serviço mantém essas relações.
3. **Iniciar a experiência do usuário final**: os usuários executam o aplicativo para localizar uma âncora, que pode estar em qualquer um dos locais escolhidos. Seu design geral deve determinar os locais onde os usuários podem inserir a experiência.
4. **Localizar âncoras próximas**: depois que o usuário encontra uma âncora, o aplicativo pode solicitar âncoras próximas. Esse procedimento retorna uma pose entre o dispositivo e essas âncoras.
5. **Orientar o usuário**: o aplicativo pode usar a pose para cada uma dessas âncoras para fornecer orientação sobre a direção e a distância geral do usuário. Por exemplo, o feed de câmera no aplicativo pode mostrar um ícone e uma seta para representar cada destino potencial, como mostra a imagem a seguir.
6. **Refine as diretrizes**: conforme o usuário percorre, o aplicativo pode calcular periodicamente uma nova pose entre o dispositivo e a âncora de destino. O aplicativo continua a refinar as dicas de orientação que ajudam o usuário a chegar ao destino.

    ![Um exemplo de como um aplicativo pode mostrar diretrizes de descoberta de formas](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Conectar âncoras

Para criar uma experiência de detecção de formas, primeiro você precisa posicionar as âncoras nos locais escolhidos. Nesta seção, vamos supor que o administrador do aplicativo já terminou esse trabalho.

### <a name="connect-anchors-in-a-single-session"></a>Conectar âncoras em uma única sessão

Para conectar âncoras:

1. Percorra o primeiro local e crie a âncora A usando um CloudSpatialAnchorSession.
2. Passe para o segundo local. A plataforma de Sr/AR subjacente acompanha o movimento.
3. Crie a ancoragem B usando o mesmo CloudSpatialAnchorSession. As âncoras A e B agora estão conectadas. O serviço de âncoras espaciais mantém essa relação.
4. Continue o procedimento para as âncoras restantes.

### <a name="connect-anchors-in-multiple-sessions"></a>Conectar âncoras em várias sessões

Você pode conectar âncoras espaciais em várias sessões. Usando esse método, você pode criar e conectar algumas âncoras ao mesmo tempo e depois criar e conectar mais âncoras.

Para conectar âncoras em várias sessões:

1. O aplicativo cria algumas âncoras (âncoras A e B) em um CloudSpatialAnchorSession.
2. Em um momento diferente, o aplicativo localiza uma dessas âncoras (por exemplo, Anchor A) usando um novo CloudSpatialAnchorSession.
3. Passe para um novo local. A plataforma subjacente de realidade misturada ou aumento da realidade acompanha o movimento.
4. Crie a âncora C usando o mesmo CloudSpatialAnchorSession. As âncoras A, B e C agora estão conectadas. O serviço de âncoras espaciais mantém essa relação.

Você pode continuar este procedimento para mais âncoras e mais sessões ao longo do tempo.

### <a name="verify-anchor-connections"></a>Verificar conexões de âncora

O aplicativo pode verificar se duas âncoras estão conectadas emitindo uma consulta para âncoras próximas, que você pode fazer definindo o `NearAnchorCriteria` em um `CloudSpatialAnchorWatcher` . Quando o resultado da consulta contiver a âncora de destino, a conexão de âncora será verificada. Se as âncoras não estiverem conectadas, o aplicativo poderá tentar conectá-las novamente.

Aqui estão algumas razões pelas quais as âncoras podem falhar ao se conectar:

* A plataforma de realidade misturada ou ampliada da realidade perdeu o rastreamento durante o processo de conexão de âncoras.
* Devido a um erro de rede durante a comunicação com o serviço âncoras espaciais, a conexão de ancoragem não pode ser persistente.

### <a name="find-sample-code"></a>Encontre o código de exemplo

Para encontrar um código de exemplo que mostra como conectar âncoras e fazer consultas próximas, consulte [aplicativos de exemplo de âncoras espaciais](https://github.com/Azure/azure-spatial-anchors-samples).
