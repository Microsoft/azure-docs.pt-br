---
title: Adicionar um usuário como um criador de laboratório no Azure Lab Services
description: Este artigo mostra como adicionar um usuário à função de criador de laboratório para uma conta de laboratório no Azure Lab Services. Os criadores de laboratório podem criar laboratórios nessa conta de laboratório.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a6c89483744f943926f126701d28988358627a27
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435953"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>Adicionar criadores de laboratório a uma conta de laboratório no Azure Lab Services
Este artigo mostra como adicionar usuários como criadores de laboratório a uma conta de laboratório no Azure Lab Services. Esses usos podem criar laboratórios na conta do laboratório. 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>Adicionar conta de usuário da Microsoft à função de criador de laboratório
Para configurar um laboratório de curso em uma conta de laboratório, o usuário deve ser um membro da função **Criador de Laboratório** na conta de laboratório. A conta usada para criar a conta de laboratório é automaticamente adicionada a essa função. Se você planeja usar a mesma conta de usuário para criar um laboratório de curso, poderá pular esta etapa. Para usar outra conta de usuário e criar um laboratório de curso, siga estas etapas: 

Para fornecer aos educadores a permissão para criar laboratórios para suas classes, adicione-os à função de **Criador de Laboratório**:

1. Na página **Conta de Laboratório**, selecione **Controle de acesso (IAM)** e clique em **+ Adicionar atribuição de função** na barra de ferramentas. 

    ![Controle de Acesso -> botão Adicionar atribuição de função](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na página **Adicionar atribuição de função**, selecione **Criador de Laboratório** em **Função**, selecione o usuário que você deseja adicionar à função de Criadores de Laboratório e selecione **Salvar**. 

    ![Adicionar criador de laboratório](./media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Se você estiver adicionando um usuário não conta Microsoft como um criador de laboratório, consulte a seção [Adicionar um usuário não conta Microsoft como um criador de laboratório](#add-a-non-microsoft-account-user-as-a-lab-creator) . 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Adicionar um usuário não conta Microsoft como um criador de laboratório
Para adicionar um usuário como um criador de laboratório, você usa suas contas de email. Os seguintes tipos de contas de email podem ser usados:

- Uma conta de email fornecida pelo Azure Active Directory da sua universidade (AAD).
- Uma conta de email da Microsoft, como `@outlook.com` ,, `@hotmail.com` `@msn.com` ou `@live.com` .
- Uma conta de email que não seja da Microsoft, como uma fornecida pelo Yahoo ou pelo Google. No entanto, esses tipos de contas devem ser vinculados a um conta Microsoft.
- Uma conta do GitHub. Essa conta deve ser vinculada a um conta Microsoft.

### <a name="using-a-non-microsoft-email-account"></a>Usando uma conta de email que não seja da Microsoft
Os criadores/instrutores de laboratório podem usar contas de email que não são da Microsoft para se registrar e entrar em um laboratório de sala de aula.  No entanto, a entrada no portal de serviços de laboratório requer que os instrutores criem primeiro um conta Microsoft que esteja vinculado ao seu endereço de email que não seja da Microsoft.

Muitos instrutores podem já ter uma conta Microsoft vinculada a seus endereços de email que não são da Microsoft. Por exemplo, os instrutores já têm um conta Microsoft se usaram seu endereço de email com outros produtos ou serviços da Microsoft, como Office, Skype, OneDrive ou Windows.  

Quando os instrutores entram no portal dos serviços de laboratório, eles são solicitados a fornecer seu endereço de email e senha. Se o instrutor tentar entrar com um não conta Microsoft que não tenha uma conta Microsoft vinculada, o instrutor receberá a seguinte mensagem de erro: 

![Mensagem de erro](./media/how-to-configure-student-usage/cant-find-account.png)

Para se inscrever em um conta Microsoft, os instrutores devem ir para [http://signup.live.com](http://signup.live.com) .  


### <a name="using-a-github-account"></a>Usando uma conta do GitHub
Os instrutores também podem usar uma conta do GitHub existente para se registrar e entrar em um laboratório de sala de aula. Se o instrutor já tiver um conta Microsoft vinculado à sua conta do GitHub, ele poderá entrar e fornecer sua senha, conforme mostrado na seção anterior. Se eles ainda não tiverem vinculado sua conta do GitHub a um conta Microsoft, eles deverão selecionar **as opções de entrada**:

![Link de opções de entrada](./media/how-to-configure-student-usage/signin-options.png)

Na página **Opções de entrada** , selecione **entrar com o GitHub**.

![Entrar com o link do GitHub](./media/how-to-configure-student-usage/signin-github.png)

Por fim, eles são solicitados a criar um conta Microsoft que esteja vinculado à sua conta do GitHub. Ele ocorre automaticamente quando o instrutor seleciona **Avançar**.  O instrutor é imediatamente conectado e conectado ao laboratório da sala de aula.


## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Como um proprietário de laboratório, crie e gerencie laboratórios](how-to-manage-classroom-labs.md)
- [Como um proprietário de laboratório, configure e publique modelos](how-to-create-manage-template.md)
- [Como um proprietário de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
- [Como usuário do laboratório, laboratórios de acesso](how-to-use-classroom-lab.md)
