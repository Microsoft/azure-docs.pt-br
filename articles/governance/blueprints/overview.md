---
title: Visão geral do Azure Blueprint
description: Entenda como o serviço do Azure Blueprints permite criar, definir e implantar artefatos no seu ambiente do Azure.
ms.date: 01/27/2021
ms.topic: overview
ms.openlocfilehash: f4ba77f5fcb376bf600d94997b0d6ba569f04f82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919335"
---
# <a name="what-is-azure-blueprints"></a>O que é o Azure Blueprints?

Assim como um blueprint permite que um engenheiro ou arquiteto desenhe os parâmetros de um projeto, o Azure Blueprints permite que arquitetos de nuvem e grupos centrais de tecnologia da informação definam um conjunto repetitivo de recursos do Azure que implementa e adere aos padrões, padrões e requisitos de uma organização. O Azure Blueprints permite que as equipes de desenvolvimento criem e mantenham novos ambientes rapidamente, sabendo que eles são criados de acordo com as especificações da organização e contêm um conjunto de componentes integrados, como redes, para acelerar o desenvolvimento e a entrega.

O Blueprints é uma maneira declarativa de orquestrar a implementação de vários modelos de recursos e outros artefatos, como:

- Atribuições de Funções
- Atribuições de Políticas
- Modelos do ARM (modelos do Azure Resource Manager)
- Grupos de recursos

O serviço do Azure BluePrint é apoiado pelo [Azure Cosmos DB](../../cosmos-db/introduction.md) distribuído globalmente. Objetos de blueprint são replicados para várias regiões do Azure. Essa replicação oferece baixa latência, alta disponibilidade e acesso consistente a seus objetos de blueprint, independentemente de em qual região o Azure Blueprints implanta seus recursos.

## <a name="how-its-different-from-arm-templates"></a>Como ele difere dos modelos do ARM

O serviço foi projetado para ajudá-lo na _configuração do ambiente_. Essa configuração geralmente consiste em um conjunto de grupos de recursos, políticas, atribuições de função e implantações de modelo do ARM. Um blueprint é um pacote para reunir cada um desses _artefatos_ e permitir que você componha e versione esse pacote, inclusive por meio de um pipeline de CI/CD (integração contínua e entrega contínua). Por fim, cada um é atribuído a uma assinatura em uma única operação que pode ser auditada e rastreada.

Quase tudo o que você deseja incluir na implantação no Azure Blueprints pode ser feito com um modelo do ARM. No entanto, um modelo do ARM é um documento que não existe nativamente no Azure. Cada um está armazenado localmente ou no controle do código-fonte. O modelo é usado para implantações de um ou mais recursos do Azure, mas, quando esses recursos são implantados, não há relação ou conexão ativa com o modelo.

Com o Azure Blueprints, a relação entre a definição do blueprint (o que _deve ser_ implantado) e a atribuição do blueprint (o que _foi_ implantado) é preservada. Essa conexão dá suporte ao acompanhamento e à auditoria de implantações aprimorados. O Azure Blueprints também pode atualizar várias assinaturas ao mesmo tempo quando regidas pelo mesmo blueprint.

Não há necessidade de escolher entre um modelo ARM e um blueprint. Cada blueprint pode consistir em zero ou mais _artefatos_ de modelo ARM. Esse suporte significa que os esforços anteriores para desenvolver e manter uma biblioteca de modelos do ARM podem ser reutilizados no Azure Blueprints.

## <a name="how-its-different-from-azure-policy"></a>Como ele difere do Azure Policy

Um blueprint é um pacote ou contêiner para composição de conjuntos específicos de foco de padrões, padrões e requisitos relacionados à implementação de serviços em nuvem, segurança e design do Azure que podem ser reutilizados para manter a consistência e a conformidade.

Uma [política](../policy/overview.md) é uma permissão padrão e um sistema de negação explícito focado nas propriedades do recurso durante a implementação e para recursos já existentes. Ele dá suporte à governança de nuvem confirmando que os recursos dentro de uma assinatura estão cumprindo os requisitos e os padrões.

A inclusão de uma política em um blueprint permite a criação do padrão ou design correto durante a atribuição do blueprint. A inclusão da política faz com que apenas alterações aprovadas ou esperadas possam ocorrer no ambiente para proteger a conformidade com a intenção do blueprint.

Uma política pode ser incluída como um dos muitos _artefatos_ em uma definição de blueprint. Os Blueprints também suportam o uso de parâmetros com políticas e iniciativas.

## <a name="blueprint-definition"></a>Definição de planta

Um blueprint é composto por _artefatos_. O Azure Blueprints atualmente dá suporte aos seguintes recursos como artefatos:

|Recurso  | Opções de hierarquia| Descrição  |
|---------|---------|---------|
|Grupos de recursos | Subscription | Crie um novo grupo de recursos para uso por outros artefatos no blueprint.  Esses grupos de recursos de espaço reservado permitem que você organize os recursos exatamente da maneira que você deseja que eles sejam estruturados e fornece um limitador de escopo para os artefatos de atribuição de políticas e funções incluídos, além dos modelos do ARM. |
|Modelo de ARM | Assinatura, Grupo de Recursos | Modelos, incluindo aninhados e vinculados, são usados para compor ambientes complexos. Ambientes de exemplo: um farm do SharePoint, uma configuração de estado da Automação do Azure ou um espaço de trabalho do Log Analytics. |
|Atribuição de política | Assinatura, Grupo de Recursos | Permite a atribuição de uma política ou iniciativa à assinatura a qual o blueprint está atribuído. A política ou iniciativa deve estar dentro do escopo do local de definição do blueprint. Se a política ou iniciativa tiver parâmetros, esses parâmetros serão atribuídos na criação do blueprint ou durante a atribuição do blueprint. |
|Atribuição de função | Assinatura, Grupo de Recursos | Adicione um usuário ou grupo existente a uma função interna para fazer com que as pessoas certas tenham sempre o acesso correto aos seus recursos. Atribuições de função podem ser definidas para a assinatura inteira ou aninhadas em um grupo de recursos específico incluído no blueprint. |

### <a name="blueprint-definition-locations"></a>Localizações de definição do blueprint

Ao criar uma definição de blueprint, você definirá onde o blueprint será salvo. Blueprints podem ser salvos em um [grupo de gerenciamento](../management-groups/overview.md) ou assinatura ao qual você tenha acesso de **Contribuidor**. Se o local for um grupo de gerenciamento, o blueprint estará disponível para atribuir qualquer assinatura filho daquele grupo de gerenciamento.

### <a name="blueprint-parameters"></a>Parâmetros de blueprint

Os blueprints podem passar parâmetros para uma política/iniciativa ou um modelo do ARM. Ao adicionar um _artefato_ a um blueprint, o criador decide fornecer um valor definido para cada atribuição de blueprint ou permitir que cada atribuição de blueprint forneça um valor na hora da atribuição. Essa flexibilidade fornece a opção para definir um valor predeterminado para todos os usos do plano gráfico ou para habilitar essa decisão a ser feita no momento da atribuição.

> [!NOTE]
> Um plano gráfico pode ter seus próprios parâmetros, mas elas atualmente podem ser criado somente se um plano gráfico é gerado da API REST, em vez de por meio do Portal.

Para obter mais informações, consulte [parâmetros de plano gráfico](./concepts/parameters.md).

### <a name="blueprint-publishing"></a>Especificações técnicas de publicação

Quando um plano gráfico é criado, ele é considerado para estar no modo **rascunho**. Quando estiver pronto para ser atribuído, ele precisa ser **Publicado**. A publicação requer a definição de uma cadeia de caracteres **Versão** (letras, números e hifens com um comprimento máximo de 20 caracteres) juntamente com a opção de **Alterar anotações**. A **versão** a diferencia de futuras alterações no mesmo blueprint e permite que cada versão seja atribuída. Esse controle de versão também significa que várias **Versões** do mesmo blueprint podem ser atribuídas à mesma assinatura. Quando há outras alterações no blueprint, a **Versão**
**Publicada** ainda existe, bem como as **Alterações não publicadas**. Depois que as alterações forem concluídas, o blueprint atualizado está **publicado** com uma nova e exclusiva **versão** e agora também podem ser atribuídos.

## <a name="blueprint-assignment"></a>Atribuição de planta

Cada **Versão** **Publicada** de um blueprint pode ser atribuída (com um tamanho de nome máximo de 90 caracteres) a uma assinatura ou um grupo de gerenciamento existente. No portal, o blueprint usará como padrão a **Versão** em vez do que foi **Publicado** mais recentemente. Se houver parâmetros de artefato ou de blueprint, eles serão definidos durante o processo de atribuição.

> [!NOTE]
> A atribuição de uma definição de blueprint a um grupo de gerenciamento significa que o objeto de atribuição existe no grupo de gerenciamento. A implantação de artefatos ainda é direcionada a uma assinatura. Para executar uma atribuição de grupo de gerenciamento, a [API REST Criar ou Atualizar](/rest/api/blueprints/assignments/createorupdate) precisa ser usada e o corpo da solicitação precisa incluir um valor para `properties.scope` a fim de definir a assinatura de destino.

## <a name="permissions-in-azure-blueprints"></a>Permissões nos Blueprints do Azure

Para usar blueprints, você deverá receber permissões por meio do [Azure RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md). Para ler ou ver um blueprint no portal do Azure, sua conta deve ter acesso de leitura ao escopo no qual a definição do blueprint está localizada.

Para criar planos gráficos, sua conta precisa das seguintes permissões:

- `Microsoft.Blueprint/blueprints/write` -Criar uma definição de planta
- `Microsoft.Blueprint/blueprints/artifacts/write` -Criar artefatos em uma definição de planta
- `Microsoft.Blueprint/blueprints/versions/write` -Publicar um plano gráfico

Para excluir os planos gráficos, sua conta precisa das seguintes permissões:

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> As permissões de definição de blueprint devem ser concedidas ou herdadas no escopo de assinatura ou do grupo de gerenciamento em que estão salvas.

Para atribuir ou desatribuir um plano gráfico, sua conta precisa das seguintes permissões:

- `Microsoft.Blueprint/blueprintAssignments/write` -Atribuir um plano gráfico
- `Microsoft.Blueprint/blueprintAssignments/delete` -Cancelar a atribuição de um plano gráfico

> [!NOTE]
> Como as atribuições de especificações técnicas são criadas em uma assinatura, atribua o plano gráfico e cancelar a atribuição de permissões devem ser concedidas em um escopo de assinatura ou ser herdadas para um escopo de assinatura.

As seguintes funções internas estão disponíveis:

|Função do Azure | Descrição |
|-|-|
|[Proprietário](../../role-based-access-control/built-in-roles.md#owner) | Além de outras permissões, inclui todas as permissões relacionadas ao Azure Blueprint. |
|[Colaborador](../../role-based-access-control/built-in-roles.md#contributor) | Além de outras permissões, pode criar e excluir definições de blueprint, mas não tem permissões de atribuição de blueprint. |
|[Colaborador do Blueprint](../../role-based-access-control/built-in-roles.md#blueprint-contributor) | Pode gerenciar definições de blueprint, mas não as atribuir. |
|[Operador do Blueprint](../../role-based-access-control/built-in-roles.md#blueprint-operator) | Pode atribuir blueprints publicados existentes, mas não pode criar definições de blueprint. A atribuição de blueprint só funcionará se a atribuição for concluída com uma identidade gerenciada atribuída pelo usuário. |

Se essas funções internas não se ajustarem às suas necessidades de segurança, considere a criação de uma [função personalizada](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Se estiver usando uma identidade gerenciada atribuída pelo sistema, a entidade de serviço para o Azure BluePrints requer a função **Proprietário** na assinatura atribuída para habilitar a implantação. Se estiver usando o portal, essa função é concedida automaticamente e revogada para a implantação. Se usando a API REST, essa função deve ser concedida manualmente, mas é revogada ainda automaticamente após a conclusão da implantação. Se você estiver usando uma identidade gerenciada atribuída pelo usuário, somente o usuário que criar a atribuição blueprint precisará da permissão `Microsoft.Blueprint/blueprintAssignments/write`, que está incluída nas funções internas **Proprietário** e **Operador de Blueprint**.

## <a name="naming-limits"></a>Limites de nomenclatura

As seguintes limitações existem para determinados campos:

|Objeto|Campo|Caracteres Permitidos|Máx. Comprimento|
|-|-|-|-|
|Blueprint|Nome|letras, números, hifens e pontos|48|
|Blueprint|Versão|letras, números, hifens e pontos|20|
|Atribuição de planta|Nome|letras, números, hifens e pontos|90|
|Artefato de blueprint|Nome|letras, números, hifens e pontos|48|

## <a name="video-overview"></a>Visão geral em vídeo

A visão geral a seguir do Azure Blueprints é do Azure Fridays. Para baixar um vídeo, acesse [Azure Fridays – Uma visão geral do Azure Blueprints](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints) no Channel 9.

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>Próximas etapas

- [Criar um blueprint – Portal](./create-blueprint-portal.md).
- [Criar um blueprint – PowerShell](./create-blueprint-powershell.md).
- [Criar um blueprint – API REST](./create-blueprint-rest-api.md).