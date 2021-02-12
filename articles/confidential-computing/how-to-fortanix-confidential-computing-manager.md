---
title: Fortanix confidencial Computing Manager em um aplicativo gerenciado do Azure
description: Saiba como implantar o CCM (Fortanix confidencial Computing Manager) em um aplicativo gerenciado no portal do Azure.
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/03/2021
ms.author: jencook
ms.openlocfilehash: 6132b864e94c5c2af691183e198ca882c361d5ec
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551195"
---
# <a name="fortanix-confidential-computing-manager-in-an-azure-managed-application"></a>Fortanix confidencial Computing Manager em um aplicativo gerenciado do Azure

Este artigo mostra como implantar um aplicativo gerenciado pelo Fortanix confidencial Computing Manager no portal do Azure.

O Fortanix é um fornecedor de software de terceiros com produtos e serviços criados com base na infraestrutura do Azure. Há outros provedores de terceiros que oferecem serviços de computação confidencial semelhantes no Azure.

> [!NOTE]
>Os produtos mencionados neste documento não estão sob o controle da Microsoft. A Microsoft está fornecendo essas informações a você apenas como uma conveniência, e a referência a esses produtos que não são da Microsoft não implica o endosso da Microsoft.

## <a name="prerequisites"></a>Pré-requisitos

- Um registro do Docker privado para enviar imagens de aplicativo convertidas.
- Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de começar.

## <a name="deploy-a-confidential-computing-manager-through-an-azure-managed-application"></a>Implantar um Gerenciador de computação confidencial por meio de um aplicativo gerenciado do Azure

1. Acesse o [portal do Azure](https://portal.azure.com/).

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/azure-portal.png" alt-text="portal do Azure.":::

2. Na barra de pesquisa, pesquise "Fortanix confidencial Computing Manager" e você encontrará a listagem do Marketplace para Fortanix CCM. Selecione **Fortanix confidencial Computing Manager no Azure**.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/search-marketplace-listing.png" alt-text="Listagem do Marketplace.":::

3. A página na qual você cria o aplicativo de gerenciamento CCM é aberta. Selecione **criar**.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/create-managed-application.png" alt-text="Criar aplicativo.":::

4. Preencha todos os campos obrigatórios.
   1. Na seção detalhes do aplicativo gerenciado, o campo **grupo de recursos gerenciado** terá um valor padrão que o usuário pode modificar se for necessário.
   2. No campo **região** , selecione leste da **Austrália**, **sudeste da Austrália**, **leste dos eua**, **oeste dos EUA 2**, **Europa Ocidental**, **Europa setentrional**, **Canadá central**, **leste do Canadá** ou **leste dos EUA 2 EUAP**.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/required-fields.png" alt-text="Campos obrigatórios":::

   Selecione **examinar + criar** para criar o aplicativo gerenciado por CCM Fortanix.

5. Examine os detalhes e, depois que a validação for aprovada, marque a caixa de seleção **eu concordo com os termos e condições acima** e, em seguida, selecione **criar** para criar o aplicativo gerenciado.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/review-details.png" alt-text="Examine os detalhes.":::

6. A implantação de CCM Fortanix será iniciada e notificará que a implantação está em andamento.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-progress.png" alt-text="Progresso da implantação.":::

7. Quando a implantação for concluída, selecione **ir para** o botão de recurso para ir para a página "visão geral" do aplicativo gerenciado por CCM implantado para registrar o nó de computação.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-resource.png" alt-text="Captura de tela que mostra uma implantação bem-sucedida no portal do Azure.]":::

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-overview.png" alt-text="Captura de tela que mostra uma visão geral do recurso de computação confidencial no portal do Azure.":::

## <a name="enroll-the-compute-node-in-fortanix-ccm"></a>Registrar o nó de computação no CCM Fortanix

1. Selecione **confidencial Computing Manager** no menu de navegação à esquerda. Faça logon no Fortanix CCM e crie uma conta como você vê na **Figura 9**.

    Para obter mais detalhes sobre como se inscrever, faça logon e crie uma conta no CCM, consulte o [ccm introdução](https://support.fortanix.com/hc/en-us/articles/360034373551-User-s-Guide-Logging-in).
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-login.png" alt-text="Captura de tela que mostra o logon do Fortanix confidencial Computing Manager.":::
    
2. Para obter o token de junção do console de gerenciamento CCM, primeiro selecione o botão **registrar nó** . Em seguida, na janela registrar nó, selecione o botão **copiar** para copiar o token de junção.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/get-join-token.png" alt-text="Captura de tela que mostra obter o token de junção.":::

3. Agora, para registrar um agente de nó, selecione a guia **agente de nó de computação confidencial** e selecione **Adicionar** para adicionar um agente de nó CCM.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/add-node-agent.png" alt-text="Captura de tela que mostra a adição do agente de nó.":::

4.  No formulário do agente de nó CCM, preencha todos os campos obrigatórios. Cole o token de junção que você copiou na etapa 2 no **token de junção**. Selecione **revisar + enviar** para confirmar.

    Para obter mais informações sobre como registrar um nó de computação CCM, consulte [inscrever nó de computação](https://support.fortanix.com/hc/en-us/articles/360043085652-User-s-Guide-Compute-Nodes).
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/enroll-compute-node.png" alt-text="Captura de tela que mostra o registro do nó de computação.":::
    
5. Depois que a validação for aprovada, selecione **Enviar** para concluir a criação do agente de nó.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-agent-created.png" alt-text="Captura de tela que mostra que o agente do nó foi criado.":::

6. Para verificar o status da implantação, vá para a guia **visão geral** e selecione link do **grupo de recursos gerenciado** .

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-enrolled.png" alt-text="Captura de tela que mostra que o nó está registrado.":::
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/managed-resource-group.png" alt-text="Captura de tela que mostra a verificação do status da implantação.":::

7. Agora, você observará que o status da implantação ainda está em andamento e levará alguns minutos para que o agente do nó seja registrado com êxito.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-in-progress.png" alt-text="Captura de tela que mostra a implantação em andamento.":::

8. Quando o registro do agente de nó for bem-sucedido, o status será alterado para "êxito".

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-succeeded.png" alt-text="Captura de tela que mostra a implantação bem-sucedida.":::

9. Agora, no aplicativo gerenciado por CCM, vá para as páginas de nós de computação e você observará que o nó está em um estado **ativo** e registrado com êxito.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-active-state.png" alt-text="Captura de tela que mostra o nó registrado com êxito.":::

## <a name="clean-up-resources"></a>Limpar os recursos

O usuário também pode excluir um agente de nó CCM da página do agente de nó de computação confidencial. Para excluir o agente do nó, selecione o agente do nó e selecione o botão **excluir** na barra superior.

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager/delete-node-agent.png" alt-text="Captura de tela que mostra a exclusão do agente de nó.":::

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você registrou um nó usando um aplicativo gerenciado do Azure para o Gerenciador de computação confidencial do Fortanix. O registro de nó permite que você converta a imagem do aplicativo para ser executada sobre uma máquina virtual de computação confidencial. Para obter mais informações sobre máquinas virtuais de computação confidencial no Azure, confira [Soluções sobre máquinas virtuais](virtual-machine-solutions.md).

Para saber mais sobre as ofertas de computação confidencial do Azure, confira [computação confidencial do Azure](overview.md).

Saiba como concluir tarefas semelhantes usando outras ofertas de terceiros no Azure, como [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) [e o](https://sconedocs.github.io)

