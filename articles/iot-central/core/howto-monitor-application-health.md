---
title: Monitorar a integridade de um aplicativo de IoT Central do Azure | Microsoft Docs
description: Como um operador ou administrador, monitore a integridade geral dos dispositivos conectados ao seu aplicativo IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 01/27/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: d0e59f73dd9b62b528c3d86d315b613312df7773
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100577050"
---
# <a name="monitor-the-overall-health-of-an-iot-central-application"></a>Monitorar a integridade geral de um aplicativo IoT Central

> [!NOTE]
> As métricas só estão disponíveis para aplicativos IoT Central versão 3. Para saber como verificar a versão do aplicativo, consulte [sobre seu aplicativo](./howto-get-app-info.md).

*Este artigo se aplica a operadores e administradores.*

Neste artigo, você aprenderá a usar o conjunto de métricas fornecidas pelo IoT Central para avaliar a integridade dos dispositivos conectados ao seu aplicativo IoT Central e a integridade de suas exportações de dados em execução.

As métricas são habilitadas por padrão para seu aplicativo IoT Central e você as acessa do [portal do Azure](https://portal.azure.com/). A [plataforma de dados Azure monitor expõe essas métricas](../../azure-monitor/essentials/data-platform-metrics.md) e fornece várias maneiras para você interagir com elas. Por exemplo, você pode usar gráficos no portal do Azure, uma API REST ou consultas no PowerShell ou no CLI do Azure.

### <a name="trial-applications"></a>Aplicativos de avaliação

Os aplicativos que usam o plano de avaliação gratuita não têm uma assinatura do Azure associada e, portanto, não dão suporte a métricas de Azure Monitor. Você pode [converter um aplicativo em um plano de preços padrão](./howto-view-bill.md#move-from-free-to-standard-pricing-plan) e obter acesso a essas métricas.

## <a name="view-metrics-in-the-azure-portal"></a>Exibir métricas no portal do Azure

As etapas a seguir pressupõem que você tenha um [aplicativo IOT central](./quick-deploy-iot-central.md) com alguns [dispositivos conectados](./tutorial-connect-device.md) ou uma [exportação de dados](howto-export-data.md)em execução.

Para exibir IoT Central métricas no Portal:

1. Navegue até o recurso de IoT Central aplicativo no Portal. Por padrão, IoT Central recursos estão localizados em um grupo de recursos chamado **IOTC**.
1. Para criar um gráfico com base nas métricas do seu aplicativo, selecione **métricas** na seção **monitoramento** .

![Métricas do Azure](media/howto-monitor-application-health/metrics.png)

### <a name="azure-portal-permissions"></a>portal do Azure permissões

O acesso às métricas no portal do Azure é gerenciado pelo [controle de acesso baseado em função do Azure](../../role-based-access-control/overview.md). Use o portal do Azure para adicionar usuários ao aplicativo/grupo de recursos/assinatura do IoT Central para conceder acesso. Você deve adicionar um usuário no portal até mesmo eles já estão adicionados ao aplicativo IoT Central. Use [funções internas do Azure](../../role-based-access-control/built-in-roles.md) para um controle de acesso mais refinado.

## <a name="iot-central-metrics"></a>Métricas de IoT Central

Para obter uma lista das métricas que estão disponíveis atualmente para IoT Central, consulte [métricas com suporte com Azure monitor](../../azure-monitor/essentials/metrics-supported.md#microsoftiotcentraliotapps).

### <a name="metrics-and-invoices"></a>Métricas e faturas

As métricas podem ser diferentes dos números mostrados na sua fatura do Azure IoT Central. Essa situação ocorre por vários motivos, como:

- IoT Central [planos de preços padrão](https://azure.microsoft.com/pricing/details/iot-central/) incluem dois dispositivos e cotas de mensagens variadas gratuitamente. Embora os itens gratuitos sejam excluídos da cobrança, eles ainda são contados nas métricas.

- IoT Central gera uma ID de dispositivo de teste para cada modelo de dispositivo no aplicativo. Essa ID de dispositivo é visível na página **gerenciar dispositivo de teste** para um modelo de dispositivo. Os construtores de solução podem optar por [validar seus modelos de dispositivo](./overview-iot-central.md#create-device-templates) antes de publicá-los, gerando código que usa essas IDs de dispositivo de teste. Embora esses dispositivos sejam excluídos da cobrança, eles ainda são contados nas métricas.

- Embora as métricas possam mostrar um subconjunto da comunicação do dispositivo para a nuvem, toda a comunicação entre o dispositivo e a nuvem [conta como uma mensagem para cobrança](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar modelos de aplicativos, a próxima etapa sugerida é aprender a [gerenciar IOT central da portal do Azure](howto-manage-iot-central-from-portal.md).