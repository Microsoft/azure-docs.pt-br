---
title: Linha de base de segurança do Azure para Serviços de Nuvem do Azure
description: A linha de base de segurança de Serviços de Nuvem do Azure fornece diretrizes e recursos de procedimentos para executar a implementação de recomendações de segurança especificadas no Azure Security Benchmark.
author: msmbaldwin
ms.service: cloud-services
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c389cfd2af6fe83ec232e5f205041676ba46bd2a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558186"
---
# <a name="azure-security-baseline-for-azure-cloud-services"></a>Linha de base de segurança do Azure para Serviços de Nuvem do Azure

Essa linha de base de segurança aplica diretrizes do [Azure Security Benchmark versão 1.0](../security/benchmarks/overview-v1.md) em Serviços de Nuvem do Microsoft Azure. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure.
O conteúdo é agrupado por **controles de segurança** definidos pelo Azure Security Benchmark e por diretrizes relacionadas a Serviços de Nuvem e aplicáveis a eles. Os **Controles** não são aplicáveis a Serviços de Nuvem excluídos.

 
Para conferir de que modo os Serviços de Nuvem executam um mapeamento completo no Azure Security Benchmark, confira o [Arquivo de mapeamento completo da linha de base de segurança de Serviços de Nuvem](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Diretrizes**: crie uma Rede Virtual clássica do Azure com sub-redes públicas e privadas separadas para impor um isolamento baseado em portas e intervalos de IP confiáveis. Essa rede virtual e as sub-redes deverão ser recursos baseados na Rede Virtual clássica (implantação clássica), não os recursos atuais do Azure Resource Manager.  

Permita ou negue o tráfego usando um grupo de segurança de rede que contém regras de controle de acesso baseadas na direção do tráfego, no protocolo, no endereço e na porta de origem, bem como no endereço e na porta de destino. As regras de um grupo de segurança de rede podem ser alteradas a qualquer momento. Além disso, elas serão aplicadas a todas as instâncias associadas.

Os Serviços de Nuvem do Microsoft Azure (Clássico) não podem ser inseridos em redes virtuais do Azure Resource Manager. No entanto, as redes virtuais baseadas no Resource Manager e as redes virtuais baseadas na implantação clássica podem ser conectadas por meio de um emparelhamento. 

- [Visão geral do Grupo de Segurança de Rede](../virtual-network/network-security-groups-overview.md)

- [Emparelhamento de rede virtual](./cloud-services-connectivity-and-networking-faq.md#how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: monitorar e registrar em log a configuração e o tráfego de redes virtuais, sub-redes e NICs

**Diretrizes**: documente e monitore sua configuração de Serviços de Nuvem do Azure para receber informações sobre alterações. Use o arquivo de configuração do serviço para especificar o número de instâncias de função a ser implantado em cada função do serviço, os valores das definições de configuração e as impressões digitais de todos os certificados associados a uma função. 

Caso o serviço faça parte de uma rede virtual, as informações de configuração da rede devem ser fornecidas no arquivo de configuração de serviço, bem como no arquivo de configuração da rede virtual. A extensão padrão do arquivo de configuração de serviço é .cscfg. Observe que o Azure Policy não é compatível com implantações Clássicas para executar a imposição de configuração.

Defina os valores de configuração de um serviço de nuvem no arquivo de configuração de serviço (.cscfg) e a definição em um arquivo de definição de serviço (.csdef). Use o arquivo de definição de serviço para definir o modelo de serviço de um aplicativo. Defina as funções que estão disponíveis para um serviço de nuvem, bem como especifique os pontos de extremidade de serviço. Registre em log a configuração de Serviços de Nuvem do Azure usando um arquivo de configuração de serviço. As reconfigurações podem ser feitas por meio do arquivo ServiceConfig.cscfg. 

Monitore a definição de serviço do elemento opcional NetworkTrafficRules, que restringe quais funções podem se comunicar com pontos de extremidade específicos e internos. Configure o nó NetworkTrafficRules, um elemento opcional no arquivo de definição de serviço, para especificar de que modo as funções deverão se comunicar entre si. Coloque limites em quais funções podem acessar pontos de extremidade internos de uma função específica.  Observe que a definição de serviço não poderá ser alterada. 

Habilite logs de fluxo do grupo de segurança de rede, bem como envie os logs a uma conta de Armazenamento do Azure a fim de executar uma auditoria. Envie logs de fluxo a um workspace do Log Analytics, bem como use o recurso de Análise de Tráfego para fornecer insights sobre padrões de tráfego em seu locatário do Azure. Algumas vantagens do recurso de Análise de Tráfego incluem: a capacidade de visualizar a atividade de rede, identificar pontos de acesso e ameaças à segurança, entender os padrões de fluxo do tráfego e identificar configurações inadequadas de rede.

- [Azure Resource Manager versus Implantação clássica: compreenda os modelos de implantação e o estado dos seus recursos](../azure-resource-manager/management/deployment-models.md)

- [Arquivo de configuração de Serviços de Nuvem](schema-cscfg-file.md)

- [Lista de serviços compatíveis com o Azure Policy](/cli/azure/azure-services-the-azure-cli-can-manage)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Diretrizes**: a Microsoft usa o TLS (protocolo TLS) v1.2 para proteger dados durante o envio deles a Serviços de Nuvem do Azure e clientes. Os datacenters da Microsoft negociam uma conexão TLS com os sistemas cliente que se conectam aos serviços do Azure. O TLS fornece autenticação forte, privacidade de mensagem e integridade (habilitando a detecção de adulteração, interceptação e falsificação de mensagens), interoperabilidade, flexibilidade de algoritmo e facilidade de implantação e uso.

- [Conceitos básicos sobre a criptografia](../security/fundamentals/encryption-overview.md)

- [Configurar certificados TLS/SSL](cloud-services-configure-ssl-certificate-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: rejeitar comunicações com endereços IP maliciosos conhecidos

**Diretrizes**: a Nuvem do Azure implementa uma segurança de rede multicamadas para proteger serviços de plataforma contra DDoS (negação de serviço distribuído). A Proteção contra DDoS do Azure faz parte do processo de monitoramento contínuo da Nuvem do Azure. Ele é aprimorado de modo contínuo por meio de testes de penetração. Essa Proteção contra DDoS foi projetada para resistir tanto a ataques externos quanto de outros locatários do Azure. 

Há modos diversos de bloquear ou negar a comunicação em Serviços de Nuvem do Azure, além da proteção em nível de plataforma. Eles são: 

-  Criar uma tarefa de inicialização para bloquear de modo seletivo endereços IP específicos
-  Restringir um acesso de função Web do Azure a um conjunto específico de endereços IP alterando o arquivo web.config do IIS

Impeça o tráfego de entrada para a URL ou o nome padrão de Serviços de Nuvem, como *.cloudapp.net. Além disso, defina o cabeçalho de host para um nome DNS personalizado na opção de configuração de associação do site do arquivo de definição (* .csdef) de Serviços de Nuvem.

Configure uma regra de Negação de Aplicação para atribuições de administrador da assinatura clássica. Por padrão, após a definição de um ponto de extremidade interno, a comunicação poderá fluir de qualquer função para o ponto de extremidade interno de uma função sem qualquer restrição. Para restringir a comunicação, adicione um elemento NetworkTrafficRules ao elemento ServiceDefinition no arquivo de definição de serviço.

- [Como bloquear/desabilitar o tráfego de entrada para a URL padrão do meu serviço de nuvem](./cloud-services-connectivity-and-networking-faq.md#how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service)

- [Proteção contra DDoS do Azure](./cloud-services-connectivity-and-networking-faq.md#how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service)

- [Bloquear um endereço IP específico](./cloud-services-startup-tasks-common.md#block-a-specific-ip-address)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="15-record-network-packets"></a>1.5: Registrar os pacotes de rede

**Diretrizes**: use um serviço de monitoramento de desempenho, diagnóstico e análise de rede chamado Observador de Rede do Azure, que permite executar o monitoramento de redes do Azure. A extensão da máquina virtual, além de outras funcionalidades avançadas, do Agente do Observador de Rede é um requisito usado sob demanda para capturar o tráfego de rede em Máquinas Virtuais do Azure. Instale a extensão da máquina virtual do Agente do Observador de Rede e ative logs de fluxo do grupo de segurança de rede.

Configure o registro em log de fluxo em um grupo de segurança de rede. Examine detalhes sobre de que modo implantar a extensão da Máquina Virtual do Observador de Rede em uma Máquina Virtual existente e implantada por meio do modelo de implantação clássico.

- [Configurar o registro em log de fluxo em um grupo de segurança de rede](../virtual-machines/extensions/network-watcher-linux.md)

- [Para obter mais informações sobre como configurar logs de fluxo, acesse](/cli/azure/azure-services-the-azure-cli-can-manage)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar IDS/IPS (sistemas de detecção de intrusões/prevenção de intrusões) baseados em rede

**Diretrizes**: os Serviços de Nuvem do Azure não têm uma capacidade interna de IDS nem de IPS. Os clientes podem selecionar e implantar uma solução suplementar de IDS ou IPS baseada em rede do Azure Marketplace com base em requisitos organizacionais. Ao usar soluções de terceiros, lembre-se de testar de modo abrangente a solução selecionada de IDS ou IPS com Serviços de Nuvem do Azure para garantir a operação e a funcionalidade adequadas.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Diretrizes**: certificados de serviço anexados a Serviços de Nuvem do Azure permitem estabelecer uma comunicação segura com o serviço. Esses certificados são configurados na definição do serviço e implantados de modo automático na máquina virtual que está executando determinada instância de uma função Web. É possível usar um certificado de serviço, que pode autenticar um ponto de extremidade HTTPS exposto, em uma função Web, por exemplo. 

É necessário apenas carregar um novo certificado e alterar o valor da impressão digital no arquivo de configuração de serviço para atualizar o certificado.

Use o protocolo TLS 1.2, o método de proteção de dados usado com mais frequência, para proteger dados, bem como fornecer confidencialidade e integridade. 

Em geral, é possível implantar um Gateway de Aplicativo do Azure habilitado para o Firewall de Aplicativo Web do Azure a fim de proteger aplicativos Web contra ataques, como o OWASP Top 10. 

- [Certificados de Serviço](cloud-services-certs-create.md)

- [Como configurar o TLS para executar um aplicativo no Azure](cloud-services-configure-ssl-certificate-portal.md)

- [Como implantar o Gateway de Aplicativo](../application-gateway/quick-create-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: intensifique e monitore a configuração de Serviços de Nuvem do Azure para receber informações sobre alterações. O arquivo de configuração de serviço especifica o número de instâncias de função a ser implantado para cada função no serviço, os valores das configurações e as impressões digitais de todos os certificados associados a uma função. 

Caso o serviço faça parte de uma rede virtual, as informações de configuração da rede devem ser fornecidas no arquivo de configuração de serviço, bem como no arquivo de configuração da rede virtual. A extensão padrão do arquivo de configuração de serviço é .cscfg.

Observe que o Azure Policy não é compatível com Serviços de Nuvem do Azure para executar a imposição de configuração.

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: Documentar regras de configuração de tráfego

**Diretrizes**: os grupos de segurança de rede do Azure podem ser usados para filtrar o tráfego de rede para recursos do Azure, bem como deles em uma Rede Virtual do Azure. Um grupo de segurança de rede contém regras de segurança que permitem ou negam o tráfego de rede de entrada para vários tipos de recursos do Azure ou o tráfego de rede de saída deles. Para cada regra, você pode especificar origem e destino, porta e protocolo.

Use o campo "Descrição" a fim de executar regras individuais de grupos de segurança de rede em Serviços de Nuvem do Azure para documentar regras que permitem o tráfego de entrada e saída de uma rede.

- [Como filtrar o tráfego de rede usando regras de grupos de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Diretrizes**: use recursos de monitoramento de ponto de extremidade interno e failover de ponto de extremidade automático do Gerenciador de Tráfego do Azure. Eles ajudarão você a fornecer aplicativos de alta disponibilidade e resilientes a falhas de ponto de extremidade e região do Azure. Será necessário especificar determinadas configurações no perfil do Gerenciador de Tráfego para configurar o monitoramento de ponto de extremidade.

Colete insights sobre eventos no nível da assinatura do Log de atividades, um log de plataforma do Azure. Isso inclui informações sobre a alteração de um recurso ou a inicialização de uma máquina virtual. Veja o Log de atividades no portal do Azure ou recupere entradas usando o PowerShell e a CLI. 

Crie uma configuração de diagnóstico para enviar o Log de atividades ao Azure Monitor e aos Hubs de Eventos do Azure a fim de encaminhar informações ao Armazenamento do Azure ou fora do Azure para arquivamento. Configure o Azure Monitor para receber alertas de notificação quando recursos críticos forem alterados em Serviços de Nuvem do Azure. 

- [Log de Atividades do Azure](../azure-monitor/essentials/activity-log.md)

- [Criar, exibir e gerenciar alertas do log de atividades usando o Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

- [Monitoramento do Gerenciador de Tráfego](../traffic-manager/traffic-manager-monitoring.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, confira [Azure Security Benchmark: log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usar fontes de sincronização de tempo aprovadas

**Diretrizes**: a Microsoft mantém fontes de tempo de recursos do Azure para Serviços de Nuvem do Azure. Talvez os clientes precisem criar uma regra de rede para permitir o acesso a um servidor de horário usado no ambiente por meio da porta 123 com o protocolo UDP.

- [Acesso ao servidor NTP](../firewall/protect-windows-virtual-desktop.md#additional-considerations)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar o gerenciamento central de log de segurança

**Diretrizes**: consuma dados de streaming do serviço de nuvem de modo programático usando os Hubs de Eventos do Azure. Integre esses dados e os envie ao Azure Sentinel para monitorar e examinar seus logs. Como alternativa, use um SIEM de terceiros. Para executar o gerenciamento de logs de segurança, configure a exportação contínua dos dados escolhidos na Central de Segurança do Azure nos Hubs de Eventos do Azure, bem como configure o conector apropriado para o SIEM. Veja algumas opções do Azure Sentinel, incluindo ferramentas de terceiros:

- Azure Sentinel – Use o conector nativo de dados de alertas da Central de Segurança
- Splunk – Use o complemento do Azure Monitor para o Splunk
- IBM QRadar – Use uma origem do log configurada de modo manual
- ArcSight – Use o SmartConnector

Examine a documentação do Azure Sentinel para obter detalhes adicionais sobre conectores disponíveis do Azure Sentinel. 

- [Conectar fontes de dados](../sentinel/connect-data-sources.md)

- [Integração com um SIEM](../security-center/continuous-export.md)

- [Armazenar dados de diagnóstico](diagnostics-extension-to-storage.md)

- [Como configurar a integração SIEM por meio dos Hubs de Eventos do Azure](../security-center/continuous-export.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Diretrizes**: configure o Visual Studio a fim de configurar o Diagnóstico do Azure para executar a solução de problemas de Serviços de Nuvem do Azure que capturam dados de sistemas e logs em máquinas virtuais, incluindo instâncias de máquinas virtuais que executam Serviços de Nuvem do Azure. Os dados de Diagnóstico serão transferidos para uma conta de armazenamento de sua preferência. Ative o diagnóstico nos projetos de Serviços de Nuvem do Azure antes da implantação.

 
Veja o histórico de Alterações de alguns eventos no log de atividades dentro do Azure Monitor. Execute uma auditoria de quais alterações ocorreram durante um período do evento. Escolha um evento no Log de Atividades para executar uma inspeção mais detalhada usando a guia Histórico de alterações (Versão Prévia). Envie os dados de diagnóstico ao Application Insights durante a publicação de Serviços de Nuvem do Azure do Visual Studio. Crie um recurso do Azure do Application Insights a qualquer momento ou envie os dados a um recurso existente do Azure. 

Os Serviços de Nuvem do Azure podem ser monitorados pelo Application Insights para obter uma análise da disponibilidade, do desempenho, de falhas e do uso. É possível adicionar gráficos personalizados ao Application Insights, para que você veja os dados mais importantes. Os dados da instância de função podem ser coletados usando o SDK do Application Insights em seu projeto de Serviços de Nuvem do Azure. 

- [Ative o diagnóstico no Visual Studio antes da implantação](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#to-turn-on-diagnostics-in-visual-studio-before-deployment)

- [Exibir histórico de alterações](../azure-monitor/essentials/activity-log.md#view-change-history)

- [Application Insights para o Serviço de Nuvem do Azure (Clássico)](../azure-monitor/app/cloudservices.md)

- [Configurar o diagnóstico para o Serviço de Nuvem (Clássico) e as máquinas virtuais do Azure](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretrizes**: é possível usar um monitoramento avançado com Serviços de Nuvem do Azure. Isso permite coletar uma amostra de métricas adicionais em intervalos de 5 minutos, 1 hora e 12 horas. Os dados agregados são armazenados em uma conta de armazenamento, bem como em tabelas, depois são excluídos após 10 dias. No entanto, a conta de armazenamento usada é configurada pela função. Além disso, é possível usar diferentes contas de armazenamento em funções distintas. Isso é configurado com uma cadeia de conexão nos arquivos .csdef e .cscfg.

Observe que o monitoramento avançado envolve usar uma extensão do Diagnóstico do Azure (o SDK do Application Insights é opcional) na função que você deseja monitorar. A extensão de diagnóstico usa um arquivo de configuração (por função) chamado diagnostics.wadcfgx para configurar as métricas de diagnóstico monitoradas. A extensão Diagnóstico do Azure coleta e armazena dados em uma conta de Armazenamento do Azure. Essas configurações são definidas nos arquivos .wadcfgx, .csdef e .cscfg.

- [Introdução ao Monitoramento do Serviço de Nuvem](cloud-services-how-to-monitor.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: monitorar e revisar logs

**Diretrizes**: modos de monitoramento básico ou avançado estão disponíveis em Serviços de Nuvem do Azure. Os Serviços de Nuvem do Azure coletam de modo automático dados básicos de monitoramento (percentual de CPU, entrada/saída de rede e leitura/gravação de disco) de uma máquina virtual do host. Veja os dados de monitoramento coletados nas páginas de visão geral e métricas do serviço de nuvem no portal do Azure. 

Habilite o diagnóstico em Serviços de Nuvem do Azure ao usar a extensão do Diagnóstico do Azure para coletar dados de diagnóstico, como logs de aplicativo, contadores de desempenho e muito mais. Habilite ou atualize a configuração de diagnóstico em um serviço de nuvem que já esteja em execução usando o cmdlet Set-AzureServiceDiagnosticsExtension ou implante um serviço de nuvem com a extensão de diagnóstico de modo automático. Como alternativa, instale o SDK do Application Insights. Envie contadores de desempenho ao Azure Monitor.

A extensão Diagnóstico do Azure coleta e armazena dados em uma conta de Armazenamento do Azure. Transfira os dados de Diagnóstico para o Emulador de Armazenamento do Microsoft Azure ou o Armazenamento do Azure, já que eles não serão armazenados de modo permanente. Após ser armazenamento, os dados poderão ser vistos usando uma das várias ferramentas disponíveis, como o Gerenciador de Servidores no Visual Studio, o Gerenciador de Armazenamento do Microsoft Azure e o Management Studio do Azure. Configure as métricas de diagnóstico a serem monitoradas usando um arquivo de configuração (por função) chamado diagnostics.wadcfgx na extensão de diagnóstico. 

- [Introdução ao Monitoramento do Serviço de Nuvem](cloud-services-how-to-monitor.md)

- [Como habilitar o diagnóstico em uma função de trabalho – Integração com um SIEM](../security-center/continuous-export.md)

- [Habilitar o diagnóstico nos Serviços de Nuvem do Azure usando o PowerShell](cloud-services-diagnostics-powershell.md)

- [Armazenar e exibir dados de diagnóstico no Armazenamento do Azure](./diagnostics-extension-to-storage.md?preserve-view=)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Habilitar alertas sobre atividades anormais

**Diretrizes**: é possível monitorar dados de logs de Serviços de Nuvem do Azure por meio de uma integração com o Azure Sentinel ou com um SIEM de terceiros habilitando o alerta para atividades anômalas.

- [Integração com um SIEM](../security-center/continuous-export.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware

**Diretrizes**: o Microsoft Antimalware para Azure protege máquinas virtuais e Serviços de Nuvem do Azure. Opte por implantar soluções adicionais de segurança de terceiros, como firewalls de aplicativos Web, firewalls de redes, antimalware, IDS ou IPS (sistemas de detecção e prevenção de intrusões) e muito mais.

- [Quais são os recursos e as funcionalidades que o DDOS e o IPS/IDS básico do Azure oferecem?](./cloud-services-configuration-and-management-faq.md#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, confira [Azure Security Benchmark: controle de acesso e identidade](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Diretrizes**: a Microsoft recomenda gerenciar o acesso aos recursos do Azure usando o RBAC do Azure (controle de acesso baseado em função do Azure). No entanto, os Serviços de Nuvem do Azure não são compatíveis com o modelo RBAC do Azure, pois ele não é um serviço baseado no Azure Resource Manager, além disso, é necessário usar uma assinatura clássica

Por padrão, há três funções de administrador da assinatura clássica no Azure: o Administrador da Conta, o Administrador de Serviços e o Coadministrador. 

Os administradores de assinatura clássicos têm acesso total à assinatura do Azure. Eles podem gerenciar recursos usando o portal do Azure, as APIs do Azure Resource Manager e as APIs do modelo de implantação clássico. A conta que é usada para se inscrever no Azure é definida automaticamente como o Administrador da Conta e o Administrador de Serviços. Será possível adicionar Coadministradores posteriormente. 

O Administrador de Serviços, os Coadministradores e os usuários que receberam a função de Proprietário (uma função do Azure) têm um acesso equivalente no escopo da assinatura. Gerencie os Coadministradores ou veja o Administrador de Serviços usando a guia de Administradores clássicos no portal do Azure. 

Liste as atribuições de função do administrador de serviços e de coadministradores clássicos com o PowerShell usando o comando:

Get-AzRoleAssignment -IncludeClassicAdministrators

Examine as diferenças entre as funções administrativas da assinatura clássica. 

- [Diferenças entre três funções administrativas da assinatura clássica](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretrizes**: recomendamos criar procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas, com base em funções disponíveis e permissões necessárias para operar e gerenciar recursos de Serviços de Nuvem do Azure.

- [Diferenças entre as funções administrativas da assinatura clássica](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usar o SSO (logon único) com o Azure Active Directory

**Diretrizes**: evite gerenciar identidades separadas para aplicativos em execução nos Serviços de Nuvem do Azure. Implemente um logon único para evitar exigir que os usuários gerenciem várias identidades e credenciais.

- [O que é o SSO (logon único)?](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: recomendamos usar uma estação de trabalho segura e gerenciada pelo Azure (também conhecida como uma Estação de Trabalho com Acesso Privilegiado) para executar tarefas administrativas que exijam privilégios elevados.

- [Entender de que modo funcionam as estações de trabalho seguras e gerenciadas pelo Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como habilitar a autenticação multifator do Azure AD (Azure Active Directory)](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretrizes**: use APIs REST do Serviço de Nuvem do Azure para inventariar recursos do Serviço de Nuvem do Azure a fim de obter informações confidenciais. Execute uma pesquisa em recursos implantados do serviço de nuvem para obter a configuração e os recursos de arquivos .pkg.

 Algumas APIs estão listadas abaixo como exemplo:

- Obter Implantação – A operação Obter Implantação retorna informações de configuração, status e propriedades do sistema para executar uma implantação.
- Obter Pacote – A operação Obter Pacote recupera um pacote de serviços de nuvem de uma implantação e armazena os arquivos do pacote no Armazenamento de Blobs do Microsoft Azure
- Obter Propriedades do Serviço de Nuvem – A operação Obter Propriedades de Serviço de Nuvem recupera propriedades de um serviço de nuvem específico

Examine a documentação de APIs REST do Serviço de Nuvem do Azure e crie um processo para executar a proteção de dados de informações confidenciais com base em seus requisitos organizacionais.

- [Obter implantação](/rest/api/compute/cloudservices/rest-get-deployment)

- [Obter propriedades do serviço de nuvem](/rest/api/compute/cloudservices/rest-get-cloud-service-properties)

- [Obter pacote](/rest/api/compute/cloudservices/rest-get-package)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: implemente um isolamento usando assinaturas e grupos de gerenciamento separados para obter domínios de segurança individuais, como o tipo de ambiente e o nível de confidencialidade de dados para Serviços de Nuvem do Azure.

Também é possível editar o "permissionLevel" no elemento do Certificado do Serviço de Nuvem do Azure para especificar as permissões de acesso concedidas aos processos de função. Caso deseje que somente processos com privilégios elevados possam acessar a chave privada, especifique a permissão elevada. A permissão limitedOrElevated permite que todos os processos de função acessem a chave privada. Os valores possíveis são limitedOrElevated ou elevado. O valor padrão é o limitedOrElevated.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Esquema WebRole](./schema-csdef-webrole.md#Certificate)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Diretrizes**: recomendamos usar uma solução de terceiros do Azure Marketplace em perímetros de rede para monitorar e bloquear a transferência não autorizada de informações confidenciais, alertando os profissionais de segurança da informação.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: configurar o TLS v2 para Serviços de Nuvem do Azure. Use o portal do Azure para adicionar o certificado à implantação de teste de Serviços de Nuvem do Azure, bem como incluir informações do certificado nos arquivos CSDEF e CSCFG dos serviços. Empacote novamente seu aplicativo e atualize a implantação de teste para usar o novo pacote. 

No Azure, use certificados de serviço que serão anexados a Serviços de Nuvem do Azure para estabelecer uma comunicação segura com o serviço. Forneça um certificado que possa autenticar um ponto de extremidade HTTPS exposto. Defina os Certificados de serviço na definição de serviço do serviço de nuvem e implante-os de modo automático na Máquina Virtual, executando uma instância de sua função.

Executar uma autenticação usando determinada API de gerenciamento com certificados de gerenciamento permite executar uma autenticação com o modelo de implantação clássico. Muitos programas e ferramentas (como o Visual Studio ou o SDK do Azure) usam esses certificados para automatizar a configuração e a implantação de diversos serviços do Azure. 

Para referência adicional, uma API do modelo de implantação clássico fornece acesso programático à funcionalidade do modelo de implantação clássico disponível no portal do Azure. O SDK do Azure para Python pode ser usado para gerenciar Serviços de Nuvem do Azure e Contas de Armazenamento do Azure. O SDK do Azure para Python encapsula uma API REST, API do modelo de implantação clássico. Todas as operações da API são executadas por meio do TLS e autenticadas respectivamente usando certificados X.509 v3. O serviço de gerenciamento pode ser acessado dentro de um serviço em execução no Azure. Ele também pode ser acessado diretamente pela Internet em qualquer aplicativo que possa enviar uma solicitação HTTPS e receber uma resposta HTTPS.

- [Configurar o TLS para um aplicativo no Azure](cloud-services-configure-ssl-certificate-portal.md)

- [Usar o modelo de implantação clássico do Python](cloud-services-python-how-to-use-service-management.md)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: recomendamos usar uma ferramenta de descoberta ativa de terceiros para identificar todas as informações confidenciais armazenadas e processadas nos sistemas de tecnologia da organização ou transmitidas por eles, incluindo as informações localizadas no local ou em um provedor de serviço remoto, depois atualizar o inventário de informações confidenciais da organização.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Diretrizes**: não aplicável ao Serviço de Nuvem (Clássico). Ele não impõe a prevenção contra perda de dados.

Recomendamos implementar uma ferramenta de terceiros, como uma solução automatizada de prevenção contra perda de dados baseada em host para impor controles de acesso aos dados, mesmo quando eles forem copiados de um sistema.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente permaneçam seguros no Azure, a Microsoft implementou um conjunto de funcionalidades e controles robustos de proteção de dados. Além disso, a empresa executa a manutenção desse conjunto.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografar informações confidenciais em repouso

**Diretrizes**: os Serviços de Nuvem do Azure não são compatíveis com a criptografia em repouso. Isso ocorre porque os Serviços de Nuvem do Azure foram projetados como um serviço sem estado. Os Serviços de Nuvem do Azure são compatíveis com o armazenamento externo, por exemplo, o Armazenamento do Azure, que é criptografado em repouso por padrão.  

Os dados do aplicativo armazenados em discos temporários não são criptografados. O cliente é responsável pelo gerenciamento e pela criptografia desses dados, conforme necessário.  

- [Noções básicas sobre a criptografia em repouso do Azure](../security/fundamentals/encryption-atrest.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: é possível usar alertas de métrica clássicos no Azure Monitor para ser notificado quando uma de suas métricas aplicadas a recursos críticos ultrapassar um limite. Os alertas de métrica clássicos são uma funcionalidade anterior que permite receber alertas somente em métricas não dimensionais. Há uma funcionalidade mais recente chamada Alertas de métrica, que aprimorou a funcionalidade em relação aos alertas de métrica clássicos. 

Além disso, o Application Insights pode monitorar aplicativos de Serviços de Nuvem do Azure para analisar a disponibilidade, o desempenho, o uso e falhas. Ele usa dados combinados de SDKs do Application Insights com os dados de Diagnóstico do Azure de Serviços de Nuvem do Azure.

- [Criar, exibir e gerenciar alertas de métrica clássicos usando o Azure Monitor](../azure-monitor/alerts/alerts-classic-portal.md)

- [Visão geral de Alertas de métrica](../azure-monitor/alerts/alerts-metric-overview.md) 

- [Application Insights para o Serviço de Nuvem do Azure (Clássico)](../azure-monitor/app/cloudservices.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, confira [Azure Security Benchmark: gerenciamento de vulnerabilidades](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: implantar solução automatizada de gerenciamento de patch de sistema operacional

**Diretrizes**: observe que essas informações estão relacionadas ao sistema operacional convidado do Azure para executar funções Web e de trabalho de Serviços de Nuvem do Azure com PaaS (Plataforma como Serviço). No entanto, elas não se aplicam às Máquinas Virtuais com IaaS (Infraestrutura como Serviço).

Por padrão, o Azure atualiza o sistema operacional convidado do cliente de modo periódico para obter a imagem mais recente e compatível com a família do sistema operacional indicado na configuração de serviço (.cscfg), como o Windows Server 2016.

Quando um cliente escolhe uma versão específica do sistema operacional para executar uma implantação de Serviços de Nuvem do Azure, ele desabilita as atualizações automáticas do sistema operacional, bem como se responsabiliza pela aplicação de patch. O cliente deverá garantir que as instâncias de função estejam recebendo atualizações ou elas poderão expor o aplicativo a vulnerabilidades de segurança.

- [Sistema operacional convidado do Azure](cloud-services-guestos-msrc-releases.md)

- [Capacidade de suporte e política de desativação do SO convidado do Azure](cloud-services-guestos-retirement-policy.md)

- [Como configurar o Serviço de Nuvem (Clássico)](cloud-services-how-to-configure-portal.md)

- [Gerenciar versão do SO Convidado](./cloud-services-how-to-configure-portal.md#manage-guest-os-version)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: implantar uma solução automatizada de gerenciamento de patch em títulos de software de terceiros

**Diretrizes**: use uma solução de gerenciamento de patches de terceiros. Os clientes que já usam o Configuration Manager no ambiente também podem usar o System Center Updates Publisher. Isso permite publicar atualizações personalizadas no Serviço de Atualização do Windows Server. 

Esse cenário permite que o Gerenciamento de Atualizações aplique patch em computadores que usam o Configuration Manager como repositório de atualização com um software de terceiros.

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretrizes**: Recomendamos que os clientes entendam o escopo do risco de um DDoS ocorrendo de modo contínuo. 

Sugerimos refletir sobre estes cenários:

- Quais novos recursos do Azure disponíveis publicamente precisam de proteção?
- Há um ponto único de falha no serviço?
- Como os serviços podem ser isolados para limitar o impacto de um ataque e ainda disponibilizá-los para os clientes válidos?
- Há redes virtuais em que a Proteção contra DDoS Standard deve ser habilitada, mas ainda não está?
- Meu serviços são ativo/ativo com failover em várias regiões?

Documentação de suporte:

- [Avaliação de risco dos seus recursos do Azure](../security/fundamentals/ddos-best-practices.md#risk-evaluation-of-your-azure-resources)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, confira [Azure Security Benchmark: gerenciamento de inventário e ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usar uma solução de descoberta de ativos automatizada

**Diretrizes**: não aplicável a Serviços de Nuvem do Azure. Essa recomendação é aplicável a recursos de computação de IaaS.

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Excluir recursos do Azure não autorizados

**Diretrizes**: recomendamos reconciliar o inventário com regularidade e garantir que os recursos não autorizados sejam excluídos da assinatura de modo oportuno.

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: definir e manter um inventário de recursos aprovados do Azure

**Diretrizes**: o cliente deverá configurar os recursos aprovados do Azure e o software aprovado para obter recursos de computação.

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorar recursos do Azure não aprovados

**Diretrizes**: use o recurso de Controle de Aplicativos Adaptáveis, disponível na Central de Segurança do Azure. Ele é uma solução de ponta a ponta, automatizada e inteligente da Central de Segurança que ajuda a controlar quais aplicativos podem ser executados em computadores Windows e Linux, bem como Azure e não Azure. Ele também ajuda a proteger seus computadores contra malware. 

Esse recurso está disponível para computadores Azure e não Azure, bem como Windows e Linux (todas as versões: clássicas ou do Azure Resource Manager).

A Central de Segurança usa o machine learning para analisar aplicativos em execução nos computadores, bem como cria uma lista de permitidos dessa inteligência. Essa funcionalidade simplifica bastante o processo de configuração e manutenção de políticas da lista de permitidos do aplicativo. Além disso, ela permite:
- Bloquear ou alertar sobre tentativas de executar aplicativos mal-intencionados, incluindo aqueles que, de outra forma, poderiam ser perdidos por soluções antimalware.

- Cumpra a política de segurança da sua organização que impõe o uso somente de software licenciado.
- Evite o uso de softwares indesejados em seu ambiente.
- Evite a execução de aplicativos antigos e sem suporte.
- Impeça o uso de ferramentas de software específicas que não são permitidas em sua organização.
- Habilite a TI a controlar o acesso a dados confidenciais pelo uso do aplicativo.

Mais detalhes estão disponíveis nos links de referência.

- [Controles de aplicativo adaptáveis](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorar aplicativos de software não aprovados nos recursos de computação

**Diretrizes**: use o recurso de Controle de Aplicativos Adaptáveis, disponível na Central de Segurança do Azure. Ele é uma solução de ponta a ponta, automatizada e inteligente da Central de Segurança que ajuda a controlar quais aplicativos podem ser executados em computadores Windows e Linux, bem como Azure e não Azure. Ele também ajuda a proteger seus computadores contra malware. 

Esse recurso está disponível para computadores Azure e não Azure, bem como Windows e Linux (todas as versões: clássicas ou do Azure Resource Manager).

A Central de Segurança usa o machine learning para analisar aplicativos em execução nos computadores, bem como cria uma lista de permitidos dessa inteligência. Essa funcionalidade simplifica bastante o processo de configuração e manutenção de políticas da lista de permitidos do aplicativo. Além disso, ela permite:

- Bloquear ou alertar sobre tentativas de executar aplicativos mal-intencionados, incluindo aqueles que, de outra forma, poderiam ser perdidos por soluções antimalware.

- Cumpra a política de segurança da sua organização que impõe o uso somente de software licenciado.

- Evite o uso de softwares indesejados em seu ambiente.

- Evite a execução de aplicativos antigos e sem suporte.

- Impeça o uso de ferramentas de software específicas que não são permitidas em sua organização.

- Habilite a TI a controlar o acesso a dados confidenciais pelo uso do aplicativo.

Mais detalhes estão disponíveis nos links de referência.

- [Controles de aplicativo adaptáveis](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Diretrizes**: use o recurso de Controle de Aplicativos Adaptáveis, disponível na Central de Segurança do Azure. Ele é uma solução de ponta a ponta, automatizada e inteligente da Central de Segurança que ajuda a controlar quais aplicativos podem ser executados em computadores Windows e Linux, bem como Azure e não Azure. Ele também ajuda a proteger seus computadores contra malware. 

Esse recurso está disponível para computadores Azure e não Azure, bem como Windows e Linux (todas as versões: clássicas ou do Azure Resource Manager).

A Central de Segurança usa o machine learning para analisar aplicativos em execução nos computadores, bem como cria uma lista de permitidos dessa inteligência. Essa funcionalidade simplifica bastante o processo de configuração e manutenção de políticas da lista de permitidos do aplicativo. Além disso, ela permite:

- Bloquear ou alertar sobre tentativas de executar aplicativos mal-intencionados, incluindo aqueles que, de outra forma, poderiam ser perdidos por soluções antimalware.

- Cumpra a política de segurança da sua organização que impõe o uso somente de software licenciado.

- Evite o uso de softwares indesejados em seu ambiente.

- Evite a execução de aplicativos antigos e sem suporte.

- Impeça o uso de ferramentas de software específicas que não são permitidas em sua organização.

- Habilite a TI a controlar o acesso a dados confidenciais pelo uso do aplicativo.

Mais detalhes estão disponíveis nos links de referência.

- [Controles de aplicativo adaptáveis](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="68-use-only-approved-applications"></a>6.8: Usar somente aplicativos aprovados

**Diretrizes**: use o recurso de Controle de Aplicativos Adaptáveis, disponível na Central de Segurança do Azure. Ele é uma solução de ponta a ponta, automatizada e inteligente da Central de Segurança que ajuda a controlar quais aplicativos podem ser executados em computadores Windows e Linux, bem como Azure e não Azure. Ele também ajuda a proteger seus computadores contra malware. 

Esse recurso está disponível para computadores Azure e não Azure, bem como Windows e Linux (todas as versões: clássicas ou do Azure Resource Manager).

A Central de Segurança usa o machine learning para analisar aplicativos em execução nos computadores, bem como cria uma lista de permitidos dessa inteligência. Essa funcionalidade simplifica bastante o processo de configuração e manutenção de políticas da lista de permitidos do aplicativo. Além disso, ela permite:

- Bloquear ou alertar sobre tentativas de executar aplicativos mal-intencionados, incluindo aqueles que, de outra forma, poderiam ser perdidos por soluções antimalware.

- Cumpra a política de segurança da sua organização que impõe o uso somente de software licenciado.

- Evite o uso de softwares indesejados em seu ambiente.

- Evite a execução de aplicativos antigos e sem suporte.

- Impeça o uso de ferramentas de software específicas que não são permitidas em sua organização.

- Habilite a TI a controlar o acesso a dados confidenciais pelo uso do aplicativo.

Mais detalhes estão disponíveis nos links de referência.

- [Controles de aplicativo adaptáveis](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Manter um inventário de nomes de software aprovados

**Diretrizes**: use o recurso de Controle de Aplicativos Adaptáveis, disponível na Central de Segurança do Azure. Ele é uma solução de ponta a ponta, automatizada e inteligente da Central de Segurança que ajuda a controlar quais aplicativos podem ser executados em computadores Windows e Linux, bem como Azure e não Azure. Ele também ajuda a proteger seus computadores contra malware. 

Esse recurso está disponível para computadores Azure e não Azure, bem como Windows e Linux (todas as versões: clássicas ou do Azure Resource Manager).

A Central de Segurança usa o machine learning para analisar aplicativos em execução nos computadores, bem como cria uma lista de permitidos dessa inteligência. Essa funcionalidade simplifica bastante o processo de configuração e manutenção de políticas da lista de permitidos do aplicativo. Além disso, ela permite:
- Bloquear ou alertar sobre tentativas de executar aplicativos mal-intencionados, incluindo aqueles que, de outra forma, poderiam ser perdidos por soluções antimalware.

- Cumpra a política de segurança da sua organização que impõe o uso somente de software licenciado.
- Evite o uso de softwares indesejados em seu ambiente.
- Evite a execução de aplicativos antigos e sem suporte.
- Impeça o uso de ferramentas de software específicas que não são permitidas em sua organização.
- Habilite a TI a controlar o acesso a dados confidenciais pelo uso do aplicativo.

Mais detalhes estão disponíveis nos links de referência.

- [Controles de aplicativo adaptáveis](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: limitar a capacidade dos usuários de executar scripts em recursos de computação

**Diretrizes**: use o recurso de Controle de Aplicativos Adaptáveis, disponível na Central de Segurança do Azure. Ele é uma solução de ponta a ponta, automatizada e inteligente da Central de Segurança que ajuda a controlar quais aplicativos podem ser executados em computadores Windows e Linux, bem como Azure e não Azure. Ele também ajuda a proteger seus computadores contra malware. 

Esse recurso está disponível para computadores Azure e não Azure, bem como Windows e Linux (todas as versões: clássicas ou do Azure Resource Manager).

A Central de Segurança usa o machine learning para analisar aplicativos em execução nos computadores, bem como cria uma lista de permitidos dessa inteligência. Essa funcionalidade simplifica bastante o processo de configuração e manutenção de políticas da lista de permitidos do aplicativo. Além disso, ela permite:

- Bloquear ou alertar sobre tentativas de executar aplicativos mal-intencionados, incluindo aqueles que, de outra forma, poderiam ser perdidos por soluções antimalware.

- Cumpra a política de segurança da sua organização que impõe o uso somente de software licenciado.

- Evite o uso de softwares indesejados em seu ambiente.

- Evite a execução de aplicativos antigos e sem suporte.

- Impeça o uso de ferramentas de software específicas que não são permitidas em sua organização.

- Habilite a TI a controlar o acesso a dados confidenciais pelo uso do aplicativo.

Mais detalhes estão disponíveis nos links de referência.

- [Controles de aplicativo adaptáveis](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separar física ou logicamente os aplicativos de alto risco

**Diretrizes**: implemente assinaturas ou grupos de gerenciamento separados a fim de fornecer isolamento para aplicativos confidenciais ou de alto risco usando Serviços de Nuvem do Azure.

Use um grupo de segurança de rede, crie uma regra de segurança de entrada, escolha um serviço como HTTP, escolha uma porta personalizada, bem como atribua uma prioridade e um nome a ela. A prioridade afetará a ordem em que as regras serão aplicadas: quanto menor for o valor numérico, mais cedo a regra será aplicada. Será preciso associar seu grupo de segurança de rede a uma sub-rede ou adaptador de rede específico para isolar ou segmentar o tráfego de rede com base em suas necessidades de negócios.

Mais detalhes estão disponíveis nos links de referência.

- [Tutorial – Filtrar o tráfego de rede com um grupo de segurança de rede usando o portal do Azure](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, confira [Azure Security Benchmark: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: use as recomendações da Central de Segurança do Azure como uma linha de base de configuração segura para recursos de Serviços de Nuvem do Azure. 

No portal do Azure, escolha a opção Central de Segurança, depois Computação &amp; aplicativos e Serviços de Nuvem do Azure para conferir recomendações aplicáveis aos seus recursos de serviço.

- [Recomendações de segurança – um guia de referência](../security-center/recommendations-reference.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Diretrizes**: não aplicável a Serviços de Nuvem do Azure. Ele é baseado no modelo de implantação clássico. Recomendamos usar uma solução de terceiros para manter a segurança das configurações de recursos do Azure

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar configuração de recursos do Azure com segurança

**Diretrizes**: o arquivo de configuração do Serviço de Nuvem do Azure armazena os atributos operacionais de um recurso. É possível armazenar uma cópia dos arquivos de configuração em uma conta de armazenamento segura.

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implantar ferramentas de gerenciamento de configuração para recursos do Azure

**Diretrizes**: não aplicável a Serviços de Nuvem do Azure. Ele é baseado no modelo de implantação clássico e não pode ser gerenciado por ferramentas de configuração baseadas na implantação do Azure Resource Manager.

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Implantar ferramentas de gerenciamento de configuração para sistemas operacionais

**Diretrizes**: não aplicável a Serviços de Nuvem do Azure. Essa recomendação é aplicável aos recursos de computação baseados em IaaS (Infraestrutura como Serviço).

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar o monitoramento automatizado de configuração para recursos do Azure

**Diretrizes**: use a Central de Segurança do Azure para executar verificações de linha de base em recursos do Azure.  

- [De que modo corrigir as recomendações da Central de Segurança do Azure](../security-center/security-center-remediate-recommendations.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitoramento automatizado de configuração para sistemas operacionais

**Diretrizes**: na Central de Segurança do Azure, escolha o recurso de Computação &amp; Aplicativos, depois siga as recomendações para analisar máquinas virtuais, servidores e contêineres.

- [Entenda as recomendações do contêiner da Central de Segurança do Azure](../security-center/container-security.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Diretrizes**: os Serviços de Nuvem do Azure são baseados em um modelo de implantação clássico. Além disso, eles não se integram ao Azure Key Vault.

É possível proteger segredos, como credenciais usadas em Serviços de Nuvem do Azure, para que não seja necessário digitar uma senha todas as vezes. Para começar, especifique uma senha de texto sem formatação, depois converta essa senha em uma cadeia de caracteres segura usando o comando ConvertTo-SecureString do PowerShell. Depois, converta essa cadeia de caracteres segura em uma cadeia de caracteres padrão e criptografada usando o comando ConvertFrom-SecureString.  Agora é possível salvar essa cadeia de caracteres padrão e criptografada em um arquivo usando o comando Set-Content.

Além disso, recomendamos armazenar as chaves privadas de certificados usados nos Serviços de Nuvem do Azure em um armazenamento protegido.

- [Configurar a Área de Trabalho Remota por meio do PowerShell](./cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretrizes**: proteja segredos, como credenciais usadas em Serviços de Nuvem do Azure, para que não seja necessário digitar uma senha todas as vezes. 
 

Para começar, especifique uma senha de texto sem formatação, depois altere essa senha para obter uma cadeia de caracteres segura usando o comando ConvertTo-SecureString do PowerShell. Depois, converta essa cadeia de caracteres segura em uma cadeia de caracteres padrão e criptografada usando o comando ConvertFrom-SecureString. Agora salve essa cadeia de caracteres padrão e criptografada em um arquivo usando o comando Set-Content.

Armazene as chaves privadas dos certificados usados nos Serviços de Nuvem do Azure em um local de armazenamento seguro.

- [Configurar a Área de Trabalho Remota por meio do PowerShell](./cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, confira [Azure Security Benchmark: defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: usar um software antimalware gerenciado de modo centralizado

**Diretrizes**: o Microsoft Antimalware para Azure está disponível para Serviços de Nuvem do Azure e Máquinas Virtuais. Ele é uma proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares maliciosos. Ele gera alertas quando um software mal-intencionado ou indesejado conhecido tenta se instalar ou executar nos sistemas do Azure. 

Use o cmdlet do Antimalware baseado no PowerShell para obter a configuração do Antimalware usando o comando "Get-AzureServiceAntimalwareConfig".

Habilite a extensão do Antimalware com um script do PowerShell na Tarefa de Inicialização de Serviços de Nuvem do Azure.

Escolha o recurso de Controle de aplicativos adaptáveis, uma solução de ponta a ponta, automatizada e inteligente da Central de Segurança do Azure. Ele ajudará a proteger seus computadores contra malware e permitirá que você receba alertas ou bloqueie tentativas de execução de aplicativos maliciosos, incluindo tentativas que poderão passar desapercebidas por soluções antimalware.

- [Como adicionar uma extensão Antimalware aos meus Serviços de Nuvem do Azure de maneira automatizada](./cloud-services-configuration-and-management-faq.md#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)

- [Cenários de implantação de antimalware](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios)

- [Controles de aplicativo adaptáveis](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

- [Como configurar automações de fluxo de trabalho na Central de Segurança do Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [O cliente também poderá aproveitar o guia sobre como lidar com incidentes de segurança do computador do NIST para ajudar na criação de um plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretrizes**: a Central de Segurança do Azure atribui uma gravidade a cada alerta para ajudar você a priorizar os alertas que deverão ser investigados primeiro. A severidade é baseada no grau de confiança da Central de Segurança nas conclusões ou na análise usada para emitir o alerta, bem como no nível de confiança em uma possível ação mal-intencionada por trás da atividade que gerou o alerta. 

Marque as assinaturas de modo evidente (por exemplo: produção ou não produção) e crie um sistema de nomeação para identificar e categorizar recursos do Azure com clareza.

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Diretriz**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário. 

- [Veja a publicação do NIST: Guia para testar, treinar e exercitar programas para planos de TI e recursos](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: as informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que os dados do cliente foram acessados por uma pessoa não autorizada ou ilegal. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos. 

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretriz**: exporte os alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Será possível usar o conector de dados da Central de Segurança a fim de transmitir alertas para o Azure Sentinel. 

- [Como configurar a exportação contínua](../security-center/continuous-export.md) 

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na Central de Segurança do Azure para disparar automaticamente respostas por meio de "Aplicativos Lógicos" em alertas de segurança e recomendações.

- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, confira [Azure Security Benchmark: testes de penetração e exercícios de Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes de penetração regulares dos recursos do Azure e garantir a correção de todas as conclusões de segurança críticas

**Diretrizes**: siga as Regras de participação para testes de penetração do Microsoft Cloud para verificar se os testes de penetração não estão violando as políticas da Microsoft. Use a estratégia da Microsoft, a execução de Equipes Vermelhas e os testes de penetração de sites online na infraestrutura, nos serviços e nos aplicativos de nuvem gerenciados pela Microsoft. 

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)
