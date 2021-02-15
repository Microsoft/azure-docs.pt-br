---
title: Usar fluxos de trabalho para integrar o aplicativo Azure IoT Central a outros serviços em nuvem | Microsoft Docs
description: Este artigo de instruções mostra como configurar regras e ações que integram o aplicativo IoT Central a outros serviços em nuvem. Para criar uma regra avançada, use um conector de IoT Central no Power Automate ou nos Aplicativos Lógicos do Azure.
author: dominicbetts
ms.author: dobett
ms.date: 05/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 257855b4f7b1fae56ed8d6a063acfb0588da9b6a
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92123313"
---
# <a name="use-workflows-to-integrate-your-azure-iot-central-application-with-other-cloud-services"></a>Usar fluxos de trabalho para integrar o aplicativo Azure IoT Central a outros serviços em nuvem

*Este artigo se aplica a construtores de solução.*

Você pode criar regras no IoT Central que acionam ações, como enviar um email, em resposta a condições baseadas em telemetria, como a temperatura do dispositivo que excede um limite.

O conector do Azure IoT Central V3 para a automação de energia e o aplicativo lógico do Azure permite que você crie regras mais avançadas para automatizar operações no IoT Central:

- Quando uma regra é acionada no aplicativo Azure IoT Central, ela pode acionar um fluxo de trabalho no Power Automate ou nos Aplicativos Lógicos do Azure. Esses fluxos de trabalho podem executar ações em outros serviços de nuvem, como Microsoft 365 ou um serviço de terceiros.
- Um evento em outro serviço de nuvem, como Microsoft 365, pode disparar um fluxo de trabalho no Power Automate ou no aplicativo lógico do Azure. Esses fluxos de trabalho podem executar ações ou recuperar dados do aplicativo IoT Central.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste guia de instruções, é necessário ter uma assinatura ativa do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

A configuração da solução requer um aplicativo de IoT Central da versão 3. Para saber como verificar a versão do aplicativo, consulte [sobre seu aplicativo](./howto-get-app-info.md). Para saber como criar um aplicativo IoT Central, consulte [criar um aplicativo de IOT central do Azure](./quick-deploy-iot-central.md).

> [!NOTE]
> Se você estiver usando um aplicativo de IoT Central de versão 2, consulte [criar fluxos de trabalho com o conector de IOT central em aplicativos lógicos do Azure](/previous-versions/azure/iot-central/core/howto-build-azure-logic-apps) no site de documentação de versões anteriores e usar o conector do Azure IOT central v2

## <a name="trigger-a-workflow-from-a-rule"></a>Acionar um fluxo de trabalho de uma regra

Antes que possa acionar um fluxo de trabalho no Power Automate ou nos Aplicativos Lógicos do Azure, você precisa de uma regra no aplicativo IoT Central. Para saber mais, confira [Configurar regras e ações no Azure IoT Central](./howto-configure-rules.md).

Para adicionar o conector **do Azure IOT central v3-Preview** como um gatilho na automação de energia:

1. No Power Automate, selecione **+ Criar** e depois selecione a guia **Personalizar**.
1. Pesquise *IOT central*e selecione o conector **do Azure IOT central v3-Preview** .
1. Na lista de gatilhos de evento, selecione **Quando uma regra é acionada (versão prévia)** .
1. Na etapa **Quando uma regra é acionada**, selecione o aplicativo IoT Central e a regra que você está usando.

Para adicionar o conector **do Azure IOT central v3-Preview** como um gatilho em aplicativos lógicos do Azure:

1. Em **Designer de Aplicativos Lógicos**, selecione o modelo **Aplicativo lógico em branco**.
1. No designer, selecione a guia **Personalizar**.
1. Pesquise *IOT central*e selecione o conector **do Azure IOT central v3-Preview** .
1. Na lista de gatilhos de evento, selecione **Quando uma regra é acionada (versão prévia)** .
1. Na etapa **Quando uma regra é acionada**, selecione o aplicativo IoT Central e a regra que você está usando.

:::image type="content" source="./media/howto-configure-rules-advanced/triggers.png" alt-text="Encontre o conector do Azure IoT Central - versão prévia e escolha o gatilho de evento":::

Agora você pode adicionar mais etapas ao fluxo de trabalho para criar o cenário de integração.

## <a name="run-an-action"></a>Executar uma ação

Você pode executar ações em um aplicativo IoT Central nos fluxos de trabalho do Power Automate e dos Aplicativos Lógicos do Azure. Primeiro, crie o fluxo de trabalho e use um conector para definir um gatilho de evento para iniciar o fluxo de trabalho. Em seguida, use o conector **do Azure IOT central v3-Preview** como uma ação.

Para adicionar o conector **do Azure IOT central v3-Preview** como uma ação na automação de energia:

1. No Power Automate, no painel **Escolher uma ação**, selecione a guia **Personalizar**.
1. Pesquise *IOT central* e selecione o conector **do Azure IOT central v3-Preview** .
1. Na lista de ações, escolha a ação IoT Central que você deseja usar.
1. Na etapa de ação, conclua a configuração para a ação escolhida. Em seguida, selecione **Salvar**.

Para adicionar o conector **do Azure IOT central v3-Preview** como uma ação nos aplicativos lógicos do Azure:

1. Nos **Aplicativos Lógicos do Azure**, no painel **Escolher uma ação**, selecione a guia **Personalizar**.
1. Pesquise *IOT central*e selecione o conector **do Azure IOT central v3-Preview** .
1. Na lista de ações, escolha a ação IoT Central que você deseja usar.
1. Na etapa de ação, conclua a configuração para a ação escolhida. Em seguida, selecione **Salvar**.

:::image type="content" source="./media/howto-configure-rules-advanced/actions.png" alt-text="Encontre o conector do Azure IoT Central - versão prévia e escolha o gatilho de evento":::

## <a name="list-of-actions"></a>Lista de ações

A lista a seguir mostra todas as ações de IoT Central disponíveis no conector **do Azure IOT central v3-Preview** e suas opções de configuração. O conteúdo de muitos dos campos pode ser gerado dinamicamente. Por exemplo, uma etapa anterior pode determinar a ID do dispositivo em que a etapa atual atua.

### <a name="create-or-update-a-device"></a>Criar ou atualizar um dispositivo

Use esta ação para criar ou atualizar um dispositivo no aplicativo IoT Central.

| Campo | Descrição |
| ----- | ----------- |
| Aplicativo | Escolha na lista de aplicativos IoT Central. |
| Dispositivo | A ID exclusiva do dispositivo a ser criado ou atualizado. |
| Aprovado | Escolha se o dispositivo foi aprovado para se conectar ao IoT Central. |
| Descrição do dispositivo | Uma descrição detalhada do dispositivo. |
| Nome do dispositivo | O nome de exibição do dispositivo. |
| Modelo de dispositivo | Escolha na lista de modelos de dispositivo no aplicativo IoT Central. |
| Simulado | Escolha se o dispositivo é simulado. |

### <a name="delete-a-device"></a>Excluir um dispositivo

Use esta ação para excluir um dispositivo no aplicativo IoT Central.

| Campo | Descrição |
| ----- | ----------- |
| Aplicativo | Escolha na lista de aplicativos IoT Central. |
| Dispositivo | A ID exclusiva do dispositivo a ser excluído. |

### <a name="execute-a-device-command"></a>Executar um comando de dispositivo

Use esta ação para executar um comando definido em uma das interfaces do dispositivo.

| Campo | Descrição |
| ----- | ----------- |
| Aplicativo | Escolha na lista de aplicativos IoT Central. |
| Dispositivo | A ID exclusiva do dispositivo a ser excluído. |
| Componente do dispositivo | A interface no modelo de dispositivo que contém o comando. |
| Comando do dispositivo | Escolha um dos comandos na interface selecionada. |
| Modelo de dispositivo | Escolha na lista de modelos de dispositivo no aplicativo IoT Central. |
| Carga de solicitação de comando do dispositivo | Se o comando exigir uma carga de solicitação, adicione-a aqui.  |

> [!NOTE]
> Não é possível escolher um componente de dispositivo até ter escolhido um modelo de dispositivo.

### <a name="get-a-device-by-id"></a>Obter um dispositivo por ID

Use esta ação para recuperar os detalhes do dispositivo.

| Campo | Descrição |
| ----- | ----------- |
| Aplicativo | Escolha na lista de aplicativos IoT Central. |
| Dispositivo | A ID exclusiva do dispositivo a ser excluído. |

Você pode usar os detalhes retornados nas expressões dinâmicas em outras ações. Os detalhes do dispositivo retornados incluem: **Aprovado**, **Corpo**, **Descrição do dispositivo**, **Nome do dispositivo**, **Modelo de dispositivo**, **Provisionado** e **Simulado**.

### <a name="get-device-cloud-properties"></a>Obter propriedades de nuvem do dispositivo

Use esta ação para recuperar os valores de propriedade de nuvem de um dispositivo específico.

| Campo | Descrição |
| ----- | ----------- |
| Aplicativo | Escolha na lista de aplicativos IoT Central. |
| Dispositivo | A ID exclusiva do dispositivo a ser excluído. |
| Modelo de dispositivo | Escolha na lista de modelos de dispositivo no aplicativo IoT Central. |

Você pode usar os valores de propriedade de nuvem retornados nas expressões dinâmicas em outras ações.

### <a name="get-device-properties"></a>Obter propriedades do dispositivo

Use esta ação para recuperar os valores de propriedade de um dispositivo específico.

| Campo | Descrição |
| ----- | ----------- |
| Aplicativo | Escolha na lista de aplicativos IoT Central. |
| Dispositivo | A ID exclusiva do dispositivo a ser excluído. |
| Modelo de dispositivo | Escolha na lista de modelos de dispositivo no aplicativo IoT Central. |

Você pode usar os valores de propriedade retornados nas expressões dinâmicas em outras ações.

### <a name="get-device-telemetry-value"></a>Obter valor de telemetria do dispositivo

Use esta ação para recuperar os valores de telemetria de um dispositivo específico.

| Campo | Descrição |
| ----- | ----------- |
| Aplicativo | Escolha na lista de aplicativos IoT Central. |
| Dispositivo | A ID exclusiva do dispositivo a ser excluído. |
| Modelo de dispositivo | Escolha na lista de modelos de dispositivo no aplicativo IoT Central. |

Você pode usar os valores de telemetria retornados nas expressões dinâmicas em outras ações.

### <a name="update-device-cloud-properties"></a>Atualizar propriedades de nuvem do dispositivo

Use esta ação para atualizar os valores de propriedade de nuvem de um dispositivo específico.

| Campo | Descrição |
| ----- | ----------- |
| Aplicativo | Escolha na lista de aplicativos IoT Central. |
| Dispositivo | A ID exclusiva do dispositivo a ser excluído. |
| Modelo de dispositivo | Escolha na lista de modelos de dispositivo no aplicativo IoT Central. |
| Propriedades da Nuvem | Depois de escolher um modelo de dispositivo, um campo é adicionado para cada propriedade de nuvem definida no modelo. |

### <a name="update-device-properties"></a>Atualizar propriedades do dispositivo

Use esta ação para atualizar os valores de propriedade gravável de um dispositivo específico.

| Campo | Descrição |
| ----- | ----------- |
| Aplicativo | Escolha na lista de aplicativos IoT Central. |
| Dispositivo | A ID exclusiva do dispositivo a ser excluído. |
| Modelo de dispositivo | Escolha na lista de modelos de dispositivo no aplicativo IoT Central. |
| Propriedades graváveis | Depois de escolher um modelo de dispositivo, um campo é adicionado para cada propriedade gravável definida no modelo. |

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a criar uma regra avançada no aplicativo Azure IoT Central, pode aprender a [Analisar os dados do dispositivo no aplicativo Azure IoT Central](howto-create-analytics.md)