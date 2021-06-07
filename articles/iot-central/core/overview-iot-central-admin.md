---
title: Guia do administrador do Azure IoT Central
description: O Azure IoT Central é uma plataforma de aplicativo IoT que simplifica a criação de soluções de IoT. Este artigo fornece uma visão geral da função de administrador no IoT Central.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 16a8aecae70d73399acb3878d7088e5086c053a1
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110484"
---
# <a name="iot-central-administrator-guide"></a>Guia do administrador do IoT Central

*Este artigo se aplica aos administradores do.*

Um aplicativo do IoT Central permite que você monitore e gerencie milhões de dispositivos ao longo de seu ciclo de vida. Este guia destina-se a administradores que gerenciam aplicativos IoT Central.

No IoT Central, um administrador:

- Gerencia usuários e funções no aplicativo.
- Gerencia a segurança, como a autenticação de dispositivo.
- Define as configurações do aplicativo.
- Atualiza aplicativos.
- Exporta e compartilha aplicativos.
- Monitora a integridade do aplicativo.

## <a name="users-and-roles"></a>Usuários e funções

O IoT Central usa um sistema de controle de acesso baseado em função para gerenciar permissões de usuário em um aplicativo. O IoT Central tem três funções internas para administradores, construtores de solução e operadores. Um administrador pode criar funções personalizadas com conjuntos específicos de permissões. Um administrador é responsável por adicionar usuários a um aplicativo e atribuí-los a funções.

Para saber mais, consulte [gerenciar usuários e funções em seu aplicativo IOT central](howto-manage-users-roles.md).

## <a name="application-security"></a>Segurança do aplicativo

Os dispositivos que se conectam ao aplicativo IoT Central normalmente usam certificados X. 509 ou SAS (assinaturas de acesso compartilhado) como credenciais. O administrador gerencia os certificados de grupo ou as chaves das quais as credenciais do dispositivo são derivadas.

Para saber mais, confira [registro de grupo x. 509](concepts-get-connected.md#x509-group-enrollment), [registro de grupo SAS](concepts-get-connected.md#sas-group-enrollment)e [como reverter certificados de dispositivo x. 509](how-to-roll-x509-certificates.md).

O administrador também pode criar e gerenciar os tokens de API que um aplicativo cliente usa para autenticar com seu aplicativo IoT Central. Os aplicativos cliente usam a API REST para interagir com IoT Central.

## <a name="configure-an-application"></a>Configurar um aplicativo

O administrador pode configurar o comportamento e a aparência de um aplicativo IoT Central. Para obter mais informações, consulte:

- [Alterar o nome do aplicativo e URL](howto-administer.md#change-application-name-and-url)
- [Personalizar a interface do usuário](howto-customize-ui.md)
- [Mover um aplicativo para um plano de preços diferente](howto-view-bill.md)
- [Configurar carregamentos de arquivo](howto-configure-file-uploads.md)

## <a name="export-an-application"></a>Exportar um aplicativo

Um administrador pode:

- Crie uma cópia de um aplicativo se você precisar apenas de uma cópia duplicada do seu aplicativo. Por exemplo, você pode precisar de uma cópia duplicada para teste.
- Crie um modelo de aplicativo a partir de um aplicativo existente se você planeja criar várias cópias.

Para saber mais, consulte [exportar seu aplicativo de IOT do Azure](howto-use-app-templates.md).

## <a name="migrate-to-a-new-version"></a>Migrar para uma nova versão

Um administrador pode migrar um aplicativo para uma versão mais recente. Atualmente, um aplicativo recém-criado é um aplicativo v3. Um administrador pode precisar migrar um aplicativo v2 para um aplicativo v3.

Para saber mais, consulte [migrar seu aplicativo de IOT central v2 para v3](howto-migrate.md).

## <a name="monitor-application-health"></a>Monitorar a integridade do aplicativo

Um administrador pode usar IoT Central métricas para avaliar a integridade dos dispositivos conectados e a integridade das exportações de dados em execução.

Para exibir as métricas, um administrador pode usar gráficos nas consultas portal do Azure, API REST ou PowerShell ou CLI do Azure.

Para saber mais, consulte [monitorar a integridade geral de um aplicativo IOT central](howto-monitor-application-health.md).

## <a name="tools"></a>Ferramentas

Muitas das ferramentas que você usa como administrador estão disponíveis na seção **Administração** de cada aplicativo IOT central. Você também pode usar as seguintes ferramentas para concluir algumas tarefas administrativas:

- [CLI do Azure](howto-manage-iot-central-from-cli.md)
- [PowerShell do Azure](howto-manage-iot-central-from-powershell.md)
- [Azure portal](howto-manage-iot-central-from-portal.md)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre como administrar seu aplicativo de IoT Central do Azure, a próxima etapa sugerida é aprender a [gerenciar usuários e funções](howto-manage-users-roles.md) no Azure IOT central.
