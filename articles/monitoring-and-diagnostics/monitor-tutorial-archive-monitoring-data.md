---
title: Arquivar dados de monitoramento do Azure | Microsoft Docs
description: "Arquive dados de log e de métrica produzidos no Azure em uma conta de armazenamento."
author: johnkemnetz
manager: orenr
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: johnkem
ms.custom: mvc
ms.openlocfilehash: 445901a740920a74f259aaa9c6b862680c1c807e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="archive-azure-monitoring-data"></a>Arquivar dados de monitoramento do Azure

Várias camadas do ambiente do Azure produzem dados de log e de métrica que podem ser arquivados em uma Conta de Armazenamento do Azure. Talvez você deseje fazer isso para preservar um histórico dos dados de monitoramento ao longo do tempo em um repositório de baixo custo e não pesquisável depois que os dados passaram seu período de retenção no Log Analytics ou no Azure Monitor. Este tutorial executa em etapas o processo de configuração do ambiente do Azure para arquivar dados em uma conta de armazenamento.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entrar no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Primeiro, você precisa configurar uma conta de armazenamento na qual os dados de monitoramento serão arquivados. Para fazer isso, [siga as etapas apresentadas aqui](../storage/common/storage-create-storage-account.md).

## <a name="route-subscription-logs-to-the-storage-account"></a>Encaminhar os logs de assinatura para a conta de armazenamento

Agora você está pronto para começar a configurar o ambiente do Azure para encaminhar os dados de monitoramento para uma conta de armazenamento. Primeiro, configuramos os dados no nível da assinatura (contidos no Log de Atividades do Azure) para que sejam encaminhados para a conta de armazenamento. O [**Log de Atividades do Azure**](monitoring-overview-activity-logs.md) fornece um histórico de eventos no nível da assinatura no Azure. Procure-o no portal do Azure para determinar *quem* criou, atualizou ou excluiu *quais* recursos e *quando* fizeram isso.

1. Clique no botão **Monitorar** encontrado na lista de navegação à esquerda e, em seguida, no **Log de Atividades**.

   ![Seção Log de Atividades](media/monitor-tutorial-archive-monitoring-data/activity-log-home.png)

2. Na seção Log de Atividades exibida, clique no botão **Exportar**.

3. Na seção **Exportar log de atividades** exibida, marque a caixa de **Exportar para uma conta de armazenamento** e clique em **Selecionar uma conta de armazenamento.**

   ![Exportação do Log de Atividades](media/monitor-tutorial-archive-monitoring-data/activity-log-export.png)

4. Na seção exibida, use a lista suspensa **Conta de armazenamento** para selecionar o nome da conta de armazenamento criada na etapa anterior **Criar uma conta de armazenamento** e, em seguida, clique em **OK**.

   ![Selecionar uma conta de armazenamento](media/monitor-tutorial-archive-monitoring-data/activity-log-storage.png)

5. Defina o controle deslizante **Retenção (dias)** como 30. Esse controle deslizante define um número de dias para reter os dados de monitoramento na conta de armazenamento. O Azure Monitor exclui automaticamente os dados mais antigos do que o número de dias especificado. Uma retenção de zero dias armazena os dados por tempo indeterminado.

6. Clique em **Salvar** e feche esta seção.

Os dados de monitoramento de sua assinatura agora estão fluindo para a conta de armazenamento.

## <a name="route-resource-data-to-the-storage-account"></a>Encaminhar os dados do recurso para a conta de armazenamento

Agora configuramos os dados no nível do recurso (logs de diagnóstico e métricas do recurso) para que sejam encaminhados para a conta de armazenamento definindo as **configurações de diagnóstico do recurso**.

1. Clique no botão **Monitorar** encontrado na lista de navegação à esquerda e, em seguida, em **Configurações de Diagnóstico**. Veja aqui uma lista de todos os recursos em sua assinatura que produzem dados de monitoramento por meio do Azure Monitor. Caso não tenha todos os recursos nessa lista, [crie um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md) antes de continuar, de modo que você tenha um recurso no qual possa definir uma configuração de diagnóstico.

2. Clique em um recurso na lista e, em seguida, clique em **Ativar diagnóstico**.
   
   ![Ativar diagnóstico](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-turn-on.png)

   Se já houver uma configuração definida, consulte as configurações existentes e um botão para **Adicionar configuração de diagnóstico**. Clique neste botão.

   Uma configuração de diagnóstico de recurso é uma definição de *quais* dados de monitoramento devem ser encaminhados de um recurso específico e para *onde* os dados de monitoramento devem ir.

3. Na seção exibida, dê à configuração um **nome** e marque a caixa de **Arquivar em uma conta de armazenamento**.

   ![Seção Configurações de diagnóstico](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-home.png)

4. Clique no botão **Configurar** em **Arquivar em uma conta de armazenamento** e selecione a conta de armazenamento criada na seção anterior. Clique em **OK**.

   ![Conta de armazenamento das configurações de diagnóstico](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-storage.png)

5. Marque todas as caixas em **Log** e **Métrica**. Dependendo do tipo de recurso, você poderá ter somente uma dessas opções. Essas caixas de seleção controlam quais categorias de dados de log e métrica disponíveis para o tipo de recurso são enviadas para o destino selecionado, nesse caso, uma conta de armazenamento.

   ![Categorias das configurações de diagnóstico](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-categories.png)
   
6. Defina o controle deslizante **Retenção (dias)** como 30. Esse controle deslizante define um número de dias para reter os dados de monitoramento na conta de armazenamento. O Azure Monitor exclui automaticamente os dados mais antigos do que o número de dias especificado. Uma retenção de zero dias armazena os dados por tempo indeterminado.

7. Clique em **Salvar**.

Os dados de monitoramento de seu recurso agora estão fluindo para a conta de armazenamento.

## <a name="route-virtual-machine-guest-os-data-to-the-storage-account"></a>Encaminhar os dados da máquina virtual (SO convidado) para a conta de armazenamento

1. Caso você ainda não tenha uma máquina virtual em sua assinatura, [crie uma máquina virtual](../virtual-machines/windows/quick-create-portal.md).

2. Na lista de navegação à esquerda no portal, clique em **Máquinas Virtuais**.

3. Na lista de máquinas virtuais exibida, clique na máquina virtual criada.

4. Na seção exibida, clique em **Configurações de Diagnóstico** na barra de navegação à esquerda. Essa seção permite configurar a extensão de monitoramento pronta para uso por meio do Azure Monitor na máquina virtual e encaminhar os dados produzidos pelo Windows ou Linux para uma conta de armazenamento.

   ![Navegar para as configurações de diagnóstico](media/monitor-tutorial-archive-monitoring-data/guest-navigation.png)

5. Clique em **Habilitar o monitoramento no nível do convidado** na seção exibida.

   ![Habilitar configurações de diagnóstico](media/monitor-tutorial-archive-monitoring-data/guest-enable.png)

6. Depois que a configuração de diagnóstico for salva corretamente, a guia **Visão Geral** mostrará uma lista dos dados coletados e onde eles estão sendo armazenados. Clique na seção **Contadores de desempenho** para examinar o conjunto de contadores de desempenho do Windows coletados.

   ![Configurações de contadores de desempenho](media/monitor-tutorial-archive-monitoring-data/guest-perf-counters.png)
   
7. Clique na guia **Logs** e marque as caixas de seleção dos logs no nível **Informações** nos logs do Aplicativo e do Sistema.

   ![Configurações de logs](media/monitor-tutorial-archive-monitoring-data/guest-logs.png)

8. Clique na guia **Agente** e, em **Conta de armazenamento**, clique no nome da conta de armazenamento mostrada.

   ![Atualizar a conta de armazenamento](media/monitor-tutorial-archive-monitoring-data/guest-storage-home.png)

9. Na seção exibida, selecione a conta de armazenamento criada na etapa anterior **Criar uma conta de armazenamento**.

10. Clique em **Salvar**.

Os dados de monitoramento de suas máquinas virtuais agora estão fluindo para a conta de armazenamento.

## <a name="view-the-monitoring-data-in-the-storage-account"></a>Exibir os dados de monitoramento na conta de armazenamento

Se você seguiu as etapas anteriores, os dados começaram a fluir para sua conta de armazenamento.

1. Para alguns tipos de dados, por exemplo, o Log de Atividades, deve haver uma atividade que gera um evento na conta de armazenamento. Para gerar a atividade no Log de Atividades, siga [estas instruções](./monitor-quick-audit-notify-action-in-subscription.md). Talvez seja necessário aguardar até cinco minutos antes que o evento seja exibido na conta de armazenamento.

2. No portal, navegue para a seção **Contas de Armazenamento** localizando-a na barra de navegação à esquerda.

3. Identifique a conta de armazenamento criada na seção anterior e clique nela.

4. Clique em **Blobs**, em seguida, no contêiner rotulado **insights-operational-logs** e, finalmente, no contêiner rotulado **name=default**. Esse é o contêiner que contém o Log de Atividades. Os dados de monitoramento são divididos em contêineres por ID de recurso (apenas a ID de assinatura do Log de Atividades) e, em seguida, por data e hora. O formato completo desses blobs é:

   insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{ID da assinatura}/y={ano de quatro dígitos}/m={mês numérico de dois dígitos}/d={dia numérico de dois dígitos}/h={hora de relógio de 24 horas de dois dígitos}/m=00/PT1H.json

5. Navegue para o arquivo PT1H.json clicando nos contêineres, em ID de recurso, data e hora. Clique no arquivo PT1H.json e clique em **Baixar**. Cada blob PT1H.json contém um blob JSON de eventos que ocorreram dentro de uma hora especificada na URL do blob (por exemplo, h=12). Durante a hora presente, os eventos são acrescentados ao arquivo PT1H.json conforme eles ocorrem. O valor de minuto (m=00) é sempre 00, pois os eventos de log são divididos em blobs individuais por hora.

   Agora você pode exibir o evento do JSON armazenado na conta de armazenamento. Para os logs de diagnóstico de recurso, o formato dos blobs é:

   insights-logs-{log category name}/resourceId=/{resource ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json

6. Os dados de monitoramento do SO convidado são armazenados em tabelas. Navegue de volta para a página inicial da conta de armazenamento e, em seguida, clique em **Tabelas**. Há tabelas de métricas, contadores de desempenho e logs de eventos.

Agora você configurou com êxito os dados de monitoramento a serem arquivados em uma conta de armazenamento.

## <a name="clean-up-resources"></a>Limpar recursos

1. Navegue de volta para a seção **Exportar Log de Atividades** da etapa anterior **Encaminhar os logs de assinatura para a conta de armazenamento** e, em seguida, clique em **Redefinir**.

2. Navegue para a seção **Configurações de Diagnóstico**, clique no recurso no qual você criou uma configuração de diagnóstico na etapa anterior **Encaminhar os dados do recurso para a conta de armazenamento**, em seguida, localize a configuração criada, clique no botão **Editar configuração** e clique em **Excluir**.

3. Navegue para a seção **Configurações de Diagnóstico** na máquina virtual configurada na etapa anterior **Encaminhar os dados da máquina virtual (SO convidado) para a conta de armazenamento** e, na guia **Agente**, clique em **Remover** (abaixo da seção **Remover o agente de Diagnóstico do Azure**).

4. Navegue para a conta de armazenamento criada na etapa anterior **Criar uma conta de armazenamento** e clique em **Excluir conta de armazenamento**. Digite o nome da conta de armazenamento e, em seguida, clique em **Excluir**.

5. Se você criou uma máquina virtual ou um Aplicativo Lógico nas etapas anteriores, exclua-os também.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a configurar os dados de monitoramento do ambiente do Azure (assinatura, recurso e SO convidado) a serem arquivados em uma conta de armazenamento. Para tornar seus dados mais significativos e obter insights deles, tente enviá-los para o Log Analytics também.

> [!div class="nextstepaction"]
> [Introdução ao Log Analytics](../log-analytics/log-analytics-get-started.md)
