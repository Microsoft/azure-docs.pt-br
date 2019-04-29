---
title: Solucionar problemas de falhas de criação de VM e o ambiente do Azure DevTest Labs | Microsoft Docs
description: Saiba como solucionar problemas de falhas na criação do ambiente no Azure DevTest Labs e máquina virtual (VM).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.openlocfilehash: a653a785e99619c3e256613d6a4d2c7592f54c8c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848446"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Solucionar problemas de falhas na criação do ambiente no Azure DevTest Labs e máquina virtual (VM)
DevTest Labs dá avisos se um nome de máquina é inválido ou se você está prestes a violar uma política de laboratório. Às vezes, você verá vermelho `X` ao lado de seu laboratório de VM ou ambiente de status que informa que algo deu errado.  Este artigo fornece alguns truques que você pode usar para localizar o problema subjacente e, Felizmente, evitar o problema no futuro.

## <a name="portal-notifications"></a>Notificações do Portal
Se você estiver usando o portal do Azure, o primeiro lugar a observar é a **painel de notificações**.  Painel de notificações, disponíveis na barra de comandos principal clicando o **ícone de sino**, informará se o laboratório de VM ou ambiente de criação foi bem-sucedida ou não.  Se houve uma falha, você verá a mensagem de erro associada à falha de criação. Os detalhes, muitas vezes ainda mais fornecem informações para ajudá-lo a resolver o problema. No exemplo a seguir, a criação de máquina virtual falhou devido à falta de núcleos. A mensagem detalhada informa como corrigir o problema e solicitar um aumento de cota de núcleo.

![Notificação no portal do Azure](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)


## <a name="activity-logs"></a>Logs de atividade
Examinar os logs de atividade, se você estiver investigando uma falha em algum momento após a tentativa a criação de um ambiente ou sua VM. Esta seção mostra como encontrar logs para VMs e ambientes.

## <a name="activity-logs-for-virtual-machines"></a>Logs de atividade para máquinas virtuais

1. Na home page para seu laboratório, selecione a VM para iniciar o **Máquina Virtual** página.
2. No **Máquina Virtual** página, o **monitoramento** seção do menu à esquerda, selecione **log de atividades** para ver todos os logs associados à VM.
3. Os itens do log de atividade, selecione a operação que falhou. Normalmente, a operação com falha é chamada `Write Virtualmachines`.
4. No painel direito, alterne para a guia JSON. Você ver os detalhes na exibição de JSON do log.

    ![Log de atividades para uma VM](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Procurar por meio do log JSON até encontrar o `statusMessage` propriedade. Ele fornece a mensagem de erro principal e obter mais informações de detalhe, se aplicável. O JSON a seguir é um erro de exemplo para o núcleo entre aspas excedido visto neste artigo.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at http://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Log de atividades para um ambiente

Para ver o log de atividades para a criação de um ambiente, siga estas etapas:

1. Na home page para seu laboratório, selecione **configuração e políticas** no menu à esquerda.
2. sobre o **configuração e políticas** página, selecione **logs de atividade** no menu.
3. Procure a falha na lista de atividade no log e selecioná-lo.
4. No painel direito, alterne para a guia JSON e procure o **statusMessage**.

    ![Log de atividades do ambiente](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Logs de implantação de modelo do Resource Manager
Se seu ambiente ou a máquina virtual foi criada por meio da automação, há um último local para procurar informações de erro. Que é o log de implantação de modelo do Azure Resource Manager. Quando um recurso de laboratório é criado por meio da automação, ele geralmente é feito por meio de uma implantação de modelo do Azure Resource Manager. Ver[ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) para modelos do Azure Resource Manager de exemplo que cria recursos do DevTest Labs.

Para ver os logs de implantação de modelo de laboratório, siga estas etapas:

1. Inicie a página do grupo de recursos no qual existe o laboratório.
2. Selecione **implantações** no menu à esquerda sob **configurações**.
3. Procure a implantações com um status de falha e selecioná-lo.
4. Sobre o **implantação** página, selecione **detalhes da operação** link para a operação que falhou.
5. Você verá detalhes sobre a operação que falhou na **detalhes da operação** janela.

## <a name="next-steps"></a>Próximas etapas
Consulte [Solucionando problemas de falhas de artefato](devtest-lab-troubleshoot-artifact-failure.md)