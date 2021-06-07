---
title: Tutorial – Gerenciar o acesso a recursos no gerenciamento de direitos do Azure AD
description: Tutorial passo a passo sobre como criar seu primeiro pacote de acesso usando o portal do Azure no gerenciamento de direitos do Azure Active Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 09/30/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1adbc653c8b698cb7b439b54b0a77d2b8cd4042a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100547393"
---
# <a name="tutorial-manage-access-to-resources-in-azure-ad-entitlement-management"></a>Tutorial: Gerenciar o acesso a recursos no gerenciamento de direitos do Azure AD

Gerenciar o acesso a todos os recursos de que os funcionários precisam, como grupos, aplicativos e sites, é uma função importante para as organizações. Você quer conceder aos funcionários o nível de acesso necessário para que eles sejam produtivos e remover o acesso quando ele não for mais necessário.

Neste tutorial, você trabalhará para o Woodgrove Bank como administrador de TI. Você foi incumbido de criar um pacote de recursos para uma campanha de marketing que os usuários internos podem usar para solicitar o autoatendimento. As solicitações não exigem aprovação e o acesso do usuário expira após 30 dias. Para este tutorial, os recursos da campanha de marketing apenas são a associação em um único grupo, mas poderiam ser uma coleção de grupos, aplicativos ou sites do SharePoint Online.

![Diagrama mostrando a visão geral do cenário.](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um pacote de acesso com um grupo como um recurso
> * Permitir que um usuário em seu diretório solicite acesso
> * Demonstrar como um usuário interno pode solicitar o pacote de acesso

Para obter uma demonstração passo a passo do processo de implantação do gerenciamento de direitos do Azure Active Directory, incluindo a criação de seu primeiro pacote de acesso, veja o vídeo a seguir:

>[!VIDEO https://www.youtube.com/embed/zaaKvaaYwI4]

## <a name="prerequisites"></a>Pré-requisitos

Para usar o gerenciamento de direitos do Azure AD, é necessário ter uma das seguintes licenças:

- Azure AD Premium P2
- Licença do Enterprise Mobility + Security (EMS) E5

Para obter mais informações, veja [Requisitos de licença](entitlement-management-overview.md#license-requirements).

## <a name="step-1-set-up-users-and-group"></a>Etapa 1: Configurar usuários e grupo

Um diretório de recursos tem um ou mais recursos para serem compartilhados. Nesta etapa, você criará um grupo chamado **Recursos de marketing** no diretório do Woodgrove Bank que é o recurso de destino para o gerenciamento de direitos. Você também configurará um solicitante interno.

**Função de pré-requisito:** Administrador global ou administrador de usuário

![Criar usuários e grupos](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Entre no [portal do Azure](https://portal.azure.com) como administrador Global ou de Usuários.  

1. Na navegação à esquerda, clique em **Active Directory do Azure**.

1. Crie ou configure os dois usuários a seguir. É possível usar estes nomes ou nomes diferentes. **Admin1** pode ser o usuário como o que você está se conectando no momento.

    | Nome | Função do diretório |
    | --- | --- |
    | **Admin1** | Administrador global<br/>-ou-<br/>Administrador de usuários |
    | **Requestor1** | Usuário |

1. Crie um grupo de segurança do Azure AD chamado **Recursos de marketing** com um tipo de associação **Atribuído**.

    Esse grupo será o recurso de destino do gerenciamento de direitos. O grupo deve estar sem membros para começar.

## <a name="step-2-create-an-access-package"></a>Etapa 2: Criar um pacote de acesso

Um *pacote de acesso* é um pacote de recursos de que uma equipe ou projeto precisa e é regido por políticas. Pacotes de acesso são definidos em contêineres chamados *catálogos*. Nesta etapa, você criará um pacote de acesso de **Campanha de marketing** no catálogo **Geral**.

**Função de pré-requisito:** Administrador global, Administrador de usuário, Proprietário do catálogo ou Gerenciador de pacote de acesso

![Criar um pacote de acesso](./media/entitlement-management-access-package-first/elm-access-package.png)

1. No portal do Azure, na navegação esquerda, clique em **Azure Active Directory**.

2. No menu esquerdo, clique em **Governança de Identidade**

3. No menu esquerdo, clique em **Pacotes de acesso**.  Se você vir **Acesso negado**, verifique se uma licença P2 do Azure AD Premium está presente em seu diretório.

4. Clique em **Novo pacote de acesso**.

    ![Gerenciamento de direitos no portal do Azure](./media/entitlement-management-shared/access-packages-list.png)

5. Na guia **Noções básicas**, digite o nome do pacote de acesso **Campanha de marketing** e a descrição **Acesso a recursos para a campanha**.

6. Deixe a lista suspensa **Catálogo** definida como **Geral**.

    ![Novo pacote de acesso – Guia Noções Básicas](./media/entitlement-management-access-package-first/basics.png)

7. Clique em **Avançar** para abrir a guia **Funções de recurso**.

    Nesta guia, você seleciona os recursos e a função de recurso para incluir no pacote de acesso.

8. Clique em **Grupos e Equipes**.

9. No painel Selecionar grupos, localize e selecione o grupo **Recursos de marketing** criado anteriormente.

     Por padrão, você vê grupos dentro do catálogo Geral. Quando você seleciona um grupo fora do catálogo Geral, que você pode ver ao marcar a caixa de seleção **Ver todos**, ele é adicionado ao catálogo Geral.

    ![Captura de tela que mostra a guia "Novo pacote de acesso – Funções de recurso" e a janela "Selecionar grupos".](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

10. Clique em **Selecionar** para adicionar o grupo à lista.

11. Na lista suspensa **Função**, selecione **Membro**.

    ![Novo pacote de acesso – Guia Funções de recurso](./media/entitlement-management-access-package-first/resource-roles.png)

    >[!IMPORTANT]
    >Os grupos atribuíveis a funções adicionados a um pacote de acesso serão indicados usando o subtipo **Atribuível a funções**. Confira [Criar um grupo atribuível a função](../roles/groups-create-eligible.md) no Azure Active Directory para obter mais detalhes sobre os grupos atribuíveis a funções do Azure AD. Tenha em mente que, uma vez que um grupo de atribuição de função está presente em um catálogo de pacotes de acesso, os usuários administrativos capazes de realizar o gerenciamento de direitos, incluindo administradores globais, administradores de usuários e proprietários do catálogo, poderão controlar os pacotes de acesso no catálogo, permitindo que eles escolham quem pode ser adicionado a esses grupos. Se não vir um grupo atribuível a funções que deseja adicionar ou se não puder adicioná-lo, verifique se você tem a função de gerenciamento de direitos e a função do Azure AD necessárias para executar essa operação. Talvez seja necessário solicitar que alguém com as funções necessárias adicione o recurso ao seu catálogo. Para obter mais informações, confira [Funções necessárias para adicionar recursos a um catálogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

    >[!NOTE]
    > Ao usar [grupos dinâmicos](../enterprise-users/groups-create-rule.md), você não verá outras funções disponíveis além do proprietário. Isso ocorre por design.
    > ![Visão geral do cenário](./media/entitlement-management-access-package-first/dynamic-group-warning.png)
    


12. Clique **Avançar** para abrir a guia **Solicitações**.

    Nessa guia, você cria uma política de solicitação. Uma *política* define as regras ou grades de proteção para acessar um pacote de acesso. Você cria uma política que permite que um usuário específico no diretório do recurso solicite o pacote de acesso.

13. Na seção **Usuários que podem solicitar acesso**, clique em **Para usuários em seu diretório** e clique em **Usuários e grupos específicos**.

    ![Novo pacote de acesso – guia Solicitações](./media/entitlement-management-access-package-first/requests.png)

14. Clique em **Adicionar usuários e grupos**.

15. No painel Selecionar usuários e grupos, selecione o usuário **Requestor1** que você criou anteriormente.

    ![Novo pacote de acesso – guia Solicitações – Selecionar usuários e grupos](./media/entitlement-management-access-package-first/requests-select-users-groups.png)

16. Clique em **Selecionar**.

17. Role para baixo até as seções **Aprovação** e **Habilitar solicitações**.

18. Deixe **Exigir aprovação** definido como **Não**.

19. Para **Habilitar solicitações**, clique em **Sim** para permitir que esse pacote de acesso seja solicitado assim que for criado.

    ![Novo pacote de acesso – guia Solicitações – Aprovação e habilitar solicitações](./media/entitlement-management-access-package-first/requests-approval-enable.png)

20. Clique em **Avançar** para abrir a guia **Ciclo de vida**.

21. Na seção **Término**, defina **As atribuições de pacote de acesso expiram** como **Número de dias**.

22. Defina **Atribuições expiram após** como **30** dias.

    ![Novo pacote de acesso – guia Ciclo de Vida](./media/entitlement-management-access-package-first/lifecycle.png)

23. Clique em **Avançar** para abrir a guia **Examinar + Criar**.

    ![Novo pacote de acesso – Guia Examinar + Criar](./media/entitlement-management-access-package-first/review-create.png)

    Após alguns instantes, você deverá ver uma notificação de que o pacote de acesso foi criado com êxito.

24. No menu à esquerda do pacote de acesso da campanha de marketing, clique em **Visão Geral**.

25. Copie o **Link do portal Meus Acessos**.

    Você usará esse link na próxima etapa.

    ![Visão geral do pacote de acesso – Link do portal Meus Acessos](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-3-request-access"></a>Etapa 3: Solicitar acesso

Nesta etapa, você executará as etapas como o **solicitante interno** e solicitará acesso ao pacote de acesso. Os solicitadores enviam suas solicitações usando um site denominado portal Meus Acessos. O portal Meus Acessos permite que os solicitantes enviem solicitações para pacotes de acesso, vejam os pacotes de acesso aos quais eles já têm acesso e vejam seu histórico de solicitações.

**Função de pré-requisito:** Solicitante interno

1. Saia do portal do Azure.

1. Em uma nova janela do navegador, navegue até o link do portal Meus Acessos copiado na etapa anterior.

1. Entre no portal Meus Acessos como **Requestor1**.

    Você deve ver o pacote de acesso **Campanha de Marketing**.

1. Se necessário, na coluna **Descrição**, clique na seta para exibir detalhes sobre o pacote de acesso.

    ![Portal Meus Acessos – Pacotes de acesso](./media/entitlement-management-shared/my-access-access-packages.png)

1. Clique na marca de seleção para selecionar o pacote.

1. Clique em **Solicitar acesso** para abrir o painel Solicitar acesso.

    ![Portal Meus Acessos – botão Solicitar acesso](./media/entitlement-management-access-package-first/my-access-request-access-button.png)

1. Na caixa **Justificativa comercial**, digite a justificativa **Estou trabalhando na nova campanha de marketing**.

    ![Portal Meus Acessos – Solicitar acesso](./media/entitlement-management-shared/my-access-request-access.png)

1. Clique em **Enviar**.

1. No menu esquerdo, clique em **Histórico de solicitações** para verificar se sua solicitação foi enviada.

## <a name="step-4-validate-that-access-has-been-assigned"></a>Etapa 4: Validar que o acesso foi atribuído

Nesta etapa, você confirma que o **solicitante interno** foi atribuído ao pacote de acesso e que agora é um membro do grupo **Recursos de marketing**.

**Função de pré-requisito:** Administrador global, Administrador de usuário, Proprietário do catálogo ou Gerenciador de pacote de acesso

1. Saia do portal Meus Acessos.

1. Entre no [portal do Azure](https://portal.azure.com) como o **Admin1**.

1. Clique em **Azure Active Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **Pacotes de acesso**.

1. Localize e clique no pacote de acesso de **Campanha de Marketing**.

1. No menu esquerdo, clique em **Solicitações**.

    Você deve ver Requestor1 e a política Inicial com um status de **Entregue**.

1. Clique na solicitação para ver os detalhes dela.

    ![Pacote de acesso – Detalhes da solicitação](./media/entitlement-management-access-package-first/request-details.png)

1. Na navegação à esquerda, clique em **Active Directory do Azure**.

1. Clique em **Grupos** e abra o grupo de **Recursos de marketing**.

1. Clique em **Membros**.

    Você deve ver **Requestor1** listado como um membro.

    ![Membros de recursos de marketing](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-5-clean-up-resources"></a>Etapa 5: Limpar os recursos

Nesta etapa, você removerá as alterações feitas e excluirá o pacote de acesso **Campanha de Marketing**.

**Função de pré-requisito:**  Administrador global ou administrador de usuário

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. Abra o pacote de acesso **Campanha de Marketing**.

1. Clique em **Atribuições**.

1. Para **Requestor1**, clique nas reticências ( **...** ) e em **Remover acesso**. Na mensagem que é exibida, clique em **Sim**.

    Depois de alguns momentos, o status mudará de Entregue para Expirado.

1. Clique em **Funções do recurso**.

1. Para **Recursos de marketing**, clique nas reticências ( **...** ) e em **Remover função do recurso**. Na mensagem que é exibida, clique em **Sim**.

1. Abra a lista de pacotes de acesso.

1. Para **Campanha de Marketing**, clique nas reticências ( **...** ) e, em seguida, clique em **Excluir**. Na mensagem que é exibida, clique em **Sim**.

1. No Azure Active Directory, exclua os usuários criados por você como **Requestor1** e **Admin1**.

1. Exclua o grupo **Recursos de marketing**.

## <a name="next-steps"></a>Próximas etapas

Passe para o próximo artigo para conhecer etapas comuns de cenário no gerenciamento de direitos.
> [!div class="nextstepaction"]
> [Cenários comuns](entitlement-management-scenarios.md)
