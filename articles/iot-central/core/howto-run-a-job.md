---
title: Criar e executar trabalhos em seu aplicativo Azure IoT Central | Microsoft Docs
description: Os trabalhos do Azure IoT Central permitem recursos de gerenciamento de dispositivos em massa, como atualizar propriedades ou executar um comando.
ms.service: iot-central
services: iot-central
author: philmea
ms.author: philmea
ms.date: 11/19/2020
ms.topic: how-to
ms.openlocfilehash: 19d8738790b5634b9de989fa94edac6a542f85f4
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917334"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Criar e executar um trabalho em seu aplicativo de IoT Central do Azure

Você pode usar o Azure IoT Central para gerenciar seus dispositivos conectados em escala por meio de trabalhos. Os trabalhos permitem que você faça atualizações em massa nas propriedades do dispositivo e da nuvem e execute comandos. Este artigo mostra como começar a usar trabalhos em seu próprio aplicativo.

## <a name="create-and-run-a-job"></a>Criar e executar um trabalho

O exemplo a seguir mostra como criar e executar um trabalho para definir o limite de luz para um grupo de dispositivos de gateway logística. Use o assistente de trabalho para criar e executar trabalhos. Você pode salvar um trabalho para ser executado posteriormente.

1. No painel esquerdo, selecione **trabalhos**.

1. Selecione **+ novo trabalho**.

1. Na página **configurar seu trabalho** , insira um nome e uma descrição para identificar o trabalho que você está criando.

1. Selecione o grupo de dispositivos de destino ao qual você deseja que seu trabalho se aplique. Você pode ver quantos dispositivos sua configuração de trabalho aplica abaixo da seleção do seu **grupo de dispositivos** .

1. Escolha **propriedade de nuvem**, **Propriedade** ou **comando** como o **tipo de trabalho**:

    Para configurar um trabalho de **Propriedade** , selecione uma propriedade e defina seu novo valor. Para configurar um trabalho de **comando** , escolha o comando a ser executado. Um trabalho de propriedade pode definir várias propriedades.

    :::image type="content" source="media/howto-run-a-job/configure-job.png" alt-text="Captura de tela que mostra seleções para criar um trabalho de propriedade chamado definir limite claro":::

    Selecione **salvar e sair** para adicionar o trabalho à lista de trabalhos salvos na página **trabalhos** . Posteriormente, você pode retornar a um trabalho na lista de trabalhos salvos.

1. Selecione **Avançar** para ir para a página **Opções de entrega** . A página **Opções de entrega** permite que você defina as opções de entrega para este trabalho: limite de **lotes** e **cancelamento**.

    Os lotes permitem escalonar trabalhos para um grande número de dispositivos. O trabalho é dividido em vários lotes e cada lote contém um subconjunto dos dispositivos. Os lotes são enfileirados e executados em sequência.

    O limite de cancelamento permite cancelar automaticamente um trabalho se o número de erros exceder o limite definido. O limite pode ser aplicado a todos os dispositivos no trabalho ou a lotes individuais.

    :::image type="content" source="media/howto-run-a-job/job-wizard-delivery-options.png" alt-text="Captura de tela da página opções de entrega do assistente de trabalho":::

1. Selecione **Avançar** para ir para a página **agenda** . A página **agenda** permite que você habilite uma agenda para executar o trabalho no futuro:

    Escolha uma opção de recorrência para a agenda. Você pode configurar um trabalho a ser executado:

    * Única
    * Diário
    * Semanal

    Defina uma data e hora de início para um trabalho agendado. A data e a hora são específicas para o fuso horário e não para a hora local do dispositivo.

    Para encerrar um agendamento recorrente, escolha:

    * **Neste dia** , para definir uma data de término para a agenda.
    * **Depois** de definir o número de vezes para executar o trabalho.

    Os trabalhos agendados sempre são executados nos dispositivos em um grupo de dispositivos, mesmo que a associação ao grupo de dispositivos mude ao longo do tempo.

    :::image type="content" source="media/howto-run-a-job/job-wizard-schedule.png" alt-text="Captura de tela da página opções de agenda do assistente de trabalho":::

1. Selecione **Avançar** para ir para a página **revisão** . A página **revisão** mostra os detalhes de configuração do trabalho. Selecione **agendar** para agendar o trabalho:

    :::image type="content" source="media/howto-run-a-job/job-wizard-schedule-review.png" alt-text="Captura de tela da página de revisão do assistente de trabalho agendado":::

1. A página detalhes do trabalho mostra informações sobre os trabalhos agendados. Quando o trabalho agendado for executado, você verá uma lista das instâncias de trabalho. A execução do trabalho agendado também faz parte da última lista de trabalhos de **30 dias** .

    Nessa página, você pode **desagendar** o trabalho ou **Editar** o trabalho agendado. Você pode retornar a um trabalho agendado na lista de trabalhos agendados.

    :::image type="content" source="media/howto-run-a-job/job-schedule-details.png" alt-text="Captura de tela da página de detalhes do trabalho agendado":::

1. No assistente de trabalho, você pode optar por não agendar um trabalho e executá-lo imediatamente. A captura de tela a seguir mostra um trabalho sem uma agenda que está pronta para ser executada imediatamente. Selecione **executar** para executar o trabalho:

    :::image type="content" source="media/howto-run-a-job/job-wizard-schedule-immediate.png" alt-text="Captura de tela da página de revisão do assistente de trabalho":::

1. Um trabalho passa por etapas *pendentes*, *em execução* e *concluídas* . Os detalhes de execução do trabalho contêm métricas de resultado, detalhes de duração e uma grade de lista de dispositivos.

    Quando o trabalho for concluído, você poderá selecionar **log de resultados** para baixar um arquivo CSV dos detalhes do trabalho, incluindo os dispositivos e seus valores de status. Essas informações podem ser úteis para a solução de problemas.

    :::image type="content" source="media/howto-run-a-job/download-details.png" alt-text="Captura de tela que mostra o status do dispositivo":::

1. O trabalho agora aparece na lista **últimos 30 dias** na página **trabalhos** . Esta página mostra os trabalhos atualmente em execução e o histórico de todos os trabalhos executados ou salvos anteriormente.

    > [!NOTE]
    > Você pode exibir 30 dias de histórico para os trabalhos executados anteriormente.

## <a name="manage-jobs"></a>Gerenciar trabalhos

Para interromper um trabalho em execução, abra-o e selecione **parar**. O status do trabalho é alterado para refletir que o trabalho foi interrompido. A seção de **Resumo** mostra quais dispositivos foram concluídos, falharam ou ainda estão pendentes.

:::image type="content" source="media/howto-run-a-job/manage-job.png" alt-text="Captura de tela que mostra um trabalho em execução e o botão para parar um trabalho":::

Quando um trabalho está em um estado parado, você pode selecionar **continuar** para retomar a execução do trabalho. O status do trabalho é alterado para refletir que o trabalho agora está em execução novamente. A seção de **Resumo** continua a ser atualizada com o progresso mais recente.

:::image type="content" source="media/howto-run-a-job/stopped-job.png" alt-text="Captura de tela que mostra um trabalho parado e o botão para continuar um trabalho":::

## <a name="copy-a-job"></a>Copiar um trabalho

Para copiar um trabalho existente, selecione um trabalho executado. Selecione **copiar** na página de resultados do trabalho ou na página de detalhes de trabalhos:

:::image type="content" source="media/howto-run-a-job/job-details-copy.png" alt-text="Captura de tela que mostra o botão de cópia":::

Uma cópia da configuração do trabalho é aberta para você editar e a **cópia** é anexada ao nome do trabalho.

## <a name="view-job-status"></a>Exibir status do trabalho

Depois que um trabalho é criado, a coluna **status** é atualizada com a mensagem de status de trabalho mais recente. A tabela a seguir lista os possíveis valores de *status de trabalho* :

| Mensagem de status       | Significado do status                                          |
| -------------------- | ------------------------------------------------------- |
| Concluído            | Esse trabalho foi executado em todos os dispositivos.              |
| Com falha               | Esse trabalho falhou e não foi totalmente executado nos dispositivos.  |
| Pendente              | Esse trabalho ainda não começou a ser executado em dispositivos.         |
| Executando              | Este trabalho está sendo executado atualmente em dispositivos.             |
| Parado              | Um usuário interrompeu manualmente este trabalho.           |
| Canceled             | Este trabalho foi cancelado porque o limite definido na página **Opções de entrega** foi excedido. |

A mensagem de status é seguida por uma visão geral dos dispositivos no trabalho. A tabela a seguir lista os possíveis valores de *status do dispositivo* :

| Mensagem de status       | Significado do status                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Com sucesso            | O número de dispositivos em que o trabalho foi executado com êxito.       |
| Com falha               | O número de dispositivos em que o trabalho falhou em execução.       |

Para exibir o status do trabalho e de todos os dispositivos afetados, abra o trabalho. Ao lado de cada nome de dispositivo, você verá uma das seguintes mensagens de status:

| Mensagem de status       | Significado do status                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Concluído            | O trabalho foi executado neste dispositivo.                                     |
| Com falha               | Falha ao executar o trabalho neste dispositivo. A mensagem de erro mostra mais informações.  |
| Pendente              | O trabalho ainda não foi executado neste dispositivo.                                   |

Para baixar um arquivo CSV que inclui os detalhes do trabalho e a lista de dispositivos e seus valores de status, selecione **log de resultados**.

## <a name="filter-the-device-list"></a>Filtrar a lista de dispositivos

Você pode filtrar a lista de dispositivos na página de **detalhes do trabalho** selecionando o ícone de filtro. Você pode filtrar o campo **ID do dispositivo** ou **status** :

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Captura de tela que mostra seleções para filtrar uma lista de dispositivos.":::

## <a name="customize-columns-in-the-device-list"></a>Personalizar colunas na lista de dispositivos

Você pode adicionar colunas à lista de dispositivos selecionando o ícone de opções de coluna:

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Captura de tela que mostra o ícone para as opções de coluna.":::

Use a caixa de diálogo **Opções de coluna** para escolher as colunas da lista de dispositivos. Selecione as colunas que você deseja exibir, selecione a seta para a direita e, em seguida, selecione **OK**. Para selecionar todas as colunas disponíveis, escolha **selecionar tudo**. As colunas selecionadas aparecem na lista de dispositivos.

As colunas selecionadas persistem em uma sessão de usuário ou em sessões de usuário que têm acesso ao aplicativo.

## <a name="rerun-jobs"></a>Executar trabalhos novamente

Você pode executar novamente um trabalho que tenha dispositivos com falha. Selecione **executar novamente em com falha**:

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Captura de tela que mostra o botão para executar novamente um trabalho em dispositivos com falha.":::

Insira um nome de trabalho e uma descrição e, em seguida, selecione **executar novamente o trabalho**. Um novo trabalho é enviado para repetir a ação em dispositivos com falha.

> [!NOTE]
> Não é possível executar mais de cinco trabalhos ao mesmo tempo de um aplicativo de IoT Central do Azure.
>
> Quando um trabalho é concluído e você exclui um dispositivo que está na lista de dispositivos do trabalho, a entrada do dispositivo aparece como excluído no nome do dispositivo. O link detalhes não está disponível para o dispositivo excluído.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como criar trabalhos em seu aplicativo IoT Central do Azure, aqui estão algumas próximas etapas:

- [Gerenciar seus dispositivos](howto-manage-devices.md)
- [Versão de seu modelo de dispositivo](howto-version-device-template.md)
