---
title: "Segurança de dados do Log Analytics | Microsoft Docs"
description: Saiba mais sobre como o Log Analytics protege a sua privacidade e seus dados.
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: a33bb05d-b310-4f2c-8f76-f627e600c8e7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: magoedte
ms.openlocfilehash: 91af774560860b35913e57b49fb7a1dd59f5640f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-data-security"></a>Segurança de dados do Log Analytics
A Microsoft está comprometida em proteger sua privacidade e segurança de dados, oferecendo softwares e serviços que ajudam a gerenciar a infraestrutura de TI da sua organização. Reconhecemos que quando você entrega seus dados a outros, essa confiança requer segurança rigorosa. A Microsoft obedece às diretrizes rígidas de conformidade e segurança — da codificação à operação de um serviço.

Segurança e proteção de dados é uma prioridade principal da Microsoft. Entre em contato conosco com quaisquer perguntas, sugestões ou problemas sobre qualquer uma das seguintes informações, incluindo nossas políticas de segurança nas [opções de suporte do Azure](http://azure.microsoft.com/support/options/).

Este artigo explica como os dados são coletados, processados e protegidos pelo Log Analytics no OMS (Operations Management Suite). Você pode usar agentes para se conectar ao serviço Web, usar o System Center Operations Manager para coletar dados operacionais ou recuperar dados de diagnóstico do Azure para uso pelo Log Analytics. Os dados coletados são enviados pela Internet usando SSL 3 e autenticação baseada em certificado para o serviço Log Analytics, que está hospedado no Microsoft Azure. Os dados são compactados pelo agente antes de serem enviados.

O serviço Log Analytics gerencia seus dados baseados em nuvem com segurança usando os seguintes métodos:

* segregação de dados
* retenção de dados
* segurança física
* gerenciamento de incidentes
* conformidade
* certificações de padrões de segurança

## <a name="data-segregation"></a>Segregação de dados
Os dados do cliente são mantidos separados logicamente em cada componente no serviço OMS. Todos os dados são marcados por organização. Essa marcação persiste em todo o ciclo de vida dos dados e é imposta em cada camada do serviço. Cada cliente tem um blob do Azure dedicado que hospeda os dados de longo prazo

## <a name="data-retention"></a>Retenção de dados
Dados indexados de pesquisa de log são armazenados e retidos de acordo com o plano de preço. Para obter mais informações, consulte [Preços do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

A Microsoft exclui os dados do cliente 30 dias depois que o espaço de trabalho do OMS é fechado. A Microsoft também exclui a conta do Azure Storage em que os dados residem. Quando os dados do cliente são removidos, não ocorre a destruição de nenhuma unidade física.

A tabela a seguir lista algumas das soluções disponíveis no OMS e os exemplos de tipos de dados coletados.

| **Solução** | **Tipos de dados** |
| --- | --- |
| Avaliação de Configuração |Dados de configuração, metadados e dados de estado |
| Planejamento da capacidade |Dados de desempenho e metadados |
| Antimalware |Dados de configuração e metadados |
| Avaliação de atualização do sistema |Metadados e dados de estado |
| Gerenciamento de Log |Logs de eventos de definidos pelo usuário, logs de eventos do Windows e/ou os logs do IIS |
| Controle de Alterações |Inventário de software e metadados do serviço Windows |
| Avaliação do SQL e Active Directory |Dados WMI, dados do registro, dados de desempenho e resultados de exibição do gerenciamento dinâmico do SQL Server |

A tabela a seguir mostra exemplos de tipos de dados:

| **Tipo de dados** | **Campos** |
| --- | --- |
| Alerta |Nome do Alerta, Descrição do Alerta, BaseManagedEntityId, ID do Problema, IsMonitorAlert, RuleId, ResolutionState, Prioridade, Gravidade, Categoria, Proprietário, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Configuração |CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Evento |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Categoria, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Observação:** quando você grava eventos com campos personalizados no log de eventos do Windows, o OMS os coleta. |
| Metadados |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Desempenho |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Estado |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Segurança física
O serviço Log Analytics em OMS é operado pelo pessoal da Microsoft e todas as atividades são registradas em log e podem ser auditadas. O serviço é executado por completo no Azure e está em conformidade com os critérios de engenharia comuns do Azure. Você pode exibir detalhes sobre a segurança física dos ativos do Azure na página 18 da [Visão geral de Segurança do Microsoft Azure](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Os direitos de acesso físico a áreas protegidas são alterados dentro de um dia útil para qualquer pessoa que não tenha mais responsabilidade pelo serviço OMS, incluindo a transferência e encerramento. Você pode ler sobre a infraestrutura física global que usamos em [Datacenters da Microsoft](https://www.microsoft.com/en-us/server-cloud/cloud-os/global-datacenters.aspx).

## <a name="incident-management"></a>Gerenciamento de incidentes
O OMS tem um processo de gerenciamento de incidentes que todos os serviços Microsoft seguem. Resumidamente, nós:

* Usamos um modelo de responsabilidade compartilhada em que uma parte da responsabilidade pela segurança pertence à Microsoft e uma parte pertence ao cliente
* Gerenciamos incidentes de segurança do Azure
  * Início de uma investigação sobre a detecção de um incidente
  * Avaliamos o impacto e a gravidade de um incidente por um membro da equipe de resposta a incidentes de plantão. Com base nas evidências, a avaliação pode ou não resultar em mais escalonamento para a equipe de resposta de segurança.
  * Diagnosticamos um incidente por especialistas de resposta de segurança para conduzir a investigação forense ou técnica, identificar estratégias de contenção, atenuação e solução alternativa. Se a equipe de segurança acreditar que os dados do cliente podem ter sido expostos a um indivíduo não autorizado ou ilícito, a execução paralela do processo de Notificação de incidente do cliente começa em paralelo.  
  * Estabilizamos e recuperamos do incidente. A equipe de resposta a incidentes cria um plano de recuperação para atenuar o problema. As etapas de contenção de crise, como colocar os sistemas afetados em quarentena, podem ocorrer imediatamente e em paralelo com o diagnóstico. Atenuações de prazo mais longo podem ser planejadas, o que ocorre após o risco imediato ter terminado.  
  * Fechamos o incidente e realizamos um post-mortem. A equipe de resposta a incidentes cria um post-mortem que descreve os detalhes do incidente, com a intenção de revisar as políticas, procedimentos e processos para evitar a recorrência do evento.
* Notificamos os clientes dos incidentes de segurança
  * Determinamos o escopo dos clientes afetados e fornecemos qualquer pessoa que seja afetada como um aviso detalhado conforme possível
  * Criamos um aviso para fornecer aos clientes informações suficientemente detalhadas para que possam realizar uma investigação do seu lado e atender quaisquer compromissos que tenham firmado com seus usuários finais ao não atrasar indevidamente o processo de notificação.
  * Confirmamos e declaramos o incidente, se necessário.
  * Notificamos os clientes com uma notificação de incidentes sem demora excessiva e acordo com qualquer compromisso contratual ou legal. As notificações de incidentes de segurança são entregue a um ou mais administradores do cliente por qualquer meio que a Microsoft selecione, inclusive por email.
* Conduzimos o treinamento e a prontidão de equipe
  * Os funcionários da Microsoft devem concluir o treinamento de segurança e conscientização, o que os ajuda a identificar e relatar problemas de segurança suspeitos.  
  * Os operadores que trabalham no serviço Microsoft Azure têm obrigações de treinamento adicionais que envolvem seu acesso a sistemas confidenciais que hospedam dados do cliente.
  * A equipe de resposta de segurança da Microsoft recebe treinamento especializado para suas funções

Caso haja uma perda de dados de qualquer cliente, notificamos cada cliente dentro de um dia. No entanto, a perda de dados do cliente nunca ocorreu com o OMS. Além disso, mantemos cópias dos dados que foram criados e eles são distribuídos geograficamente.

Para obter mais informações sobre como a Microsoft responde a incidentes de segurança, confira [Microsoft Azure Security Response in the Cloud](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/1/Microsoft Azure Security Response in the cloud.pdf) (Resposta de segurança do Microsoft Azure na nuvem).

## <a name="compliance"></a>Conformidade
O programa de controle e segurança da informação da equipe de serviço e desenvolvimento de software do OMS dá suporte aos requisitos de negócios e obedece às leis e regulamentações conforme descrito em [Central de Confiabilidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/) e [Conformidade do Microsoft Trust Center](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx). Como o OMS estabelece os requisitos de segurança, identifica os controles de segurança, gerencia e monitora os riscos também são descritos lá. Anualmente, revisamos as políticas, padrões, procedimentos e diretrizes.

Cada membro de equipe de desenvolvimento do OMS recebe treinamento formal sobre a segurança de aplicativo. Internamente, usamos um sistema de controle de versão para o desenvolvimento de software. Cada projeto de software é protegido pelo sistema de controle de versão.

A Microsoft tem uma equipe de segurança e conformidade que monitora e analisa todos os serviços na Microsoft. Os responsáveis pela segurança de informações compõem a equipe e eles não são associados aos departamentos de engenharia que desenvolvem o OMS. Os responsáveis pela segurança têm sua própria cadeia de gerenciamento e realizaram avaliações independentes dos produtos e serviços para garantir a segurança e a conformidade.

O conselho de diretores da Microsoft é notificado por um relatório anual sobre todos os programas de segurança da informação na Microsoft.

A equipe de serviço e desenvolvimento de software do OMS trabalha ativamente com as equipes de conformidade e jurídica da Microsoft e outros parceiros do setor para adquirir várias certificações.

## <a name="certifications-and-attestations"></a>Certificações e atestados
O Log Analytics do OMS atende aos seguintes requisitos:

* [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/en-us/blog/iso22301/)
* [PCI DSS (Padrão de Segurança de dados do Setor de Cartões de Pagamento (Compatível com PCI))](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) pelo PCI Security Standards Council.
* Compatível com [SOC (Service Organization Controls) 1 Tipo 1 e SOC 2 Tipo 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2)
* [HIPAA e HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA) para empresas que tenham um contrato de sócio corporativo HIPAA
* Critérios de engenharia comum do Windows
* Microsoft Trustworthy Computing (a página pode estar em inglês)
* Como um serviço do Azure, os componentes que o OMS usa seguem os requisitos de conformidade do Azure. Você pode ler mais em [Conformidade do Microsoft Trust Center](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx).

> [!NOTE]
> Em algumas certificações/atestados, o Log Analytics está listado com o nome antigo de *Operational Insights*.
>
>


## <a name="cloud-computing-security-data-flow"></a>Fluxo de dados de segurança de computação em nuvem
O diagrama a seguir mostra uma arquitetura de segurança de nuvem como o fluxo de informações de sua empresa e como elas são protegidas à medida que se movem para o serviço Log Analytics e por fim vistas por você no portal OMS. Após o diagrama, mais informações sobre cada etapa.

![Imagem de coleta de dados e segurança da OMS](./media/log-analytics-security/log-analytics-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Inscrever-se no Log Analytics e coletar dados
Para a sua organização enviar dados para o Log Analytics, você configura agentes do Windows, agentes em execução em máquinas virtuais do Azure ou agentes do OMS para Linux. Se você usar agentes do Operations Manager, você usará um assistente de configuração no console Operações para configurá-los. Os usuários (que podem ser você, outros usuários individuais ou um grupo de pessoas) criam uma ou mais contas da OMS (espaços de trabalho do OMS) e registram agentes usando uma das seguintes contas:

* [ID Organizacional](../active-directory/sign-up-organization.md)
* [Conta da Microsoft - Outlook, Office Live, MSN](http://www.microsoft.com/account/default.aspx)

Um espaço de trabalho do OMS é onde os dados são coletados, agregados, analisados e apresentados. Um espaço de trabalho é usado principalmente como um meio para particionar dados e cada espaço de trabalho é exclusivo. Por exemplo, talvez você queira ter seus dados de produção gerenciados com um espaço de trabalho do OMS e seus dados de teste gerenciados com outro espaço de trabalho. Os espaços de trabalho também ajudam um administrador a controlar o acesso dos usuários aos dados. Cada espaço de trabalho pode ter várias contas de usuário associadas a ele, e cada conta de usuário pode acessar vários espaços de trabalho do OMS. Você cria os espaços de trabalho com base na região do datacenter. Cada espaço de trabalho é replicado para outros datacenters na região, principalmente para disponibilidade do serviço do OMS.

Para o Operations Manager, quando o assistente de configuração for concluído, cada grupo de gerenciamento do Operations Manager estabelecerá uma conexão com o serviço Log Analytics. Então, você poderá usar o Assistente para Adicionar Computadores para escolher quais computadores do grupo de gerenciamento poderão enviar dados para o serviço. Para outros tipos de agente, cada um conecta-se com segurança ao serviço OMS.

Toda a comunicação entre os sistemas conectados e o serviço do Log Analytics é criptografada.  O protocolo TLS (HTTPS) é usado para criptografia.  O processo SDL da Microsoft é seguido para garantir que o Log Analytics esteja atualizado com os avanços mais recentes nos protocolos criptográficos.

Cada tipo de agente coleta dados para o Log Analytics. Os tipos de dados coletados dependem dos tipos de soluções usadas. Você pode ver um resumo da coleta de dados em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md). Além disso, há informações mais detalhadas de coleção disponíveis para a maioria das soluções. Uma solução é um conjunto de exibições predefinidas, de consultas de pesquisa de log, de regras de coleta de dados e de lógica de processamento. Somente os administradores podem usar o Log Analytics para importar uma solução. Após a importação da solução, ela será movida para os servidores de gerenciamento do Operations Manager (se usado) e então para quaisquer agentes escolhidos por você. Depois disso, os agentes coletam os dados.

## <a name="2-send-data-from-agents"></a>2. Enviar dados de agentes
Registre todos os tipos de agente com uma chave de registro e uma conexão segura será estabelecida entre o agente e o serviço Log Analytics usando a autenticação baseada em certificado e SSL com a porta 443. O OMS usa um repositório secreto para gerar e manter as chaves. As chaves privadas são rotacionadas a cada 90 dias e armazenadas no Azure e são gerenciadas pelas operações do Azure que seguem práticas de conformidade e regulatórias estritas.

Com o Operations Manager, registre um espaço de trabalho com o serviço Log Analytics e uma conexão HTTPS segura será estabelecida entre o servidor de gerenciamento do Operations Manager.

Para agentes do Windows em execução em máquinas virtuais do Azure, uma chave de armazenamento somente leitura é usada para ler eventos de diagnóstico nas tabelas do Azure.

Se qualquer agente não puder se comunicar com o serviço por algum motivo, os dados coletados serão armazenados localmente em um cache temporário e o servidor de gerenciamento tentará reenviar os dados a cada oito minutos por duas horas. Os dados armazenados em cache do agente são protegidos pelo repositório de credenciais do sistema operacional. Se o serviço não puder processar os dados depois de duas horas, os agentes colocarão os dados em fila. Se a fila encher, o OMS começará a remover tipos de dados, começando com os dados de desempenho. O limite de fila do agente é uma chave do registro para que você possa modificá-lo, se necessário. Os dados coletados são compactados e enviados para o serviço, ignorando os bancos de dados locais para que ele não adicione qualquer carga a eles. Depois que os dados coletados forem enviados, eles serão removidos do cache.

Conforme descrito acima, os dados de seus agentes são enviados por SSL para datacenters do Microsoft Azure. Opcionalmente, você pode usar o ExpressRoute para fornecer segurança adicional para os dados. O ExpressRoute é uma maneira de se conectar diretamente ao Azure pela rede WAN existente, como um VPN MPLS (multi-protocol label switching), fornecida por um provedor de serviço de rede. Para obter mais informações, consulte [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. O serviço Log Analytics recebe e processa os dados
O serviço Log Analytics garante que os dados de entrada sejam de uma fonte confiável ao validar certificados e a integridade dos dados com a autenticação do Azure. Os dados brutos não processados são então armazenados como um blob no [Microsoft Azure Storage](../storage/common/storage-introduction.md) e não são criptografados. No entanto, cada blob do Azure Storage tem um conjunto exclusivo de chaves, que está acessível somente para esse usuário. Os tipos de dados armazenados dependem dos tipos de soluções importados e usados para coletar dados. Em seguida, o serviço Log Analytics processa os dados brutos para o blob do armazenamento do Azure.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Usar o Log Analytics para acessar os dados
Você pode entrar no Log Analytics no portal do OMS usando a conta organizacional ou uma conta da Microsoft que você configurou anteriormente. Todo o tráfego entre o portal do OMS e o Log Analytics no OMS é enviado por um canal HTTPS seguro. Ao usar o portal do OMS, uma ID de sessão é gerada no cliente do usuário (navegador da Web) e dados são armazenados em um cache local até que a sessão seja encerrada. Após o encerramento, o cache é excluído. Os cookies do lado do cliente, que não contêm informações de identificação pessoal, não são removidos automaticamente. Os cookies de sessão são marcados como HTTPOnly e são protegidos. Após um período ocioso predeterminado, a sessão do portal do OMS é encerrada.

Ao usar o portal do OMS, você pode exportar os dados para um arquivo CSV e pode acessar dados usando APIs de Pesquisa. A exportação de CSV está limitada a 50.000 linhas por exportação e os dados de API estão restritos a 5.000 linhas por pesquisa.

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Log Analytics](log-analytics-get-started.md) para saber mais sobre o Log Analytics e colocá-lo em funcionamento em minutos.
