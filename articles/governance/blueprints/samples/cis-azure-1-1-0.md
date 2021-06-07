---
title: Exemplo de blueprint do CIS Microsoft Azure Foundations Benchmark v1.1.0
description: Visão geral do exemplo de blueprint do CIS Microsoft Azure Foundations Benchmark v1.1.0. Este exemplo de blueprint ajuda os clientes a avaliar controles específicos.
ms.date: 03/11/2021
ms.topic: sample
ms.openlocfilehash: b6188d96971329a83bc82042a1f840bd893354eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103197796"
---
# <a name="cis-microsoft-azure-foundations-benchmark-v110-blueprint-sample"></a>Exemplo de blueprint do CIS Microsoft Azure Foundations Benchmark v1.1.0

O exemplo de blueprint do CIS Microsoft Azure Foundations Benchmark v1.1.0 fornece diretrizes de governança usando o [Azure Policy](../../policy/overview.md) que ajudam você a avaliar recomendações específicas do CIS Microsoft Azure Foundations Benchmark. Esse blueprint ajuda os clientes a implantar um conjunto principal de políticas para qualquer arquitetura implantada pelo Azure que precise implementar as recomendações do CIS Microsoft Azure Foundations Benchmark v1.1.0.

## <a name="recommendation-mapping"></a>Mapeamento de recomendação

O [mapeamento de recomendação do Azure Policy](../../policy/samples/cis-azure-1-1-0.md) fornece detalhes sobre as definições de política incluídas neste blueprint e como essas definições de política mapeiam para as **recomendações** no CIS Microsoft Azure Foundations Benchmark v1.1.0. Quando atribuídos a uma arquitetura, os recursos são avaliados pelo Azure Policy para verificar a não conformidade com definições de política atribuídas. Para saber mais, veja [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Implantar

Para implantar o exemplo de blueprint do CIS Microsoft Azure Foundations Benchmark do Azure Blueprints v1.1.0, é necessário executar as seguintes etapas:

> [!div class="checklist"]
> - Criar um blueprint com base na amostra
> - Marcar sua cópia do exemplo como **Publicado**
> - Atribuir a sua cópia do blueprint a uma assinatura existente

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

### <a name="create-blueprint-from-sample"></a>Criar um blueprint com base na amostra

Primeiro, implemente a amostra de blueprint criando um blueprint no ambiente usando a amostra como ponto de partida.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Na página **Introdução** à esquerda, selecione o botão **Criar** em _Criar um blueprint_.

1. Encontre a amostra de blueprint **CIS Microsoft Azure Foundations Benchmark v1.1.0** em _Outras Amostras_ e selecione **Usar esta amostra**.

1. Insira as informações _Básicas_ do exemplo de blueprint:

   - **Nome do blueprint**: Forneça um nome para sua cópia da amostra de blueprint do CIS Microsoft Azure Foundations Benchmark.
   - **Localização da definição**: Use as reticências e selecione o grupo de gerenciamento em que deseja salvar a cópia da amostra.

1. Selecione a guia _Artefatos_ na parte superior da página ou clique em **Avançar: Artefatos** na parte inferior da página.

1. Examine a lista de artefatos que estão incluídos no exemplo de blueprint. Muitos dos artefatos têm parâmetros que definiremos mais tarde. Selecione **Salvar Rascunho** quando terminar de examinar o exemplo de blueprint.

### <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

Agora a cópia do exemplo de blueprint foi criada em seu ambiente. Ela é criada no modo **Rascunho** e deve ser **Publicada** antes de ser atribuída e implantada. A cópia do exemplo de blueprint pode ser personalizada de acordo com seu ambiente e suas necessidades, mas essa modificação poderá desviá-lo do que é recomendado pelo CIS Microsoft Azure Foundations Benchmark v1.1.0.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a cópia da amostra de blueprint e, em seguida, selecione-a.

1. Selecione **Publicar blueprint** na parte superior da página. Na nova página à direita, informe a **Versão** da sua cópia da amostra de blueprint. Essa propriedade será útil se você fizer uma modificação mais tarde. Forneça **Notas de alterações**, como "Primeira versão publicada da amostra de blueprint do CIS Microsoft Azure Foundations Benchmark". Em seguida, selecione **Publicar** na parte inferior da página.

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
|Auditar as recomendações do CIS Microsoft Azure Foundations Benchmark v1.1.0 e implantar extensões de VM de suporte específicas|Atribuição de política|Lista de regiões em que o Observador de Rede deve ser habilitado|Uma lista de regiões separada por ponto e vírgula. Para ver uma lista completa de regiões, use Get-AzLocation. Por exemplo: eastus; eastus2|
|Auditar as recomendações do CIS Microsoft Azure Foundations Benchmark v1.1.0 e implantar extensões de VM de suporte específicas|Atribuição de política|Lista de extensões da máquina virtual aprovadas para uso|Uma lista de extensões separada por ponto e vírgula. Para ver uma lista completa de extensões da máquina virtual, use Get-AzVMExtensionImage. Por exemplo: AzureDiskEncryption; IaaSAntimalware|

## <a name="next-steps"></a>Próximas etapas

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md).