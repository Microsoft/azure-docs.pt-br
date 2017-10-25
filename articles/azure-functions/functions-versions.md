---
title: "Como direcionar para versões de tempo de execução do Azure Functions | Microsoft Docs"
description: "O Functions dá suporte a várias versões do tempo de execução. Saiba como especificar a versão de tempo de execução de um aplicativo de funções hospedado no Azure."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: glenga
ms.openlocfilehash: 26d4276a0a550d78a9c7657c464bd3320c956fb0
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2017
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Como direcionar para versões de tempo de execução do Azure Functions

O tempo de execução do Azure Functions implementa a execução sem servidor de seu código no Azure. Encontre esse tempo de execução em vários ambientes não hospedados no Azure. As [Ferramentas Básicas do Azure Functions](functions-run-local.md) implementa esse tempo de execução em seu computador de desenvolvimento. O [Tempo de Execução do Azure Functions](functions-runtime-overview.md) permite que você use o Functions em ambientes locais. 

O Functions dá suporte às principais versões do tempo de execução. A versão principal de uma atualização pode introduzir alterações consideráveis. Este tópico descreve como você pode direcionar seus aplicativos de função para uma versão de tempo de execução específica, quando for hospedada no Azure. 

O Functions permite que você direcione para uma versão principal específica do tempo de execução usando a configuração de aplicativo `FUNCTIONS_EXTENSION_VERSION` em seu aplicativo de funções. Isso se aplica às versões pública e de versão prévia. Seu aplicativo de funções é mantido na versão principal de tempo de execução especificada até que você escolha explicitamente mudar para uma nova versão. Seu aplicativo de funções será atualizado com as novas versões secundárias do tempo de execução assim que elas forem disponibilizadas. As atualizações de versão secundária não introduzem alterações consideráveis.  

Quando uma nova versão principal for disponibilizada publicamente, você terá a chance de mudar para essa versão quando exibir seu aplicativo de funções no portal. Depois de mudar para uma nova versão, você sempre poderá usar a configuração de aplicativo `FUNCTIONS_EXTENSION_VERSION` para regredir uma versão do tempo de execução.

Cada alteração na versão do tempo de execução faz com que seu aplicativo de função reinicie. Confira as notas de versão de todas as versões de tempo de execução (principais e secundárias) no [repositório GitHub](https://github.com/Azure/azure-webjobs-sdk-script/releases).   
## <a name="view-the-current-runtime-version"></a>Exibir a versão de tempo de execução atual

Use o procedimento a seguir para exibir a versão de tempo de execução específica usada no momento por seu aplicativo de funções. 

1. No [Portal do Azure](https://portal.azure.com), navegue até seu aplicativo de funções e, em **Recursos Configurados**, escolha **Configurações do aplicativo de funções**. 

    ![Selecionar as configurações do aplicativo de funções](./media/functions-versions/add-update-app-setting.png)

2. Na guia **Configurações do aplicativo de funções**, localize a **Versão de tempo de execução**. Observe a versão de tempo de execução específica e a versão principal solicitada. No exemplo a seguir, a versão principal é definida como `~1.0`.
 
   ![Selecionar as configurações do aplicativo de funções](./media/functions-versions/function-app-view-version.png)

## <a name="target-the-functions-version-20-runtime"></a>Direcionar para o tempo de execução da versão 2.0 do Functions

>[!IMPORTANT]   
> O tempo de execução 2.0 do Azure Functions está em versão prévia e, no momento, nem todos os recursos do Azure Functions têm suporte. Para saber mais, confira [problemas conhecidos do tempo de execução 2.0 do Azure Functions](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues)  

<!-- Add a table comparing the 1.x and 2.x runtime features-->

[!INCLUDE [functions-set-runtime-version](../../includes/functions-set-runtime-version.md)]

Você pode mudar seu aplicativo de funções para a versão prévia da versão 2.0 do tempo de execução no Portal do Azure. Na guia **Configurações do aplicativo de funções**, escolha **beta** em **Versão de tempo de execução**.  

   ![Selecionar as configurações do aplicativo de funções](./media/functions-versions/function-app-view-version.png)

Essa configuração é equivalente à definição da configuração de aplicativo `FUNCTIONS_EXTENSION_VERSION` como `beta`. Escolha o botão **~1** para voltar à versão principal com suporte público atual. Você também pode usar a CLI do Azure para atualizar essa configuração de aplicativo. 

## <a name="target-a-specific-runtime-version-from-the-portal"></a>Direcionar para uma versão de tempo de execução específica no Portal

Quando você precisar direcionar para uma versão principal diferente da versão principal atual ou da versão 2.0, defina a configuração de aplicativo `FUNCTIONS_EXTENSION_VERSION`.

1. No [Portal do Azure](https://portal.azure.com), navegue até seu aplicativo de funções e, em **Recursos Configurados**, escolha **Configurações de aplicativo**.

    ![Selecionar as configurações do aplicativo de funções](./media/functions-versions/add-update-app-setting1a.png)

2. Na guia **Configurações de aplicativo**, localize a configuração `FUNCTIONS_EXTENSION_VERSION` e altere o valor para uma versão principal válida do tempo de execução 1.x, ou `beta` para a versão 2.0. 

    ![Definir a configuração do aplicativo de funções](./media/functions-versions/add-update-app-setting2.png)

3. Clique em **Salvar** para salvar a atualização da configuração de aplicativo. 

## <a name="target-a-specific-version-using-azure-cli"></a>Direcionar para uma versão específica usando a CLI do Azure

 Você também pode definir a `FUNCTIONS_EXTENSION_VERSION` na CLI do Azure. Usando a CLI do Azure, atualize a configuração de aplicativo no aplicativo de funções com o comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set).

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
Nesse código, substitua `<function_app>` pelo nome de seu aplicativo de funções. Substitua também `<my_resource_group>` pelo nome do grupo de recursos para seu aplicativo de funções. Substitua `<version>` por uma versão principal válida do tempo de execução 1.x, ou `beta` para a versão 2.0. 

Execute esse comando a partir do [Azure Cloud Shell](../cloud-shell/overview.md) escolhendo **Experimentar** no exemplo de código anterior. Você também pode usar a [CLI do Azure localmente](/cli/azure/install-azure-cli) para executar esse comando após a execução de [az login](/cli/azure#az_login) para entrar.
