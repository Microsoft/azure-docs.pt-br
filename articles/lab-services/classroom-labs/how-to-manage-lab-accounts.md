---
title: Gerenciar contas de laboratório no Azure Lab Services | Microsoft Docs
description: Saiba como criar uma conta de laboratório, exibir todas as contas de laboratório ou excluir uma conta de laboratório em uma assinatura do Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: spelluru
ms.openlocfilehash: f1194d8385d1e7ddcb906d0c8c3a2b56648e2547
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60696274"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Gerenciar contas de laboratório no Azure Lab Services 
Nos serviços de laboratório do Azure, uma conta de laboratório é um contêiner para tipos de laboratório gerenciado, como laboratórios de sala de aula. Um administrador configura uma conta de laboratório no Azure Lab Services e fornece acesso aos proprietários de laboratório que podem criar laboratórios na conta. Este artigo descreve como criar uma conta de laboratório, exibir todas as contas de laboratório ou excluir uma conta de laboratório.

## <a name="create-a-lab-account"></a>Criar uma conta de laboratório
As etapas a seguir ilustram como usar o portal do Azure para criar uma conta de laboratório no Azure Lab Services. 

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no menu esquerdo. Selecione **Contas de laboratório** na seção **DEVOPS**. Se você selecionar a estrela (`*`) ao lado de **Contas de laboratório**, ela será adicionada à seção **FAVORITOS** no menu esquerdo. Na próxima vez em diante, selecione **Contas de laboratório** em **FAVORITOS**.

    ![Todos os serviços -> Contas de laboratório](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Na página **Contas de laboratório**, selecione **Adicionar** na barra de ferramentas. 

    ![Selecione Adicionar na página Contas de laboratório](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Na página **Conta de laboratório**, execute as seguintes ações: 
    1. Para **Nome da conta de laboratório**, insira um nome. 
    2. Selecione a **Assinatura do Azure** na qual você quer criar a conta de laboratório.
    3. Para **Grupo de recursos**, selecione **Criar novo** e digite um nome para o grupo de recursos.
    4. Para **Local**, selecione um local/região em que você deseja que a conta de laboratório seja criada. 
    5. Para **Emparelhar rede virtual**, selecione uma VNet (rede virtual) de emparelhamento para a rede de laboratório. Os laboratórios criados nesta conta são conectados à VNet selecionada e têm acesso aos recursos na rede virtual selecionada. 
    7. Para o campo **Permitir que o criador de laboratório escolha a localização do laboratório**, especifique se deseja que os criadores de laboratório possam selecionar uma localização para o laboratório. Por padrão, a opção está desabilitada. Quando ela estiver desabilitada, os criadores de laboratório não poderão especificar uma localização para o laboratório que estão criando. Os laboratórios são criados na localização geográfica mais próxima da conta de laboratório. Quando ela estiver habilitada, um criador de laboratório poderá selecionar uma localização no momento da criação de um laboratório.      
    8. Selecione **Criar**. 

        ![Janela Criar uma conta de laboratório](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Selecione o **ícone de sino** na barra de ferramentas (**Notificações**), confirme se a implantação foi bem-sucedida e, em seguida, selecione **Ir para o recurso**. 

    Como alternativa, selecione **Atualizar** na página **Contas de laboratório** e, em seguida, selecione a conta de laboratório que você criou. 

    ![Janela Criar uma conta de laboratório](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Você verá a seguinte página de **conta de laboratório**:

    ![Página da conta de laboratório](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Adicionar um usuário à função de criador de laboratório
Para configurar um laboratório de curso em uma conta de laboratório, o usuário deve ser um membro da função **Criador de Laboratório** na conta de laboratório. A conta usada para criar a conta de laboratório é automaticamente adicionada a essa função. Se você planeja usar a mesma conta de usuário para criar um laboratório de curso, poderá pular esta etapa. Para usar outra conta de usuário para criar um laboratório de sala de aula, execute as seguintes etapas: 

Para fornecer aos educadores a permissão para criar laboratórios para suas classes, adicione-os à função de **Criador de Laboratório**:

1. Na página **Conta de Laboratório**, selecione **Controle de acesso (IAM)** e clique em **+ Adicionar atribuição de função** na barra de ferramentas. 

    ![Controle de Acesso -> botão Adicionar atribuição de função](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na página **Adicionar atribuição de função**, selecione **Criador de Laboratório** em **Função**, selecione o usuário que você deseja adicionar à função de Criadores de Laboratório e selecione **Salvar**. 

    ![Adicionar criador de laboratório](../media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="specify-marketplace-images-available-to-lab-creators"></a>Especificar imagens do Marketplace disponíveis para os criadores de laboratório
Como proprietário da conta de laboratório, você pode especificar as imagens do Marketplace que os criadores do laboratório podem usar para criar laboratórios na conta de laboratório. 

1. Selecione **Imagens do Marketplace** no menu esquerdo. Por padrão, você deve ver a lista completa de imagens (habilitadas e desabilitadas). É possível filtrar a lista para ver apenas as imagens habilitadas/desabilitadas, selecionando a opção **Somente habilitadas**/**Somente desabilitadas** na lista suspensa na parte superior. 
    
    ![Página Imagens do Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    As imagens do Marketplace exibidas na lista são apenas aquelas que atendem as seguintes condições:
        
    - Cria uma única VM
    - Usa o Azure Resource Manager para provisionar VMs
    - Não requer a compra de um plano de licenciamento extra
2. Para **desabilitar** uma imagem do Marketplace que tenha sido habilitada, execute uma das seguintes ações: 
    1. Selecione **... (reticências)**  na última coluna e selecione **Desabilitar imagem**. 

        ![Desabilitar uma imagem](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Selecione uma ou mais imagens na lista, marcando as caixas de seleção antes dos nomes de imagem na lista e selecione **Desabilitar imagens selecionadas**. 

        ![Desabilitar várias imagens](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. De modo semelhante, para **habilitar** uma imagem do Marketplace execute uma das seguintes ações: 
    1. Selecione **... (reticências)**  na última coluna e selecione **Habilitar imagem**. 
    2. Selecione uma ou mais imagens na lista, marcando as caixas de seleção antes dos nomes de imagem na lista e selecione **Habilitar imagens selecionadas**. 

## <a name="configure-the-lab-account"></a>Configurar a conta de laboratório
1. Sobre o **conta de laboratório** página, selecione **configuração Labs** no menu à esquerda.

    ![Página de configuração de laboratórios](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. Para **rede virtual do par**, selecione **Enabled** ou **desabilitado**. O valor padrão é **desabilitado**. Para habilitar a rede virtual do par, execute as seguintes etapas: 
    1. Selecione **Habilitado**.
    2. Selecione o **VNet** na lista suspensa. 
    3. Selecione **Salvar** na barra de ferramentas. 
    
        Os laboratórios criados nessa conta são conectados à rede virtual selecionada. Eles podem acessar os recursos na rede virtual selecionada. 
3. Para o **permitir que o criador de laboratório para escolher o local do laboratório**, selecione **habilitado** se você quiser que o criador de laboratório para poder selecionar um local para o laboratório. Se ele estiver desabilitado, os laboratórios são criados automaticamente no mesmo local no qual existe a conta de laboratório. 

## <a name="view-lab-accounts"></a>Exibir contas de laboratório
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os recursos** no menu. 
3. Selecione **Contas de Laboratório** para o **tipo**. 
    Você também pode filtrar por assinatura, grupo de recursos, locais e marcas. 

    ![Todos os recursos -> Contas de laboratório](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>Visualize e gerencie laboratórios na conta do laboratório

1. Na página **Conta de laboratório**, selecione **Labs** no menu à esquerda.

    ![Laboratórios na conta](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. Você vê uma **lista de laboratórios** na conta com as seguintes informações: 
    1. Nome do laboratório.
    2. A data em que o laboratório foi criado. 
    3. Endereço de e-mail do usuário que criou o laboratório. 
    4. Número máximo de usuários permitidos no laboratório. 
    5. Status do laboratório. 



## <a name="delete-a-lab-in-the-lab-account"></a>Excluir um laboratório na conta do laboratório
Siga as instruções na seção anterior para ver uma lista dos laboratórios na conta do laboratório.

1. Selecione **... (reticências)** e selecione **excluir**. 

    ![Excluir um laboratório - botão](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. Selecione **Sim** na mensagem de aviso. 

    ![Confirmar exclusão do laboratório](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>Excluir uma conta de laboratório
Siga as instruções da seção anterior que exibe as contas de laboratório em uma lista. Use as instruções a seguir para excluir uma conta de laboratório: 

1. Selecione a **conta de laboratório** que você deseja excluir. 
2. Selecione **Excluir** na barra de ferramentas. 

    ![Contas de laboratório -> botão Excluir](../media/how-to-manage-lab-accounts/delete-button.png)
1. Digite **Sim** para confirmar.
1. Selecione **Excluir**. 

    ![Excluir conta de laboratório – confirmação](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)



## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos:

- [Como um proprietário de laboratório, crie e gerencie laboratórios](how-to-manage-classroom-labs.md)
- [Como um proprietário de laboratório, configure e publique modelos](how-to-create-manage-template.md)
- [Como um proprietário de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
- [Como um usuário de laboratório, acesse os laboratórios de sala de aula](how-to-use-classroom-lab.md)