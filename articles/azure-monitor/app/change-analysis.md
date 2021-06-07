---
title: Usar a análise de alterações do aplicativo no Azure Monitor para encontrar problemas do aplicativo Web | Microsoft Docs
description: Use a análise de alterações do aplicativo no Azure Monitor para solucionar problemas de aplicativos em sites ativos no serviço Azure App.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: 43ece2cb0f5cb9428d8d73f769018e9fe2408ab8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655795"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Usar a Análise de Alterações de Aplicativo (versão prévia) no Azure Monitor

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
- Recursos de rede: grupo de segurança de rede, rede virtual, gateway de aplicativo, etc.
- Serviços de dados: armazenamento, SQL, cache Redis, Cosmos DB, etc.

## <a name="data-sources"></a>Fontes de dados

Consultas de análise de alteração de aplicativo para Azure Resource Manager propriedades rastreadas, configurações com proxy e alterações no convidado do aplicativo Web. Além disso, o serviço rastreia as alterações de dependência de recursos para diagnosticar e monitorar um aplicativo de ponta a ponta.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Azure Resource Manager alterações de propriedades rastreadas

Usando o [grafo de recursos do Azure](../../governance/resource-graph/overview.md), a análise de alterações fornece um registro histórico de como os recursos do Azure que hospedam seu aplicativo foram alterados ao longo do tempo. Configurações rastreadas, como identidades gerenciadas, atualização do sistema operacional da plataforma e nomes de host podem ser detectadas.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Alterações de configuração Azure Resource Manager com proxy

As configurações como regra de configuração de IP, configurações de TLS e versões de extensão ainda não estão disponíveis no grafo de recursos do Azure, portanto, as consultas de análise de alterações e calculam essas alterações com segurança para fornecer mais detalhes sobre o que mudou no aplicativo.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Alterações na implantação e na configuração do aplicativo Web (alterações no convidado)

A análise de alterações captura o estado de implantação e configuração de um aplicativo a cada 4 horas. Ele pode detectar, por exemplo, alterações nas variáveis de ambiente do aplicativo. A ferramenta computa as diferenças e apresenta o que mudou. Ao contrário das alterações do Resource Manager, as informações de alteração da implantação de código podem não estar disponíveis imediatamente na ferramenta. Para exibir as alterações mais recentes na análise de alterações, selecione **Atualizar**.

![Captura de tela do botão "examinar alterações agora"](./media/change-analysis/scan-changes.png)

Atualmente, todos os arquivos baseados em texto sob a raiz do site **wwwroot** com as seguintes extensões têm suporte:
- *. config
- *. xml
- *. JSON
- *. Gem
- *. yml
- *.txt
- *. ini
- *. env

### <a name="dependency-changes"></a>Alterações de dependência

As alterações nas dependências de recursos também podem causar problemas em um recurso. Por exemplo, se um aplicativo Web chamar um cache Redis, a SKU do cache Redis poderá afetar o desempenho do aplicativo Web. Outro exemplo é se a porta 22 foi fechada no grupo de segurança de rede de uma máquina virtual, isso causará erros de conectividade.

#### <a name="web-app-diagnose-and-solve-problems-navigator-preview"></a>Navegador de problemas de diagnóstico e resolução de aplicativos Web (versão prévia)

Para detectar alterações nas dependências, a análise de alterações verifica o registro DNS do aplicativo Web. Dessa forma, ele identifica as alterações em todos os componentes de aplicativo que podem causar problemas.
Atualmente, há suporte para as seguintes dependências no **aplicativo Web diagnosticar e solucionar problemas | Navegador (versão prévia)**:

- Aplicativos Web
- Armazenamento do Azure
- SQL do Azure

#### <a name="related-resources"></a>Recursos relacionados

A análise de alterações do aplicativo detecta recursos relacionados. Exemplos comuns são grupo de segurança de rede, rede virtual, gateway de aplicativo e Load Balancer relacionados a uma máquina virtual.
Os recursos de rede geralmente são provisionados automaticamente no mesmo grupo de recursos que os recursos que o utilizam, portanto, filtrar as alterações por grupo de recursos mostrará todas as alterações para a máquina virtual e recursos de rede relacionados.

![Captura de tela de alterações de rede](./media/change-analysis/network-changes.png)

## <a name="application-change-analysis-service-enablement"></a>Habilitação do serviço de análise de alteração do aplicativo

O serviço de análise de alterações do aplicativo calcula e agrega dados de alteração das fontes de dados mencionadas acima. Ele fornece um conjunto de análises para que os usuários naveguem facilmente por todas as alterações de recursos e identifiquem qual alteração é relevante no contexto de solução de problemas ou monitoramento.
O provedor de recursos "Microsoft. ChangeAnalysis" precisa ser registrado com uma assinatura para o Azure Resource Manager propriedades rastreadas e as configurações de proxy alteram os dados para que estejam disponíveis. À medida que você insere a ferramenta diagnosticar e solucionar problemas do aplicativo Web ou ativa a guia autônomo da análise de alterações, esse provedor de recursos é registrado automaticamente.
Para alterações no convidado do aplicativo Web, a habilitação separada é necessária para a verificação de arquivos de código em um aplicativo Web. Para obter mais informações, consulte [análise de alterações na seção ferramenta diagnosticar e solucionar problemas](change-analysis-visualizations.md#application-change-analysis-in-the-diagnose-and-solve-problems-tool) mais adiante neste artigo para obter mais detalhes.

## <a name="cost"></a>Cost
A análise de alterações do aplicativo é um serviço gratuito-ela não incorre em nenhum custo de cobrança para assinaturas com ela habilitada. O serviço também não tem nenhum impacto no desempenho para a verificação de alterações de propriedades de recursos do Azure. Quando você habilita a análise de alterações para aplicativos Web no arquivo convidado (ou habilita a ferramenta diagnosticar e resolver problemas), ele terá um impacto insignificante no desempenho do aplicativo Web e nenhum custo de cobrança.


## <a name="enable-change-analysis-at-scale-for-web-app-in-guest-file-and-environment-variable-changes"></a>Habilitar a análise de alterações em escala para o arquivo de aplicativo Web no convidado e as alterações de variável de ambiente

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

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [visualizações na análise de alterações](change-analysis-visualizations.md)
- Saiba como [solucionar problemas na análise de alterações](change-analysis-troubleshoot.md)
- Habilitar Application Insights para [aplicativos de serviços Azure apps](azure-web-apps.md).
- Habilite Application Insights para a [VM do Azure e o conjunto de dimensionamento de máquinas virtuais do Azure aplicativos hospedados pelo IIS](azure-vm-vmss-apps.md).
