---
title: Atualização do depurador de instantâneo do Application Insights do Azure para aplicativos .NET | Microsoft Docs
description: Como atualizar o depurador de instantâneo para a versão mais recente nos serviços de aplicativo do Azure ou por meio de pacotes do Nuget
services: application-insights
author: MarioHewardt
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: Mario.Hewardt
ms.reviewer: mbullwin
ms.openlocfilehash: 54b79897ee378cda52106fe704e25c50a4325f38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60784125"
---
# <a name="upgrading-the-snapshot-debugger"></a>Atualizando o depurador de instantâneo

Para fornecer a melhor segurança possível para seus dados, a Microsoft está mudando para longe do TLS 1.0 e TLS 1.1, que foram mostrados fiquem vulneráveis a determinados invasores. Se você estiver usando uma versão mais antiga da extensão do site, ela exigirá uma atualização para continuar trabalhando. Este documento descreve as etapas necessárias para atualizar seu depurador de instantâneo para a versão mais recente. Há dois caminhos de atualização primários, dependendo se você tiver habilitado o depurador de instantâneo usando uma extensão de site ou se você tiver usado um SDK/Nuget adicionado ao seu aplicativo. Ambos os caminhos de atualização são discutidos abaixo. 

## <a name="upgrading-the-site-extension"></a>Atualizando a extensão de site

Se você habilitou o depurador de instantâneo usando a extensão de site, você pode atualizar facilmente usando o procedimento a seguir:

1. Entre no Portal do Azure.
2. Navegue até o recurso que tem o Application Insights e depurador de instantâneo habilitado. Por exemplo, para um aplicativo Web, navegue até o recurso de serviço de aplicativo:

   ![Captura de tela de recurso individual do serviço de aplicativo denominado DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Depois de navegar até seu recurso, clique em Application Insights na folha de visão geral:

   ![Captura de tela dos três botões. Botão central com o nome Application Insights está selecionado](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. Uma nova folha será aberta com as configurações atuais. A menos que você deseja aproveitar a oportunidade para alterar suas configurações, você pode deixá-los como está. O **aplicar** botão na parte inferior da folha não está habilitado por padrão e você terá que alternar de uma das configurações para ativar o botão. Você não precisa alterar as configurações reais, em vez disso, você pode alterar a configuração e, em seguida, imediatamente alterá-lo novamente. É recomendável ativar/desativar o Profiler, configuração e, em seguida, selecionando **aplicar**.

   ![Página de captura de tela do aplicativo serviço configuração do Application Insights com o botão Aplicar realçado em vermelho](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. Depois de clicar em **aplicar**, você será solicitado a confirmar as alterações.

    > [!NOTE]
    > O site será reiniciado como parte do processo de atualização.

   ![Captura de tela do serviço de aplicativo aplicam-se o prompt de monitoramento. Caixa de texto exibe a mensagem: "Estamos agora aplicar alterações às configurações do aplicativo e instalará nossas ferramentas para vincular seu recurso Application Insights ao aplicativo web. Isso reiniciará o site. Deseja continuar?"](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. Clique em **Sim** para aplicar as alterações. Durante o processo de uma notificação será exibida mostrando que as alterações estão sendo aplicadas:

   ![Captura de tela de aplicar alterações - atualização de mensagem de extensões que aparece no canto superior direito](./media/snapshot-debugger-upgrade/updating-extensions.png)

Uma vez concluído, uma **"E as alterações são aplicadas"** notificação será exibida.

   ![Captura de tela da mensagem informando que as alterações são aplicadas](./media/snapshot-debugger-upgrade/changes-are-applied.png)

O site foi atualizado e está pronto para uso.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Atualizando o depurador de instantâneo usando o SDK/Nuget

Se o aplicativo está usando uma versão do `Microsoft.ApplicationInsights.SnapshotCollector` abaixo da versão 1.3.1, ele precisará ser atualizado para um [versão mais recente](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) para continuar trabalhando.
