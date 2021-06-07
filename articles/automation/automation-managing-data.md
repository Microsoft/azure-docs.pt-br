---
title: Segurança de dados da automação do Azure
description: Este artigo ajuda você a aprender como a automação do Azure protege sua privacidade e protege seus dados.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/10/2021
ms.topic: conceptual
ms.openlocfilehash: c3d1dfc5d6ea16a128f5f3bc1129f5f50bc9cb61
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954666"
---
# <a name="management-of-azure-automation-data"></a>Gerenciamento de dados da Automação do Azure

Este artigo contém vários tópicos explicando como os dados são protegidos e protegidos em um ambiente de automação do Azure.

## <a name="tls-12-enforcement-for-azure-automation"></a>Imposição de TLS 1,2 para a automação do Azure

Para garantir a segurança dos dados em trânsito para a automação do Azure, recomendamos enfaticamente que você configure o uso do protocolo TLS (Transport Layer Security) 1,2. Veja a seguir uma lista de métodos ou clientes que se comunicam por HTTPS com o serviço de automação:

* Chamadas de webhook

* Hybrid runbook Workers, que incluem computadores gerenciados por Gerenciamento de Atualizações e Controle de Alterações e inventário.

* Nós DSC

Constatou-se que versões mais antigas do protocolo TLS/protocolo SSL eram vulneráveis e embora elas ainda funcionem no momento para permitir a compatibilidade com versões anteriores, elas **não são recomendadas**. Não recomendamos definir explicitamente seu agente para usar somente o TLS 1,2, a menos que seja necessário, pois ele pode interromper os recursos de segurança no nível da plataforma que permitem detectar e aproveitar automaticamente os protocolos mais seguros à medida que eles se tornam disponíveis, como o TLS 1,3.

Para obter informações sobre o suporte a TLS 1,2 com o agente de Log Analytics para Windows e Linux, que é uma dependência para a função de Hybrid Runbook Worker, consulte [visão geral do agente de log Analytics-TLS 1,2](..//azure-monitor/agents/log-analytics-agent.md#tls-12-protocol).

### <a name="platform-specific-guidance"></a>Diretrizes específicas da plataforma

|Plataforma/linguagem | Suporte | Mais informações |
| --- | --- | --- |
|Linux | Distribuições do Linux tendem a depender do [OpenSSL](https://www.openssl.org) para suporte a TLS 1.2.  | Verifique as [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) para confirmar a sua versão do OpenSSL é suportada.|
| Windows 8.0 - 10 | Suporte e habilitado por padrão. | Para confirmar que você ainda está usando o [as configurações padrão](/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012 - 2016 | Suporte e habilitado por padrão. | Para confirmar que você ainda está usando as [configurações padrão](/windows-server/security/tls/tls-registry-settings) |
| Windows Server 7 SP1 e Windows Server 2008 R2 SP1 | Com suporte, mas não habilitado por padrão. | Consulte a página [configurações do registro de segurança de camada de transporte (TLS)](/windows-server/security/tls/tls-registry-settings) para obter detalhes sobre como habilitar.  |

## <a name="data-retention"></a>Retenção de dados

Quando você exclui um recurso na automação do Azure, ele é retido por muitos dias para fins de auditoria antes da remoção permanente. Você não pode ver nem usar o recurso durante esse período. Essa política também se aplica aos recursos que pertencem a uma conta da Automação excluída. A política de retenção se aplica a todos os usuários e atualmente não pode ser personalizada. No entanto, se você precisar manter os dados por um longo período, [poderá encaminhar os dados do trabalho de Automação do Azure para os logs do Azure Monitor](automation-manage-send-joblogs-log-analytics.md).

A tabela a seguir resume a política de retenção para diferentes recursos.

| Dados | Política |
|:--- |:--- |
| Contas |Uma conta é removida permanentemente 30 dias depois que um usuário a exclui. |
| Ativos |Um ativo é removido permanentemente 30 dias depois que é excluído por um usuário, ou 30 dias depois que a conta que o contém é excluída por um usuário. Os ativos incluem variáveis, agendas, credenciais, certificados, pacotes python 2 e conexões. |
| Nós DSC |Um nó DSC é removido permanentemente 30 dias após o cancelamento do registro de uma conta da Automação usando o portal do Azure ou o cmdlet [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode) no Windows PowerShell. Um nó também é removido permanentemente 30 dias após um usuário excluir a conta que o contém. |
| Trabalhos |Um trabalho é excluído e removido permanentemente 30 dias após a modificação, por exemplo, depois que o trabalho é concluído, interrompido ou suspenso. |
| Módulos |Um módulo é removido permanentemente 30 dias depois que é excluído por um usuário, ou 30 dias depois que a conta que o contém é excluída por um usuário. |
| Arquivos de configurações/MOF de nó |Uma configuração de nó antigo é removida permanentemente 30 dias depois que uma nova configuração de nó é gerada. |
| Relatórios de Nó |Um relatório de nó é removido de forma permanente 90 dias depois que um novo relatório é gerado para esse nó. |
| Runbooks |Um runbook é removido permanentemente 30 dias depois que um usuário exclui o recurso ou 30 dias depois que um usuário exclui a conta que contém o recurso<sup>1</sup>. |

<sup>1</sup> O runbook pode ser recuperado dentro do período de 30 dias, criando um incidente de suporte do Azure com suporte Microsoft Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Enviar uma solicitação de suporte**.

## <a name="data-backup"></a>Backup de dados

Quando você exclui uma conta da Automação no Azure, todos os objetos na conta são excluídos. Os objetos incluem runbooks, módulos, configurações, definições, trabalhos e ativos. Eles não podem ser recuperados depois que a conta é excluída. Você pode usar as informações a seguir para fazer backup do conteúdo de sua conta da Automação antes de excluí-la.

### <a name="runbooks"></a>Runbooks

Você pode exportar seus runbooks para arquivos de script usando o Portal do Azure ou o cmdlet [Get-AzureAutomationRunbookDefinition](/powershell/module/servicemanagement/azure.service/get-azureautomationrunbookdefinition) no Windows PowerShell. Você pode importar esses arquivos de script para outra conta da Automação, conforme discutido em [Gerenciar runbooks na Automação do Azure](manage-runbooks.md).

### <a name="integration-modules"></a>Módulos de integração

Você não pode exportar módulos de integração da automação do Azure, eles precisam ser disponibilizados fora da conta de automação.

### <a name="assets"></a>Ativos

Você não pode exportar ativos da Automação do Azure: certificados, conexões, credenciais, agendamentos e variáveis. Em vez disso, você pode usar o portal do Azure e os cmdlets do Azure para observar os detalhes desses ativos. Em seguida, use esses detalhes para criar quaisquer ativos usados pelos runbooks importados para outra conta da Automação.

Não é possível recuperar os valores de variáveis criptografadas, nem os campos de senha de credenciais usando cmdlets. Se você não souber esses valores, poderá recuperá-los em um runbook. Para recuperar valores de variáveis, confira [Recursos variáveis na Automação do Azure](shared-resources/variables.md). Para saber mais sobre como recuperar valores de credenciais, confira [Recursos de credenciais na Automação do Azure](shared-resources/credentials.md).

### <a name="dsc-configurations"></a>Configurações DSC

Você pode exportar suas configurações DSC para arquivos de script usando o portal do Azure ou o cmdlet [Export-AzAutomationDscConfiguration](/powershell/module/az.automation/export-azautomationdscconfiguration) no Windows PowerShell. Você pode importar e usar essas configurações em outra conta da Automação.

## <a name="geo-replication-in-azure-automation"></a>Replicação geográfica na Automação do Azure

A replicação geográfica é padrão nas contas da Automação do Azure. Você escolhe uma região primária ao configurar sua conta. O serviço de replicação geográfica interna da Automação atribui automaticamente uma região secundária à conta. Em seguida, o serviço faz o backup contínuo dos dados da conta da região primária para a região secundária. A lista completa de regiões primária e secundária pode ser encontrada em [Continuidade dos negócios e recuperação de desastres (BCDR): Regiões Combinadas do Azure](../best-practices-availability-paired-regions.md).

O backup criado pelo serviço de replicação geográfica de automação é uma cópia completa dos ativos, configurações e similares da automação. Esse backup pode ser usado se a região principal falhar e perder dados. No evento improvável de os dados de uma região primária serem perdidos, a Microsoft tentará recuperá-los.

> [!NOTE]
> A automação do Azure armazena dados do cliente na região selecionada pelo cliente. Para a finalidade de BCDR, para todas as regiões, exceto sul do Brasil e sudeste asiático, os dados de automação do Azure são armazenados em uma região diferente (região emparelhada do Azure). Somente para a região do Sul do Brasil (estado de São Paulo) da geografia do Brasil e da região da Ásia do Sudeste (Cingapura) da geografia Pacífico Asiático, armazenamos dados de automação do Azure na mesma região para acomodar os requisitos de residência de dados para essas regiões.

O serviço de replicação geográfica da Automação não pode ser acessado diretamente por clientes externos se houver uma falha regional. Se você deseja manter a configuração da Automação e os runbooks durante falhas regionais:

1. Selecione uma região secundária a ser emparelhada com a região geográfica da sua conta da Automação primária.

2. Crie uma conta da Automação na região secundária.

3. Na conta principal, exporte seus runbooks como arquivos de script.

4. Importe os runbooks para sua conta da Automação na região secundária.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre ativos seguros na Automação do Azure, confira [Criptografia de ativos seguros na Automação do Azure](automation-secure-asset-encryption.md).

* Para saber mais sobre a replicação geográfica, confira [Criar e usar a replicação geográfica ativa](../azure-sql/database/active-geo-replication-overview.md).
