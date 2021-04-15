---
author: mgoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 11/09/2018
ms.author: magoedte
ms.openlocfilehash: 6890c71ac7c265d46cc77751786fea4d0b228588
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "67172315"
---
### <a name="troubleshoot-azure-diagnostics"></a>Solucionar problemas de diagnóstico do Azure

Se você receber a seguinte mensagem de erro, o provedor de recursos Microsoft.insights não está registrado:

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

Para registrar o provedor de recursos, execute as seguintes etapas no portal do Azure:

1.  No painel de navegação à esquerda, clique em *Assinaturas*
2.  Selecione a assinatura identificada na mensagem de erro
3.  Clique em *Provedores de Recursos*
4.  Localize o provedor *Microsoft.insights*
5.  Clique o *registrar* link

![Registrar o provedor de recursos microsoft.insights](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

Uma vez o *Microsoft.insights* provedor de recursos é registrado, configurando o diagnóstico de repetição.


No PowerShell, se você receber a seguinte mensagem de erro, precisará atualizar sua versão do PowerShell:

`Set-AzDiagnosticSetting : A parameter cannot be found that matches parameter name 'WorkspaceId'.`

Atualize a versão do Azure PowerShell e siga as instruções no artigo [Instalar o Azure PowerShell](/powershell/azure/install-az-ps).
