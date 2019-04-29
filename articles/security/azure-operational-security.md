---
title: Segurança Operacional do Azure | Microsoft Docs
description: Saiba mais sobre os logs do Microsoft Azure Monitor, seus serviços e como ele funciona.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: ab5b50433b85416ff471546171998e992293b0ea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586883"
---
# <a name="azure-operational-security"></a>Segurança Operacional do Azure
## <a name="introduction"></a>Introdução

### <a name="overview"></a>Visão geral
Sabemos que a segurança é o primeiro trabalho na nuvem e o quanto é importante que você encontre informações precisas e atualizadas sobre a segurança do Azure. Uma das melhores razões para usar o Azure para seus aplicativos e serviços é aproveitar a ampla variedade de ferramentas de segurança e recursos disponíveis. Essas ferramentas e recursos ajudam a tornar possível criar soluções seguras na plataforma segura do Azure. O Microsoft Azure deve fornecer confidencialidade, integridade e disponibilidade dos dados do cliente, ao mesmo tempo que também permite uma responsabilidade transparente.

Para ajudar os clientes a entender melhor a matriz de controles de segurança implementada no Microsoft Azure das perspectivas do cliente e operacionais da Microsoft, este white paper, “Segurança Operacional do Azure”, foi elaborado, que fornece uma visão abrangente da segurança operacional disponível no Microsoft Azure.

### <a name="azure-platform"></a>Plataforma Azure
O Azure é uma plataforma de serviço de nuvem pública que dá suporte a uma ampla seleção de sistemas operacionais, linguagens de programação, estruturas, ferramentas, bancos de dados e dispositivos. Ele pode executar contêineres do Linux com a integração com o Docker, criar aplicativos com JavaScript, Python, .NET, PHP, Java e Node.js e criar back-ends para dispositivos iOS, Android e Windows. O serviço de Nuvem do Azure dá suporte às mesmas tecnologias com as quais milhões de desenvolvedores e profissionais de TI já contam e confiam.

Ao se basear ou migrar ativos de TI para um provedor de serviços de nuvem pública, você está confiando na capacidade dessa organização de proteger seus aplicativos e dados com os serviços e os controles que ela oferece para gerenciar a segurança de seus ativos baseados em nuvem.

A infraestrutura do Azure foi projetada desde a instalação até os aplicativos para hospedar milhões de clientes simultaneamente e fornece uma base confiável com a qual as empresas podem atender aos seus requisitos de segurança. Além disso, o Azure oferece uma ampla variedade de opções de segurança configuráveis e a capacidade de controlá-las, de forma que você possa personalizar a segurança para atender aos requisitos exclusivos das implantações de sua organização. Este documento ajudará você a entender como as funcionalidades de segurança do Azure podem ajudá-lo a atender a esses requisitos.

### <a name="abstract"></a>Resumo
A Segurança Operacional do Azure refere-se a serviços, controles e recursos disponíveis aos usuários para proteger seus dados, aplicativos e outros recursos no Microsoft Azure. A Segurança Operacional do Azure baseia-se em uma estrutura que incorpora o conhecimento adquirido por meio de várias funcionalidades que são exclusivas à Microsoft, incluindo o Microsoft Security Development Lifecycle (SDL), o programa Microsoft Security Response Center e uma conscientização profunda do cenário de ameaças à segurança cibernética.

Este white paper descreve a abordagem da Microsoft em relação à Segurança Operacional do Azure na plataforma de nuvem do Microsoft Azure e aborda os seguintes serviços:
1.  [Azure Monitor](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

2.  [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)

3.  [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

4.  [Observador de Rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

5.  [Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)


## <a name="microsoft-azure-monitor-logs"></a>Logs do Microsoft Azure Monitor

Logs do Microsoft Azure Monitor é a solução de gerenciamento de TI para a nuvem híbrida. Usado sozinho ou para estender sua implantação existente do System Center, os logs do Azure Monitor fornece a máxima flexibilidade e controle para o gerenciamento baseado em nuvem da sua infra-estrutura.

![Logs do Azure Monitor](./media/azure-operational-security/azure-operational-security-fig1.png)

Com os logs do Azure Monitor, você pode gerenciar qualquer instância em qualquer nuvem, incluindo local, Azure, AWS, Windows Server, Linux, VMware e OpenStack, a um custo menor do que as soluções competitivas. Criado para o mundo de nuvem em primeiro lugar, os logs do Azure Monitor oferece uma nova abordagem para gerenciar sua empresa que é a maneira mais rápida e mais econômica de atender aos novos desafios de negócios e acomodar novas cargas de trabalho, aplicativos e ambientes de nuvem.

### <a name="azure-monitor-services"></a>Monitorar os serviços do Azure

A funcionalidade básica dos logs do Azure Monitor é fornecida por um conjunto de serviços que são executados no Azure. Cada serviço fornece uma função de gerenciamento específico, e você pode combinar serviços para obter cenários de gerenciamento diferentes.

| Descrição do  | DESCRIÇÃO|
| :------------- | :-------------|
| Logs do Azure Monitor | Monitorar e analisar a disponibilidade e o desempenho de recursos diferentes, incluindo máquinas físicas e virtuais. |
|Automação | Automatizar processos manuais e impor configurações de máquinas físicas e virtuais. |
| Backup | Fazer backup e restaurar dados críticos. |
| Site Recovery | Fornecer alta disponibilidade para aplicativos críticos. |

### <a name="azure-monitor-logs"></a>Logs do Azure Monitor

[Os logs do Azure Monitor](https://azure.microsoft.com/documentation/services/log-analytics) fornece serviços de monitoramento, coletando dados de recursos gerenciados em um repositório central. Esses dados podem incluir eventos, dados de desempenho ou dados personalizados fornecidos pela API. Depois de coletados, os dados ficam disponíveis para alertas, análise e exportação.


Esse método permite consolidar dados de várias fontes, de forma que você possa combinar dados dos serviços do Azure com o ambiente local existente. Ele também separa a coleta dos dados da ação executada neles para que todas as ações fiquem disponíveis para todos os tipos de dados.


![Logs do Azure Monitor](./media/azure-operational-security/azure-operational-security-fig2.png)

O serviço do Azure Monitor gerencia seus dados baseados em nuvem com segurança usando os seguintes métodos:
-   segregação de dados
-   retenção de dados
-   segurança física
-   gerenciamento de incidentes
-   conformidade
-   certificações de padrões de segurança

### <a name="azure-backup"></a>Serviço de Backup do Azure

[O Backup do Azure](https://azure.microsoft.com/documentation/services/backup) fornece dados de backup e restauração dos serviços e faz parte do pacote de produtos e serviços do Azure Monitor.
Ele protege seus dados de aplicativos e os retém por vários anos, sem nenhum investimento de capital e custos operacionais mínimos. Ele pode fazer backup de dados de servidores físicos e virtuais do Windows, além de cargas de trabalho de aplicativos, como SQL Server e SharePoint. Também pode ser usado pelo [DPM (System Center Data Protection Manager)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) para replicar dados protegidos no Azure para redundância e armazenamento de longo prazo.


Os dados protegidos no Backup do Azure são armazenados em um cofre de backup localizado em uma região geográfica específica. Os dados são replicados na mesma região e, dependendo do tipo de cofre, também podem ser replicados para outra região para resiliência adicional.

### <a name="management-solutions"></a>Soluções de gerenciamento
[O Azure Monitor](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) é baseados em nuvem gerenciamento solução de TI da Microsoft que ajuda você a gerenciar e proteger suas instalações e infraestrutura de nuvem.


[Soluções de gerenciamento](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) são pré-empacotados conjuntos de lógica pré-empacotados que implementam um cenário de gerenciamento específico usando um ou mais serviços do Azure Monitor. Soluções diferentes disponibilizadas pela Microsoft e por parceiros que você pode adicionar facilmente à sua assinatura do Azure para aumentar o valor de seu investimento no Azure Monitor. Como parceiro, você pode criar suas próprias soluções para dar suporte a seus aplicativos e serviços e oferecê-las aos usuários por meio do Azure Marketplace ou como Modelos de Início Rápido.


![Soluções de gerenciamento](./media/azure-operational-security/azure-operational-security-fig4.png)

Um bom exemplo de uma solução que usa vários serviços para oferecer mais funcionalidades é a [solução de Gerenciamento de Atualizações](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management). Esta solução usa o [registra em log do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) agente para Windows e Linux coletar informações sobre as atualizações necessárias em cada agente. Ele grava esses dados para o repositório de logs do Azure Monitor que você possa analisá-lo com um painel incluído.

Quando você cria uma implantação, os runbooks na [Automação do Azure](https://docs.microsoft.com/azure/automation/automation-intro) são usados para instalar as atualizações necessárias. Você gerencia todo o processo no portal e não precisa se preocupar com os detalhes subjacentes.

## <a name="azure-security-center"></a>Central de Segurança do Azure

A Central de Segurança do Azure ajuda a proteger os recursos do Azure. Ela fornece monitoramento de segurança integrado e gerenciamento de políticas em suas assinaturas do Azure. Dentro do serviço, você pode definir políticas não apenas em relação a suas assinaturas do Azure, mas também aos [Grupos de Recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups); portanto, é possível ser mais granular.

### <a name="security-policies-and-recommendations"></a>Políticas de segurança e recomendações 

Uma política de segurança define o conjunto de controles que são recomendados para os recursos na assinatura ou grupo de recursos especificado.

Na Central de Segurança, você define as políticas de acordo com os requisitos de segurança de sua empresa e com o tipo de aplicativo ou confidencialidade dos dados.

![Políticas de segurança e recomendações ](./media/azure-operational-security/azure-operational-security-fig5.png)


As políticas habilitadas no nível da assinatura são propagadas automaticamente para todos os grupos de recursos na assinatura, conforme mostrado no diagrama do lado direito:


### <a name="data-collection"></a>Coleta de dados

A Central de Segurança coleta dados de suas máquinas virtuais (VMs) para avaliar o estado de sua segurança, fornecer recomendações de segurança e alertar sobre ameaças. Quando você acessa pela primeira vez a Central de Segurança, a coleta de dados é habilitada em todas as VMs em sua assinatura. A coleta de dados é recomendada, mas você pode recusar desativando-a na política da Central de Segurança.

### <a name="data-sources"></a>Fontes de dados

- A Central de Segurança do Azure analisa os dados das seguintes fontes para fornecer visibilidade sobre o estado da segurança, identificar as vulnerabilidades e recomendar atenuações, e detectar as ameaças ativas:

-   Serviços do Azure: usa as informações sobre a configuração dos serviços do Azure que você implantou comunicando-se com o provedor de recursos do serviço.

- Tráfego de rede: usa os metadados do tráfego da rede de exemplo na infraestrutura da Microsoft, como IP/porta de origem/detino, tamanho do pacote e protocolo da rede.

-   Soluções de Parceiros: usa alertas de segurança das soluções de parceiros integradas, como firewalls e soluções antimalware.

-   Suas Máquinas Virtuais: usa as informações da configuração e informações sobre os eventos de segurança, como eventos do Windows e logs de auditoria, logs do IIS, mensagens do syslog e arquivos de despejo de memória corrompidos de suas máquinas virtuais.

### <a name="data-protection"></a>Proteção de dados

Para ajudar os clientes a evitarem, detectarem e responderem às ameaças, a Central de Segurança do Azure coleta e processa dados relacionados à segurança, incluindo informações da configuração, metadados, logs de eventos, arquivos de despejo corrompidos e mais. A Microsoft obedece às diretrizes rígidas de conformidade e segurança — da codificação à operação de um serviço.

-   **Segregação de dados**: os dados são mantidos separados logicamente em cada componente em todo o serviço. Todos os dados são marcados por organização. Essa marcação persiste em todo o ciclo de vida dos dados e é imposta em cada camada do serviço.

-   **Acesso a dados**: Para fornecer recomendações de segurança e investigar as possíveis ameaças de segurança, os funcionários da Microsoft podem acessar as informações coletadas ou analisadas pelos serviços do Azure, incluindo arquivos de despejo de memória, eventos de criação do processo, instantâneos de disco da VM e artefatos, que podem incluir, involuntariamente, Dados do Cliente ou dados pessoais de suas máquinas virtuais. Respeitamos os [Termos e a Política de Privacidade do Microsoft Online Services](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), que determinam que a Microsoft não usará os Dados do Cliente nem obterá as informações para fins comerciais ou de publicidade semelhantes.

-   **Uso de dados**: a Microsoft usa os padrões e a inteligência de ameaças vistos em vários locatários para aprimorar os recursos de detecção e prevenção. Fazemos isso de acordo com os compromissos de privacidade descritos em nossa [Política de Privacidade](https://www.microsoft.com/en-us/privacystatement/OnlineServices/).

### <a name="data-location"></a>Local dos dados

A Central de Segurança do Azure coleta as cópias transitórias dos seus arquivos de despejo corrompidos e analisa-as para obter evidências das tentativas de exploração e comprometimentos bem-sucedidos. A Central de Segurança do Azure executa essa análise na mesma área geográfica do workspace e exclui as cópias transitórias quando a análise é concluída. Os artefatos da máquina são armazenados de modo central na mesma região da VM.

-   **Suas Contas de Armazenamento**: uma conta de armazenamento é especificada para cada região onde as máquinas virtuais estão em execução. Isso permite que você armazene os dados na mesma região da máquina virtual na qual os dados são coletados.

-   **Armazenamento da Central de Segurança do Azure**: informações sobre os alertas de segurança, incluindo alertas de parceiros, recomendações e status de integridade da segurança são armazenadas de modo central atualmente nos Estados Unidos. Essas informações podem incluir informações de configuração relacionadas e eventos de segurança coletados de suas máquinas virtuais conforme o necessário para fornecer um alerta de segurança, recomendação ou status de integridade da segurança.


## <a name="azure-monitor"></a>Azure Monitor

O [segurança de logs do Azure Monitor](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) e permite que a solução TI monitorar de forma ativa todos os recursos, que podem ajudar a minimizar o impacto dos incidentes de segurança de auditoria. Logs de Monitor de segurança e auditoria do Azure tem domínios de segurança que podem ser usados para monitorar os recursos. O domínio de segurança fornece um acesso rápido a várias opções; para o monitoramento de segurança, os seguintes domínios são abordados mais detalhadamente:

-   Avaliação de malware
-   Avaliação de atualização
-   Identidade e Acesso.

O Azure Monitor fornece ponteiros para informações sobre tipos específicos de recursos. Ele oferece visualização, consulta, roteamento, alertas, escala automática e automação nos dados por meio da infraestrutura do Azure (Logs de Atividades) e de cada recurso individual do Azure (Logs de Diagnóstico).

![Azure Monitor](./media/azure-operational-security/azure-operational-security-fig6.png)


Os aplicativos em nuvem são complexos com muitas partes móveis. O monitoramento fornece dados para garantir que seu aplicativo permaneça ativo e em execução em um estado íntegro. Ele também ajuda a afastar os problemas potenciais ou solucionar problemas antigos.

Além disso, você pode usar os dados de monitoramento para obter mais informações sobre seu aplicativo. Esse conhecimento pode ajudá-lo a melhorar o desempenho ou a capacidade de manutenção do aplicativo ou automatizar ações que normalmente exigiriam intervenção manual.

### <a name="azure-activity-log"></a>Log de Atividades do Azure


É um log que fornece informações sobre as operações que foram executadas em recursos de sua assinatura. O Log de Atividades era conhecido como "Logs de Auditoria" ou "Logs Operacionais", pois ele relata eventos de plano de controle de suas assinaturas.

![Log de Atividades do Azure](./media/azure-operational-security/azure-operational-security-fig7.png)

Usando o Log de Atividades, você pode determinar 'o que, quem e quando' para quaisquer operações de gravação (PUT, POST, DELETE) executadas nos recursos em sua assinatura. Também é possível compreender o status da operação e outras propriedades relevantes. O Log de Atividades não inclui operações de leitura (GET) nem operações para recursos que usam o modelo Clássico.

### <a name="azure-diagnostic-logs"></a>Logs de Diagnóstico do Azure

Esses logs são emitidos por um recurso e fornecem dados avançados e frequentes sobre a operação do recurso. O conteúdo desses logs varia de acordo com o tipo de recurso.

Por exemplo, os logs de eventos do sistema Windows são uma categoria de Log de Diagnóstico para VMs, e logs de blobs, tabelas e filas são categorias de Logs de Diagnóstico para contas de armazenamento.

Os logs de diagnóstico diferem do [Log de Atividades (anteriormente conhecido como Log de Auditoria ou Log Operacional)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). O Log de Atividades fornece informações sobre as operações executadas em recursos em sua assinatura. Os Logs de Diagnóstico fornecem informações em operações que o recurso realizou por conta própria.

### <a name="metrics"></a>Métricas

O Azure Monitor permite consumir a telemetria para ter visibilidade do desempenho e da integridade de suas cargas de trabalho no Azure. Os tipos de dados de telemetria do Azure mais importantes são as métricas (também chamadas de contadores de desempenho) emitidas pela maioria dos recursos do Azure. O Azure Monitor fornece várias maneiras de configurar e consumir essas [métricas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) para monitoramento e solução de problemas. As métricas são uma fonte valiosa de telemetria e permitem que você realize as seguintes tarefas:

-   **Controlar o desempenho** do recurso (como uma VM, um site ou um aplicativo lógico) plotando as métricas em um gráfico do portal e fixando o gráfico em um painel.

-   **Ser notificado sobre um problema** que afeta o desempenho do recurso quando uma métrica cruza determinado limite.

-   **Configurar ações automatizadas**, como dimensionar automaticamente um recurso ou disparar um runbook quando uma métrica cruza certo limite.

-   **Executar análises avançadas** ou gerar relatórios sobre as tendências de desempenho ou uso do recurso.

-   **Arquive** o histórico de desempenho ou integridade do recurso para fins de conformidade ou auditoria.

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

É a capacidade do Azure que habilita a coleta de dados de diagnóstico em um aplicativo implantado. Você pode usar a extensão de diagnóstico de várias fontes diferentes. As que têm suporte no momento são as [Funções de Trabalho ou Web do Serviço de Nuvem do Azure](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), as [Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/overview) que executam o Microsoft Windows e o [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics). Outros serviços do Azure têm seu próprios diagnósticos separados.

## <a name="azure-network-watcher"></a>Observador de Rede do Azure

A auditoria da segurança de sua rede é fundamental para detectar vulnerabilidades de rede e garantir a conformidade com o modelo de governança regulatória e segurança de TI. Com a exibição Grupo de Segurança, você pode recuperar o Grupo de Segurança de Rede configurado e as regras de segurança, bem como as regras de segurança efetivas. Com a lista de regras aplicadas, é possível determinar as portas que estão abertas e avaliar a vulnerabilidade de rede.

O [Observador de Rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) é um serviço regional que permite monitorar e diagnosticar as condições em um nível de rede em, para e do Azure. As ferramentas de diagnóstico e visualização da rede disponíveis com o Observador de Rede ajudam a entender, diagnosticar e ter informações para sua rede no Azure. Esse serviço inclui a captura de pacotes, próximo salto, verificação do fluxo de IP, exibição do grupo de segurança e logs de fluxo de NSG. O monitoramento no nível do cenário fornece uma exibição completa dos recursos de rede em contraste com o monitoramento de recursos de rede individual.

![Observador de Rede do Azure](./media/azure-operational-security/azure-operational-security-fig8.png)

O Observador de Rede tem atualmente os seguintes recursos:

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">Logs de Auditoria</a>** – as operações executadas como parte da configuração das redes são registradas. Esses logs podem ser exibidos no portal do Azure ou recuperados usando as ferramentas da Microsoft, como o Power BI ou ferramentas de terceiros. Os logs de auditoria estão disponíveis por meio do portal, PowerShell, CLI e API Rest. Para obter mais informações sobre os Logs de auditoria, consulte Operações de auditoria com o Resource Manager. Os logs de auditoria estão disponíveis para as operações realizadas em todos os recursos da rede.


-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">Verificações do fluxo de IP </a>** – verifica se um pacote é permitido ou negado com base nos parâmetros do pacote com cinco tuplas das informações do fluxo (IP de Destino, IP de Origem, Porta de Destino, Porta de Origem e Protocolo). Se o pacote for negado por um Grupo de Segurança de Rede, a regra e o Grupo de Segurança de Rede que negaram o pacote serão retornados.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">Próximo salto</a>** - determina o próximo salto para os pacotes serem roteados no Azure Network Fabric, permitindo diagnosticar quaisquer rotas definidas pelo usuário mal configuradas.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">Exibição do grupo de segurança</a>** - obtém as regras de segurança efetivas e aplicadas que são usadas em uma VM.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">Log de fluxo de NSG</a>** - os logs de fluxo para os Grupos de Segurança da Rede permitem capturar os logs relacionados ao tráfego que são permitidos ou negados pelas regras de segurança no grupo. O fluxo é definido por informações com cinco tuplas – IP de Origem, IP de Destino, Porta de Origem, Porta de Destino e Protocolo.

## <a name="azure-storage-analytics"></a>Análise do Armazenamento do Azure

O [Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) pode armazenar métricas que incluem estatísticas de transação agregadas e dados de capacidade sobre Solicitações em um serviço de armazenamento. As transações são relatadas no nível de operação da API, bem como no nível de serviço de armazenamento e a capacidade é relatada no nível de serviço de armazenamento. Os dados de métricas podem ser usados para analisar o uso do serviço de armazenamento, diagnosticar problemas com solicitações feitas no serviço de armazenamento e melhorar o desempenho de aplicativos que usam um serviço.

O [Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) executa o registro em log e fornece dados de métrica para uma conta de armazenamento. Você pode usar esses dados para rastrear solicitações, analisar tendências de uso e diagnosticar problemas com sua conta de armazenamento. O log do Storage Analytics está disponível para os [serviços Blob, Fila e Tabela](https://docs.microsoft.com/azure/storage/storage-introduction). A análise de armazenamento registra informações detalhadas sobre solicitações bem-sucedidas e com falha para um serviço de armazenamento.

Essas informações podem ser usadas para monitorar solicitações individuais e diagnosticar problemas com um serviço de armazenamento. As solicitações são registradas em uma base de melhor esforço. As entradas de log são criadas somente se há solicitações feitas no ponto de extremidade de serviço. Por exemplo, se uma conta de armazenamento tiver atividades em seu ponto de extremidade Blob, mas não em seus pontos de extremidade Tabela ou Fila, somente os logs pertencentes ao serviço Blob serão criados.

Para usar a análise de armazenamento, você deve habilitá-la separadamente para cada serviço que você deseja monitorar. Você pode habilitá-la no [portal do Azure](https://portal.azure.com/); para obter detalhes, consulte [Monitorar uma conta de armazenamento no portal do Azure](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Você também pode habilitar a análise de armazenamento programaticamente por meio da API REST ou da biblioteca de cliente. Use a operação Definir Propriedades do Serviço para habilitar o Storage Analytics individualmente em cada serviço.

Os dados agregados são armazenados em um blob conhecido (para registro em log) e em tabelas conhecidas (para métricas), que podem ser acessados usando os serviços de Blob e APIs do serviço de tabela

O Storage Analytics tem um limite de 20 TB na quantidade de dados armazenados, que não depende do limite total da conta de armazenamento. Todos os logs são armazenados em [blobs de blocos](https://docs.microsoft.com/azure/storage/storage-analytics) em um contêiner chamado $logs, que é criado automaticamente quando o Storage Analytics é habilitado em uma conta de armazenamento.

As seguintes ações executadas pela análise de armazenamento são faturáveis:

-   Solicitações para criar blobs para registro
-   Solicitações para criar entidades de tabela para métricas.

> [!Note]
> Para obter mais informações sobre cobrança e políticas de retenção de dados, consulte [Storage Analytics e cobrança](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> Para obter o desempenho ideal, você deverá limitar a quantidade de discos altamente utilizados anexados à máquina virtual para evitar possíveis limitações. Se todos os discos não estiverem sendo altamente utilizados ao mesmo tempo, a conta de armazenamento poderá dar suporte a um número maior de discos.

> [!Note]
> Para obter mais informações sobre os limites da conta de armazenamento, consulte [Escalabilidade e metas de desempenho do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-scalability-targets).


Os tipos de solicitações autenticadas e anônimas a seguir são registrados.

| Autenticada  | Anônima|
| :------------- | :-------------|
| Solicitações bem-sucedidas | Solicitações bem-sucedidas |
|Solicitações com falha, incluindo tempo limite, limitação, rede, autorização e outros erros | Solicitações que usam uma SAS (Assinatura de Acesso Compartilhado), incluindo solicitações bem-sucedidas e com falha |
| Solicitações que usam uma SAS (Assinatura de Acesso Compartilhado), incluindo solicitações bem-sucedidas e com falha |Erros de tempo limite para o cliente e o servidor |
|   Solicitações de dados de análise |    Solicitações GET com falha com o código de erro 304 (Não Modificado) |
| As solicitações feitas pela própria análise de armazenamento, como criação de log ou exclusão, não estão conectadas. Uma lista completa dos dados registrados está documentada nos tópicos [Storage Analytics Logged Operations and Status Messages](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) (Mensagens de operações e status registradas do Storage Analytics) e [Storage Analytics Log Format](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) (Formato do log do Storage Analytics). | Todas as outras solicitações anônimas com falha não estão conectadas. Uma lista completa dos dados registrados está documentada em [Mensagens de operações e status registradas do Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) e [Formato do log do Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

## <a name="azure-active-directory"></a>Azure Active Directory

O Azure AD também inclui um pacote completo de funcionalidades de gerenciamento de identidade, incluindo autenticação multifator, registro de dispositivos, gerenciamento de senhas de autoatendimento, gerenciamento de grupos de autoatendimento, gerenciamento de contas com privilégios, controle de acesso baseado em função, monitoramento de uso de aplicativos, auditoria avançada, alertas e monitoramento de segurança.

-   Aprimore a segurança dos aplicativos com o acesso condicional e a autenticação multifator do AD do Azure.

-   Monitore o uso dos aplicativos e proteja sua empresa contra ameaças avançadas com monitoramento e relatórios de segurança.

O Active Directory do Azure (Azure AD) inclui relatórios de auditoria, atividade e segurança para seu diretório. [O Relatório de Auditoria do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) ajuda os clientes a identificar as ações com privilégios que ocorreram em seu Azure Active Directory. Ações com privilégios incluem alterações de elevação (por exemplo, criação de funções ou redefinições de senha), alteração de configurações de política (por exemplo, políticas de senha) ou alterações na configuração de diretório (por exemplo, as alterações às configurações de federação de domínio).

Os relatórios fornecem o registro de auditoria para o nome do evento, o ator que executou a ação, o recurso de destino afetado pela alteração e a data e hora (em UTC). Os clientes podem recuperar a lista dos eventos de auditoria do Azure Active Directory por meio do [portal do Azure](https://portal.azure.com/), conforme descrito em [Exibir os Logs de Auditoria](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). Aqui está uma lista dos relatórios incluídos:

| Relatórios de segurança  | Relatórios de atividades| Relatórios de auditoria |
| :------------- | :-------------| :-------------|
|Entradas de fontes desconhecidas | Uso do aplicativo: resumo | Relatório de auditoria de diretório |
|Entradas após várias falhas | Uso do aplicativo: detalhado |   |
|Entradas de várias geografias | Painel do aplicativo |  |
|Entradas de endereços IP com atividade suspeita |Erros de provisionamento de conta |  |
|Atividades de entrada irregulares |Dispositivos de usuário individual |  |
|Entradas de dispositivos possivelmente infectados |Atividade de usuário individual |   |
|Usuários com atividade de entrada anômala |Relatório de atividade de grupos |   |
| |Relatório de atividade de registro de redefinição de senha |   |
| |Atividade de redefinição de senha |   |



Os dados desses relatórios podem ser úteis para os aplicativos, como sistemas SIEM, auditoria e ferramentas de business intelligence. As [APIs](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) de relatório do Azure AD fornecem acesso programático aos dados por meio de um conjunto de APIs baseadas em REST. Você pode chamar essas APIs em várias ferramentas e linguagens de programação.

Os eventos no relatório de auditoria do Azure AD são mantidos por 180 dias.

> [!Note]
> Para saber mais sobre retenção de relatórios, confira [Políticas de retenção de relatório do Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Para clientes interessados no armazenamento de seus [eventos de auditoria](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) por períodos de retenção mais longos, a API de Relatório pode ser usada regularmente para efetuar pull dos eventos de auditoria em um armazenamento de dados separado.

## <a name="summary"></a>Resumo

Este artigo resume a proteção de sua privacidade e de seus dados, oferecendo softwares e serviços que ajudam você a gerenciar a infraestrutura de TI de sua organização. A Microsoft reconhece que, quando confia seus dados a terceiros, essa relação de confiança exige uma segurança rigorosa. A Microsoft obedece às diretrizes rígidas de conformidade e segurança — da codificação à operação de um serviço.  Segurança e proteção de dados é uma prioridade principal da Microsoft.

Este artigo explica

-   Como dados são coletados, processados e protegidos no pacote do Azure Monitor.

-   Analisar eventos rapidamente em várias fontes de dados. Identificar riscos à segurança e entender o escopo e o impacto de ameaças e ataques para atenuar os danos de uma violação de segurança.

-   Identificar padrões de ataque visualizando o tráfego IP mal-intencionado de saída e tipos de ameaça mal-intencionados. Entender a postura de segurança de todo o ambiente, independentemente da plataforma.

-   Capturar todos os dados de log e de eventos necessários para uma auditoria de segurança ou conformidade. Reduzir o tempo e os recursos necessários para fornecer a uma auditoria de segurança um conjunto de dados de log e de eventos completo, pesquisável e exportável.

<ul>
<li>Coletar eventos relacionados à segurança, auditoria e análise de violações para observar com atenção seus ativos:</li>
<ul>
<li>Postura de segurança</li>
<li>Problemas importantes</li>
<li>Ameaças resumidas</li>
</ul>
</ul>

## <a name="next-steps"></a>Próximas etapas

- [Design e segurança operacional](https://www.microsoft.com/trustcenter/security/designopsecurity)

A Microsoft projeta seus serviços e softwares com a segurança em mente, a fim de ajudar a garantir que sua infraestrutura de nuvem é resiliente e está protegida contra ataques.

- [Os logs do Azure Monitor | Segurança e conformidade](https://www.microsoft.com/cloud-platform/security-and-compliance)

Use dados de segurança e a análise da Microsoft para executar uma detecção de ameaças mais inteligente e eficaz.

- [Planejamento e operações da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide) Um conjunto de etapas e tarefas que você pode seguir para otimizar seu uso da Central de Segurança de acordo com o modelo de gerenciamento em nuvem e os requisitos de segurança de sua organização.

