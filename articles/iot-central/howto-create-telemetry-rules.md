---
title: Criar e gerenciar regras de telemetria no aplicativo Azure IoT Central | Microsoft Docs
description: As regras de telemetria do Azure IoT Central permitem monitorar os dispositivos quase em tempo real e invocar ações automaticamente, como enviar um email, quando a regra é disparada.
author: ankitgupta
ms.author: ankitgup
ms.date: 02/02/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 0fb7cdaa24d139549545c93c920d60936d3c9fc1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60886024"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Criar uma regra de telemetria e configurar notificações no aplicativo Azure IoT Central

*Este artigo aplica-se a operadores, construtores e administradores.*

É possível usar a Azure IoT Central para monitorar remotamente os dispositivos conectados. As regras do Azure IoT Central permitem monitorar os dispositivos quase em tempo real e invocar ações automaticamente, como enviar um email ou disparar o Microsoft Flow. Com apenas alguns cliques, você pode definir a condição para monitorar os dados do dispositivo e configurar a ação correspondente. Este artigo explica como criar regras para monitorar a telemetria enviada pelo dispositivo.

Dispositivos podem usar a medição de telemetria para enviar dados numéricos do dispositivo. Uma regra de telemetria é disparada quando a telemetria do dispositivo selecionado ultrapassa um limite especificado.

## <a name="create-a-telemetry-rule"></a>Criar uma regra de telemetria

Para criar uma regra de telemetria, o modelo do dispositivo deve ter pelo menos uma medida de telemetria definida. Este exemplo usa um dispositivo de máquina de vendas conectado que envia telemetria de temperatura e umidade. A regra monitora a temperatura relatada pelo dispositivo e envia um email quando ultrapassa os 80 graus.

1. Usando o **modelos de dispositivo** página, navegue até o modelo de dispositivo para o qual você está adicionando a regra para.

1. Se você ainda não criou regras, a tela a seguir será exibida:

    ![Não há regras](media/howto-create-telemetry-rules/Rules_Landing_Page.png)

1. Sobre o **regras** guia, selecione **+ nova regra** para ver os tipos de regras que você pode criar.

1. Selecione **telemetria** para criar uma regra para monitorar a telemetria do dispositivo.

    ![Tipos de Regras](media/howto-create-telemetry-rules/Rule_Types.png)

1. Digite um nome que o ajude a identificar a regra neste modelo de dispositivo.

1. Para habilitar imediatamente a regra para todos os dispositivos criados para esse modelo, alterne **Habilitar regra para todos os dispositivos deste modelo**.

   ![Detalhe da Regra](media/howto-create-telemetry-rules/Rule_Detail.png)

    A regra se aplica automaticamente a todos os dispositivos no modelo de dispositivo.

### <a name="configure-the-rule-conditions"></a>Configurar as condições de regra

A condição define os critérios que são monitorados pela regra.

1. Selecione **+** próximo a **condições** para adicionar uma nova condição.

1. Escolha a telemetria que deseja monitorar na lista suspensa **Medida**.

1. Em seguida, escolha **Agregação**, **Operador** e forneça um valor **Limite**.
   - A agregação é opcional. Sem agregação, a regra é disparada para cada ponto de dados de telemetria que atenda à condição. Por exemplo, se a regra está configurada para o gatilho quando a temperatura estiver acima de 80, em seguida, a regra dispara quase instantaneamente quando o dispositivo relata temperatura > 80.
   - Se uma função como Contagem, Média, Mín, Máx for escolhida, o usuário deverá fornecer uma **Janela de tempo de agregação** por meio da qual a condição precisa ser avaliada. Por exemplo, se você definir o período como "5 minutos" e a regra procurar a temperatura Média acima de 80, a regra dispara quando a temperatura média estiver acima de 80 por pelo menos 5 minutos. A frequência de avaliação da regra é igual à **Janela de tempo de agregação**, o que significa que, neste exemplo, a regra é avaliada uma vez a cada 5 minutos.

     ![Condição](media/howto-create-telemetry-rules/Aggregate_Condition_Filled_Out.png)

     >[!NOTE]
     >Mais de uma medida de telemetria pode ser adicionada em **Condição**. Quando várias condições são especificadas, todas as condições devem ser atendidas para que a regra seja disparada. Cada condição é unida por uma cláusula 'AND' implicitamente. Ao usar a agregação, cada medida deve ser agregada.

### <a name="configure-actions"></a>Configurar ações

Esta seção mostra como configurar as ações a serem executadas quando a regra é disparada. Ações são invocadas quando todas as condições especificadas na regra são avaliadas como true.

1. Escolha **+** próximo a **Ações**. Aqui, você vê a lista de ações disponíveis.  

    ![Adicionar Ação](media/howto-create-telemetry-rules/Add_Action.png)

1. Escolha a ação **Email**, insira um endereço de email válido no campo **Para** e forneça uma observação para aparecer no corpo do email quando a regra for disparada.

    > [!NOTE]
    > Os emails serão enviados apenas aos usuários que foram adicionados ao aplicativo e fizeram logon pelo menos uma vez. Saiba mais sobre o [gerenciamento de usuários](howto-administer.md) no Azure IoT Central.

   ![Configurar Ação](media/howto-create-telemetry-rules/Configure_Action.png)

1. Para salvar a regra, escolha **Salvar**. A regra entra em ação em poucos minutos e começa a monitorar a telemetria enviada ao aplicativo. Quando a condição especificada na regra é atendida, a regra aciona a ação de e-mail configurada.

É possível adicionar outras ações à regra, como o Microsoft Flow e webhooks. Você pode adicionar até 5 ações por regra.

- [Ação do Microsoft Flow](howto-add-microsoft-flow.md) para iniciar um fluxo de trabalho no Microsoft Flow quando uma regra é disparada 
- [Ação do Webhook](howto-create-webhooks.md) para notificar outros serviços quando uma regra é disparada

## <a name="parameterize-the-rule"></a>Parametrizar a regra

As regras podem derivar determinados valores de **Propriedades do Dispositivo** como parâmetros. A utilização de parâmetros é útil em cenários onde os limites de telemetria variam para diferentes dispositivos. Ao criar a regra, escolha uma propriedade de dispositivo que especifique o limite, como **Limite Máximo Ideal**, em vez de fornecer um valor absoluto, como 80 graus. Quando a regra é executada, ela corresponde à telemetria do dispositivo com o valor fornecido na propriedade de dispositivo.

Usar parâmetros é uma maneira eficiente de reduzir o número de regras a serem gerenciadas por modelo de dispositivo.

As ações também podem ser configuradas usando a **Propriedade de Dispositivo** como um parâmetro. Se um endereço de email for armazenado como uma propriedade, ele poderá ser usado quando você definir o endereço **Para**.

## <a name="delete-a-rule"></a>Excluir uma regra

Se você não precisar mais de uma regra, exclua-a, abrindo a regra e escolhendo **Excluir**. Excluir a regra remove-a do modelo de dispositivo e de todos os dispositivos associados.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Habilitar ou desabilitar uma regra para um modelo de dispositivo

Navegue até o dispositivo e escolha a regra que você deseja habilitar ou desabilitar. Alterne o botão **Habilitar regra para todos os dispositivos deste modelo** na regra para habilitar ou desabilitar a regra para todos os dispositivos associados ao modelo de dispositivo.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Habilitar ou desabilitar uma regra para um dispositivo

Navegue até o dispositivo e escolha a regra que você deseja habilitar ou desabilitar. Alterne o botão **Habilitar regra para este dispositivo** para habilitar ou desabilitar a regra para esse dispositivo.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como criar regras no aplicativo Azure IoT Central, estas são algumas das próximas etapas:

- [Adicionar ação do Microsoft Flow em regras](howto-add-microsoft-flow.md)
- [Adicionar ação de Webhook em regras](howto-create-webhooks.md)
- [Várias ações para executar a partir de uma ou mais regras de grupo](howto-use-action-groups.md)
- [Como gerenciar seus dispositivos](howto-manage-devices.md)
