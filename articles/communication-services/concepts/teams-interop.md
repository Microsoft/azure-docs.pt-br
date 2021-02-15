---
title: Interoperabilidade de reuniões do Teams
titleSuffix: An Azure Communication Services concept document
description: Participar das reuniões do Teams
author: chpalm
manager: chpalm
services: azure-communication-services
ms.author: chpalm
ms.date: 10/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 894f8b17c3c5e9e3147b66854a5809bf82351fb9
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100012381"
---
# <a name="teams-interoperability"></a>Interoperabilidade do Teams

[!INCLUDE [Private Preview Notice](../includes/private-preview-include.md)]

Os Serviços de Comunicação do Azure podem ser usados para criar experiências de reunião personalizadas que interagem com o Microsoft Teams. Os usuários das suas soluções dos Serviços de Comunicação podem interagir com os participantes do Teams usando o compartilhamento de voz, vídeo e tela.

Essa interoperabilidade permite que você crie aplicativos personalizados do Azure que conectam os usuários às reuniões do Teams. Os usuários dos seus aplicativos personalizados não precisam ter identidades do Azure Active Directory nem licenças do Teams para experimentar essa funcionalidade. Isso é ideal para reunir funcionários (que possam estar familiarizados com o Teams) e usuários externos (que usam uma experiência de aplicativo personalizada) em uma experiência de reunião perfeita. Isso permite que você crie experiências semelhantes às seguintes:

1. Os funcionários usam o Teams para agendar uma reunião
2. Seu aplicativo personalizado dos Serviços de Comunicação usa as APIs do Microsoft Graph para acessar os detalhes da reunião
3. Os detalhes da reunião são compartilhados com usuários externos por meio do aplicativo personalizado
4. Os usuários externos usam seu aplicativo personalizado para participar da reunião do Teams (por meio da biblioteca de clientes de Chamada dos Serviços de Comunicação)

A arquitetura de alto nível para esse caso de uso tem esta aparência: 

![Arquitetura para interoperabilidade com o Teams](./media/call-flows/teams-interop.png)

Embora alguns recursos de reunião do Teams, por exemplo, mão levantada, Modo conferência e salas para sessão de grupo, só estejam disponíveis para os usuários do Teams, seu aplicativo personalizado terá acesso às principais funcionalidades de áudio, vídeo e compartilhamento de tela da reunião.

Quando um usuário dos Serviços de Comunicação participa da reunião do Teams, o nome de exibição fornecido por meio da biblioteca de clientes de Chamada será mostrado aos usuários do Teams. O usuário dos Serviços de Comunicação, de outro modo, será tratado como um usuário anônimo no Teams. Seu aplicativo personalizado deve considerar a autenticação do usuário e outras medidas de segurança para proteger as reuniões do Teams. Lembre-se das implicações de segurança de permitir que usuários anônimos participem das reuniões e use o [guia de segurança do Teams](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings) para configurar as funcionalidades disponíveis para usuários anônimos.

Os usuários dos Serviços de Comunicação podem participar das reuniões agendadas do Teams, desde que as participações anônimas estejam habilitadas nas [configurações da reunião](/microsoftteams/meeting-settings-in-teams).

## <a name="teams-in-government-clouds-gcc"></a>Teams em Nuvens Governamentais (GCC)
No momento, a interoperabilidade dos Serviços de Comunicação do Azure não tem permissão para implantações do Teams usando as [nuvens governamentais (GCC) do Microsoft 365](/MicrosoftTeams/plan-for-government-gcc). 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Ingressar seu aplicativo de chamada em uma reunião do Teams](../quickstarts/voice-video-calling/get-started-teams-interop.md)