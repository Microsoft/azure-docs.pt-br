---
title: Linha de base de segurança do Azure para Cloud Shell
description: A linha de base de segurança Cloud Shell fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b0045eacea7fdc13abdad35b5567eba3e994dcf9
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558866"
---
# <a name="azure-security-baseline-for-cloud-shell"></a>Linha de base de segurança do Azure para Cloud Shell

Essa linha de base de segurança aplica as diretrizes da [1,0 versão de avaliação de segurança do Azure](../security/benchmarks/overview-v1.md) para Cloud Shell. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure.
O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao Cloud Shell. **Controles** não aplicáveis a Cloud Shell foram excluídos.

 
Para ver como Cloud Shell é completamente mapeado para o benchmark de segurança do Azure, consulte o [arquivo completo de mapeamento de linha de base de segurança Cloud Shell](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Orientação**: os clientes podem implantar Azure cloud Shell em uma rede virtual de Propriedade do cliente.

Ao implantar Azure Cloud Shell em uma rede virtual de Propriedade do cliente, você deve criar ou usar uma rede virtual existente. Certifique-se de que a rede virtual escolhida tenha um grupo de segurança de rede aplicado a suas sub-redes e controles de acesso à rede configurados específicos para as portas e fontes confiáveis do seu aplicativo.

- [Implantar Cloud Shell em uma rede virtual do Azure](private-vnet.md)

- [Como criar um grupo de segurança de rede com regras de segurança](../virtual-network/tutorial-filter-network-traffic.md)

- [Como implantar e configurar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usar o SSO (logon único) com o Azure Active Directory

**Diretrizes**: Azure cloud Shell é uma experiência de linha de comando baseada em navegador que usa a mesma autorização usada para acessar o portal do Azure, nesse caso, um SSO no portal do Azure também irá autenticar você com Cloud Shell.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

**Diretrizes**: Azure cloud Shell é uma experiência de linha de comando baseada em navegador que usa a mesma autorização usada para acessar o portal do Azure, nesse caso, qualquer autenticação multifator necessária para se conectar ao portal do Azure também será necessária para Cloud Shell.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte o [benchmark de segurança do Azure: gerenciamento de vulnerabilidade](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: executar ferramentas automatizadas de verificação de vulnerabilidade

**Diretrizes**: Azure cloud Shell é uma experiência de linha de comando baseada em navegador que é usada para gerenciamento interativo de recursos de nuvem.  Cada contêiner de cliente é efêmero, um novo contêiner é usado para cada sessão.  As imagens de contêiner são monitoradas e atualizadas pela equipe de Cloud Shell.

Azure Cloud Shell permite que os clientes instalem suas próprias ferramentas ou software em suas próprias imagens de acordo com suas necessidades organizacionais.

Os clientes são responsáveis por executar ferramentas automatizadas de verificação de vulnerabilidade em relação ao software em execução no ambiente.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: implantar uma solução de gerenciamento de patch automatizada para títulos de software de terceiros

**Orientação**: não aplicável; Azure Cloud Shell é uma experiência de linha de comando baseada em navegador que é usada para gerenciamento interativo de recursos de nuvem.  Cada contêiner de cliente é efêmero, um novo contêiner é usado para cada sessão.  As imagens de contêiner são monitoradas e atualizadas pela equipe de Cloud Shell.

Azure Cloud Shell permite que os clientes instalem suas próprias ferramentas ou software em suas próprias imagens de acordo com suas necessidades organizacionais.

Os clientes são responsáveis pelo gerenciamento de patches de software em execução em seu ambiente.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: comparar verificações de vulnerabilidade consecutivas

**Diretrizes**: Azure cloud Shell é uma experiência de linha de comando baseada em navegador que é usada para gerenciamento interativo de recursos de nuvem.  Cada contêiner de cliente é efêmero, um novo contêiner é usado para cada sessão.  As imagens de contêiner são monitoradas e atualizadas pela equipe de Cloud Shell.

Azure Cloud Shell permite que os clientes instalem suas próprias ferramentas ou software em suas próprias imagens de acordo com suas necessidades organizacionais.

Os clientes são responsáveis por corrigir as vulnerabilidades descobertas por meio de suas verificações de vulnerabilidade de software. Exporte os resultados da verificação em intervalos consistentes e compare os resultados com verificações anteriores para verificar se as vulnerabilidades foram corrigidas. Ao usar as recomendações de gerenciamento de vulnerabilidade sugeridas pela central de segurança do Azure, você pode dinamizar o portal da solução selecionada para exibir dados de verificação históricas.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretrizes**: Azure cloud Shell é uma experiência de linha de comando baseada em navegador que é usada para gerenciamento interativo de recursos de nuvem.  Cada contêiner de cliente é efêmero, um novo contêiner é usado para cada sessão.  As imagens de contêiner são monitoradas e atualizadas pela equipe de Cloud Shell.

Azure Cloud Shell permite que os clientes instalem suas próprias ferramentas ou software em suas próprias imagens de acordo com suas necessidades organizacionais.

Os clientes são responsáveis por corrigir as vulnerabilidades descobertas por meio de suas verificações de vulnerabilidade de software.  Use um programa de Pontuação de risco comum (por exemplo, sistema comum de Pontuação de vulnerabilidade) ou as classificações de risco padrão fornecidas por sua ferramenta de verificação de terceiros. 

- [Publicação NIST – sistema de Pontuação de vulnerabilidade comum](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definir e manter um inventário de recursos aprovados do Azure

**Diretrizes**: Azure cloud Shell é uma experiência de linha de comando baseada em navegador que é usada para gerenciamento interativo de recursos de nuvem.  Cada contêiner de cliente é efêmero, um novo contêiner é usado para cada sessão.  As imagens e as ferramentas de contêiner são monitoradas e atualizadas pela equipe de Cloud Shell.  O cliente é capaz de instalar suas próprias ferramentas em suas próprias imagens de acordo com suas necessidades organizacionais e as ferramentas não exigem `sudo` permissões durante a instalação.

Os clientes são recomendados para criar um inventário de softwares aprovados que são instalados por meio de Azure Cloud Shell de acordo com suas necessidades organizacionais.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorar aplicativos de software não aprovados nos recursos de computação

**Diretrizes**: Azure cloud Shell é um serviço gratuito sem ativos de Propriedade do cliente.  As imagens e as ferramentas de contêiner são monitoradas e atualizadas pela equipe de Cloud Shell. 

Azure Cloud Shell permite que os clientes instalem suas próprias ferramentas ou software em suas próprias imagens de acordo com suas necessidades organizacionais.

Os clientes são responsáveis por monitorar aplicativos de software em execução no ambiente para garantir que eles sejam aprovados por política da organização.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: remover recursos e aplicativos de software não aprovados do Azure

**Diretrizes**: Azure cloud Shell é um serviço gratuito sem ativos de Propriedade do cliente.  As imagens e as ferramentas de contêiner são monitoradas e atualizadas pela equipe de Cloud Shell. 

Azure Cloud Shell permite que os clientes instalem suas próprias ferramentas ou software em suas próprias imagens de acordo com suas necessidades organizacionais.

Os clientes são responsáveis por monitorar aplicativos de software em execução no ambiente para garantir que o software não aprovado seja gerenciado por política da organização.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="68-use-only-approved-applications"></a>6.8: usar somente aplicativos aprovados

**Diretrizes**: Azure cloud Shell é um serviço gratuito sem ativos de Propriedade do cliente.  As imagens e as ferramentas de contêiner são monitoradas e atualizadas pela equipe de Cloud Shell.  As ferramentas específicas podem não ser removidas pelo cliente.

Azure Cloud Shell permite que os clientes instalem suas próprias ferramentas ou aplicativos em suas próprias imagens de acordo com suas necessidades organizacionais.

Os clientes são responsáveis por monitorar aplicativos em execução no ambiente para garantir que eles sejam aprovados por política da organização.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: manter um inventário de títulos de software aprovados

**Orientação**: não aplicável; Azure Cloud Shell é uma experiência de linha de comando baseada em navegador que é usada para gerenciamento interativo de recursos de nuvem.  Cada contêiner de cliente é efêmero, um novo contêiner é usado para cada sessão.  As imagens de contêiner são monitoradas e atualizadas pela equipe de Cloud Shell.

Azure Cloud Shell permite que os clientes instalem suas próprias ferramentas ou software em suas próprias imagens de acordo com suas necessidades organizacionais.

Os clientes são responsáveis por manter um inventário de softwares aprovados em execução no ambiente para verificar se eles são softwares aprovados por política da organização.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: limitar a capacidade dos usuários de executar scripts em recursos de computação

**Diretrizes**: Azure cloud Shell é uma experiência de linha de comando baseada em navegador que é usada para gerenciamento interativo de recursos de nuvem.  As ações executadas dentro de Cloud Shell funcionam da mesma forma que as ações executadas das mesmas ferramentas ou linguagens executadas em um ambiente local.  As ações de ferramentas e idiomas individuais devem ser restritas, os clientes não podem restringir o acesso a Cloud Shell ou restringir o que está disponível para um usuário.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separar física ou logicamente os aplicativos de alto risco

**Diretrizes**: Azure cloud shell pode ser isolada em uma rede virtual do cliente.

- [Implantar Cloud Shell em uma rede virtual do Azure](private-vnet.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretrizes**: Cloud Shell permite que os scripts sejam executados no, criados e carregados no ambiente de Cloud Shell.  Mover credenciais para Azure Key Vault é nossa recomendação.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte o [benchmark de segurança do Azure: defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: usar software antimalware gerenciado centralmente

**Diretrizes**: Azure cloud Shell é uma experiência de linha de comando baseada em navegador que é usada para gerenciamento interativo de recursos de nuvem.  Cada contêiner de cliente é efêmero, um novo contêiner é usado para cada sessão.  As imagens e as ferramentas de contêiner são monitoradas e atualizadas pela equipe de Cloud Shell.  O cliente é capaz de instalar suas próprias ferramentas em suas próprias imagens de acordo com suas necessidades organizacionais e as ferramentas não exigem `sudo` permissões durante a instalação.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: garantir que o software antimalware e as assinaturas sejam atualizados

**Diretrizes**: Azure cloud Shell é uma experiência de linha de comando baseada em navegador que é usada para gerenciamento interativo de recursos de nuvem.  Cada contêiner de cliente é efêmero, um novo contêiner é usado para cada sessão.  As imagens e as ferramentas de contêiner são monitoradas e atualizadas pela equipe de Cloud Shell.  O cliente é capaz de instalar suas próprias ferramentas em suas próprias imagens de acordo com suas necessidades organizacionais e as ferramentas não exigem `sudo` permissões durante a instalação.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

- [Como configurar automações de fluxo de trabalho na Central de Segurança do Azure](../security-center/security-center-planning-and-operations-guide.md) 

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [O cliente também pode aproveitar o guia de tratamento de incidentes de segurança do computador da NIST para ajudar na criação de seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretriz**: a Central de Segurança atribui uma severidade a cada alerta para ajudar você a priorizar quais alertas devem ser investigados primeiro. A gravidade é baseada em quão confiante a central de segurança está na localização ou na forma analítica usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) e crie um sistema de nomenclatura para identificar e categorizar claramente os recursos do Azure.

- [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md) 

- [Usar marcas para organizar seus recursos do Azure](../azure-resource-manager/management/tag-resources.md)

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

**Diretriz**: exporte os alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Azure Sentinel.

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

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)