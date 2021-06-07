---
title: Solucionar problemas de análise de alterações do aplicativo-Azure Monitor
description: Saiba como solucionar problemas na análise de alterações do aplicativo.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 7200978ce31a47f7bd0d023cecf359b10a1c96de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520875"
---
# <a name="troubleshoot-application-change-analysis-preview"></a>Solucionar problemas de análise de alterações do aplicativo (versão prévia)

## <a name="having-trouble-registering-microsoft-change-analysis-resource-provider-from-change-history-tab"></a>Tendo problemas para registrar a Microsoft. Provedor de recursos de análise de alterações da guia histórico de alterações

Se for a primeira vez que você exibir o histórico de alterações após sua integração com a análise de alterações do aplicativo, você verá que ele registra automaticamente um provedor de recursos **Microsoft. ChangeAnalysis**. Em casos raros, pode ocorrer falha pelos seguintes motivos:

- **Você não tem permissões suficientes para registrar o provedor de recursos Microsoft. ChangeAnalysis**. Essa mensagem de erro significa que sua função na assinatura atual não tem o escopo **Microsoft. support/Register/Action** associado a ela. Isso pode acontecer se você não for o proprietário de uma assinatura e tiver permissões de acesso compartilhado por meio de um colega (ou seja, o acesso de exibição a um grupo de recursos). Para corrigir isso, você pode entrar em contato com o proprietário da sua assinatura para registrar o provedor de recursos **Microsoft. ChangeAnalysis** . Isso pode ser feito em portal do Azure por meio de **assinaturas | Provedores de recursos** e pesquisam ```Microsoft.ChangeAnalysis``` e registram na interface do usuário, ou por meio de Azure PowerShell ou CLI do Azure.

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

## <a name="azure-lighthouse-subscription-is-not-supported"></a>Não há suporte para a assinatura do Azure Lighthouse

- **Falha ao consultar o provedor de recursos Microsoft. ChangeAnalysis** com a mensagem *não há suporte para a assinatura do Azure Lighthouse, as alterações só estão disponíveis no locatário inicial da assinatura*. Há uma limitação no momento para que o provedor de recursos de análise de alterações seja registrado por meio da assinatura do Azure Lighthouse para usuários que não estão no locatário inicial. Estamos trabalhando para resolver essa limitação. Se esse for um problema de bloqueio para você, há uma solução alternativa que envolve a criação de uma entidade de serviço e a atribuição explícita da função para permitir o acesso.  Entre em contato changeanalysishelp@microsoft.com para saber mais sobre isso.

## <a name="an-error-occurred-while-getting-changes-please-refresh-this-page-or-come-back-later-to-view-changes"></a>Ocorreu um erro ao obter as alterações. Atualize esta página ou volte mais tarde para exibir as alterações

Esta é a mensagem de erro geral apresentada pelo serviço de análise de alteração de aplicativo quando não foi possível carregar as alterações. Algumas causas conhecidas são:

- Erro de conectividade com a Internet do dispositivo cliente
- O serviço de análise de alterações está temporariamente indisponível, atualizando a página depois de alguns minutos geralmente corrige esse problema. Se o erro persistir, contate changeanalysishelp@micorosoft.com

## <a name="you-dont-have-enough-permissions-to-view-some-changes-contact-your-azure-subscription-administrator"></a>Você não tem permissões suficientes para exibir algumas alterações. Contate o administrador da assinatura do Azure

Esta é a mensagem de erro geral não autorizada, explicando que o usuário atual não tem permissões suficientes para exibir a alteração. Pelo menos acesso de leitor é necessário no recurso para exibir as alterações de infraestrutura retornadas pelo grafo de recursos do Azure e Azure Resource Manager. Para alterações no arquivo de aplicativo Web no convidado e alterações de configuração, pelo menos a função de colaborador é necessária.

## <a name="failed-to-register-microsoftchangeanalysis-resource-provider"></a>Falha ao registrar o provedor de recursos Microsoft. ChangeAnalysis

Essa mensagem significa que algo falhou imediatamente porque a interface do usuário enviou a solicitação para registrar o provedor de recursos e não está relacionada ao problema de permissão. É provável que possa ser um problema de conectividade de Internet temporário. Tente atualizar a página e verificar sua conexão com a Internet. Se o erro persistir, contate changeanalysishelp@microsoft.com

## <a name="you-dont-have-enough-permissions-to-register-microsoftchangeanalysis-resource-provider-contact-your-azure-subscription-administrator"></a>Você não tem permissões suficientes para registrar o provedor de recursos Microsoft. ChangeAnalysis. Contate o administrador da assinatura do Azure

Essa mensagem de erro significa que sua função na assinatura atual não tem o escopo **Microsoft. support/Register/Action** associado a ela. Isso pode acontecer se você não for o proprietário de uma assinatura e tiver permissões de acesso compartilhado por meio de um colega (ou seja, o acesso de exibição a um grupo de recursos). Para corrigir isso, você pode entrar em contato com o proprietário da sua assinatura para registrar o provedor de recursos **Microsoft. ChangeAnalysis** . Isso pode ser feito em portal do Azure por meio de **assinaturas | Provedores de recursos** e pesquisam ```Microsoft.ChangeAnalysis``` e registram na interface do usuário, ou por meio de Azure PowerShell ou CLI do Azure.

Registrar provedor de recursos por meio do PowerShell:

```PowerShell
# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [grafo de recursos do Azure](../../governance/resource-graph/overview.md), que ajuda a análise de alterações de energia.