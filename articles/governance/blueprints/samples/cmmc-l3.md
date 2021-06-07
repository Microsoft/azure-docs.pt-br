---
title: Exemplo de blueprint do CMMC Nível 3
description: Visão geral do exemplo de blueprint do CMMC Nível 3. Este exemplo de blueprint ajuda os clientes a avaliar controles específicos.
ms.date: 03/24/2021
ms.topic: sample
ms.openlocfilehash: 950c6064ce8b3d9973ac08e5895a4b6f48e37d6a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572078"
---
# <a name="cmmc-level-3-blueprint-sample"></a>Exemplo de blueprint do CMMC Nível 3

O exemplo de blueprint do CMMC Nível 3 fornece proteções de governança com uso do [Azure Policy](../../policy/overview.md) que ajudam você a avaliar controles específicos da [Estrutura de CMMC (Certificação do Modelo de Maturidade da Segurança Cibernética)](https://www.acq.osd.mil/cmmc/index.html). Esse blueprint ajuda os clientes a implantar um conjunto principal de políticas em qualquer arquitetura implantada pelo Azure que deve implementar os controles do CMMC Nível 3.

## <a name="control-mapping"></a>Mapeamento de controle

O [mapeamento de controles do Azure Policy](../../policy/samples/cmmc-l3.md) fornece detalhes sobre as definições de política incluídas neste blueprint e sobre como essas definições de política são mapeadas para os **controles** na estrutura de CMMC. Quando atribuídos a uma arquitetura, os recursos são avaliados pelo Azure Policy para verificar a não conformidade com definições de política atribuídas. Para saber mais, veja [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Implantar

Para implantar o exemplo de blueprint do CMMC Nível 3 do Azure Blueprints, as seguintes etapas precisam ser executadas:

> [!div class="checklist"]
> - Criar um blueprint com base na amostra
> - Marcar sua cópia do exemplo como **Publicado**
> - Atribuir a sua cópia do blueprint a uma assinatura existente

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

### <a name="create-blueprint-from-sample"></a>Criar um blueprint com base na amostra

Primeiro, implemente a amostra de blueprint criando um blueprint no ambiente usando a amostra como ponto de partida.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Na página **Introdução** à esquerda, selecione o botão **Criar** em _Criar um blueprint_.

1. Encontre o exemplo de blueprint **CMMC Nível 3** em _Outros Exemplos_ e selecione **Usar este exemplo**.

1. Insira as informações _Básicas_ do exemplo de blueprint:

   - **Nome do blueprint**: forneça um nome para sua cópia do exemplo de blueprint CMMC Nível 3.
   - **Localização da definição**: Use as reticências e selecione o grupo de gerenciamento em que deseja salvar a cópia da amostra.

1. Selecione a guia _Artefatos_ na parte superior da página ou clique em **Avançar: Artefatos** na parte inferior da página.

1. Examine a lista de artefatos que estão incluídos no exemplo de blueprint. Muitos dos artefatos têm parâmetros que definiremos mais tarde. Selecione **Salvar Rascunho** quando terminar de examinar o exemplo de blueprint.

### <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

Agora a cópia do exemplo de blueprint foi criada em seu ambiente. Ela é criada no modo **Rascunho** e deve ser **Publicada** antes de ser atribuída e implantada. A cópia do exemplo de blueprint pode ser personalizada de acordo com seu ambiente e suas necessidades, mas essa modificação poderá desviá-lo dos controles do CMMC Nível 3.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a cópia da amostra de blueprint e, em seguida, selecione-a.

1. Selecione **Publicar blueprint** na parte superior da página. Na nova página à direita, informe a **Versão** da sua cópia da amostra de blueprint. Essa propriedade será útil se você fizer uma modificação mais tarde. Forneça **Notas de alterações**, por exemplo, "Primeira versão publicada do exemplo de blueprint CMMC Nível 3". Em seguida, selecione **Publicar** na parte inferior da página.

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
|CMMC nível 3|Atribuição de política|Incluir servidores conectados com o Arc ao avaliar políticas de configuração de convidado|Ao selecionar 'true', você concorda em ser cobrado mensalmente por computador conectado do Arc; para obter mais informações, visite https://aka.ms/policy-pricing|
|CMMC nível 3|Atribuição de política|Lista de usuários que precisam ser excluídos do grupo de Administradores de VM do Windows|Uma lista separada por ponto e vírgula de usuários que devem ser excluídos do grupo local de Administradores, por exemplo: Administrador; myUser1; myUser2|
|CMMC nível 3|Atribuição de política|Lista de usuários que devem ser incluídos no grupo de Administradores de VM do Windows|Uma lista separada por ponto e vírgula de usuários que devem ser incluídos no grupo local de Administradores, por exemplo: Administrador; myUser1; myUser2|
|CMMC nível 3|Atribuição de política|ID do workspace do Log Analytics para relatórios do agente de VM|ID (GUID) do workspace do Log Analytics em que os agentes de VMs devem relatar|
|CMMC nível 3|Atribuição de política|Nomes de curva elíptica permitidos|A lista de nomes de curva permitidos para certificados de criptografia de curva elíptica.|
|CMMC nível 3|Atribuição de política|Tipos de chave permitidos|A lista de tipos de chave permitidos|
|CMMC nível 3|Atribuição de política|Permitir o uso da rede do host para os pods do cluster do Kubernetes|Defina esse valor como true se for permitido que o pod use a rede do host. Caso contrário, defina-o como false.|
|CMMC nível 3|Atribuição de política|Auditoria da mudança na política de autenticação|Especifica se eventos de auditoria são gerados quando são feitas alterações na política de autenticação. Essa configuração é útil para controlar alterações de confiança e de privilégios no nível do domínio e no nível da floresta concedidos a contas de usuário ou a grupos.|
|CMMC nível 3|Atribuição de política|Auditoria da mudança na política de autorização|Especifica se os eventos de auditoria são gerados para atribuição e remoção de direitos do usuário nas políticas de direito do usuário, alterações na permissão de objeto de token de segurança, alterações de atributos de recurso e alterações de Política de Acesso Central para objetos do sistema de arquivos.|
|CMMC nível 3|Atribuição de política|Efeito para a política: o Backup do Azure deve ser habilitado para as Máquinas Virtuais|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: as contas dos Serviços Cognitivos devem restringir o acesso à rede|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: as instâncias gerenciadas de SQL devem usar chaves gerenciadas pelo cliente para criptografar dados inativos|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: a API do Azure para FHIR deve usar uma CMK (chave gerenciada pelo cliente) para criptografar dados inativos|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito da política: o WAF (Firewall de Aplicativo Web) deve ser habilitado para o Azure Front Door Service|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: as configurações de Avaliação de Vulnerabilidade do servidor SQL devem conter um endereço de email para receber os relatórios de exame|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: o acesso à rede pública deve ser desabilitado nas contas dos Serviços Cognitivos|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: o CORS não deve permitir que todos os recursos tenham acesso aos seus Aplicativos de Funções|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: as recomendações da proteção de rede adaptável devem ser aplicadas nas máquinas virtuais para a Internet|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: Deve haver mais de um proprietário atribuído à sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: A criptografia de disco deve ser aplicada em máquinas virtuais|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: A notificação por email para o proprietário da assinatura para alertas de severidade alta deve ser habilitada|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: o cofre de chaves deve ter a proteção contra limpeza habilitada|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: os servidores SQL devem usar chaves gerenciadas pelo cliente para criptografar dados inativos|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: A depuração remota deve ser desativada para o aplicativos de funções|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: o Azure Defender para o Key Vault deve ser habilitado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: o backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MariaDB|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: o CORS não deve permitir que todos os domínios tenham acesso à sua API para FHIR|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: os computadores Windows devem atender aos requisitos de 'Opções de Segurança – Segurança de Rede'|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: as regras da lista de permitidos na política de controle de aplicativos adaptáveis devem ser atualizadas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: o WAF (Firewall de Aplicativo Web) deve usar o modo especificado para Gateway de Aplicativo|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: as chaves devem ter datas de validade definidas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: A Transparent Data Encryption em bancos de dados SQL deve ser habilitada|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: o perfil de log do Azure Monitor deve coletar logs para as categorias 'gravação', 'exclusão' e 'ação'|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: a avaliação de vulnerabilidades deve ser habilitada na Instância Gerenciada de SQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: garantir que a 'versão do PHP' seja a última, se usada como parte do aplicativo de API|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: o cofre de chaves deve ter a exclusão reversível habilitada|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: Um administrador do Azure Active Directory deve ser provisionado para servidores SQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito a política: somente as conexões seguras com o Cache do Azure para Redis devem ser habilitadas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: a criptografia de infraestrutura deve ser habilitada para servidores do Banco de Dados do Azure para PostgreSQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: A solução de proteção de ponto de extremidade deve ser instalada nos conjuntos de dimensionamento de máquinas virtuais|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: o Azure Defender para o Serviço de Aplicativo deve ser habilitado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: os computadores Windows devem atender aos requisitos de 'Políticas de Auditoria do Sistema – Alteração de Política'|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: as contas dos Serviços Cognitivos devem habilitar a criptografia de dados|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: o acesso SSH da Internet deve ser bloqueado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: discos desanexados devem ser criptografados|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: o Azure Defender para Armazenamento deve ser habilitado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: contas de armazenamento devem restringir o acesso à rede|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: o CORS não deve permitir que todos os recursos acessem seu Aplicativo de API|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: implantar a Proteção Avançada contra Ameaças em Contas de Armazenamento|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: as variáveis da conta de Automação devem ser criptografadas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: os logs de diagnóstico no Hub IoT devem ser habilitados|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: a criptografia de infraestrutura deve ser habilitada para servidores do Banco de Dados do Azure para MySQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: um alerta do log de atividades deve existir para operações de segurança específicas (Microsoft.Security/securitySolutions/excluir)|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: os computadores Windows devem atender aos requisitos de 'Opções de Segurança – Acesso à Rede'|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: A transferência segura para contas de armazenamento deve ser habilitada|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: o Azure Monitor deve coletar logs de atividades de todas as regiões|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: o WAF (Firewall de Aplicativo Web) deve usar o modo especificado para o Azure Front Door Service|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: as contas de armazenamento devem ter criptografia de infraestrutura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito da política: os controles de aplicativos adaptáveis para definir aplicativos seguros devem ser habilitados nos computadores|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para PostgreSQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: os computadores Windows devem atender aos requisitos de 'Opções de Segurança – Controle de Conta de Usuário'|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: Garantir que a 'versão do Java' seja a última, se usada como parte do aplicativo Web|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: o Azure Defender para servidores deve ser habilitado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: Um máximo de três proprietários deve ser designado para sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: as assinaturas devem ter um endereço de email de contato para problemas de segurança|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: o acesso público da conta de armazenamento não deve ser permitido|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito da política: a solução de avaliação de vulnerabilidades deve ser habilitada nas máquinas virtuais|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: o Azure Defender para Kubernetes deve ser habilitado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: o firewall deve ser habilitado no Key Vault|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito da política: o WAF (Firewall de Aplicativo Web) deve ser habilitado para o Gateway de Aplicativo|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: O CORS não deve permitir que todos os recursos acessem seus aplicativos Web|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: auditar os computadores Windows que permitem a reutilização das últimas 24 senhas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: os registros de contêiner devem ser criptografados com uma CMK (chave gerenciada pelo cliente)|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: As contas externas com permissões de gravação devem ser removidas de sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: o acesso à rede pública deve ser desabilitado em servidores PostgreSQL flexíveis|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: as vulnerabilidades nas imagens do Registro de Contêiner do Azure devem ser corrigidas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: As contas externas com permissões de leitura devem ser removidas de sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: a propriedade ClusterProtectionLevel dos clusters do Service Fabric deve ser definida como EncryptAndSign|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: o Azure Defender para SQL Servers deve ser habilitado nos computadores|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: as contas dos Serviços Cognitivos devem habilitar a criptografia de dados com uma chave gerenciada pelo cliente|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: As contas preteridas devem ser removidas de sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: O aplicativo de funções deve ser acessível apenas por HTTPS|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: a notificação por email de alertas de severidade alta deve ser habilitada|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: a conta de armazenamento deve usar uma chave gerenciada pelo cliente para criptografia|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: Garantir que a 'versão do Python' seja a última, se usada como parte do aplicativo Web|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: Garantir que a 'versão do Python' seja a última, se usada como parte do aplicativo de funções|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: Garantir que a 'versão do PHP' seja a última, se usada como parte do aplicativo Web|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: Garantir que a 'versão do Python' seja a última, se usada como parte do aplicativo de API|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: as chaves devem ser do tipo de criptografia especificado, RSA ou EC|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: as assinaturas do Azure devem ter um perfil de log para o Log de Atividades|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: os sistemas operacionais e os discos de dados em clusters do Serviço de Kubernetes do Azure devem ser criptografados por chaves gerenciadas pelo cliente|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: o Azure Defender para servidores do Banco de Dados SQL do Azure deve ser habilitado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: a criptografia em repouso do Azure Data Explorer deve usar uma chave gerenciada pelo cliente|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: as chaves que usam a criptografia RSA devem ter um tamanho da chave mínimo especificado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MySQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: os pods do cluster do Kubernetes só devem usar o intervalo de portas e a rede do host aprovados|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: As atualizações do sistema devem ser instaladas em suas máquinas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: os computadores Windows devem atender aos requisitos de 'Políticas de Auditoria do Sistema – Uso de Privilégios'|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: os trabalhos do Azure Stream Analytics devem usar chaves gerenciadas pelo cliente para criptografar dados|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: Garantir que a 'versão do Java' seja a última, se usada como parte do aplicativo de API|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: Garantir que a 'Versão do HTTP' seja a última, se usada para executar o aplicativo Web|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: A última versão do TLS deve ser usada no aplicativo de API|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: Garantir que a 'Versão do HTTP' seja a última, se usada para executar o aplicativo de API|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: A extensão IaaSAntimalware da Microsoft deve ser implantada em servidores do Windows|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: Garantir que a "versão do Java" seja a mais recente, se usada como parte do aplicativo de funções|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: todas as portas de rede devem ser restritas nos grupos de segurança de rede associados à sua máquina virtual|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: O tipo de preço Standard da Central de Segurança deve ser selecionado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: auditar os computadores Windows que não restringem o tamanho mínimo da senha a 14 caracteres|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: Auditar o uso de regras personalizadas do RBAC|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: Aplicativo Web deve ser acessível somente por HTTPS|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: A auditoria no SQL Server deve ser habilitada|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: O agente do Log Analytics deve ser instalado nas máquinas virtuais|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: A Segurança de Dados Avançada deve ser habilitada nos servidores SQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: a Segurança de Dados Avançada deve ser habilitada na Instância Gerenciada de SQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: o RBAC (Controle de Acesso Baseado em Função) deve ser usado nos Serviços de Kubernetes|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: as máquinas virtuais devem ter a extensão de Configuração de Convidado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: monitora o Endpoint Protection ausente na Central de Segurança do Azure|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: o log de atividades deve ser retido por pelo menos um ano|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito da política: as portas de gerenciamento de máquinas virtuais devem ser protegidas com o controle de acesso à rede Just-In-Time|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: o acesso à rede pública deve ser desabilitado para os servidores PostgreSQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: implantar a Proteção Avançada contra Ameaças em Contas do Cosmos DB|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: os logs de diagnóstico nos Serviços de Aplicativos devem ser habilitados|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: O aplicativo de API só deve estar acessível via HTTPS|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: um alerta do log de atividades deve existir para Operações administrativas específicas (Microsoft.ClassicNetwork/networkSecurityGroups/excluir)|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: um alerta do log de atividades deve existir para Operações administrativas específicas (Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/excluir)|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: um alerta do log de atividades deve existir para Operações administrativas específicas (Microsoft.Network/networkSecurityGroups/excluir)|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: um alerta do log de atividades deve existir para Operações administrativas específicas (Microsoft.Network/networkSecurityGroups/securityRules/excluir)|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: um alerta do log de atividades deve existir para Operações administrativas específicas (Microsoft.Sql/servidores/firewallRules/excluir)|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: as máquinas virtuais não para a Internet devem ser protegidas com grupos de segurança de rede|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: auditar os computadores Windows que não têm a configuração de complexidade de senha habilitada|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: o Azure Defender para Registros de contêiner deve ser habilitado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: os trabalhos do Azure Data Box devem habilitar a criptografia dupla para dados inativos no dispositivo|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: As atualizações do sistema nos conjuntos de dimensionamento de máquinas virtuais devem ser instaladas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: o Microsoft Antimalware para Azure deve ser configurado para atualizar automaticamente as assinaturas de proteção|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: um alerta do log de atividades deve existir para Operações de política específicas (Microsoft.Authorization/policyAssignments/excluir)|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: o acesso à rede pública deve ser desabilitado em servidores MySQL flexíveis|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: as contas de armazenamento devem permitir o acesso de serviços confiáveis da Microsoft|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: A depuração remota deve ser desativada para aplicativos Web|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: os certificados que usam a criptografia RSA devem ter o tamanho mínimo de chave especificado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: os registros de contêiner não devem permitir acesso irrestrito à rede|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: a imposição da conexão SSL deve ser habilitada para os servidores de banco de dados PostgreSQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: a extensão de Configuração de Convidado deve ser implantada em máquinas virtuais do Azure com identidade gerenciada atribuída ao sistema|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: O backup com redundância geográfica de longo prazo deve ser habilitado para os Bancos de Dados SQL do Azure|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: o acesso à rede pública deve ser desabilitado nos servidores MySQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: auditar os computadores Windows que não armazenam senhas usando a criptografia reversível|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: os computadores Windows devem atender aos requisitos de 'Atribuição de Direitos do Usuário'|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: Garantir que a 'Versão do HTTP' seja a última, se usada para executar o aplicativo de funções|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: O MFA deve ser habilitado em contas com permissões de leitura em sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: o acesso RDP da Internet deve ser bloqueado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: auditar os computadores Linux que não têm as permissões de arquivo de senha definidas como 0644|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: as sub-redes devem ser associadas a um Grupo de Segurança de Rede|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: a imposição da conexão SSL deve ser habilitada para os servidores de banco de dados MySQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: As vulnerabilidades nas configurações de segurança do contêiner devem ser corrigidas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: A depuração remota deve ser desligada para aplicativos de API|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: auditar os computadores Linux que permitem conexões remotas de contas sem senhas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: a criptografia dupla deve estar habilitada no Azure Data Explorer|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: A avaliação da vulnerabilidade deve ser habilitada nos servidores SQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: O agente do Log Analytics deve ser instalado nos Conjuntos de Dimensionamento de Máquinas Virtuais|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: A última versão do TLS deve ser usada no aplicativo Web|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: a criptografia de disco deve ser habilitada no Azure Data Explorer|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito da política: as máquinas virtuais para a Internet devem ser protegidas com grupos de segurança de rede|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: auditar os computadores Linux que têm contas sem senhas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: os workspaces do Azure Synapse devem usar chaves gerenciadas pelo cliente para criptografar dados inativos|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: As contas externas com permissões de proprietário devem ser removidas de sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: A última versão do TLS deve ser usada no aplicativo de funções|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: os Serviços do Kubernetes devem ser atualizados para uma versão não vulnerável do Kubernetes|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: todo o tráfego da Internet deve ser encaminhado por meio do Firewall do Azure implantado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: os computadores Linux devem atender aos requisitos da linha de base de segurança do Azure|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: o acesso à rede pública deve ser desabilitado para os servidores MariaDB|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para a política: As vulnerabilidades nos bancos de dados SQL devem ser corrigidas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Efeito para política: as chaves que usam a criptografia de curva elíptica devem ter os nomes de curva especificados|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC nível 3|Atribuição de política|Namespaces excluídos da avaliação da política: os pods do cluster do Kubernetes só devem usar o intervalo de portas e a rede do host aprovados|Lista de namespaces do Kubernetes a serem excluídos da avaliação de política.|
|CMMC nível 3|Atribuição de política|Última versão do Java para os Serviços de Aplicativos|Versão mais recente do Java com suporte para Serviços de Aplicativos|
|CMMC nível 3|Atribuição de política|Última versão do Python para Linux para os Serviços de Aplicativos|Versão mais recente do Python com suporte para Serviços de Aplicativos|
|CMMC nível 3|Atribuição de política|Opcional: lista de imagens de VM que têm o sistema operacional Linux com suporte para adicionar ao escopo ao auditar a implantação do agente do Log Analytics|Valor de exemplo: '/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage'|
|CMMC nível 3|Atribuição de política|Opcional: lista de imagens de VM que têm o sistema operacional Windows com suporte para adicionar ao escopo ao auditar a implantação do agente do Log Analytics|Valor de exemplo: '/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage'|
|CMMC nível 3|Atribuição de política|Lista de regiões em que o Observador de Rede deve ser habilitado|Faça um auditoria se o Observador de Rede não estiver habilitado para regiões.|
|CMMC nível 3|Atribuição de política|Lista de tipos de recurso que devem ter os logs de diagnóstico habilitados||
|CMMC nível 3|Atribuição de política|O valor máximo no intervalo de portas do host permitido que os pods podem usar no namespace da rede do host|O valor máximo no intervalo de portas do host permitido que os pods podem usar no namespace da rede do host.|
|CMMC nível 3|Atribuição de política|Tamanho mínimo da chave RSA para chaves|O tamanho mínimo da chave para chaves RSA.|
|CMMC nível 3|Atribuição de política|Certificados de tamanho mínimo da chave RSA|O tamanho mínimo da chave para certificados RSA.|
|CMMC nível 3|Atribuição de política|Versão mínima do TLS para servidores Web do Windows|Os Windows Web Servers com versões inferiores do TLS serão avaliados como sem conformidade|
|CMMC nível 3|Atribuição de política|O valor mínimo no intervalo de portas do host permitido que os pods podem usar no namespace da rede do host|O valor mínimo no intervalo de portas do host permitido que os pods podem usar no namespace da rede do host.|
|CMMC nível 3|Atribuição de política|Requisito de modo|Modo obrigatório para todas as políticas do WAF|
|CMMC nível 3|Atribuição de política|Requisito de modo|Modo obrigatório para todas as políticas do WAF|
|CMMC nível 3|Atribuição de política|Caminhos do host que os volumes de hostPath do pod podem usar|Os caminhos do host que os volumes de hostPath do pod podem usar. Forneça uma lista de caminhos vazia para bloquear todos os caminhos do host.|
|CMMC nível 3|Atribuição de política|Acesso à rede: Caminhos do registro acessíveis remotamente|Especifica quais caminhos do Registro poderão ser acessados na rede, independentemente dos usuários ou dos grupos indicados na ACL (lista de controle de acesso) da chave do Registro `winreg`.|
|CMMC nível 3|Atribuição de política|Acesso à rede: caminhos e subcaminhos do Registro acessíveis remotamente|Especifica quais caminhos e subcaminhos do Registro poderão ser acessados na rede, independentemente dos usuários ou dos grupos indicados na ACL (lista de controle de acesso) da chave do Registro `winreg`.|
|CMMC nível 3|Atribuição de política|Acesso à rede: Compartilhamentos que podem ser acessados anonimamente|Especifica quais compartilhamentos de rede podem ser acessados por usuários anônimos. A configuração padrão dessa configuração de política tem pouco efeito, porque todos os usuários precisam ser autenticados para acessarem os recursos compartilhados no servidor.|
|CMMC nível 3|Atribuição de política|Segurança de rede: Configurar tipos de criptografia permitidos para Kerberos|Especifica os tipos de criptografia que o Kerberos pode usar.|
|CMMC nível 3|Atribuição de política|Segurança de rede: Nível de autenticação do LAN Manager|Especifique qual protocolo de autenticação de desafio/resposta é usado para logons de rede. Essa escolha afeta o nível do protocolo de autenticação usado pelos clientes, o nível de segurança de sessão negociado e o nível de autenticação aceito pelos servidores.|
|CMMC nível 3|Atribuição de política|Segurança de rede: Requisitos de assinatura do cliente LDAP|Especifique o nível de assinatura de dados necessário em nome de clientes que emitem solicitações de Associação LDAP.|
|CMMC nível 3|Atribuição de política|Segurança de rede: Segurança mínima de sessão para clientes baseados em NTLM SSP (incluindo RPC seguro)|Especifica quais comportamentos são permitidos por clientes para aplicativos que usam o SSP (provedor de suporte de segurança) NTLM. A SSPI (Interface do SSP) é usada por aplicativos que precisam de serviços de autenticação. Confira [https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-minimum-session-security-for-ntlm-ssp-based-including-secure-rpc-servers](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-minimum-session-security-for-ntlm-ssp-based-including-secure-rpc-servers) para obter mais informações.|
|CMMC nível 3|Atribuição de política|Segurança de rede: Segurança mínima de sessão para servidores baseados em NTLM SSP (incluindo RPC seguro)|Especifica quais comportamentos são permitidos por servidores para aplicativos que usam o SSP (provedor de suporte de segurança) NTLM. A SSPI (Interface do SSP) é usada por aplicativos que precisam de serviços de autenticação.|
|CMMC nível 3|Atribuição de política|Última versão do PHP para os Serviços de Aplicativos|Versão mais recente do PHP com suporte para Serviços de Aplicativos|
|CMMC nível 3|Atribuição de política|Período de retenção necessário (dias) para os logs de diagnóstico do Hub IoT||
|CMMC nível 3|Atribuição de política|Nome do grupo de recursos do Observador de Rede|Nome do grupo de recursos do NetworkWatcher, como NetworkWatcherRG. Esse é o grupo de recursos no qual os Observadores de Rede estão localizados.|
|CMMC nível 3|Atribuição de política|Configuração de auditoria necessária para os SQL Servers||
|CMMC nível 3|Atribuição de política|SKUs do Azure Data Box que dão suporte à criptografia dupla baseada em software|A lista de SKUs do Azure Data Box que dão suporte à criptografia dupla baseada em software|
|CMMC nível 3|Atribuição de política|UAC: Modo de Aprovação de Administrador para a Conta de administrador Interno|Especifica o comportamento do Modo de Aprovação de Administrador para a Conta de administrador interno.|
|CMMC nível 3|Atribuição de política|UAC: comportamento da solicitação de elevação para administradores no Modo de Aprovação de Administrador|Especifica o comportamento da solicitação de elevação para administradores.|
|CMMC nível 3|Atribuição de política|UAC: detectar instalações de aplicativos e solicitar elevação|Especifica o comportamento da detecção de instalação de aplicativo do computador.|
|CMMC nível 3|Atribuição de política|UAC: executar todos os administradores no Modo de Aprovação de Administrador|Especifica o comportamento de todas as configurações de política do UAC (Controle de Conta de Usuário) do computador.|
|CMMC nível 3|Atribuição de política|Usuários e grupos que podem forçar o desligamento por meio de um sistema remoto|Especifica quais usuários e grupos têm permissão para desligar o computador de um local remoto na rede.|
|CMMC nível 3|Atribuição de política|Usuários e grupos com acesso a este computador negado pela rede|Especifica quais usuários ou grupos estão explicitamente proibidos de se conectar com o computador por meio da rede.|
|CMMC nível 3|Atribuição de política|Usuários e grupos com logon local negado|Especifica quais usuários e grupos explicitamente não têm permissão para fazer logon no computador.|
|CMMC nível 3|Atribuição de política|Usuários e grupos para os quais o logon como um trabalho em lotes é negado|Especifica quais usuários e grupos explicitamente não têm permissão para fazer logon no computador como um trabalho em lotes (ou seja, tarefa agendada).|
|CMMC nível 3|Atribuição de política|Usuários e grupos para os quais o logon como um serviço é negado|Especifica quais contas de serviço explicitamente não têm permissão para registrar um processo como um serviço.|
|CMMC nível 3|Atribuição de política|Usuários e grupos para os quais o logon por meio de Serviços de Área de Trabalho Remota é negado|Especifica quais usuários e grupos explicitamente não têm permissão para fazer logon no computador por meio de Serviços de Terminal/Cliente da Área de Trabalho Remota.|
|CMMC nível 3|Atribuição de política|Usuários e grupos que podem restaurar arquivos e diretórios|Especifica quais usuários e grupos têm permissão para ignorar arquivos, diretórios, Registro e outras permissões de objeto persistentes ao restaurar o backup de arquivos e diretórios.|
|CMMC nível 3|Atribuição de política|Usuários e grupos que podem desligar o sistema|Especifica quais usuários e grupos conectados localmente aos computadores no seu ambiente têm permissão para desligar o sistema operacional com o comando Desligar.|
|CMMC nível 3|Atribuição de política|Usuários ou grupos que podem fazer logon localmente|Especifica quais usuários remotos na rede têm permissão para se conectar ao computador. Não inclui a Conexão de Área de Trabalho Remota.|
|CMMC nível 3|Atribuição de política|Usuários ou grupos que podem fazer backup de arquivos e diretórios|Especifica usuários e grupos com permissão para desviar das permissões de arquivo e de diretório para fazer backup do sistema.|
|CMMC nível 3|Atribuição de política|Usuários ou grupos que podem alterar a hora do sistema|Especifica quais usuários e grupos têm permissão para alterar a data e a hora no relógio interno do computador.|
|CMMC nível 3|Atribuição de política|Usuários ou grupos que podem alterar o fuso horário|Especifica quais usuários e grupos têm permissão para alterar o fuso horário do computador.|
|CMMC nível 3|Atribuição de política|Usuários ou grupos que podem criar um objeto de token|Especifica quais usuários e grupos têm permissão para criar um token de acesso, o que pode fornecer direitos elevados para acessar dados confidenciais.|
|CMMC nível 3|Atribuição de política|Usuários ou grupos que podem fazer logon localmente|Especifica quais usuários ou grupos podem fazer logon interativamente no computador. Os usuários que tentam fazer logon via Conexão de Área de Trabalho Remota ou IIS também requerem esse direito de usuário.|
|CMMC nível 3|Atribuição de política|Usuários da Área de Trabalho Remota|Usuários ou grupos que podem fazer logon pelos Serviços de Área de Trabalho Remota|
|CMMC nível 3|Atribuição de política|Usuários ou grupos que podem gerenciar a auditoria e o log de segurança|Especifica usuários e grupos com permissão para alterar as opções de auditoria de arquivos e diretórios e para limpar o Log de segurança.|
|CMMC nível 3|Atribuição de política|Usuários ou grupos que podem assumir a propriedade de arquivos ou outros objetos|Especifica quais usuários e grupos têm permissão para assumir a propriedade de arquivos, pastas, chaves do registro, processos ou threads. O direito do usuário ignora todas as permissões que estão em vigor para proteger objetos para dar propriedade ao usuário especificado.|

## <a name="next-steps"></a>Próximas etapas

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md).