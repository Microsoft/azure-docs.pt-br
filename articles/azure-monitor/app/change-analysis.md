---
title: Usar a análise de alterações do aplicativo no Azure Monitor para encontrar problemas do aplicativo Web | Microsoft Docs
description: Use a análise de alterações do aplicativo no Azure Monitor para solucionar problemas de aplicativos em sites ativos no serviço Azure App.
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 3efa26a1eaea8f522d9717efb0de0ec8e1682e0e
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875135"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Usar a análise de alterações do aplicativo (versão prévia) no Azure Monitor

Quando ocorre um problema ou uma interrupção do site ativo, determinar rapidamente a causa raiz é fundamental. As soluções de monitoramento padrão podem alertá-lo sobre um problema. Eles podem até mesmo indicar qual componente está falhando. Mas esse alerta nem sempre explicará imediatamente a causa da falha. Você sabe que seu site funcionou cinco minutos atrás e agora ele está interrompido. O que mudou nos últimos cinco minutos? Essa é a pergunta que a análise de alterações do aplicativo foi projetada para responder em Azure Monitor.

Aproveitando o poder do [grafo de recursos do Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview), a análise de alterações fornece informações sobre as alterações do aplicativo do Azure para aumentar a capacidade de observação e reduzir o MTTR (tempo médio de reparo).

> [!IMPORTANT]
> A análise de alterações está atualmente em visualização. Esta versão de visualização é fornecida sem um contrato de nível de serviço. Esta versão não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter recursos restritos. Para obter mais informações, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Visão geral

A análise de alterações detecta vários tipos de alterações, da camada de infraestrutura até a implantação do aplicativo. É um provedor de recursos do Azure de nível de assinatura que verifica as alterações de recursos na assinatura. A análise de alterações fornece dados para várias ferramentas de diagnóstico para ajudar os usuários a entender quais alterações podem ter causado problemas.

O diagrama a seguir ilustra a arquitetura da análise de alterações:

![Diagrama de arquitetura de como a análise de alterações obtém dados de alteração e fornece a ele ferramentas de cliente](./media/change-analysis/overview.png)

Atualmente, a análise de alterações é integrada à experiência **diagnosticar e resolver problemas** no aplicativo Web do serviço de aplicativo. Para habilitar a detecção de alterações e exibir alterações no aplicativo Web, consulte a seção *análise de alterações para o recurso aplicativos Web* mais adiante neste artigo.

### <a name="azure-resource-manager-deployment-changes"></a>Azure Resource Manager alterações de implantação

Usando o [grafo de recursos do Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview), a análise de alterações fornece um registro histórico de como os recursos do Azure que hospedam seu aplicativo foram alterados ao longo do tempo. A análise de alterações pode detectar, por exemplo, alterações nas regras de configuração de IP, identidades gerenciadas e configurações de SSL. Portanto, se uma marca for adicionada a um aplicativo Web, a análise de alterações refletirá a alteração. Essas informações estarão disponíveis contanto que o `Microsoft.ChangeAnalysis` provedor de recursos esteja habilitado na assinatura do Azure.

### <a name="changes-in-web-app-deployment-and-configuration"></a>Alterações na implantação e na configuração do aplicativo Web

A análise de alterações captura o estado de implantação e configuração de um aplicativo a cada 4 horas. Ele pode detectar, por exemplo, alterações nas variáveis de ambiente do aplicativo. A ferramenta computa as diferenças e apresenta o que mudou. Ao contrário das alterações do Resource Manager, as informações de alteração da implantação de código podem não estar disponíveis imediatamente na ferramenta. Para exibir as alterações mais recentes na análise de alterações, selecione **verificar alterações agora**.

![Captura de tela do botão "examinar alterações agora"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Alterações de dependência

As alterações nas dependências de recursos também podem causar problemas em um aplicativo Web. Por exemplo, se um aplicativo Web chamar um cache Redis, a SKU do cache Redis poderá afetar o desempenho do aplicativo Web. Para detectar alterações nas dependências, a análise de alterações verifica o registro DNS do aplicativo Web. Dessa forma, ele identifica as alterações em todos os componentes de aplicativo que podem causar problemas.

## <a name="change-analysis-for-the-web-apps-feature"></a>Análise de alteração para o recurso de aplicativos Web

No Azure Monitor, a análise de alterações está atualmente incorporada à experiência de **diagnóstico e solução de problemas** de autoatendimento. Acesse essa experiência na página **visão geral** do aplicativo do serviço de aplicativo.

![Captura de tela do botão "visão geral" e o botão "diagnosticar e resolver problemas"](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Habilitar a análise de alterações na ferramenta diagnosticar e resolver problemas

1. Selecione **disponibilidade e desempenho**.

    ![Captura de tela das opções de solução de problemas de "disponibilidade e desempenho"](./media/change-analysis/availability-and-performance.png)

1. Selecione **alterações do aplicativo**. Não que o recurso também esteja disponível em **falhas do aplicativo**.

   ![Captura de tela do botão "falhas do aplicativo"](./media/change-analysis/application-changes.png)

1. Para habilitar a análise de alterações, selecione **habilitar agora**.

   ![Captura de tela de opções de "falhas do aplicativo"](./media/change-analysis/enable-changeanalysis.png)

1. Ative a **análise de alterações** e selecione **salvar**.

    ![Captura de tela da interface do usuário "Habilitar análise de alteração"](./media/change-analysis/change-analysis-on.png)


1. Para acessar a análise de alterações, selecione **diagnosticar e solucionar problemas** > de**falhas de aplicativo** **de desempenho** > e disponibilidade. Você verá um grafo que resume o tipo de alterações ao longo do tempo, juntamente com detalhes sobre essas alterações:

     ![Captura de tela da exibição alterar comparação](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Habilitar análise de alterações em escala

Se sua assinatura inclui vários aplicativos Web, a habilitação do serviço no nível do aplicativo Web seria ineficiente. Nesse caso, siga estas instruções alternativas.

### <a name="register-the-change-analysis-resource-provider-for-your-subscription"></a>Registrar o provedor de recursos de análise de alterações para sua assinatura

1. Registrar o sinalizador de recurso de análise de alterações (versão prévia). Como o sinalizador de recurso está em versão prévia, você precisa registrá-lo para torná-lo visível para sua assinatura:

   1. Abra o [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/).

      ![Captura de tela de alteração Cloud Shell](./media/change-analysis/cloud-shell.png)

   1. Altere o tipo de Shell para **PowerShell**.

      ![Captura de tela de alteração Cloud Shell](./media/change-analysis/choose-powershell.png)

   1. Execute o seguinte comando do PowerShell:

        ``` PowerShell
        Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to enable
        Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
        Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag
        ```

1. Registre o provedor de recursos de análise de alterações para a assinatura.

   - Vá para **assinaturas**e selecione a assinatura que você deseja habilitar no serviço de alteração. Em seguida, selecione provedores de recursos:

        ![Captura de tela mostrando como registrar o provedor de recursos de análise de alterações](./media/change-analysis/register-rp.png)

       - Selecione **Microsoft. ChangeAnalysis**. Em seguida, na parte superior da página, selecione **registrar**.

       - Depois que o provedor de recursos estiver habilitado, você poderá definir uma marca oculta no aplicativo Web para detectar alterações no nível de implantação. Para definir uma marca oculta, siga as instruções em **não é possível buscar informações de análise de alterações**.

   - Como alternativa, você pode usar um script do PowerShell para registrar o provedor de recursos:

        ```PowerShell
        Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
        ```

        Para usar o PowerShell para definir uma marca oculta em um aplicativo Web, execute o seguinte comando:

        ```powershell
        $webapp=Get-AzWebApp -Name <name_of_your_webapp>
        $tags = $webapp.Tags
        $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
        Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
        ```

     > [!NOTE]
     > Depois de adicionar a marca oculta, talvez você ainda precise aguardar até 4 horas antes de começar a ver as alterações. Os resultados são atrasados porque a análise de alterações verifica seu aplicativo Web somente a cada 4 horas. O agendamento de 4 horas limita o impacto no desempenho do exame.

## <a name="next-steps"></a>Próximas etapas

- Habilitar Application Insights para [aplicativos de serviços Azure apps](azure-web-apps.md).
- Habilite Application Insights para a [VM do Azure e o conjunto de dimensionamento de máquinas virtuais do Azure aplicativos hospedados pelo IIS](azure-vm-vmss-apps.md).
- Saiba mais sobre o [grafo de recursos do Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview), que ajuda a análise de alterações de energia.
