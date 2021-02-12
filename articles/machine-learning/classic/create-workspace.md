---
title: 'ML Studio (clássico): criar um espaço de trabalho – Azure'
description: Para usar Azure Machine Learning Studio (clássico), você precisa ter um espaço de trabalho Machine Learning Studio (clássico). Esse workspace contém as ferramentas necessárias para criar, gerenciar e publicar testes.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 0e2a67141970d1f5091457066db9b18a00ffecf8
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95486153"
---
# <a name="create-and-share-an-machine-learning-studio-classic-workspace"></a>Criar e compartilhar um espaço de trabalho Machine Learning Studio (clássico)

**aplica-se a:** ![ Essa é uma marca de seleção, o que significa que este artigo se aplica a Machine Learning Studio (clássico). ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ é um X, o que significa que este artigo se aplica a Azure Machine Learning ](../../../includes/media/aml-applies-to-skus/no.png)[ . Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Para usar Azure Machine Learning Studio (clássico), você precisa ter um espaço de trabalho Machine Learning Studio (clássico). Esse workspace contém as ferramentas necessárias para criar, gerenciar e publicar testes.

## <a name="create-a-studio-classic-workspace"></a>Criar um espaço de trabalho do estúdio (clássico)

Para abrir um espaço de trabalho no Machine Learning Studio (clássico), você deve estar conectado à conta da Microsoft usada para criar o espaço de trabalho ou você precisa receber um convite do proprietário para ingressar no espaço de trabalho. No portal do Azure você pode gerenciar o workspace, que inclui a capacidade de alterar o proprietário e configurar o acesso.

1. Entre no [Portal do Azure](https://portal.azure.com/)

    > [!NOTE]
    > Para entrar e criar um espaço de trabalho do estúdio (clássico), você precisa ser um administrador de assinatura do Azure. 
    >
    > 

2. Clique em **+ novo**

3. Na caixa de pesquisa, digite **Machine Learning Studio espaço de trabalho (clássico)** e selecione o item correspondente. Em seguida, selecione **Criar** na parte inferior da página.

4. Insira suas informações de workspace:

   - O *nome do workspace* pode ter até 260 caracteres, sem terminar com um espaço. O nome não pode incluir estes caracteres:`< > * % & : \ ? + /`
   - O *plano do serviço Web* escolhido (ou criado), juntamente com o *tipo de preço* associado, será usado se você implantar os serviços Web deste workspace.

     ![Criar um novo espaço de trabalho Studio (clássico)](./media/create-workspace/create-new-workspace.png)

5. Clique em **Criar**.

   No momento, o Machine Learning está disponível em um número limitado de regiões. Se sua assinatura não incluir uma dessas regiões, você poderá ver a mensagem de erro "você não tem assinaturas nas regiões permitidas".  Para solicitar a adição de uma região à sua assinatura, crie uma nova solicitação de suporte da Microsoft no portal do Azure, escolha o tipo de problema **Cobrança** e siga os prompts para enviar sua solicitação.


> [!NOTE]
> Machine Learning Studio (clássico) depende de uma conta de armazenamento do Azure que você fornece para salvar dados intermediários ao executar o fluxo de trabalho. Após o workspace ser criado, se a conta de armazenamento for excluída, ou se as chaves de acesso forem alteradas, o workspace deixará de funcionar e todos os testes nele falharão.
Se você excluir acidentalmente a conta de armazenamento, recrie-a com o mesmo nome e na mesma região da conta excluída e sincronize novamente a chave de acesso. Se tiver alterado as chaves de acesso da conta de armazenamento, sincronize novamente as chaves de acesso na configuração do workspace usando o portal do Azure.

Depois que o espaço de trabalho for implantado, você poderá abri-lo no Machine Learning Studio (clássico).

1. Navegue até Machine Learning Studio (clássico) em [https://studio.azureml.net/](https://studio.azureml.net/) .

2. Selecione seu workspace no canto superior direito.

    ![Selecione o workspace](./media/create-workspace/open-workspace.png)

3. Clique em **meus experimentos**.

    ![Experimentos abertos](./media/create-workspace/my-experiments.png)

Para obter informações sobre como gerenciar seu espaço de trabalho do estúdio (clássico), consulte [gerenciar um espaço de trabalho Azure Machine Learning Studio (clássico)](manage-workspace.md).
Se você encontrar um problema ao criar seu espaço de trabalho, consulte [Guia de solução de problemas: criar e conectar-se a um espaço de trabalho Machine Learning Studio (clássico)](index.yml).


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Compartilhar um espaço de trabalho Azure Machine Learning Studio (clássico)
Quando um espaço de trabalho Machine Learning Studio (clássico) é criado, você pode convidar usuários para seu espaço de trabalho para compartilhar o acesso ao seu espaço de trabalho e a todos os seus experimentos, conjuntos de seus, etc. Você pode adicionar usuários em uma das duas funções:

* **Usuário**: um usuário do workspace pode criar, abrir, modificar e excluir conjuntos de dados, experimentos etc. no workspace.
* **Proprietário**: um proprietário pode convidar e remover usuários no workspace, além do que o usuário pode fazer.

> [!NOTE]
> A conta de administrador que cria o workspace é adicionado automaticamente ao workspace como proprietário. No entanto, outros administradores ou usuários nessa assinatura não recebem automaticamente acesso ao workspace - é preciso convidá-los explicitamente.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Para compartilhar um espaço de trabalho do Studio (clássico)

1. Entre no Machine Learning Studio (clássico) em [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. No painel esquerdo, clique em **CONFIGURAÇÕES**

3. Clique na guia **usuários**

4. Clique em **CONVIDAR MAIS USUÁRIOS** na parte inferior da página

    ![Configurações do Studio](./media/create-workspace/settings.png)

5. Insira um ou mais endereços de email. O usuário precisa de uma conta da Microsoft válida ou de uma conta organizacional (do Azure Active Directory).

6. Selecione se deseja adicionar os usuários como Proprietário ou Usuário.

7. Clique no botão de seleção **OK**.

Cada usuário que você adicionar receberá um email com instruções sobre como entrar no workspace compartilhado.

> [!NOTE]
> Para que os usuários possam implantar ou gerenciar os serviços Web neste workspace, eles devem ser colaboradores ou administradores na assinatura do Azure. 

## <a name="troubleshoot-storage-accounts"></a>Solucionar problemas de contas de armazenamento


O serviço de Machine Learning precisa de uma conta de armazenamento para armazenar dados. Você pode usar uma conta de armazenamento existente ou pode criar uma nova conta de armazenamento ao criar o novo espaço de trabalho Machine Learning Studio (clássico) (se você tiver uma cota para criar uma nova conta de armazenamento).

Depois que o novo espaço de trabalho de Machine Learning Studio (clássico) for criado, você poderá entrar no Machine Learning Studio (clássico) usando o conta Microsoft usado para criar o espaço de trabalho. Se você encontrar a mensagem de erro "espaço de trabalho não encontrado" (semelhante à captura de tela a seguir), use as etapas a seguir para excluir os cookies do navegador.

![Workspace não encontrado](media/troubleshooting-creating-ml-workspace/screen3.png)

**Para excluir cookies do navegador**

1. Se você usa o Internet Explorer, clique no botão **Ferramentas** no canto superior direito e selecione **Opções da Internet**.  

   ![Opções da Internet](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Na guia **Geral**, clique em **Excluir…**

   ![Guia Geral](media/troubleshooting-creating-ml-workspace/screen5.png)

3. Na caixa de diálogo **Excluir Histórico de Navegação**, selecione **Cookies e dados de sites** e clique em **Excluir**.

   ![Excluir cookies](media/troubleshooting-creating-ml-workspace/screen6.png)

Depois que os cookies forem excluídos, reinicie o navegador e vá para a página [Microsoft Azure Machine Learning Studio (clássica)](https://studio.azureml.net) . Quando forem solicitados nome de usuário e senha, insira os dados da mesma conta da Microsoft usada para criar o workspace.


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como gerenciar um espaço de trabalho, consulte [gerenciar um espaço de trabalho Azure Machine Learning Studio (clássico)](manage-workspace.md).