---
title: incluir arquivo
description: incluir arquivo
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 03/30/2020
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 1bbc549a9ba457d5f515df25d6fdce0c2ede6f2e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105582272"
---
## <a name="for-users-in-your-directory"></a>Para usuários em seu diretório

Siga estas etapas se desejar permitir que os usuários em seu diretório possam solicitar esse pacote de acesso. Ao definir a política de solicitação, você pode especificar usuários individuais ou grupos de usuários mais comuns. Por exemplo, sua organização pode já ter um grupo como **todos os funcionários**.  Se esse grupo for adicionado na política para usuários que podem solicitar acesso, então qualquer membro desse grupo poderá solicitar acesso.

1. Na seção **Usuários que podem solicitar acesso**, clique em **Para usuários em seu diretório**.

    Quando você seleciona essa opção, novas opções aparecem para refinar ainda mais quem em seu diretório pode solicitar esse pacote de acesso.

    ![Pacotes de acesso-solicitações-para usuários em seu diretório](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Selecione uma das seguintes opções:

    |  | Description |
    | --- | --- |
    | **Usuários e grupos específicos** | Escolha esta opção se desejar que apenas os usuários e grupos em seu diretório especificados possam solicitar esse pacote de acesso. |
    | **Todos os membros (exceto convidados)** | Escolha esta opção se desejar que todos os usuários Membros em seu diretório possam solicitar esse pacote de acesso. Essa opção não inclui nenhum usuário convidado que você possa ter convidado em seu diretório. |
    | **Todos os usuários (incluindo convidados)** | Escolha esta opção se desejar que todos os usuários Membros e usuários convidados em seu diretório possam solicitar esse pacote de acesso. |

    Os usuários convidados se referem a usuários externos que foram convidados em seu diretório com o [Azure ad B2B](../articles/active-directory/external-identities/what-is-b2b.md). Para obter mais informações sobre as diferenças entre usuários Membros e usuários convidados, consulte [quais são as permissões de usuário padrão no Azure Active Directory?](../articles/active-directory/fundamentals/users-default-permissions.md).

1. Se você selecionou **usuários e grupos específicos**, clique em **Adicionar usuários e grupos**.

1. No painel Selecionar usuários e grupos, selecione os usuários e grupos que você deseja adicionar.

    ![Pacotes de acesso-solicitações-Selecionar usuários e grupos](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. Clique em **selecionar** para adicionar os usuários e grupos.

1. Pule para a seção [aprovação](#approval) .

## <a name="for-users-not-in-your-directory"></a>Para usuários que não estão em seu diretório

 **Os usuários que não estão em seu diretório** referem-se aos usuários que estão em outro diretório ou domínio do Azure AD. Esses usuários podem ainda não ter sido convidados para seu diretório. Os diretórios do AD do Azure devem ser configurados para permitir convites em **restrições de colaboração**. Para obter mais informações, consulte [habilitar colaboração externa B2B e gerenciar quem pode convidar convidados](../articles/active-directory/external-identities/delegate-invitations.md).

> [!NOTE]
> Uma conta de usuário convidado será criada para um usuário que ainda não está em seu diretório cuja solicitação foi aprovada ou aprovada automaticamente. O convidado será convidado, mas não receberá um email de convite. Em vez disso, eles receberão um email quando sua atribuição de pacote de acesso for entregue. Por padrão, mais tarde, quando o usuário convidado não tiver mais nenhuma atribuição de pacote de acesso, porque sua última atribuição expirou ou foi cancelada, essa conta de usuário convidado será bloqueada de entrar e excluída subsequentemente. Se você quiser que os usuários convidados permaneçam em seu diretório indefinidamente, mesmo que eles não tenham atribuições de pacote de acesso, você poderá alterar as configurações de sua configuração de gerenciamento de direitos. Para obter mais informações sobre o objeto de usuário convidado, consulte [Propriedades de um usuário de colaboração Azure Active Directory B2B](../articles/active-directory/external-identities/user-properties.md).

Siga estas etapas se desejar permitir que os usuários que não estão em seu diretório solicitem este pacote de acesso:

1. Na seção **usuários que podem solicitar acesso** , clique em **para usuários que não estão em seu diretório**.

    Quando você seleciona essa opção, novas opções são exibidas.

    ![Pacotes de acesso-solicitações-para usuários que não estão em seu diretório](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Selecione uma das seguintes opções:

    |  | Description |
    | --- | --- |
    | **Organizações conectadas específicas** | Escolha esta opção se desejar selecionar em uma lista de organizações que o administrador adicionou anteriormente. Todos os usuários das organizações selecionadas podem solicitar esse pacote de acesso. |
    | **Todas as organizações conectadas** | Escolha esta opção se todos os usuários de todas as suas organizações conectadas puderem solicitar esse pacote de acesso. |
    | **Todos os usuários (todas as organizações conectadas + quaisquer novos usuários externos)** | Escolha esta opção se todos os usuários de todas as suas organizações conectadas puderem solicitar esse pacote de acesso e se as configurações de lista de permissões B2B ou de negação devem ter precedência para qualquer novo usuário externo. |

    Uma organização conectada é um diretório ou domínio externo do Azure AD com o qual você tem uma relação.

1. Se você selecionou **organizações conectadas específicas**, clique em **adicionar diretórios** para selecionar em uma lista de organizações conectadas que seu administrador adicionou anteriormente.

1. Digite o nome ou nome de domínio para procurar uma organização conectada anteriormente.

    ![Pacotes de acesso-solicitações-selecionar diretórios](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Se a organização com a qual você deseja colaborar não estiver na lista, você poderá pedir ao administrador para adicioná-lo como uma organização conectada. Para obter mais informações, consulte [Adicionar uma organização conectada](../articles/active-directory/governance/entitlement-management-organization.md).

1. Depois de selecionar todas as suas organizações conectadas, clique em **selecionar**.

    > [!NOTE]
    > Todos os usuários das organizações conectadas selecionadas poderão solicitar esse pacote de acesso. Isso inclui os usuários no Azure AD de todos os subdomínios associados à organização, a menos que esses domínios sejam bloqueados pela lista de permissão ou negação B2B do Azure. Para obter mais informações, consulte [Permitir ou bloquear convites para usuários B2B de organizações específicas](../articles/active-directory/external-identities/allow-deny-list.md).

1. Pule para a seção [aprovação](#approval) .

## <a name="none-administrator-direct-assignments-only"></a>Nenhum (somente atribuições diretas do administrador)

Siga estas etapas se desejar ignorar as solicitações de acesso e permitir que os administradores atribuam usuários específicos diretamente a esse pacote de acesso. Os usuários não precisarão solicitar o pacote de acesso. Você ainda pode definir as configurações do ciclo de vida, mas não há configurações de solicitação.

1. Na seção **usuários que podem solicitar acesso** , clique em **nenhum (somente atribuições diretas do administrador**.

    ![Pacotes de acesso-solicitações-nenhuma somente atribuições diretas de administrador](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    Depois de criar o pacote do Access, você pode atribuir diretamente usuários internos e externos específicos ao pacote do Access. Se você especificar um usuário externo, uma conta de usuário convidado será criada em seu diretório. Para obter informações sobre como atribuir um usuário diretamente, consulte [Exibir, adicionar e remover atribuições para um pacote de acesso](../articles/active-directory/governance/entitlement-management-access-package-assignments.md).

1. Pule para a seção [habilitar solicitações](#enable-requests) .

## <a name="approval"></a>Aprovação

Na seção aprovação, você especifica se uma aprovação é necessária quando os usuários solicitam esse pacote de acesso. As configurações de aprovação funcionam da seguinte maneira:

- Somente um dos aprovadores ou aprovadores de fallback selecionados precisa aprovar uma solicitação de aprovação de estágio único. 
- Somente um dos aprovadores selecionados de cada estágio precisa aprovar uma solicitação de aprovação de 2 estágios.
- O aprovador pode ser um gerente, patrocinador interno ou patrocinador externo, dependendo de quem a política está governando o acesso.
- A aprovação de cada aprovador selecionado não é necessária para a aprovação de um ou dois estágios.
- A decisão de aprovação se baseia em qualquer aprovador que examine a solicitação primeiro.

Para ver uma demonstração de como adicionar aprovadores a uma política de solicitação, Assista ao vídeo a seguir:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

Para ver uma demonstração de como adicionar uma aprovação de vários estágios a uma política de solicitação, Assista ao vídeo a seguir:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]

Siga estas etapas para especificar as configurações de aprovação para solicitações para o pacote de acesso:

1. Para exigir aprovação para solicitações dos usuários selecionados, defina a opção **exigir aprovação** para **Sim**. Ou, para que as solicitações sejam aprovadas automaticamente, defina a alternância para **não**.

1. Para exigir que os usuários forneçam uma justificativa para solicitar o pacote de acesso, defina a alternância de **justificação exigir solicitante** como **Sim**.
    
1. Agora, determine se as solicitações exigirão a aprovação de um ou dois estágios. Defina o **número de estágios** de alternância para **1** para aprovação de estágio único ou defina a alternância como **2** para aprovação de dois estágios.

    ![Pacotes de acesso-solicitações-configurações de aprovação](./media/active-directory-entitlement-management-request-policy/approval.png)

Use as etapas a seguir para adicionar aprovadores depois de selecionar Quantos estágios você precisa: 

### <a name="single-stage-approval"></a>Aprovação de estágio único

1. Adicione o **primeiro aprovador**:
    
    Se a política estiver definida para controlar o acesso para usuários em seu diretório, você poderá selecionar **gerente como Aprovador**. Ou então, adicione um usuário específico clicando em **Adicionar aprovadores** depois de selecionar escolher aprovadores específicos no menu suspenso.
    
    ![Pacotes de acesso-solicitações-para usuários no diretório-aprovador primeiro](./media/active-directory-entitlement-management-request-policy/approval-single-stage-first-approver-manager.png)

    Se essa política estiver definida para controlar o acesso para usuários que não estão em seu diretório, você poderá selecionar **patrocinador externo** ou **patrocinador interno**. Ou então, adicione um usuário específico clicando em **Adicionar aprovadores** ou grupos em escolher aprovadores específicos.
    
    ![Pacotes de acesso-solicitações-para usuários fora do diretório-aprovador primeiro](./media/active-directory-entitlement-management-request-policy/out-directory-first-approver.png)
    
1. Se você selecionou **gerente** como o primeiro aprovador, clique em **Adicionar fallback** para selecionar um ou mais usuários ou grupos em seu diretório para ser um Aprovador de fallback. Os aprovadores de fallback receberão a solicitação se o gerenciamento de direitos não conseguir localizar o Gerenciador do usuário que solicitou acesso.

    O gerente é encontrado pelo gerenciamento de direitos usando o atributo **Manager** . O atributo está no perfil do usuário no Azure AD. Para obter mais informações, consulte [Adicionar ou atualizar as informações de perfil de um usuário usando Azure Active Directory](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

1. Se você selecionou **escolher aprovadores específicos**, clique em **Adicionar aprovadores** para selecionar um ou mais usuários ou grupos em seu diretório para serem aprovadores.

1. Na caixa sob **decisão deve ser feita em quantos dias?**, especifique o número de dias que um aprovador precisa examinar uma solicitação para esse pacote de acesso.

    Se uma solicitação não for aprovada dentro desse período de tempo, ela será negada automaticamente. O usuário precisará enviar outra solicitação para o pacote de acesso.

1. Para exigir que os aprovadores forneçam uma justificativa para sua decisão, defina exigir justificação do aprovador como **Sim**.

    A justificativa é visível para outros Aprovadores e o solicitante.

### <a name="2-stage-approval"></a>aprovação de dois estágios

Se você selecionou uma aprovação de dois estágios, precisará adicionar um segundo aprovador.

1. Adicione o **segundo aprovador**: 
    
    Se os usuários estiverem em seu diretório, adicione um usuário específico como o segundo aprovador clicando em **Adicionar aprovadores** em escolher aprovadores específicos.

    ![Pacotes de acesso-solicitações-para usuários no Aprovador de diretório-segundo](./media/active-directory-entitlement-management-request-policy/in-directory-second-approver.png)

    Se os usuários não estiverem em seu diretório, selecione **patrocinador interno** ou **patrocinador externo** como o segundo aprovador. Depois de selecionar o aprovador, adicione os aprovadores de fallback.

    ![Pacotes de acesso-solicitações-para usuários fora do aprovador de diretório-segundo](./media/active-directory-entitlement-management-request-policy/out-directory-second-approver.png) 

1. Especifique o número de dias que o segundo aprovador precisa aprovar a solicitação na caixa sob **decisão deve ser feita em quantos dias?**. 

1. Defina a alternância exigir justificação do aprovador para **Sim** ou **não**.

### <a name="alternate-approvers"></a>Aprovadores alternativos

Você pode especificar aprovadores alternativos, semelhante à especificação do primeiro e do segundo Aprovadores que podem aprovar solicitações. Ter aprovadores alternativos ajudará a garantir que as solicitações sejam aprovadas ou negadas antes de expirarem (tempo limite). Você pode listar aprovadores alternativos o primeiro aprovador e o segundo aprovador para aprovação de 2 estágios. 

Ao especificar aprovadores alternativos, no caso de o primeiro ou segundo aprovadores não conseguirem aprovar ou negar a solicitação, a solicitação pendente é encaminhada para os aprovadores alternativos, de acordo com o agendamento de encaminhamento especificado durante a configuração da política. Eles recebem um email para aprovar ou negar a solicitação pendente.

Depois que a solicitação é encaminhada para os aprovadores alternativos, o primeiro ou o segundo aprovadores ainda podem aprovar ou negar a solicitação. Os aprovadores alternativos usam o mesmo site meu acesso para aprovar ou negar a solicitação pendente.

Podemos listar pessoas ou grupos de pessoas para serem Aprovadores e aprovadores alternativos. Verifique se você lista diferentes conjuntos de pessoas para serem os aprovadores primeiro, segundo e alternativo.
Por exemplo, se você tiver listado Alice e Bob como os primeiros Aprovadores, liste Carol e Dave como os aprovadores alternativos. Use as etapas a seguir para adicionar aprovadores alternativos a um pacote do Access:

1. No primeiro aprovador, segundo Aprovador ou ambos, clique em **Mostrar configurações avançadas de solicitação**.

    ![Pacotes de acesso-política-Mostrar configurações avançadas de solicitação](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. Definir **se nenhuma ação foi executada, avançar para aprovadores alternativos?** alterne para **Sim**.

1. Clique em **Adicionar aprovadores alternativos** e selecione os aprovadores alternativos na lista.

    ![Pacote de acesso-política-adicionar aprovadores alternativos](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

    Se você selecionar gerente como Aprovador para o primeiro aprovador, terá uma opção adicional, gerente de **segundo nível como Aprovador alternativo**, disponível para escolher no campo aprovador alternativo. Se você selecionar essa opção, será necessário adicionar um Aprovador de fallback para encaminhar a solicitação para, caso o sistema não possa encontrar o gerente de segundo nível.

1. Na caixa **Avançar para aprovadores alternativos após a quantidade de dias** , coloque o número de dias que os aprovadores precisam aprovar ou negar uma solicitação. Se nenhum aprovador tiver aprovado ou negado a solicitação antes da duração da solicitação, a solicitação expirará (tempo limite) e o usuário precisará enviar outra solicitação para o pacote de acesso. 

    As solicitações só podem ser encaminhadas a aprovadores alternativos por dia após a duração da solicitação chegar à metade da vida útil, e a decisão do (s) aprovador (es) principal deve expirar após pelo menos 4 dias. Se o tempo limite da solicitação for menor ou igual a 3, não haverá tempo suficiente para encaminhar a solicitação para os aprovadores alternativos. Neste exemplo, a duração da solicitação é de 14 dias. Portanto, a duração da solicitação atinge a metade da vida no dia 7. Portanto, a solicitação não pode ser encaminhada antes do dia 8. Além disso, as solicitações não podem ser encaminhadas no último dia da duração da solicitação. Portanto, no exemplo, a última solicitação pode ser encaminhada é o dia 13.

## <a name="enable-requests"></a>Habilitar solicitações

1. Se você quiser que o pacote de acesso seja disponibilizado imediatamente para usuários na política de solicitação para solicitar, mova a opção Habilitar alternar para **Sim**.

    Você sempre poderá habilitá-lo no futuro depois de concluir a criação do pacote de acesso.

    Se você selecionou **nenhum (somente atribuições diretas de administrador)** e definir habilitar como **não**, os administradores não poderão atribuir diretamente este pacote de acesso.

    ![Captura de tela que mostra a opção de habilitar novas solicitações e atribuições.](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Clique em **Avançar**.

## <a name="add-requestor-information-preview-to-an-access-package"></a>Adicionar informações do solicitante (versão prévia) a um pacote do Access

1. Vá para a guia **informações do solicitante** e clique na subguia **perguntas** .
 
1. Digite o que você deseja solicitar ao solicitante, também conhecido como cadeia de caracteres de exibição, para a pergunta na caixa de **pergunta** .

    ![Pacote de acesso-configuração de informações do solicitante de habilitação de política](./media/active-directory-entitlement-management-request-policy/add-requestor-info-question.png)

1. Se você quiser adicionar suas próprias opções de localização, clique em **Adicionar localização**.
    1. Uma vez no painel **Adicionar localizações para pergunta** , selecione o **código de idioma** para o idioma no qual você está localizando a pergunta.
    1. No idioma configurado, digite a pergunta na caixa de **texto localizada** .
    1. Depois de adicionar todas as localizações necessárias, clique em **salvar**.

    ![Pacote de acesso-política-configurar texto localizado](./media/active-directory-entitlement-management-request-policy/add-localization-question.png)

1. Selecione o **formato de resposta** no qual você deseja que os solicitantes respondam. Os formatos de resposta incluem: *texto curto*, *várias opções* e *texto longo*.
 
    ![Pacote de acesso-política-selecione Exibir e editar formato de resposta de múltipla escolha](./media/active-directory-entitlement-management-request-policy/answer-format-view-edit.png)
 
1. Ao selecionar várias opções, clique no botão **Exibir e editar** para configurar as opções de resposta.
    1. Depois de selecionar Exibir e editar, o painel **Exibir/editar pergunta** será aberto.
    1. Digite as opções de resposta que você deseja fornecer ao solicitante ao responder a pergunta nas caixas **valores de resposta** .
    1. Digite quantas respostas forem necessárias e clique em **salvar**.
    
    ![Opções de pacote de acesso-política-inserir várias seleções](./media/active-directory-entitlement-management-request-policy/answer-multiple-choice.png)
  
1. Para exigir que os solicitantes respondam a essa pergunta ao solicitar acesso a um pacote do Access, clique na caixa de seleção em **obrigatório**.

1. Clique em Avançar