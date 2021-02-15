---
title: Solucionar problemas de falhas de VM e ambiente Azure DevTest Labs
description: Saiba como solucionar problemas de falhas de máquina virtual (VM) e de criação de ambiente no Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b7d3f3ad34d8a5bb48607816623c67121d21d78c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85476471"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Solucionar problemas de falhas de máquina virtual (VM) e de criação de ambiente no Azure DevTest Labs
O DevTest Labs fornecerá avisos se um nome de computador for inválido ou se você estiver prestes a violar uma política de laboratório. Às vezes, você vê vermelho ao `X` lado da VM do laboratório ou do status do ambiente informando que algo deu errado.  Este artigo fornece alguns truques que você pode usar para encontrar o problema subjacente e, espero, evitar o problema no futuro.

## <a name="portal-notifications"></a>Notificações do portal
Se você estiver usando o portal do Azure, o primeiro lugar a ser examinado é o **painel notificações**.  O painel notificações, disponível na barra de comandos principal, clicando no **ícone de sino**, indicará se a VM do laboratório ou a criação do ambiente foi bem-sucedida ou não.  Se houve uma falha, você verá a mensagem de erro associada à falha de criação. Os detalhes geralmente fornecem mais informações para ajudá-lo a resolver o problema. No exemplo a seguir, a criação da máquina virtual falhou devido à execução de núcleos. A mensagem detalhada informa como corrigir o problema e solicitar um aumento de cota de núcleo.

![Notificação de portal do Azure](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>VM em estado de corrupção
Se você vir o status de sua VM no laboratório como **corrompido**, a VM subjacente poderá ter sido excluída da página da **máquina virtual** para a qual o usuário pode navegar na página de **máquinas virtuais** (não na página do DevTest Labs). Limpe seu laboratório no DevTest Labs excluindo a VM do laboratório. Em seguida, recrie sua VM no laboratório. 

![VM em estado corrompido](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>Logs de atividades
Examine os logs de atividade se você estiver investigando uma falha em algum momento depois de tentar a criação de sua VM ou ambiente. Esta seção mostra como localizar logs para VMs e ambientes.

## <a name="activity-logs-for-virtual-machines"></a>Logs de atividade para máquinas virtuais

1. No home page para seu laboratório, selecione a VM para iniciar a página da **máquina virtual** .
2. Na página **máquina virtual** , na seção **monitoramento** do menu à esquerda, selecione log de **atividades** para ver todos os logs associados à VM.
3. Nos itens do log de atividades, selecione a operação que falhou. Normalmente, a operação com falha é chamada `Write Virtualmachines` .
4. No painel direito, alterne para a guia JSON. Você verá os detalhes na exibição JSON do log.

    ![Log de atividades para uma VM](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Examine o log JSON até encontrar a `statusMessage` propriedade. Ele fornece a mensagem de erro principal e mais informações detalhadas, se aplicável. O JSON a seguir é um exemplo para o erro principal entre aspas acima neste artigo.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Log de atividades para um ambiente

Para ver o log de atividades para uma criação de ambiente, siga estas etapas:

1. Na home page de seu laboratório, selecione **configuração e políticas** no menu à esquerda.
2. na página **configuração e políticas** , selecione **logs de atividade** no menu.
3. Procure a falha na lista de atividades no log e selecione-a.
4. No painel direito, alterne para a guia JSON e procure o **statusMessage**.

    ![Log de atividades do ambiente](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Logs de implantação do modelo do Resource Manager
Se seu ambiente ou máquina virtual foi criado por meio da automação, há um último local para procurar informações de erro. Esse é o log de implantação do modelo de Azure Resource Manager. Quando um recurso de laboratório é criado por meio da automação, geralmente é feito por meio de uma implantação de modelo de Azure Resource Manager. Consulte [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) para obter exemplos de modelos de Azure Resource Manager que criam recursos do DevTest Labs.

Para ver os logs de implantação do modelo de laboratório, siga estas etapas:

1. Inicie a página do grupo de recursos no qual o laboratório existe.
2. Selecione **implantações** no menu à esquerda em **configurações**.
3. Procure implantações com um status de falha e selecione-a.
4. Na página **implantação** , selecione **detalhes da operação** link para a operação que falhou.
5. Você verá detalhes sobre a operação que falhou na janela **detalhes da operação** .

## <a name="next-steps"></a>Próximas etapas
Consulte [Solucionando problemas de falhas de artefato](devtest-lab-troubleshoot-artifact-failure.md)
