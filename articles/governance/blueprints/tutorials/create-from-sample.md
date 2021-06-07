---
title: 'Tutorial: Amostra do blueprint para o novo ambiente'
description: Neste tutorial, você usa um exemplo de blueprint para criar uma definição dele que configure dois grupos de recursos e uma atribuição de função para cada grupo.
ms.date: 01/27/2021
ms.topic: tutorial
ms.openlocfilehash: aea3760128221d42e092e4ca45200874d54da54a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98915536"
---
# <a name="tutorial-create-an-environment-from-a-blueprint-sample"></a>Tutorial: Criar um ambiente com base em um exemplo de blueprint

Os blueprints de exemplo são amostras do que pode ser feito usando o Azure Blueprints. Cada um é um exemplo com uma intenção ou finalidade específica, mas que não cria um ambiente completo por si só. Cada um serve como um ponto de partida para explorar o uso do Azure Blueprints com várias combinações dos parâmetros, designs e artefatos incluídos.

O tutorial a seguir usa o exemplo de blueprint dos **Grupos de Recursos com RBAC** para demonstrar os diferentes aspectos do serviço do Azure Blueprints. As seguintes etapas serão abordadas:

> [!div class="checklist"]
> - Criar uma nova definição de blueprint com base no exemplo
> - Marcar sua cópia do exemplo como **Publicado**
> - Atribuir a sua cópia do blueprint a uma assinatura existente
> - Inspecionar os recursos implantados para a atribuição
> - Cancelar a atribuição do blueprint para remover os bloqueios

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, será necessária uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-blueprint-definition-from-sample"></a>Criar definição de blueprint com base no exemplo

Primeiro, implemente o exemplo de blueprint. A importação cria um novo blueprint em seu ambiente com base no exemplo.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Na página **Introdução** à esquerda, selecione o botão **Criar** em _Criar um blueprint_.

1. Localize o exemplo de blueprint **Grupos de Recursos com RBAC** em _Outros Exemplos_ e selecione-o.

1. Insira as informações _Básicas_ do exemplo de blueprint:

   - **Nome do blueprint**: forneça um nome para a sua cópia do exemplo de blueprint. Para este tutorial, usaremos o nome _two-rgs-with-role-assignments_.
   - **Localização da definição**: use o botão de reticências e selecione o grupo de gerenciamento ou a assinatura em que deseja salvar sua cópia do exemplo.

1. Selecione a guia _Artefatos_ na parte superior da página ou clique em **Avançar: Artefatos** na parte inferior da página.

1. Examine a lista de artefatos que compõem o exemplo de blueprint. Este exemplo define dois grupos de recursos com os nomes de exibição _ProdRG_ e _PreProdRG_. O nome final e a localização de cada grupo de recursos são definidos durante a atribuição do blueprint. O grupo de recursos _ProdRG_ é atribuído à função _Colaborador_ e o grupo de recursos _PreProdRG_ é atribuído às funções _Proprietário_ e _Leitores_. As funções atribuídas na definição são estáticas, mas o usuário, o aplicativo ou o grupo aos quais a função é atribuída são definidos durante a atribuição do blueprint.

1. Selecione **Salvar Rascunho** quando terminar de examinar o exemplo de blueprint.

Esta etapa cria uma cópia da definição do blueprint de exemplo na assinatura ou no grupo de gerenciamento selecionado. A definição de blueprint salva é gerenciada como qualquer blueprint criado do zero. Você pode salvar o exemplo em seu grupo de gerenciamento ou na assinatura tantas vezes quanto forem necessárias. No entanto, forneça um nome exclusivo para cada cópia.

Quando a notificação do portal **Êxito ao salvar a definição de blueprint** for exibida, avance para a próxima etapa.

## <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

Agora a cópia do exemplo de blueprint foi criada em seu ambiente. Ela é criada no modo **Rascunho** e deve ser **Publicada** antes de ser atribuída e implantada. A cópia do exemplo de blueprint pode ser personalizada de acordo com as necessidades e o ambiente. Neste tutorial, não faremos alterações.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a definição de blueprint _two-rgs-with-role-assignments_ e, em seguida, selecione-o.

1. Selecione **Publicar blueprint** na parte superior da página. No novo painel à direita, forneça a **Versão** como _1.0_ para a cópia do exemplo de blueprint. Essa propriedade será útil se você fizer uma modificação mais tarde. Forneça **Notas de alteração** como: "Primeira versão publicada com base no exemplo de blueprint de grupos de recursos com RBAC". Em seguida, selecione **Publicar** na parte inferior da página.

Esta etapa possibilita atribuir o blueprint a uma assinatura. Alterações poderão ser feitas mesmo após a publicação. As alterações adicionais exigem a publicação com uma novo valor de **Versão** a fim de rastrear as diferenças entre as versões da mesma definição de blueprint.

Quando a notificação do portal **A publicação da definição do blueprint foi bem-sucedida** for exibida, avance para a próxima etapa.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Quando a cópia do exemplo de blueprint for **Publicada** com êxito, ele poderá ser atribuído a uma assinatura do grupo de gerenciamento em que ele foi salvo. Esta é a etapa em que os parâmetros são fornecidos para tornar exclusiva cada implantação da cópia do exemplo de blueprint.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a definição de blueprint _two-rgs-with-role-assignments_ e, em seguida, selecione-o.

1. Selecione **Atribuir blueprint** na parte superior da página de definição de blueprint.

1. Forneça os valores de parâmetro para a atribuição de blueprint:

   - Noções básicas

     - **Assinaturas**: Selecione uma ou mais das assinaturas que estão no grupo de gerenciamento em que você salvou a cópia do exemplo de blueprint. Se você selecionar mais de uma assinatura, será criada uma atribuição para cada uma, usando os parâmetros inseridos.
     - **Nome da atribuição**: Com base no nome da definição de blueprint, esse nome é preenchido automaticamente.
     - **Localização**: Selecione uma região para a identidade gerenciada a ser criada. O Blueprint do Azure usa essa identidade gerenciada para implantar todos os artefatos no blueprint atribuído. Para saber mais, veja [identidades gerenciadas para recursos do Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
       Para este tutorial, selecione _Leste dos EUA 2_.
     - **Versão de definição de blueprint**: Escolha a versão **Publicada**_1.0_ da sua cópia da definição de blueprint de exemplo.

   - Bloquear atribuição

     Selecione o modo de bloqueio de blueprint _Somente Leitura_. Para obter mais informações, consulte [bloqueio de recursos de projetos](../concepts/resource-locking.md).

   - Identidade Gerenciada

     Deixe a opção padrão _Atribuída pelo sistema_. Para obter mais informações, confira [identidades gerenciadas](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Parâmetros do artefato

     Os parâmetros definidos nesta seção se aplicam ao artefato sob o qual ele está definido. Esses são [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters), pois são definidos durante a atribuição do blueprint. Para cada artefato, defina o valor de parâmetro para o que está definido na coluna **Valor**. Para `{Your ID}`, selecione sua conta de usuário do Azure.

     |Nome do artefato|Tipo de artefato|Nome do parâmetro|Valor|Descrição|
     |-|-|-|-|-|
     |Grupo de recursos ProdRG|Resource group|Nome|ProductionRG|Define o nome do primeiro grupo de recursos.|
     |Grupo de recursos ProdRG|Resource group|Location|Oeste dos EUA 2|Define a localização do primeiro grupo de recursos.|
     |Colaborador|Atribuição de função|Usuário ou Grupo|{Sua ID}|Define a qual usuário ou grupo conceder a atribuição de função _Colaborador_ dentro do primeiro grupo de recursos.|
     |Grupo de recursos PreProdRG|Resource group|Nome|PreProductionRG|Define o nome do segundo grupo de recursos.|
     |Grupo de recursos PreProdRG|Resource group|Location|Oeste dos EUA|Define a localização do segundo grupo de recursos.|
     |Proprietário|Atribuição de função|Usuário ou Grupo|{Sua ID}|Define a qual usuário ou grupo conceder a atribuição de função _Proprietário_ dentro do segundo grupo de recursos.|
     |Leitores|Atribuição de função|Usuário ou Grupo|{Sua ID}|Define a qual usuário ou grupo conceder a atribuição de função _Leitor_ dentro do segundo grupo de recursos.|

1. Depois que todos os parâmetros forem inseridos, selecione **Atribuir** na parte inferior da página.

Esta etapa implanta os recursos definidos e configura a **Atribuição de Bloqueio** selecionada. Os bloqueios de blueprint podem levar até 30 minutos para serem aplicados.

Quando a notificação do portal **A atribuição da definição do blueprint foi bem-sucedida** for exibida, avance para a próxima etapa.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Inspecionar recursos implantados pela atribuição

A atribuição de blueprint cria e controla os artefatos definidos na definição de blueprint. Podemos ver o status dos recursos na página de atribuição de blueprint e examinando os recursos diretamente.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Blueprints atribuídos** à esquerda. Use os filtros para localizar a atribuição de blueprint _Assignment-two-rgs-with-role-assignments_ e, em seguida, selecione-o.

   Nessa página, podemos ver que a atribuição foi bem-sucedida e a lista de recursos criados juntamente com o respectivo estado de bloqueio do blueprint. Se a atribuição for atualizada, a lista suspensa **Operação de atribuição** mostrará detalhes sobre a implantação de cada versão de definição. Cada recurso listado que foi criado pode ser selecionado e abre a página de propriedades desses recursos.

1. Selecione o grupo de recursos **ProductionRG**.

   Vemos que o nome do grupo de recursos é **ProductionRG** e não o nome de exibição do artefato _ProdRG_. Esse nome corresponde ao valor definido durante a atribuição de blueprint.

1. Selecione a página **Controle de acesso (IAM)** à esquerda e, em seguida, a guia **Atribuições de função**.

   Aqui podemos ver que foi concedida a função _Colaborador_ à sua conta, no escopo de _Este recurso_. A atribuição de blueprint _Assignment-two-rgs-with-role-assignments_ tem a função _Proprietário_, pois foi usada para criar o grupo de recursos. Essas permissões também são usadas para gerenciar recursos com bloqueios de blueprint configurados.

1. Na trilha do portal do Azure, selecione **Assignment-two-rgs-with-role-assignments** para retornar uma página e, em seguida, selecione o grupo de recursos **PreProductionRG**.

1. Selecione a página **Controle de acesso (IAM)** à esquerda e, em seguida, a guia **Atribuições de função**.

   Aqui vemos que as funções _Proprietário_ e _Leitor_ foram concedidas à sua conta, no escopo de _Este recurso_. A atribuição de blueprint também tem a função _Proprietário_ assim como o primeiro grupo de recursos.

1. Selecione a guia **Negar atribuições**.

   A atribuição de blueprint criou uma [atribuição de negação](../../../role-based-access-control/deny-assignments.md) no grupo de recursos implantado para impor o modo de bloqueio _Somente Leitura_ do blueprint. A atribuição de negação impede que alguém com os direitos adequados execute ações específicas na guia _Atribuições de função_. A atribuição de negação afeta _Todas as entidades_.

1. Selecione a atribuição de negação e, em seguida, selecione a página **Permissões Negadas** à esquerda.

   A atribuição de negação está impedindo todas as operações com o **\* *_ e a configuração _* Ação**, mas permite o acesso de leitura excluindo **\*/read** por meio de **NotActions**.

1. Na trilha do portal do Azure, selecione **PreProductionRG – Controle de acesso (IAM)** . Depois, selecione a página **Visão geral** à esquerda e, em seguida, o botão **Excluir grupo de recursos**. Insira o nome _PreProductionRG_ para confirmar a exclusão e selecione **Excluir** na parte inferior do painel.

   A notificação do portal, **Falha ao excluir grupo de recursos PreProductionRG**, é exibida. O erro informa que, embora sua conta tenha permissão para excluir o grupo de recursos, o acesso é negado pela atribuição de blueprint. Lembre-se de que selecionamos o modo de bloqueio de blueprint _Somente Leitura_ durante a atribuição de blueprint. O bloqueio de blueprint impede que uma conta com permissão, até mesmo _Proprietário_, exclua o recurso. Para obter mais informações, consulte [bloqueio de recursos de projetos](../concepts/resource-locking.md).

Estas etapas mostram que nossos recursos foram criados conforme definido e os bloqueios de blueprint impediram a exclusão indesejada, até mesmo usando uma conta com permissão.

## <a name="unassign-the-blueprint"></a>Cancelar a atribuição do blueprint

A última etapa é remover a atribuição do blueprint e os recursos que ele implantou.
A remoção da atribuição não removerá os artefatos implantados.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Blueprints atribuídos** à esquerda. Use os filtros para localizar a atribuição de blueprint _Assignment-two-rgs-with-role-assignments_ e, em seguida, selecione-o.

1. Selecione o botão **Cancelar atribuição do blueprint** na parte superior da página. Leia o aviso na caixa de diálogo de confirmação e, em seguida, selecione **OK**.

   Com a atribuição de blueprint removida, os bloqueios de blueprint também são removidos. Os recursos criados já podem ser excluídos por uma conta com permissões.

1. Selecione **Grupos de recursos** no menu do Azure e, em seguida, selecione **ProductionRG**.

1. Selecione a página **Controle de acesso (IAM)** à esquerda e, em seguida, a guia **Atribuições de função**.

A segurança de cada grupo de recursos ainda tem as atribuições de função implantadas, mas a atribuição de blueprint não tem mais acesso de _Proprietário_.

Quando a notificação do portal **A remoção da atribuição do blueprint foi bem-sucedida** for exibida, avance para a próxima etapa.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar este tutorial, exclua os seguintes recursos:

- Grupo de recursos _ProductionRG_
- Grupo de recursos _PreProductionRG_
- Definição de blueprint _two-rgs-with-role-assignments_

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar um novo blueprint com base em uma definição de exemplo. Para saber mais sobre o Azure Blueprints, prossiga para o artigo de ciclo de vida do blueprint.

> [!div class="nextstepaction"]
> [Saiba mais sobre o ciclo de vida do blueprint](../concepts/lifecycle.md)