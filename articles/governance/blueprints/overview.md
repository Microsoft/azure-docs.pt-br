---
title: Visão geral do Azure Blueprint
description: Entenda como o serviço do Azure Blueprints permite criar, definir e implantar artefatos no seu ambiente do Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/08/2019
ms.topic: overview
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 5f403ee7d2bf4a0730ceaa9b5a95b3e7b11f3695
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67428767"
---
# <a name="overview-of-the-azure-blueprints-service"></a>Visão geral do serviço do Azure Blueprints

Assim como um blueprint permite que um engenheiro ou arquiteto desenhe os parâmetros de um projeto, o Azure Blueprints permite que arquitetos de nuvem e grupos centrais de tecnologia da informação definam um conjunto repetitivo de recursos do Azure que implementa e adere aos padrões, padrões e requisitos de uma organização. O Azure Blueprints permite que as equipes de desenvolvimento criem e mantenham novos ambientes rapidamente, sabendo que eles são criados de acordo com as especificações da organização e contêm um conjunto de componentes integrados, como redes, para acelerar o desenvolvimento e a entrega.

O Blueprints é uma maneira declarativa de orquestrar a implementação de vários modelos de recursos e outros artefatos, como:

- Atribuições de Funções
- Atribuições de Políticas
- Modelos do Azure Resource Manager
- Grupos de recursos

O serviço do Azure BluePrint é apoiado pelo [Azure Cosmos DB](../../cosmos-db/introduction.md) distribuído globalmente.
Objetos de blueprint são replicados para várias regiões do Azure. Essa replicação oferece baixa latência, alta disponibilidade e acesso consistente a seus objetos de blueprint, independentemente de qual região o Blueprint implanta seus recursos.

## <a name="how-its-different-from-resource-manager-templates"></a>Como ele difere dos modelos do Resource Manager

O serviço foi projetado para ajudá-lo na _configuração do ambiente_. Essa configuração geralmente consiste em um conjunto de grupos de recursos, políticas, atribuições de função e implantações de modelo do Resource Manager. Um blueprint é um pacote para reunir cada um desses _artefatos_ e permitir que você componha e versione esse pacote - inclusive por meio de um pipeline de CI / CD. Por fim, cada um é atribuído a uma assinatura em uma única operação que pode ser auditada e rastreada.

Quase tudo o que você deseja incluir na implantação em Blueprints pode ser feito com um modelo do Resource Manager. No entanto, um modelo do Resource Manager é um documento que não existe nativamente no Azure. Cada um está armazenado localmente ou no controle do código-fonte. O modelo é usado para implantações de um ou mais recursos do Azure, mas, quando esses recursos são implantados, não há relação ou conexão ativa com o modelo.

Com o Blueprints, a relação entre a definição do blueprint (o _que deve ser_ implementado) e a atribuição do blueprint (o _que foi_ implementado) é preservada. Essa conexão dá suporte ao acompanhamento e à auditoria de implantações aprimorados. O Blueprints também pode atualizar várias assinaturas ao mesmo tempo quando regidas pelo mesmo blueprint.

Não há necessidade de escolher entre um modelo do Resource Manager e um blueprint. Cada blueprint pode consistir em zero ou mais artefatos _do modelo do Resource Manager_. Esse suporte significa que os esforços anteriores para desenvolver e manter uma biblioteca de modelos do Resource Manager podem ser reutilizados no Blueprints.

## <a name="how-its-different-from-azure-policy"></a>Como ele difere do Azure Policy

Um blueprint é um pacote ou contêiner para composição de conjuntos específicos de foco de padrões, padrões e requisitos relacionados à implementação de serviços em nuvem, segurança e design do Azure que podem ser reutilizados para manter a consistência e a conformidade.

Uma [política](../policy/overview.md) é uma permissão padrão e um sistema de negação explícito focado nas propriedades do recurso durante a implementação e para recursos já existentes. Ele dá suporte à governança de nuvem confirmando que os recursos dentro de uma assinatura estão cumprindo os requisitos e os padrões.

A inclusão de uma política em um blueprint permite a criação do design ou padrão correto durante a atribuição do blueprint. A inclusão da política faz com que apenas alterações aprovadas ou esperadas possam ocorrer no ambiente para proteger a conformidade com a intenção do blueprint.

Uma política pode ser incluída como um dos muitos _artefatos_ em uma definição de blueprint. Os Blueprints também suportam o uso de parâmetros com políticas e iniciativas.

## <a name="blueprint-definition"></a>Definição de planta

Um plano gráfico é composto por _artefatos_. Plantas atualmente dão suporte os recursos a seguir como artefatos:

|Recurso  | Opções de hierarquia| DESCRIÇÃO  |
|---------|---------|---------|
|Grupos de recursos | Assinatura | Crie um novo grupo de recursos para uso por outros artefatos no blueprint.  Esses grupos de recursos de espaço reservado permitem que você organize os recursos exatamente da maneira que você deseja que eles sejam estruturados e fornece um limitador de escopo para os artefatos de atribuição de diretivas e funções incluídos, além dos modelos do Azure Resource Manager. |
|Modelo do Azure Resource Manager | Assinatura, Grupo de Recursos | Modelos são usados para compor ambientes complexos. Ambientes de exemplo: um farm do SharePoint, uma configuração de estado da Automação do Azure ou um espaço de trabalho do Log Analytics. |
|Atribuição de política | Assinatura, Grupo de Recursos | Permite a atribuição de uma política ou iniciativa à assinatura a qual o blueprint está atribuído. A política ou iniciativa deve estar dentro do escopo do local de definição do blueprint. Se a política ou iniciativa tiver parâmetros, esses parâmetros serão atribuídos na criação do blueprint ou durante a atribuição do blueprint. |
|Atribuição de função | Assinatura, Grupo de Recursos | Adicione um usuário ou grupo existente a uma função interna para fazer com que as pessoas certas tenham sempre o acesso correto aos seus recursos. Atribuições de função podem ser definidas para a assinatura inteira ou aninhadas em um grupo de recursos específico incluído no blueprint. |

### <a name="blueprint-definition-locations"></a>Localizações de definição do blueprint

Ao criar uma definição de blueprint, você definirá onde o blueprint será salvo. Blueprints podem ser salvos em um [grupo de gerenciamento](../management-groups/overview.md) ou assinatura ao qual você tenha acesso de **Contribuidor**. Se o local for um grupo de gerenciamento, o blueprint estará disponível para atribuir qualquer assinatura filho daquele grupo de gerenciamento.

### <a name="blueprint-parameters"></a>Parâmetros de blueprint

Planos gráficos podem passar parâmetros para uma iniciativa de política/ou um modelo do Azure Resource Manager.
Ao adicionar um _artefato_ a um blueprint, o criador decide fornecer um valor definido para cada atribuição de blueprint ou permitir que cada atribuição de blueprint forneça um valor na hora da atribuição. Essa flexibilidade fornece a opção para definir um valor predeterminado para todos os usos do plano gráfico ou para habilitar essa decisão a ser feita no momento da atribuição.

> [!NOTE]
> Um plano gráfico pode ter seus próprios parâmetros, mas elas atualmente podem ser criado somente se um plano gráfico é gerado da API REST, em vez de por meio do Portal.

Para obter mais informações, consulte [parâmetros de plano gráfico](./concepts/parameters.md).

### <a name="blueprint-publishing"></a>Especificações técnicas de publicação

Quando um plano gráfico é criado, ele é considerado para estar no modo **rascunho**. Quando estiver pronto para ser atribuído, ele precisa ser **Publicado**. A publicação requer a definição de uma cadeia de caracteres **Versão** (letras, números e hifens com um comprimento máximo de 20 caracteres) juntamente com a opção de **Alterar anotações**. A **versão** a diferencia de futuras alterações no mesmo blueprint e permite que cada versão seja atribuída. Esse controle de versão também significa que várias **Versões** do mesmo blueprint podem ser atribuídas à mesma assinatura. Quando há outras alterações no blueprint, a **Versão** **Publicada** ainda existe, assim como as **Alterações não publicadas**. Depois que as alterações forem concluídas, o blueprint atualizado está **publicado** com uma nova e exclusiva **versão** e agora também podem ser atribuídos.

## <a name="blueprint-assignment"></a>Atribuição de planta

Cada **versão** **publicada** de um blueprint pode ser atribuída a uma assinatura existente. No portal, o blueprint usará como padrão a **Versão** em vez do que foi **Publicado** mais recentemente. Se houver parâmetros de artefatos (ou parâmetros de blueprint), os parâmetros serão definidos durante o processo de atribuição.

## <a name="permissions-in-azure-blueprints"></a>Permissões nos Blueprints do Azure

Para usar blueprints, você deverá receber permissões por meio do [RBAC](../../role-based-access-control/overview.md) (controle de acesso baseado em função). Para criar planos gráficos, sua conta precisa das seguintes permissões:

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

Todas as permissões acima são incluídas na função **Proprietário**. A função **Colaborador** precisa das permissões para criar blueprint e excluir blueprint, mas não tem permissões de atribuição de blueprint. Se essas funções internas não se ajustarem às suas necessidades de segurança, considere a criação de uma [função personalizada](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Se estiver usando uma identidade gerenciada atribuída pelo sistema, a entidade de serviço para o Azure BluePrints requer a função **Proprietário** na assinatura atribuída para habilitar a implantação. Se estiver usando o portal, essa função é concedida automaticamente e revogada para a implantação. Se usando a API REST, essa função deve ser concedida manualmente, mas é revogada ainda automaticamente após a conclusão da implantação. Se estiver usando uma identidade gerenciada atribuída pelo usuário, somente o usuário que está criando a atribuição de especificação técnica precisa de permissões de **Proprietário**.

## <a name="video-overview"></a>Visão geral em vídeo

A visão geral a seguir do Azure Blueprints é do Azure Fridays. Para baixar um vídeo, acesse [Azure Fridays – Uma visão geral do Azure Blueprints](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints) no Channel 9.

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>Próximas etapas

- [Criar um plano gráfico - Portal](create-blueprint-portal.md)
- [Criar um plano gráfico - API REST](create-blueprint-rest-api.md)
