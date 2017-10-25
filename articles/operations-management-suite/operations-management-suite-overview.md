---
title: "Visão geral do OMS (Operations Management Suite) | Microsoft Docs"
description: "O OMS (Microsoft Operations Management Suite) é a solução de gerenciamento de TI baseada em nuvem da Microsoft que ajuda a gerenciar e proteger sua infraestrutura local e de nuvem.  Este artigo descreve o valor do OMS, identifica os serviços e as ofertas diferentes incluídos no OMS e fornece links para conteúdo detalhado."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: bwren
ms.openlocfilehash: 452dd602387db6db04ca87f6834c9e8606185484
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-operations-management-suite-oms"></a>O que é Operations Management Suite (OMS)?
Este artigo fornece uma introdução ao OMS (Operations Management Suite), incluindo uma visão geral sobre o valor de negócios que ele fornece, os serviços e as soluções de gerenciamento incluídos e as ofertas que reúnem serviços e soluções diferentes.  Há links para a documentação detalhada sobre como implantar e usar cada solução e serviço.

## <a name="from-on-premises-to-the-cloud"></a>Do local para a nuvem
A Microsoft vem há tempos oferecendo produtos para gerenciar ambientes corporativos.  Vários produtos foram consolidados no pacote de produtos de gerenciamento do System Center em 2007.  Isso incluiu o Configuration Manager, que fornece recursos como distribuição de e inventário de software, o Operations Manager, que oferece o monitoramento proativo de sistemas e aplicativos, o Orchestrator, que inclui runbooks para automatizar processos manuais e o Data Protection Manager para backup e recuperação de dados críticos.

Conforme os recursos de computação vão mudando para a nuvem, os produtos do System Center ganharam mais recursos de nuvem, como os recursos de gerenciamento do Operations Manager e do Orchestrator no Azure.  No entanto, eles foram projetados como soluções locais e exigem um investimento significativo na implantação e manutenção do ambiente de gerenciamento local.  Para poder aproveitar totalmente a nuvem e dar suporte a aplicativos futuros, era necessária uma nova abordagem de gerenciamento.

## <a name="introducing-operations-management-suite"></a>Apresentamos o Operations Management Suite
O OMS (Operations Management Suite) é um conjunto de serviços de gerenciamento que foram criados na nuvem desde o início.  Em vez de implantar e gerenciar recursos locais, os componentes do OMS estão totalmente hospedados no Azure.  A configuração é mínima e você pode ter tudo funcionando literalmente em questão de minutos.  

- **Custo mínimo e complexidade da implantação.**  Como todos os componentes e dados do OMS são armazenados no Azure, você pode ter tudo pronto rapidamente sem a complexidade e o investimento necessários no caso de componentes locais.
- **Escale para níveis de nuvem.**  Você não precisa se preocupar em pagar por recursos de computação de que não precisa ou com falta de espaço de armazenamento, já que a nuvem permite que você pague somente pelo que realmente usa e dimensiona para a carga necessária.  Você pode começar gerenciando alguns recursos e depois dimensionar para todo o seu ambiente.
- **Aproveite os recursos mais recentes.**  Os recursos nos serviços do OMS estão sendo adicionados e atualizados constantemente.  Você sempre terá acesso aos recursos mais recentes sem a necessidade de implantar atualizações.
- **Serviços integrados.**  Embora cada um dos serviços do OMS gerem valor significativo por conta própria, eles podem ser combinados para solucionar cenários complexos de gerenciamento.  Por exemplo, um runbook na Automação do Azure pode fazer um processo de failover com o Azure Site Recovery e registrar informações no Log Analytics a fim de gerar um alerta.
- **Conhecimento global.**  As soluções de gerenciamento do OMS têm acesso contínuo às informações mais recentes.  Por exemplo, a solução de Segurança e Auditoria pode executar uma análise de ameaças usando as ameaças mais recentes que estão sendo detectadas em todo o mundo.
- **Acesso de qualquer lugar.**  Acesse seu ambiente de gerenciamento em qualquer lugar com um navegador.  Instale o aplicativo OMS no smartphone para ter acesso imediato a seus dados de monitoramento.

### <a name="is-it-just-for-the-cloud"></a>É apenas para a nuvem?
Só porque os serviços do OMS são executados na nuvem não significa que eles não podem gerenciar efetivamente seu ambiente local.  Coloque um agente em qualquer computador Windows ou Linux no seu data center e ele enviará dados para o Log Analytics, onde poderão ser analisados junto com todos os outros dados coletados de serviços na nuvem ou locais.  Use o Backup do Azure e o Azure Site Recovery para aproveitar a nuvem como backup e alta disponibilidade de recursos locais.  
Os runbooks na nuvem normalmente não podem acessar os recursos locais, mas você pode instalar um agente em um ou mais computadores para hospedar runbooks em seu data center.  Quando você inicia um runbook, basta simplesmente especificar se deseja que ele seja executado na nuvem ou em um local de trabalho.

## <a name="hybrid-management-with-system-center"></a>Gerenciamento híbrido com o System Center
Se você tiver uma instalação do System Center existente, poderá integrar esses componentes aos serviços do OMS para fornecer uma solução híbrida para ambientes locais e de nuvem, aproveitando as especialidades relativas de cada produto.  Conecte seu grupo de gerenciamento existente do Operations Manager ao Log Analytics para analisar os agentes gerenciados na nuvem.  Use o processo de backup existente com o Data Protection Manager para fazer backup dos dados na nuvem.  


## <a name="oms-services"></a>Serviços do OMS
A principal funcionalidade do OMS é oferecida por um conjunto de serviços que são executados no Azure.  Cada serviço fornece uma função de gerenciamento específico, e você pode combinar serviços para obter cenários de gerenciamento diferentes.

|| Descrição do | serviço |
|:--|:--|:--|
| ![Log Analytics](media/operations-management-suite-overview/icon-log-analytics.png) | Log Analytics | Monitorar e analisar a disponibilidade e o desempenho de recursos diferentes, incluindo máquinas físicas e virtuais. |
| ![Automação do Azure](media/operations-management-suite-overview/icon-automation.png) | Automação | Automatizar processos manuais e impor configurações de máquinas físicas e virtuais. |
| ![Serviço de Backup do Azure](media/operations-management-suite-overview/icon-backup.png) | Backup | Backup e restauração de dados críticos. |
| ![Azure Site Recovery](media/operations-management-suite-overview/icon-site-recovery.png) | Recuperação de Site | Fornecer alta disponibilidade para aplicativos críticos. |

### <a name="log-analytics"></a>Log Analytics
O [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) fornece serviços de monitoramento para o OMS coletando dados de recursos gerenciados em um repositório central.  Esses dados podem incluir eventos, dados de desempenho ou dados personalizados fornecidos pela API. Depois de coletados, os dados ficam disponíveis para alertas, análise e exportação.  Esse método permite consolidar dados de várias fontes para que você pode combinar dados de serviços do Azure com o ambiente local.  Ele também separa a coleta dos dados da ação executada neles para que todas as ações fiquem disponíveis para todos os tipos de dados.  

![Visão geral do Log Analytics](media/operations-management-suite-overview/overview-log-analytics.png)

#### <a name="collecting-data"></a>Coletando dados
Há várias maneiras de obter dados para o repositório que o Log Analytics vai analisar.

- **Computadores Windows ou Linux e máquinas virtuais.**  Instale o Microsoft Monitoring Agent em computadores ou máquinas virtuais [Windows](../log-analytics/log-analytics-windows-agents.md) e [Linux](../log-analytics/log-analytics-linux-agents.md) de que você deseja coletar dados.  O agente fará o download automaticamente da configuração do Log Analytics que define os eventos e dados de desempenho que devem ser coletados.  Você pode instalar o agente em máquinas virtuais em execução no Azure facilmente usando o portal do Azure.  Se você tiver um ambiente existente do Operations Manager, poderá conectar o grupo de gerenciamento ao Log Analytics e começar a coletar dados de todos os agentes existentes.
- **Serviços do Azure.**  O Log Analytics coleta a telemetria do [Diagnóstico do Azure e do Monitoramento do Azure](../log-analytics/log-analytics-azure-storage.md) no repositório para que você possa monitorar os recursos do Azure.
- **API do Coletor de Dados.**  O Log Analytics tem uma [API REST para preenchimento de dados de qualquer cliente](../log-analytics/log-analytics-data-collector-api.md).  Isso permite coletar dados de aplicativos de terceiros ou implementar cenários de gerenciamento personalizado.  Um método comum é usar um runbook na Automação do Azure para coletar dados e usar a API do coletor de dados para gravá-lo no repositório.

#### <a name="reporting-and-analyzing-data"></a>Emissão de relatórios e análise de dados
O Log Analytics inclui uma linguagem de consulta avançada para extrair os dados armazenados no repositório.  Como os dados de todas as fontes são armazenados como registros, você pode analisar dados de várias fontes em uma única consulta.
  
Além de análise ad hoc, o Log Analytics oferece várias maneiras de relatar e analisar dados de uma consulta.

- **Modos de exibição e painéis.**  [Modos de exibição](../log-analytics/log-analytics-view-designer.md) e [Painéis](../log-analytics/log-analytics-dashboards.md) exibem os resultados de uma consulta no portal.  As soluções de gerenciamento normalmente incluirão modos de exibição que analisam os dados da solução.  Você também pode criar seus próprios modos de exibição personalizados para analisar os dados e torná-los prontamente disponíveis no seu portal personalizado.
- **Exportação.**  Você tem a opção de exportar os resultados de qualquer consulta para analisá-la fora do Log Analytics.  Você também pode programar uma exportação regular para o [Power BI](../log-analytics/log-analytics-powerbi.md), que oferece recursos de visualização e análise relevantes.
- **API da Pesquisa de Logs.**  O Log Analytics tem uma [API REST para coletar dados de qualquer cliente](../log-analytics/log-analytics-log-search-api.md).  Isso permite trabalhar com dados coletados no repositório de forma programática ou acessá-los de outra ferramenta de monitoramento.

#### <a name="alerting"></a>Alertas
O Log Analytics pode [alertá-lo proativamente](../log-analytics/log-analytics-alerts.md) ou tomar uma ação corretiva quando detecta um problema.  Como todas as outras análises no Log Analytics, isso é feito com uma pesquisa de logs.  Esta pesquisa é executada regularmente e um alerta é criado se os resultados correspondem a critérios específicos.

![Alertas do Log Analytics](media/operations-management-suite-overview/overview-alerts.png)

Além de criar um registro de alerta no repositório do Log Analytics, os alertas podem executar as ações a seguir.

- **Email.**  Enviar um email para notificar sobre um problema detectado.
- **Runbook.**  Um alerta no Log Analytics pode iniciar um runbook na Automação do Azure.  Normalmente, isso é feito para tentar corrigir o problema detectado.  O runbook pode ser iniciado na nuvem no caso de um problema no Azure ou outra nuvem, ou pôde ser iniciado em um agente local no caso de um problema em uma máquina virtual ou física.
- **Webhook.**  Um alerta pode iniciar um webhook e passar dados dos resultados da pesquisa de logs.  Isso permite a integração com serviços externos, como um sistema de alerta alternativo, ou a tentativa de executar a ação corretiva para um site externo.

### <a name="azure-automation"></a>Automação do Azure
[A Automação do Azure](http://azure.microsoft.com/documentation/services/automation) oferece gerenciamento de configuração e automação de processo para o OMS.  Ele automatiza processos manuais e ajuda a impor configurações para computadores físicos e virtuais.  

#### <a name="process-automation"></a>Automação de processos
A Automação do Azure automatiza processos manuais usando [runbooks](../automation/automation-runbook-types.md) que são baseados em script ou fluxo de trabalho do PowerShell.  Ele também inclui runbooks de suporte a ativos, como variáveis que podem ser compartilhadas entre vários runbooks e credenciais e conexões que permitem armazenar informações criptografadas que talvez sejam necessárias à autenticação do runbook.
Os runbooks oferece automação do processo para outros serviços no pacote.  Como cada um dos outros serviços pode ser acessado com o PowerShell ou por uma API REST, você pode criar runbooks para executar funções como coletar dados de gerenciamento do Log Analytics ou iniciar um backup com o Backup do Azure.

##### <a name="accessing-resources"></a>Acessando recursos
Como os runbooks são baseados no PowerShell, eles podem gerenciar qualquer recurso que possa ser acessado com cmdlets do PowerShell.  Quando você [carrega um módulo](../automation/automation-integration-modules.md) em sua conta de automação, ele fica disponível para todos os runbooks na conta. 
 
Quando os runbooks são executados na nuvem, eles podem acessar todos os recursos acessíveis da nuvem.  Isso pode ser recursos na sua assinatura do Azure, em outra nuvem, como o AWS (Amazon Web Services) ou um serviço acessível por uma API REST.  Os runbooks na nuvem não são executados com credenciais, mas podem aproveitar os ativos da Automação, como certificados, conexões e credenciais para autenticar em relação aos recursos que eles acessam.

Os recursos em seu data center provavelmente não podem ser acessados de um runbook em execução na nuvem.  Você pode instalar um ou mais [Hybrid Runbook Workers](../automation/automation-hybrid-runbook-worker.md) em seu data center para executar runbooks que exigem acesso a recursos locais.  Ao iniciar um runbook, especifique se ele deve ser executado na nuvem ou em um trabalho específico.

![Runbooks da Automação do Azure](media/operations-management-suite-overview/overview-runbooks.png)

##### <a name="starting-a-runbook"></a>Iniciando um runbook
Os runbooks podem ser [iniciados por vários métodos](../automation/automation-starting-a-runbook.md) para que eles possam ser incluídos em vários cenários de gerenciamento.  

- **Portal do Azure.**  Como outros serviços do Azure, a Automação do Azure podem ser gerenciada no Portal do Azure.  Além de iniciar runbooks, você pode importá-los ou criar seus próprios.
- **Agendado.**  Você pode agendar runbooks para serem iniciados em intervalos regulares.  Isso permite que você repita um processo de gerenciamento regular ou recolha dados para o Log Analytics.
- **PowerShell e API.**  Você pode iniciar runbooks e passar as informações de parâmetro necessárias de um cmdlet do PowerShell ou da API REST da Automação do Azure.  
- **Webhook.**  Um webhook pode ser criado todos os runbooks que permitem sua criação de aplicativos externos ou sites da Web.
- **Alerta do Log Analytics.**  Um alerta no Log Analytics pode iniciar um runbook automaticamente para tentar corrigir o problema identificado pelo alerta.

#### <a name="configuration-management"></a>Gerenciamento de configuração
[DSC (Configuração do Estado Desejado) do PowerShell](../automation/automation-dsc-overview.md) é uma plataforma de gerenciamento do Windows PowerShell que permite implantar e impor a configuração de máquinas físicas e virtuais.  A Automação do Azure gerencia DSCs e oferece um servidor de recepção na nuvem que os agentes podem acessar para recuperar as configurações necessárias.

![DSC de Automação do Azure](media/operations-management-suite-overview/overview-dsc.png)

### <a name="azure-backup-and-azure-site-recovery"></a>Backup do Azure e Azure Site Recovery
O Backup do Azure e o Azure Site Recovery contribuem para a recuperação de desastres e a continuidade de negócios.  Cada um deles tem recursos que ajudam a garantir que os aplicativos permanecerão disponíveis em caso de interrupção e retornarão ao funcionamento normalmente quando os sistemas voltarem a ficar online.  Ambos os serviços contribuem para os RPOs (objetivos de ponto de recuperação) e os RTOs (objetivos de tempo de recuperação) definidos pela sua organização. O RPO define o limite aceitável para a indisponibilidade de dados durante uma interrupção e o RTO limita a quantidade aceitável de tempo em que um aplicativo ou serviço pode não ficar disponível durante uma interrupção.

#### <a name="azure-backup"></a>Serviço de Backup do Azure
[O Backup do Azure](http://azure.microsoft.com/documentation/services/backup) oferece backup de dados e serviços de restauração para o OMS.  Ele protege seus dados de aplicativos e os retém por vários anos, sem nenhum investimento de capital e custos operacionais mínimos.  Ele pode fazer backup de dados de servidores físicos e virtuais do Windows, além de cargas de trabalho de aplicativos, como SQL Server e SharePoint.  Também pode ser usado pelo DPM (System Center Data Protection Manager) para replicar dados protegidos no Azure para redundância e armazenamento de longo prazo.

Os dados protegidos no Backup do Azure são armazenados em um cofre de backup localizado em uma região geográfica específica. Os dados são replicados na mesma região e, dependendo do tipo de cofre, também podem ser replicados para outra região para resiliência adicional.

O Backup do Azure apresenta três cenários fundamentais.

- **Computador com Windows com o Agente de Backup do Azure.** Faça backup de arquivos e pastas por meio de qualquer servidor ou cliente Windows diretamente em seu cofre de backup do Azure.<br><br>![Computador com Windows com o Agente de Backup do Azure](media/operations-management-suite-overview/overview-backup-01.png)
- **DPM (System Center Data Protection Manager) ou Servidor de Backup do Microsoft Azure.** Aproveite o DPM ou o Servidor de Backup do Microsoft Azure para fazer backup de arquivos e pastas, além de cargas de trabalho do aplicativo, como SQL e SharePoint, no armazenamento local e replique-os em seu cofre de backup do Azure. Suporte a máquinas virtuais Windows e Linux Hyper-V ou VMware.<br><br>![DPM (System Center Data Protection Manager) ou Servidor de Backup do Microsoft Azure](media/operations-management-suite-overview/overview-backup-02.png)
- **Extensões da Máquina Virtual do Azure.** Faça backup das máquinas virtuais Windows ou Linux do Azure em seu cofre de backup do Azure.<br><br>![Extensões da Máquina Virtual do Azure](media/operations-management-suite-overview/overview-backup-03.png)



#### <a name="azure-site-recovery"></a>Azure Site Recovery
[O Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) fornece continuidade de negócios pela orquestração de replicação de máquinas virtuais e físicas locais no Azure ou em um site secundário. Se seu site primário não estiver disponível, faça failover para o local secundário para que os usuários possam continuar trabalhando e faça failback quando os sistemas voltarem a funcionar corretamente. 

O Azure Site Recovery fornece alta disponibilidade para servidores e aplicativos.  Ele contribui para sua estratégia de BCDR (continuidade dos negócios e recuperação de desastre) orquestrando a replicação, o failover e a recuperação de máquinas virtuais Hyper-V locais, máquinas virtuais VMware e servidores físicos Windows/Linux. É possível replicar computadores em um data center secundário ou estender seu data center replicando-os no Azure. A Recuperação de Site também fornece failover e recuperação simples para cargas de trabalho. Ela integra mecanismos de recuperação de desastre, como o SQL Server AlwaysOn, e fornece planos de recuperação para failover fácil de cargas de trabalho que são organizadas em camadas em vários computadores.

O Azure Site Recovery apresenta três cenários fundamentais de replicação.

- **Replicação de máquinas virtuais Hyper-V.**  Se as máquinas virtuais Hyper-V forem gerenciadas em nuvens do VMM, será possível replicá-las em um data center secundário ou no armazenamento do Azure. A replicação no Azure é feita por uma conexão segura com a Internet. A replicação em um data center secundário é feita pela LAN.  Se as máquinas virtuais Hyper-V não forem gerenciadas pelo VMM, será possível replicar apenas o armazenamento do Azure. A replicação no Azure é feita por uma conexão segura com a Internet.<br><br>![Replicação de máquinas virtuais Hyper-V](media/operations-management-suite-overview/overview-siterecovery-hyperv.png)
- **Replicação de máquinas virtuais VMWare.**  É possível replicar máquinas virtuais VMware em um data center secundário que executa o VMware ou no armazenamento do Azure. A replicação no Azure pode ocorrer por uma VPN site a site, pelo Azure ExpressRoute ou por uma conexão segura com a Internet. A replicação em um data center secundário ocorre pelo canal de dados do InMage Scout.<br><br>![Replicação de máquinas virtuais VMWare](media/operations-management-suite-overview/overview-siterecovery-vmware.png)
- **Replicação de servidores físicos Windows e Linux**  É possível replicar servidores físicos em um data center secundário ou no armazenamento do Azure. A replicação no Azure pode ocorrer por uma VPN site a site, pelo Azure ExpressRoute ou por uma conexão segura com a Internet. A replicação em um data center secundário ocorre pelo canal de dados do InMage Scout. O Azure Site Recovery traz uma solução do OMS que exibe algumas estatísticas, mas é necessário usar o portal do Azure para qualquer operação.<br><br>![Replicação de servidores físicos do Windows e Linux](media/operations-management-suite-overview/overview-siterecovery-physical.png)


A Recuperação de Site armazena os metadados em cofres localizados em determinada região geográfica do Azure. Nenhum dado replicado é armazenado pelo serviço Site Recovery.

## <a name="management-solutions"></a>Soluções de gerenciamento
[Soluções de gerenciamento](operations-management-suite-solutions.md) são conjuntos de lógica pré-empacotados que implementam um cenário de gerenciamento específico utilizando um ou mais serviços do OMS.  Existem soluções diferentes disponibilizadas pela Microsoft e por parceiros que você pode adicionar facilmente à assinatura do Azure para aumentar o valor de seu investimento no OMS.  Como parceiro, você pode criar suas próprias soluções para dar suporte a seus aplicativos e serviços e oferecê-las aos usuários por meio do Azure Marketplace ou como Modelos de Início Rápido.

Um bom exemplo de uma solução que utiliza vários serviços para oferecer mais funcionalidades é a [solução Gerenciamento de Atualizações](oms-solution-update-management.md).  Esta solução usa o agente do Log Analytics para Windows e Linux a fim de coletar informações sobre as atualizações necessárias em cada agente.  Ele grava esses dados no repositório do Log Analytics, onde você pode analisá-los com um painel incluído.  Quando você cria uma implantação, os runbooks na Automação do Azure são usados para instalar as atualizações necessárias.  Você gerencia todo o processo no portal e não precisa se preocupar com os detalhes subjacentes.

![Solução](media/operations-management-suite-overview/overview-solution.png)

A maioria das soluções pode executar uma ou mais das funções a seguir.

- Recolher Informações adicionais.  O Log Analytics reúne uma variedade de dados de clientes e serviços, incluindo dados de desempenho e eventos.  Uma solução de gerenciamento pode coletar informações adicionais não disponibilizadas por outras fontes de dados, geralmente usando runbooks da Automação do Azure.
- Fornecer análise adicional das informações coletadas.  As soluções de gerenciamento incluem painéis e modos de exibição que permitem a análise e a visualização dos dados.  Essas opções estão vinculadas a pesquisas de logs predefinidas que permitem a análise dos dados detalhadamente.  Elas também podem analisar dados que já foram coletadas no repositório, por exemplo, pesquisando nos eventos de segurança por padrões que indicam uma ameaça.
- Adicionar funcionalidade.  Algumas soluções oferecidas pela Microsoft aproveitam os recursos dos serviços principais para fornecer outras funcionalidades.  Por exemplo, o Mapa de Serviço fornece seu próprio console de descoberta e mapeia as dependências de servidor e dos processos em tempo real.
A Microsoft e seus parceiros adicionam soluções regularmente ao OMS, permitindo que você aumente o valor de seu investimento continuamente.  Você pode procurar e instalar soluções da Microsoft no Catálogo de Soluções no portal do OMS ou da Microsoft e de parceiros no Marketplace do Azure no Portal do Azure.  

![Galeria de Soluções](media/operations-management-suite-overview/solution-gallery.png)


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics).
* Saiba mais sobre a [Automação do Azure](../automation/automation-intro.md).
* Saiba mais sobre o [Backup do Azure](http://azure.microsoft.com/documentation/services/backup).
* Saiba mais sobre o [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery).
* Descubra as [soluções disponíveis](../log-analytics/log-analytics-add-solutions.md) nas diferentes ofertas do OMS. 

