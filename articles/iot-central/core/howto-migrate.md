---
title: Migrar um aplicativo de IoT Central do Azure v2 para v3 | Microsoft Docs
description: Como administrador, saiba como migrar seu aplicativo de IoT Central do Azure v2 para v3
author: troyhopwood
ms.author: troyhop
ms.date: 01/18/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 735ad7ad9ded6baded59ab3f08e239d1c8376b74
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101702718"
---
# <a name="migrate-your-v2-iot-central-application-to-v3"></a>Migrar seu aplicativo IoT Central v2 para v3

*Este artigo se aplica aos administradores do.*

Atualmente, quando você cria um novo aplicativo IoT Central, ele é um aplicativo v3. Se você tiver criado um aplicativo anteriormente, dependendo de quando ele foi criado, ele poderá ser v2. Este artigo descreve como migrar um V2 para um aplicativo V3 para ter certeza de que você está usando os recursos de IoT Central mais recentes.

Para saber como identificar a versão de um aplicativo IoT Central, consulte [sobre seu aplicativo](howto-get-app-info.md).

As etapas para migrar um aplicativo de v2 para v3 são:

1. Crie um novo aplicativo v3 a partir do aplicativo v2.
1. Configure o aplicativo v3.
1. Excluir para o aplicativo v2.

## <a name="create-a-new-v3-application"></a>Criar um novo aplicativo v3

Use o assistente de migração para criar um novo aplicativo v3.

IoT Central não dá suporte à migração para um aplicativo v3 existente. Para mover dispositivos existentes automaticamente, use o assistente de migração para criar seu aplicativo v3.

O assistente de migração:

- Cria um novo aplicativo v3.
- Verifica a compatibilidade dos modelos de dispositivo com v3.
- Copia todos os modelos de dispositivo para o novo aplicativo v3.
- Copia todos os usuários e atribuições de função para o novo aplicativo v3.

> [!NOTE]
> Para garantir a compatibilidade do dispositivo com v3, os tipos primitivos no modelo de dispositivo se tornam propriedades de objeto. Você não precisa fazer nenhuma alteração no código do dispositivo.

Você deve ser um administrador para migrar um aplicativo para v3.

1. No menu **Administração** , selecione **migrar para um aplicativo v3**:

    :::image type="content" source="media/howto-migrate/migrate-menu.png" alt-text="Captura de tela que mostra o assistente de migração de aplicativos":::

1. Insira um novo **nome de aplicativo** e, opcionalmente, altere a  **URL** gerada automaticamente. A URL não pode ser igual à URL do seu aplicativo v2 atual. No entanto, você pode alterar a URL mais tarde depois de excluir o aplicativo v2.

1. Selecione **criar um novo aplicativo v3**.

    :::image type="content" source="media/howto-migrate/create-app.png" alt-text="Captura de tela que mostra as opções no assistente de migração de aplicativos":::

    Dependendo do número e da complexidade de seus modelos de dispositivo, esse processo pode levar vários minutos para ser concluído.

    > [!Warning]
    > A criação de seu aplicativo v3 falhará se qualquer modelo de dispositivo tiver campos que começam com um número ou contiverem qualquer um dos caracteres a seguir (,,,,,,, `+` `*` ,,,, `?` `^` `$` `(` `)` `[` `]` `{` `}` `|` , `\` ). O esquema de modelo de dispositivo DTDL usado pelos aplicativos v3 não permite esses caracteres. Atualize seu modelo de dispositivo para resolver esse problema antes de migrar para o v3.

1. Quando seu novo aplicativo v3 estiver pronto, selecione **abrir seu novo aplicativo** para abri-lo.

    :::image type="content" source="media/howto-migrate/open-app.png" alt-text="Captura de tela que mostra o assistente de migração de aplicativos após a migração do aplicativo":::

## <a name="configure-the-v3-application"></a>Configurar o aplicativo v3

Depois que o novo aplicativo v3 for criado, faça as alterações de configuração antes de mover os dispositivos do aplicativo v2 para o aplicativo v3.

> [!TIP]
> Reserve um tempo para se [familiarizar com o V3](overview-iot-central-tour.md#navigate-your-application) , pois ele tem algumas diferenças da versão anterior.

Aqui estão algumas etapas de configuração recomendadas a serem consideradas:

- [Configurar painéis](howto-add-tiles-to-your-dashboard.md)
- [Configurar exportação de dados](howto-export-data.md)
- [Configurar regras e ações](quick-configure-rules.md)
- [Personalizar a interface do usuário do aplicativo](howto-customize-ui.md)

Quando seu aplicativo v3 estiver configurado para atender às suas necessidades, você estará pronto para mover seus dispositivos do seu aplicativo v2 para seu aplicativo v3.

## <a name="move-your-devices-to-the-v3-application"></a>Mover seus dispositivos para o aplicativo v3

Quando esta etapa for concluída, todos os seus dispositivos se comunicarão somente com seu novo aplicativo v3.

> [!IMPORTANT]
> Antes de mover seus dispositivos para seu aplicativo v3, exclua todos os dispositivos que você criou no aplicativo v3.

Esta etapa move todos os dispositivos existentes para seu novo aplicativo v3. Os dados do dispositivo não são copiados.

Selecione **mover todos os dispositivos** para começar a mover seus dispositivos. Esta etapa pode levar vários minutos para ser concluída.

:::image type="content" source="media/howto-migrate/move-devices.png" alt-text="Captura de tela que mostra o assistente de migração de aplicativos com a opção mover dispositivos":::

Depois que a movimentação for concluída, reinicie todos os seus dispositivos para garantir que eles se conectem ao novo aplicativo v3.

> [!WARNING]
> Não exclua seu aplicativo v3 já que seu aplicativo v2 agora está inoperante.

## <a name="delete-your-old-v2-application"></a>Excluir seu aplicativo v2 antigo

Depois de validar que tudo funciona conforme o esperado em seu novo aplicativo v3, exclua seu aplicativo v2 antigo. Essa etapa garante que você não seja cobrado por um aplicativo que não usa mais.

> [!Note]
> Para excluir um aplicativo, você deve ter permissões para excluir recursos na assinatura do Azure que você escolheu ao criar o aplicativo. Para saber mais, confira [Usar o controle de acesso baseado em função para gerenciar o acesso aos recursos de sua assinatura do Azure](../../role-based-access-control/role-assignments-portal.md).

1. Em seu aplicativo v2, selecione a guia **Administração** no menu
2. Selecione **excluir** para excluir permanentemente seu aplicativo IOT central. Essa opção exclui permanentemente todos os dados associados a esse aplicativo.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como migrar seu aplicativo, a próxima etapa sugerida é examinar a [interface do usuário IOT central do Azure](overview-iot-central-tour.md) para ver o que mudou na v3.