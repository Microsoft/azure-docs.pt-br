---
title: Usar a análise de alterações do aplicativo no Azure Monitor para encontrar problemas do aplicativo Web | Microsoft Docs
description: Use a análise de alterações do aplicativo no Azure Monitor para solucionar problemas de aplicativos em sites ativos no serviço Azure App.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: 133a7d9b3fa04797648fa253825505d29e37ca98
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576374"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Usar a análise de alterações do aplicativo (versão prévia) no Azure Monitor

Quando ocorre um problema ou uma interrupção do site ativo, determinar rapidamente a causa raiz é fundamental. As soluções de monitoramento padrão podem alertá-lo sobre um problema. Eles podem até mesmo indicar qual componente está falhando. Mas esse alerta nem sempre explicará imediatamente a causa da falha. Você sabe que seu site funcionou cinco minutos atrás e agora ele está interrompido. O que mudou nos últimos cinco minutos? Essa é a pergunta que a análise de alterações do aplicativo foi projetada para responder em Azure Monitor.

Aproveitando o poder do [grafo de recursos do Azure](../../governance/resource-graph/overview.md), a análise de alterações fornece informações sobre as alterações do aplicativo do Azure para aumentar a capacidade de observação e reduzir o MTTR (tempo médio de reparo).

> [!IMPORTANT]
> A análise de alterações está atualmente em visualização. Esta versão de visualização é fornecida sem um contrato de nível de serviço. Esta versão não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter recursos restritos. Para obter mais informações, consulte os [Termos de uso complementares de versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Visão geral

A análise de alterações detecta vários tipos de alterações, da camada de infraestrutura até a implantação do aplicativo. É um provedor de recursos do Azure de nível de assinatura que verifica as alterações de recursos na assinatura. A análise de alterações fornece dados para várias ferramentas de diagnóstico para ajudar os usuários a entender quais alterações podem ter causado problemas.

O diagrama a seguir ilustra a arquitetura da análise de alterações:

![Diagrama de arquitetura de como a análise de alterações obtém dados de alteração e fornece a ele ferramentas de cliente](./media/change-analysis/overview.png)

## <a name="supported-resource-types"></a>Tipos de recurso compatíveis

O serviço de análise de alterações de aplicativo dá suporte a alterações de nível de propriedade de recurso em todos os tipos de recursos do Azure, incluindo recursos comuns como
- Máquina Virtual
- Conjunto de escala de máquina virtual
- Serviço de Aplicativo
- Serviço kubernetes do Azure
- Azure Function
- Recursos de rede: ou seja, grupo de segurança de rede, rede virtual, gateway de aplicativo, etc.
- Serviços de dados: ou seja, armazenamento, SQL, cache Redis, Cosmos DB, etc.

## <a name="data-sources"></a>Fontes de dados

Consultas de análise de alteração de aplicativo para Azure Resource Manager propriedades rastreadas, configurações de proxy e aplicativo Web em alterações de convidado. Além disso, o serviço rastreia as alterações de dependência de recursos para diagnosticar e monitorar um aplicativo de ponta a ponta.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Azure Resource Manager alterações de propriedades rastreadas

Usando o [grafo de recursos do Azure](../../governance/resource-graph/overview.md), a análise de alterações fornece um registro histórico de como os recursos do Azure que hospedam seu aplicativo foram alterados ao longo do tempo. Configurações rastreadas, como identidades gerenciadas, atualização do sistema operacional da plataforma e nomes de host podem ser detectadas.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Alterações de configuração Azure Resource Manager com proxy

As configurações como regra de configuração de IP, configurações de TLS e versões de extensão ainda não estão disponíveis no grafo de recursos do Azure, portanto, as consultas de análise de alterações e calculam essas alterações com segurança para fornecer mais detalhes sobre o que mudou no aplicativo.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Alterações na implantação e na configuração do aplicativo Web (alterações no convidado)

A análise de alterações captura o estado de implantação e configuração de um aplicativo a cada 4 horas. Ele pode detectar, por exemplo, alterações nas variáveis de ambiente do aplicativo. A ferramenta computa as diferenças e apresenta o que mudou. Ao contrário das alterações do Resource Manager, as informações de alteração da implantação de código podem não estar disponíveis imediatamente na ferramenta. Para exibir as alterações mais recentes na análise de alterações, selecione **Atualizar**.

![Captura de tela do botão "examinar alterações agora"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Alterações de dependência

As alterações nas dependências de recursos também podem causar problemas em um recurso. Por exemplo, se um aplicativo Web chamar um cache Redis, a SKU do cache Redis poderá afetar o desempenho do aplicativo Web. Outro exemplo é se a porta 22 foi fechada no grupo de segurança de rede de uma máquina virtual, isso causará erros de conectividade. 

#### <a name="web-app-diagnose-and-solve-problems-navigator-preview"></a>Navegador de problemas de diagnóstico e resolução de aplicativos Web (versão prévia)
Para detectar alterações nas dependências, a análise de alterações verifica o registro DNS do aplicativo Web. Dessa forma, ele identifica as alterações em todos os componentes de aplicativo que podem causar problemas.
Atualmente, há suporte para as seguintes dependências no **aplicativo Web diagnosticar e solucionar problemas | Navegador (versão prévia)**:
- Aplicativos Web
- Armazenamento do Azure
- SQL do Azure

#### <a name="related-resources"></a>Recursos relacionados
A análise de alterações do aplicativo detecta recursos relacionados. Exemplos comuns são grupo de segurança de rede, rede virtual, gateway de aplicativo e Load Balancer relacionados a uma máquina virtual. Os recursos de rede geralmente são provisionados automaticamente no mesmo grupo de recursos que os recursos que o utilizam, portanto, filtrar as alterações por grupo de recursos mostrará todas as alterações para a máquina virtual e recursos de rede relacionados.

![Captura de tela de alterações de rede](./media/change-analysis/network-changes.png)

## <a name="application-change-analysis-service-enablement"></a>Habilitação do serviço de análise de alteração do aplicativo

O serviço de análise de alterações do aplicativo calcula e agrega dados de alteração das fontes de dados mencionadas acima. Ele fornece um conjunto de análises para que os usuários naveguem facilmente por todas as alterações de recursos e identifiquem qual alteração é relevante no contexto de solução de problemas ou monitoramento.
O provedor de recursos "Microsoft. ChangeAnalysis" precisa ser registrado com uma assinatura para o Azure Resource Manager propriedades rastreadas e as configurações de proxy alteram os dados para que estejam disponíveis. À medida que você insere a ferramenta diagnosticar e solucionar problemas do aplicativo Web ou ativa a guia autônomo da análise de alterações, esse provedor de recursos é registrado automaticamente. Para alterações no convidado do aplicativo Web, a habilitação separada é necessária para a verificação de arquivos de código em um aplicativo Web. Para obter mais informações, consulte [análise de alterações na seção ferramenta diagnosticar e solucionar problemas](#application-change-analysis-in-the-diagnose-and-solve-problems-tool) mais adiante neste artigo para obter mais detalhes.

## <a name="cost"></a>Cost
A análise de alterações do aplicativo é um serviço gratuito-ela não incorre em nenhum custo de cobrança para assinaturas com ela habilitada. O serviço também não tem nenhum impacto no desempenho para a verificação de alterações de propriedades de recursos do Azure. Quando você habilita a análise de alterações para aplicativos Web no arquivo convidado (ou habilita a ferramenta diagnosticar e resolver problemas), ele terá um impacto insignificante no desempenho do aplicativo Web e nenhum custo de cobrança.

## <a name="visualizations-for-application-change-analysis"></a>Visualizações para análise de alterações de aplicativo

### <a name="standalone-ui"></a>Interface do usuário autônoma

Em Azure Monitor, há um painel autônomo para análise de alterações para exibir todas as alterações com informações sobre dependências e recursos do aplicativo.

Pesquise a análise de alterações na barra de pesquisa em portal do Azure para iniciar a experiência.

![Captura de tela da pesquisa de análise de alterações no portal do Azure](./media/change-analysis/search-change-analysis.png)

Todos os recursos em uma assinatura selecionada são exibidos com as alterações das últimas 24 horas. Para otimizar o desempenho de carregamento de página, o serviço está exibindo 10 recursos por vez. Clique em próximas páginas para exibir mais recursos. Estamos trabalhando para remover essa limitação.

![Captura de tela da folha de análise de alterações no portal do Azure](./media/change-analysis/change-analysis-standalone-blade.png)

Clicando em um recurso para exibir todas as suas alterações. Se necessário, faça uma busca detalhada em uma alteração para exibir detalhes e informações de alteração formatada em JSON.

![Captura de tela de detalhes de alteração](./media/change-analysis/change-details.png)

Para qualquer comentário, use o botão enviar comentários na folha ou no email changeanalysisteam@microsoft.com .

![Captura de tela do botão de comentários na folha de análise de alterações](./media/change-analysis/change-analysis-feedback.png)

#### <a name="multiple-subscription-support"></a>Suporte a várias assinaturas
A interface do usuário dá suporte à seleção de várias assinaturas para exibir as alterações de recursos. Use o filtro de assinatura:

![Captura de tela do filtro de assinatura que dá suporte à seleção de várias assinaturas](./media/change-analysis/multiple-subscriptions-support.png)

### <a name="web-app-diagnose-and-solve-problems"></a>Diagnóstico e resolução de problemas do aplicativo Web

Em Azure Monitor, a análise de alterações também é incorporada à experiência de **diagnóstico e solução de problemas** de autoatendimento. Acesse essa experiência na página **visão geral** do aplicativo do serviço de aplicativo.

![Captura de tela do botão "visão geral" e o botão "diagnosticar e resolver problemas"](./media/change-analysis/change-analysis.png)

### <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Análise de alteração de aplicativo na ferramenta diagnosticar e resolver problemas

A análise de alterações do aplicativo é um detector autônomo nas ferramentas de diagnóstico e solução de problemas do aplicativo Web. Ele também é agregado em **falhas de aplicativo** e **detectores de aplicativo Web**. À medida que você insere a ferramenta diagnosticar e resolver problemas, o provedor de recursos **Microsoft. ChangeAnalysis** será registrado automaticamente. Siga estas instruções para habilitar o controle de alterações do aplicativo Web no convidado.

1. Selecione **disponibilidade e desempenho**.

    ![Captura de tela das opções de solução de problemas de "disponibilidade e desempenho"](./media/change-analysis/availability-and-performance.png)

2. Selecione **alterações do aplicativo**. O recurso também está disponível em **falhas do aplicativo**.

   ![Captura de tela do botão "falhas do aplicativo"](./media/change-analysis/application-changes.png)

3. O link leva a uma interface do usuário Aalysis de alteração do aplicativo no escopo do aplicativo Web. Se o controle de alterações do aplicativo Web no convidado não estiver habilitado, siga a faixa para obter alterações de configurações de arquivo e aplicativo.

   ![Captura de tela de opções de "falhas do aplicativo"](./media/change-analysis/enable-changeanalysis.png)

4. Ative a **análise de alterações** e selecione **salvar**. A ferramenta exibe todos os aplicativos Web em um plano do serviço de aplicativo. Você pode usar a opção de nível de plano para ativar a análise de alterações para todos os aplicativos Web em um plano.

    ![Captura de tela da interface do usuário "Habilitar análise de alteração"](./media/change-analysis/change-analysis-on.png)

5. Os dados de alteração também estão disponíveis em selecionar **aplicativo Web inoperante** e detectores de **falhas de aplicativo** . Você verá um grafo que resume o tipo de alterações ao longo do tempo, juntamente com detalhes sobre essas alterações. Por padrão, as alterações nas últimas 24 horas são exibidas para ajudar com problemas imediatos.

     ![Captura de tela da exibição alterar comparação](./media/change-analysis/change-view.png)



### <a name="virtual-machine-diagnose-and-solve-problems"></a>Problemas de diagnóstico e resolução da máquina virtual

Acesse a ferramenta diagnosticar e solucionar problemas para uma máquina virtual.  Vá para **ferramentas de solução de problemas**, navegue para baixo na página e selecione **analisar alterações recentes** para exibir as alterações na máquina virtual.

![Captura de tela da VM diagnosticar e resolver problemas](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Analisador de alterações em ferramentas de solução de problemas](./media/change-analysis/analyze-recent-changes.png)

### <a name="activity-log-change-history"></a>Histórico de alterações do log de atividades
O recurso [Exibir histórico de alterações](../platform/activity-log.md#view-change-history) no log de atividades chama o back-end do serviço de análise de alterações do aplicativo para obter alterações associadas a uma operação. **O histórico de alterações** usado para chamar o grafo de [recursos do Azure](../../governance/resource-graph/overview.md) diretamente, mas permutau o back-end para chamar a análise de alterações de aplicativo para que as alterações retornadas incluam alterações no nível de recurso do [grafo de recursos do Azure](../../governance/resource-graph/overview.md), propriedades de recurso de [Azure Resource Manager](../../azure-resource-manager/management/overview.md)e alterações no convidado de serviços de PaaS, como aplicativo Web de serviços de aplicativos. Para que o serviço de análise de alterações do aplicativo possa verificar se há alterações nas assinaturas dos usuários, um provedor de recursos precisa ser registrado. Na primeira vez que você inserir a guia **histórico de alterações** , a ferramenta começará automaticamente a registrar o provedor de recursos **Microsoft. ChangeAnalysis** . Após o registro, as alterações do **grafo de recursos do Azure** estarão disponíveis imediatamente e cobrirá os últimos 14 dias. As alterações de outras fontes estarão disponíveis após cerca de 4 horas após a integração da assinatura.

![Integração do histórico de alterações do log de atividades](./media/change-analysis/activity-log-change-history.png)

### <a name="vm-insights-integration"></a>Integração de informações de VM
Os usuários que têm o [VM insights](../insights/vminsights-overview.md) habilitado podem exibir o que mudou em suas máquinas virtuais que podem causar picos em um gráfico de métricas, como CPU ou memória, e imaginar o que o causou. Os dados de alteração são integrados na barra de navegação do lado do insights da VM. O usuário pode exibir se alguma alteração ocorreu na VM e clicar em **investigar alterações** para exibir os detalhes da alteração na interface do usuário autônoma da análise de alterações do aplicativo.

[![Integração de informações de VM](./media/change-analysis/vm-insights.png)](./media/change-analysis/vm-insights.png#lightbox)



## <a name="enable-change-analysis-at-scale"></a>Habilitar análise de alterações em escala

Se sua assinatura inclui vários aplicativos Web, a habilitação do serviço no nível do aplicativo Web seria ineficiente. Execute o script a seguir para habilitar todos os aplicativos Web em sua assinatura.

Pré-requisitos:

- Módulo AZ do PowerShell. Siga as instruções em [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps)

Execute o seguinte script:

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"

# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```

## <a name="troubleshoot"></a>Solucionar problemas

### <a name="having-trouble-registering-microsoftchange-analysis-resource-provider-from-change-history-tab"></a>Tendo problemas para registrar o provedor de recursos da Microsoft. Change Analysis no histórico de alterações guia
Se for a primeira vez que você exibir o histórico de alterações após sua integração com a análise de alterações do aplicativo, você verá que ele registra automaticamente um provedor de recursos **Microsoft. ChangeAnalysis**. Em casos raros, pode ocorrer falha pelos seguintes motivos:

- **Você não tem permissões suficientes para registrar o provedor de recursos Microsoft. ChangeAnalysis**. Essa mensagem de erro significa que sua função na assinatura atual não tem o escopo **Microsoft. support/Register/Action** associado a ela. Isso pode acontecer se você não for o proprietário de uma assinatura e tiver permissões de acesso compartilhado por meio de um colega de colaborador. ou seja, exiba o acesso a um grupo de recursos. Para corrigir isso, você pode entrar em contato com o proprietário da sua assinatura para registrar o provedor de recursos **Microsoft. ChangeAnalysis** . Isso pode ser feito em portal do Azure por meio de **assinaturas | Provedores de recursos** e pesquisam ```Microsoft.ChangeAnalysis``` e registram na interface do usuário, ou por meio de Azure PowerShell ou CLI do Azure.

    Registrar provedor de recursos por meio do PowerShell: 
    ```PowerShell
    # Register resource provider
    Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
    ```

- **Falha ao registrar o provedor de recursos Microsoft. ChangeAnalysis**. Essa mensagem significa que algo falhou imediatamente porque a interface do usuário enviou a solicitação para registrar o provedor de recursos e não está relacionada ao problema de permissão. É provável que possa ser um problema de conectividade de Internet temporário. Tente atualizar a página e verificar sua conexão com a Internet. Se o erro persistir, contate changeanalysishelp@microsoft.com

- **Isso está demorando mais do que o esperado**. Essa mensagem significa que o registro está demorando mais do que 2 minutos. Isso é incomum, mas não significa necessariamente que algo deu errado. Você pode ir para **assinaturas | Provedor de recursos** para verificar o status de registro do provedor de recursos **Microsoft. ChangeAnalysis** . Você pode tentar usar a interface do usuário para cancelar o registro, registrar novamente ou atualizar para ver se ele ajuda. Se o problema persistir, entre em contato com o changeanalysishelp@microsoft.com suporte.
    ![Solucionar problemas de registro de RP demorando muito](./media/change-analysis/troubleshoot-registration-taking-too-long.png)

![Captura de tela da ferramenta diagnosticar e resolver problemas para uma máquina virtual com ferramentas de solução de problemas selecionadas.](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Captura de tela do bloco da ferramenta analisar alterações recentes de solução de problemas para uma máquina virtual.](./media/change-analysis/analyze-recent-changes.png)

### <a name="azure-lighthouse-subscription-is-not-supported"></a>Não há suporte para a assinatura do Azure Lighthouse

- **Falha ao consultar o provedor de recursos Microsoft. ChangeAnalysis** com a mensagem *não há suporte para a assinatura do Azure Lighthouse, as alterações só estão disponíveis no locatário inicial da assinatura*. Há uma limitação no momento para que o provedor de recursos de análise de alterações seja registrado por meio da assinatura do Azure Lighthouse para usuários que não estão no locatário inicial. Esperamos que essa limitação seja abordada em um futuro próximo. Se esse for um problema de bloqueio para você, há uma solução alternativa que envolve a criação de uma entidade de serviço e a atribuição explícita da função para permitir o acesso.  Entre em contato changeanalysishelp@microsoft.com para saber mais sobre isso.

### <a name="an-error-occurred-while-getting-changes-please-refresh-this-page-or-come-back-later-to-view-changes"></a>Ocorreu um erro ao obter as alterações. Atualize esta página ou volte mais tarde para exibir as alterações

Esta é a mensagem de erro geral apresentada pelo serviço de análise de alteração de aplicativo quando não foi possível carregar as alterações. Algumas causas conhecidas são:
- Erro de conectividade com a Internet do dispositivo cliente
- O serviço de análise de alterações está temporariamente indisponível, atualizando a página depois de alguns minutos geralmente corrige esse problema. Se o erro persistir, contate changeanalysishelp@micorosoft.com

### <a name="you-dont-have-enough-permissions-to-view-some-changes-contact-your-azure-subscription-administrator"></a>Você não tem permissões suficientes para exibir algumas alterações. Contate o administrador da assinatura do Azure

Esta é a mensagem de erro geral não autorizada, explicando que o usuário atual não tem permissões suficientes para exibir a alteração. Pelo menos acesso de leitor é necessário no recurso para exibir as alterações de infraestrutura retornadas pelo grafo de recursos do Azure e Azure Resource Manager. Para alterações no arquivo de aplicativo Web no convidado e alterações de configuração, pelo menos a função de colaborador é necessária.

### <a name="failed-to-register-microsoftchangeanalysis-resource-provider"></a>Falha ao registrar o provedor de recursos Microsoft. ChangeAnalysis
Essa mensagem significa que algo falhou imediatamente porque a interface do usuário enviou a solicitação para registrar o provedor de recursos e não está relacionada ao problema de permissão. É provável que possa ser um problema de conectividade de Internet temporário. Tente atualizar a página e verificar sua conexão com a Internet. Se o erro persistir, contate changeanalysishelp@microsoft.com
 
### <a name="you-dont-have-enough-permissions-to-register-microsoftchangeanalysis-resource-provider-contact-your-azure-subscription-administrator"></a>Você não tem permissões suficientes para registrar o provedor de recursos Microsoft. ChangeAnalysis. Contate o administrador da assinatura do Azure.
Essa mensagem de erro significa que sua função na assinatura atual não tem o escopo **Microsoft. support/Register/Action** associado a ela. Isso pode acontecer se você não for o proprietário de uma assinatura e tiver permissões de acesso compartilhado por meio de um colega de colaborador. ou seja, exiba o acesso a um grupo de recursos. Para corrigir isso, você pode entrar em contato com o proprietário da sua assinatura para registrar o provedor de recursos **Microsoft. ChangeAnalysis** . Isso pode ser feito em portal do Azure por meio de **assinaturas | Provedores de recursos** e pesquisam ```Microsoft.ChangeAnalysis``` e registram na interface do usuário, ou por meio de Azure PowerShell ou CLI do Azure.

Registrar provedor de recursos por meio do PowerShell: 

```PowerShell
# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
```

## <a name="next-steps"></a>Próximas etapas

- Habilitar Application Insights para [aplicativos de serviços Azure apps](azure-web-apps.md).
- Habilite Application Insights para a [VM do Azure e o conjunto de dimensionamento de máquinas virtuais do Azure aplicativos hospedados pelo IIS](azure-vm-vmss-apps.md).
- Saiba mais sobre o [grafo de recursos do Azure](../../governance/resource-graph/overview.md), que ajuda a análise de alterações de energia.