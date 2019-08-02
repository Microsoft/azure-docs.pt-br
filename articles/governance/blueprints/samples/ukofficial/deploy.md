---
title: Exemplo-Blueprint oficial do Reino Unido e do Reino Unido NHS-etapas de implantação
description: Implante as etapas dos exemplos do Blueprint oficial do Reino Unido e do Reino Unido NHS.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d720f220b8903d960e7bba2a75aa5a86bd0391ce
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68225991"
---
# <a name="deploy-the-uk-official-and-uk-nhs-blueprint-samples"></a>Implantar os exemplos do Blueprint oficial do Reino Unido e do Reino Unido NHS

Para implantar os exemplos de plano gráfico do Reino Unido e do Reino Unido NHS, as seguintes etapas devem ser executadas:

> [!div class="checklist"]
> - Criar um blueprint com base na amostra
> - Marcar sua cópia do exemplo como **Publicado**
> - Atribuir a sua cópia do blueprint a uma assinatura existente

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar um blueprint com base na amostra

Primeiro, implemente a amostra de blueprint criando um blueprint no ambiente usando a amostra como ponto de partida.

1. Selecione **Todos os serviços** e pesquise e selecione **Política** no painel esquerdo. Na página **Política**, selecione **Blueprints**.

1. Na página **Introdução** à esquerda, selecione o botão **Criar** em _Criar um blueprint_.

1. Encontre o exemplo **oficial do Reino Unido** ou do **Reino Unido do NHS** em _outros exemplos_ e selecione **usar este exemplo**.

1. Insira as informações _Básicas_ do exemplo de blueprint:

   - **Nome do blueprint**: forneça um nome para a sua cópia do exemplo de blueprint.
   - **Localização da definição**: Use as reticências e selecione o grupo de gerenciamento em que deseja salvar a cópia da amostra.

1. Selecione a guia _Artefatos_ na parte superior da página ou clique em **Avançar: Artefatos** na parte inferior da página.

1. Examine a lista de artefatos que compõem o exemplo de blueprint. Muitos dos artefatos têm parâmetros que definiremos mais tarde. Selecione **Salvar Rascunho** quando terminar de examinar o exemplo de blueprint.

## <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

Agora a cópia do exemplo de blueprint foi criada em seu ambiente. Ela é criada no modo **Rascunho** e deve ser **Publicada** antes de ser atribuída e implantada. A cópia do exemplo Blueprint pode ser personalizada para seu ambiente e necessidades, mas essa modificação pode movê-la para fora do padrão.

1. Selecione **Todos os serviços** e pesquise e selecione **Política** no painel esquerdo. Na página **Política**, selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a cópia da amostra de blueprint e, em seguida, selecione-a.

1. Selecione **Publicar blueprint** na parte superior da página. Na nova página à direita, informe a **Versão** da sua cópia da amostra de blueprint. Essa propriedade será útil se você fizer uma modificação mais tarde. Forneça **observações de alteração** , como "primeira versão publicada do exemplo do Reino Unido ou do RU NHS Blueprint". Em seguida, selecione **Publicar** na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Quando a cópia do exemplo de blueprint for **Publicada** com êxito, ele poderá ser atribuído a uma assinatura do grupo de gerenciamento em que ele foi salvo. Esta é a etapa em que os parâmetros são fornecidos para tornar exclusiva cada implantação da cópia do exemplo de blueprint.

1. Selecione **Todos os serviços** e pesquise e selecione **Política** no painel esquerdo. Na página **Política**, selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a cópia da amostra de blueprint e, em seguida, selecione-a.

1. Selecione **Atribuir blueprint** na parte superior da página de definição de blueprint.

1. Forneça os valores de parâmetro para a atribuição de blueprint:

   - Noções básicas

     - **Assinaturas**: Selecione uma ou mais das assinaturas que estão no grupo de gerenciamento em que você salvou a cópia do exemplo de blueprint. Se você selecionar mais de uma assinatura, será criada uma atribuição para cada uma, usando os parâmetros inseridos.
     - **Nome da atribuição**: O nome é pré-preenchido para você com base no nome do blueprint.
       Altere-o conforme necessário ou mantenha-o como está.
     - **Localização**: Selecione uma região para a identidade gerenciada a ser criada. O Blueprint do Azure usa essa identidade gerenciada para implantar todos os artefatos no blueprint atribuído. Para saber mais, veja [identidades gerenciadas para recursos do Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Versão de definição de blueprint**: Escolha uma versão **Publicada** da cópia da amostra de blueprint.

   - Bloquear atribuição

     Selecione a configuração de bloqueio de blueprint para o seu ambiente. Para obter mais informações, consulte [bloqueio de recursos de projetos](../../concepts/resource-locking.md).

   - Identidade Gerenciada

     Deixe a opção de identidade gerenciada _atribuída ao sistema_ padrão.

   - Parâmetros do artefato

     Os parâmetros definidos nesta seção se aplicam ao artefato sob o qual ele está definido. Esses são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters), pois são definidos durante a atribuição do blueprint. Para obter uma lista completa ou parâmetros de artefato e suas descrições, confira a [Tabela de parâmetros de artefato](#artifact-parameters-table).

1. Depois que todos os parâmetros forem inseridos, selecione **Atribuir** na parte inferior da página. A atribuição de blueprint é criada, e a implantação de artefato é iniciada. A implantação leva aproximadamente uma hora. Para verificar o status da implantação, abra a atribuição de blueprint.

> [!WARNING]
> As amostras internas de blueprint e o serviço Azure Blueprints são **gratuitos**. Os recursos do Azure são [precificados por produto](https://azure.microsoft.com/pricing/). Use a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/) para estimar o custo da execução de recursos implantados por essa amostra de blueprint.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefato

A seguinte tabela fornece uma lista dos parâmetros de artefato de blueprint:

Nome do artefato|Tipo de artefato|Nome do parâmetro|DESCRIÇÃO|
|-|-|-|-|
|Iniciativa Blueprint para o Reino Unido ou o NHS UK|Atribuição de política |Tipos de recurso para auditar logs de diagnóstico (política: Iniciativa Blueprint para o Reino Unido ou o NHS UK) |Lista de tipos de recursos para auditoria se a configuração do log de diagnóstico está habilitada.  Para obter valores aceitáveis, consulte [serviços, esquemas e categorias com suporte para logs de diagnóstico do Azure](../../../../azure-monitor/platform/diagnostic-logs-schema.md). |
|\[Versão Prévia\]: Implantar o Agente do Log Analytics para VMs do Linux |Atribuição de política |Opcional: Lista de imagens de VM com suporte do SO Linux para adicionar ao escopo (política: \[Versão Prévia\]: Implantar o agente de Log Analytics para VMs do Linux) |Adicional O valor padrão é _None_. Para obter mais informações, consulte [criar um log Analytics espaço de trabalho no portal do Azure](../../../../azure-monitor/learn/quick-create-workspace.md). |
|\[Versão Prévia\]: Implantar o Agente do Log Analytics para VMs do Windows |Atribuição de política |Opcional: Lista de imagens de VM que têm suporte para o sistema operacional Windows para adicionar ao escopo (política: \[Versão Prévia\]: Implantar o agente de Log Analytics para VMs do Windows) |Adicional O valor padrão é _None_. Para obter mais informações, consulte [criar um log Analytics espaço de trabalho no portal do Azure](../../../../azure-monitor/learn/quick-create-workspace.md). |

## <a name="next-steps"></a>Próximas etapas

Agora que você analisou as etapas para implantar os exemplos do plano gráfico do Reino Unido e do Reino Unido NHS, visite os seguintes artigos para saber mais sobre a visão geral e o mapeamento de controle:

> [!div class="nextstepaction"]
> [Plantas NHSis do Reino Unido e do Reino Unido – visão geral dos](./index.md)
> plantas do Reino Unido e da NHS do Reino Unido[– mapeamento de controle](./control-mapping.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).
