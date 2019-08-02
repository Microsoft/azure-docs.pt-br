---
title: Amostra – blueprint do NIST SP 800-53 R4 – etapas de implantação
description: Etapas de implantação da amostra de blueprint NIST SP 800-53 R4.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 206763e2d17f4ad711ff5fd897f1429814e61837
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68228862"
---
# <a name="deploy-the-nist-sp-800-53-r4-blueprint-sample"></a>Implantar a amostra de blueprint NIST SP 800-53 R4

Para implantar a amostra de blueprint NIST SP 800-53 R4 do Azure Blueprints, as seguintes etapas precisam ser executadas:

> [!div class="checklist"]
> - Criar um blueprint com base na amostra
> - Marcar sua cópia do exemplo como **Publicado**
> - Atribuir a sua cópia do blueprint a uma assinatura existente

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar um blueprint com base na amostra

Primeiro, implemente a amostra de blueprint criando um blueprint no ambiente usando a amostra como ponto de partida.

1. Selecione **Todos os serviços** e pesquise e selecione **Política** no painel esquerdo. Na página **Política**, selecione **Blueprints**.

1. Na página **Introdução** à esquerda, selecione o botão **Criar** em _Criar um blueprint_.

1. Encontre a amostra de blueprint **NIST SP 800-53 R4** em _Outras Amostras_ e selecione **Usar esta amostra**.

1. Insira as informações _Básicas_ do exemplo de blueprint:

   - **Nome do blueprint**: Forneça um nome para a sua cópia do exemplo de blueprint NIST SP 800-53 R4.
   - **Localização da definição**: Use as reticências e selecione o grupo de gerenciamento em que deseja salvar a cópia da amostra.

1. Selecione a guia _Artefatos_ na parte superior da página ou clique em **Avançar: Artefatos** na parte inferior da página.

1. Examine a lista de artefatos que compõem o exemplo de blueprint. Muitos dos artefatos têm parâmetros que definiremos mais tarde. Selecione **Salvar Rascunho** quando terminar de examinar o exemplo de blueprint.

## <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

Agora a cópia do exemplo de blueprint foi criada em seu ambiente. Ela é criada no modo **Rascunho** e deve ser **Publicada** antes de ser atribuída e implantada. A cópia da amostra de blueprint pode ser personalizada de acordo com seu ambiente e suas necessidades, mas essa modificação poderá desviá-lo dos controles NIST SP 800-53.

1. Selecione **Todos os serviços** e pesquise e selecione **Política** no painel esquerdo. Na página **Política**, selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a cópia da amostra de blueprint e, em seguida, selecione-a.

1. Selecione **Publicar blueprint** na parte superior da página. Na nova página à direita, informe a **Versão** da sua cópia da amostra de blueprint. Essa propriedade será útil se você fizer uma modificação mais tarde. Forneça **Notas de alterações**, como "Primeira versão publicada da amostra de blueprint NIST SP 800-53 R4". Em seguida, selecione **Publicar** na parte inferior da página.

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

|Nome do artefato|Tipo de artefato|Nome do parâmetro|DESCRIÇÃO|
|-|-|-|-|
|\[Versão Prévia\]: Auditar controles NIST SP 800-53 R4 e implantar extensões de VM específicas para dar suporte aos requisitos de auditoria|Atribuição de política|ID do workspace do Log Analytics para a qual as VMs devem ser configuradas|Esta é a ID (GUID) do workspace do Log Analytics para a qual as VMs devem ser configuradas.|
|\[Versão Prévia\]: Auditar controles NIST SP 800-53 R4 e implantar extensões de VM específicas para dar suporte aos requisitos de auditoria|Atribuição de política|Lista de tipos de recurso que devem ter os logs de diagnóstico habilitados|Lista de tipos de recurso a serem auditados se a configuração do log de diagnóstico não estiver habilitada. Os valores aceitáveis podem ser encontrados em [Esquemas de logs de diagnóstico do Azure Monitor](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|\[Versão Prévia\]: Auditar controles NIST SP 800-53 R4 e implantar extensões de VM específicas para dar suporte aos requisitos de auditoria|Atribuição de política|Lista de usuários que devem ser excluídos do grupo de administradores de VM do Windows|Uma lista separada por ponto e vírgula de membros que devem ser excluídos do grupo local de Administradores. Por exemplo: Administrador; myUser1; myUser2|
|\[Versão Prévia\]: Auditar controles NIST SP 800-53 R4 e implantar extensões de VM específicas para dar suporte aos requisitos de auditoria|Atribuição de política|Lista de usuários que devem ser incluídos no grupo de administradores de VM do Windows|Uma lista separada por ponto e vírgula de membros que devem ser incluídos no grupo local de Administradores. Por exemplo: Administrador; myUser1; myUser2|
|\[Versão Prévia\]: Implantar o Agente do Log Analytics no VMSS (Conjuntos de Dimensionamento de VMs) do Linux|Atribuição de política|Workspace do Log Analytics para VMSS (Conjuntos de Dimensionamento de VMs) do Linux|Se este workspace estiver fora do escopo da atribuição, você deverá conceder permissões de 'Colaborador do Log Analytics' (ou semelhantes) à ID da entidade de segurança da atribuição da política.|
|\[Versão Prévia\]: Implantar o Agente do Log Analytics no VMSS (Conjuntos de Dimensionamento de VMs) do Linux|Atribuição de política|Opcional: Lista de imagens de VM compatíveis com o sistema operacional Linux a serem adicionadas ao escopo|Uma matriz vazia pode ser usada para indicar a inexistência de parâmetros opcionais: \[\]|
|\[Versão Prévia\]: Implantar o Agente do Log Analytics para VMs do Linux|Atribuição de política|Workspace do Log Analytics para VMs Linux|Se este workspace estiver fora do escopo da atribuição, você deverá conceder permissões de 'Colaborador do Log Analytics' (ou semelhantes) à ID da entidade de segurança da atribuição da política.|
|\[Versão Prévia\]: Implantar o Agente do Log Analytics para VMs do Linux|Atribuição de política|Opcional: Lista de imagens de VM compatíveis com o sistema operacional Linux a serem adicionadas ao escopo|Uma matriz vazia pode ser usada para indicar a inexistência de parâmetros opcionais: \[\]|
|\[Versão Prévia\]: Implantar o Agente do Log Analytics no VMSS (Conjuntos de Dimensionamento de VMs) do Windows|Atribuição de política|Workspace do Log Analytics para VMSS (Conjuntos de Dimensionamento de VMs) do Windows|Se este workspace estiver fora do escopo da atribuição, você deverá conceder permissões de 'Colaborador do Log Analytics' (ou semelhantes) à ID da entidade de segurança da atribuição da política.|
|\[Versão Prévia\]: Implantar o Agente do Log Analytics no VMSS (Conjuntos de Dimensionamento de VMs) do Windows|Atribuição de política|Opcional: Lista de imagens de VM compatíveis com o sistema operacional Windows a serem adicionadas ao escopo|Uma matriz vazia pode ser usada para indicar a inexistência de parâmetros opcionais: \[\]|
|\[Versão Prévia\]: Implantar o Agente do Log Analytics para VMs do Windows|Atribuição de política|Workspace do Log Analytics para VMs do Windows|Se este workspace estiver fora do escopo da atribuição, você deverá conceder permissões de 'Colaborador do Log Analytics' (ou semelhantes) à ID da entidade de segurança da atribuição da política.|
|\[Versão Prévia\]: Implantar o Agente do Log Analytics para VMs do Windows|Atribuição de política|Opcional: Lista de imagens de VM compatíveis com o sistema operacional Windows a serem adicionadas ao escopo|Uma matriz vazia pode ser usada para indicar a inexistência de parâmetros opcionais: \[\]|
|Implantar a Proteção Avançada contra Ameaças em Contas de Armazenamento|Atribuição de política|Efeito|As informações sobre os efeitos da política podem [Compreender os efeitos do Azure Policy](../../../policy/concepts/effects.md)|
|Implantar Auditoria em servidores SQL|Atribuição de política|O valor em dias do período de retenção (0 é uma indicação de retenção ilimitada)|Dias de retenção (opcional, 180 dias se não for especificado)|
|Implantar Auditoria em servidores SQL|Atribuição de política|Nome do grupo de recursos da conta de armazenamento para auditoria do SQL Server|A auditoria grava eventos de banco de dados em um log de auditoria na sua conta de Armazenamento do Azure (uma conta de armazenamento será criada em cada região onde um SQL Server é criado e será compartilhada por todos os servidores na região). Importante: para um funcionamento adequado da Auditoria, não exclua nem renomeie o grupo de recursos nem as contas de armazenamento.|
|Implantar configurações de diagnóstico para Grupos de Segurança de Rede|Atribuição de política|Prefixo da conta de armazenamento para diagnóstico do grupo de segurança de rede|Este prefixo será combinado com o local de grupo de segurança de rede para formar o nome da conta de armazenamento criada.|
|Implantar configurações de diagnóstico para Grupos de Segurança de Rede|Atribuição de política|Nome do grupo de recursos da conta de armazenamento para diagnóstico do grupo de segurança de rede (precisa existir)|O nome do grupo de recursos em que a conta de armazenamento será criada. Esse grupo de recursos já precisa existir.|

## <a name="next-steps"></a>Próximas etapas

Agora que você examinou as etapas para implantar a amostra de blueprint NIST SP 800-53 R4, leia os seguintes artigos para saber mais sobre o blueprint e o mapeamento de controle:

> [!div class="nextstepaction"]
> [Blueprint NIST SP 800-53 R4 – visão geral](./index.md)
> [Blueprint NIST SP 800-53 R4 – mapeamento de controle](./control-mapping.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).
