---
title: Linha de base de segurança do Azure para Azure Data Box
description: A linha de base de segurança Azure Data Box fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: databox
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 0a05a4692bb4a09213532ed29b2501f6b8056857
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721983"
---
# <a name="azure-security-baseline-for-azure-data-box"></a>Linha de base de segurança do Azure para Azure Data Box

Essa linha de base de segurança aplica as diretrizes da [1,0 versão de avaliação de segurança do Azure](../security/benchmarks/overview-v1.md) para Azure data box. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure.
O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao Azure data box. **Controles** não aplicáveis a Azure data Box foram excluídos.

 
Para ver como Azure Data Box é completamente mapeado para o benchmark de segurança do Azure, consulte o [arquivo completo de mapeamento de linha de base de segurança Azure data Box](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usar fontes de sincronização de tempo aprovadas

**Diretrizes**: a Microsoft mantém a fonte de tempo usada para recursos do Azure, por exemplo, para carimbos de data/hora nos logs. Azure Data Box tempo poderá descompasso se não estiver conectado à rede que possa acessar o servidor NTP no local do cliente.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Orientação**: correspondente a cada etapa em seu data Box ordem, você pode executar várias ações para controlar o acesso ao pedido, auditar os eventos, acompanhar o pedido e interpretar os vários logs gerados.

- [Entenda o rastreamento e o log de eventos para seu Azure Data Box](data-box-logs.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Orientação**: correspondente a cada etapa em seu data Box ordem, você pode executar várias ações para controlar o acesso ao pedido, auditar os eventos, acompanhar o pedido e interpretar os vários logs gerados.

- [Entenda o rastreamento e o log de eventos para seu Azure Data Box](data-box-logs.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Orientação**: correspondente a cada etapa em seu data Box ordem, você pode executar várias ações para controlar o acesso ao pedido, auditar os eventos, acompanhar o pedido e interpretar os vários logs gerados.

- [Entenda o rastreamento e o log de eventos para seu Azure Data Box](data-box-logs.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Orientação**: correspondente a cada etapa em seu data Box ordem, você pode executar várias ações para controlar o acesso ao pedido, auditar os eventos, acompanhar o pedido e interpretar os vários logs gerados.

- [Entenda o rastreamento e o log de eventos para seu Azure Data Box](data-box-logs.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Diretrizes**: Mantenha um inventário das contas de usuário que têm acesso administrativo à sua Azure data box. Você pode usar o painel IAM (controle de acesso e identidade) no portal do Azure para sua assinatura para configurar o controle de acesso baseado em função do Azure (RBAC do Azure). As funções são aplicadas a usuários, grupos, entidades de serviço e identidades gerenciadas no Active Directory.

Você pode controlar quem pode acessar seu pedido quando a ordem é criada pela primeira vez. Configure as funções de RBAC do Azure em vários escopos para controlar o acesso à ordem de Data Box. Uma função de RBAC do Azure determina o tipo de acesso – leitura-gravação, somente leitura e gravação para um subconjunto de operações.

- [Entender as funções personalizadas](../role-based-access-control/custom-roles.md)

- [Como configurar o RBAC do Azure para pastas de trabalho](../sentinel/quickstart-get-visibility.md)

- [Entenda como configurar o controle de acesso na ordem](https://docs.microsoft.com/azure/databox/data-box-logs#set-up-access-control-on-the-order)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Diretrizes**: Azure Active Directory (Azure AD) não tem o conceito de senhas padrão. Outros recursos do Azure que exigem uma senha forçam a criação de uma senha com requisitos de complexidade e um comprimento mínimo de senha, que difere dependendo do serviço. Você é responsável por aplicativos de terceiros e serviços do Marketplace que podem usar senhas padrão.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretriz**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. Use a identidade e o gerenciamento de acesso da Central de Segurança do Azure para monitorar a quantidade de contas administrativas.

Além disso, para ajudá-lo a controlar contas administrativas dedicadas, você pode seguir as recomendações da Central de Segurança do Azure ou as políticas internas do Azure, como:

- Deve haver mais de um proprietário atribuído à sua assinatura
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de proprietário devem ser removidas de sua assinatura

- [Como usar a Central de Segurança do Azure para monitorar a identidade e o acesso (versão prévia)](../security-center/security-center-identity-access.md)

- [Como usar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: Use uma Paw (estação de trabalho com acesso privilegiado) com a autenticação multifator do Azure ad habilitada para fazer logon e configurar seus Azure data Box pedidos.

- [Estações de trabalho com acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Planejando uma implantação de autenticação multifator do Azure AD baseada em nuvem](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Orientação**: não aplicável, Azure data box não tem sua própria conta administrativa. Você o acessa por meio do portal do Azure. No entanto, você pode configurar sua assinatura do Azure para usar o Azure Active Directory (Azure AD) Privileged Identity Management (PIM) para a geração de logs e alertas quando ocorre uma atividade suspeita ou insegura no ambiente.

Além disso, use as detecções de risco do Azure Active Directory para ver alertas e relatórios sobre o comportamento do usuário suspeito.

- [Como implantar o Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Entenda as detecções de risco do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerenciar recursos do Azure provenientes somente de locais aprovados

**Diretriz**: Use localizações nomeadas de acesso condicional para permitir o acesso ao portal do Azure somente a agrupamentos lógicos de intervalos de endereços IP ou de países/regiões específicos.

- [Como configurar localizações nomeadas no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central, quando aplicável. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário.

- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure Active Directory (Azure AD) fornece logs para ajudá-lo a descobrir contas obsoletas. Além disso, use as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso de usuários pode ser examinado regularmente para garantir que somente os usuários corretos tenham acesso contínuo.

Para o dispositivo Data Box, não há suporte para isso em tempo real. Você pode examinar os logs no final do trabalho.

- [Entender os relatórios do Azure AD](/azure/active-directory/reports-monitoring/)

- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central, quando aplicável. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário.

Você tem acesso à atividade de entrada do Azure AD, às fontes de log de eventos de auditoria e de risco, que permitem a integração com o Azure Sentinel ou um SIEM de terceiros.

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuário do Azure AD e enviando logs de auditoria e logs de entrada para um espaço de trabalho Log Analytics. Você pode configurar os alertas de log desejados no Log Analytics.

Azure Data Box logs de serviço não são gravados no espaço de trabalho Log Analytics.

- [Como integrar os logs de atividades do Azure ao Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de entrada da conta

**Diretrizes**: para o desvio do comportamento de logon da conta no plano de controle (por exemplo, portal do Azure), use os recursos de proteção de identidade e de detecção de riscos do Azure Active Directory (Azure AD) para configurar respostas automatizadas para detectar ações suspeitas relacionadas a identidades de usuário. Você também pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada.

- [Como exibir o logon arriscado do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Diretrizes**: em breve para estar disponível; Sistema de Proteção de Dados do Cliente ainda não tem suporte para Azure Data Box. 

- [Lista de serviços suportados do Sistema de Proteção de Dados do Cliente](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Diretrizes**: a Microsoft gerencia a infraestrutura subjacente para Azure data box e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente. Quando Data Box estiver no local do cliente, siga as práticas recomendadas para garantir que os dados confidenciais que estão sendo transferidos sejam protegidos.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: a Microsoft gerencia a infraestrutura subjacente para Azure data box e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente. Quando Data Box estiver no local do cliente, siga as práticas recomendadas para garantir que os dados confidenciais que estão sendo transferidos sejam protegidos.

- [Entender a migração de dados no Azure Data Box](data-box-faq.md)

- [Visão geral da segurança do Data Box](data-box-security.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usar o RBAC do Azure para controlar o acesso a recursos 

**Orientação**: Verifique se você tem acesso de proprietário ou colaborador à assinatura para criar uma ordem de data box. Também é possível definir Data Box leitor e Data Box funções de colaborador no nível de recurso.

- [Entenda como começar a usar o Azure Data Box](data-box-quickstart-portal.md)

- [Entenda como configurar o controle de acesso](https://docs.microsoft.com/azure/databox/data-box-logs#set-up-access-control-on-the-order)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: criptografar informações confidenciais em repouso

**Diretrizes**: Azure data Box implementa a criptografia AES de 256 bits para dados em repouso.

Azure Data Box implementa a criptografia AES de 256 bits para dados em repouso.

Além disso, o Azure Data Box protege a chave de desbloqueio do dispositivo (também conhecida como senha do dispositivo) que é usada para bloquear o dispositivo por meio de uma chave de criptografia. Por padrão, a chave de desbloqueio de dispositivo para um pedido de Data Box é criptografada com uma chave gerenciada da Microsoft. Para obter mais controle sobre a chave de desbloqueio de dispositivo, você também pode fornecer uma chave gerenciada pelo cliente. As chaves gerenciadas pelo cliente devem ser criadas e armazenadas em um Azure Key Vault.
 

- [Entender Data Box proteção de dados](data-box-security.md)

- [Usar chaves gerenciadas pelo cliente no Azure Key Vault para Azure Data Box](data-box-customer-managed-encryption-key-portal.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem para Azure data Box, bem como outros recursos críticos ou relacionados.

- [Como criar alertas para eventos do log de atividades do Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Diretriz**: Configure o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de “Gerenciamento do Microsoft Azure”.

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="711-manage-azure-secrets-securely"></a>7.11: gerenciar segredos do Azure com segurança

**Diretrizes**: as chaves gerenciadas pelo cliente devem ser criadas e armazenadas em um Azure Key Vault.

- [Como usar chaves gerenciadas pelo cliente no Azure Key Vault para Azure Data Box](data-box-customer-managed-encryption-key-portal.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretrizes**: a Microsoft executa o verificador de credenciais em data Box código. Além disso, a Microsoft também protege as credenciais com segurança. Implemente o verificador de credenciais para identificar as credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte o [benchmark de segurança do Azure: defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: o antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, sistema de proteção de dados do cliente do Azure), no entanto, ele não é executado no conteúdo do cliente. 

É sua responsabilidade verificar previamente qualquer conteúdo que esteja sendo carregado em recursos não computados do Azure. A Microsoft não pode acessar dados do cliente e, portanto, não pode realizar verificações antimalware do conteúdo do cliente em seu nome.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretrizes**: Certifique-se de fazer backup de todos os dados e da chave gerenciada pelo cliente. Data Box não faz nenhum backup.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: você deve verificar se todos os dados estão na conta de armazenamento do Azure antes de excluí-los de seu local.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Diretrizes**: Certifique-se de que todos os backups ou chaves gerenciadas pelo cliente que você tem estão protegidos de acordo com as práticas recomendadas.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente. 

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Aproveitar o Guia de tratamento de incidentes de segurança de computador do NIST para ajudar na criação de seu plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretriz**: a Central de Segurança atribui uma severidade a cada alerta para ajudar você a priorizar quais alertas devem ser investigados primeiro. A gravidade se baseia em quão confiante a central de segurança está na localização ou na análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta. 

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) usando marcas e crie um sistema de nomeação para identificar claramente e categorizar os recursos do Azure, em especial aqueles que processam dados confidenciais.  É sua responsabilidade priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu.

- [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="103-test-security-response-procedures"></a>10.3: Testar procedimentos de resposta de segurança

**Diretriz**: Conduza regularmente exercícios para testar os recursos de resposta a incidentes de seus sistemas para ajudar a proteger seus recursos do Azure. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

- [Publicação do NIST - Guia para testar, treinar e exercitar programas para planos de TI e capacidades](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: As informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que seus dados foram acessados por uma pessoa não autorizada ou ilegal. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretriz**: Exporte seus alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua para ajudar a identificar riscos para os recursos do Azure. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na central de segurança do Azure para disparar automaticamente respostas por meio de "aplicativos lógicos" em alertas de segurança e recomendações para proteger os recursos do Azure.

- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte o [benchmark de segurança do Azure: testes de penetração e exercícios de equipe vermelho](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretrizes**: a Microsoft faz testes de penetração e verificação de vulnerabilidade em dispositivos data box. Você pode fazer seu próprio teste de penetração e verificação de vulnerabilidade. Se você optar por fazer isso, siga as regras de envolvimento da Microsoft para garantir que os testes de penetração não estejam violando as políticas da Microsoft. Use a estratégia da Microsoft e a execução de equipes vermelhas e testes de penetração de sites ativos em infraestrutura de nuvem, serviços e aplicativos gerenciados pela Microsoft.

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Saiba mais sobre a [Linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
