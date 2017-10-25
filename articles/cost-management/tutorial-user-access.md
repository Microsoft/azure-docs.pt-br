---
title: Atribuir acesso no Azure Cost Management | Microsoft Docs
description: "Atribua acesso aos dados de gerenciamento de custos com contas de usuário que definem os níveis de acesso para entidades."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 09/19/2017
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: aca9f0b3320f298987cf39172fd4b043326a2f4b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="assign-access-to-cost-management-data"></a>Atribuir acesso aos dados de gerenciamento de custos

O acesso aos dados de gerenciamento de custos é fornecido pelo gerenciamento de usuário ou de entidade. As contas de usuário da Cloudyn determinam o acesso a *entidades* e a funções administrativas. Há dois tipos de acesso: administrador e usuário. A menos que seja modificado para cada usuário, o acesso de administrador permite o uso irrestrito de todas as funções no portal da Cloudyn, incluindo: gerenciamento de usuários, gerenciamento de listas de destinatários e acesso de entidade raiz a todos os dados de entidade. O acesso do usuário é indicado para que os usuários finais exibam relatórios e criem relatórios usando o acesso que têm aos dados de entidade.

As entidades são usadas para refletir a estrutura hierárquica da sua organização de negócios. Elas identificam os departamentos, as divisões e as equipes da organização na Cloudyn. A hierarquia de entidades ajuda a acompanhar com precisão os gastos de cada entidade.

Quando você registrou sua conta ou um contrato do Azure, uma conta com permissão de administrador foi criada na Cloudyn, portanto, você pode executar todas as etapas neste tutorial. Este tutorial aborda o acesso aos dados de gerenciamento de custos, incluindo o gerenciamento de usuários e gerenciamento de entidades. Você aprenderá como:

> [!div class="checklist"]
> * Criar um usuário com acesso de administrador
> * Criar um usuário com acesso de usuário
> * Criar entidades



## <a name="create-a-user-with-admin-access"></a>Criar um usuário com acesso de administrador

Embora você já tenha acesso de administrador, os colegas de trabalho em sua organização também precisaram ter acesso de administrador. No portal da Cloudyn, clique no símbolo de engrenagem no canto superior direito e selecione **Gerenciamento de Usuários**. Clique em **Adicionar Novo Usuário** para adicionar um novo usuário.

Insira as informações necessárias sobre o usuário. Você pode deixar o campo de senha vazio para que o usuário possa definir uma nova senha na primeira entrada. Um link com informações de entrada é enviado da Cloudyn para o email do usuário quando você seleciona **Notificar usuário por email**. Escolha permissões para permitir o Gerenciamento de Usuários para que o usuário possa criar e modificar a outros usuários. Gerenciamento de listas de destinatários para permitir que o usuário edite listas de destinatários.

Em **Usuário tem acesso de administrador**, a entidade de raiz de sua organização está selecionada. Deixe a raiz selecionada e, em seguida, salve as informações do usuário. Selecionar a entidade raiz permite que o usuário tenha permissão de administrador não apenas para a entidade raiz na árvore, mas também para todas as entidades que residem abaixo dela.  
  ![adicionar novo usuário com acesso de administrador](.\media\tutorial-user-access\new-admin-access.png)

## <a name="create-a-user-with-user-access"></a>Criar um usuário com acesso de usuário
Usuários típicos que precisam de acesso aos dados de gerenciamento de custos, como painéis e relatórios, devem ter acesso de usuário para exibi-los. Crie um novo usuário com acesso de usuário semelhante ao que você criou com acesso de administrador, com as seguintes diferenças:

- Limpe **Permitir gerenciamento de usuários**, **Permitir gerenciamento de listas de destinatários** e limpe tudo na lista **Usuário tem acesso de administrador**.
- Selecione as entidades para as quais o usuário precisa de acesso na lista **Usuário tem acesso de usuário**.
- Você também pode permitir que o administrador acesse entidades específicas, conforme o necessário.

![adicionar novo usuário com acesso de usuário](.\media\tutorial-user-access\new-user-access.png)

## <a name="create-entities"></a>Criar entidades

Ao definir a hierarquia de entidades de custo, uma prática recomendada é identificar a estrutura da organização.

Ao criar a árvore, considere como você deseja ou precisa ver os custos separados por unidades de negócios, departamentos de vendas, ambientes e centros de custo. A árvore de entidades na Cloudyn é flexível devido à herança de entidade. A assinaturas individuais das contas de nuvem são vinculadas a entidades específicas. Portanto, as entidades são multilocatárias. Você pode atribuir acesso a usuários específicos somente para o segmento deles na empresa usando entidades. Isso mantém os dados isolados, mesmo em uma parte grande da uma empresa, como nas subsidiárias. E o isolamento de dados ajuda na governança.  

Quando você registrou seu contrato do Azure ou sua conta com a Cloudyn, seus dados de recurso do Azure, incluindo dados de uso, desempenho, cobrança e marca das suas assinaturas foram copiados para sua conta da Cloudyn. No entanto, você deve criar a árvore de entidades manualmente. Se você tiver ignorado o registro do Azure Resource Manager, somente os dados de cobrança e alguns relatórios de ativos estarão disponíveis no portal da Cloudyn.

No portal da Cloudyn, clique em **Configurações** no canto superior direito e selecione **Contas de Nuvem**. Você começa com uma única entidade (raiz) e cria a árvore de entidade na raiz. Aqui está um exemplo de uma hierarquia de entidades que pode ser semelhante à de muitas organizações de TI após a conclusão da árvore:

![árvore de entidades](.\media\tutorial-user-access\entity-tree.png)

Ao lado de **Entidades**, clique em **Adicionar Entidade**. Insira informações sobre a pessoa ou o departamento que você deseja adicionar. Os campos **Nome Completo** e **Email** não devem corresponder aos usuários existentes. Se você quiser exibir uma lista de níveis de acesso, pesquise na ajuda o tema *Adicionando uma entidade*.

![adicionar entidade](.\media\tutorial-user-access\add-entity.png)

Quando terminar, **Salve** a entidade.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar um usuário com acesso de administrador
> * Criar um usuário com acesso de usuário
> * Criar entidades

Avance para o próximo tutorial para aprender a prever despesas usando dados históricos.

> [!div class="nextstepaction"]
> [Previsão de despesas futuras](tutorial-forecast-spending.md)
