---
title: Ambiente de visualização de área de trabalho Virtual do Windows – Azure
description: Os elementos básicos de um ambiente de visualização de área de trabalho Virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: ceed6a8bb74206b7c6689ce542482148800e4ba9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870312"
---
# <a name="windows-virtual-desktop-preview-environment"></a>Ambiente de visualização de área de trabalho Virtual do Windows

Visualização de área de trabalho Virtual do Windows é um serviço que fornece aos usuários acesso fácil e seguro a suas áreas de trabalho virtualizadas e RemoteApps. Este tópico informa um pouco mais sobre a estrutura geral do ambiente de área de trabalho Virtual do Windows.

## <a name="tenants"></a>Locatários

O locatário de área de trabalho Virtual do Windows é a principal interface para gerenciar seu ambiente de área de trabalho Virtual do Windows. Cada locatário de área de trabalho Virtual do Windows deve estar associado com o Azure Active Directory que contém os usuários que se conectará ao ambiente. O locatário de área de trabalho Virtual do Windows, você pode começar criando pools de host para executar cargas de trabalho dos usuários.

## <a name="host-pools"></a>Pools de host

Um pool de host é uma coleção de máquinas virtuais do Azure que se registram para a área de trabalho Virtual do Windows como hosts de sessão quando você executar o agente de área de trabalho Virtual do Windows. Todas as máquinas virtuais de host de sessão em um pool de host deve ser originadas da mesma imagem para uma experiência de usuário consistente.

Um pool de host pode ser um dos dois tipos:

- Pessoal, onde cada host de sessão é atribuída a usuários individuais.
- Em pool, onde os hosts de sessão podem aceitar conexões de qualquer usuário autorizado a um grupo de aplicativos dentro do pool de host.

Você pode definir propriedades adicionais sobre o pool de host para alterar seu comportamento de balanceamento de carga, quantas sessões de cada host de sessão pode tomar, e que o usuário pode fazer para hosts de sessão no pool de host, enquanto estiver conectado às suas sessões de área de trabalho Virtual do Windows. Você controlar os recursos publicados para usuários por meio de grupos de aplicativos.

## <a name="app-groups"></a>Grupos de aplicativos

Um grupo de aplicativos é um agrupamento lógico de aplicativos instalados nos hosts de sessão no pool de host. Um grupo de aplicativos pode ser um dos dois tipos:

- O RemoteApp, onde os usuários acessam os RemoteApps selecionar individualmente e publicar para o grupo de aplicativos
- Área de trabalho, onde os usuários acessam a área de trabalho completa

Por padrão, um grupo de aplicativos da área de trabalho (denominado "Grupo de aplicativos de área de trabalho") é criado automaticamente sempre que você cria um pool de host. Você pode remover esse grupo de aplicativo a qualquer momento. No entanto, é possível criar outro grupo de aplicativos da área de trabalho no pool de host, enquanto existe um grupo de aplicativos da área de trabalho. Para publicar aplicativos remotos, você deve criar um grupo de aplicativos do RemoteApp. Você pode criar vários grupos de aplicativos do RemoteApp para acomodar cenários de trabalho diferentes. Diferentes grupos de aplicativos do RemoteApp também podem conter a sobreposição de RemoteApps.

Para publicar recursos para os usuários, você deve atribuí-los para grupos de aplicativos. Ao atribuir usuários a grupos de aplicativos, considere o seguinte:

- Um usuário não pode ser atribuído a um grupo de aplicativos da área de trabalho e um grupo de aplicativos do RemoteApp no mesmo pool de host.
- Um usuário pode ser atribuído a vários grupos de aplicativo dentro do mesmo pool de host, e seu feed será um acúmulo de ambos os grupos de aplicativos.

## <a name="tenant-groups"></a>Grupos de locatários

Na área de trabalho Virtual do Windows, o locatário de área de trabalho Virtual do Windows é onde acontece a maior parte do programa de instalação e configuração. O locatário de área de trabalho Virtual do Windows contém os pools de host, grupos de aplicativos e as atribuições de usuário de grupo do aplicativo. No entanto, pode haver algumas situações em que você precise gerenciar vários locatários de área de trabalho Virtual do Windows ao mesmo tempo, especialmente se você for um provedor de serviços de nuvem (CSP) ou um parceiro de hospedagem. Nessas situações, você pode usar um grupo personalizado de locatário de área de trabalho Virtual do Windows para colocar cada um dos locatários de área de trabalho Virtual do Windows dos clientes e gerencie centralmente o acesso. No entanto, se estiver gerenciando apenas um único locatário de área de trabalho Virtual do Windows, o conceito de grupo do locatário não se aplica e você pode continuar a operar e gerenciar seu locatário que existe no grupo padrão de locatário.

## <a name="end-users"></a>Usuários finais

Depois de atribuir usuários a seus grupos de aplicativos, eles possam se conectar a uma implantação de área de trabalho Virtual do Windows com qualquer um dos clientes de área de trabalho Virtual do Windows.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como atribuir funções a usuários e o acesso delegado [acesso delegado na visualização de área de trabalho Virtual do Windows](delegated-access-virtual-desktop.md).

Para saber como configurar seu locatário de área de trabalho Virtual do Windows, consulte [criar um locatário na visualização de área de trabalho Virtual do Windows](tenant-setup-azure-active-directory.md).

Para saber como se conectar à área de trabalho Virtual do Windows, consulte um dos seguintes artigos:

- [Conectar-se ao cliente da Área de Trabalho Remota no Windows 7 e no Windows 10](connect-windows-7-and-10.md)
- [Conectar-se ao cliente Web da Versão Prévia da Área de Trabalho Virtual do Windows](connect-web.md)
