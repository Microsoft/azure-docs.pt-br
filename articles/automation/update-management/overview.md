---
title: Visão geral do Gerenciamento de Atualizações da Automação do Azure
description: Este artigo fornece uma visão geral do recurso Gerenciamento de Atualizações que implementa atualizações para seus computadores com Windows e com Linux.
services: automation
ms.subservice: update-management
ms.date: 01/22/2021
ms.topic: conceptual
ms.openlocfilehash: 6e312d354a25113a764bca5e9492909d22af9873
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007730"
---
# <a name="update-management-overview"></a>Visão geral do Gerenciamento de Atualizações

Você pode usar Gerenciamento de Atualizações na automação do Azure para gerenciar atualizações do sistema operacional para suas máquinas virtuais Windows e Linux no Azure, em ambientes locais e em outros ambientes de nuvem. Você pode rapidamente avaliar o status de atualizações disponíveis em todos os computadores de agente e gerenciar o processo de instalação das atualizações necessárias para os servidores.

> [!NOTE]
> Você não pode usar um computador configurado com o Gerenciamento de Atualizações para executar scripts personalizados da Automação do Azure. Este computador só pode executar o script de atualização assinado pela Microsoft.

> [!NOTE]
> Neste momento, não há suporte para habilitar o Gerenciamento de Atualizações diretamente de um servidor habilitado para Arc. Confira [Habilitar o Gerenciamento de Atualizações em sua conta de automação](../../automation/update-management/enable-from-automation-account.md) para entender os requisitos e como habilitá-los para o servidor.

Para baixar e instalar os patches *críticos* e de *segurança* disponíveis automaticamente em sua VM do Azure, examine [aplicação automática de patches de convidado de VM](../../virtual-machines/windows/automatic-vm-guest-patching.md) para VMs do Windows.

Antes de implantar Gerenciamento de Atualizações e habilitar seus computadores para gerenciamento, verifique se você entendeu as informações nas seções a seguir.  

## <a name="about-update-management"></a>Sobre o Gerenciamento de Atualizações

As máquinas gerenciadas pelo Gerenciamento de Atualizações dependem do seguinte para executar a avaliação e para implantar atualizações:

* [Agente de log Analytics](../../azure-monitor/platform/log-analytics-agent.md) para Windows ou Linux
* DSC (PowerShell Desired State Configuration) para Linux
* Hybrid Runbook Worker de automação (instalado automaticamente quando você habilita Gerenciamento de Atualizações no computador)
* Microsoft Update ou [Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) (WSUS) para computadores Windows
* Um repositório de atualização privada ou pública para computadores Linux

O diagrama a seguir ilustra como o Gerenciamento de Atualizações avalia e aplica atualizações de segurança a todos os servidores Windows Server e Linux conectados em um espaço de trabalho:

![Fluxo de trabalho do Gerenciamento de Atualizações](./media/overview/update-mgmt-updateworkflow.png)

Gerenciamento de Atualizações pode ser usado para implantar nativamente em computadores em várias assinaturas no mesmo locatário.

Após um pacote ser lançado, leva de duas a três horas para o patch aparecer nos computadores com Linux para avaliação. Para computadores com Windows, leva de 12 a 15 horas após o lançamento de um patch até que ele seja exibido para avaliação. Quando um computador conclui uma verificação de conformidade de atualização, o agente encaminha as informações em massa para Azure Monitor logs. Em um computador com Windows, a verificação de conformidade é executada a cada 12 horas por padrão. Para um computador com Linux, a verificação de conformidade é executada a cada hora por padrão. Se o agente do Log Analytics for reiniciado, uma verificação de conformidade será iniciada dentro de 15 minutos.

Além do agendamento da verificação, a verificação de conformidade de atualizações será iniciada em 15 minutos se o agente do Log Analytics estiver sendo reiniciado antes da instalação da atualização e após a instalação da atualização.

O Gerenciamento de Atualizações relata o grau de atualização do computador com base na fonte com a qual você está configurado para sincronizar. Se o computador Windows estiver configurado para relatar para [Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) (WSUS), dependendo de quando o WSUS foi sincronizado pela última vez com Microsoft Update, os resultados poderão ser diferentes do que Microsoft Update mostra. Esse comportamento é o mesmo para computadores com Linux configurados para relatar a um repositório local em vez de para um repositório público.

> [!NOTE]
> Para relatar adequadamente o serviço, o Gerenciamento de Atualizações requer que determinadas URLs e portas sejam habilitadas. Para saber mais sobre esses requisitos, confira [Configuração de rede](../automation-hybrid-runbook-worker.md#network-planning).

Você pode implantar e instalar atualizações de software em computadores que precisam de atualizações, criando uma implantação agendada. As atualizações classificadas como opcionais não são incluídas no escopo de implantação para computadores com Windows. Somente as atualizações necessárias são incluídas no escopo de implantação.

A implantação agendada define quais computadores de destino recebem as atualizações aplicáveis. Ele faz isso especificando explicitamente determinados computadores ou selecionando um [grupo de computadores](../../azure-monitor/platform/computer-groups.md) com base em pesquisas de log de um conjunto específico de computadores (ou em uma [consulta do Azure](query-logs.md) que seleciona dinamicamente as VMs do Azure com base em critérios especificados). Esses grupos diferem da [configuração de escopo](../../azure-monitor/insights/solution-targeting.md), que é usada para controlar o direcionamento de computadores que recebem a configuração para habilitar o Gerenciamento de Atualizações. Isso impede que eles executem e relatem a conformidade de atualizações e instalem atualizações necessárias aprovadas.

Ao definir uma implantação, você também pode especificar uma agenda para aprovar e definir um período de tempo durante o qual as atualizações podem ser instaladas. Esse período é chamado de janela de manutenção. Um período de 20 minutos da janela de manutenção é reservado para reinicializações, supondo que uma seja necessária e você tenha selecionado a opção de reinicialização apropriada. Se a aplicação de patch demorar mais do que o esperado e houver menos de 20 minutos na janela de manutenção, uma reinicialização não ocorrerá.

As atualizações são instaladas por runbooks na Automação do Azure. Você não consegue exibir esses runbooks e eles não exigem nenhuma configuração. Quando uma implantação de atualizações é criada, ela cria uma agenda que inicia um runbook de atualização mestre no momento especificado para os computadores incluídos. O runbook mestre inicia um runbook filho em cada agente para instalar as atualizações necessárias.

Na data e hora especificadas na implantação da atualização, os computadores de destino executam a implantação em paralelo. Antes da instalação, uma verificação é executada para verificar se as atualizações ainda são necessárias. Para computadores cliente WSUS, se as atualizações não forem aprovadas no WSUS, a implantação da atualização falhará.

Registrar um computador para Gerenciamento de Atualizações em mais de um workspace do Log Analytics (também chamado de hospedagem múltipla) não é um procedimento compatível.

## <a name="clients"></a>Clientes

### <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

A tabela a seguir lista os sistemas operacionais com suporte para avaliações de atualização e aplicação de patches. A aplicação de patch requer um Hybrid Runbook Worker do sistema, que é instalado automaticamente quando você habilita a máquina virtual ou o servidor para gerenciamento pelo Gerenciamento de Atualizações. Para obter informações sobre os requisitos de sistema Hybrid Runbook Worker, consulte [implantar um Hybrid runbook Worker do Windows](../automation-windows-hrw-install.md) e um [Hybrid runbook Worker implantar um Linux](../automation-linux-hrw-install.md).

> [!NOTE]
> A avaliação de atualização de computadores com Linux só pode ser realizada em determinadas regiões, conforme listado na [tabela de mapeamentos](../how-to/region-mappings.md#supported-mappings) da conta de Automação e do workspace do Log Analytics.

|Sistema operacional  |Observações  |
|---------|---------|
|Windows Server 2019 (datacenter/Standard, incluindo Server Core)<br><br>Windows Server 2016 (datacenter/padrão, exceto Server Core)<br><br>Windows Server 2012 R2(Datacenter/Standard)<br><br>Windows Server 2012 | |
|Windows Server 2008 R2 (RTM e SP1 Standard)| O Gerenciamento de Atualizações dá suporte a avaliações e aplicação de patches para este sistema operacional. O [Hybrid runbook Worker](../automation-windows-hrw-install.md) tem suporte para o Windows Server 2008 R2. |
|CentOS 6 e 7 (x64)      | Os agentes do Linux requerem acesso a um repositório de atualização. O patch baseado em classificação requer que `yum` retorne dados de segurança que as versões RTM do CentOS não têm. Para obter mais informações sobre aplicação de patch com base em classificação no CentOS, confira [Classificações de atualização no Linux](view-update-assessments.md#linux).          |
|Red Hat Enterprise 6 e 7 (x64)     | Os agentes do Linux requerem acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 12 (x64)     | Os agentes do Linux requerem acesso a um repositório de atualização.        |
|Ubuntu 14, 4 LTS, 16, 4 LTS e 18, 4 LTS (x64)      |Os agentes do Linux requerem acesso a um repositório de atualização.         |

> [!NOTE]
> Os conjuntos de dimensionamento de máquinas virtuais do Azure podem ser gerenciados por meio do Gerenciamento de Atualizações. O Gerenciamento de Atualizações funciona nas instâncias e não na imagem base. Você precisará agendar as atualizações de maneira incremental, para que nem todas as instâncias de VM sejam atualizadas de imediato. Você pode adicionar nós para conjuntos de dimensionamento de máquinas virtuais seguindo as etapas em [Adicionar um computador não Azure ao Controle de Alterações e Inventário](../automation-tutorial-installed-software.md#add-a-non-azure-machine-to-change-tracking-and-inventory).

### <a name="unsupported-operating-systems"></a>Sistemas operacionais sem suporte

A tabela a seguir lista os sistemas operacionais sem suporte pelo Gerenciamento de Atualizações:

|Sistema operacional  |Observações  |
|---------|---------|
|Windows Client     | Os sistemas operacionais clientes (como Windows 7 e Windows 10) não têm suporte.<br> Para a WVD (Área de Trabalho Virtual do Windows) do Azure, o método recomendado<br> para gerenciar atualizações é [o Microsoft Endpoint Configuration Manager para o](../../virtual-desktop/configure-automatic-updates.md) gerenciamento de patches de computador cliente do Windows 10. |
|Windows Server 2016 Nano Server     | Sem suporte.       |
|Nós do Serviço de Kubernetes do Azure | Sem suporte. Use o processo de aplicação de patch descrito em [Aplicar atualizações de segurança e kernel a nós do Linux no AKS (Serviço de Kubernetes do Azure)](../../aks/node-updates-kured.md)|

### <a name="system-requirements"></a>Requisitos de sistema

As informações a seguir descrevem os requisitos específicos do sistema operacional. Para obter diretrizes adicionais, confira [Planejamento de rede](#ports). Para entender os requisitos do TLS 1,2, consulte [imposição tls 1,2 para a automação do Azure](../automation-managing-data.md#tls-12-enforcement-for-azure-automation).

#### <a name="windows"></a>Windows

Requisitos de software:

- É necessário o .NET Framework 4.6 ou posterior. ([Baixe o .NET Framework](/dotnet/framework/install/guide-for-developers).
- O Windows PowerShell 5,1 é necessário ([Baixe o Windows Management Framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616).)

Os agentes do Windows precisam ser configurados para comunicarem-se com um servidor WSUS, caso contrário, eles exigem acesso ao Microsoft Update. Para computadores híbridos, recomendamos instalar o agente de Log Analytics para Windows primeiro conectando o computador aos [servidores habilitados para Arc do Azure](../../azure-arc/servers/overview.md)e, em seguida, use Azure Policy para atribuir o agente de log Analytics de implantação à política interna de [computadores Arc do Windows Azure](../../governance/policy/samples/built-in-policies.md#monitoring) . Como alternativa, se você planeja monitorar as máquinas com Azure Monitor para VMs, use a iniciativa [habilitar Azure monitor para VMs](../../governance/policy/samples/built-in-initiatives.md#monitoring) .

Você pode usar o Gerenciamento de Atualizações com o Microsoft Endpoint Configuration Manager. Para saber mais sobre cenários de integração, confira [Integrar o Gerenciamento de Atualizações com o Microsoft Endpoint Configuration Manager](mecmintegration.md). O [agente do Log Analytics para Windows](../../azure-monitor/platform/agent-windows.md) é necessário para servidores Windows gerenciados por sites em seu ambiente do Configuration Manager.

Por padrão, as VMs do Windows que são implantadas no Azure Marketplace são definidas para receber atualizações automáticas do serviço Windows Update. Esse comportamento não é alterado quando você adiciona VMs do Windows ao seu workspace. Se você não gerenciou atualizações ativamente usando o Gerenciamento de Atualizações, o comportamento padrão (para aplicar automaticamente as atualizações) é aplicado.

> [!NOTE]
> Você pode modificar a Política de Grupo para que as reinicializações do computador possam ser executadas somente pelo usuário, não pelo sistema. Os computadores gerenciados poderão ficar presos se o Gerenciamento de Atualizações não tiver direitos para reinicializar o computador sem interação manual do usuário. Para obter mais informações, confira [Definir configurações de Política de Grupo para Atualizações Automáticas](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Requisitos de software:

- O computador requer acesso a um repositório de atualização, seja privado ou público.
- TLS 1.1 ou TLS 1.2 é necessário para interagir com o gerenciamento de atualizações.
- Python 2. x instalado.

> [!NOTE]
> A avaliação de atualização de computadores com Linux só é compatível com determinadas regiões. Confira a [tabela de mapeamentos](../how-to/region-mappings.md#supported-mappings) da conta de Automação e do workspace do Log Analytics.

Para computadores híbridos, é recomendável instalar o agente de Log Analytics para Linux primeiro conectando seu computador aos [servidores habilitados para Arc do Azure](../../azure-arc/servers/overview.md)e, em seguida, use Azure Policy para atribuir o agente de log Analytics de implantação à política interna de [computadores do Arc do Azure para Linux](../../governance/policy/samples/built-in-policies.md#monitoring) . Como alternativa, se você planeja monitorar as máquinas com Azure Monitor para VMs, use a iniciativa [habilitar Azure monitor para VMs](../../governance/policy/samples/built-in-initiatives.md#monitoring) .

As VMs criadas a partir das imagens de Red Hat Enterprise Linux (RHEL) sob demanda que estão disponíveis no Azure Marketplace são registradas para acessar a [RHUI (infraestrutura de atualização do Red Hat)](../../virtual-machines/workloads/redhat/redhat-rhui.md) implantada no Azure. Qualquer distribuição do Linux deve ser atualizada nos repositórios de distribuição de arquivo online usando os métodos compatíveis com a distribuição.

## <a name="permissions"></a>Permissões

Para criar e gerenciar implantações de atualização, você precisa ter permissões específicas. Para saber mais sobre essas permissões, confira [Acesso baseado em Função – Gerenciamento de Atualizações](../automation-role-based-access-control.md#update-management-permissions).

## <a name="update-management-components"></a>Componentes do Gerenciamento de Atualizações

O Gerenciamento de Atualizações usa os recursos descritos nesta seção. Esses recursos são adicionados automaticamente à sua conta de Automação quando você habilita o Gerenciamento de Atualizações.

### <a name="hybrid-runbook-worker-groups"></a>Grupos de Runbook Worker Híbrido

Depois de habilitar Gerenciamento de Atualizações, qualquer computador Windows conectado diretamente ao seu espaço de trabalho do Log Analytics é automaticamente configurado como um Hybrid Runbook Worker de sistema para dar suporte aos runbooks que dão suporte a Gerenciamento de Atualizações.

Cada computador com Windows que é gerenciado pelo Gerenciamento de Atualizações é listado no painel de grupos do Hybrid Worker como um grupo do Hybrid Worker do sistema para a conta de Automação. Os grupos usam a convenção de nomenclatura `Hostname FQDN_GUID`. Não é possível direcionar esses grupos com runbooks em sua conta. Se você tentar fazê-lo, a tentativa falhará. Esses grupos são destinados a dar suporte somente ao Gerenciamento de Atualizações. Para saber mais sobre como exibir a lista de computadores Windows configurados como um Hybrid Runbook Worker, consulte [Exibir Hybrid runbook Workers](../automation-hybrid-runbook-worker.md#view-system-hybrid-runbook-workers).

Você pode adicionar o computador Windows a um usuário Hybrid Runbook Worker grupo em sua conta de automação para dar suporte a runbooks de automação se usar a mesma conta para Gerenciamento de Atualizações e a associação de grupo de Hybrid Runbook Worker. Essa funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.

### <a name="management-packs"></a>Pacotes de gerenciamento

Se o grupo de gerenciamento do Operations Manager estiver [conectado a um workspace do Log Analytics](../../azure-monitor/platform/om-agents.md), os pacotes de gerenciamento a seguir serão instalados no Operations Manager. Esses pacotes de gerenciamento também são instalados para o Gerenciamento de Atualizações em computadores com Windows conectados diretamente. Você não precisa configurar ou gerenciar esses pacotes de gerenciamento.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* MP de Implantação de Atualizações

> [!NOTE]
> Se você tiver um grupo de gerenciamento do Operations Manager 1807 ou 2019 conectado a um workspace do Log Analytics com agentes configurados no grupo de gerenciamento para coletar dados de log, será necessário substituir o parâmetro `IsAutoRegistrationEnabled` e defini-lo como True na regra **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init**.

Para obter mais informações sobre atualizações para pacotes de gerenciamento, confira [Conectar o Operations Manager aos logs do Azure Monitor](../../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Para que o Gerenciamento de Atualizações gerencie totalmente os computadores com o agente do Log Analytics, você precisa atualizar para o agente do Log Analytics para Windows ou o agente do Log Analytics para Linux. Para saber como atualizar o agente, consulte [como atualizar um agente do Operations Manager](/system-center/scom/deploy-upgrade-agents). Em ambientes que usam o Operations Manager, você precisa estar executando o System Center Operations Manager 2012 R2 UR 14 ou posterior.

## <a name="data-collection"></a>Coleta de dados

### <a name="supported-sources"></a>Fontes compatíveis

A tabela a seguir descreve as fontes conectadas compatíveis com o Gerenciamento de Atualizações:

| Fonte conectada | Com suporte | Descrição |
| --- | --- | --- |
| Agentes do Windows |Sim |O Gerenciamento de Atualizações coleta informações sobre atualizações do sistema de agentes do Windows e, em seguida, inicia a instalação das atualizações necessárias. |
| Agentes do Linux |Sim |O Gerenciamento de Atualizações coleta informações sobre atualizações do sistema de agentes para Linux e, em seguida, inicia a instalação das atualizações necessárias nas distribuições compatíveis. |
| Grupo de gerenciamento do Operations Manager |Sim |O Gerenciamento de Atualizações coleta informações sobre atualizações do sistema de agentes em um grupo de gerenciamento conectado.<br/><br/>Não é necessária uma conexão direta entre o agente do Operations Manager e os logs do Azure Monitor. Os dados são encaminhados do grupo de gerenciamento para o espaço de trabalho do Log Analytics. |

### <a name="collection-frequency"></a>Frequência de coleta

O Gerenciamento de Atualizações verifica os dados em computadores gerenciados usando as regras a seguir. Pode demorar entre 30 minutos e seis horas para o painel exibir os dados atualizados dos computadores gerenciados.

* Cada computador com Windows – o Gerenciamento de Atualizações faz uma verificação duas vezes por dia para cada computador.

* Cada computador com Linux – o Gerenciamento de Atualizações faz uma verificação a cada hora.

A média de uso de dados por logs do Azure Monitor para um computador usando o Gerenciamento de Atualizações é de aproximadamente 25 MB por mês. Esse valor é somente uma aproximação e está sujeito a alterações, dependendo do seu ambiente. Recomendamos que você monitore seu ambiente para controlar seu uso exato. Para obter mais informações sobre como analisar o uso de dados de logs de Azure Monitor, consulte [gerenciar o uso e o custo](../../azure-monitor/platform/manage-cost-storage.md).

## <a name="network-planning"></a><a name="ports"></a>Planejamento de rede

Verifique a [configuração de rede da automação do Azure](../automation-network-configuration.md#hybrid-runbook-worker-and-state-configuration) para obter informações detalhadas sobre as portas, URLs e outros detalhes de rede necessários para gerenciamento de atualizações.

Para computadores Windows, você também precisa permitir o tráfego para os ponto de extremidade exigidos pelo Windows Update. Você pode encontrar uma lista atualizada de pontos de extremidade necessários em [Problemas relacionados a HTTP/Proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Se você tiver um [Servidor do Windows Update](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment) local, também precisará permitir o tráfego para o servidor especificado em sua [chave do WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

Para computadores com Red Hat Linux, confira [IPs para os servidores de distribuição de conteúdo da RHUI](../../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) para conhecer os pontos de extremidade necessários. Para outras distribuições do Linux, confira a documentação do provedor.

Para obter mais informações sobre as portas necessárias para o Hybrid Runbook Worker, confira [Endereços do Gerenciamento de Atualizações para o Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md#update-management-addresses-for-hybrid-runbook-worker).

Se suas políticas de segurança de ti não permitirem que computadores na rede se conectem à Internet, você poderá configurar um [log Analytics gateway](../../azure-monitor/platform/gateway.md) e, em seguida, configurar o computador para se conectar por meio do gateway para a automação do Azure e Azure monitor.

## <a name="update-classifications"></a>Classificações de origem

A tabela a seguir define as classificações compatíveis com o Gerenciamento de Atualizações para atualizações do Windows.

|classificação  |Descrição  |
|---------|---------|
|Atualizações críticas     | Uma atualização para um problema específico que aborda um bug crítico não relacionado à segurança.        |
|Atualizações de segurança     | Uma atualização para um problema específico do produto relacionadas à segurança.        |
|Pacotes cumulativos de atualização     | Um conjunto cumulativo de hotfixes que são reunidos para facilitar a implantação.        |
|Feature packs     | Novos recursos do produto que são distribuídos fora de uma versão do produto.        |
|Service packs     | Um conjunto cumulativo de hotfixes que são aplicados a um aplicativo.        |
|Atualizações de definição     | Uma atualização para vírus ou outros arquivos de definição.        |
|Ferramentas     | Um utilitário ou recurso que ajuda a concluir uma ou mais tarefas.        |
|Atualizações     | Uma atualização para um aplicativo ou arquivo que está atualmente instalado.        |

A tabela a seguir define as classificações compatíveis para atualizações do Linux.

|classificação  |Descrição  |
|---------|---------|
|Atualizações críticas ou de segurança     | Atualizações para um problema específico ou um problema relacionado à segurança específico do produto.         |
|Outras atualizações     | Todas as outras atualizações que não são críticas nem de segurança.        |

>[!NOTE]
>A classificação de atualização para computadores Linux só está disponível quando usada em regiões de nuvem pública do Azure com suporte. Não há nenhuma classificação de atualizações do Linux ao usar Gerenciamento de Atualizações nas seguintes regiões de nuvem nacional:
>
>* Azure US Government
>* 21Vianet na China
>
> Em vez de serem classificados, as atualizações são relatadas na categoria **outras atualizações** .
>
> Gerenciamento de Atualizações usa os dados publicados pelas distribuições com suporte, especificamente seus arquivos de [oval](https://oval.mitre.org/) (linguagem de avaliação e de vulnerabilidade aberta) lançados. Como o acesso à Internet é restrito a essas nuvens nacionais, Gerenciamento de Atualizações não pode acessar os arquivos.

Para o Linux, Gerenciamento de Atualizações pode distinguir entre atualizações críticas e atualizações de segurança na nuvem em **segurança** de classificação e **outras**, ao mesmo tempo em que exibe dados de avaliação devido ao enriquecimento de dados na nuvem. Para aplicação de patch, o Gerenciamento de Atualizações se baseia em dados de classificação disponíveis no computador. Ao contrário de outras distribuições, o CentOS não tem essas informações disponíveis na versão RTM. Se você tiver computadores CentOS configurados para retornar dados de segurança para o comando a seguir, o Gerenciamento de Atualizações poderá aplicar patch com base em classificações.

```bash
sudo yum -q --security check-update
```

Atualmente, não há nenhum método compatível para habilitar a disponibilidade de dados nativos de classificação em CentOS. Neste momento, o suporte limitado é fornecido aos clientes que podem ter habilitado esse recurso por conta própria.

Para classificar atualizações no Red Hat Enterprise versão 6, você precisa instalar o plug-in yum-security. No Red Hat Enterprise Linux 7, esse plug-in já faz parte do yum propriamente dito e não há necessidade de instalar nada. Para obter mais informações, confira o [artigo de conhecimento](https://access.redhat.com/solutions/10021) do Red Hat a seguir.

Quando você agenda uma atualização para ser executada em um computador Linux, isso, por exemplo, é configurado para instalar somente as atualizações que correspondem à classificação de **segurança** , as atualizações instaladas podem ser diferentes de ou são subconjuntos das atualizações correspondentes a essa classificação. Quando uma avaliação das atualizações do sistema operacional pendentes para seu computador Linux é executada, os arquivos de [linguagem oval e de vulnerabilidades](https://oval.mitre.org/) fornecidas pelo fornecedor do Linux distribuição são usados pelo gerenciamento de atualizações para classificação.

A categorização é feita para atualizações do Linux como **segurança** ou **outras** com base nos arquivos oval, que inclui atualizações que abordam problemas de segurança ou vulnerabilidades. Mas quando o agendamento de atualização é executado, ele é executado no computador Linux usando o Gerenciador de pacotes apropriado, como YUM, APT ou ZYPPER para instalá-los. O Gerenciador de pacotes para o distribuição do Linux pode ter um mecanismo diferente para classificar atualizações, onde os resultados podem diferir daqueles obtidos dos arquivos OVAL por Gerenciamento de Atualizações. Para verificar manualmente o computador e entender quais atualizações são relevantes para a segurança do Gerenciador de pacotes, consulte [solucionar problemas de implantação de atualização do Linux](../troubleshoot/update-management.md#updates-linux-installed-different).

## <a name="integrate-update-management-with-configuration-manager"></a>Integração do Gerenciamento de Atualizações com o Configuration Manager

Os clientes que investiram no Microsoft Endpoint Configuration Manager para gerenciar PCs, servidores e dispositivos móveis também dependem da força e maturidade do Configuration Manager para ajudá-los a gerenciar as atualizações de software. Para saber mais sobre como integrar o Gerenciamento de Atualizações com o Configuration Manager, confira [Integrar o Gerenciamento de Atualizações com o Microsoft Endpoint Configuration Manager](mecmintegration.md).

## <a name="third-party-updates-on-windows"></a>Atualizações de terceiros no Windows

O Gerenciamento de Atualizações se baseia no repositório de atualização configurado localmente para atualizar sistemas Windows compatíveis, ou seja, o WSUS ou o Windows Update. Ferramentas como [System Center Updates Publisher](/configmgr/sum/tools/updates-publisher) permitem que você publique atualizações personalizadas com o WSUS. Esse cenário permite que o Gerenciamento de Atualizações atualize computadores que usam o Configuration Manager como repositório de atualização com software de terceiros. Para saber como configurar o Updates Publisher, veja [Instalar o Updates Publisher](/configmgr/sum/tools/install-updates-publisher).

## <a name="enable-update-management"></a>Habilitar Gerenciamento de Atualizações

Aqui estão as maneiras como você pode habilitar o Gerenciamento de Atualizações e selecionar os computadores a serem gerenciados:

- Usando um modelo do Azure [Resource Manager](enable-from-template.md) para implantar gerenciamento de atualizações em uma conta de automação nova ou existente e Azure monitor espaço de trabalho log Analytics em sua assinatura. Ele não configura o escopo de computadores que devem ser gerenciados; isso é realizado como uma etapa separada após o uso do modelo.

- Da sua [conta de automação](enable-from-automation-account.md) para uma ou mais máquinas do Azure e não Azure, incluindo servidores habilitados para Arc.

- Usando o método [runbook](enable-from-runbook.md) **Enable-AutomationSolution** .

- Para uma [VM do Azure selecionada](enable-from-vm.md) da página **máquinas virtuais** no portal do Azure. Esse cenário está disponível para VMs do Linux e do Windows.

- Para [várias VMs do Azure](enable-from-portal.md) , selecione-as na página **máquinas virtuais** no portal do Azure.

> [!NOTE]
> O Gerenciamento de Atualizações requer a vinculação de um workspace do Log Analytics à sua conta de Automação. Para obter uma lista definitiva de regiões compatíveis, confira [Mapeamentos de Workspace do Azure](../how-to/region-mappings.md). Os mapeamentos de região não afetam a capacidade de gerenciar VMs em uma região separada da sua conta de Automação.

## <a name="next-steps"></a>Próximas etapas

* Para obter detalhes sobre como trabalhar com Gerenciamento de Atualizações, consulte [gerenciar atualizações para suas VMs](manage-updates-for-vm.md).

* Examine as perguntas frequentes sobre Gerenciamento de Atualizações nas [Perguntas frequentes sobre a Automação do Azure](../automation-faq.md).
