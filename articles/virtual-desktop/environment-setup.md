---
title: Ambiente de área de trabalho virtual do Windows – Azure
description: Saiba mais sobre os elementos básicos de um ambiente de área de trabalho virtual do Windows, como pools de hosts e grupos de aplicativos.
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 99a07dd1791b539ea44fcbab250aa9c227ee1705
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88002607"
---
# <a name="windows-virtual-desktop-environment"></a>Ambiente da Área de Trabalho Virtual do Windows

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica), sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/environment-setup-2019.md).

A área de trabalho virtual do Windows é um serviço que oferece aos usuários acesso fácil e seguro a seus desktops e RemoteApps virtualizados. Este tópico lhe dirá um pouco mais sobre a estrutura geral do ambiente de área de trabalho virtual do Windows.

## <a name="host-pools"></a>Pools de hosts

Um pool de hosts é uma coleção de máquinas virtuais do Azure que se registram na área de trabalho virtual do Windows como hosts de sessão ao executar o agente de área de trabalho virtual do Windows. Todas as máquinas virtuais do host de sessão em um pool de hosts devem ser originadas da mesma imagem para uma experiência de usuário consistente.

Um pool de hosts pode ser um dos dois tipos:

- Pessoal, onde cada host de sessão é atribuído a usuários individuais.
- Em pool, onde os hosts de sessão podem aceitar conexões de qualquer usuário autorizado a um grupo de aplicativos dentro do pool de hosts.

Você pode definir propriedades adicionais no pool de hosts para alterar seu comportamento de balanceamento de carga, quantas sessões cada host de sessão pode executar e o que o usuário pode fazer para os hosts de sessão no pool de hosts enquanto entra em suas sessões de área de trabalho virtual do Windows. Você controla os recursos publicados para os usuários por meio de grupos de aplicativos.

## <a name="app-groups"></a>Grupos de aplicativos

Um grupo de aplicativos é um agrupamento lógico de aplicativos instalados em hosts de sessão no pool de hosts. Um grupo de aplicativos pode ser de um dos dois tipos:

- RemoteApp, onde os usuários acessam os RemoteApps que você seleciona individualmente e publica no grupo de aplicativos
- Área de trabalho, onde os usuários acessam a área de trabalho completa

Por padrão, um grupo de aplicativos de desktop (denominado "grupo de aplicativos de área de trabalho") é criado automaticamente sempre que você cria um pool de hosts. Você pode remover esse grupo de aplicativos a qualquer momento. No entanto, você não pode criar outro grupo de aplicativos de área de trabalho no pool de hosts enquanto houver um grupo de aplicativos de desktop. Para publicar RemoteApps, você deve criar um grupo de aplicativos do RemoteApp. Você pode criar vários grupos de aplicativos do RemoteApp para acomodar diferentes cenários de trabalho. Grupos diferentes de aplicativos do RemoteApp também podem conter RemoteApps sobrepostos.

Para publicar recursos para os usuários, você deve atribuí-los a grupos de aplicativos. Ao atribuir usuários a grupos de aplicativos, considere as seguintes coisas:

- Um usuário pode ser atribuído a um grupo de aplicativos de desktop e a um grupo de aplicativos do RemoteApp no mesmo pool de hosts. No entanto, os usuários só podem iniciar um tipo de grupo de aplicativos por sessão. Os usuários não podem iniciar os dois tipos de grupos de aplicativos ao mesmo tempo em uma única sessão.
- Um usuário pode ser atribuído a vários grupos de aplicativos dentro do mesmo pool de hosts, e seu feed será um acúmulo de ambos os grupos de aplicativos.

## <a name="workspaces"></a>Workspaces

Um espaço de trabalho é um agrupamento lógico de grupos de aplicativos na área de trabalho virtual do Windows. Cada grupo de aplicativos de área de trabalho virtual do Windows deve ser associado a um espaço de trabalho para que os usuários vejam os aplicativos e áreas de trabalho remotas publicados neles.

## <a name="end-users"></a>Usuários finais

Depois de atribuir usuários aos seus grupos de aplicativos, eles podem se conectar a uma implantação de área de trabalho virtual do Windows com qualquer um dos clientes de área de trabalho virtual do Windows.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o acesso delegado e como atribuir funções a usuários em [acesso delegado na área de trabalho virtual do Windows](delegated-access-virtual-desktop.md).

Para saber como configurar o pool de hosts da área de trabalho virtual do Windows, confira [criar um pool de hosts com o portal do Azure](create-host-pools-azure-marketplace.md).

Para saber como se conectar à área de trabalho virtual do Windows, consulte um dos seguintes artigos:

- [Conectar-se com o Windows 10 ou o Windows 7](connect-windows-7-10.md)
- [Conectar-se com um navegador da Web](connect-web.md)
- [Conectar-se ao cliente Android](connect-android.md)
- [Conectar-se ao cliente macOS](connect-macos.md)
- [Conectar-se ao cliente iOS](connect-ios.md)