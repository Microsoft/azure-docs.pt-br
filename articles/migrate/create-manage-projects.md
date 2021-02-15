---
title: Criar e gerenciar projetos de Migrações para Azure
description: Localize, crie, gerencie e exclua projetos em migrações para Azure.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: f66aef64d68bd5d99d7a27d1382306d9202bd350
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388049"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Criar e gerenciar projetos de Migrações para Azure

Este artigo descreve como criar, gerenciar e excluir projetos de [migrações para Azure](migrate-services-overview.md) . 

A migração do Azure clássica está se aposentando em fevereiro de 2024. Depois de fevereiro de 2024, a versão clássica de migrações para Azure não terá mais suporte e os metadados de inventário no projeto clássico serão excluídos. Se você estiver usando projetos de migração do Azure clássicos, exclua esses projetos e siga as etapas para criar um novo projeto de migrações para Azure. Você não pode atualizar projetos ou componentes de migração do Azure clássicos para as migrações para Azure. Veja as [perguntas frequentes](https://docs.microsoft.com/azure/migrate/resources-faq#i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version) antes de iniciar o processo de criação.

Um projeto das Migrações para Azure é usado para armazenar metadados de descoberta, avaliação e migração coletados do ambiente que você está avaliando ou migrando. Em um projeto, você pode acompanhar os ativos descobertos, criar avaliações e orquestrar as migrações para o Azure.  

## <a name="verify-permissions"></a>Verificar permissões

Verifique se você tem as permissões corretas para criar um projeto de migrações para Azure:

1. No portal do Azure, abra a assinatura relevante e selecione **controle de acesso (iam)**.
2. Em **verificar acesso**, localize a conta relevante e selecione-a para exibir as permissões. Você deve ter permissões de *Colaborador* e *Proprietário*. 


## <a name="create-a-project-for-the-first-time"></a>Criar um projeto pela primeira vez

Configure um novo projeto de migrações para Azure em uma assinatura do Azure.

1. Na portal do Azure, pesquise *migrações para Azure*.
2. Em **Serviços**, selecione **migrações para Azure**.
3. Em **Visão Geral**, selecione **Avaliar e migrar servidores**.

    ![Opção em visão geral para avaliar e migrar servidores](./media/create-manage-projects/assess-migrate-servers.png)

4. Em **servidores**, selecione **criar projeto**.

    ![Botão para iniciar a criação do projeto](./media/create-manage-projects/create-project.png)

5. Em **criar projeto**, selecione a assinatura do Azure e o grupo de recursos. Crie um grupo de recursos, caso ainda não tenha um.
6. Em **Detalhes do Projeto**, especifique o nome do projeto e a geografia em que deseja criá-lo.
    - A geografia é usada somente para armazenar os metadados coletados de computadores locais. Você pode selecionar qualquer região de destino para migração. 
    - Examine as geografias compatíveis para [nuvens públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais](migrate-support-matrix.md#supported-geographies-azure-government).

8. Selecione **Criar**.

   ![Página para inserir configurações de projeto](./media/create-manage-projects/project-details.png)


Aguarde alguns minutos até que o projeto das Migrações para Azure seja implantado.

## <a name="create-a-project-in-a-specific-region"></a>Criar um projeto em uma região específica

No portal, você pode selecionar a geografia na qual deseja criar o projeto. Se você quiser criar o projeto dentro de uma região específica do Azure, usando o comando de API a seguir para criar o projeto.

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>Criar projetos adicionais

Se você já tiver um projeto de migrações para Azure e desejar criar um projeto adicional, faça o seguinte:  

1. No [Portal público do Azure](https://portal.azure.com) ou no [Azure governamental](https://portal.azure.us), pesquise **migrações para Azure**.
2. No painel migrações para Azure > **servidores**, selecione **alterar** no canto superior direito.

   ![Alterar projeto de migrações para Azure](./media/create-manage-projects/switch-project.png)

3. Para criar um novo projeto, selecione **clique aqui**.


## <a name="find-a-project"></a>Localizar um projeto

Localize um projeto da seguinte maneira:

1. Na [portal do Azure](https://portal.azure.com), pesquise *migrações para Azure*.
2. No painel migrações para Azure > **servidores**, selecione **alterar** no canto superior direito.

    ![Alternar para um projeto atual de migrações para Azure](./media/create-manage-projects/switch-project.png)

3. Selecione a assinatura apropriada e o projeto de migrações para Azure.


### <a name="find-a-classic-azure-migrate-project"></a>Encontre um projeto de migrações do Azure clássico

Se você criou o projeto na [versão anterior](migrate-services-overview.md#azure-migrate-versions) do migrações para Azure, encontre-o da seguinte maneira:

1. Na [portal do Azure](https://portal.azure.com), pesquise *migrações para Azure*.
2. No painel migrações para Azure, se você tiver criado um projeto na versão anterior, um banner referenciando projetos mais antigos será exibido. Selecione a faixa.

    ![Acessar projetos existentes](./media/create-manage-projects/access-existing-projects.png)

3. Examine a lista de projetos antigos.


## <a name="delete-a-project"></a>Excluir um projeto

Exclua da seguinte maneira:

1. Abra o grupo de recursos do Azure no qual o projeto foi criado.
2. Na página grupo de recursos, selecione **Mostrar tipos ocultos**.
3. Selecione o projeto de migração que você deseja excluir e seus recursos associados.
    - O tipo de recurso é **Microsoft. Migrate/migrateprojects**.
    - Se o grupo de recursos for usado exclusivamente pelo projeto de migrações para Azure, você poderá excluir o grupo de recursos inteiro.

Observe que:

- Quando você exclui, o projeto e os metadados sobre computadores descobertos são excluídos.
- Se você estiver usando a versão mais antiga do migrações para Azure, abra o grupo de recursos do Azure no qual o projeto foi criado. Selecione o projeto de migração que você deseja excluir (o tipo de recurso é **projeto de migração**).
- Se você estiver usando a análise de dependência com um espaço de trabalho do Azure Log Analytics:
    - Se você anexou um espaço de trabalho Log Analytics à ferramenta de avaliação do servidor, o espaço de trabalho não será excluído automaticamente. O mesmo espaço de trabalho Log Analytics pode ser usado para vários cenários.
    - Se você quiser excluir o espaço de trabalho Log Analytics, faça isso manualmente.
- A exclusão do projeto é irreversível. Objetos excluídos não podem ser recuperados.

### <a name="delete-a-workspace-manually"></a>Excluir um espaço de trabalho manualmente

1. Navegue até o espaço de trabalho do Log Analytics associado ao projeto.

    - Se você não tiver excluído o projeto de migrações para Azure, poderá encontrar o link para o espaço de trabalho na avaliação do **Essentials**  >  **Server**.
       ![Espaço de trabalho da LA ](./media/create-manage-projects/loganalytics-workspace.png) .
       
    - Se você já tiver excluído o projeto de migrações para Azure, selecione **grupos de recursos** no painel esquerdo da portal do Azure e localize o espaço de trabalho.
       
2. [Siga as instruções](../azure-monitor/platform/delete-workspace.md) para excluir o espaço de trabalho.

## <a name="next-steps"></a>Próximas etapas

Adicione ferramentas de [avaliação](how-to-assess.md) ou [migração](how-to-migrate.md) para projetos de migrações para Azure.
