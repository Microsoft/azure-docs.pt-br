---
title: Criar e gerenciar projetos
description: Localize, crie, gerencie e exclua projetos em migrações para Azure.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: cb0ac41d469ad9a7670ce4b1bae23b315a17dc38
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871038"
---
# <a name="create-and-manage-projects"></a>Criar e gerenciar projetos

Este artigo descreve como criar, gerenciar e excluir [projetos](migrate-services-overview.md). 

As Migrações para Azure (versão clássica) serão desativadas em fevereiro de 2024. Depois de fevereiro de 2024, a versão clássica de migrações para Azure não terá mais suporte e os metadados de inventário no projeto clássico serão excluídos. Se você estiver usando projetos clássicos, exclua esses projetos e siga as etapas para criar um novo projeto. Não é possível atualizar projetos ou componentes clássicos para as migrações para Azure. Veja as [perguntas frequentes](./resources-faq.md#i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version) antes de iniciar o processo de criação.

O projeto é usado para armazenar metadados de descoberta, avaliação e migração coletados do ambiente que você está avaliando ou migrando. Em um projeto, você pode acompanhar os ativos descobertos, criar avaliações e orquestrar as migrações para o Azure.  

## <a name="verify-permissions"></a>Verificar permissões

Verifique se você tem as permissões corretas para criar um projeto:

1. No portal do Azure, abra a assinatura relevante e selecione **controle de acesso (iam)**.
2. Em **verificar acesso**, localize a conta relevante e selecione-a para exibir as permissões. Você deve ter permissões de *Colaborador* e *Proprietário*. 


## <a name="create-a-project-for-the-first-time"></a>Criar um projeto pela primeira vez

Configure um novo projeto em uma assinatura do Azure.

1. Na portal do Azure, pesquise *migrações para Azure*.
2. Em **Serviços**, selecione **migrações para Azure**.
3. Em **Visão Geral**, selecione **Avaliar e migrar servidores**.

    :::image type="content" source="./media/create-manage-projects/assess-migrate-servers.png" alt-text="Opção em visão geral para avaliar e migrar servidores":::

4. Em **servidores**, selecione **criar projeto**.

    :::image type="content" source="./media/create-manage-projects/create-project.png" alt-text="Botão para iniciar a criação do projeto":::

5. Em **criar projeto**, selecione a assinatura do Azure e o grupo de recursos. Crie um grupo de recursos, caso ainda não tenha um.
6. Em **Detalhes do Projeto**, especifique o nome do projeto e a geografia em que deseja criá-lo.
    - A geografia é usada somente para armazenar os metadados coletados de servidores locais. Você pode selecionar qualquer região de destino para migração. 
    - Examine as geografias compatíveis para [nuvens públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais](migrate-support-matrix.md#supported-geographies-azure-government).

8. Selecione **Criar**.

     :::image type="content" source="./media/create-manage-projects/project-details.png" alt-text="Página para inserir configurações de projeto":::


Aguarde alguns minutos para que o projeto seja implantado.

## <a name="create-a-project-in-a-specific-region"></a>Criar um projeto em uma região específica

No portal, você pode selecionar a geografia na qual deseja criar o projeto. Se você quiser criar o projeto dentro de uma região específica do Azure, usando o comando de API a seguir para criar o projeto.

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>Criar projetos adicionais

Se você já tiver um projeto e quiser criar um projeto adicional, faça o seguinte:  

1. No [Portal público do Azure](https://portal.azure.com) ou no [Azure governamental](https://portal.azure.us), pesquise **migrações para Azure**.
2. No painel migrações para Azure > **servidores**, selecione **alterar** no canto superior direito.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Alterar projeto":::

3. Para criar um novo projeto, selecione **clique aqui**.


## <a name="find-a-project"></a>Localizar um projeto

Localize um projeto da seguinte maneira:

1. Na [portal do Azure](https://portal.azure.com), pesquise *migrações para Azure*.
2. No painel migrações para Azure > **servidores**, selecione **alterar** no canto superior direito.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Alternar para um projeto existente":::

3. Selecione a assinatura e o projeto apropriados.


### <a name="find-a-classic-project"></a>Localizar um projeto clássico

Se você criou o projeto na [versão anterior](migrate-services-overview.md#azure-migrate-versions) do migrações para Azure, encontre-o da seguinte maneira:

1. Na [portal do Azure](https://portal.azure.com), pesquise *migrações para Azure*.
2. No painel migrações para Azure, se você tiver criado um projeto na versão anterior, um banner referenciando projetos mais antigos será exibido. Selecione a faixa.

    :::image type="content" source="./media/create-manage-projects/access-existing-projects.png" alt-text="Acessar projetos existentes":::

3. Examine a lista de projetos antigos.


## <a name="delete-a-project"></a>Excluir um projeto

Exclua da seguinte maneira:

1. Abra o grupo de recursos do Azure no qual o projeto foi criado.
2. Na página grupo de recursos, selecione **Mostrar tipos ocultos**.
3. Selecione o projeto que você deseja excluir e seus recursos associados.
    - O tipo de recurso é **Microsoft. Migrate/migrateprojects**.
    - Se o grupo de recursos for usado exclusivamente pelo projeto, você poderá excluir o grupo de recursos inteiro.

Observe que:

- Quando você exclui, o projeto e os metadados sobre os servidores descobertos são excluídos.
- Se você estiver usando a versão mais antiga do migrações para Azure, abra o grupo de recursos do Azure no qual o projeto foi criado. Selecione o projeto que você deseja excluir (o tipo de recurso é **projeto de migração**).
- Se você estiver usando a análise de dependência com um espaço de trabalho do Azure Log Analytics:
    - Se você anexou um espaço de trabalho Log Analytics à ferramenta de avaliação do servidor, o espaço de trabalho não será excluído automaticamente. O mesmo espaço de trabalho Log Analytics pode ser usado para vários cenários.
    - Se você quiser excluir o espaço de trabalho Log Analytics, faça isso manualmente.
- A exclusão do projeto é irreversível. Objetos excluídos não podem ser recuperados.

### <a name="delete-a-workspace-manually"></a>Excluir um espaço de trabalho manualmente

1. Navegue até o espaço de trabalho do Log Analytics associado ao projeto.

    - Se você não tiver excluído o projeto, poderá encontrar o link para o espaço de trabalho na avaliação do **Essentials**  >  **Server**.
    :::image type="content" source="./media/create-manage-projects/loganalytics-workspace.png" alt-text="Workspace do LA":::
       
    - Se você já tiver excluído o projeto, selecione **grupos de recursos** no painel esquerdo da portal do Azure e localize o espaço de trabalho.
       
2. [Siga as instruções](../azure-monitor/logs/delete-workspace.md) para excluir o espaço de trabalho.

## <a name="next-steps"></a>Próximas etapas

Adicione ferramentas de [avaliação](how-to-assess.md) ou [migração](how-to-migrate.md) a projetos.