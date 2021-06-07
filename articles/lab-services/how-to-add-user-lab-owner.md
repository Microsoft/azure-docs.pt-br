---
title: Como adicionar proprietários adicionais a um laboratório no Azure Lab Services
description: Este artigo mostra como um administrador pode adicionar um usuário como um proprietário a um laboratório no Azure Lab Services.
ms.topic: article
ms.date: 09/04/2020
ms.openlocfilehash: 4b46dd08a3b9527cdfc7bdf766ee4976e67656d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89482662"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Como adicionar proprietários adicionais a um laboratório existente no Azure Lab Services
Este artigo mostra como você, como administrador, pode adicionar outros proprietários a um laboratório existente.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Adicionar usuário à função leitor da conta do laboratório
Para adicionar um usuário como um proprietário adicional a um laboratório existente, primeiro você deve conceder permissões de **leitura** ao usuário na conta do laboratório.

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no menu esquerdo. Procure **serviços de laboratório** e, em seguida, selecione-o.
3. Selecione sua **conta de laboratório** na lista. 
2. Na **página conta do laboratório**, selecione **controle de acesso (iam)** no menu à esquerda. 
2. Na página **controle de acesso (iam)** , selecione **Adicionar** na barra de ferramentas e selecione **Adicionar atribuição de função**.

    ![Atribuição de função para a conta do laboratório ](./media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. Na página **Adicionar uma atribuição de função**, siga os seguintes passos: 
    1. Selecione **leitor** para a **função**. 
    2. Selecione o usuário. 
    3. Clique em **Salvar**. 

        ![Adicionar usuário à função leitor da conta do laboratório ](./media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Adicionar usuário à função de proprietário do laboratório

> [!NOTE]
> Se o usuário tiver apenas acesso de leitor em um laboratório, o laboratório não será mostrado em labs.azure.com.

1. De volta à página **conta do laboratório** , selecione **todos os laboratórios** no menu à esquerda.
2. Selecione o **laboratório** ao qual você deseja adicionar o usuário como um proprietário. 
    
    ![Selecione o laboratório ](./media/how-to-add-user-lab-owner/select-lab.png)    
3. Na página do **laboratório** , selecione **controle de acesso (iam)** no menu à esquerda.
4. Na página **controle de acesso (iam)** , selecione **Adicionar** na barra de ferramentas e selecione **Adicionar atribuição de função**.
5. Na página **Adicionar uma atribuição de função**, siga os seguintes passos: 
    1. Selecione **proprietário** para a **função**. 
    2. Selecione o usuário. 
    3. Clique em **Salvar**. 

## <a name="next-steps"></a>Próximas etapas
Confirme se o usuário vê o laboratório após fazer logon no [portal de serviços de laboratório](https://labs.azure.com).
