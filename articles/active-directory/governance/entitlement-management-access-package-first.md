---
title: Tutorial – Criar seu primeiro pacote de acesso no gerenciamento de direitos do Azure AD (Versão Prévia) – Azure Active Directory
description: Tutorial passo a passo sobre como criar seu primeiro pacote de acesso no gerenciamento de direitos do Azure Active Directory (versão prévia).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1688651466ba6748e1254c9d33bb24435602868b
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489161"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management-preview"></a>Tutorial: Criar seu primeiro pacote de acesso no gerenciamento de direitos do Azure AD (Versão Prévia)

> [!IMPORTANT]
> No momento, o gerenciamento de direitos do Azure AD (Azure Active Directory) está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Gerenciar o acesso a todos os recursos de que os funcionários precisam, como grupos, aplicativos e sites, é uma função importante para as organizações. Você quer conceder aos funcionários o nível de acesso necessário para que eles sejam produtivos e remover o acesso quando ele não for mais necessário.

Neste tutorial, você trabalhará para o Woodgrove Bank como administrador de TI. Você foi solicitado para criar um pacote de recursos para um projeto Web para o qual os usuários internos podem solicitar o autoatendimento. As solicitações exigem aprovação e o acesso do usuário expira após 30 dias. Para este tutorial, os recursos do projeto Web apenas são a associação em um único grupo, mas poderiam ser uma coleção de grupos, aplicativos ou sites do SharePoint Online.

![Visão geral do cenário](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um pacote de acesso com um grupo como um recurso
> * Designar um aprovador
> * Demonstrar como um usuário interno pode solicitar o pacote de acesso
> * Aprovar a solicitação de acesso

Se você não tiver uma licença P2 do Azure AD Premium ou E5 do Enterprise Mobility + Security, crie uma [avaliação gratuita do E5 do Enterprise Mobility + Security](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1).

## <a name="prerequisites"></a>Pré-requisitos

Para usar o gerenciamento de direitos do Azure AD (Versão Prévia), é necessário ter uma das seguintes licenças:

- Azure AD Premium P2
- Licença do Enterprise Mobility + Security (EMS) E5

## <a name="step-1-set-up-users-and-group"></a>Etapa 1: Configurar usuários e grupo

Um diretório de recursos tem um ou mais recursos para serem compartilhados. Nesta etapa, você criará um grupo chamado **Grupo de Engenharia** no diretório do Woodgrove Bank que é o recurso de destino para o gerenciamento de direitos. Você também configurará um solicitante interno.

**Função de pré-requisito:** Administrador global ou administrador de usuário

![Criar usuários e grupos](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Entre no [portal do Azure](https://portal.azure.com) como administrador Global ou de Usuários.  

1. Na navegação à esquerda, clique em **Active Directory do Azure**.

1. Crie ou configure os dois usuários a seguir. É possível usar estes nomes ou nomes diferentes. **Admin1** pode ser o usuário como o que você está se conectando no momento.

    | NOME | Função do diretório | DESCRIÇÃO |
    | --- | --- | --- |
    | **Admin1** | Administrador global<br/>-ou-<br/>Administrador limitado (Administrador de usuários) | Administrador e aprovador |
    | **Requestor1** | Usuário | Solicitante interno |

    Para este tutorial, o administrador e o aprovador são a mesma pessoa, mas normalmente você designa uma ou mais pessoas para serem aprovadores.

1. Crie um grupo de segurança do Azure AD chamado **Grupo de Engenharia** com um tipo de associação **Atribuído**.

    Esse grupo será o recurso de destino do gerenciamento de direitos. O grupo deve estar sem membros para começar.

## <a name="step-2-create-an-access-package"></a>Etapa 2: Criar um pacote de acesso

Um *pacote de acesso* é um pacote de todos os recursos de que um usuário precisa para trabalhar em um projeto ou executar seu trabalho. Pacotes de acesso são definidos em contêineres chamados *catálogos*. Nesta etapa, você criará um **pacote de acesso de projeto Web** no catálogo **Geral**.

**Função de pré-requisito:** Administrador global ou administrador de usuário

![Criar um pacote de acesso](./media/entitlement-management-access-package-first/elm-access-package.png)

1. No portal do Azure, na navegação esquerda, clique em **Azure Active Directory**.

1. No menu esquerdo, clique em **Governança de Identidade**

1. No menu esquerdo, clique em **Pacotes de acesso**.  Se você vir **Acesso negado**, verifique se uma licença P2 do Azure AD Premium está presente nesse diretório.

1. Clique em **Novo pacote de acesso**.

    ![Gerenciamento de direitos no portal do Azure](./media/entitlement-management-access-package-first/access-packages-list.png)

1. Na guia **Noções básicas**, digite o nome **Pacote de acesso de projeto Web** e a descrição **Pacote de acesso de projeto Web de Engenharia**.

1. Deixe a lista suspensa **Catálogo** definida como **Geral**.

    ![Novo pacote de acesso – Guia Noções Básicas](./media/entitlement-management-access-package-first/basics.png)

1. Clique em **Avançar** para abrir a guia **Funções de recurso**.

    Nesta guia, selecione as permissões a serem incluídas no pacote de acesso.

1. Clique em **Grupos**.

1. No painel Selecionar grupos, localize e selecione o grupo **Grupo de Engenharia** criado anteriormente.

    Por padrão, você vê grupos dentro e fora do catálogo **Geral**. Quando você seleciona um grupo fora do catálogo **Geral**, ele será adicionado ao catálogo **Geral**.

    ![Novo pacote de acesso – Guia Funções de recurso](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. Clique em **Selecionar** para adicionar o grupo à lista.

1. Na lista suspensa **Função**, selecione **Membro**.

    ![Novo pacote de acesso – Guia Funções de recurso](./media/entitlement-management-access-package-first/resource-roles.png)

1. Clique em **Avançar** para abrir a guia **Política**.

1. Defina a alternância **Criar a primeira política** como **Posterior**.

    Você criará a política na próxima seção.

    ![Novo pacote de acesso – Guia Política](./media/entitlement-management-access-package-first/policy.png)

1. Clique em **Avançar** para abrir a guia **Examinar + Criar**.

    ![Novo pacote de acesso – Guia Examinar + Criar](./media/entitlement-management-access-package-first/review-create.png)

1. Examine as configurações do pacote de acesso e clique em **Criar**.

    Você pode ver uma mensagem de que o pacote de acesso não estará visível aos usuários porque o catálogo ainda não foi habilitado.

    ![Novo pacote de acesso – Mensagem não visível](./media/entitlement-management-access-package-first/not-visible.png)

1. Clique em **OK**.

    Após alguns instantes, você deverá ver uma notificação de que o pacote de acesso foi criado com êxito.

## <a name="step-3-create-a-policy"></a>Etapa 3: Criar uma política

Uma *política* define as regras ou grades de proteção para acessar um pacote de acesso. Nesta etapa, você criará uma política que permite que um usuário específico no diretório do recurso solicite o pacote de acesso. Você também especificará que as solicitações devem ser aprovadas e quem será o aprovador.

![Criar uma política de pacote de acesso](./media/entitlement-management-access-package-first/elm-access-package-policy.png)

**Função de pré-requisito:** Administrador global ou administrador de usuário

1. No **Pacote de acesso de projeto Web**, no menu esquerdo, clique em **Políticas**.

    ![Lista de políticas do pacote de acesso](./media/entitlement-management-access-package-first/policies-list.png)

1. Clique em **Adicionar política** para abrir Criar política.

1. Digite o nome **Política do solicitante interno** e uma descrição **Permite que os usuários neste diretório solicitem acesso aos recursos de projeto Web**.

1. Na seção **Usuários que podem solicitar acesso**, clique em **Para usuários em seu diretório**.

    ![Criar política](./media/entitlement-management-access-package-first/policy-create.png)

1. Role para baixo até a seção **Selecionar usuários e grupos** e clique em **Adicionar usuários e grupos**.

1. No painel Selecionar usuários e grupos, selecione o usuário **Requestor1** que você criou anteriormente e clique em **Selecionar**.

1. Na seção **Solicitar**, defina **Exigir aprovação** como **Sim**.

1. Na seção **Selecionar aprovadores**, clique em **Adicionar aprovadores**.

1. No painel Selecionar aprovadores, selecione o **Admin1** que você criou anteriormente e clique em **Selecionar**.

    Para este tutorial, o administrador e o aprovador são a mesma pessoa, mas é possível designar outra pessoa como um aprovador.

1. Na seção **Expiração**, defina **O pacote de acesso expira** como **Número de dias**.

1. Defina **O acesso expira após** como **30** dias.

1. Para **Habilitar política**, clique em **Sim**.

    ![Criar configurações de política](./media/entitlement-management-access-package-first/policy-create-settings.png)

1. Clique em **Criar** para criar a **Política de solicitante interno**.

1. No menu esquerdo do pacote de acesso de projeto Web, clique em **Visão geral**.

1. Copie o **Link do portal Meus Acessos**.

    Você usará esse link na próxima etapa.

    ![Visão geral do pacote de acesso – Link do portal Meus Acessos](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-4-request-access"></a>Etapa 4: Solicitar acesso

Nesta etapa, você executará as etapas como o **solicitante interno** e solicitará acesso ao pacote de acesso. Os solicitadores enviam suas solicitações usando um site denominado portal Meus Acessos. O portal Meus Acessos permite que os solicitantes enviem solicitações para pacotes de acesso, vejam os pacotes de acesso aos quais eles já têm acesso e vejam seu histórico de solicitações.

**Função de pré-requisito:** Solicitante interno

1. Saia do portal do Azure.

1. Em uma nova janela do navegador, navegue até o link do portal Meus Acessos copiado na etapa anterior.

1. Entre no portal Meus Acessos como **Requestor1**.

    Você deverá ver o **Pacote de acesso de projeto Web**.

1. Se necessário, na coluna **Descrição**, clique na seta para exibir detalhes sobre o pacote de acesso.

    ![Portal Meus Acessos – Pacotes de acesso](./media/entitlement-management-shared/my-access-access-packages.png)

1. Clique na marca de seleção para selecionar o pacote.

1. Clique em **Solicitar acesso** para abrir o painel Solicitar acesso.

1. Na caixa **Justificativa comercial**, digite a justificativa **Trabalhando no projeto Web**.

1. Defina a alternância **Solicitar para período específico** como **Sim**.

1. Defina a **Data de início** como hoje e a **Data de término** como amanhã.

    ![Portal Meus Acessos – Solicitar acesso](./media/entitlement-management-shared/my-access-request-access.png)

1. Clique em **Enviar**.

1. No menu esquerdo, clique em **Histórico de solicitações** para verificar se sua solicitação foi enviada.

## <a name="step-5-approve-access-request"></a>Etapa 5: Aprovar solicitação de acesso

Nesta etapa, você entrará como o usuário **aprovador** e aprovará a solicitação de acesso para o solicitante interno. Os aprovadores usam o mesmo portal Meus Acessos que os solicitantes usam para enviar solicitações. Usando o portal Meus Acessos, os aprovadores podem exibir aprovações e pendentes e aprovar ou negar solicitações.

**Função de pré-requisito:** Aprovador

1. Saia do portal Meus Acessos.

1. Entre no [portal Meus Acessos](https://myaccess.microsoft.com) como **Admin1**.

1. No menu esquerdo, clique em **Aprovações**.

1. Na guia **Pendente**, localize **Requestor1**.

    Se você não vir a solicitação de Requestor1, aguarde alguns minutos e tente novamente.

1. Clique no link **Exibir** para abrir o painel Solicitação de acesso.

1. Clique em **Aprovar**.

1. Na caixa **Motivo**, digite o motivo **Acesso aprovado para o projeto Web**.

    ![Portal Meus Acessos – Solicitação de acesso](./media/entitlement-management-shared/my-access-approve-request.png)

1. Clique em **Enviar** para enviar sua decisão.

    Você deverá ver uma mensagem de que ela foi aprovada com êxito.

## <a name="step-6-validate-that-access-has-been-assigned"></a>Etapa 6: Validar que o acesso foi atribuído

Agora que você aprovou a solicitação de acesso, nesta etapa, confirme se o **solicitante interno** recebeu o pacote de acesso e se agora ele é membro do grupo **Grupo de Engenharia**.

**Função de pré-requisito:** Administrador global ou administrador de usuário

1. Saia do portal Meus Acessos.

1. Entre no [portal do Azure](https://portal.azure.com) como o **Admin1**.

1. Clique em **Azure Active Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **Pacotes de acesso**.

1. Localize o **Pacote de acesso de projeto Web** e clique nele.

1. No menu esquerdo, clique em **Solicitações**.

    Você deve ver Requestor1 e a política de Solicitante interno com um status de **Entregue**.

1. Clique na solicitação para ver os detalhes dela.

    ![Pacote de acesso – Detalhes da solicitação](./media/entitlement-management-access-package-first/request-details.png)

1. Na navegação à esquerda, clique em **Active Directory do Azure**.

1. Clique em **Grupos** e abra o grupo **Grupo de Engenharia**.

1. Clique em **Membros**.

    Você deve ver **Requestor1** listado como um membro.

    ![Membros do Grupo de Engenharia](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-7-clean-up-resources"></a>Etapa 7: Limpar recursos

Nesta etapa, você removerá as alterações feitas e excluirá o pacote de acesso **Pacote de acesso de projeto Web**.

**Função de pré-requisito:**  Administrador global ou administrador de usuário

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. Abra o **Pacote de acesso de projeto Web**.

1. Clique em **Atribuições**.

1. Para **Requestor1**, clique nas reticências ( **...** ) e em **Remover acesso**.

    O status será alterado de Entregue para Expirado.

1. Clique em **Políticas**.

1. Para **Política de solicitante interno**, clique nas reticências ( **...** ) e em **Excluir**.

1. Clique em **Funções do recurso**.

1. Para o **Grupo de Engenharia**, clique nas reticências ( **...** ) e em **Remover função do recurso**.

1. Abra a lista de pacotes de acesso.

1. Em **Projeto de acesso de projeto Web**, clique nas reticências ( **...** ) e em **Excluir**.

1. No Azure Active Directory, exclua os usuários criados por você como **Requestor1** e **Admin1**.

1. Exclua o grupo **Grupo de Engenharia**.

## <a name="next-steps"></a>Próximas etapas

Passe para o próximo artigo para conhecer etapas comuns de cenário no gerenciamento de direitos.
> [!div class="nextstepaction"]
> [Cenários comuns](entitlement-management-scenarios.md)
