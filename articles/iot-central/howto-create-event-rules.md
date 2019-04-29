---
title: Criar e gerenciar regras de eventos no aplicativo Azure IoT Central | Microsoft Docs
description: As regras de eventos do Azure IoT Central permitem monitorar os dispositivos quase em tempo real e invocar ações automaticamente, como enviar um email, quando a regra é disparada.
author: ankitscribbles
ms.author: ankitgup
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: c98136e2f45965834fa1c538a5929eee14b24466
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60886143"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Criar uma regra de Eventos e configurar notificações no aplicativo Azure IoT Central

*Este artigo aplica-se a operadores, construtores e administradores.*

É possível usar a Azure IoT Central para monitorar remotamente os dispositivos conectados. As regras do Azure IoT Central permitem monitorar os dispositivos quase em tempo real e invocar ações automaticamente, como enviar um email ou disparar o Microsoft Flow. Com apenas alguns cliques, você pode definir a condição para monitorar os dados do dispositivo e configurar a ação correspondente. Este artigo explica como criar regras para monitorar eventos enviados pelo dispositivo.

Dispositivos podem usar a medição de evento para enviar eventos de dispositivo importantes ou informativos. Uma regra de eventos é disparada quando o evento de dispositivo selecionado é relatado pelo dispositivo.

## <a name="create-an-event-rule"></a>Criar uma regra de eventos

Para criar uma regra de evento, o modelo do dispositivo deve ter pelo menos um evento de medida definido. Este exemplo usa um dispositivo de máquina de venda refrigerada que relata um evento de erro do motor da ventoinha. A regra monitora o evento relatado pelo dispositivo e enviará um email sempre que o evento for relatado.

1. Usando o **modelos de dispositivo** página, navegue até o modelo de dispositivo para o qual você está adicionando a regra para.

1. Se você ainda não criou regras, a tela a seguir será exibida:

    ![Não há regras](media/howto-create-event-rules/Rules_Landing_Page.png)

1. Sobre o **regras** guia, selecione **+ nova regra** para ver os tipos de regras que você pode criar.

1. Escolha o **evento** lado a lado para criar uma regra de monitoramento de evento.

    ![Tipos de regras](media/howto-create-event-rules/Rule_Types.png)

1. Digite um nome que o ajude a identificar a regra neste modelo de dispositivo.

1. Para habilitar imediatamente a regra para todos os dispositivos criados com base neste modelo, ativar/desativar **Habilitar regra para todos os dispositivos deste modelo**.

    ![Detalhe da Regra](media/howto-create-event-rules/Rule_Detail.png)

    A regra se aplica automaticamente a todos os dispositivos no modelo de dispositivo.

### <a name="configure-the-rule-conditions"></a>Configurar as condições de regra

A condição define os critérios que são monitorados pela regra.

1. Escolha **+** próximo a **Condições** para adicionar uma nova condição.

1. Escolha o evento que deseja monitorar na lista suspensa Medida. Neste exemplo, foi selecionado o evento **Erro do Motor do Ventilador**.

   ![Condição](media/howto-create-event-rules/Condition_Filled_Out.png)

1. Opcionalmente, você também pode definir **Contagem** como **Agregação** e fornecer o limite correspondente.

   - Sem agregação, a regra dispara para cada ponto de dados de evento que atenda à condição. Por exemplo, se você configurar a regra de condição para disparar quando uma **erro de Motor do ventilador** evento ocorre em seguida, a regra dispara quase que imediatamente quando o dispositivo relata que o evento.
   - Se a Contagem for usada como uma função de agregação, você precisará fornecer um **Limite** e uma **Janela de tempo de agregação** por meio dos quais a condição precisa ser avaliada. Nesse caso, a contagem de eventos é agregada e a regra dispara apenas se a contagem de eventos agregados com o limite.

     Por exemplo, se você quiser alertar quando há mais de três eventos de dispositivo em cinco minutos, selecione o evento e defina a função de agregação como "contagem", o operador como "maior que" e "limite" como 3. Defina "Período de tempo de agregação" como "5 minutos". A regra dispara quando mais de três eventos são enviados pelo dispositivo em cinco minutos. A frequência de avaliação da regra é igual à **Janela de tempo de agregação**, o que significa que, neste exemplo, a regra é avaliada uma vez a cada cinco minutos.

     ![Adicionar a condição de evento](media/howto-create-event-rules/Aggregate_Condition_Filled_Out.png)

     >[!NOTE]
     >Mais de uma medida de evento pode ser adicionada em **Condição**. Quando várias condições são especificadas, todas as condições devem ser atendidas para que a regra seja disparada. Cada condição obtém unida por uma cláusula 'AND' implicitamente. Ao usar a agregação, cada medida deve ser agregada.

### <a name="configure-actions"></a>Configurar ações

Esta seção mostra como configurar as ações a serem executadas quando a regra é disparada. Ações são invocadas quando todas as condições especificadas na regra são avaliadas como true.

1. Escolha **+** próximo a **Ações**. Aqui, você vê a lista de ações disponíveis.

    ![Adicionar Ação](media/howto-create-event-rules/Add_Action.png)

1. Escolha a ação **Email**, insira um endereço de email válido no campo **Para** e forneça uma observação para aparecer no corpo do email quando a regra for disparada.

    > [!NOTE]
    > Os emails serão enviados apenas aos usuários que foram adicionados ao aplicativo e fizeram logon pelo menos uma vez. Saiba mais sobre o [gerenciamento de usuários](howto-administer.md) no Azure IoT Central.

   ![Configurar Ação](media/howto-create-event-rules/Configure_Action.png)

1. Para salvar a regra, escolha **Salvar**. A regra entra em ação em alguns minutos e começa a monitorar os eventos enviados ao aplicativo. Quando a condição especificada na regra corresponde, a regra dispara a ação de email configurada.

É possível adicionar outras ações à regra, como o Microsoft Flow e webhooks. Você pode adicionar até 5 ações por regra.

- [Ação do Microsoft Flow](howto-add-microsoft-flow.md) para iniciar um fluxo de trabalho no Microsoft Flow quando uma regra é disparada 
- [Ação do Webhook](howto-create-webhooks.md) para notificar outros serviços quando uma regra é disparada

## <a name="parameterize-the-rule"></a>Parametrizar a regra

As ações também podem ser configuradas usando a **Propriedade de Dispositivo** como um parâmetro. Se um endereço de email for armazenado como uma propriedade de dispositivo, ele poderá ser usado quando você definir o endereço **Para**.

## <a name="delete-a-rule"></a>Excluir uma regra

Se você não precisar mais de uma regra, exclua-a, abrindo a regra e escolhendo **Excluir**. Excluir a regra remove-a do modelo de dispositivo e de todos os dispositivos associados.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Habilitar ou desabilitar uma regra para um modelo de dispositivo

Navegue até o dispositivo e escolha a regra que você deseja habilitar ou desabilitar. Alterne o botão **Habilitar regra para todos os dispositivos deste modelo** para habilitar ou desabilitar a regra para todos os dispositivos associados ao modelo de dispositivo.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Habilitar ou desabilitar uma regra para um dispositivo

Navegue até o dispositivo e escolha a regra que você deseja habilitar ou desabilitar. Alterne o botão **Habilitar regra para este dispositivo** para habilitar ou desabilitar a regra para esse dispositivo.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como criar regras no aplicativo Azure IoT Central, estas são algumas das próximas etapas:

- [Adicionar ação do Microsoft Flow em regras](howto-add-microsoft-flow.md)
- [Adicionar ação de Webhook em regras](howto-create-webhooks.md)
- [Várias ações para executar a partir de uma ou mais regras de grupo](howto-use-action-groups.md)
- [Como gerenciar seus dispositivos](howto-manage-devices.md)
