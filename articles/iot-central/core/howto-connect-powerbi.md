---
title: Visualize seus dados do Azure IoT Central em um painel do Power BI | Microsoft Docs
description: Use a solução do Power BI para Azure IoT Central para visualizar e analisar seus dados da IoT Central.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/4/2019
ms.topic: conceptual
ms.openlocfilehash: ea4a47f1ba3eac39820e839a10330840f57afe42
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629063"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Visualize e analise seus dados do Azure IoT Central em um painel do Power BI

*Este tópico se aplica a administradores e desenvolvedores de soluções.*

> [!Note] 
> Esta solução usa [recursos de exportação de dados herdados](./howto-export-data-legacy.md). Fique atento a diretrizes atualizadas sobre como se conectar a Power BI usando a exportação de dados mais recente.

:::image type="content" source="media/howto-connect-powerbi/iot-continuous-data-export.png" alt-text="Pipeline de solução do Power BI":::

Use a solução Power BI para o Azure IoT Central V3 para criar um painel de Power BI poderoso para monitorar o desempenho de seus dispositivos IoT. No seu painel do Power BI, você pode:

- Acompanhe quantos dados seus dispositivos estão enviando ao longo do tempo
- Comparar volumes de dados entre fluxos de telemetria diferentes
- Filtrar para dados enviados por dispositivos específicos
- Exibir os dados de telemetria mais recentes em uma tabela

Esta solução configura um pipeline que lê dados de sua conta de armazenamento de BLOBs do Azure de [exportação de dados herdados](./howto-export-data-legacy.md) . O pipeline usa Azure Functions, o Azure Data Factory e o banco de dados SQL do Azure para processar e transformar os dados. Você pode visualizar e analisar os dados em um Power BI relatório que você baixa como um arquivo PBIX. Todos os recursos são criados em sua assinatura do Azure, para que você possa personalizar cada componente para atender às suas necessidades.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste guia de instruções, é necessário ter uma assinatura ativa do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

A configuração da solução requer os seguintes recursos:

- Um aplicativo IoT Central versão 3. Para saber como verificar a versão do aplicativo, consulte [sobre seu aplicativo](./howto-get-app-info.md). Para saber como criar um aplicativo IoT Central, consulte [criar um aplicativo de IOT central do Azure](./quick-deploy-iot-central.md).
- Exportação de dados contínua herdada que é configurada para exportar telemetria, dispositivos e modelos de dispositivo para o armazenamento de BLOBs do Azure. Para saber mais, consulte [documentação de exportação de dados herdados](howto-export-data-legacy.md).
  - Certifique-se de que apenas seu aplicativo IoT Central está exportando dados para o contêiner de BLOB.
  - Seus [dispositivos devem enviar mensagens codificadas em JSON](../../iot-hub/iot-hub-devguide-messages-d2c.md). Os dispositivos devem especificar `contentType:application/JSON` e `contentEncoding:utf-8` ou `contentEncoding:utf-16` ou `contentEncoding:utf-32` nas propriedades do sistema de mensagens.
- Power BI Desktop (versão mais recente). Consulte [Power bi downloads](https://powerbi.microsoft.com/downloads/).
- Power BI Pro (se você quiser compartilhar o painel com outras pessoas).

> [!NOTE]
> Se você estiver usando um aplicativo IoT Central versão 2, consulte [Visualizar e analisar seus dados do Azure IOT central em um painel Power bi](/previous-versions/azure/iot-central/core/howto-connect-powerbi) no site de documentação das versões anteriores.

## <a name="install"></a>Instalar

Para configurar o pipeline, navegue até a página [Power bi Solution para o Azure IOT central v3](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) no site do **Microsoft AppSource** . Selecione **obter agora** e siga as instruções.

Ao abrir o arquivo PBIX, certifique-se de ler e siga as instruções na página de rosto. Estas instruções descrevem como conectar seu relatório ao banco de dados SQL.

## <a name="report"></a>Relatório

O arquivo PBIX contém o relatório de **dispositivos e telemetria** mostra uma exibição histórica da telemetria que foi enviada por dispositivos. Ele fornece uma análise dos diferentes tipos de telemetria e também mostra a telemetria mais recente enviada pelos dispositivos.

:::image type="content" source="media/howto-connect-powerbi/report.png" alt-text="Power BI relatório de dispositivos e telemetria":::

## <a name="pipeline-resources"></a>Recursos de pipeline

Você pode acessar todos os recursos do Azure que compõem o pipeline no portal do Azure. Todos os recursos estão no grupo de recursos que você criou quando configurou o pipeline.

:::image type="content" source="media/howto-connect-powerbi/azure-deployment.png" alt-text="portal do Azure exibição do grupo de recursos":::

A lista a seguir descreve a função de cada recurso no pipeline:

### <a name="azure-functions"></a>Funções do Azure

O aplicativo de funções do Azure é disparado cada vez que IoT Central grava um novo arquivo no armazenamento de BLOBs. As funções extraem dados dos BLOBs de telemetria, dispositivos e modelos de dispositivo para preencher as tabelas intermediárias do SQL que Azure Data Factory usa.

### <a name="azure-data-factory"></a>Fábrica de dados do Azure

Azure Data Factory se conecta ao banco de dados SQL como um serviço vinculado. Ele executa procedimentos armazenados para processar os dados e armazená-los nas tabelas de análise.

Azure Data Factory é executado a cada 15 minutos para transformar o lote de dados mais recente para carregar nas tabelas SQL (que é o número mínimo atual para o **gatilho de janela em cascata**).

### <a name="azure-sql-database"></a>Banco de Dados SQL do Azure

Azure Data Factory gera um conjunto de tabelas de análise para Power BI. Você pode explorar esses esquemas em Power BI e usá-los para criar suas próprias visualizações.

## <a name="estimated-costs"></a>Custos estimados

A página [Power bi solução para o Azure IOT central v3](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) no site Microsoft AppSource inclui um link para um avaliador de custo para os recursos que você implanta.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a visualizar seus dados em Power BI, a próxima etapa sugerida é aprender a [gerenciar dispositivos](howto-manage-devices.md).
