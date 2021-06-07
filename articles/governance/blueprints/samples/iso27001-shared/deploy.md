---
title: Implantar a amostra de blueprint dos Serviços Compartilhados ISO 27001
description: Etapas de implantação do modelo de blueprint dos Serviços Compartilhados da ISO 27001, incluindo detalhes do parâmetro do artefato de blueprint.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: 2eb9ed2199b28ad7cb349e6088c3cc583d8f011d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100577325"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Implantar a amostra de blueprint dos Serviços Compartilhados ISO 27001

Para implantar a amostra de blueprint dos Serviços Compartilhados ISO 27001 do Azure Blueprints, as seguintes etapas precisam ser executadas:

> [!div class="checklist"]
> - Criar um blueprint com base na amostra
> - Marcar sua cópia do exemplo como **Publicado**
> - Atribuir a sua cópia do blueprint a uma assinatura existente

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar um blueprint com base na amostra

Primeiro, implemente a amostra de blueprint criando um blueprint no ambiente usando a amostra como ponto de partida.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Na página **Introdução** à esquerda, selecione o botão **Criar** em _Criar um blueprint_.

1. Localize a amostra de blueprint **ISO 27001: Serviços Compartilhados** em _Outras Amostras_ e selecione **Usar esta amostra**.

1. Insira as informações _Básicas_ do exemplo de blueprint:

   - **Nome do blueprint**: Forneça um nome para a cópia da amostra de blueprint dos Serviços Compartilhados ISO 27001.
   - **Localização da definição**: Use as reticências e selecione o grupo de gerenciamento em que deseja salvar a cópia da amostra.

1. Selecione a guia _Artefatos_ na parte superior da página ou clique em **Avançar: Artefatos** na parte inferior da página.

1. Examine a lista de artefatos que compõem o exemplo de blueprint. Muitos dos artefatos têm parâmetros que definiremos mais tarde. Selecione **Salvar Rascunho** quando terminar de examinar o exemplo de blueprint.

## <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

Agora a cópia do exemplo de blueprint foi criada em seu ambiente. Ela é criada no modo **Rascunho** e deve ser **Publicada** antes de ser atribuída e implantada. A cópia da amostra de blueprint pode ser personalizada de acordo com seu ambiente e suas necessidades, mas essa modificação poderá desviá-lo do padrão ISO 27001.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a cópia da amostra de blueprint e, em seguida, selecione-a.

1. Selecione **Publicar blueprint** na parte superior da página. Na nova página à direita, informe a **Versão** da sua cópia da amostra de blueprint. Essa propriedade será útil se você fizer uma modificação mais tarde. Forneça **Notas de alterações**, como "Primeira versão publicada da amostra de blueprint ISO 27001". Em seguida, selecione **Publicar** na parte inferior da página.

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
     - **Localização**: Selecione uma região para a identidade gerenciada a ser criada. O Blueprint do Azure usa essa identidade gerenciada para implantar todos os artefatos no blueprint atribuído. Para saber mais, veja [identidades gerenciadas para recursos do Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Versão de definição de blueprint**: Escolha uma versão **Publicada** da cópia da amostra de blueprint.

   - Bloquear atribuição

     Selecione a configuração de bloqueio de blueprint para o seu ambiente. Para obter mais informações, consulte [bloqueio de recursos de projetos](../../concepts/resource-locking.md).

   - Identidade Gerenciada

     Deixe a opção de identidade gerenciada _atribuída ao sistema_ padrão.

   - Parâmetros de blueprint

     Os parâmetros definidos nesta seção são usados por muitos dos artefatos na definição de blueprint para fornecer coerência.

     - **Nome da organização**: insira um nome curto para a sua organização. Essa propriedade é usada principalmente para recursos de nomenclatura.
     - **Prefixo de endereço da sub-rede dos serviços compartilhados**: Forneça o valor de notação CIDR para colocar os recursos implantados na mesma rede.
     - **Localização dos serviços compartilhados**: Determina em que local os artefatos são implantados. Nem todos os serviços estão disponíveis em todas as localizações. Os artefatos que implantam esses serviços fornecem uma opção de parâmetro para a localização na qual esse artefato deverá ser implantado.
     - **Localização permitida (política: Iniciativa de blueprint para ISO 27001)** : Valor que indica as localizações permitidas para grupos de recursos e recursos.
     - **Workspace do Log Analytics para agentes de VM (política: Iniciativa de blueprint para ISO 27001)** : Especifica a ID do recurso de um workspace. Esse parâmetro usa uma função `concat` para construir a ID do recurso.

   - Parâmetros do artefato

     Os parâmetros definidos nesta seção se aplicam ao artefato sob o qual ele está definido. Esses são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters), pois são definidos durante a atribuição do blueprint. Para obter uma lista completa ou parâmetros de artefato e suas descrições, confira a [Tabela de parâmetros de artefato](#artifact-parameters-table).

1. Depois que todos os parâmetros forem inseridos, selecione **Atribuir** na parte inferior da página. A atribuição de blueprint é criada, e a implantação de artefato é iniciada. A implantação leva aproximadamente uma hora. Para verificar o status da implantação, abra a atribuição de blueprint.

> [!WARNING]
> As amostras internas de blueprint e o serviço Azure Blueprints são **gratuitos**. Os recursos do Azure são [precificados por produto](https://azure.microsoft.com/pricing/). Use a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/) para estimar o custo da execução de recursos implantados por essa amostra de blueprint.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefato

A seguinte tabela fornece uma lista dos parâmetros de artefato de blueprint:

|Nome do artefato|Tipo de artefato|Nome do parâmetro|Descrição|
|-|-|-|-|
|\[Versão Prévia\]: Implantar o Agente do Log Analytics no VMSS (Conjuntos de Dimensionamento de VMs) do Linux|Atribuição de política|Opcional: Lista de imagens de VM compatíveis com o sistema operacional Linux a serem adicionadas ao escopo|(Opcional) O valor padrão é _["none"]_ .|
|\[Versão Prévia\]: Implantar o Agente do Log Analytics para VMs do Linux|Atribuição de política|Opcional: Lista de imagens de VM compatíveis com o sistema operacional Linux a serem adicionadas ao escopo|(Opcional) O valor padrão é _["none"]_ .|
|\[Versão Prévia\]: Implantar o Agente do Log Analytics no VMSS (Conjuntos de Dimensionamento de VMs) do Windows|Atribuição de política|Opcional: Lista de imagens de VM compatíveis com o sistema operacional Windows a serem adicionadas ao escopo|(Opcional) O valor padrão é _["none"]_ .|
|\[Versão Prévia\]: Implantar o Agente do Log Analytics para VMs do Windows|Atribuição de política|Opcional: Lista de imagens de VM compatíveis com o sistema operacional Windows a serem adicionadas ao escopo|(Opcional) O valor padrão é _["none"]_ .|
|Tipos de recursos permitidos|Atribuição de política|Tipos de recursos permitidos|Lista de tipos de recursos permitidos a serem implantados. Essa lista é composta por todos os tipos de recursos implantados nos Serviços Compartilhados.|
|SKUs de contas de armazenamento permitidas|Atribuição de política|SKUs de armazenamento permitidos|Lista de SKUs de conta de armazenamento dos logs de diagnóstico permitidos. O valor padrão é _["Standard_LRS"]_ .|
|SKUs de máquinas virtuais permitidas|Atribuição de política|Lista de SKUs de máquina virtual com permissão de serem implantados. O valor padrão é _["Standard_DS1_v2", "Standard_DS2_v2"]_ .|
|Iniciativa de blueprint para ISO 27001|Atribuição de política|Tipos de recursos para auditar logs de diagnóstico|Lista de tipos de recurso a serem auditados se a configuração do log de diagnóstico não estiver habilitada. Os valores aceitáveis podem ser encontrados em [Esquemas de logs de diagnóstico do Azure Monitor](../../../../azure-monitor/essentials/resource-logs-schema.md#service-specific-schemas).|
|Grupo de recursos do Log Analytics|Resource group|Nome|**Bloqueado** – concatena o **Nome da organização** com `-sharedsvsc-log-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos do Log Analytics|Resource group|Location|**Bloqueado** – usa o parâmetro de blueprint.|
|Modelo do Log Analytics|Modelo do Resource Manager|Camada de serviço|Define a camada do workspace do Log Analytics. O valor padrão é _PerNode_.|
|Modelo do Log Analytics|Modelo do Resource Manager|Retenção de log em dias|Retenção de dados em dias. O valor padrão é _365_.|
|Modelo do Log Analytics|Modelo do Resource Manager|Location|Região usada para criar o workspace do Log Analytics. O valor padrão é _Oeste dos EUA 2_.|
|Grupo de recursos de rede|Resource group|Nome|**Bloqueado** – concatena o **Nome da organização** com `-sharedsvcs-net-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos de rede|Resource group|Location|**Bloqueado** – usa o parâmetro de blueprint.|
|Modelo do Firewall do Azure|Modelo do Resource Manager|IP privado do firewall do Azure|Configura o IP privado do [Firewall do Azure](../../../../firewall/overview.md). Esse valor também é usado como a tabela de rotas padrão na sub-rede dos serviços compartilhados. Deve fazer parte da notação CIDR definida no **prefixo de endereço da sub-rede do Firewall do Azure**. O valor padrão é _10.0.4.4_.|
|Modelo do Firewall do Azure|Modelo do Resource Manager|Retenção de log em dias|Retenção de dados em dias. O valor padrão é _365_.|
|Modelo do Grupo de Segurança de Rede|Modelo do Resource Manager|Retenção de log em dias|Retenção de dados em dias. O valor padrão é _365_.|
|Modelo de Rede Virtual e a Tabela de Rotas|Modelo do Resource Manager|Prefixo de endereço da Rede Virtual|A notação CIDR da rede virtual. O valor padrão é _10.0.0.0/16_.|
|Modelo de Rede Virtual e a Tabela de Rotas|Modelo do Resource Manager|Habilitar a proteção contra DDoS da Rede Virtual|Configura a proteção contra DDoS para a rede virtual. O valor padrão é _true_.|
|Modelo de Rede Virtual e a Tabela de Rotas|Modelo do Resource Manager|Prefixo de endereço da sub-rede dos Serviços Compartilhados|A notação CIDR da sub-rede dos Serviços Compartilhados. O valor padrão é _10.0.0.0/24_.|
|Modelo de Rede Virtual e a Tabela de Rotas|Modelo do Resource Manager|Prefixo de endereço da sub-rede da DMZ|A notação CIDR da sub-rede da DMZ. O valor padrão é _10.0.1.0/24_.|
|Modelo de Rede Virtual e a Tabela de Rotas|Modelo do Resource Manager|Prefixo de endereço da sub-rede do Gateway de Aplicativo|A notação CIDR da sub-rede do Gateway de Aplicativo. O valor padrão é _10.0.2.0/24_.|
|Modelo de Rede Virtual e a Tabela de Rotas|Modelo do Resource Manager|Prefixo de endereço da sub-rede do gateway de rede virtual|A notação CIDR da sub-rede do gateway de rede virtual. O valor padrão é _10.0.3.0/24_.|
|Modelo de Rede Virtual e a Tabela de Rotas|Modelo do Resource Manager|Prefixo de endereço da sub-rede do Firewall do Azure|A notação CIDR da sub-rede do [Firewall do Azure](../../../../firewall/overview.md). Deve incluir o parâmetro de **IP privado do Firewall do Azure**.|
|Grupo de recursos do Cofre de Chaves|Resource group|Nome|**Bloqueado** – concatena o **Nome da organização** com `-sharedsvcs-kv-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos do Cofre de Chaves|Resource group|Location|**Bloqueado** – usa o parâmetro de blueprint.|
|Modelo do Key Vault|Modelo do Resource Manager|Nome de usuário do administrador do jumpbox|Nome de usuário do jumpbox. Precisa corresponder ao mesmo valor da propriedade no **Modelo do jumpbox**. O valor padrão é _jb-admin-user_.|
|Modelo do Key Vault|Modelo do Resource Manager|Chave SSH ou senha do administrador do jumpbox|Chave ou senha para a conta no jumpbox. Precisa corresponder ao mesmo valor da propriedade no **Modelo do jumpbox**. Nenhum valor padrão e não pode ser deixado em branco.|
|Modelo do Key Vault|Modelo do Resource Manager|Nome de usuário do administrador de domínio|O nome de usuário usado para acessar a VM do Active Directory e para ingressar outras VMs em um domínio. Precisa corresponder ao valor da propriedade **Usuário administrador do domínio** no **Modelo do Active Directory Domain Services**. O valor padrão é _domain-admin-user_.|
|Modelo do Key Vault|Modelo do Resource Manager|Senha do administrador de domínio|Senha do usuário administrador de domínio. Nenhum valor padrão e não pode ser deixado em branco.|
|Modelo do Key Vault|Modelo do Resource Manager|ID de objeto do AAD|O identificador de objeto do AAD da conta que exige acesso à instância do Key Vault. Nenhum valor padrão e não pode ser deixado em branco. Para localizar esse valor no portal do Azure, pesquise e selecione "Usuários" em _Serviços_. Use a caixa _Nome_ para filtrar o nome da conta e selecione essa conta. Na página _Perfil do usuário_, selecione o ícone "Clique para copiar" ao lado da _ID de Objeto_.  |
|Modelo do Key Vault|Modelo do Resource Manager|Retenção de log em dias|Retenção de dados em dias. O valor padrão é _365_.|
|Modelo do Key Vault|Modelo do Resource Manager|SKU do Key Vault|Especifica o SKU do Key Vault criado. O valor padrão é _Premium_.|
|Grupo de recursos do jumpbox|Resource group|Nome|**Bloqueado** – concatena o **Nome da organização** com `-sharedsvcs-jb-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos do jumpbox|Resource group|Location|**Bloqueado** – usa o parâmetro de blueprint.|
|Modelo do jumpbox|Modelo do Resource Manager|Nome de usuário do administrador do jumpbox|O nome de usuário usado para acessar as VMs do jumpbox. Precisa corresponder ao mesmo valor da propriedade no **Modelo do Key Vault**. O valor padrão é _jb-admin-user_.|
|Modelo do jumpbox|Modelo do Resource Manager|Senha do administrador do jumpbox (ID do recurso do Key Vault)|A ID do Recurso do Key Vault. Use "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" e substitua `{subscriptionId}` pela ID de assinatura e `{orgName}` pelo parâmetro de blueprint **Nome da organização**.|
|Modelo do jumpbox|Modelo do Resource Manager|Senha do administrador do jumpbox (nome do segredo do Key Vault)|Nome de usuário do administrador do jumpbox. Precisa corresponder ao valor da propriedade **Nome de usuário do administrador do jumpbox** no **Modelo do Key Vault**.|
|Modelo do jumpbox|Modelo do Resource Manager|Sistema operacional do jumpbox|Determina o sistema operacional da VM de jumpbox. O valor padrão é _Windows_.|
|Grupo de recursos do Active Directory Domain Services|Resource group|Nome|**Bloqueado** – concatena o **Nome da organização** com `-sharedsvcs-adds-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos do Active Directory Domain Services|Resource group|Location|**Bloqueado** – usa o parâmetro de blueprint.|
|Modelo do Active Directory Domain Services|Modelo do Resource Manager|Nome de usuário do administrador de domínio|Nome de usuário para o jumpbox do ADDS. Precisa corresponder ao mesmo valor da propriedade no **Modelo do Key Vault**. O valor padrão é _adds-admin-user_.|
|Modelo do Active Directory Domain Services|Modelo do Resource Manager|Senha do administrador de domínio (ID do recurso do Key Vault)|A ID do Recurso do Key Vault. Use "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" e substitua `{subscriptionId}` pela ID de assinatura e `{orgName}` pelo parâmetro de blueprint **Nome da organização**.|
|Modelo do Active Directory Domain Services|Modelo do Resource Manager|Senha do administrador de domínio (nome do segredo do Key Vault)|Nome de usuário do administrador do domínio. Precisa corresponder ao valor da propriedade **Nome de usuário do administrador do domínio** no **Modelo do Key Vault**.|
|Modelo do Active Directory Domain Services|Modelo do Resource Manager|Nome de domínio|Nome do Active Directory criado pela amostra. O valor padrão é _contoso.com_.|
|Modelo do Active Directory Domain Services|Modelo do Resource Manager|Usuário administrador de domínio|Nome de usuário para a conta do AD do administrador e para ingresso de dispositivos no domínio do AD. Precisa corresponder ao valor da propriedade **Nome de usuário do administrador do AD** no **Modelo do Key Vault**. O valor padrão é _domain-admin-user_.|
|Modelo do Active Directory Domain Services|Modelo do Resource Manager|Senha do administrador de domínio|Defina os detalhes do Key Vault para armazenar a senha. Nenhum valor padrão e não pode ser deixado em branco.|

## <a name="next-steps"></a>Próximas etapas

Agora que você examinou as etapas para implantar a amostra de blueprint dos Serviços Compartilhados ISO 27001, leia os seguintes artigos para saber mais sobre a arquitetura e o mapeamento de controle:

> [!div class="nextstepaction"]
> [Blueprint dos Serviços Compartilhados ISO 27001 – Visão geral](./index.md)
> [Blueprint dos Serviços Compartilhados ISO 27001 – Mapeamento de controle](./control-mapping.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).
