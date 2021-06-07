---
title: Implantar o exemplo de blueprint do Azure Security Benchmark Foundation
description: Etapas de implantação do exemplo de blueprint do Azure Security Benchmark Foundation, incluindo detalhes do parâmetro do artefato de blueprint.
ms.date: 03/12/2021
ms.topic: sample
ms.openlocfilehash: af41dd50c976ac6c0570b8a089211fa310ef4ef1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103232606"
---
# <a name="deploy-the-azure-security-benchmark-foundation-blueprint-sample"></a>Implantar o exemplo de blueprint do Azure Security Benchmark Foundation

Para implantar o exemplo de blueprint do Azure Security Benchmark Foundation, execute estas etapas:

> [!div class="checklist"]
> - Criar um blueprint com base na amostra
> - Marcar sua cópia do exemplo como **Publicado**
> - Atribuir a sua cópia do blueprint a uma assinatura existente

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar um blueprint com base na amostra

Primeiro, implemente a amostra de blueprint criando um blueprint no ambiente usando a amostra como ponto de partida.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Na página **Introdução** à esquerda, selecione o botão **Criar** em _Criar um blueprint_.

1. Localize o exemplo de blueprint do **Azure Security Benchmark Foundation** em _Outros Exemplos_ e selecione **Usar este exemplo**.

1. Insira as informações _Básicas_ do exemplo de blueprint:

   - **Nome do blueprint**: forneça um nome para a cópia do exemplo de blueprint do Azure Security Benchmark Foundation.
   - **Localização da definição**: Use as reticências e selecione o grupo de gerenciamento em que deseja salvar a cópia da amostra.

1. Selecione a guia _Artefatos_ na parte superior da página ou clique em **Avançar: Artefatos** na parte inferior da página.

1. Examine a lista de artefatos que compõem o exemplo de blueprint. Muitos dos artefatos têm parâmetros que definiremos mais tarde. Selecione **Salvar Rascunho** quando terminar de examinar o exemplo de blueprint.

## <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

Agora a cópia do exemplo de blueprint foi criada em seu ambiente. Ela é criada no modo **Rascunho** e deve ser **Publicada** antes de ser atribuída e implantada. A cópia do exemplo de blueprint pode ser personalizada de acordo com seu ambiente e suas necessidades, mas essa modificação poderá desviá-la do blueprint do Azure Security Benchmark Foundation.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a cópia da amostra de blueprint e, em seguida, selecione-a.

1. Selecione **Publicar blueprint** na parte superior da página. Na nova página à direita, informe a **Versão** da sua cópia da amostra de blueprint. Essa propriedade será útil se você fizer uma modificação mais tarde. Forneça **Notas de alterações** como "Primeira versão publicada do exemplo de blueprint do Azure Security Benchmark Foundation". Em seguida, selecione **Publicar** na parte inferior da página.

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
    
     - **Prefixo dos recursos e dos grupos de recursos**: essa cadeia de caracteres é usada como um prefixo para todos os nomes de recursos e de grupos de recursos
     - **Nome do hub**: nome do hub
     - **Retenção de log (dias)** : número de dias que os logs são retidos; a inserção de '0' retém os logs por tempo indefinido
     - **Implantar hub**: insira 'true' ou 'false' para especificar se a atribuição implanta os componentes do hub da arquitetura
     - **Localização do hub**: localização do grupo de recursos do hub
     - **Endereços IP de destino**: endereços IP de destino para a conectividade de saída; lista separada por vírgula de endereços IP ou prefixos de intervalo de IP
     - **Nome do Observador de Rede**: nome do recurso de Observador de Rede
     - **Nome do grupo de recursos do Observador de Rede**: nome do grupo de recursos do Observador de Rede
     - **Habilitar proteção contra DDoS**: insira 'true' ou 'false' para especificar se a Proteção contra DDoS está ou não habilitada na rede virtual
     
    > [!NOTE] 
    > Se o Observador de Rede já está habilitado, é recomendável usar o grupo de recursos do Observador de Rede existente. Você também precisa fornecer o local do grupo de recursos do Observador de Rede existente para o parâmetro de artefato **Local do grupo de recursos do Observador de Rede**.

   - Parâmetros do artefato

     Os parâmetros definidos nesta seção se aplicam ao artefato sob o qual ele está definido. Esses são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters), pois são definidos durante a atribuição do blueprint. Para obter uma lista completa ou parâmetros de artefato e suas descrições, confira a [Tabela de parâmetros de artefato](#artifact-parameters-table).

1. Depois que todos os parâmetros forem inseridos, selecione **Atribuir** na parte inferior da página. A atribuição de blueprint é criada, e a implantação de artefato é iniciada. A implantação leva aproximadamente uma hora. Para verificar o status da implantação, abra a atribuição de blueprint.

> [!WARNING]
> As amostras internas de blueprint e o serviço Azure Blueprints são **gratuitos**. Os recursos do Azure são [precificados por produto](https://azure.microsoft.com/pricing/). Use a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/) para estimar o custo da execução de recursos implantados por essa amostra de blueprint.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefato

A seguinte tabela fornece uma lista dos parâmetros de blueprint:

|Nome do artefato|Tipo de artefato|Nome do parâmetro|Descrição|
|-|-|-|-|
|Grupo de recursos do hub|Resource group|Nome do grupo de recursos|Bloqueado – Concatena o prefixo com o nome do hub|
|Grupo de recursos do hub|Resource group|Localização do grupo de recursos|Bloqueado – Usa a localização do hub|
|Modelo do Firewall do Azure|Modelo do Resource Manager|Endereço IP privado do Firewall do Azure||
|Modelo de Diagnóstico e do Azure Log Analytics|Modelo do Resource Manager|Localização do workspace do Log Analytics|Localização em que o workspace do Log Analytics foi criado; execute `Get-AzLocation | Where-Object Providers -like 'Microsoft.OperationalInsights' | Select DisplayName` no Azure PowerShell para ver as regiões disponíveis|
|Modelo de Diagnóstico e do Azure Log Analytics|Modelo do Resource Manager|ID da conta de Automação do Azure (opcional)|ID do recurso da conta de Automação; usada para criar um serviço vinculado entre o Log Analytics e uma conta de Automação|
|Modelo do grupo de segurança de rede do Azure|Modelo do Resource Manager|Habilitar os logs de fluxo do NSG|Insira 'true' ou 'false' para habilitar ou desabilitar os logs de fluxo do NSG|
|Modelo de hub da Rede Virtual do Azure|Modelo do Resource Manager|Prefixo de endereço de rede virtual|Prefixo de endereço de rede virtual para a rede virtual hub|
|Modelo de hub da Rede Virtual do Azure|Modelo do Resource Manager|Prefixo de endereço de sub-rede do firewall|Prefixo de endereço de sub-rede do firewall para a rede virtual hub|
|Modelo de hub da Rede Virtual do Azure|Modelo do Resource Manager|Prefixo de endereço de sub-rede do Bastion|Prefixo de endereço de sub-rede do Bastion para a rede virtual hub|
|Modelo de hub da Rede Virtual do Azure|Modelo do Resource Manager|Prefixo de endereço de sub-rede do gateway|Prefixo de endereço de sub-rede do gateway para a rede virtual hub|
|Modelo de hub da Rede Virtual do Azure|Modelo do Resource Manager|Prefixo de endereço de sub-rede de gerenciamento|Prefixo de endereço de sub-rede de gerenciamento para a rede virtual hub|
|Modelo de hub da Rede Virtual do Azure|Modelo do Resource Manager|Prefixo de endereço de sub-rede do Jumpbox|Prefixo de endereço de sub-rede do Jumpbox para a rede virtual hub|
|Modelo de hub da Rede Virtual do Azure|Modelo do Resource Manager|Nomes de endereço de sub-rede (opcional)|Matriz de nomes de sub-rede para implantação na rede virtual hub; por exemplo, "subnet1", "subnet2"|
|Modelo de hub da Rede Virtual do Azure|Modelo do Resource Manager|Prefixos de endereço de sub-rede (opcional)|Matriz de prefixos de endereço IP para sub-redes opcionais para a rede virtual hub; por exemplo, "10.0.7.0/24", "10.0.8.0/24"|
|Grupo de recursos do spoke|Resource group|Nome do grupo de recursos|Bloqueado – Concatena o prefixo com o nome do spoke|
|Grupo de recursos do spoke|Resource group|Localização do grupo de recursos|Bloqueado – Usa a localização do hub|
|Modelo de spoke da Rede Virtual do Azure|Modelo do Resource Manager|Implantar spoke|Insira 'true' ou 'false' para especificar se a atribuição implanta os componentes do spoke da arquitetura|
|Modelo de spoke da Rede Virtual do Azure|Modelo do Resource Manager|ID da assinatura do hub|ID da assinatura na qual o hub é implantado; o valor padrão é a assinatura em que a definição de blueprint está localizada|
|Modelo de spoke da Rede Virtual do Azure|Modelo do Resource Manager|Nome do spoke|Nome do spoke|
|Modelo de spoke da Rede Virtual do Azure|Modelo do Resource Manager|Prefixo de endereço da Rede Virtual|Prefixo de endereço de Rede Virtual para a rede virtual spoke|
|Modelo de spoke da Rede Virtual do Azure|Modelo do Resource Manager|Prefixo de endereço de sub-rede|Prefixo de endereço de sub-rede para a rede virtual spoke|
|Modelo de spoke da Rede Virtual do Azure|Modelo do Resource Manager|Nomes de endereço de sub-rede (opcional)|Matriz de nomes de sub-rede para implantação na rede virtual spoke; por exemplo, "subnet1", "subnet2"|
|Modelo de spoke da Rede Virtual do Azure|Modelo do Resource Manager|Prefixos de endereço de sub-rede (opcional)|Matriz de prefixos de endereço IP para sub-redes opcionais para a rede virtual spoke; por exemplo, "10.0.7.0/24", "10.0.8.0/24"|
|Modelo de spoke da Rede Virtual do Azure|Modelo do Resource Manager|Implantar spoke|Insira 'true' ou 'false' para especificar se a atribuição implanta os componentes do spoke da arquitetura|
|Modelo do Observador de Rede do Azure|Modelo do Resource Manager|Localização do Observador de Rede|Local do recurso de Observador de Rede|
|Modelo do Observador de Rede do Azure|Modelo do Resource Manager|Localização do grupo de recursos do Observador de Rede|Se o Observador de Rede já estiver habilitado, esse valor de parâmetro **precisará** corresponder à localização do grupo de recursos existente do Observador de Rede.|

## <a name="troubleshooting"></a>Solução de problemas

Se encontrar o erro `The resource group 'NetworkWatcherRG' failed to deploy due to the
following error: Invalid resource group location '{location}'. The Resource group already exists in
location '{location}'.`, verifique se o parâmetro de blueprint **Nome do grupo de recursos do Observador de Rede** especifica o nome do grupo de recursos do Observador de Rede existente e se o parâmetro de artefato **Local do grupo de recursos do Observador de Rede** especifica o local do grupo de recursos do Observador de Rede existente.

## <a name="next-steps"></a>Próximas etapas

Agora que você examinou as etapas para implantar o exemplo de blueprint do Azure Security Benchmark Foundation, acesse o seguinte artigo para saber mais sobre a arquitetura:

> [!div class="nextstepaction"]
> [Blueprint do Azure Security Benchmark Foundation – Visão geral](./index.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).
