---
title: Linha de base de segurança do Azure para serviços de nuvem do Azure
description: A linha de base de segurança dos serviços de nuvem do Azure fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: cloud-services
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 105fd185900692669fdc40e0c47a03474524e250
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731095"
---
# <a name="azure-security-baseline-for-azure-cloud-services"></a>Linha de base de segurança do Azure para serviços de nuvem do Azure

Essa linha de base de segurança aplica as diretrizes da [1,0 versão de avaliação de segurança do Azure](../security/benchmarks/overview-v1.md) para serviços de nuvem do Microsoft Azure. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure.
O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis aos serviços de nuvem. Os **controles** não aplicáveis aos serviços de nuvem foram excluídos.

 
Para ver como os serviços de nuvem são totalmente mapeados para o benchmark de segurança do Azure, consulte o [arquivo de mapeamento de linha de base de segurança dos serviços de nuvem](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Diretrizes**: Crie uma rede virtual clássica do Azure com sub-redes públicas e privadas separadas para impor o isolamento com base em portas confiáveis e intervalos de IP. Essas sub-redes e redes virtuais devem ser os recursos baseados na rede virtual clássica (implantação clássica) e não os recursos de Azure Resource Manager atuais.  

Permitir ou negar o tráfego usando um grupo de segurança de rede, que contém regras de controle de acesso com base na direção do tráfego, no protocolo, no endereço de origem e na porta e no endereço de destino e na porta. As regras de um grupo de segurança de rede podem ser alteradas a qualquer momento e as alterações são aplicadas a todas as instâncias associadas.

Serviços de Nuvem do Microsoft Azure (clássico) não pode ser colocado em Azure Resource Manager redes virtuais. No entanto, as redes virtuais baseadas no Gerenciador de recursos e as redes virtuais clássicas baseadas em implantação podem ser conectadas por meio de emparelhamento. 

- [Visão geral do Grupo de Segurança de Rede](../virtual-network/network-security-groups-overview.md)

- [Emparelhamento de rede virtual](https://docs.microsoft.com/azure/cloud-services/cloud-services-connectivity-and-networking-faq#how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e NICs

**Diretrizes**: Documente sua configuração de serviços de nuvem do Azure e monitore-as quanto a alterações. Use o arquivo de configuração do serviço para especificar o número de instâncias de função a serem implantadas para cada função no serviço, os valores de quaisquer parâmetros de configuração e as impressões digitais de qualquer certificado associado a uma função. 

Se o serviço fizer parte de uma rede virtual, as informações de configuração para a rede deverão ser fornecidas no arquivo de configuração de serviço, bem como no arquivo de configuração de rede virtual. A extensão padrão do arquivo de configuração de serviço é .cscfg. Observe que Azure Policy não tem suporte para implantações clássicas para a imposição de configuração.

Defina os valores de configuração de um serviço de nuvem no arquivo de configuração de serviço (. cscfg) e a definição em um arquivo de definição de serviço (. csdef). Use o arquivo de definição de serviço para definir o modelo de serviço para um aplicativo. Defina as funções, que estão disponíveis para um serviço de nuvem e também especifique os pontos de extremidade de serviço. Registre a configuração dos serviços de nuvem do Azure com o arquivo de configuração de serviço. Qualquer reconfiguração pode ser feita por meio do arquivo ServiceConfig. cscfg. 

Monitore a definição do serviço do elemento NetworkTrafficRules opcional que restringe quais funções podem se comunicar com pontos de extremidade internos especificados. Configure o nó NetworkTrafficRules, um elemento opcional no arquivo de definição de serviço, para especificar como as funções devem se comunicar entre si. Coloque limites em quais funções podem acessar os pontos de extremidade internos da função específica.  Observe que a definição de serviço não pode ser alterada. 

Habilite logs de fluxo do grupo de segurança de rede e envie os logs para uma conta de armazenamento do Azure para auditoria. Envie os logs de fluxo para um espaço de trabalho Log Analytics e use Análise de Tráfego para fornecer informações sobre os padrões de tráfego em seu locatário do Azure. Algumas vantagens do Análise de Tráfego são a capacidade de visualizar a atividade da rede, identificar pontos de acesso e ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar as configurações incorretas da rede.

- [Azure Resource Manager vs. implantação clássica – compreenda os modelos de implantação e o estado de seus recursos](../azure-resource-manager/management/deployment-models.md)

- [Arquivo de configuração de serviços de nuvem](schema-cscfg-file.md)

- [Lista de serviços com suporte pelo Azure Policy](/cli/azure/azure-services-the-azure-cli-can-manage)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Diretrizes**: a Microsoft usa o protocolo TLS v 1.2 para proteger dados quando está viajando entre os clientes e os serviços de nuvem do Azure. Os datacenters da Microsoft negociam uma conexão TLS com os sistemas cliente que se conectam aos serviços do Azure. O TLS fornece autenticação forte, privacidade de mensagem e integridade (habilitando a detecção de adulteração, interceptação e falsificação de mensagens), interoperabilidade, flexibilidade de algoritmo e facilidade de implantação e uso.

- [Conceitos básicos de criptografia](../security/fundamentals/encryption-overview.md)

- [Configurar certificados TLS/SSL](cloud-services-configure-ssl-certificate-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: rejeitar comunicações com endereços IP maliciosos conhecidos

**Diretrizes**: a nuvem do Azure implementa uma segurança de rede multicamadas para proteger seus serviços de plataforma contra ataques de DDoS (negação de serviço distribuído). A proteção contra DDoS do Azure faz parte do processo de monitoramento contínuo da nuvem do Azure, que é continuamente aprimorado por meio de testes de penetração. Essa proteção contra DDoS foi projetada para resistir não apenas a ataques externos, mas também de outros locatários do Azure. 

Há algumas maneiras diferentes de bloquear ou negar a comunicação, além da proteção em nível de plataforma nos serviços de nuvem do Azure. Eles são: 

-  Criar uma tarefa de inicialização para bloquear seletivamente alguns endereços IP específicos
-  Restringir o acesso de uma função Web do Azure a um conjunto de endereços IP especificados modificando o arquivo de web.config do IIS

Impeça o tráfego de entrada para a URL padrão ou o nome de seus serviços de nuvem, por exemplo, *. cloudapp.net. Defina o cabeçalho de host para um nome DNS personalizado, em configuração de associação de site no arquivo de definição de serviços de nuvem (*. csdef).

Configure uma regra de negação de aplicação para atribuições de administrador de assinatura clássica. Por padrão, após a definição de um ponto de extremidade interno, a comunicação poderá fluir de qualquer função para o ponto de extremidade interno de uma função sem qualquer restrição. Para restringir a comunicação, adicione um elemento NetworkTrafficRules ao elemento ServiceDefinition no arquivo de definição de serviço.

- [Como bloquear/desabilitar o tráfego de entrada para a URL padrão do meu serviço de nuvem](https://docs.microsoft.com/azure/cloud-services/cloud-services-connectivity-and-networking-faq#how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service)

- [Proteção contra DDOS do Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-connectivity-and-networking-faq#how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service)

- [Bloquear um endereço IP específico](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks-common#block-a-specific-ip-address)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Orientação**: usar o observador de rede do Azure, o monitoramento de desempenho de rede, o diagnóstico e o serviço de análise, que permite o monitoramento de redes do Azure. A extensão da máquina virtual do agente do observador de rede é um requisito para capturar o tráfego de rede sob demanda e outras funcionalidades avançadas em máquinas virtuais do Azure. Instale a extensão da máquina virtual do agente do observador de rede e ative os logs de fluxo do grupo de segurança de rede.

Configure o log de fluxo em um grupo de segurança de rede. Examine os detalhes sobre como implantar a extensão da máquina virtual do observador de rede em uma máquina virtual existente implantada por meio do modelo de implantação clássico.

- [Configurar o log de fluxo em um grupo de segurança de rede](../virtual-machines/extensions/network-watcher-linux.md)

- [Para obter mais informações sobre como configurar logs de fluxo, visite](/cli/azure/azure-services-the-azure-cli-can-manage)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Diretrizes**: os serviços de nuvem do Azure não têm nenhuma ID ou recurso IPS internos. Os clientes podem selecionar e implantar uma solução de IPS ou IDS com base em rede complementar do Azure Marketplace com base em seus requisitos organizacionais. Ao usar soluções de terceiros, certifique-se de testar exaustivamente sua solução de IDS ou IPS selecionada com os serviços de nuvem do Azure para garantir a operação e a funcionalidade adequadas.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Orientação**: certificados de serviço, que são anexados aos serviços de nuvem do Azure, permitem a comunicação segura de e para o serviço. Esses certificados são definidos na definição de serviços e são implantados automaticamente na máquina virtual que está executando uma instância de uma função Web. Por exemplo, para uma função Web, você pode usar um certificado de serviço que pode autenticar um ponto de extremidade HTTPS exposto. 

Para atualizar o certificado, é necessário apenas carregar um novo certificado e alterar o valor da impressão digital no arquivo de configuração de serviço.

Use o protocolo TLS 1,2, o método mais comumente usado de proteção de dados para fornecer proteção de confidencialidade e integridade. 

Em geral, para proteger os aplicativos Web e protegê-los contra ataques como o OWASP Top 10, você pode implantar um gateway de Aplicativo Azure habilitado para firewall do aplicativo Web do Azure para proteger aplicativos Web. 

- [Certificados de serviço](cloud-services-certs-create.md)

- [Configurando o TLS para um aplicativo no Azure](cloud-services-configure-ssl-certificate-portal.md)

- [Como implantar o gateway de aplicativo](../application-gateway/quick-create-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Orientação**: proteger a configuração dos serviços de nuvem do Azure e monitorá-lo quanto a alterações. O arquivo de configuração de serviço especifica o número de instâncias de função a ser implantado para cada função no serviço, os valores das configurações e as impressões digitais de todos os certificados associados a uma função. 

Se o serviço fizer parte de uma rede virtual, as informações de configuração para a rede deverão ser fornecidas no arquivo de configuração de serviço, bem como no arquivo de configuração de rede virtual. A extensão padrão do arquivo de configuração de serviço é .cscfg.

Observe que não há suporte para Azure Policy com os serviços de nuvem do Azure para a imposição de configuração.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Diretrizes**: os grupos de segurança de rede do Azure podem ser usados para filtrar o tráfego de rede de e para recursos do Azure em uma rede virtual do Azure. Um grupo de segurança de rede contém regras de segurança que permitem ou negam o tráfego de rede de entrada para, ou, o tráfego de rede de saída do, vários tipos de recursos do Azure. Para cada regra, você pode especificar origem e destino, porta e protocolo.

Use o campo "Descrição" para regras de grupo de segurança de rede individuais nos serviços de nuvem do Azure para documentar as regras, que permitem o tráfego para ou de uma rede.

- [Como filtrar o tráfego de rede com regras de grupo de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Diretrizes**: Use o monitoramento de ponto de extremidade interno do Gerenciador de tráfego do Azure e os recursos de failover de ponto de extremidade automático. Eles ajudam você a fornecer aplicativos de alta disponibilidade, que são resilientes a falhas de ponto de extremidade e região do Azure. Para configurar o monitoramento de ponto de extremidade, você deve especificar determinadas configurações em seu perfil do Gerenciador de tráfego.

Reúna informações do log de atividades, um log de plataforma no Azure, em eventos de nível de assinatura. Ele inclui informações como quando um recurso é modificado ou quando uma máquina virtual é iniciada. Exiba o log de atividades no portal do Azure ou recupere entradas com o PowerShell e a CLI. 

Crie uma configuração de diagnóstico para enviar o log de atividades para Azure Monitor, hubs de eventos do Azure para encaminhar fora do Azure ou para o armazenamento do Azure para arquivamento. Configure Azure Monitor para alertas de notificação quando recursos críticos em seus serviços de nuvem do Azure forem alterados. 

- [Log de Atividades do Azure](/azure/azure-monitor/platform/activity-log)

- [Criar, exibir e gerenciar alertas do log de atividades usando o Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

- [Monitoramento do Traffic Manager](../traffic-manager/traffic-manager-monitoring.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usar fontes de sincronização de tempo aprovadas

**Diretrizes**: a Microsoft mantém fontes de tempo para recursos do Azure para serviços de nuvem do Azure. Talvez os clientes precisem criar uma regra de rede para permitir o acesso a um servidor de horário usado em seu ambiente, pela porta 123 com o protocolo UDP.

- [Acesso ao servidor NTP](https://docs.microsoft.com/azure/firewall/protect-windows-virtual-desktop#additional-considerations)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Diretrizes**: consuma os dados de streaming do serviço de nuvem programaticamente com os hubs de eventos do Azure. Integre e envie todos esses dados para o Azure Sentinel para monitorar e examinar seus logs ou use um SIEM de terceiros. Para o gerenciamento de log de segurança central, configure a exportação contínua dos dados escolhidos da central de segurança do Azure para os hubs de eventos do Azure e configure o conector apropriado para o SIEM. Aqui estão algumas opções para o Azure Sentinel, incluindo ferramentas de terceiros:

- Sentinela do Azure – usar o conector de dados de alertas da central de segurança nativa
- Splunk-use o complemento Azure Monitor para Splunk
- IBM QRadar-usar uma fonte de log configurada manualmente
- ArcSight – usar SmartConnector

Examine a documentação do Azure Sentinel para obter detalhes adicionais sobre os conectores disponíveis com o Azure Sentinel. 

- [Conectar fontes de dados](../sentinel/connect-data-sources.md)

- [Integrar com um SIEM](../security-center/continuous-export.md)

- [Armazenar dados de diagnóstico](diagnostics-extension-to-storage.md)

- [Configurando a integração SIEM por meio dos hubs de eventos do Azure](../security-center/continuous-export.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Orientação**: Configure o Visual Studio para configurar diagnóstico do Azure para solucionar problemas de serviços de nuvem do Azure que capturam dados de sistema e de log em máquinas virtuais, incluindo instâncias de máquina virtual que executam os serviços de nuvem do Azure. Os dados de diagnóstico são transferidos para uma conta de armazenamento de sua escolha. Ative o diagnóstico nos projetos dos serviços de nuvem do Azure antes da implantação.

 
Exiba o histórico de alterações de alguns eventos no log de atividades dentro de Azure Monitor. Auditar quais alterações ocorreram durante um período de tempo de evento. Escolha um evento no log de atividades para inspeção mais profunda com a guia histórico de alterações (versão prévia). Envie os dados de diagnóstico para Application Insights ao publicar os serviços de nuvem do Azure a partir do Visual Studio. Crie o Application Insights recurso do Azure nesse momento ou envie os dados para um recurso existente do Azure. 

Os serviços de nuvem do Azure podem ser monitorados por Application Insights para disponibilidade, desempenho, falhas e uso. É possível adicionar gráficos personalizados ao Application Insights, para que você veja os dados mais importantes. Os dados da instância de função podem ser coletados usando o SDK do Application Insights em seu projeto de serviços de nuvem do Azure. 

- [Ativar o diagnóstico no Visual Studio antes da implantação](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#to-turn-on-diagnostics-in-visual-studio-before-deployment)

- [Exibir histórico de alterações](/azure/azure-monitor/platform/activity-log#view-change-history)

- [Application Insights para o serviço de nuvem do Azure (clássico)](../azure-monitor/app/cloudservices.md)

- [Configurar o diagnóstico para o serviço de nuvem do Azure (clássico) e máquinas virtuais](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurar a retenção de armazenamento do log de segurança

**Orientação**: você pode usar o monitoramento avançado com os serviços de nuvem do Azure, que permite que as métricas adicionais sejam amostradas e coletadas em intervalos de 5 minutos, 1 hora e 12 horas. Os dados agregados são armazenados na conta de armazenamento, em tabelas e são limpos após 10 dias. No entanto, a conta de armazenamento usada é configurada por função e você pode usar contas de armazenamento diferentes para funções diferentes. Isso é configurado com uma cadeia de conexão nos arquivos .csdef e .cscfg.

Observe que o monitoramento avançado envolve o uso da extensão Diagnóstico do Azure (o SDK do Application Insights é opcional) na função que você deseja monitorar. A extensão de diagnóstico usa um arquivo de configuração (por função) chamado diagnostics.wadcfgx para configurar as métricas de diagnóstico monitoradas. A extensão Diagnóstico do Azure coleta e armazena dados em uma conta de Armazenamento do Azure. Essas configurações são definidas nos arquivos .wadcfgx, .csdef e .cscfg.

- [Introdução ao Monitoramento do Serviço de Nuvem](cloud-services-how-to-monitor.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: monitorar e revisar logs

**Orientação**: os modos de monitoramento básico ou avançado estão disponíveis para os serviços de nuvem do Azure. Os serviços de nuvem do Azure coletam automaticamente dados básicos de monitoramento (percentual de CPU, entrada/saída de rede e leitura/gravação de disco) de uma máquina virtual do host. Exiba os dados de monitoramento coletados nas páginas visão geral e métricas de um serviço de nuvem no portal do Azure. 

Habilite o diagnóstico nos serviços de nuvem do Azure para coletar dados de diagnóstico como logs de aplicativo, contadores de desempenho e muito mais, ao usar a extensão de Diagnóstico do Azure. Habilite ou atualize a configuração de diagnóstico em um serviço de nuvem que já esteja em execução com Set-AzureServiceDiagnosticsExtension cmdlet ou implante um serviço de nuvem com a extensão de diagnóstico automaticamente. Opcionalmente, instale o SDK do Application Insights. Envie contadores de desempenho para Azure Monitor.

A extensão Diagnóstico do Azure coleta e armazena dados em uma conta de Armazenamento do Azure. Transfira dados de diagnóstico para o Emulador de Armazenamento do Microsoft Azure ou para o armazenamento do Azure, pois eles não são armazenados permanentemente. Uma vez no armazenamento, ele pode ser exibido com uma das várias ferramentas disponíveis, como Gerenciador de Servidores no Visual Studio, Gerenciador de Armazenamento do Microsoft Azure, Management Studio do Azure. Configure as métricas de diagnóstico a serem monitoradas com um arquivo de configuração (por função) chamado Diagnostics. wadcfgx na extensão de diagnóstico. 

- [Introdução ao Monitoramento do Serviço de Nuvem](cloud-services-how-to-monitor.md)

- [Como habilitar o diagnóstico em uma função de trabalho – integrar com um SIEM](../security-center/continuous-export.md)

- [Habilitar o diagnóstico nos Serviços de Nuvem do Azure usando o PowerShell](cloud-services-diagnostics-powershell.md)

- [Armazenar e exibir dados de diagnóstico no Armazenamento do Azure](https://docs.microsoft.com/azure/cloud-services/diagnostics-extension-to-storage?&amp;preserve-view=true)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Orientação**: você pode monitorar os dados de log dos serviços de nuvem do Azure por integração com o Azure Sentinel ou com um Siem de terceiros, habilitando o alerta para atividades anormais.

- [Integrar com um SIEM](../security-center/continuous-export.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware

**Diretrizes**: Microsoft antimalware para Azure, protege os serviços de nuvem do Azure e as máquinas virtuais. Você tem a opção de implantar soluções de segurança de terceiros além, como paredes de fogo de aplicativo Web, firewalls de rede, Antimalware, sistemas de prevenção e detecção de intrusão (IDS ou IPS) e muito mais.

- [Quais são os recursos e as funcionalidades que os IPS/IDS e DDOS básico do Azure fornecem](https://docs.microsoft.com/azure/cloud-services/cloud-services-configuration-and-management-faq#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Diretrizes**: a Microsoft recomenda que você gerencie o acesso aos recursos do Azure usando o controle de acesso baseado em função do Azure (RBAC do Azure). No entanto, os serviços de nuvem do Azure não dão suporte ao modelo RBAC do Azure, pois não é um serviço baseado em Azure Resource Manager e você precisa usar uma assinatura clássica

Por padrão, administrador da conta, administrador de serviços e Co-Administrator são as três funções de administrador de assinatura clássicas no Azure. 

Os administradores de assinatura clássicos têm acesso total à assinatura do Azure. Eles podem gerenciar recursos usando o portal do Azure, as APIs do Azure Resource Manager e as APIs do modelo de implantação clássico. A conta que é usada para se inscrever no Azure é definida automaticamente como o Administrador da Conta e o Administrador de Serviços. Co-Administrators adicionais podem ser adicionadas posteriormente. 

O administrador do serviço e o Co-Administrators ter acesso equivalente de usuários que receberam a função de proprietário (uma função do Azure) no escopo da assinatura. Gerencie Co-Administrators ou exiba o administrador de serviços usando a guia administradores clássicos na portal do Azure. 

Liste as atribuições de função para administrador de serviço clássico e coadministradores com o PowerShell com o comando:

Get-AzRoleAssignment-IncludeClassicAdministrators

Examine as diferenças entre as funções administrativas clássicas de assinatura. 

- [Diferenças entre três funções administrativas de assinatura clássica](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles#classic-subscription-administrator-roles)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usar contas administrativas dedicadas

**Orientação**: é recomendável criar procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas, com base nas funções disponíveis e nas permissões necessárias para operar e gerenciar os recursos dos serviços de nuvem do Azure.

- [Diferenças entre as funções administrativas da assinatura clássica](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles#classic-subscription-administrator-roles)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usar o SSO (logon único) com o Azure Active Directory

**Diretrizes**: Evite gerenciar identidades separadas para aplicativos em execução nos serviços de nuvem do Azure. Implemente o logon único para evitar exigir que os usuários gerenciem várias identidades e credenciais.

- [O que é SSO (logon único)](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Orientação**: é recomendável usar uma estação de trabalho segura gerenciada pelo Azure (também conhecida como estação de trabalho de acesso privilegiado) para tarefas administrativas, que exigem privilégios elevados.

- [Entenda as estações de trabalho seguras e gerenciadas pelo Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como habilitar a autenticação multifator do Azure Active Directory (Azure AD)](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: manter um inventário de informações confidenciais

**Diretrizes**: Use as APIs REST do serviço de nuvem do Azure para inventariar seus recursos de serviço de nuvem do Azure para obter informações confidenciais. Sondar os recursos de serviço de nuvem implantados para obter os recursos de configuração e. pkg.

 Por exemplo, algumas APIs estão listadas abaixo:

- Obter implantação-a operação obter implantação retorna informações de configuração, status e propriedades do sistema para uma implantação.
- Obter pacote-a operação obter pacote recupera um pacote de serviço de nuvem para uma implantação e armazena os arquivos de pacote em Microsoft Azure armazenamento de BLOBs
- Obter propriedades do serviço de nuvem – a operação obter propriedades do serviço de nuvem recupera propriedades para o serviço de nuvem especificado

Examine a documentação das APIs REST do serviço de nuvem do Azure e crie um processo de proteção de dados de informações confidenciais com base em seus requisitos organizacionais.

- [Obter implantação](/rest/api/compute/cloudservices/rest-get-deployment)

- [Obter propriedades do serviço de nuvem](/rest/api/compute/cloudservices/rest-get-cloud-service-properties)

- [Obter pacote](/rest/api/compute/cloudservices/rest-get-package)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: implemente o isolamento usando assinaturas e grupos de gerenciamento separados para domínios de segurança individuais, como tipo de ambiente e nível de sensibilidade de dados para serviços de nuvem do Azure.

Você também pode editar o "permissionLevel" no elemento de certificado do serviço de nuvem do Azure para especificar as permissões de acesso dadas aos processos de função. Se você quiser que apenas processos elevados possam acessar a chave privada, especifique a permissão elevada. a permissão limitedOrElevated permite que todos os processos de função acessem a chave privada. Os valores possíveis são limitedOrElevated ou elevado. O valor padrão é limitedOrElevated.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Esquema WebRole](https://docs.microsoft.com/azure/cloud-services/schema-csdef-webrole#Certificate)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: é recomendável usar uma solução de terceiros do Azure Marketplace em perímetros de rede para monitorar a transferência não autorizada de informações confidenciais e bloquear essas transferências ao alertar os profissionais de segurança de informações.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Orientação**: configurar TLS v2 para serviços de nuvem do Azure. Use o portal do Azure para adicionar o certificado à implantação de serviços de nuvem do Azure em etapas e adicionar as informações de certificado aos arquivos CSDEF e CSCFG dos serviços. Empacote novamente seu aplicativo e atualize sua implantação em etapas para usar o novo pacote. 

Use certificados de serviço no Azure que são anexados aos serviços de nuvem do Azure para permitir a comunicação segura de e para o serviço. Forneça um certificado que possa autenticar um ponto de extremidade HTTPS exposto. Defina certificados de serviço na definição de serviço do serviço de nuvem e implante-os automaticamente na máquina virtual, executando uma instância de sua função.

Autenticar com a API de gerenciamento com certificados de gerenciamento) os certificados de gerenciamento permitem que você autentique com o modelo de implantação clássico. Muitos programas e ferramentas (como o Visual Studio ou o SDK do Azure) usam esses certificados para automatizar a configuração e a implantação de diversos serviços do Azure. 

Para referência adicional, a API do modelo de implantação clássica fornece acesso programático à funcionalidade do modelo de implantação clássico disponível por meio do portal do Azure. O SDK do Azure para Python pode ser usado para gerenciar serviços de nuvem do Azure e contas de armazenamento do Azure. O SDK do Azure para Python encapsula a API de modelo de implantação clássica, uma API REST. Todas as operações de API são executadas por TLS e mutuamente autenticadas usando certificados X. 509 v3. O serviço de gerenciamento pode ser acessado dentro de um serviço em execução no Azure. Ele também pode ser acessado diretamente pela Internet em qualquer aplicativo que possa enviar uma solicitação HTTPS e receber uma resposta HTTPS.

- [Configurar o TLS para um aplicativo no Azure](cloud-services-configure-ssl-certificate-portal.md)

- [Usar o modelo de implantação clássico do Python](cloud-services-python-how-to-use-service-management.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: é recomendável usar uma ferramenta de descoberta ativa de terceiros para identificar todas as informações confidenciais armazenadas, processadas ou transmitidas pelos sistemas de tecnologia da organização, incluindo aquelas localizadas no local, ou em um provedor de serviços remoto e, em seguida, atualizar o inventário de informações confidenciais da organização.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Orientação**: não aplicável ao serviço de nuvem (clássico). Ele não impõe a prevenção de perda de dados.

É recomendável implementar uma ferramenta de terceiros, como uma solução de prevenção contra perda de dados baseada em host automatizada, para impor controles de acesso nos dados, mesmo quando os dados são copiados fora de um sistema.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: criptografar informações confidenciais em repouso

**Diretrizes**: os serviços de nuvem do Azure não dão suporte à criptografia em repouso. Isso ocorre porque os serviços de nuvem do Azure foram projetados para serem sem monitoração de estado. Os serviços de nuvem do Azure dão suporte ao armazenamento externo, por exemplo, armazenamento do Azure, que é, por padrão, criptografado em repouso.  

Os dados de aplicativo armazenados em discos temporários não são criptografados. O cliente é responsável por gerenciar e criptografar esses dados, conforme necessário.  

- [Noções básicas sobre a criptografia em repouso do Azure](../security/fundamentals/encryption-atrest.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Orientação**: você pode usar alertas de métrica clássicos no Azure monitor para ser notificado quando uma de suas métricas aplicadas a recursos críticos ultrapassar um limite. Os alertas de métrica clássicos são uma funcionalidade mais antiga que permite alertas somente em métricas não dimensionais. Há uma funcionalidade mais recente chamada alertas de métrica que tem funcionalidade aprimorada sobre alertas de métrica clássicos. 

Além disso, Application Insights pode monitorar os aplicativos dos serviços de nuvem do Azure quanto à disponibilidade, ao desempenho, às falhas e ao uso. Isso usa dados combinados de SDKs de Application Insights com dados de Diagnóstico do Azure de seus serviços de nuvem do Azure.

- [Criar, exibir e gerenciar alertas de métrica clássicos usando o Azure Monitor](/azure/azure-monitor/platform/alerts-classic-portal)

- [Visão geral dos alertas de métrica](/azure/azure-monitor/platform/alerts-metric-overview) 

- [Application Insights para o serviço de nuvem do Azure (clássico)](../azure-monitor/app/cloudservices.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte o [benchmark de segurança do Azure: gerenciamento de vulnerabilidade](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: implantar solução automatizada de gerenciamento de patch de sistema operacional

**Orientação**: Observe que essas informações estão relacionadas ao sistema operacional convidado do Azure para funções Web e de trabalho de serviços de nuvem do Azure com PaaS (plataforma como serviço). No entanto, ele não se aplica às máquinas virtuais com a infraestrutura como um serviço (IaaS).

Por padrão, o Azure atualiza periodicamente o sistema operacional convidado do cliente para a imagem mais recente com suporte na família do sistema operacional que ele especificou em sua configuração de serviço (. cscfg), como o Windows Server 2016.

Quando um cliente escolhe uma versão específica do sistema operacional para a implantação dos serviços de nuvem do Azure, ele desabilita as atualizações automáticas do sistema operacional e faz o patch de sua responsabilidade. O cliente deve garantir que suas instâncias de função estejam recebendo atualizações ou que possam expor seus aplicativos a vulnerabilidades de segurança.

- [Sistema operacional convidado do Azure](cloud-services-guestos-msrc-releases.md)

- [Capacidade de suporte e política de desativação do SO convidado do Azure](cloud-services-guestos-retirement-policy.md)

- [Como configurar o serviço de nuvem (clássico)](cloud-services-how-to-configure-portal.md)

- [Gerenciar versão do SO Convidado](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal#manage-guest-os-version)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: implantar uma solução de gerenciamento de patch automatizada para títulos de software de terceiros

**Diretrizes**: Use uma solução de gerenciamento de patches de terceiros. Os clientes que já usam Configuration Manager em seu ambiente também podem usar System Center Updates Publisher, permitindo que eles publiquem atualizações personalizadas no Windows Server Update Service. 

Isso permite que Gerenciamento de Atualizações corrija os computadores que usam Configuration Manager como seu repositório de atualizações com software de terceiros.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Orientação**: é recomendável para um cliente entender o escopo do risco de um ataque de DDoS em uma base contínua. 

Sugerimos pensar nesses cenários:

- Quais novos recursos do Azure disponíveis publicamente precisam de proteção?
- Há um ponto único de falha no serviço?
- Como os serviços podem ser isolados para limitar o impacto de um ataque e ainda disponibilizá-los para os clientes válidos?
- Há redes virtuais em que a Proteção contra DDoS Standard deve ser habilitada, mas ainda não está?
- Meu serviços são ativo/ativo com failover em várias regiões?

Documentação de suporte:

- [Avaliação de risco dos seus recursos do Azure](https://docs.microsoft.com/azure/security/fundamentals/ddos-best-practices#risk-evaluation-of-your-azure-resources)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Orientação**: não aplicável aos serviços de nuvem do Azure. Essa recomendação é aplicável aos recursos de computação IaaS.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Orientação**: é recomendável reconciliar o inventário regularmente e garantir que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definir e manter um inventário de recursos aprovados do Azure

**Diretrizes**: o cliente deve definir os recursos aprovados do Azure e o software aprovado para recursos de computação.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Orientação**: Use o recurso de controle de aplicativo adaptável, disponível na central de segurança do Azure. É uma solução inteligente, automatizada e de ponta a ponta da central de segurança que ajuda a controlar quais aplicativos podem ser executados em computadores Windows e Linux, Azure e não Azure. Ele também ajuda a proteger seus computadores contra malware. 

Esse recurso está disponível para Windows Azure e não Azure (todas as versões, clássicas ou Azure Resource Manager) e computadores Linux.

A central de segurança usa o aprendizado de máquina para analisar os aplicativos em execução em seus computadores e cria uma lista de permissões dessa inteligência. Esse recurso simplifica muito o processo de configuração e manutenção de políticas de lista de permissões de aplicativo, permitindo que você:
- Bloquear ou alertar sobre tentativas de executar aplicativos mal-intencionados, incluindo aqueles que, de outra forma, poderiam ser perdidos por soluções antimalware.

- Cumpra a política de segurança da sua organização que impõe o uso somente de software licenciado.
- Evite o uso de softwares indesejados em seu ambiente.
- Evite a execução de aplicativos antigos e sem suporte.
- Impeça o uso de ferramentas de software específicas que não são permitidas em sua organização.
- Habilite a TI a controlar o acesso a dados confidenciais pelo uso do aplicativo.

Mais detalhes estão disponíveis nos links referenciados.

- [Controles de aplicativo adaptáveis](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorar aplicativos de software não aprovados nos recursos de computação

**Orientação**: Use o recurso de controle de aplicativo adaptável, disponível na central de segurança do Azure. É uma solução inteligente, automatizada e de ponta a ponta da central de segurança que ajuda a controlar quais aplicativos podem ser executados em computadores Windows e Linux, Azure e não Azure. Ele também ajuda a proteger seus computadores contra malware. 

Esse recurso está disponível para Windows Azure e não Azure (todas as versões, clássicas ou Azure Resource Manager) e computadores Linux.

A central de segurança usa o aprendizado de máquina para analisar os aplicativos em execução em seus computadores e cria uma lista de permissões dessa inteligência. Esse recurso simplifica muito o processo de configuração e manutenção de políticas de lista de permissões de aplicativo, permitindo que você:

- Bloquear ou alertar sobre tentativas de executar aplicativos mal-intencionados, incluindo aqueles que, de outra forma, poderiam ser perdidos por soluções antimalware.

- Cumpra a política de segurança da sua organização que impõe o uso somente de software licenciado.

- Evite o uso de softwares indesejados em seu ambiente.

- Evite a execução de aplicativos antigos e sem suporte.

- Impeça o uso de ferramentas de software específicas que não são permitidas em sua organização.

- Habilite a TI a controlar o acesso a dados confidenciais pelo uso do aplicativo.

Mais detalhes estão disponíveis nos links referenciados.

- [Controles de aplicativo adaptáveis](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: remover recursos e aplicativos de software não aprovados do Azure

**Orientação**: Use o recurso de controle de aplicativo adaptável, disponível na central de segurança do Azure. É uma solução inteligente, automatizada e de ponta a ponta da central de segurança que ajuda a controlar quais aplicativos podem ser executados em computadores Windows e Linux, Azure e não Azure. Ele também ajuda a proteger seus computadores contra malware. 

Esse recurso está disponível para Windows Azure e não Azure (todas as versões, clássicas ou Azure Resource Manager) e computadores Linux.

A central de segurança usa o aprendizado de máquina para analisar os aplicativos em execução em seus computadores e cria uma lista de permissões dessa inteligência. Esse recurso simplifica muito o processo de configuração e manutenção de políticas de lista de permissões de aplicativo, permitindo que você:

- Bloquear ou alertar sobre tentativas de executar aplicativos mal-intencionados, incluindo aqueles que, de outra forma, poderiam ser perdidos por soluções antimalware.

- Cumpra a política de segurança da sua organização que impõe o uso somente de software licenciado.

- Evite o uso de softwares indesejados em seu ambiente.

- Evite a execução de aplicativos antigos e sem suporte.

- Impeça o uso de ferramentas de software específicas que não são permitidas em sua organização.

- Habilite a TI a controlar o acesso a dados confidenciais pelo uso do aplicativo.

Mais detalhes estão disponíveis nos links referenciados.

- [Controles de aplicativo adaptáveis](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="68-use-only-approved-applications"></a>6.8: usar somente aplicativos aprovados

**Orientação**: Use o recurso de controle de aplicativo adaptável, disponível na central de segurança do Azure. É uma solução inteligente, automatizada e de ponta a ponta da central de segurança que ajuda a controlar quais aplicativos podem ser executados em computadores Windows e Linux, Azure e não Azure. Ele também ajuda a proteger seus computadores contra malware. 

Esse recurso está disponível para Windows Azure e não Azure (todas as versões, clássicas ou Azure Resource Manager) e computadores Linux.

A central de segurança usa o aprendizado de máquina para analisar os aplicativos em execução em seus computadores e cria uma lista de permissões dessa inteligência. Esse recurso simplifica muito o processo de configuração e manutenção de políticas de lista de permissões de aplicativo, permitindo que você:

- Bloquear ou alertar sobre tentativas de executar aplicativos mal-intencionados, incluindo aqueles que, de outra forma, poderiam ser perdidos por soluções antimalware.

- Cumpra a política de segurança da sua organização que impõe o uso somente de software licenciado.

- Evite o uso de softwares indesejados em seu ambiente.

- Evite a execução de aplicativos antigos e sem suporte.

- Impeça o uso de ferramentas de software específicas que não são permitidas em sua organização.

- Habilite a TI a controlar o acesso a dados confidenciais pelo uso do aplicativo.

Mais detalhes estão disponíveis nos links referenciados.

- [Controles de aplicativo adaptáveis](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: manter um inventário de títulos de software aprovados

**Orientação**: Use o recurso de controle de aplicativo adaptável, disponível na central de segurança do Azure. É uma solução inteligente, automatizada e de ponta a ponta da central de segurança que ajuda a controlar quais aplicativos podem ser executados em computadores Windows e Linux, Azure e não Azure. Ele também ajuda a proteger seus computadores contra malware. 

Esse recurso está disponível para Windows Azure e não Azure (todas as versões, clássicas ou Azure Resource Manager) e computadores Linux.

A central de segurança usa o aprendizado de máquina para analisar os aplicativos em execução em seus computadores e cria uma lista de permissões dessa inteligência. Esse recurso simplifica muito o processo de configuração e manutenção de políticas de lista de permissões de aplicativo, permitindo que você:
- Bloquear ou alertar sobre tentativas de executar aplicativos mal-intencionados, incluindo aqueles que, de outra forma, poderiam ser perdidos por soluções antimalware.

- Cumpra a política de segurança da sua organização que impõe o uso somente de software licenciado.
- Evite o uso de softwares indesejados em seu ambiente.
- Evite a execução de aplicativos antigos e sem suporte.
- Impeça o uso de ferramentas de software específicas que não são permitidas em sua organização.
- Habilite a TI a controlar o acesso a dados confidenciais pelo uso do aplicativo.

Mais detalhes estão disponíveis nos links referenciados.

- [Controles de aplicativo adaptáveis](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: limitar a capacidade dos usuários de executar scripts em recursos de computação

**Orientação**: Use o recurso de controle de aplicativo adaptável, disponível na central de segurança do Azure. É uma solução inteligente, automatizada e de ponta a ponta da central de segurança que ajuda a controlar quais aplicativos podem ser executados em computadores Windows e Linux, Azure e não Azure. Ele também ajuda a proteger seus computadores contra malware. 

Esse recurso está disponível para Windows Azure e não Azure (todas as versões, clássicas ou Azure Resource Manager) e computadores Linux.

A central de segurança usa o aprendizado de máquina para analisar os aplicativos em execução em seus computadores e cria uma lista de permissões dessa inteligência. Esse recurso simplifica muito o processo de configuração e manutenção de políticas de lista de permissões de aplicativo, permitindo que você:

- Bloquear ou alertar sobre tentativas de executar aplicativos mal-intencionados, incluindo aqueles que, de outra forma, poderiam ser perdidos por soluções antimalware.

- Cumpra a política de segurança da sua organização que impõe o uso somente de software licenciado.

- Evite o uso de softwares indesejados em seu ambiente.

- Evite a execução de aplicativos antigos e sem suporte.

- Impeça o uso de ferramentas de software específicas que não são permitidas em sua organização.

- Habilite a TI a controlar o acesso a dados confidenciais pelo uso do aplicativo.

Mais detalhes estão disponíveis nos links referenciados.

- [Controles de aplicativo adaptáveis](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separar física ou logicamente os aplicativos de alto risco

**Diretrizes**: para aplicativos confidenciais ou de alto risco com os serviços de nuvem do Azure, implemente assinaturas separadas ou grupos de gerenciamento para fornecer isolamento.

Use um grupo de segurança de rede, crie uma regra de segurança de entrada, escolha um serviço como http, escolha uma porta personalizada também, dê a ela uma prioridade e um nome. A prioridade afeta a ordem na qual as regras são aplicadas, quanto menor o valor numérico, mais cedo a regra é aplicada. Você precisará associar o grupo de segurança de rede a uma sub-rede ou a uma interface de rede específica para isolar ou segmentar o tráfego de rede com base nas suas necessidades de negócios.

Mais detalhes estão disponíveis nos links referenciados.

- [Tutorial – filtrar o tráfego de rede com um grupo de segurança de rede usando o portal do Azure](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: Use as recomendações da central de segurança do Azure como uma linha de base de configuração segura para os recursos dos serviços de nuvem do Azure. 

Na portal do Azure, escolha central de segurança, &amp; aplicativos de computação e serviços de nuvem do Azure para ver as recomendações aplicáveis aos seus recursos de serviço.

- [Recomendações de segurança – um guia de referência](../security-center/recommendations-reference.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: manter configurações seguras de recursos do Azure

**Orientação**: não aplicável aos serviços de nuvem do Azure. Ele se baseia no modelo de implantação clássico. É recomendável usar uma solução de terceiros para manter as configurações de recursos do Azure seguras

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: armazenar configuração de recursos do Azure com segurança

**Diretrizes**: o arquivo de configuração do serviço de nuvem do Azure armazena os atributos operacionais de um recurso. Você pode armazenar uma cópia dos arquivos de configuração em uma conta de armazenamento seguro.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: não aplicável aos serviços de nuvem do Azure. Ele se baseia no modelo de implantação clássico e não pode ser gerenciado por Azure Resource Manager ferramentas de configuração baseadas em implantação.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: implantar as ferramentas de gerenciamento de configuração para sistemas operacionais

**Orientação**: não aplicável aos serviços de nuvem do Azure. Essa recomendação é aplicável a recursos de computação baseados em IaaS (infraestrutura como serviço).

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Diretrizes**: Use a central de segurança do Azure para executar verificações de linha de base para os recursos do Azure.  

- [Como corrigir recomendações na central de segurança do Azure](../security-center/security-center-remediate-recommendations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementar monitoramento automatizado de configuração para sistemas operacionais

**Diretrizes**: na central de segurança do Azure, escolha &amp; recurso aplicativos de computação e siga as recomendações para máquinas virtuais, servidores e contêineres.

- [Entenda as recomendações do contêiner da Central de Segurança do Azure](../security-center/container-security.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: gerenciar segredos do Azure com segurança

**Diretrizes**: os serviços de nuvem do Azure se baseiam em um modelo de implantação clássico e não são integrados ao Azure Key Vault.

Você pode proteger segredos, como credenciais, que são usadas nos serviços de nuvem do Azure para que você não precise digitar uma senha a cada vez. Para começar, especifique uma senha de texto sem formatação, converta-a em uma cadeia de caracteres segura usando ConvertTo-SecureString, comando do PowerShell. Em seguida, converta essa cadeia de caracteres segura em uma cadeia de caracteres criptografada padrão usando ConvertFrom-SecureString.  Agora você pode salvar essa cadeia de caracteres criptografada padrão em um arquivo usando Set-Content.

Além disso, é recomendável armazenar as chaves privadas para certificados usados nos serviços de nuvem do Azure para um armazenamento protegido.

- [Configurar a Área de Trabalho Remota por meio do PowerShell](https://docs.microsoft.com/azure/cloud-services/cloud-services-role-enable-remote-desktop-powershell#configure-remote-desktop-from-powershell)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Orientação**: Proteja os segredos, como as credenciais usadas nos serviços de nuvem do Azure, para que você não precise digitar uma senha a cada vez. 
 

Para começar, especifique uma senha de texto sem formatação, altere-a para uma cadeia de caracteres segura usando ConvertTo-SecureString, comando do PowerShell. Em seguida, converta essa cadeia de caracteres segura em uma cadeia de caracteres criptografada padrão usando ConvertFrom-SecureString. Agora, salve essa cadeia de caracteres criptografada padrão em um arquivo usando Set-Content comando.

Armazene as chaves privadas dos certificados usados nos serviços de nuvem do Azure em um local de armazenamento seguro.

- [Configurar a Área de Trabalho Remota por meio do PowerShell](https://docs.microsoft.com/azure/cloud-services/cloud-services-role-enable-remote-desktop-powershell#configure-remote-desktop-from-powershell)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte o [benchmark de segurança do Azure: defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: usar software antimalware gerenciado centralmente

**Diretrizes**: o Microsoft antimalware para Azure está disponível para serviços de nuvem e máquinas virtuais do Azure. É uma proteção livre em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados. Ele gera alertas quando um software mal-intencionado ou indesejado conhecido tenta se instalar ou executar nos sistemas do Azure. 

Use o cmdlet Antimalware baseado em PowerShell para obter a configuração de Antimalware, com "Get-AzureServiceAntimalwareConfig".

Habilite a extensão Antimalware com um script do PowerShell na tarefa de inicialização nos serviços de nuvem do Azure.

Escolha o recurso de controle de aplicativo adaptável na central de segurança do Azure, uma solução inteligente, automatizada e de ponta a ponta. Ele ajuda a proteger seus computadores contra malware e permite bloquear ou alertar tentativas de execução de aplicativos mal-intencionados, incluindo aqueles que, de outra forma, podem ser perdidos por soluções antimalware.

- [Como posso adicionar uma extensão antimalware para meus serviços de nuvem do Azure de forma automatizada](https://docs.microsoft.com/azure/cloud-services/cloud-services-configuration-and-management-faq#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)

- [Cenários de implantação de antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware#antimalware-deployment-scenarios)

- [Controles de aplicativo adaptáveis](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

- [Como configurar automações de fluxo de trabalho na Central de Segurança do Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [O cliente também pode aproveitar o guia de tratamento de incidentes de segurança do computador da NIST para ajudar na criação de seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretrizes**: a central de segurança do Azure atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A gravidade se baseia em quão confiante a central de segurança está na localização ou na análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta. 

Marque claramente as assinaturas (por exemplo, produção, não produção) e crie um sistema de nomeação para identificar e categorizar claramente os recursos do Azure.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Diretriz**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário. 

- [Veja a publicação do NIST: Guia para testar, treinar e exercitar programas para planos de TI e recursos](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: as informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que os dados do cliente foram acessados por uma pessoa não autorizada ou ilegal. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos. 

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretriz**: exporte os alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da central de segurança para transmitir os alertas para o Azure Sentinel. 

- [Como configurar a exportação contínua](../security-center/continuous-export.md) 

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na Central de Segurança do Azure para disparar automaticamente respostas por meio de "Aplicativos Lógicos" em alertas de segurança e recomendações.

- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte o [benchmark de segurança do Azure: testes de penetração e exercícios de equipe vermelho](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretrizes**: siga as regras de teste de penetração Microsoft Cloud do Engagement para garantir que seus testes de penetração não estejam violando as políticas da Microsoft. Use a estratégia da Microsoft, a execução de Equipes Vermelhas e os testes de penetração de sites online na infraestrutura, nos serviços e nos aplicativos de nuvem gerenciados pela Microsoft. 

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Saiba mais sobre a [Linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
