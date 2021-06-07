---
title: Solução de problemas de conta da Automação do Azure
description: Este artigo informa como solucionar problemas com uma conta do Azure.
services: automation
ms.subservice: ''
ms.date: 03/24/2020
ms.topic: troubleshooting
ms.openlocfilehash: 06c15136e9d2fabdf50031c8b4be455cf2f7bbca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896572"
---
# <a name="troubleshoot-azure-automation-account-issues"></a>Solução de problemas de conta da Automação do Azure

Este artigo aborda soluções para problemas que você pode encontrar ao usar uma conta de Automação do Azure. Confira informações gerais sobre contas de automação em [Visão geral da autenticação da conta de Automação do Azure](../automation-security-overview.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Cenário: não é possível registrar o Provedor de Recursos de Automação para assinaturas

### <a name="issue"></a>Problema

Ao trabalhar em sua conta de automação com recursos de gerenciamento como, por exemplo, o Gerenciamento de Atualizações, você encontra o seguinte erro:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Causa

O Provedor de Recursos de Automação não está registrado na assinatura.

### <a name="resolution"></a>Resolução

Para registrar o Provedor de Recursos de Automação, execute as seguintes etapas no portal do Azure:

1. No navegador, acesse o [portal do Azure](https://portal.azure.com).

2. Em **Assinaturas**, selecione sua assinatura.   

3. Em **Configurações**, selecione **Provedores de Recursos**.

4. Na lista de provedores de recursos, verifique se o provedor de recursos **Microsoft.Automation** está registrado.

5. Se o provedor não estiver listado, registre-o conforme descrito em [Resolver erros de registro do provedor de recursos](../../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="next-steps"></a>Próximas etapas

Se este artigo não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure nos [Fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport). Esta é a conta oficial do Microsoft Azure para conectar-se aos recursos certos de respostas, suporte e especialistas da comunidade do Azure.
* Registrar um incidente de suporte do Azure. Acesse o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter suporte**.
