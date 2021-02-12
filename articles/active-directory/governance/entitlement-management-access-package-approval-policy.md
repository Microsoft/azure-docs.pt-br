---
title: Alterar as configurações de aprovação de um pacote do Access no gerenciamento de direitos do Azure AD-Azure Active Directory
description: Saiba como alterar as configurações de informações de aprovação e solicitante para um pacote de acesso no gerenciamento de direitos Azure Active Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/16/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48b5260e883d85899953240f6ee4f83127681c9e
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591286"
---
# <a name="change-approval-and-requestor-information-preview-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Alterar as configurações de aprovação e do solicitante (versão prévia) para um pacote de acesso no gerenciamento de direitos do Azure AD

Como um Gerenciador de pacotes do Access, você pode alterar as configurações de informações de aprovação e solicitante de um pacote do Access a qualquer momento editando uma política existente ou adicionando uma nova política.

Este artigo descreve como alterar as configurações de informações de aprovação e solicitante para um pacote do Access existente.

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


## <a name="change-approval-settings-of-an-existing-access-package"></a>Alterar as configurações de aprovação de um pacote do Access existente

Siga estas etapas para especificar as configurações de aprovação para solicitações para o pacote de acesso:

**Função de pré-requisito:** Administrador global, Administrador de usuário, Proprietário do catálogo ou Gerenciador de pacote de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Selecione uma política para editar ou adicionar uma nova política ao pacote de acesso
    1. Clique em **políticas** e em **Adicionar política** se desejar criar uma nova política.
    1. Clique na política que você deseja editar e clique em **Editar**.

1. Vá para a guia **solicitação** .

1. Para exigir aprovação para solicitações dos usuários selecionados, defina a opção **exigir aprovação** para **Sim**. Ou, para que as solicitações sejam aprovadas automaticamente, defina a alternância para **não**.

1. Para exigir que os usuários forneçam uma justificativa para solicitar o pacote de acesso, defina a alternância de **justificação exigir solicitante** como **Sim**.
    
1. Agora, determine se as solicitações exigirão a aprovação de um ou dois estágios. Defina o **número de estágios** de alternância para **1** para aprovação de estágio único ou defina a alternância como **2** para aprovação de dois estágios.

    ![Pacotes de acesso-solicitações-configurações de aprovação](./media/entitlement-management-access-package-approval-policy/approval.png)


Use as etapas a seguir para adicionar aprovadores depois de selecionar Quantos estágios você precisa: 

### <a name="single-stage-approval"></a>Aprovação de estágio único

1. Adicione o **primeiro aprovador**:
    
    Se a política estiver definida para controlar o acesso para usuários em seu diretório, você poderá selecionar **gerente como Aprovador**. Ou então, adicione um usuário específico clicando em **Adicionar aprovadores** depois de selecionar escolher aprovadores específicos no menu suspenso.
    
    ![Pacotes de acesso-solicitações-para usuários no diretório-aprovador primeiro](./media/entitlement-management-access-package-approval-policy/approval-single-stage-first-approver-manager.png)

    Se essa política estiver definida para controlar o acesso para usuários que não estão em seu diretório, você poderá selecionar **patrocinador externo** ou **patrocinador interno**. Ou então, adicione um usuário específico clicando em **Adicionar aprovadores** ou grupos em escolher aprovadores específicos.
    
    ![Pacotes de acesso-solicitações-para usuários fora do diretório-aprovador primeiro](./media/entitlement-management-access-package-approval-policy/out-directory-first-approver.png)
    
1. Se você selecionou **gerente** como o primeiro aprovador, clique em **Adicionar fallback** para selecionar um ou mais usuários ou grupos em seu diretório para ser um Aprovador de fallback. Os aprovadores de fallback receberão a solicitação se o gerenciamento de direitos não conseguir localizar o Gerenciador do usuário que solicitou acesso.

    O gerente é encontrado pelo gerenciamento de direitos usando o atributo **Manager** . O atributo está no perfil do usuário no Azure AD. Para obter mais informações, consulte [Adicionar ou atualizar as informações de perfil de um usuário usando Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).

1. Se você selecionou **escolher aprovadores específicos**, clique em **Adicionar aprovadores** para selecionar um ou mais usuários ou grupos em seu diretório para serem aprovadores.

1. Na caixa sob **decisão deve ser feita em quantos dias?**, especifique o número de dias que um aprovador precisa examinar uma solicitação para esse pacote de acesso.

    Se uma solicitação não for aprovada dentro desse período de tempo, ela será negada automaticamente. O usuário precisará enviar outra solicitação para o pacote de acesso.

1. Para exigir que os aprovadores forneçam uma justificativa para sua decisão, defina exigir justificação do aprovador como **Sim**.

    A justificativa é visível para outros Aprovadores e o solicitante.

### <a name="2-stage-approval"></a>aprovação de dois estágios

Se você selecionou uma aprovação de dois estágios, precisará adicionar um segundo aprovador.

1. Adicione o **segundo aprovador**: 
    
    Se os usuários estiverem em seu diretório, adicione um usuário específico como o segundo aprovador clicando em **Adicionar aprovadores** em escolher aprovadores específicos.

    ![Pacotes de acesso-solicitações-para usuários no Aprovador de diretório-segundo](./media/entitlement-management-access-package-approval-policy/in-directory-second-approver.png)

    Se os usuários não estiverem em seu diretório, selecione **patrocinador interno** ou **patrocinador externo** como o segundo aprovador. Depois de selecionar o aprovador, adicione os aprovadores de fallback.

    ![Pacotes de acesso-solicitações-para usuários fora do aprovador de diretório-segundo](./media/entitlement-management-access-package-approval-policy/out-directory-second-approver.png) 

1. Especifique o número de dias que o segundo aprovador precisa aprovar a solicitação na caixa sob **decisão deve ser feita em quantos dias?**. 

1. Defina a alternância exigir justificação do aprovador para **Sim** ou **não**.

### <a name="alternate-approvers"></a>Aprovadores alternativos

Você pode especificar aprovadores alternativos, semelhante à especificação do primeiro e do segundo Aprovadores que podem aprovar solicitações. Ter aprovadores alternativos ajudará a garantir que as solicitações sejam aprovadas ou negadas antes de expirarem (tempo limite). Você pode listar aprovadores alternativos o primeiro aprovador e o segundo aprovador para aprovação de 2 estágios. 

Ao especificar aprovadores alternativos, no caso de o primeiro ou segundo aprovadores não conseguirem aprovar ou negar a solicitação, a solicitação pendente é encaminhada para os aprovadores alternativos, de acordo com o agendamento de encaminhamento especificado durante a configuração da política. Eles recebem um email para aprovar ou negar a solicitação pendente.

Depois que a solicitação é encaminhada para os aprovadores alternativos, o primeiro ou o segundo aprovadores ainda podem aprovar ou negar a solicitação. Os aprovadores alternativos usam o mesmo site meu acesso para aprovar ou negar a solicitação pendente.

Podemos listar pessoas ou grupos de pessoas para serem Aprovadores e aprovadores alternativos. Verifique se você lista diferentes conjuntos de pessoas para serem os aprovadores primeiro, segundo e alternativo.
Por exemplo, se você tiver listado Alice e Bob como os primeiros Aprovadores, liste Carol e Dave como os aprovadores alternativos. Use as etapas a seguir para adicionar aprovadores alternativos a um pacote do Access:

1. No primeiro aprovador, segundo Aprovador ou ambos, clique em **Mostrar configurações avançadas de solicitação**.

    :::image type="content" source="media/entitlement-management-access-package-approval-policy/alternate-approvers-click-advanced-request.png" alt-text="Pacotes de acesso-política-Mostrar configurações avançadas de solicitação":::

1. Definir **se nenhuma ação foi executada, avançar para aprovadores alternativos?** alterne para **Sim**.

1. Clique em **Adicionar aprovadores alternativos** e selecione os aprovadores alternativos na lista.

    ![Pacote de acesso-política-adicionar aprovadores alternativos](./media/entitlement-management-access-package-approval-policy/alternate-approvers-add.png)

    Se você selecionar gerente como Aprovador para o primeiro aprovador, terá uma opção adicional, gerente de **segundo nível como Aprovador alternativo**, disponível para escolher no campo aprovador alternativo. Se você selecionar essa opção, será necessário adicionar um Aprovador de fallback para encaminhar a solicitação para, caso o sistema não possa encontrar o gerente de segundo nível.

1. Na caixa **Avançar para aprovadores alternativos após a quantidade de dias** , coloque o número de dias que os aprovadores precisam aprovar ou negar uma solicitação. Se nenhum aprovador tiver aprovado ou negado a solicitação antes da duração da solicitação, a solicitação expirará (tempo limite) e o usuário precisará enviar outra solicitação para o pacote de acesso. 

    As solicitações só podem ser encaminhadas a aprovadores alternativos por dia após a duração da solicitação chegar à metade da vida útil, e a decisão do (s) aprovador (es) principal deve expirar após pelo menos 4 dias. Se o tempo limite da solicitação for menor ou igual a 3, não haverá tempo suficiente para encaminhar a solicitação para os aprovadores alternativos. Neste exemplo, a duração da solicitação é de 14 dias. Portanto, a duração da solicitação atinge a metade da vida no dia 7. Portanto, a solicitação não pode ser encaminhada antes do dia 8. Além disso, as solicitações não podem ser encaminhadas no último dia da duração da solicitação. Portanto, no exemplo, a última solicitação pode ser encaminhada é o dia 13.

## <a name="enable-requests"></a>Habilitar solicitações

1. Se você quiser que o pacote de acesso seja disponibilizado imediatamente para usuários na política de solicitação para solicitar, mova a opção Habilitar alternar para **Sim**.

    Você sempre poderá habilitá-lo no futuro depois de concluir a criação do pacote de acesso.

    Se você selecionou **nenhum (somente atribuições diretas de administrador)** e definir habilitar como **não**, os administradores não poderão atribuir diretamente este pacote de acesso.

    ![Pacote de acesso-configuração de política de habilitação de política](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. Clique em **Avançar**.

## <a name="collect-additional-requestor-information-for-approval-preview"></a>Coletar informações adicionais do solicitante para aprovação (versão prévia)

Para garantir que os usuários tenham acesso aos pacotes de acesso corretos, você pode exigir que os solicitantes respondam ao campo de texto personalizado ou a várias perguntas de escolha no momento da solicitação. Há um limite de 20 perguntas por política e um limite de 25 respostas para perguntas de várias opções. Em seguida, as perguntas serão mostradas aos aprovadores para ajudá-las a tomar uma decisão.

1. Vá para a guia **informações do solicitante** e clique na subguia **perguntas** .
 
1. Digite o que você deseja solicitar ao solicitante, também conhecido como cadeia de caracteres de exibição, para a pergunta na caixa de **pergunta** .

    ![Pacote de acesso-configuração de informações do solicitante de habilitação de política](./media/entitlement-management-access-package-approval-policy/add-requestor-info-question.png)

1. Se a comunidade de usuários que precisarem de acesso ao pacote de acesso não tiver todos uma linguagem preferencial comum, você poderá melhorar a experiência para usuários que solicitam acesso no myaccess.microsoft.com. Para melhorar a experiência, você pode fornecer cadeias de caracteres de exibição alternativas para idiomas diferentes. Por exemplo, se o navegador da Web de um usuário estiver definido como espanhol e você tiver cadeias de caracteres de exibição em espanhol configuradas, essas cadeias de caracteres serão exibidas para o usuário solicitante. Para configurar a localização de solicitações, clique em **Adicionar localização**.
    1. Uma vez no painel **Adicionar localizações para pergunta** , selecione o **código de idioma** para o idioma no qual você está localizando a pergunta.
    1. No idioma configurado, digite a pergunta na caixa de **texto localizada** .
    1. Depois de adicionar todas as localizações necessárias, clique em **salvar**.

    ![Pacote de acesso-política-configurar texto localizado](./media/entitlement-management-access-package-approval-policy/add-localization-question.png)

1. Selecione o **formato de resposta** no qual você deseja que os solicitantes respondam. Os formatos de resposta incluem: *texto curto*, *várias opções* e *texto longo*.
 
    ![Pacote de acesso-política-selecione Exibir e editar formato de resposta de múltipla escolha](./media/entitlement-management-access-package-approval-policy/answer-format-view-edit.png)
 
1. Ao selecionar várias opções, clique no botão **Exibir e editar** para configurar as opções de resposta.
    1. Depois de selecionar Exibir e editar, o painel **Exibir/editar pergunta** será aberto.
    1. Digite as opções de resposta que você deseja fornecer ao solicitante ao responder a pergunta nas caixas **valores de resposta** .
    1. Digite quantas respostas forem necessárias e clique em **salvar**.
    
    ![Opções de pacote de acesso-política-inserir várias seleções](./media/entitlement-management-access-package-approval-policy/answer-multiple-choice.png)
  
1. Para exigir que os solicitantes respondam a essa pergunta ao solicitar acesso a um pacote do Access, clique na caixa de seleção em **obrigatório**.

1. Preencha as guias restantes (por exemplo, ciclo de vida) com base em suas necessidades.

Depois de configurar as informações do solicitante em sua política de pacote de acesso, o pode exibir as respostas do solicitante para as perguntas. Para obter orientação sobre como ver as informações do solicitante, consulte [Exibir respostas do solicitante para perguntas (versão prévia)](entitlement-management-request-approve.md#view-requestors-answers-to-questions-preview).

## <a name="next-steps"></a>Próximas etapas
- [Alterar as configurações do ciclo de vida de um pacote de acesso](entitlement-management-access-package-lifecycle-policy.md)
- [Exibir solicitações para um pacote de acesso](entitlement-management-access-package-requests.md)
