---
title: Criar e compartilhar painéis do Portal do Azure | Microsoft Docs
description: Este artigo descreve como criar, personalizar, publicar e compartilhar painéis no portal do Azure.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: mtillman
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 07/01/2019
ms.author: kfollis
ms.openlocfilehash: 8dd1349ca9ab62484eb6693291e3b869ff079dc1
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537342"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Criar e compartilhar painéis no Portal do Azure

Os painéis fornecem uma maneira de criar uma exibição organizada e focada no portal do Azure de seus recursos de nuvem. Use painéis como um espaço de trabalho onde você pode iniciar as tarefas para as operações diárias e monitorar recursos rapidamente.  Crie painéis personalizados com base em projetos, tarefas ou funções de usuário, por exemplo.  O portal do Azure fornece um painel padrão como um ponto de partida. Você pode editar o painel padrão, criar e personalizar os painéis adicionais e publicar e compartilhar painéis para disponibilizá-las a outros usuários. Este artigo descreve como criar um novo painel, personalizar a interface e publicar e compartilhar dashboards.

## <a name="create-a-new-dashboard"></a>Crie um novo painel

Neste exemplo, criamos um novo painel privado e atribua um nome. Siga estas etapas para começar a usar:

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Selecione **Dashboard** na seção superior da barra lateral esquerda. O modo de exibição padrão já pode ser definido para o painel.
1. Selecione **+ novo painel**.

    ![Captura de tela do painel padrão](./media/azure-portal-dashboards/dashboard-new.png)

4. Essa ação abre o **Galeria de blocos**, do qual você selecionará o lado a lado e uma grade vazia onde você organizará os blocos.

    ![Captura de tela da Galeria de blocos e de grade vazia](./media/azure-portal-dashboards/dashboard-name.png)

5. Selecione o **meu painel** texto no painel de controle de rótulo e digite um nome que ajudará você a identificar facilmente o painel personalizado.
1. Selecione **personalização concluída** no cabeçalho da página para sair do modo de edição.

O modo de exibição de painel agora mostra o painel vazio. Selecione na lista suspensa ao lado do nome do dashboard para ver os painéis disponíveis para você – a lista pode incluir painéis que outros usuários criados e compartilhados.

## <a name="edit-a-dashboard"></a>Editar um painel

Agora, vamos editar o painel para adicionar, redimensionar e organizar lado a lado que representam os recursos do Azure.

### <a name="add-tiles"></a>Adicionar blocos

Para adicionar blocos em um painel, siga estas etapas:
1. Selecione ![ícone Editar](./media/azure-portal-dashboards/dashboard-edit-icon.png) **editar** do cabeçalho da página.

    ![Captura de tela do painel de edição de realce](./media/azure-portal-dashboards/dashboard-edit.png)

2. Procurar o **Galeria de blocos** ou use o campo de pesquisa para localizar o bloco que você deseja.
1. Selecione **adicionar** para adicionar automaticamente no bloco ao painel de controle com um tamanho e local padrão. Ou, arraste o bloco na grade e colocá-lo onde desejar.

Muitas páginas de recurso (também conhecido como "folhas") incluem um ícone de tachinha na barra de comandos. Se você selecionar o ícone, um bloco que representa a página de código-fonte é fixado no painel que está ativo no momento. Esse método é uma maneira alternativa para adicionar blocos em seu painel.

![Captura de tela da barra de comandos de página com ícone de pino](./media/azure-portal-dashboards/dashboard-pin-blade.png)

> [!TIP]
> Se você trabalha com mais de uma organização, adicione a **identidade da organização** bloco ao painel para mostrar claramente qual organização os recursos pertencem a.
>
>
### <a name="resize-or-rearrange-tiles"></a>Redimensionar ou reorganizar os blocos
Para alterar o tamanho de um bloco ou reorganizar os blocos em um painel, siga estas etapas:

1. Selecione ![ícone Editar](./media/azure-portal-dashboards/dashboard-edit-icon.png) **editar** do cabeçalho da página.
1. Selecione o menu de contexto no canto superior direito de um bloco. Em seguida, escolha um tamanho de bloco. Blocos que oferecem suporte a qualquer tamanho também incluem um "identificador" no canto inferior direito que permite que você arraste o bloco para o tamanho desejado.

   ![Captura de tela do painel de controle com o menu de tamanho de bloco aberto](./media/azure-portal-dashboards/dashboard-tile-resize.png)

3. Selecione um bloco e arraste-o para um novo local na grade para organizar seu painel.

### <a name="additional-tile-configuration"></a>Configuração de imagem lado a lado

Alguns blocos talvez seja necessário mais de configuração para mostrar as informações desejadas. Por exemplo, o **gráfico de métricas** lado a lado deve ser configurado para exibir uma métrica de **do Azure Monitor**. Você também pode personalizar os dados do bloco para substituir configurações de hora padrão do painel.

Qualquer bloco que precisa ser configurado exibe uma **configurar bloco** até que você personalize o bloco do banner. Selecione essa faixa, em seguida, fazer a configuração necessária.

![Captura de tela do bloco que requer configuração](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Um bloco de markdown permite exibir o conteúdo personalizado e estático em seu painel. Isso poderia ser instruções básicas, uma imagem, um conjunto de hiperlinks ou até mesmo informações de contato. Para obter mais informações sobre como usar um bloco de markdown, consulte [usar um bloco de markdown personalizada](azure-portal-markdown-tile.md).
>
>
### <a name="customize-tile-data"></a>Personalizar dados do bloco

Dados no painel mostram automaticamente a atividade nas últimas 24 horas. Para mostrar um período de tempo diferente para apenas esse bloco, siga estas etapas:

1. Selecione o ![ícone de filtro](./media/azure-portal-dashboards/dashboard-filter.png) ícone de filtro no canto superior esquerdo do bloco ou selecione **personalizar dados de bloco** no menu de contexto.

   ![Captura de tela do menu de contexto do bloco](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

2. Selecione a caixa de seleção **substituem as configurações de tempo do painel de controle no nível de bloco**.

   ![Captura de tela da caixa de diálogo para definir configurações de tempo de bloco](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

3. Escolha o período de tempo para ser mostrado para esse bloco. Você pode escolher entre os últimos 30 minutos para os últimos 30 dias ou definir um intervalo personalizado.
1. Escolha a granularidade de tempo para exibir. Você pode mostrar em qualquer lugar de incrementos de um minuto para um mês.
1. Escolha **Aplicar**.

## <a name="delete-a-tile"></a>Excluir um bloco

Para remover um bloco de um painel, siga estas etapas:

* Selecione o menu de contexto no canto superior direito do bloco e selecione **remover do dashboard**. Ou,
* Selecione ![ícone Editar](./media/azure-portal-dashboards/dashboard-edit-icon.png) **editar** para entrar no modo de personalização. Passe o mouse no canto superior direito do bloco e, em seguida, selecione a ![excluir ícone](./media/azure-portal-dashboards/dashboard-delete-icon.png) excluir ícone para remover o bloco do painel.

   ![Captura de tela mostrando como remover o bloco de painel](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Clonar um painel

Para usar um painel existente como modelo para um novo painel, siga estas etapas:

1. Certifique-se de que a exibição do painel está mostrando o painel que você deseja copiar.
1. No cabeçalho da página, selecione ![ícone de clone](./media/azure-portal-dashboards/dashboard-clone.png) **Clone**.
1. Uma cópia do painel, denominado "clonar da *seu nome do painel*" é aberto no modo de edição. Use as etapas anteriores neste artigo para renomear e personalizar o painel.

## <a name="publish-and-share-a-dashboard"></a>Publicar e compartilhar um dashboard

Quando você cria um painel, é privado por padrão, o que significa que você está a única pessoa que pode vê-lo. Para disponibilizar a outros painéis, você pode compartilhá-los com outros usuários. Primeiro, você precisa publicar o painel como um recurso do Azure. Para publicar e compartilhar um painel personalizado, siga estas etapas:

1. Selecione ![ícone compartilhar](./media/azure-portal-dashboards/dashboard-share-icon.png) **compartilhar** do cabeçalho da página. O **compartilhamento + controle de acesso** formulário é exibido.
1. Verifique se o nome do painel correto é mostrado.
1. Selecione uma **o nome da assinatura**. Os usuários com acesso à assinatura podem usar o painel compartilhado. Acesso aos recursos representados por blocos individuais é determinado pelo controle de acesso baseado em função do Azure.
1. Marque a caixa de seleção para publicar esse painel para o grupo de recursos 'painéis' para a assinatura selecionada. Ou, desmarque a caixa de seleção e escolha publicar em um grupo de recursos existente em vez disso.
1. Escolha um local para o recurso do painel. É recomendável que você localize o dashboard com outros recursos. Observação: se você escolher de grupos de recursos existentes, o painel está localizado automaticamente com o grupo de recursos.
1. Selecione **Publicar**.

   ![Captura de tela da caixa de diálogo de publicação do painel](./media/azure-portal-dashboards/dashboard-publish.png)

### <a name="set-access-control-on-a-shared-dashboard"></a>Configurar o controle de acesso em um painel compartilhado

Depois que o painel é publicado, gerencie quem tem acesso ao painel seguindo estas etapas:

1. No **compartilhamento + controle de acesso** painel, selecione **gerenciar usuários**.

   ![Captura de tela do painel de compartilhamento + acesso controle caixa de diálogo](./media/azure-portal-dashboards/dashboard-share-access-control.png)

2. O **controle de acesso** página será aberta. Nessa página, você pode revisar o nível de acesso para alguém ou adicionar uma nova atribuição de função. Quando você adiciona uma atribuição de função, você está concedendo permissões para o painel.

> [!NOTE]
> Os blocos são representativos modos de exibição de recursos em sua organização. Acesso a recursos é gerenciado por meio da atribuição de controle de acesso baseado em função e as permissões são herdadas da assinatura até o recurso. Fornecendo acesso a um painel automaticamente não atribuir permissões para os recursos mostrados no painel. Para obter mais informações sobre as permissões para os painéis compartilhados e controle de acesso baseado em função para recursos, consulte [compartilhar dashboards com controle de acesso baseado em função](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Abrir um painel compartilhado

Para localizar e abrir um painel compartilhado, siga estas etapas:

1. Selecione na lista suspensa ao lado do nome do dashboard.
1. Selecione na lista exibida de painéis ou **procurar todos os painéis** se o painel que você deseja abrir não estiver listado.

   ![Captura de tela do menu de seleção do painel](./media/azure-portal-dashboards/dashboard-browse.png)

3. No **tipo** campo, selecione **painéis compartilhados**.
1. Selecione uma ou mais assinaturas. Você também pode inserir texto para filtrar os painéis de controle por nome.
1. Selecione um dashboard na lista de painéis compartilhados.

## <a name="delete-a-dashboard"></a>Excluir um painel

Para excluir permanentemente um painel compartilhado ou particular, siga estas etapas:

1. Selecione o painel que você deseja excluir na lista suspensa ao lado do nome do dashboard.
1. Selecione ![ícone Excluir](./media/azure-portal-dashboards/dashboard-delete-icon.png) **excluir** do cabeçalho da página.
1. Para um painel privado, selecione **Okey** na caixa de diálogo de confirmação para remover o painel. Para um painel compartilhado, na caixa de diálogo de confirmação, selecione a caixa de seleção para confirmar que o painel publicado deixará de ser visualizado por outras pessoas. Depois, selecione **OK**.

   ![Captura de tela de confirmação de exclusão](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>Próximas etapas

* [Compartilhar dashboards com controle de acesso baseado em função](azure-portal-dashboard-share-access.md)
* [Criar programaticamente os painéis do Azure](azure-portal-dashboards-create-programmatically.md)
