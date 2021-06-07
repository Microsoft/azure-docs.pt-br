---
title: Implantar a amostra de blueprint de carga de trabalho do ASE/SQL do ISO 27001
description: Etapas de implantação da amostra de blueprint de carga de trabalho do Ambiente do Serviço de Aplicativo/Banco de Dados SQL da ISO 27001, incluindo detalhes do parâmetro do artefato de blueprint.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: 5c329a9d7175772e80ea6d9d8da3baf85ce0d170
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669637"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Implantação da amostra de blueprint de carga de trabalho do Ambiente do Serviço de Aplicativo/Banco de Dados SQL do ISO 27001

Para implantar a amostra de blueprint de carga de trabalho do Banco de Dados SQL/Ambiente do Serviço de Aplicativo do Azure Blueprints ISO 27001, execute as etapas a seguir:

> [!div class="checklist"]
> - Implantar a amostra de blueprint dos [Serviços Compartilhados ISO 27001](../iso27001-shared/index.md)
> - Criar um blueprint com base na amostra
> - Marcar sua cópia do exemplo como **Publicado**
> - Atribuir a sua cópia do blueprint a uma assinatura existente

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Implantar a amostra de blueprint dos Serviços Compartilhados ISO 27001

Antes que esta amostra de blueprint possa ser implantada, a amostra de blueprint do [Serviços Compartilhados ISO 27001](../iso27001-shared/index.md) deve ser implantada para a assinatura de destino. Sem uma implantação bem-sucedida da amostra de blueprint de Serviços Compartilhados ISO 27001, esta amostra de blueprint estará sem dependências de infraestrutura e falhará durante a implantação.

> [!IMPORTANT]
> Esta amostra de blueprint deve ser atribuída na mesma assinatura que a amostra de blueprint de [Serviços Compartilhados ISO 27001](../iso27001-shared/index.md).

## <a name="create-blueprint-from-sample"></a>Criar um blueprint com base na amostra

Primeiro, implemente a amostra de blueprint criando um blueprint no ambiente usando a amostra como ponto de partida.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Na página **Introdução** à esquerda, selecione o botão **Criar** em _Criar um blueprint_.

1. Localize a amostra de blueprint **ISO 27001: Carga de Trabalho do SQL/ASE** em _Outras Amostras_ e selecione **Usar este exemplo**.

1. Insira as informações _Básicas_ do exemplo de blueprint:

   - **Nome do blueprint**: Forneça um nome para a sua cópia da amostra de blueprint de carga de trabalho do ASE/SQL ISO 27001.
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
     - **ID de Assinatura do Serviço Compartilhado**: ID da assinatura em que a amostra de blueprint de [Serviços Compartilhados ISO 27001](../iso27001-shared/index.md) é atribuída.
     - **Prefixo de endereço de sub-rede padrão**: A notação CIDR para a sub-rede da rede virtual padrão.
       O valor padrão é _10.1.0.0/16_.
     - **Local da carga de trabalho**: Determina em que local os artefatos são implantados. Nem todos os serviços estão disponíveis em todas as localizações. Os artefatos que implantam esses serviços fornecem uma opção de parâmetro para a localização na qual esse artefato deverá ser implantado.

   - Parâmetros do artefato

     Os parâmetros definidos nesta seção se aplicam ao artefato sob o qual ele está definido. Esses são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters), pois são definidos durante a atribuição do blueprint. Para obter uma lista completa ou parâmetros de artefato e suas descrições, confira a [Tabela de parâmetros de artefato](#artifact-parameters-table).

1. Depois que todos os parâmetros forem inseridos, selecione **Atribuir** na parte inferior da página. A atribuição de blueprint é criada, e a implantação de artefato é iniciada. A implantação leva aproximadamente uma hora. Para verificar o status da implantação, abra a atribuição de blueprint.

> [!WARNING]
> As amostras internas de blueprint e o serviço Azure Blueprints são **gratuitos**. Os recursos do Azure são [precificados por produto](https://azure.microsoft.com/pricing/). Use a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/) para estimar o custo da execução de recursos implantados por essa amostra de blueprint.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefato

A seguinte tabela fornece uma lista dos parâmetros de artefato de blueprint:

|Nome do artefato|Tipo de artefato|Nome do parâmetro|Descrição|
|-|-|-|-|
|Grupo de recursos do Log Analytics|Resource group|Nome|**Bloqueado** – concatena o **Nome da organização** com `-workload-log-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos do Log Analytics|Resource group|Location|**Bloqueado** – usa o parâmetro de blueprint.|
|Modelo do Log Analytics|Modelo do Resource Manager|Camada de serviço|Define a camada do workspace do Log Analytics. O valor padrão é _PerNode_.|
|Modelo do Log Analytics|Modelo do Resource Manager|Retenção de log em dias|Retenção de dados em dias. O valor padrão é _365_.|
|Modelo do Log Analytics|Modelo do Resource Manager|Location|Região usada para criar o workspace do Log Analytics. O valor padrão é _Oeste dos EUA 2_.|
|Grupo de recursos de rede|Resource group|Nome|**Bloqueado** – concatena o **Nome da organização** com `-workload-net-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos de rede|Resource group|Location|**Bloqueado** – usa o parâmetro de blueprint.|
|Modelo do Grupo de Segurança de Rede|Modelo do Resource Manager|Retenção de log em dias|Retenção de dados em dias. O valor padrão é _365_.|
|Modelo de Rede Virtual e a Tabela de Rotas|Modelo do Resource Manager|IP privado do firewall do Azure|Configura o IP privado do [Firewall do Azure](../../../../firewall/overview.md). Deve fazer parte da notação CIDR definida no parâmetro de artefato _ISO 27001: Serviços Compartilhados_, **prefixo de endereço de sub-rede do Firewall do Azure**. O valor padrão é _10.0.4.4_.|
|Modelo de Rede Virtual e a Tabela de Rotas|Modelo do Resource Manager|ID da Assinatura de serviços compartilhados|Valor usado para habilitar o emparelhamento VNet entre uma Carga de Trabalho e Serviços Compartilhados.|
|Modelo de Rede Virtual e a Tabela de Rotas|Modelo do Resource Manager|Prefixo de endereço da Rede Virtual|A notação CIDR da rede virtual. O valor padrão é _10.1.0.0/16_.|
|Modelo de Rede Virtual e a Tabela de Rotas|Modelo do Resource Manager|Prefixo de endereço de sub-rede padrão|A notação CIDR para a sub-rede da rede virtual padrão. O valor padrão é _10.1.0.0/16_.|
|Modelo de Rede Virtual e a Tabela de Rotas|Modelo do Resource Manager|Endereço IP do ADDS|Endereço IP da primeira VM do ADDS. Esse valor é usado como o DNS da VNET personalizado.|
|Grupo de recursos do Cofre de Chaves|Resource group|Nome|**Bloqueado** – concatena o **Nome da organização** com `-workload-kv-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos do Cofre de Chaves|Resource group|Location|**Bloqueado** – usa o parâmetro de blueprint.|
|Modelo do Key Vault|Modelo do Resource Manager|ID de objeto do AAD|O identificador de objeto do AAD da conta que exige acesso à instância do Key Vault. Nenhum valor padrão e não pode ser deixado em branco. Para localizar esse valor no portal do Azure, pesquise e selecione "Usuários" em _Serviços_. Use a caixa _Nome_ para filtrar o nome da conta e selecione essa conta. Na página _Perfil do usuário_, selecione o ícone "Clique para copiar" ao lado da _ID de Objeto_.|
|Modelo do Key Vault|Modelo do Resource Manager|Retenção de log em dias|Retenção de dados em dias. O valor padrão é _365_.|
|Modelo do Key Vault|Modelo do Resource Manager|SKU do Key Vault|Especifica o SKU do Key Vault criado. O valor padrão é _Premium_.|
|Modelo do Key Vault|Modelo do Resource Manager|Nome de usuário de administrador do SQL Server do Azure|O nome de usuário usado para acessar o SQL Server do Azure. Deve corresponder ao mesmo valor da propriedade em **modelo de Banco de Dados SQL do Azure**. O valor padrão é _sql-admin-user_.|
|Grupo de recursos do Banco de Dados SQL do Azure|Resource group|Nome|**Bloqueado** – concatena o **Nome da organização** com `-workload-azsql-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos do Banco de Dados SQL do Azure|Resource group|Location|**Bloqueado** – usa o parâmetro de blueprint.|
|Modelo de Banco de Dados SQL do Azure|Modelo do Resource Manager|Nome de usuário de administrador do SQL Server do Azure|Nome de usuário para o SQL Server do Azure. Precisa corresponder ao mesmo valor da propriedade no **Modelo do Key Vault**. O valor padrão é _sql-admin-user_.|
|Modelo de Banco de Dados SQL do Azure|Modelo do Resource Manager|Senha do administrador do SQL Server do Azure (ID de Recurso do Key Vault)|A ID do Recurso do Key Vault. Use "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-workload-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv" e substitua `{subscriptionId}` pela sua ID de Assinatura e `{orgName}` pelo parâmetro de blueprint do **Nome da organização**.|
|Modelo de Banco de Dados SQL do Azure|Modelo do Resource Manager|Senha do administrador do SQL Server do Azure (Nome do Segredo do Key Vault)|Nome de usuário do administrador do SQL Server. Deve corresponder ao valor no **modelo de Key Vault**, propriedade **nome de usuário de administrador do SQL Server do Azure**.|
|Modelo de Banco de Dados SQL do Azure|Modelo do Resource Manager|Retenção de log em dias|Retenção de dados em dias. O valor padrão é _365_.|
|Modelo de Banco de Dados SQL do Azure|Modelo do Resource Manager|ID de objeto de administrador do AAD|ID de objeto do AAD do usuário que será atribuído como um administrador do Active Directory. Nenhum valor padrão e não pode ser deixado em branco. Para localizar esse valor no portal do Azure, pesquise e selecione "Usuários" em _Serviços_. Use a caixa _Nome_ para filtrar o nome da conta e selecione essa conta. Na página _Perfil do usuário_, selecione o ícone "Clique para copiar" ao lado da _ID de Objeto_.|
|Modelo de Banco de Dados SQL do Azure|Modelo do Resource Manager|Logon de administrador do AAD|No momento, contas da Microsoft (como live.com ou outlook.com) não podem ser definidas como administrador. Somente usuários e grupos de segurança em sua organização podem ser definidos como administrador. Nenhum valor padrão e não pode ser deixado em branco. Para localizar esse valor no portal do Azure, pesquise e selecione "Usuários" em _Serviços_. Use a caixa _Nome_ para filtrar o nome da conta e selecione essa conta. Na página _Perfil do usuário_, copie o _Nome de usuário_.|
|Grupo de recursos do Ambiente do Serviço de Aplicativo|Resource group|Nome|**Bloqueado** – concatena o **Nome da organização** com `-workload-ase-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos do Ambiente do Serviço de Aplicativo|Resource group|Location|**Bloqueado** – usa o parâmetro de blueprint.|
|Modelo de Ambiente do Serviço de Aplicativo|Modelo do Resource Manager|Nome de domínio|Nome do Active Directory criado pela amostra. O valor padrão é _contoso.com_.|
|Modelo de Ambiente do Serviço de Aplicativo|Modelo do Resource Manager|Local do ASE|Local do Ambiente do Serviço de Aplicativo. O valor padrão é _Oeste dos EUA 2_.|
|Modelo de Ambiente do Serviço de Aplicativo|Modelo do Resource Manager|Retenção de log do Gateway de Aplicativo em dias|Retenção de dados em dias. O valor padrão é _365_.|

## <a name="next-steps"></a>Próximas etapas

Agora que você examinou as etapas para implantar a amostra de blueprint de carga de trabalho do Banco de Dados SQL/Ambiente do Serviço de Aplicativo ISO 27001, leia os seguintes artigos para saber mais sobre a arquitetura e o mapeamento de controle:

> [!div class="nextstepaction"]
> [Blueprint da carga de trabalho do Ambiente do Serviço de Aplicativo/Banco de Dados SQL ISO 27001 – visão geral](./index.md)
> [Blueprint da carga de trabalho do Ambiente do Serviço de Aplicativo/Banco de Dados SQL ISO 27001 – mapeamento de controle](./control-mapping.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).
