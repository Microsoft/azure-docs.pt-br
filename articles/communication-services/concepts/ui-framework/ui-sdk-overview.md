---
title: Visão geral da estrutura da IU dos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services conceptual document
description: Saiba mais sobre a estrutura da IU dos Serviços de Comunicação do Azure
author: ddematheu2
ms.author: dademath
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: b6f9987c21ecdcf0e1b5358486312dceb26c8b82
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008546"
---
# <a name="azure-communication-services-ui-framework"></a>Estrutura da IU dos Serviços de Comunicação do Azure

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

:::image type="content" source="../media/ui-framework/pre-and-custom-composite.png" alt-text="Comparação entre composições predefinidas e personalizadas":::

A estrutura da IU dos Serviços de Comunicação do Azure facilita a criação de experiências de usuário de comunicação modernas. Ele fornece uma biblioteca de componentes de interface do usuário prontos para produção que você pode colocar em seus aplicativos:

- **Componentes de composição** – esses componentes são soluções turn-key que implementam cenários de comunicação comuns. Você pode adicionar rapidamente experiências de vídeo ou chamadas de chat a seus aplicativos. Os componentes compostos são de software livre, criados usando componentes básicos.
- **Componentes básicos** – esses componentes são blocos de construção de software livre que permitem criar uma experiência de comunicação personalizada. Os componentes são oferecidos tanto para funcionalidades de chamada quanto de chat, que podem ser combinadas para a criação de experiências. 

Todas essas bibliotecas de clientes de interface do usuário usam ativos e [linguagem do Microsoft Fluent Design](https://developer.microsoft.com/fluentui/). A interface do usuário do Fluent fornece uma camada base para a estrutura da IU que foi testada na prática em todos os produtos da Microsoft.

## <a name="differentiating-components-and-composites"></a>**Diferenciação de componentes e compostos**

Os **componentes básicos** se baseiam nas principais bibliotecas de clientes dos Serviços de Comunicação do Azure e implementam ações básicas, como inicializar as principais bibliotecas de clientes, renderizar vídeo e fornecer controles de usuário para mudo, vídeo ativado/desativado etc. Você pode usar esses **componentes básicos** criar experiências de layout personalizado usando componentes de comunicação predefinidos e prontos para produção.

:::image type="content" source="../media/ui-framework/component-overview.png" alt-text="Visão geral do componente para a estrutura da IU":::

Os **componentes de composição** combinam vários **componentes básicos** para criar experiências de comunicação mais completas. Esses componentes de nível superior podem ser facilmente integrados a um aplicativo existente, resultando em uma experiência de comunicação completa sem a necessidade de criá-la do zero. Os desenvolvedores podem se concentrar em criar a experiência e o fluxo desejados nos aplicativos deles e deixar a complexidade das comunicações com os componentes de composição.

:::image type="content" source="../media/ui-framework/composite-overview.png" alt-text="Visão geral da composição da estrutura da IU":::

## <a name="what-ui-framework-is-best-for-my-project"></a>Qual é a melhor estrutura da IU para meu projeto?

A compreensão desses requisitos ajudará você a escolher a biblioteca de clientes correta:

- **Que nível de personalização você deseja?** As principais bibliotecas de clientes da Comunicação do Azure não têm uma experiência de usuário e foram projetadas para que você possa criar qualquer experiência de usuário que desejar. Os componentes da estrutura da IU fornecem ativos de interface do usuário a um custo de personalização reduzido.
- **Você precisa de recursos de Reunião?** O sistema de Reunião tem vários recursos exclusivos que não estão disponíveis atualmente nas principais bibliotecas de clientes dos Serviços de Comunicação do Azure, como a tela de fundo borrada e a mão levantada.
- **A quais plataformas você está direcionando?** Plataformas diferentes têm funcionalidades diferentes.

Os detalhes sobre a disponibilidade de recursos nos variados [SDKs de interface do usuário estão disponíveis aqui](ui-sdk-features.md), mas as principais compensações são resumidas abaixo.

|Biblioteca de clientes/SDK|Complexidade da implementação|    Capacidade de personalização|  Chamando| Chat| [Interoperabilidade do Teams](./../teams-interop.md)
|---|---|---|---|---|---|---|
|Componentes de composição|Baixo|Baixo|✔|✔|✕
|Componentes básicos|Médio|Médio|✔|✔|✕
|Principais bibliotecas de clientes|Alto|Alto|✔|✔ |✔

## <a name="cost"></a>Custo

O uso de estruturas da IU do Azure não tem nenhum custo nem medição extra do Azure. Você paga apenas pelo uso do serviço subjacente, usando os mesmos medidores de Chamada, Chat e PSTN.

## <a name="supported-use-cases"></a>Casos de uso compatíveis

Chamada:

- Ingressar na chamada dos Serviços de Comunicação do Azure com a ID do Grupo

Chat:

- Ingressar no chat dos Serviços de Comunicação do Azure com a ID do Thread

## <a name="supported-identities"></a>Identidades compatíveis:

Uma identidade dos Serviços de Comunicação do Azure é necessária para inicializar a estrutura da IU e autenticar para o serviço. Para obter mais informações sobre autenticação, confira [Autenticação](../authentication.md) e [Tokens de acesso](../../quickstarts/access-tokens.md)


## <a name="recommended-architecture"></a>Arquitetura recomendada 

:::image type="content" source="../media/ui-framework/framework-architecture.png" alt-text="A arquitetura recomendada da estrutura da IU com arquitetura cliente-servidor":::

Os componentes básicos e de composição são inicializados usando um token de acesso dos Serviços de Comunicação do Azure. Os tokens de acesso devem ser adquiridos dos Serviços de Comunicação do Azure por meio de um serviço confiável que você gerencia. Confira [Início Rápido: criar tokens de acesso](../../quickstarts/access-tokens.md) e [Tutorial de serviço confiável](../../tutorials/trusted-service-tutorial.md) para obter mais informações.

Essas bibliotecas de clientes também exigem o contexto para a chamada ou o chat em que serão ingressadas. De modo semelhante ao que ocorre com os tokens de acesso do usuário, esse contexto deve ser disseminado para clientes por meio de seu serviço confiável. A lista a seguir resume as funções de inicialização e gerenciamento de recursos que você precisa colocar em operação.

| Responsabilidades da Contoso                                 | Responsabilidades da estrutura da IU                         |
|----------------------------------------------------------|-----------------------------------------------------------------|
| Fornecer o token de acesso do Azure                    | Passar o token de acesso fornecido para inicialização de componentes        |
| Fornecer a função refresh                                 | Atualizar o token de acesso usando a função fornecida pelo desenvolvedor          |
| Recuperar/transmitir informações de ingresso em chamada ou chat          | Passar informações de chamada e chat para inicialização de componentes |
| Recuperar/passar informações do usuário para qualquer modelo de dados personalizado | Passar o modelo de dados personalizado para os componentes a serem renderizados          |
