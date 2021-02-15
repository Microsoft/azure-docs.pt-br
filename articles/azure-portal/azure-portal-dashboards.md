---
title: Criar e compartilhar painéis no Portal do Azure
description: Este artigo descreve como criar, personalizar, publicar e compartilhar painéis no portal do Azure.
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 10c322c9d9d18f7b79a4419bd9efa2e366a5b7c3
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745818"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Criar e compartilhar painéis no Portal do Azure

Os painéis são uma exibição focada e organizada dos seus recursos de nuvem no portal do Azure. Use os painéis como um workspace no qual você possa iniciar rapidamente tarefas para operações diárias e monitorar recursos. Crie painéis personalizados com base em projetos, tarefas ou funções de usuário, por exemplo.

O portal do Azure fornece um painel padrão como um ponto de partida. Você pode editar o painel padrão. Crie e personalize painéis adicionais e publique e compartilhe painéis para torná-los disponíveis para outros usuários. Este artigo descreve como criar um novo painel, personalizar a interface e publicar e compartilhar painéis.

## <a name="create-a-new-dashboard"></a>Crie um novo painel

Neste exemplo, criamos um novo painel privado e atribuímos um nome. Siga estas etapas para começar:

1. Entre no [portal do Azure](https://portal.azure.com).

1. No menu portal do Azure, selecione **painel**. O modo de exibição padrão já pode estar definido como Dashboard.

    ![Abrir o painel](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. Selecione **novo painel**.

    ![Captura de tela do novo painel](./media/azure-portal-dashboards/create-new-dashboard.png)

    Essa ação abre a **Galeria** de blocos, na qual você selecionará blocos e uma grade vazia na qual você organizará os blocos.

    ![Captura de tela da Galeria de blocos e da grade vazia](./media/azure-portal-dashboards/dashboard-name.png)

1. Selecione o texto **meu painel** no rótulo do painel e insira um nome que o ajudará a identificar facilmente o painel personalizado.

1. Selecione **personalização concluída** no cabeçalho da página para sair do modo de edição.

A exibição de painel agora mostra o novo painel. Selecione a seta ao lado do nome do painel para ver os painéis disponíveis para você. A lista pode incluir painéis que outros usuários criaram e compartilharam.

## <a name="edit-a-dashboard"></a>Editar um painel

Agora, vamos editar o painel para adicionar, redimensionar e organizar os blocos que representam os recursos do Azure.

### <a name="add-tiles-from-the-dashboard"></a>Adicionar blocos do painel

Para adicionar blocos a um painel, siga estas etapas:

1. Selecione ![ Editar ícone ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Editar** no cabeçalho da página.

    ![Captura de tela do realce de painel Editar](./media/azure-portal-dashboards/dashboard-edit.png)

1. Navegue pela **Galeria de blocos** ou use o campo de pesquisa para localizar o bloco desejado.

1. Selecione **Adicionar** para adicionar o bloco ao painel com um tamanho e um local padrão. Ou arraste o bloco até a grade e coloque-o onde desejar.

> [!TIP]
> Se você trabalhar com mais de uma organização, adicione o bloco **identidade da organização** ao seu painel para mostrar claramente a qual organização os recursos pertencem.

### <a name="add-tiles-from-a-resource-page"></a>Adicionar blocos de uma página de recursos

Há uma maneira alternativa de adicionar blocos ao seu painel. Muitas páginas de recursos incluem um ícone de pino na barra de comandos. Se você selecionar o ícone, um bloco que representa a página de origem será fixado no painel ativo no momento. 

![Captura de tela da barra de comandos da página com ícone de fixação](./media/azure-portal-dashboards/dashboard-pin-blade.png)

### <a name="resize-or-rearrange-tiles"></a>Redimensionar ou reorganizar blocos

Para alterar o tamanho de um bloco ou reorganizar os blocos em um painel, siga estas etapas:

1. Selecione ![ Editar ícone ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Editar** no cabeçalho da página.

1. Selecione o menu de contexto no canto superior direito de um bloco. Em seguida, escolha um tamanho de bloco. Blocos que dão suporte a qualquer tamanho também incluem um "identificador" no canto inferior direito que permite que você arraste o bloco para o tamanho desejado.

    ![Captura de tela do painel com o menu tamanho do bloco aberto](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. Selecione um bloco e arraste-o para um novo local na grade para organizar seu painel.

### <a name="additional-tile-configuration"></a>Configuração de bloco adicional

Alguns blocos podem exigir mais configuração para mostrar as informações desejadas. Por exemplo, o bloco de **gráfico de métricas** precisa ser configurado para exibir uma métrica de **Azure monitor**. Você também pode personalizar os dados do bloco para substituir as configurações de tempo padrão do painel.

Qualquer bloco que precise ser configurado exibe uma faixa de **blocos de configuração** até que você personalize o bloco. Para personalizar o bloco:

1. Selecione **personalização concluída** no cabeçalho da página para sair do modo de edição.

1. Selecione a faixa e, em seguida, faça a configuração necessária.

    ![Captura de tela do bloco que requer configuração](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Um bloco de redução permite que você exiba conteúdo estático e personalizado em seu painel. Isso pode ser instruções básicas, uma imagem, um conjunto de hiperlinks ou até mesmo informações de contato. Para obter mais informações sobre como usar um bloco de redução, consulte [usar um bloco de redução nos painéis do Azure para mostrar o conteúdo personalizado](azure-portal-markdown-tile.md).

### <a name="customize-tile-data"></a>Personalizar dados de bloco

Os dados no painel mostram automaticamente a atividade das últimas 24 horas. Para mostrar um período de tempo diferente apenas para este bloco, siga estas etapas:

1. Selecione **personalizar dados de bloco** no menu de contexto ou no ![ ](./media/azure-portal-dashboards/dashboard-filter.png) filtro ícone de filtro no canto superior esquerdo do bloco.

    ![Captura de tela do menu de contexto do bloco](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. Marque a caixa de seleção para **substituir as configurações de hora do painel no nível do bloco**.

    ![Captura de tela da caixa de diálogo para definir as configurações de tempo do bloco](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Escolha o período de tempo a ser mostrado para este bloco. Você pode escolher entre os últimos 30 minutos até os últimos 30 dias ou definir um intervalo personalizado.

1. Escolha a granularidade de tempo a ser exibida. Você pode mostrar qualquer lugar de incrementos de um minuto para um mês.

1. Selecione **Aplicar**.

## <a name="delete-a-tile"></a>Excluir um bloco

Para remover um bloco de um painel, siga estas etapas:

* Selecione o menu de contexto no canto superior direito do bloco e, em seguida, selecione **remover do painel**. Ou,

* Selecione ![ Editar ícone ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Editar** para entrar no modo de personalização. Passe o mouse no canto superior direito do bloco e, em seguida, selecione o ícone Excluir ícone ![ ](./media/azure-portal-dashboards/dashboard-delete-icon.png) Excluir para remover o bloco do painel.

   ![Captura de tela mostrando como remover bloco do painel](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Clonar um painel

Para usar um painel existente como modelo para um novo painel, siga estas etapas:

1. Verifique se o modo de exibição de painel está mostrando o painel que você deseja copiar.

1. No cabeçalho da página, selecione clonar ![ ícone ](./media/azure-portal-dashboards/dashboard-clone.png) **clonar**.

1. Uma cópia do painel, chamada **clone do** *seu nome do painel* , é aberta no modo de edição. Use as etapas anteriores neste artigo para renomear e personalizar o painel.

## <a name="publish-and-share-a-dashboard"></a>Publicar e compartilhar um painel

Quando você cria um painel, ele é privado por padrão, o que significa que você é o único que pode vê-lo. Para disponibilizar os painéis para outras pessoas, você pode publicá-los e compartilhá-los. Para obter mais informações, confira [Compartilhar painéis do Azure usando o controle de acesso baseado em função do Azure](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Abrir um painel compartilhado

Para localizar e abrir um painel compartilhado, siga estas etapas:

1. Selecione a seta ao lado do nome do painel.

1. Selecione na lista de painéis exibida. Se o painel que você deseja abrir não estiver listado:

    1. Selecione **procurar todos os painéis**.

        ![Captura de tela do menu de seleção do painel](./media/azure-portal-dashboards/dashboard-browse.png)

    1. No campo **tipo** , selecione **painéis compartilhados**.

        ![Captura de tela de todos os painéis menu de seleção](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. Selecione uma ou mais assinaturas. Você também pode inserir texto para filtrar os painéis por nome.

    1. Selecione um painel na lista de painéis compartilhados.

## <a name="delete-a-dashboard"></a>Excluir um painel

Para excluir permanentemente um painel particular ou compartilhado, siga estas etapas:

1. Selecione o painel que você deseja excluir da lista ao lado do nome do painel.

1. Selecione ![ excluir ícone ](./media/azure-portal-dashboards/dashboard-delete-icon.png) **excluir** no cabeçalho da página.

1. Para um painel particular, selecione **OK** na caixa de diálogo de confirmação para remover o painel. Para um painel compartilhado, na caixa de diálogo de confirmação, marque a caixa de seleção para confirmar se o painel publicado não será mais visível para outras pessoas. Depois, selecione **OK**.

    ![Captura de tela da confirmação de exclusão](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>Próximas etapas

* [Compartilhar painéis do Azure usando o controle de acesso baseado em função do Azure](azure-portal-dashboard-share-access.md)
* [Criar programaticamente os painéis do Azure](azure-portal-dashboards-create-programmatically.md)
