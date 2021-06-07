---
title: Visão geral do exemplo de blueprint do HIPAA HITRUST 9.2
description: Visão geral do exemplo de blueprint do HIPAA HITRUST 9.2. Esse exemplo de blueprint ajuda os clientes a avaliar controles específicos do HIPAA HITRUST 9.2.
ms.date: 04/02/2021
ms.topic: sample
ms.openlocfilehash: 168946319c11f31ee41594d82d9ff186dea232cd
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106386577"
---
# <a name="hipaa-hitrust-92-blueprint-sample"></a>Exemplo de blueprint do HIPAA HITRUST 9.2

O exemplo de blueprint do HIPAA HITRUST 9.2 oferece proteções de governança por meio do [Azure Policy](../../policy/overview.md), que ajudam você a avaliar controles específicos do HIPAA HITRUST 9.2. Esse blueprint ajuda os clientes a implantar um conjunto básico de políticas para qualquer arquitetura implantada pelo Azure que precise implementar os controles do HIPAA HITRUST 9.2.

## <a name="control-mapping"></a>Mapeamento de controle

O [mapeamento de controle do Azure Policy](../../policy/samples/hipaa-hitrust-9-2.md) fornece detalhes sobre as definições de política incluídas neste blueprint e como essas definições de política são mapeadas para **domínios de conformidade** e **controles** do HIPAA HITRUST 9.2. Quando atribuídos a uma arquitetura, os recursos são avaliados pelo Azure Policy para verificar a não conformidade com definições de política atribuídas. Para saber mais, veja [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Implantar

Para implantar o exemplo de blueprint do HIPAA HITRUST 9.2 do Azure Blueprints, as seguintes etapas precisam ser executadas:

> [!div class="checklist"]
> - Criar um blueprint com base na amostra
> - Marcar sua cópia do exemplo como **Publicado**
> - Atribuir a sua cópia do blueprint a uma assinatura existente

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

### <a name="create-blueprint-from-sample"></a>Criar um blueprint com base na amostra

Primeiro, implemente a amostra de blueprint criando um blueprint no ambiente usando a amostra como ponto de partida.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Na página **Introdução** à esquerda, selecione o botão **Criar** em _Criar um blueprint_.

1. Encontre a amostra de blueprint **HIPAA/HITRUST** em _Outras Amostras_ e selecione **Usar esta amostra**.

1. Insira as informações _Básicas_ do exemplo de blueprint:

   - **Nome do blueprint**: forneça um nome para a cópia do exemplo de blueprint do HIPAA HITRUST 9.2.
   - **Localização da definição**: Use as reticências e selecione o grupo de gerenciamento em que deseja salvar a cópia da amostra.

1. Selecione a guia _Artefatos_ na parte superior da página ou clique em **Avançar: Artefatos** na parte inferior da página.

1. Examine a lista de artefatos que compõem o exemplo de blueprint. Muitos dos artefatos têm parâmetros que definiremos mais tarde. Selecione **Salvar Rascunho** quando terminar de examinar o exemplo de blueprint.

### <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

Agora a cópia do exemplo de blueprint foi criada em seu ambiente. Ela é criada no modo **Rascunho** e deve ser **Publicada** antes de ser atribuída e implantada. A cópia do exemplo de blueprint pode ser personalizada de acordo com o seu ambiente e as suas necessidades, mas essa modificação poderá desviá-lo dos controles do HIPAA HITRUST 9.2.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a cópia da amostra de blueprint e, em seguida, selecione-a.

1. Selecione **Publicar blueprint** na parte superior da página. Na nova página à direita, informe a **Versão** da sua cópia da amostra de blueprint. Essa propriedade será útil se você fizer uma modificação mais tarde. Forneça **Notas de alterações**, como "Primeira versão publicada do exemplo de blueprint do HIPAA HITRUST 9.2". Em seguida, selecione **Publicar** na parte inferior da página.

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

|Nome do artefato |Nome do parâmetro |Descrição |
|---|---|---|
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |O acesso pelo ponto de extremidade para a Internet deve ser restrito |Habilitar ou desabilitar o monitoramento de regras de entrada excessivamente permissivas do NSG |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Contas: Status da conta de convidado |Especifica se a conta Convidado local está desabilitada. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Os controles de aplicativos adaptáveis devem ser habilitados em máquinas virtuais |Habilitar ou desabilitar o monitoramento de uma lista de permissões de aplicativos na Central de Segurança do Azure |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Permitir conexões simultâneas com a Internet ou um Domínio do Windows |Especifique se deseja impedir que os computadores se conectem a uma rede baseada em domínio e a uma rede não baseada em domínio simultaneamente. Um valor igual a 0 permite conexões simultâneas, e um valor igual a 1 as bloqueia. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |O Aplicativo de API só deve estar acessível por HTTPS V2 |Habilitar ou desabilitar o monitoramento do uso de HTTPS no Aplicativo de API V2 |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Nomes de aplicativos (permite curingas) |Uma lista separada por ponto e vírgula dos nomes dos aplicativos que devem ser instalados. por ex. 'Microsoft SQL Server 2014 (64 bits); Microsoft Visual Studio Code' ou ' Microsoft SQL Server 2014*' (para corresponder a qualquer aplicativo que comece com 'Microsoft SQL Server 2014') |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Auditoria do encerramento do processo |Especifica se os eventos de auditoria são gerados quando um processo é encerrado. Recomendado para monitorar o encerramento de processos críticos. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Auditar o acesso irrestrito à rede para contas de armazenamento |Habilitar ou desabilitar o monitoramento do acesso à rede na conta de armazenamento |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Auditoria: Desligar o sistema imediatamente se não for possível registrar auditorias de segurança |Audita se o sistema será desligado quando não for possível registrar eventos de segurança. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Impressões digitais do certificado |Uma lista separada por ponto e vírgula de impressões digitais do certificado que devem existir no repositório de certificados raiz confiáveis (Cert:\LocalMachine\Root). Por exemplo, THUMBPRINT1; THUMBPRINT2; THUMBPRINT3 |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Os logs de diagnóstico em contas do Lote devem ser habilitados |Habilitar ou desabilitar o monitoramento de logs de diagnóstico nas contas do Lote |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Os logs de diagnóstico no Hub de eventos devem ser habilitados |Habilitar ou desabilitar o monitoramento de logs de diagnóstico nas contas do Hub de Eventos |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Os logs de diagnóstico nos serviços Pesquisa devem ser habilitados |Habilitar ou desabilitar o monitoramento de logs de diagnóstico no serviço Azure Search |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Os logs de diagnóstico nos Conjuntos de Dimensionamento de Máquinas Virtuais devem ser habilitados |Habilitar ou desabilitar o monitoramento de logs de diagnóstico no Service Fabric |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |A criptografia de disco deve ser aplicada em máquinas virtuais |Habilitar ou desabilitar o monitoramento para criptografia de disco de VM |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Habilitar os logons de convidado não seguros |Especifica se o cliente SMB permitirá logons de convidado não seguros em um servidor SMB. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais |Habilitar ou desabilitar o monitoramento do acesso just-in-time à rede |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Portas de gerenciamento devem ser fechadas nas máquinas virtuais |Habilitar ou desabilitar o monitoramento de portas de gerenciamento abertas nas Máquinas Virtuais |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura |Habilitar ou desabilitar o monitoramento da MFA em contas com permissões de gravação na assinatura |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura |Habilitar ou desabilitar o monitoramento da MFA em contas com permissões de proprietário na assinatura |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Acesso à rede: Caminhos do registro acessíveis remotamente |Especifica quais caminhos do Registro poderão ser acessados na rede, independentemente dos usuários ou dos grupos indicados na ACL (lista de controle de acesso) da chave do Registro `winreg`. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Acesso à rede: caminhos e subcaminhos do Registro acessíveis remotamente |Especifica quais caminhos e subcaminhos do Registro poderão ser acessados na rede, independentemente dos usuários ou dos grupos indicados na ACL (lista de controle de acesso) da chave do Registro `winreg`. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Acesso à rede: Compartilhamentos que podem ser acessados anonimamente |Especifica quais compartilhamentos de rede podem ser acessados por usuários anônimos. A configuração padrão dessa configuração de política tem pouco efeito, porque todos os usuários precisam ser autenticados para acessarem os recursos compartilhados no servidor. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Console de recuperação: permitir cópia em disquete e acesso a todas as unidades e pastas |Especifica se o comando SET do Console de Recuperação deve ser disponibilizado, o que permite a configuração de variáveis de ambiente do console de recuperação. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |A depuração remota deve ser desligada para o aplicativo de API |Habilitar ou desabilitar o monitoramento da depuração remota no Aplicativo de API |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Depuração remota deve ser desativada para o aplicativo da Web |Habilitar ou desabilitar o monitoramento da depuração remota no Aplicativo Web |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Retenção obrigatória (em dias) de logs nas contas do Lote |O período de retenção obrigatória de logs de diagnóstico em dias |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Retenção obrigatória (em dias) de logs no serviço Azure Search |O período de retenção obrigatória de logs de diagnóstico em dias |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Retenção obrigatória (em dias) de logs nas contas do Hub de Eventos |O período de retenção obrigatória de logs de diagnóstico em dias |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |O Nome do Grupo de Recursos da Conta de Armazenamento (cuja existência é obrigatória) na qual as configurações de diagnóstico dos Grupos de Segurança de Rede serão implantadas |O nome do grupo de recursos em que a conta de armazenamento será criada. Esse grupo de recursos já precisa existir. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |O RBAC (controle de acesso baseado em função) deve ser usado nos Serviços de Kubernetes |Habilitar ou desabilitar o monitoramento de Serviços do Kubernetes sem o RBAC habilitado |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |O protetor TDE da instância gerenciada do SQL deve ser criptografado com sua própria chave |Habilite ou desabilite o monitoramento da TDE (Transparent Data Encryption) com o suporte à própria chave. A TDE com o suporte à própria chave proporciona maior transparência e controle do Protetor de TDE, maior segurança com um serviço externo com suporte a HSM e promoção de separação de tarefas. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |O protetor TDE do SQL Server deve ser criptografado com sua própria chave |Habilite ou desabilite o monitoramento da TDE (Transparent Data Encryption) com o suporte à própria chave. A TDE com o suporte à própria chave proporciona maior transparência e controle do Protetor de TDE, maior segurança com um serviço externo com suporte a HSM e promoção de separação de tarefas. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |O Prefixo da Conta de Armazenamento para a Conta de Armazenamento Regional na qual as configurações de diagnóstico dos Grupos de Segurança de Rede serão implantadas |Este prefixo será combinado com o local de grupo de segurança de rede para formar o nome da conta de armazenamento criada. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |As atualizações do sistema nos conjuntos de dimensionamento de máquinas virtuais devem ser instaladas |Habilitar ou desabilitar os relatórios de conjuntos de dimensionamento de máquinas virtuais de atualizações do sistema |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |As atualizações do sistema nos conjuntos de dimensionamento de máquinas virtuais devem ser instaladas |Habilitar ou desabilitar os relatórios de conjuntos de dimensionamento de máquinas virtuais de atualizações do sistema |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Desabilitar a resolução de nomes multicast |Especifica se o LLMNR, um protocolo de resolução de nomes secundário que faz a transmissão usando multicast por um link de sub-rede local em uma só sub-rede, está habilitado. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |As máquinas virtuais devem ser migradas para os novos recursos do Azure Resource Manager |Habilitar ou desabilitar o monitoramento de VMs de computação clássica |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas |Habilitar ou desabilitar o monitoramento de vulnerabilidades do sistema operacional de conjuntos de dimensionamento de máquinas virtuais |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades |Habilitar ou desabilitar a detecção de vulnerabilidades de VM por uma solução de avaliação de vulnerabilidade |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |A avaliação de vulnerabilidades deve ser habilitada nas instâncias gerenciadas do SQL |Audite as instâncias gerenciadas do SQL que não têm verificações recorrentes de avaliação de vulnerabilidade ativadas. A avaliação de vulnerabilidades pode descobrir, acompanhar e ajudar a corrigir possíveis vulnerabilidades do banco de dados. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Firewall do Windows (Domínio): aplicar as regras de firewall locais |Especifica se os administradores locais têm permissão para criar regras de firewall locais que são aplicadas junto com as regras de firewall configuradas pela Política de Grupo para o perfil de Domínio. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Firewall do Windows (Domínio): comportamento das conexões de saída |Especifica o comportamento das conexões de saída do perfil de Domínio que não correspondem a uma regra de firewall de saída. O valor padrão 0 indica a permissão de conexões, e um valor igual a 1 indica o bloqueio das conexões. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Firewall do Windows (Domínio): comportamento das conexões de saída |Especifica o comportamento das conexões de saída do perfil de Domínio que não correspondem a uma regra de firewall de saída. O valor padrão 0 indica a permissão de conexões, e um valor igual a 1 indica o bloqueio das conexões. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Firewall do Windows (Domínio): exibir notificações |Especifica se o Firewall do Windows com Segurança Avançada exibirá notificações para o usuário quando um programa for impedido de receber conexões de entrada no perfil de Domínio. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Firewall do Windows (Domínio): usar configurações de perfil |Especifica se o Firewall do Windows com Segurança Avançada usará as configurações do perfil do Domínio para filtrar o tráfego de rede. Se você selecionar Desativado, o Firewall do Windows com Segurança Avançada não usará nenhuma das regras de firewall nem as regras de segurança de conexão nesse perfil. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Firewall do Windows (Particular): aplicar regras de segurança de conexão local |Especifica se os administradores locais têm permissão para criar regras de segurança de conexão que são aplicadas junto com as regras de segurança de conexão configuradas pela Política de Grupo para o perfil Particular. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Firewall do Windows (Particular): aplicar as regras de firewall locais |Especifica se os administradores locais têm permissão para criar regras de firewall locais que são aplicadas junto com as regras de firewall configuradas pela Política de Grupo para o perfil Particular. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Firewall do Windows (Particular): comportamento das conexões de saída |Especifica o comportamento das conexões de saída do perfil Particular que não correspondem a uma regra de firewall de saída. O valor padrão 0 indica a permissão de conexões, e um valor igual a 1 indica o bloqueio das conexões. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Firewall do Windows (Particular): exibir notificações |Especifica se o Firewall do Windows com Segurança Avançada exibirá notificações para o usuário quando um programa for impedido de receber conexões de entrada no perfil Particular. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Firewall do Windows (Particular): usar configurações de perfil |Especifica se o Firewall do Windows com Segurança Avançada usará as configurações do perfil Particular para filtrar o tráfego de rede. Se você selecionar Desativado, o Firewall do Windows com Segurança Avançada não usará nenhuma das regras de firewall nem as regras de segurança de conexão nesse perfil. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Firewall do Windows (Público): aplicar regras de segurança de conexão local |Especifica se os administradores locais têm permissão para criar regras de segurança de conexão que são aplicadas junto com as regras de segurança de conexão configuradas pela Política de Grupo para o perfil Público. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Firewall do Windows (Público): aplicar as regras de firewall locais |Especifica se os administradores locais têm permissão para criar regras de firewall locais que são aplicadas junto com as regras de firewall configuradas pela Política de Grupo para o perfil Público. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Firewall do Windows (Público): comportamento das conexões de saída |Especifica o comportamento das conexões de saída do perfil Público que não correspondem a uma regra de firewall de saída. O valor padrão 0 indica a permissão de conexões, e um valor igual a 1 indica o bloqueio das conexões. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Firewall do Windows (Público): exibir notificações |Especifica se o Firewall do Windows com Segurança Avançada exibirá notificações para o usuário quando um programa for impedido de receber conexões de entrada no perfil Público. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Firewall do Windows (Público): usar configurações de perfil |Especifica se o Firewall do Windows com Segurança Avançada usará as configurações do perfil Público para filtrar o tráfego de rede. Se você selecionar Desativado, o Firewall do Windows com Segurança Avançada não usará nenhuma das regras de firewall nem as regras de segurança de conexão nesse perfil. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Firewall do Windows: Domínio: permitir resposta unicast |Especifica se o Firewall do Windows com Segurança Avançada permite que o computador local receba respostas unicast para as mensagens de saída de difusão ou multicast no perfil de Domínio. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Firewall do Windows: Particular: permitir resposta unicast |Especifica se o Firewall do Windows com Segurança Avançada permite que o computador local receba respostas unicast para as mensagens de saída de difusão ou multicast no perfil Particular. |
|Auditar os controles do HITRUST/HIPAA e implantar Extensões de VM específicas para dar suporte aos requisitos de auditoria |Firewall do Windows: Público: permitir resposta unicast |Especifica se o Firewall do Windows com Segurança Avançada permite que o computador local receba respostas unicast para as mensagens de saída de difusão ou multicast no perfil Público. |

## <a name="next-steps"></a>Próximas etapas

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md).
