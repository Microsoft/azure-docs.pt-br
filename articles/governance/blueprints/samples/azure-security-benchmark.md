---
title: Visão geral do exemplo de blueprint do Azure Security Benchmark
description: Visão geral do exemplo de blueprint do Azure Security Benchmark. Este exemplo de blueprint ajuda os clientes a avaliar controles específicos.
ms.date: 04/02/2021
ms.topic: sample
ms.openlocfilehash: 58e8c3972b0f05e5b0a3f15da0dfd3eae301ad31
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106386985"
---
# <a name="azure-security-benchmark-blueprint-sample"></a>Exemplo de blueprint do Azure Security Benchmark

O exemplo de blueprint do Azure Security Benchmark oferece proteções de governança por meio do [Azure Policy](../../policy/overview.md) que ajudam a avaliar controles específicos do [Azure Security Benchmark v1](../../../security/benchmarks/overview.md). Esse blueprint ajuda os clientes a implantar um conjunto principal de políticas em qualquer arquitetura implantada pelo Azure em que se pretende implementar os controles do Azure Security Benchmark.

## <a name="control-mapping"></a>Mapeamento de controle

O [mapeamento de controle do Azure Policy](../../policy/samples/azure-security-benchmark.md) fornece detalhes sobre as definições de política incluídas neste projeto e como essas definições de política são mapeadas para **domínios de conformidade** e **controles** no Azure Security Benchmark. Quando atribuídos a uma arquitetura, os recursos são avaliados pelo Azure Policy para verificar a não conformidade com definições de política atribuídas. Para saber mais, veja [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Implantar

Para implantar o exemplo de blueprint do Azure Security Benchmark do Azure Blueprints, execute estas etapas:

> [!div class="checklist"]
> - Criar um blueprint com base na amostra
> - Marcar sua cópia do exemplo como **Publicado**
> - Atribuir a sua cópia do blueprint a uma assinatura existente

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

### <a name="create-blueprint-from-sample"></a>Criar um blueprint com base na amostra

Primeiro, implemente a amostra de blueprint criando um blueprint no ambiente usando a amostra como ponto de partida.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Na página **Introdução** à esquerda, selecione o botão **Criar** em _Criar um blueprint_.

1. Localize o exemplo de blueprint do **Azure Security Benchmark v1** em _Outros Exemplos_ e clique no nome para selecionar este exemplo.

1. Insira as informações _Básicas_ do exemplo de blueprint:

   - **Nome do blueprint**: Forneça um nome para a sua cópia do exemplo de blueprint do Azure Security Benchmark.
   - **Localização da definição**: Use as reticências e selecione o grupo de gerenciamento em que deseja salvar a cópia da amostra.

1. Selecione a guia _Artefatos_ na parte superior da página ou clique em **Avançar: Artefatos** na parte inferior da página.

1. Examine a lista de artefatos que estão incluídos no exemplo de blueprint. Muitos dos artefatos têm parâmetros que definiremos mais tarde. Selecione **Salvar Rascunho** quando terminar de examinar o exemplo de blueprint.

### <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

Agora a cópia do exemplo de blueprint foi criada em seu ambiente. Ela é criada no modo **Rascunho** e deve ser **Publicada** antes de ser atribuída e implantada. A cópia da amostra de blueprint pode ser personalizada de acordo com seu ambiente e suas necessidades, mas essa modificação poderá desviá-lo do que é recomendado pelo Azure Security Benchmark.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a cópia da amostra de blueprint e, em seguida, selecione-a.

1. Selecione **Publicar blueprint** na parte superior da página. Na nova página à direita, informe a **Versão** da sua cópia da amostra de blueprint. Essa propriedade será útil se você fizer uma modificação mais tarde. Forneça **Anotações de alteração** como "Primeira versão publicada do exemplo de blueprint do Azure Security Benchmark." Em seguida, selecione **Publicar** na parte inferior da página.

### <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Quando a cópia do exemplo de blueprint for **Publicada** com êxito, ele poderá ser atribuído a uma assinatura do grupo de gerenciamento em que ele foi salvo. Esta é a etapa em que os parâmetros são fornecidos para tornar exclusiva cada implantação da cópia do exemplo de blueprint.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a cópia da amostra de blueprint e, em seguida, selecione-a.

1. Selecione **Atribuir blueprint** na parte superior da página de definição de blueprint.

1. Forneça os valores de parâmetro para a atribuição de blueprint:

   - Noções básicas

     - **Assinaturas**: Selecione uma ou mais das assinaturas que estão no grupo de gerenciamento em que você salvou a cópia do exemplo de blueprint. Se você selecionar mais de uma assinatura, será criada uma atribuição para cada uma, usando os parâmetros inseridos.
     - **Nome da atribuição**: O nome é pré-preenchido para você com base no nome do blueprint.
       Altere-o conforme necessário ou mantenha-o como está.
     - **Localização**: Selecione uma região para a identidade gerenciada a ser criada. O Blueprint do Azure usa essa identidade gerenciada para implantar todos os artefatos no blueprint atribuído. Para saber mais, veja [identidades gerenciadas para recursos do Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Versão de definição de blueprint**: Escolha uma versão **Publicada** da cópia da amostra de blueprint.

   - Bloquear atribuição

     Selecione a configuração de bloqueio de blueprint para o seu ambiente. Para obter mais informações, consulte [bloqueio de recursos de projetos](../concepts/resource-locking.md).

   - Identidade Gerenciada

     Deixe a opção de identidade gerenciada _atribuída ao sistema_ padrão.

   - Parâmetros do artefato

     Os parâmetros definidos nesta seção se aplicam ao artefato sob o qual ele está definido. Esses são [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters), pois são definidos durante a atribuição do blueprint. Para obter uma lista completa ou parâmetros de artefato e suas descrições, confira a [Tabela de parâmetros de artefato](#artifact-parameters-table).

1. Depois que todos os parâmetros forem inseridos, selecione **Atribuir** na parte inferior da página. A atribuição de blueprint é criada, e a implantação de artefato é iniciada. A implantação leva aproximadamente uma hora. Para verificar o status da implantação, abra a atribuição de blueprint.

> [!WARNING]
> As amostras internas de blueprint e o serviço Azure Blueprints são **gratuitos**. Os recursos do Azure são [precificados por produto](https://azure.microsoft.com/pricing/). Use a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/) para estimar o custo da execução de recursos implantados por essa amostra de blueprint.

### <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefato

A seguinte tabela fornece uma lista dos parâmetros de artefato de blueprint:

|Nome do artefato|Tipo de artefato|Nome do parâmetro|Descrição|
|-|-|-|-|
|Auditar as recomendações do Azure Security Benchmark e implantar Extensões de VM de suporte específicas|Atribuição de política|Lista de usuários excluídos do grupo de Administradores de VM do Windows|Uma lista separada por ponto e vírgula de membros que devem ser excluídos do grupo local de Administradores. Por exemplo: Administrador; myUser1; myUser2|
|Auditar as recomendações do Azure Security Benchmark e implantar Extensões de VM de suporte específicas|Atribuição de política|Lista de usuários que devem ser incluídos no grupo de Administradores de VM do Windows|Uma lista separada por ponto e vírgula de membros que devem ser incluídos no grupo local de Administradores. Por exemplo: Administrador; myUser1; myUser2|
|Auditar as recomendações do Azure Security Benchmark e implantar Extensões de VM de suporte específicas|Atribuição de política|Lista de usuários que ser os *únicos* incluídos no grupo de Administradores de VM do Windows|Uma lista separada por ponto e vírgula de todos os membros esperados do grupo local de Administradores. Por exemplo: Administrador; myUser1; myUser2|
|Auditar as recomendações do Azure Security Benchmark e implantar Extensões de VM de suporte específicas|Atribuição de política|Lista de regiões em que o Observador de Rede deve ser habilitado|Para ver uma lista completa de regiões, use Get-AzLocation|
|Auditar as recomendações do Azure Security Benchmark e implantar Extensões de VM de suporte específicas|Atribuição de política|Rede virtual em que as VMs devem estar conectadas|Exemplo: /subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroupName/providers/Microsoft.Network/virtualNetworks/Name|
|Auditar as recomendações do Azure Security Benchmark e implantar Extensões de VM de suporte específicas|Atribuição de política|Gateway de rede que as redes virtuais devem usar|Exemplo: /subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworkGateways/Name|
|Auditar as recomendações do Azure Security Benchmark e implantar Extensões de VM de suporte específicas|Atribuição de política|Lista de IDs de workspace em que os agentes do Log Analytics devem se conectar|Uma lista separada por ponto e vírgula das IDs de workspace às quais o agente do Log Analytics deve estar conectado|
|Auditar as recomendações do Azure Security Benchmark e implantar Extensões de VM de suporte específicas|Atribuição de política|Lista de tipos de recurso que devem ter os logs de diagnóstico habilitados|Auditar configuração de diagnóstico para tipos de recursos selecionados|
|Auditar as recomendações do Azure Security Benchmark e implantar Extensões de VM de suporte específicas|Atribuição de política|Última versão do PHP|Versão mais recente do PHP com suporte para Serviços de Aplicativos|
|Auditar as recomendações do Azure Security Benchmark e implantar Extensões de VM de suporte específicas|Atribuição de política|Última versão do Java|Versão mais recente do Java com suporte para Serviços de Aplicativos|
|Auditar as recomendações do Azure Security Benchmark e implantar Extensões de VM de suporte específicas|Atribuição de política|Última versão do Python do Windows|Versão mais recente do Python com suporte para Serviços de Aplicativos|
|Auditar as recomendações do Azure Security Benchmark e implantar Extensões de VM de suporte específicas|Atribuição de política|Versão mais recente do Python do Linux|Versão mais recente do Python com suporte para Serviços de Aplicativos|

## <a name="next-steps"></a>Próximas etapas

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md).