---
title: Trabalhar com alertas no console de gerenciamento local
description: Use o console de gerenciamento local para obter uma visão empresarial das ameaças recentes em sua rede e entender melhor como os usuários do sensor estão lidando com elas.
ms.date: 12/06/2020
ms.topic: how-to
ms.openlocfilehash: 604650f0cb08eac4a3ab1cfd3fdcbf2e7ff0d19e
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105032133"
---
# <a name="work-with-alerts-on-the-on-premises-management-console"></a>Trabalhar com alertas no console de gerenciamento local 

Você pode fazer o seguinte na janela **alertas** no console de gerenciamento:

- Trabalhar com filtros de alerta

- Trabalhar com contadores de alerta

- Exibir informações de alerta

- Gerenciar eventos de alerta

- Criar regras de exclusão de alerta

- Disparar regras de exclusão de alertas de sistemas externos

- Acelerar o fluxo de trabalho de incidentes com grupos de alertas

## <a name="view-alerts-in-the-on-premises-management-console"></a>Exibir alertas no console de gerenciamento local

O console de gerenciamento local agrega alertas de todos os sensores conectados. Isso fornece uma visão empresarial das ameaças recentes em sua rede e ajuda você a entender melhor como os usuários do sensor estão lidando com eles.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alerts-with-samples.png" alt-text="Captura de tela da janela de alertas.":::

### <a name="work-with-alert-filters"></a>Trabalhar com filtros de alerta

A janela **alertas** exibe os alertas gerados por sensores conectados ao seu console de gerenciamento local. Você pode exibir todos os alertas para sensores conectados ou apresentar os alertas enviados de um específico:

- Site

- Zona

- Dispositivo

- Sensor

Selecione **limpar filtros** para exibir todos os alertas.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/clear-filters.png" alt-text="Limpe os filtros selecionando o botão Limpar Filtros.":::

### <a name="work-with-alert-counters"></a>Trabalhar com contadores de alerta

Os contadores de alerta fornecem uma divisão dos alertas por severidade e o estado confirmado.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/number-of-alerts.png" alt-text="O contador de alertas mostra quantos alertas você tem.":::

Os seguintes níveis de severidade aparecem no contador de alertas:

- **Crítico**: indica um ataque mal-intencionado que deve ser manipulado imediatamente.

- **Principal**: indica uma ameaça de segurança que é importante abordar.

- **Minor**: indica algum desvio do comportamento de linha de base que pode conter uma ameaça de segurança.

- **Aviso**: indica algum desvio do comportamento de linha de base sem ameaças de segurança.

Os níveis de severidade são predefinidos.

Você pode ajustar o contador para fornecer números com base em alertas confirmados e não confirmados. Alertas não confirmados foram disparados em defender para sensores de IoT, mas ainda não foram revisados por operadores no sensor.

Quando a opção **mostrar alertas confirmados** estiver selecionada, todos os alertas confirmados serão exibidos na janela **alertas** .

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/show-acknowledged-alerts.png" alt-text="Exiba seus alertas confirmados.":::

### <a name="view-alert-information"></a>Exibir informações de alerta

O alerta apresenta as seguintes informações:

- Um resumo do evento de alerta.

- Severidade do alerta.

- Um link para o alerta no sensor que o detectou.

- Um UUID de alerta. O UUID consiste na ID de alerta associada ao evento de alerta detectado no sensor, separado por um hífen e seguido por um número de ID de sistema exclusivo.

**UUID de alerta do console de gerenciamento local**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/unauthorized-internet-connectivity.png" alt-text="Um dispositivo está conectado, mas não está autorizado.":::

**ID do alerta do sensor**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/internet-connectivity-alert.png" alt-text="ID do alerta do sensor.":::

Trabalhar com UUIDs garante que cada alerta exibido no console de gerenciamento local seja pesquisável e identificável por um número exclusivo. Isso é necessário porque os alertas gerados de vários sensores podem produzir a mesma ID de alerta.

> [!NOTE]
> Por padrão, os UUIDs são exibidos nos seguintes sistemas de parceiros quando as regras de encaminhamento são definidas: ArcSight, servidores de syslog, QRadar, Sentinela e nettestemunha. Nenhuma configuração é necessária.

Para exibir informações de alerta:

- Na lista de alertas, selecione um alerta.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-information.png" alt-text="Captura de tela das informações de alerta.":::

Para exibir o alerta no sensor:

- Selecione **abrir sensor** do alerta.

Para exibir os dispositivos em um mapa de zona:

- Para exibir o mapa do dispositivo com um foco no dispositivo alertado e todos os dispositivos conectados a ele, selecione **Mostrar dispositivos**.

## <a name="manage-alert-events"></a>Gerenciar eventos de alerta

Várias opções estão disponíveis para o gerenciamento de eventos de alerta do console de gerenciamento local.

- Aprenda ou confirme eventos de alerta. Selecione **saiba & reconhecer** para aprender todos os eventos de alerta que podem ser autorizados e para reconhecer todos os eventos de alerta que não são confirmados no momento.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/learn-and-acknowledge.png" alt-text="Selecione saiba & reconhecer para aprender tudo.":::

- Eventos de alerta sem áudio e mudo.

Para saber mais sobre aprendizado, reconhecimento e mudo de eventos de alerta, consulte o artigo sensor [gerenciar eventos de alerta](how-to-manage-the-alert-event.md) .

## <a name="export-alert-information"></a>Exportar informações de alerta

Exportar informações de alerta para um arquivo. csv. Você pode exportar informações de todos os alertas detectados ou exportar informações com base na exibição filtrada. As seguintes informações são exportadas:

- Endereço de origem
- Endereço de destino
- Título do alerta
- Severidade do alerta
- Mensagem de alerta
- Informações adicionais
- Status confirmado
- Disponibilidade do PCAP

Para exportar:

1. Selecione alertas no menu lateral.
1. Selecione Exportar.
1. Selecione exportar alertas estendidos para exportar informações de alerta em linhas separadas para cada alerta que abrange vários dispositivos. Quando exportar alertas estendidos estiver selecionado, o arquivo. csv criará uma linha duplicada do alerta com os itens exclusivos em cada linha. Usar essa opção torna mais fácil investigar eventos de alerta exportados.  

## <a name="create-alert-exclusion-rules"></a>Criar regras de exclusão de alerta

Instrua o defender para IoT a ignorar gatilhos de alerta com base em:

- Fusos horários e períodos de tempo

- Endereço do dispositivo (IP, MAC, sub-rede)

- Nome do alerta

- Um sensor específico

Crie regras de exclusão de alertas quando desejar que o defender para IoT ignore a atividade que irá disparar um alerta.

Por exemplo, se você souber que todos os dispositivos de OT monitorados por um sensor específico passarão por procedimentos de manutenção por dois dias, você poderá definir uma regra de exclusão que instrui o defender para IoT a suprimir alertas detectados por esse sensor durante o período predefinido.

### <a name="alert-exclusion-logic"></a>Lógica de exclusão de alerta

A lógica da regra de alerta é `AND` baseada. Isso significa que um alerta será disparado somente quando todas as condições de regra forem atendidas.

Se uma condição de regra não for definida, a condição incluirá todas as opções. Por exemplo, se você não incluir o nome de um sensor na regra, ele será aplicado a todos os sensores.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-v2.png" alt-text="Captura de tela da exibição criar regra de exclusão.":::

Os resumos de regra aparecem na janela de **regra de exclusão** .

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/exclusion-summary-v2.png" alt-text="Captura de tela da exibição de resumo da regra de exclusão.":::

Além de trabalhar com regras de exclusão, você pode suprimir os alertas desativando-os.

### <a name="create-exclusion-rules"></a>Criar regras de exclusão

Para criar regras de exclusão:

1. No painel esquerdo do console de gerenciamento local, selecione **exclusão de alertas**. Defina uma nova regra de exclusão selecionando o ícone **Adicionar** :::image type="icon" source="media/how-to-work-with-alerts-on-premises-management-console/add-icon.png" border="false"::: no canto superior direito da janela que é aberta. A caixa de diálogo **criar regra de exclusão** é aberta.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-view.png" alt-text="Crie uma exclusão de alerta preenchendo as informações aqui.":::

2. Insira um nome de regra no campo **nome** . O nome não pode conter aspas ( `"` ).

3. Na seção **por fuso horário/período** , insira um período de tempo dentro de um fuso horário específico. Use esse recurso quando uma regra de exclusão for criada para um período de tempo específico em um fuso horário, mas deve ser implementada ao mesmo tempo em outros fusos horários. Por exemplo, talvez seja necessário aplicar uma regra de exclusão entre 8:00 AM e 10:00 em três fusos horários diferentes. Nesse caso, crie três regras de exclusão separadas que usam o mesmo período de tempo e o fuso horário relevante.

4. Selecione **AICIONAR**. Durante o período de exclusão, nenhum alerta é criado nos sensores conectados.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/by-the-time-period.png" alt-text="Captura de tela da exibição por período de tempo.":::

5. Na seção **por endereço de dispositivo** , defina:

  - Endereço IP do dispositivo, endereço MAC ou endereço de sub-rede que você deseja excluir.

  - Direção do tráfego para os dispositivos, a origem e o destino excluídos.

6. Selecione **AICIONAR**.

7. Na seção **título do alerta** , comece a digitar o título do alerta. Na lista suspensa, selecione o título do alerta ou os títulos a serem excluídos.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-title.png" alt-text="Captura de tela da exibição do título por alerta.":::

8. Selecione **AICIONAR**.

9. Na seção **por nome do sensor** , comece a digitar o nome do sensor. Na lista suspensa, selecione o sensor ou sensores que você deseja excluir.

10. Selecione **AICIONAR**.

11. Selecione **SALVAR**. A nova regra aparece na lista de regras.

Você pode suprimir alertas desativando-os ou criando regras de exclusão de alertas. Esta seção descreve os possíveis casos de uso para ambos os recursos.

- **Regra de exclusão**. Escreva uma regra de exclusão quando:

  - Você sabe antecipadamente que deseja excluir o evento do banco de dados. Por exemplo, você sabe que o cenário detectado em um determinado sensor irá disparar alertas irrelevantes. Por exemplo, você estará realizando o trabalho de manutenção no PLCs organizacional em um site específico e deseja suprimir os alertas relacionados ao PLCs para este site.

  - Você deseja que o defender para IoT ignore eventos para um intervalo de tempo específico (para tarefas de manutenção do sistema).

  - Você deseja ignorar eventos em uma sub-rede específica.

  - Você deseja controlar eventos de alerta gerados de vários sensores com uma regra.

  - Você não deseja acompanhar a exclusão do alerta como um evento no log de eventos.

- **Sem áudio**. Ativar mudo de um alerta quando:

  - Os itens que precisam ser mudo não estão planejados. Você não sabe antecipadamente quais eventos serão irrelevantes.

  - Você deseja suprimir o alerta da janela de **alertas** , mas ainda deseja rastreá-lo no log de eventos.

  - Você deseja ignorar eventos em um canal específico.

### <a name="trigger-alert-exclusion-rules-from-external-systems"></a>Disparar regras de exclusão de alertas de sistemas externos

Disparar regras de exclusão de alertas de sistemas externos. Por exemplo, gerencie regras de exclusão de sistemas de tíquetes corporativos ou sistemas que gerenciam processos de manutenção de rede.

Defina os sensores, os mecanismos, a hora de início e a hora de término para aplicar a regra. Para obter mais informações, consulte [defender for IOT API sensor e APIs do console de gerenciamento](references-work-with-defender-for-iot-apis.md).

As regras que você cria usando a API aparecem na janela de **regra de exclusão** como ro.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/edit-exclusion-rule-screen.png" alt-text="Captura de tela da exibição Editar regra de exclusão.":::

## <a name="next-steps"></a>Próximas etapas

[Trabalhe com alertas em seu sensor](how-to-work-with-alerts-on-your-sensor.md).
Examine os [alertas do mecanismo do defender para IOT](alert-engine-messages.md).