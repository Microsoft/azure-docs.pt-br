---
title: Amostra de blueprint de ISM Restrito da Nova Zelândia
description: Visão geral da amostra de blueprint de ISM Restrito da Nova Zelândia. Este exemplo de blueprint ajuda os clientes a avaliar controles específicos.
ms.date: 03/22/2021
ms.topic: sample
ms.openlocfilehash: a52470ea45e6358007dc49122599e87091fbf8f7
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802974"
---
# <a name="new-zealand-ism-restricted-blueprint-sample"></a>Amostra de blueprint de ISM Restrito da Nova Zelândia

A amostra de blueprint de ISM Restrito da Nova Zelândia fornece proteções de governança com o uso do [Azure Policy](../../policy/overview.md) que ajudam a avaliar controles específicos do [Manual de Segurança da Informação da Nova Zelândia](https://www.nzism.gcsb.govt.nz/). Esse blueprint ajuda os clientes a implantar um conjunto básico de políticas para qualquer arquitetura implantada pelo Azure que precise implementar controles do ISM Restrito na Nova Zelândia.

## <a name="control-mapping"></a>Mapeamento de controle

O [mapeamento de controles do Azure Policy](../../policy/samples/new-zealand-ism.md) fornece detalhes sobre as definições de política incluídas neste blueprint e como essas definições de política são mapeadas para os **controles** no Manual de Segurança da Informação da Nova Zelândia. Quando atribuídos a uma arquitetura, os recursos são avaliados pelo Azure Policy para verificar a não conformidade com definições de política atribuídas. Para saber mais, veja [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Implantar

Para implantar a amostra de blueprint ISM Restrito da Nova Zelândia do Azure Blueprints, as seguintes etapas precisam ser executadas:

> [!div class="checklist"]
> - Criar um blueprint com base na amostra
> - Marcar sua cópia do exemplo como **Publicado**
> - Atribuir a sua cópia do blueprint a uma assinatura existente

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

### <a name="create-blueprint-from-sample"></a>Criar um blueprint com base na amostra

Primeiro, implemente a amostra de blueprint criando um blueprint no ambiente usando a amostra como ponto de partida.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Na página **Introdução** à esquerda, selecione o botão **Criar** em _Criar um blueprint_.

1. Encontre a amostra de blueprint **ISM Restrito da Nova Zelândia** em _Outras Amostras_ e selecione **Usar esta amostra**.

1. Insira as informações _Básicas_ do exemplo de blueprint:

   - **Nome do blueprint**: forneça um nome para sua cópia da amostra de blueprint ISM Restrito da Nova Zelândia.
   - **Localização da definição**: Use as reticências e selecione o grupo de gerenciamento em que deseja salvar a cópia da amostra.

1. Selecione a guia _Artefatos_ na parte superior da página ou clique em **Avançar: Artefatos** na parte inferior da página.

1. Examine a lista de artefatos que estão incluídos no exemplo de blueprint. Muitos dos artefatos têm parâmetros que definiremos mais tarde. Selecione **Salvar Rascunho** quando terminar de examinar o exemplo de blueprint.

### <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

Agora a cópia do exemplo de blueprint foi criada em seu ambiente. Ela é criada no modo **Rascunho** e deve ser **Publicada** antes de ser atribuída e implantada. A cópia da amostra de blueprint pode ser personalizada de acordo com seu ambiente e suas necessidades, mas essa modificação poderá desviá-la do alinhamento com os controles de ISM Restrito da Nova Zelândia.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a cópia da amostra de blueprint e, em seguida, selecione-a.

1. Selecione **Publicar blueprint** na parte superior da página. Na nova página à direita, informe a **Versão** da sua cópia da amostra de blueprint. Essa propriedade será útil se você fizer uma modificação mais tarde. Forneça **Notas de alterações**, como "Primeira versão publicada da amostra de blueprint ISM Restrito da Nova Zelândia". Em seguida, selecione **Publicar** na parte inferior da página.

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
|ISM restrito da Nova Zelândia|Atribuição de política|Lista de usuários que devem ser incluídos no grupo de Administradores de VM do Windows|Uma lista separada por ponto e vírgula de usuários que devem ser incluídos no grupo local de Administradores, por exemplo: Administrador; myUser1; myUser2|
|ISM restrito da Nova Zelândia|Atribuição de política|Lista de usuários que precisam ser excluídos do grupo de Administradores de VM do Windows|Uma lista separada por ponto e vírgula de usuários que devem ser excluídos do grupo local de Administradores, por exemplo: Administrador; myUser1; myUser2|
|ISM restrito da Nova Zelândia|Atribuição de política|Lista de usuários que ser os únicos incluídos no grupo de Administradores de VM do Windows|Uma lista com itens separados por ponto e vírgula de todos os membros esperados do grupo local de Administradores. Por exemplo: Administrador; myUser1; myUser2|
|ISM restrito da Nova Zelândia|Atribuição de política|ID do workspace do Log Analytics para relatórios do agente de VM|ID (GUID) do workspace do Log Analytics em que os agentes de VMs devem relatar|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito da política: o WAF (Firewall de Aplicativo Web) deve ser habilitado para o Azure Front Door Service|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: as configurações de Avaliação de Vulnerabilidade do servidor SQL devem conter um endereço de email para receber os relatórios de exame|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para política: as recomendações da proteção de rede adaptável devem ser aplicadas nas máquinas virtuais para a Internet|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: Deve haver mais de um proprietário atribuído à sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: A criptografia de disco deve ser aplicada em máquinas virtuais|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: A depuração remota deve ser desativada para o aplicativos de funções|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para política: o WAF (Firewall de Aplicativo Web) deve usar o modo especificado para Gateway de Aplicativo|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Requisito de modo do WAF para o Gateway de Aplicativo|O modo de Prevenção ou de Detecção precisa estar habilitado no serviço Gateway de Aplicativo|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: A Transparent Data Encryption em bancos de dados SQL deve ser habilitada|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: a avaliação de vulnerabilidades deve ser habilitada na Instância Gerenciada de SQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Opcional: lista de imagens de VM personalizadas que deram suporte ao sistema operacional Windows para adicionar ao escopo das imagens na galeria para política: Implantar – Configurar o agente de dependência para ser habilitado em máquinas virtuais do Windows|Para obter mais informações sobre a Configuração de Convidado, acesse [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: Um administrador do Azure Active Directory deve ser provisionado para servidores SQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito a política: somente as conexões seguras com o Cache do Azure para Redis devem ser habilitadas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: A solução de proteção de ponto de extremidade deve ser instalada nos conjuntos de dimensionamento de máquinas virtuais|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Incluir os servidores conectados ao Arc ao avaliar a política: auditar os computadores Windows sem nenhum membro especificado no grupo Administradores|Ao selecionar 'true,' você concorda em ser cobrado mensalmente por computador conectado ao Arc|
|ISM restrito da Nova Zelândia|Atribuição de política|Opcional: lista de imagens de VM personalizadas com suporte do SO Windows para adicionar ao escopo adicional às imagens na galeria para política: [Versão Prévia]: o agente de Log Analytics deve ser habilitado para imagens de máquina virtual listadas|Para obter mais informações sobre a Configuração de Convidado, acesse [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|ISM restrito da Nova Zelândia|Atribuição de política|Opcional: lista de imagens de VM personalizadas com suporte do SO Linux para adicionar ao escopo adicional às imagens na galeria para política: [Versão Prévia]: o agente de Log Analytics deve ser habilitado para imagens de máquina virtual listadas|Para obter mais informações sobre a Configuração de Convidado, acesse [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: contas de armazenamento devem restringir o acesso à rede|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Opcional: lista de imagens de VM personalizadas que deram suporte ao sistema operacional Windows para adicionar ao escopo das imagens na galeria para política: Implantar – Configurar o agente de dependência para ser habilitado em conjuntos de dimensionamento de máquinas virtuais do Windows|Para obter mais informações sobre a Configuração de Convidado, acesse [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Incluir os servidores conectados ao Arc ao avaliar a política: auditar os computadores Windows com contas extras no grupo Administradores|Ao selecionar 'true,' você concorda em ser cobrado mensalmente por computador conectado ao Arc|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: A transferência segura para contas de armazenamento deve ser habilitada|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para política: o WAF (Firewall de Aplicativo Web) deve usar o modo especificado para o Azure Front Door Service|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Requisito de modo do WAF para o Azure Front Door Service|O modo de Prevenção ou de Detecção precisa estar habilitado no Azure Front Door Service|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito da política: os controles de aplicativos adaptáveis para definir aplicativos seguros devem ser habilitados nos computadores|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: Um máximo de três proprietários deve ser designado para sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito da política: [Versão prévia]: O acesso público da conta de armazenamento não deve ser permitido|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito da política: a solução de avaliação de vulnerabilidades deve ser habilitada nas máquinas virtuais|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito da política: o WAF (Firewall de Aplicativo Web) deve ser habilitado para o Gateway de Aplicativo|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: O CORS não deve permitir que todos os recursos acessem seus aplicativos Web|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Incluir os servidores conectados ao Arc ao avaliar a política: auditar os Windows Web Servers que não estão usando protocolos de comunicação segura|Ao selecionar 'true,' você concorda em ser cobrado mensalmente por computador conectado ao Arc|
|ISM restrito da Nova Zelândia|Atribuição de política|Versão mínima do TLS para servidores Web do Windows|Os Windows Web Servers com versões inferiores do TLS serão avaliados como sem conformidade|
|ISM restrito da Nova Zelândia|Atribuição de política|Opcional: lista de imagens de VM personalizadas com suporte do SO Linux para adicionar ao escopo adicional às imagens na galeria para política: o agente do Log Analytics deve ser habilitado em conjuntos de dimensionamento de máquinas virtuais para as imagens de máquina virtual listadas|Para obter mais informações sobre a Configuração de Convidado, acesse [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|ISM restrito da Nova Zelândia|Atribuição de política|Opcional: lista de imagens de VM personalizadas com suporte do SO Windows para adicionar ao escopo adicional às imagens na galeria para política: o agente do Log Analytics deve ser habilitado em conjuntos de dimensionamento de máquinas virtuais para as imagens de máquina virtual listadas|Para obter mais informações sobre a Configuração de Convidado, acesse [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: As contas externas com permissões de gravação devem ser removidas de sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Incluir os servidores conectados ao Arc ao avaliar a política: auditar os computadores Windows com os membros especificados no grupo Administradores|Ao selecionar 'true,' você concorda em ser cobrado mensalmente por computador conectado ao Arc|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: As contas preteridas devem ser removidas de sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: O aplicativo de funções deve ser acessível apenas por HTTPS|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para política: as assinaturas do Azure devem ter um perfil de log para o Log de Atividades|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Lista de tipos de recurso que devem ter os logs de diagnóstico habilitados||
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: As atualizações do sistema devem ser instaladas em suas máquinas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: A última versão do TLS deve ser usada no aplicativo de API|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: A extensão IaaSAntimalware da Microsoft deve ser implantada em servidores do Windows|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: Aplicativo Web deve ser acessível somente por HTTPS|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito da política: a Proteção contra DDoS do Azure Standard deve ser habilitada|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: A Segurança de Dados Avançada deve ser habilitada nos servidores SQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: a Segurança de Dados Avançada deve ser habilitada na Instância Gerenciada de SQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: monitora o Endpoint Protection ausente na Central de Segurança do Azure|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para política: o log de atividades deve ser retido por pelo menos um ano|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito da política: as portas de gerenciamento de máquinas virtuais devem ser protegidas com o controle de acesso à rede Just-In-Time|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: Os clusters do Service Fabric só devem usar o Azure Active Directory para autenticação de cliente|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: O aplicativo de API só deve estar acessível via HTTPS|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito da política: auditar os computadores Windows nos quais o Microsoft Defender Exploit Guard não está habilitado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Incluir os servidores conectados ao Arc ao avaliar a política: auditar os computadores Windows nos quais o Microsoft Defender Exploit Guard não está habilitado|Ao selecionar 'true,' você concorda em ser cobrado mensalmente por computador conectado ao Arc|
|ISM restrito da Nova Zelândia|Atribuição de política|Estado de conformidade a ser relatado para os computadores Windows nos quais o Microsoft Defender Exploit Guard não está disponível|O Microsoft Defender Exploit Guard está disponível somente começando no Windows 10/Windows Server com a atualização 1709. Definir esse valor como 'Sem conformidade' mostra os computadores com versões mais antigas nos quais o Microsoft Defender Exploit Guard não está disponível (como o Windows Server 2012 R2) como sem conformidade. Definir esse valor como 'Conformidade' mostra esses computadores como em conformidade.|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: As atualizações do sistema nos conjuntos de dimensionamento de máquinas virtuais devem ser instaladas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: A depuração remota deve ser desativada para aplicativos Web|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: O MFA deve ser habilitado em contas com permissões de leitura em sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: As vulnerabilidades nas configurações de segurança do contêiner devem ser corrigidas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: A depuração remota deve ser desligada para aplicativos de API|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para política: auditar os computadores Linux que permitem conexões remotas de contas sem senhas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Incluir os servidores conectados ao Arc ao avaliar a política: auditar os computadores Linux que permitem conexões remotas de contas sem senhas|Ao selecionar 'true,' você concorda em ser cobrado mensalmente por computador conectado ao Arc|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: A avaliação da vulnerabilidade deve ser habilitada nos servidores SQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: A última versão do TLS deve ser usada no aplicativo Web|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito da política: Os computadores Windows devem atender aos requisitos de 'Opções de Segurança – Políticas de Conta'|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Impor o histórico de senhas para as contas locais de VM do Windows|Especifica os limites de reutilização de senha, ou seja, quantas vezes uma senha deve ser criada para uma conta de usuário antes que a senha possa ser repetida.|
|ISM restrito da Nova Zelândia|Atribuição de política|Incluir os servidores conectados ao Arc ao avaliar a política: Os computadores Windows devem atender aos requisitos de 'Configurações de Segurança – Políticas de Conta'|Ao selecionar 'true,' você concorda em ser cobrado mensalmente por computador conectado ao Arc|
|ISM restrito da Nova Zelândia|Atribuição de política|Duração máxima da senha para contas locais de VM do Windows|Especifica o número máximo de dias para que uma senha de conta de usuário seja alterada; o formato do valor consiste em dois inteiros separados por vírgula, denotando um intervalo inclusivo|
|ISM restrito da Nova Zelândia|Atribuição de política|Duração mínima da senha para contas locais de VM do Windows|Especifica o número mínimo de dias que podem decorrer antes que uma senha de conta de usuário possa ser alterada.|
|ISM restrito da Nova Zelândia|Atribuição de política|Tamanho mínimo da senha para contas locais de VM do Windows|Especifica o número mínimo de caracteres que uma senha de conta de usuário pode conter.|
|ISM restrito da Nova Zelândia|Atribuição de política|A senha precisa atender aos requisitos de complexidade das contas locais de VM do Windows|Especifica se uma senha de conta de usuário precisa ser complexa; caso isso seja necessário, uma senha complexa não deve conter parte do nome da conta nem o nome completo do usuário, precisa ter pelo menos seis caracteres e conter uma combinação de caracteres maiúsculos, minúsculos, numéricos e não alfabéticos.|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito da política: as máquinas virtuais para a Internet devem ser protegidas com grupos de segurança de rede|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para política: auditar os computadores Linux que têm contas sem senhas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Incluir os servidores conectados ao Arc ao avaliar a política: auditar os computadores Linux que têm contas sem senhas|Ao selecionar 'true,' você concorda em ser cobrado mensalmente por computador conectado ao Arc|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: As contas externas com permissões de proprietário devem ser removidas de sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: A última versão do TLS deve ser usada no aplicativo de funções|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito da política: [Versão prévia]: Todo o tráfego da Internet deve ser encaminhado por meio do Firewall do Azure implantado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|ISM restrito da Nova Zelândia|Atribuição de política|Efeito para a política: As vulnerabilidades nos bancos de dados SQL devem ser corrigidas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|

## <a name="next-steps"></a>Próximas etapas

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md).