---
title: Atualizando o Aplicativo Azure insights Depurador de Instantâneos
description: Como atualizar Depurador de Instantâneos para aplicativos .NET para a versão mais recente no Azure App Services ou por meio de pacotes NuGet
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6991db6055ca8556adff67f194bac0f4aa04cb06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "77671385"
---
# <a name="upgrading-the-snapshot-debugger"></a>Atualizando o Depurador de Instantâneos

Para fornecer a melhor segurança possível para seus dados, a Microsoft está se afastando do TLS 1,0 e do TLS 1,1, que foram mostrados para serem vulneráveis a invasores determinados. Se você estiver usando uma versão mais antiga da extensão do site, será necessário uma atualização para continuar trabalhando. Este documento descreve as etapas necessárias para atualizar o depurador de instantâneos para a versão mais recente. Há dois caminhos de atualização principais, dependendo de se você habilitou o Depurador de Instantâneos usando uma extensão de site ou se usou um SDK/NuGet adicionado ao seu aplicativo. Os dois caminhos de atualização são discutidos abaixo. 

## <a name="upgrading-the-site-extension"></a>Atualizando a extensão do site

> [!IMPORTANT]
> As versões mais antigas do Application Insights usavam uma extensão de site particular chamada _Application insights extensão para Azure app serviço_. A experiência atual de Application Insights é habilitada definindo as configurações de aplicativo para liberar uma extensão de site pré-instalada.
> Para evitar conflitos, o que pode fazer com que seu site pare de funcionar, é importante excluir primeiro a extensão de site particular. Consulte a etapa 4 abaixo.

Se você habilitou o depurador de instantâneos usando a extensão de site, poderá atualizar usando o seguinte procedimento:

1. Entre no portal do Azure.
2. Navegue até o recurso que tem o Application Insights e o depurador de instantâneos habilitados. Por exemplo, para um aplicativo Web, navegue até o recurso do serviço de aplicativo:

   ![Captura de tela do recurso de serviço de aplicativo individual chamado DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Depois de navegar até o recurso, clique na folha extensões e aguarde a lista de extensões popular:

   ![Captura de tela das extensões do serviço de aplicativo mostrando Application Insights extensão para Azure App serviço instalado](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. Se qualquer versão da _extensão de Application insights para o serviço Azure app_ estiver instalada, selecione-a e clique em excluir. Confirme **Sim** para excluir a extensão e aguarde a conclusão da exclusão antes de passar para a próxima etapa.

   ![Captura de tela das extensões do serviço de aplicativo mostrando Application Insights extensão para Azure App serviço com o botão excluir realçado](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. Vá para a folha de visão geral do recurso e clique em Application Insights:

   ![Captura de tela de três botões. O botão central com o nome Application Insights está selecionado](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. Se esta for a primeira vez que você exibiu a folha Application Insights para esse serviço de aplicativo, será solicitado que você ative Application Insights. Selecione **ativar Application insights**.
 
   ![Captura de tela da primeira experiência para a folha Application Insights com o botão Ativar Application Insights realçado](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. As configurações de Application Insights atuais são exibidas. A menos que você queira tirar a oportunidade de alterar suas configurações, você pode deixá-las como estão. O botão **aplicar** na parte inferior da folha não é habilitado por padrão e você precisará alternar uma das configurações para ativar o botão. Você não precisa alterar as configurações reais, em vez disso, você pode alterar a configuração e, em seguida, alterá-la imediatamente. É recomendável alternar a configuração do criador de perfil e, em seguida, selecionar **aplicar**.

   ![Captura de tela da página de configuração do serviço de aplicativo Application Insights com o botão Aplicar realçado em vermelho](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. Depois de clicar em **aplicar**, você será solicitado a confirmar as alterações.

    > [!NOTE]
    > O site será reiniciado como parte do processo de atualização.

   ![Captura de tela do prompt de aplicação de monitoramento do serviço de aplicativo. A caixa de texto exibe a mensagem: "agora aplicaremos as alterações às configurações do aplicativo e instalaremos nossas ferramentas para vincular o recurso de Application Insights ao aplicativo Web. Isso reiniciará o site. Deseja continuar?"](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. Clique em **Sim** para aplicar as alterações e aguardar a conclusão do processo.

O site agora foi atualizado e está pronto para uso.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Atualizando Depurador de Instantâneos usando o SDK/NuGet

Se o aplicativo estiver usando uma versão da `Microsoft.ApplicationInsights.SnapshotCollector` versão anterior do 1.3.1, será necessário atualizá-lo para uma [versão mais recente](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) para continuar trabalhando.
