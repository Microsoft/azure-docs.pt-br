---
title: Implantar a amostra de blueprint da zona de destino de Migração do CAF
description: Etapas de implantação do modelo de blueprint da zona de aterrissagem de Migração do CAF, incluindo detalhes do parâmetro de artefato de blueprint.
ms.date: 03/12/2021
ms.topic: sample
ms.openlocfilehash: d5aed1d492fea102598a22921d04ed8681c727cb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103470108"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-migrate-landing-zone-blueprint-sample"></a>Implantar o Microsoft Cloud Adoption Framework para a amostra de blueprint da zona de aterrissagem das Migrações para Azure

Para implantar o modelo de blueprint da zona de aterrissagem de Migração do CAF do Azure Blueprints, é necessário executar as seguintes etapas:

> [!div class="checklist1"]
> - Recomendada a implantação da amostra de blueprint do [CAF Foundation](../caf-foundation/index.md)

> [!div class="checklist2"]
> - Criar um blueprint com base na amostra
> - Marcar sua cópia do exemplo como **Publicado**
> - Atribuir a sua cópia do blueprint a uma assinatura existente

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar um blueprint com base na amostra

Primeiro, implemente a amostra de blueprint criando um blueprint no ambiente usando a amostra como ponto de partida.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Na página **Introdução** à esquerda, selecione o botão **Criar** em _Criar um blueprint_.

1. Encontre o modelo de blueprint da **zona de aterrissagem da Migração do CAF** em _Outras Amostras_ e selecione **Usar esta amostra**.

1. Insira as informações _Básicas_ do exemplo de blueprint:
   - **Nome do blueprint** Forneça um nome para a cópia da amostra de blueprint da zona de aterrissagem das migrações para CAF.
   - **Localização da definição** Use as reticências e selecione o grupo de gerenciamento em que deseja salvar a cópia da amostra.

1. Selecione a guia _Artefatos_ na parte superior da página ou clique em **Avançar: Artefatos** na parte inferior da página.

1. Examine a lista de artefatos que compõem o exemplo de blueprint. Muitos dos artefatos têm parâmetros que definiremos mais tarde. Selecione **Salvar Rascunho** quando terminar de examinar o exemplo de blueprint.

## <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

Agora a cópia do exemplo de blueprint foi criada em seu ambiente. Ela é criada no modo **Rascunho** e deve ser **Publicada** antes de ser atribuída e implantada. A cópia da amostra de blueprint pode ser personalizada de acordo com seu ambiente e suas necessidades, mas essa modificação poderá desviá-lo das diretrizes da zona de aterrissagem das migrações para CAF.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a cópia da amostra de blueprint e, em seguida, selecione-a.

1. Selecione **Publicar blueprint** na parte superior da página. Na nova página à direita, informe a **Versão** da sua cópia da amostra de blueprint. Essa propriedade será útil se você fizer uma modificação mais tarde. Forneça **Notas de alterações**, como "Primeira versão publicada da amostra de blueprint da zona de aterrissagem das migrações para CAF". Em seguida, selecione **Publicar** na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Quando a cópia do exemplo de blueprint for **Publicada** com êxito, ele poderá ser atribuído a uma assinatura do grupo de gerenciamento em que ele foi salvo. Esta é a etapa em que os parâmetros são fornecidos para tornar exclusiva cada implantação da cópia do exemplo de blueprint.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a cópia da amostra de blueprint e, em seguida, selecione-a.

1. Selecione **Atribuir blueprint** na parte superior da página de definição de blueprint.

1. Forneça os valores de parâmetro para a atribuição de blueprint:

   - Noções básicas
     - **Assinaturas**: Selecione uma ou mais das assinaturas que estão no grupo de gerenciamento em que você salvou a cópia do exemplo de blueprint. Se você selecionar mais de uma assinatura, será criada uma atribuição para cada uma, usando os parâmetros inseridos.
     - **Nome da atribuição**: O nome é pré-preenchido para você com base no nome do blueprint.
       Altere-o conforme necessário ou mantenha-o como está.
     - **Localização**: Selecione uma região para a identidade gerenciada a ser criada.
     - O Blueprint do Azure usa essa identidade gerenciada para implantar todos os artefatos no blueprint atribuído.
       Para saber mais, veja [identidades gerenciadas para recursos do Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Versão de definição de blueprint**: Escolha uma versão **Publicada** da cópia da amostra de blueprint.
    
   - Bloquear atribuição

     Selecione a configuração de bloqueio de blueprint para o seu ambiente. Para obter mais informações, consulte [bloqueio de recursos de projetos](../../concepts/resource-locking.md).

   - Identidade Gerenciada

     Escolha a opção de identidade gerenciada _atribuída ao sistema_ padrão ou a opção de identidade _atribuída ao usuário_.

   - Parâmetros de blueprint

     Os parâmetros definidos nesta seção são usados por muitos dos artefatos na definição de blueprint para fornecer coerência.

       - **Organização**: Insira o nome de sua organização, como Contoso ou Fabrikam; precisa ser exclusivo.
       - **AzureRegion**: Selecione uma região do Azure para implantação.
       
   - Parâmetros do artefato

     Os parâmetros definidos nesta seção se aplicam ao artefato sob o qual ele está definido. Esses são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters), pois são definidos durante a atribuição do blueprint. Para obter uma lista completa ou parâmetros de artefato e suas descrições, confira a [Tabela de parâmetros de artefato](#artifact-parameters-table).

1. Depois que todos os parâmetros forem inseridos, selecione **Atribuir** na parte inferior da página. A atribuição de blueprint é criada, e a implantação de artefato é iniciada. A implantação leva aproximadamente cinco minutos. Para verificar o status da implantação, abra a atribuição de blueprint.

> [!WARNING]
> As amostras internas de blueprint e o serviço Azure Blueprints são **gratuitos**. Os recursos do Azure são [precificados por produto](https://azure.microsoft.com/pricing/). Use a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/) para estimar o custo da execução de recursos implantados por essa amostra de blueprint.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefato

A seguinte tabela fornece uma lista dos parâmetros de artefato de blueprint:

|Nome do artefato|Tipo de artefato|Nome do parâmetro|Descrição|
|-|-|-|-|
|Implantar a zona de aterrissagem da VNET|Modelo do Resource Manager|IPAddress_Space|**Bloqueado** – forneça o primeiro exemplo de dois octetos, 10.0|
|Implantar o Key Vault|Modelo do Resource Manager|KV-AccessPolicy|**Bloqueado** – ID de Objeto de Usuário ou Grupo para conceder permissões no Key Vault|
|Implantar o Log Analytics|Modelo do Resource Manager|LogAnalytics_DataRetention|**Bloqueado** – número de dias durante os quais os dados serão retidos no Log Analytics|
|Implantar o Log Analytics|Modelo do Resource Manager|LogAnalytics_Location|**Bloqueado** – região usada ao estabelecer o workspace|
|Implantar as Migrações para Azure|Modelo do Resource Manager|Azure_Migrate_Location|**Bloqueado** – selecione a região para implantação das Migrações para Azure|

## <a name="next-steps"></a>Próximas etapas

Agora que você examinou as etapas para implantar a amostra de blueprint da zona de aterrissagem das migrações para CAF, leia os seguintes artigos para saber mais sobre a arquitetura:

> [!div class="nextstepaction"]
> [Blueprint da zona de aterrissagem de Migração do CAF – Visão Geral](./index.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).
