---
title: 'Tutorial: atribuir acesso com Cloudyn no Azure'
description: Neste tutorial, você aprende como atribuir acesso a dados do Cloudyn com contas de usuário que definem níveis de acesso a entidades.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.custom: seodec18
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: a3c10d95a57ea81f269af8f8f1cad49fc1c1b845
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131251"
---
# <a name="tutorial-assign-access-to-cloudyn-data"></a>Tutorial: atribuir acesso aos dados do Cloudyn

Acesso a dados do Cloudyn é fornecido pelo usuário ou o gerenciamento de entidade. As contas de usuário da Cloudyn determinam o acesso a *entidades* e a funções administrativas. Há dois tipos de acesso: administrador e usuário. A menos que seja modificado para cada usuário, o acesso de administrador permite o uso irrestrito de todas as funções no portal da Cloudyn, incluindo: gerenciamento de usuários, gerenciamento de listas de destinatários e acesso de entidade raiz a todos os dados de entidade. O acesso do usuário é indicado para que os usuários finais exibam relatórios e criem relatórios usando o acesso que têm aos dados de entidade.

As entidades são usadas para refletir a estrutura hierárquica da sua organização de negócios. Elas identificam os departamentos, as divisões e as equipes da organização na Cloudyn. A hierarquia de entidades ajuda a acompanhar com precisão os gastos de cada entidade.

Quando você registrou sua conta ou um contrato do Azure, uma conta com permissão de administrador foi criada na Cloudyn, portanto, você pode executar todas as etapas neste tutorial. Este tutorial aborda o acesso aos dados da Cloudyn, inclusive gerenciamento de usuários e gerenciamento de entidades. Você aprenderá como:

> [!div class="checklist"]
> * Criar um usuário com acesso de administrador
> * Criar um usuário com acesso de usuário
> * Excluir um usuário
> * Excluir ou exportar dados pessoais
> * Criar e gerenciar entidades


Se você não tem uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter uma conta do Azure.
- Você precisa ter um registro de avaliação ou uma assinatura paga do Cloudyn.

## <a name="create-a-user-with-admin-access"></a>Criar um usuário com acesso de administrador

Embora você já tenha acesso de administrador, os colegas de trabalho em sua organização também precisaram ter acesso de administrador. No portal da Cloudyn, clique no símbolo de engrenagem no canto superior direito e selecione **Gerenciamento de Usuários**. Clique em **Adicionar Novo Usuário** para adicionar um novo usuário.

Insira as informações necessárias sobre o usuário. A **ID de logon** deve ser um endereço de email válido. Escolha permissões para permitir o Gerenciamento de Usuários para que o usuário possa criar e modificar a outros usuários. O Gerenciamento de Listas de Destinatários permite que o usuário edite listas de destinatários. Um link com informações de entrada é enviado da Cloudyn para o email do usuário quando você seleciona **Notificar usuário por email**. Na primeira entrada, o usuário define uma senha.

Em **Usuário tem acesso de administrador**, a entidade de raiz de sua organização está selecionada. Deixe a raiz selecionada e, em seguida, salve as informações do usuário. Selecionar a entidade raiz permite que o usuário tenha permissão de administrador não apenas para a entidade raiz na árvore, mas também para todas as entidades que residem abaixo dela.  
  ![Exemplo mostrando o acesso de administrador na caixa Adicionar novo usuário](./media/tutorial-user-access/new-admin-access.png)

## <a name="create-a-user-with-user-access"></a>Criar um usuário com acesso de usuário
Os usuários típicos que precisam acessar dados do Cloudyn, como painéis e relatórios, devem ter acesso de usuário para visualizá-los. Crie um novo usuário com acesso de usuário semelhante ao que você criou com acesso de administrador, com as seguintes diferenças:

- Limpe **Permitir gerenciamento de usuários**, **Permitir gerenciamento de listas de destinatários** e limpe tudo na lista **Usuário tem acesso de administrador**.
- Selecione as entidades para as quais o usuário precisa de acesso na lista **Usuário tem acesso de usuário**.
- Você também pode permitir que o administrador acesse entidades específicas, conforme o necessário.

![Exemplo mostrando o acesso de usuário na caixa Adicionar novo usuário](./media/tutorial-user-access/new-user-access.png)

Para assistir a um vídeo tutorial sobre como adicionar usuários, consulte [Adicionando usuários ao Cloudyn](https://youtu.be/Nzn7GLahx30).

## <a name="delete-a-user"></a>Excluir um usuário

Quando você exclui um usuário, quaisquer entidades as quais o usuário tenha acesso permanecerão intactas. Os relatórios *pessoais* salvos são removidos quando o usuário é excluído. Os relatórios *públicos* salvos criados pelo usuário não são excluídos.

Não é possível remover a si mesmo como um usuário.

> [!WARNING]
> Quando você exclui um usuário, ele não pode ser restaurado.

1.    No portal do Cloudyn, clique no símbolo de engrenagem no canto superior direito e selecione **Gerenciamento de Usuários**.
2.    Na lista de usuários, selecione o usuário que você deseja excluir e, em seguida, clique em **Excluir usuário** (o símbolo de lixeira).
3.    Na caixa Excluir usuário, clique em **Sim** e, em seguida, clique em **OK**.


## <a name="delete-or-export-personal-data"></a>Excluir ou exportar dados pessoais

Se você quiser excluir ou exportar dados pessoais do Cloudyn, você precisará criar um tíquete de suporte. Quando o tíquete de suporte é criado, ele atua como uma solicitação formal - uma solicitação do titular dos dados. A Microsoft então toma a ação adequada para remover a conta e excluir quaisquer dados pessoais ou de cliente. Para saber mais sobre como você pode solicitar a exclusão ou exportação dos seus dados, consulte [Solicitações de dados do Cloudyn pelo titular dos dados](https://www.cloudyn.com/cloudyn-gdpr-requests).

## <a name="create-and-manage-entities"></a>Criar e gerenciar entidades

Ao definir a hierarquia de entidades de custo, uma prática recomendada é identificar a estrutura da organização. Entidades permitem segmentar gastos por contas ou assinaturas individuais. Você cria entidades de custo para criar grupos lógicos para gerenciar e controlar gastos. Ao criar a árvore, considere como você deseja ou precisa ver os custos separados por unidades de negócios, departamentos de vendas, ambientes e centros de custo. A árvore de entidades na Cloudyn é flexível devido à herança de entidade.

A assinaturas individuais das contas de nuvem são vinculadas a entidades específicas. Você pode associar uma entidade a uma conta ou assinatura de provedor de serviços de nuvem. Portanto, as entidades são multilocatárias. Você pode atribuir acesso a usuários específicos somente para o segmento deles na empresa usando entidades. Isso mantém os dados isolados, mesmo em uma parte grande da uma empresa, como nas subsidiárias. E o isolamento de dados ajuda na governança.  

Quando você registrou seu contrato do Azure ou sua conta com a Cloudyn, seus dados de recurso do Azure, incluindo dados de uso, desempenho, cobrança e marca das suas assinaturas foram copiados para sua conta da Cloudyn. No entanto, você deve criar a árvore de entidades manualmente. Se você tiver ignorado o registro do Azure Resource Manager, somente os dados de cobrança e alguns relatórios de ativos estarão disponíveis no portal da Cloudyn.

No portal da Cloudyn, clique no símbolo de engrenagem no canto superior direito e selecione **Contas do Cloud**. Você começa com uma única entidade (raiz) e cria a árvore de entidade na raiz. Aqui está um exemplo de uma hierarquia de entidades que pode ser semelhante à de muitas organizações de TI após a conclusão da árvore:

![Exemplo de uma árvore de entidades mostrada na página Gerenciamento de contas](./media/tutorial-user-access/entity-tree.png)

Ao lado de **Entidades**, clique em **Adicionar Entidade**. Insira informações sobre a pessoa ou o departamento que você deseja adicionar. Os campos **Nome Completo** e **Email** não devem corresponder aos usuários existentes. Se você quiser exibir uma lista de níveis de acesso, pesquise na ajuda o tema *Adicionando uma entidade*.

![Exemplo mostrando os níveis de acesso e o nome de entidade na caixa Adicionar entidade](./media/tutorial-user-access/add-entity.png)

Quando terminar, **Salve** a entidade.

### <a name="entity-access-levels"></a>Níveis de acesso de entidade

Os níveis de acesso de entidade em conjunto com o acesso do usuário permitem que você defina quais os tipos de ações estão disponíveis no portal do Cloudyn.

- **Enterprise** - Fornece a capacidade de criar e gerenciar entidades de custo do elemento filho.
- **Enterprise + Alocação de custos** - Fornece a capacidade de criar e gerenciar entidades de custo do elemento filho, incluindo alocação de custos para contas consolidadas.
- **Enterprise, Custo baseado em alocação de custos do elemento pai** - Fornece a capacidade de criar e gerenciar entidades de custo do elemento filho. Os custos da conta são baseados no modelo de alocação de custos do elemento pai.
- **Somente painéis personalizados** - O usuário enxerga apenas painéis personalizados predefinidos.
- **Somente painéis** - O usuário vê somente os painéis.

### <a name="create-a-cost-entity-hierarchy"></a>Criar uma hierarquia de entidade de custo

Para criar uma hierarquia de entidade de custo, você deve ter uma conta com acesso enterprise ou enterprise + alocação de custos.

No portal da Cloudyn, clique no símbolo de engrenagem no canto superior direito e selecione **Contas do Cloud**. A árvore **Entidades** é exibida no painel esquerdo. Se necessário, expanda a árvore de entidade para que você possa exibir a entidade que deseja associar a uma conta.  Suas contas de provedor de serviço de nuvem são mostradas nas guias no painel direito. Selecione uma guia e, em seguida, clique e arraste uma conta/assinatura até a entidade e solte-a. A caixa **Mover** informa que a conta foi movida com êxito. Clique em **OK**.

Você também pode associar várias contas a uma entidade. Selecione as contas e, em seguida, clique em **Mover**. Na caixa Mover contas, selecione a entidade para onde você deseja mover a conta e, em seguida, clique em **Salvar**. A caixa Mover contas pede para você confirmar que deseja mover as contas. Clique em **Sim** e depois em **OK**.

Para assistir a um vídeo tutorial sobre como criar uma hierarquia de entidade de custo, consulte [Criando uma hierarquia de entidade de custo em Cloudyn](https://youtu.be/dAd9G7u0FmU).

Se você for um usuário do Azure Enterprise Agreement, assista a um vídeo tutorial sobre como associar contas e assinaturas a entidades em [Conectando ao Azure Resource Manager com Cloudyn](https://youtu.be/oCIwvfBB6kk).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um usuário com acesso de administrador
> * Criar um usuário com acesso de usuário
> * Excluir um usuário
> * Excluir ou exportar dados pessoais
> * Criar e gerenciar entidades


Se você ainda não habilitou o acesso à API do Azure Resource Manager para suas contas, vá para o artigo a seguir.

> [!div class="nextstepaction"]
> [Ativar assinaturas e contas do Azure](./activate-subs-accounts.md)