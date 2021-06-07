---
title: Controles de amostra de blueprint de Mídia
description: Mapeamento de controles das amostras de blueprint de Mídia. Cada controle é mapeado para uma ou mais definições do Azure Policy que auxiliam na avaliação.
ms.date: 04/13/2021
ms.topic: sample
ms.openlocfilehash: 4c04c661d7dd8b2b07a05c20524258d6bb887e62
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376691"
---
# <a name="control-mapping-of-the-media-blueprint-sample"></a>Mapeamento de controles da amostra de blueprint de Mídia

O artigo a seguir fornece detalhes sobre como a amostra de blueprint de Mídia do Azure Blueprints são mapeados para os controles de Mídia. Para obter mais informações sobre os controles, confira [Mídia](https://www.motionpictures.org/best-practices).

Os mapeamentos a seguir referem-se aos controles de **Mídia**. Use a navegação no lado direito para ir diretamente para um mapeamento de controle específico. Muitos dos controles mapeados são implementados com uma iniciativa do [Azure Policy](../../../policy/overview.md). Para examinar a iniciativa completa, abra **Política** no portal do Azure e selecione a página **Definições**. Em seguida, localize e selecione a iniciativa de política interna **\[Versão prévia\]: auditar controles de mídia**.

> [!IMPORTANT]
> Cada controle abaixo está associado com uma ou mais definições do [Azure Policy](../../../policy/overview.md). Essas políticas podem ajudar você a [avaliar a conformidade](../../../policy/how-to/get-compliance-data.md) com o controle. No entanto, geralmente não há uma correspondência um para um ou completa entre um controle e uma ou mais políticas. Dessa forma, **Conformidade** no Azure Policy refere-se somente às próprias políticas. Não garante que você está totalmente em conformidade com todos os requisitos de um controle. Além disso, o padrão de conformidade inclui controles que não são abordados por nenhuma definição do Azure Policy no momento. Portanto, a conformidade no Azure Policy é somente uma exibição parcial do status de conformidade geral. As associações entre controles e definições do Azure Policy desta amostra de blueprint de conformidade podem ser alteradas ao longo do tempo. Para exibir o histórico de alterações, confira o [Histórico de Confirmações do GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/media/control-mapping.md).

## <a name="access-control"></a>Controle de acesso

### <a name="ac-11--ensure-no-root-access-key-exists"></a>AC-1.1 – Não deve haver chave de acesso raiz

- \[Versão Prévia\]: Implantar pré-requisitos para auditar as VMs do Windows que não contêm os certificados especificados na Raiz Confiável

### <a name="ac-12---passwords-pins-and-tokens-must-be-protected"></a>AC-1.2 – Senhas, PINs e tokens devem ser protegidos

- \[Versão Prévia\]: Implantar pré-requisitos para auditar as VMs do Windows que não restringem o tamanho mínimo da senha a 14 caracteres

### <a name="ac-18---shared-account-access-is-prohibited"></a>AC-1.8 – O acesso à conta compartilhada é proibido

- Todas as regras de autorização, exceto a RootManageSharedAccessKey, devem ser removidas do namespace do Barramento de Serviço

### <a name="ac-19--system-must-restrict-access-to-authorized-users"></a>AC-1.9 – O sistema deve restringir o acesso a usuários autorizados.

- Auditar o acesso irrestrito à rede para contas de armazenamento

### <a name="ac--114--system-must-enforce-access-rights"></a>AC-1.14 – O sistema deve impor direitos de acesso.

- \[Versão Prévia\]: Implantar pré-requisitos para auditar as configurações de VMs do Windows em 'Atribuição de Direitos do Usuário'

### <a name="ac--115--prevent-unauthorized-access-to-security-relevant-information-or-functions"></a>AC-1.15 – Impedir o acesso não autorizado a informações ou funções relevantes de segurança.

- \[Versão Prévia\]: Mostrar os resultados da auditoria das configurações de VMs do Windows em 'Opções de Segurança – Configurações do sistema'

### <a name="ac-1-21---separation-of-duties-must-be-enforced-through-appropriate-assignment-of-role"></a>AC-1-21 – A separação de tarefas deve ser imposta por meio da atribuição apropriada da função.

- [Versão prévia\]: O RBAC (controle de acesso baseado em função) deve ser usado nos Serviços de Kubernetes

### <a name="ac-140--ensure-that-systems-are-not-connecting-trusted-network-and-untrusted-networks-at-the-same-time"></a>AC-1.40 – Os sistemas não devem estar conectando redes confiáveis e não confiáveis ao mesmo tempo.

- \[Versão Prévia\]: Implantar pré-requisitos para auditar as configurações de VMs do Windows em 'Opções de Segurança – Acesso à Rede'

### <a name="ac-142--ac--143---remote-access-for-non-employees-must-be-restricted-to-allow-access-only-to-specifically-approved-information-systems"></a>AC-1.42 e AC-1.43 – O acesso remoto para não funcionários deve ser restrito para permitir acesso apenas a sistemas de informações especificamente aprovados

- \[Versão Prévia\]: Mostrar os resultados da auditoria das VMs do Linux que permitem conexões remotas em contas sem senhas

### <a name="ac-150--log-security-related-events-for-all-information-system-components"></a>AC-1.50 – Registrar em log eventos relacionados à segurança para todos os componentes do sistema de informações.

- Os logs de diagnóstico nos Aplicativos Lógicos devem ser habilitados

### <a name="ac-154--ensure-multi-factor-authentication-mfa-is-enabled-for-all-cloud-console-users"></a>AC-1.54 – A MFA (autenticação multifator) deve estar habilitada para todos os usuários do console de nuvem.

- A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura
- A autenticação Multifator do Microsoft Azure (MFA) deve estar habilitada para todas as contas de assinatura com privilégios de gravação para evitar uma quebra de contas ou recursos.

## <a name="auditing--logging"></a>Auditoria e registro em log

### <a name="al-21--successful-and-unsuccessful-events-must-be-logged"></a>AL-2.1 – Eventos bem-sucedidos e malsucedidos devem ser registrados em log.

- Os logs de diagnóstico nos serviços Pesquisa devem ser habilitados

### <a name="al--216---network-devicesinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-network-deviceinstance-elbs-web-application-firewalls-etc"></a>AL-2.16 – As instâncias/dispositivos de rede devem registrar qualquer evento classificado como um evento de segurança crítico por esse dispositivo/instância de rede (ELBs, firewalls do aplicativo Web etc.)

- \[Versão Prévia\]: Mostrar os resultados da auditoria das configurações de VMs do Windows em 'Opções de Segurança – Contas'

### <a name="al-217--serversinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-serverinstance"></a>AL-2.17 – Servidores/instâncias devem registrar qualquer evento classificado como um evento de segurança crítico por esse servidor/instância

- \[Versão Prévia\]: Mostrar os resultados da auditoria das configurações de VMs do Windows em 'Opções de Segurança – Contas'

### <a name="al-219---domain-events-must-log-any-event-classified-as-a-critical-or-high-security-event-by-the-domain-management-software"></a>AL-2.19 – Os eventos de domínio devem registrar qualquer evento classificado como um evento de segurança elevada ou crítica pelo software de gerenciamento de domínio

- \[Versão Prévia\]: Mostrar os resultados da auditoria das configurações de VMs do Windows em 'Opções de Segurança – Contas'
- \[Versão Prévia\]: Implantar pré-requisitos para auditar as configurações de VMs do Windows em 'Opções de Segurança – Cliente de Rede Microsoft'

### <a name="al-220--domain-events-must-log-any-event-classified-as-a-critical-security-event-by-domain-security-controls"></a>AL-2.20 – Os eventos de domínio devem registrar qualquer evento classificado como um evento de segurança crítico por controles de segurança de domínio

- \[Versão Prévia\]: Mostrar os resultados da auditoria das configurações de VMs do Windows em 'Opções de Segurança – Contas'

### <a name="al-221--domain-events-must-log-any-access-or-changes-to-the-domain-log"></a>AL-2.21 – Os eventos de domínio devem registrar qualquer acesso ou alterações no log de domínio

- \[Versão Prévia\]: Mostrar os resultados da auditoria das configurações de VMs do Windows em 'Opções de Segurança – Console de Recuperação'

## <a name="cryptographic-controls"></a>Controles de criptografia

### <a name="cc-42--applications-and-systems-must-use-current-cryptographic-solutions-for-protecting-data"></a>CC-4.2 – Aplicativos e sistemas devem usar as soluções de criptografia atuais para proteger os dados.

- A Transparent Data Encryption em bancos de dados SQL deve ser habilitada
- A Transparent Data Encryption deve ser habilitada para proteger os dados em repouso e atender aos requisitos de conformidade

### <a name="cc-45--digital-certificates-must-be-signed-by-an-approved-certificate-authority"></a>CC-4.5 – Os certificados digitais devem ser assinados por uma autoridade de certificação aprovada.

- \[Versão Prévia\]: Mostrar os resultados da auditoria das VMs do Windows que contêm certificados que expiram dentro do número de dias especificado

### <a name="cc-46--digital-certificates-must-be-uniquely-assigned-to-a-user-or-device"></a>CC-4.6 – Os certificados digitais devem ser atribuídos exclusivamente a um usuário ou dispositivo.

- \[Versão Prévia\]: Implantar pré-requisitos para auditar as VMs do Windows que contêm certificados com expiração dentro do número de dias especificado

### <a name="cc-47--cryptographic-material-must-be-stored-to-enable-decryption-of-the-records-for-the-length-of-time-the-records-are-retained"></a>CC-4.7 – O material criptográfico deve ser armazenado para habilitar a descriptografia dos registros durante o período pelo qual os registros são retidos.

- A criptografia de disco deve ser aplicada em máquinas virtuais
- As VMs sem uma criptografia de disco ativada serão monitoradas pela Central de Segurança do Azure como recomendações

### <a name="cc-48--secret-and-private-keys-must-be-stored-securely"></a>CC-4.8 – As chaves secretas e privadas devem ser armazenadas com segurança.

- A Transparent Data Encryption em bancos de dados SQL deve ser habilitada
- A Transparent Data Encryption deve ser habilitada para proteger os dados em repouso e atender aos requisitos de conformidade

## <a name="change--config-management"></a>Gerenciamento de alterações e configuração

### <a name="cm-52--only-authorized-users-may-implement-approved-changes-on-the-system"></a>CM-5.2 – Somente usuários autorizados podem implementar alterações aprovadas no sistema.

- As atualizações do sistema devem ser instaladas em suas máquinas
- A falta de atualizações do sistema de segurança em seus servidores será monitorada pela Central de Segurança do Azure como recomendações

### <a name="cm-512--maintain-an-up-to-date-complete-accurate-and-readily-available-baseline-configuration-of-the-information-system"></a>CM-5.12 – Manter uma configuração de linha de base atualizada, completa, precisa e prontamente disponível do sistema de informações.

- As atualizações do sistema devem ser instaladas em suas máquinas
- A falta de atualizações do sistema de segurança em seus servidores será monitorada pela Central de Segurança do Azure como recomendações

### <a name="cm-513--employ-automated-tools-to-maintain-a-baseline-configuration-of-the-information-system"></a>CM-5.13 – Usar ferramentas automatizadas para manter uma configuração de linha de base do sistema de informações.

- As atualizações do sistema devem ser instaladas em suas máquinas
- A falta de atualizações do sistema de segurança em seus servidores será monitorada pela Central de Segurança do Azure como recomendações

### <a name="cm-514--identify-and-disable-unnecessary-andor-non-secure-functions-ports-protocols-and-services"></a>CM-5.14 – Identificar e desabilitar funções, portas, protocolos e serviços desnecessários e/ou não seguros.

- Os adaptadores de rede devem desabilitar o encaminhamento IP
- \[Versão Prévia\]: O encaminhamento IP na máquina virtual deve ser desabilitado

### <a name="cm-519--monitor-changes-to-the-security-configuration-settings"></a>CM-5.19 – Monitorar alterações nas definições de configuração de segurança.

- Implantar configurações de diagnóstico para grupos de segurança de rede

### <a name="cm-522--ensure-that-only-authorized-software-and-updates-are-installed-on-company-systems"></a>CM-5.22 – Verifique se apenas software e atualizações autorizados estão instalados nos sistemas da empresa.

- As atualizações do sistema devem ser instaladas em suas máquinas
- A falta de atualizações do sistema de segurança em seus servidores será monitorada pela Central de Segurança do Azure como recomendações

## <a name="identity--authentication"></a>Identidade e autenticação

### <a name="ia-71--user-accounts-must-be-uniquely-assigned-to-individuals-for-access-to-information-that-is-not-classified-as-public-account-ids-must-be-constructed-using-a-standardized-logical-format"></a>IA-7.1 – As contas de usuário devem ser atribuídas exclusivamente a indivíduos para acesso a informações que não são classificadas como Públicas. As IDs de conta devem ser construídas usando um formato lógico padronizado.

- As contas externas com permissões de proprietário devem ser removidas de sua assinatura
- Contas externas com permissões de proprietário devem ser removidas da sua assinatura para evitar o acesso não monitorado.

## <a name="network-security"></a>Segurança de rede

### <a name="ns-92--access-to-network-device-management-functionality-is-restricted-to-authorized-users"></a>NS-9.2 – O acesso à funcionalidade de gerenciamento de dispositivo de rede é restrito a usuários autorizados.

- \[Versão Prévia\]: Implantar pré-requisitos para auditar as configurações de VMs do Windows em 'Opções de Segurança – Acesso à Rede'

### <a name="ns-93--all-network-devices-must-be-configured-using-their-most-secure-configurations"></a>NS-9.3 – Todos os dispositivos de rede devem ser configurados usando suas configurações mais seguras.

- \[Versão Prévia\]: Implantar pré-requisitos para auditar as configurações de VMs do Windows em 'Opções de Segurança – Acesso à Rede'

### <a name="ns-95--all-network-connections-to-a-system-through-a-firewall-must-be-approved-and-audited-on-a-regular-basis"></a>NS-9.5 – Todas as conexões de rede com um sistema por meio de um firewall devem ser aprovadas e auditadas regularmente.

- \[Versão Prévia\]: Mostrar os resultados da auditoria das configurações de VMs do Windows em 'Propriedades do Firewall do Windows'

### <a name="ns-97--appropriate-controls-must-be-present-at-any-boundary-between-a-trusted-network-and-any-untrusted-or-public-network"></a>NS-9.7 – Os controles apropriados devem estar presentes em qualquer limite entre uma rede confiável e qualquer rede pública ou não confiável.

- \[Versão Prévia\]: Implantar pré-requisitos para auditar as configurações de VMs do Windows em 'Propriedades do Firewall do Windows'

## <a name="security-planning"></a>Planejamento de segurança

### <a name="sp-113--threats-must-be-identified-that-could-negatively-impact-the-confidentiality-integrity-or-availability-of-company-information-and-content-along-with-the-likelihood-of-their-occurrence"></a>SP-11.3 – Devem ser identificadas as ameaças que poderiam afetar negativamente a confidencialidade, a integridade ou a disponibilidade das informações e do conteúdo da empresa, juntamente com a probabilidade de sua ocorrência.

- Os tipos de Proteção Avançada contra Ameaças devem estar definidos como “Todos” nas configurações de Segurança de Dados Avançada da instância gerenciada de SQL

### <a name="security-continuity"></a>Continuidade de segurança

## <a name="sc-125--data-in-long-term-storage-must-be-accessible-throughout-the-retention-period-and-protected-against-media-degradation-and-technology-changes"></a>SC-12.5 – Os dados no armazenamento de longo prazo devem estar acessíveis ao longo do período de retenção e protegidos contra a degradação de mídia e alterações tecnológicas.

- Os servidores SQL devem ser configurados com um período de retenção de auditoria superior a 90 dias.
- Audite os servidores SQL configurados com um período de retenção de auditoria menor que 90 dias.

## <a name="system-integrity"></a>Integridade do Sistema

### <a name="si-143--only-authorized-personnel-may-monitor-network-and-user-activities"></a>SI-14.3 – Somente pessoal autorizado pode monitorar as atividades de rede e de usuário.

- As vulnerabilidades nos bancos de dados SQL devem ser corrigidas
- Monitore os resultados da verificação da avaliação de vulnerabilidade e as recomendações sobre como corrigir vulnerabilidades do banco de dados.

### <a name="si-144--internet-facing-systems-must-have-intrusion-detection"></a>SI-14.4 – Os sistemas voltados para a Internet devem ter detecção de intrusão.

- Implantar a Detecção de Ameaças em servidores SQL

### <a name="si-1413--standardized-centrally-managed-anti-malware-software-should-be-implemented-across-the-company"></a>SI-14.13 – O software antimalware com gerenciamento centralizado e padronizado deve ser implementado em toda a empresa.

- Implantar a extensão padrão antimalware de IaaS da Microsoft para Windows Server

### <a name="si-1414--anti-malware-software-must-scan-computers-and-media-weekly-at-a-minimum"></a>SI-14.14 – O software antimalware deve examinar computadores e mídia pelo menos semanalmente.

- Implantar a extensão padrão antimalware de IaaS da Microsoft para Windows Server

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

### <a name="vm-154--ensure-that-applications-are-scanned-for-vulnerabilities-on-a-monthly-basis"></a>VM-15.4 – Os aplicativos devem ser verificados em busca de vulnerabilidades mensalmente.

- As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas
- Faça a auditoria das vulnerabilidades do SO nos seus conjuntos de dimensionamento de máquinas virtuais para protegê-los contra ataques.

### <a name="vm-155--ensure-that-vulnerabilities-are-identified-paired-to-threats-and-evaluated-for-risk"></a>VM-15.5 – Garanta que as vulnerabilidades sejam identificadas, combinadas com ameaças e avaliadas quanto ao risco.

- As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas
- Faça a auditoria das vulnerabilidades do SO nos seus conjuntos de dimensionamento de máquinas virtuais para protegê-los contra ataques.

### <a name="vm-156--ensure-that-identified-vulnerabilities-have-been-remediated-within-a-mutually-agreed-upon-timeline"></a>VM-15.6 – Verifique se as vulnerabilidades identificadas foram corrigidas dentro de um período mutuamente acordado.

- As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas
- Faça a auditoria das vulnerabilidades do SO nos seus conjuntos de dimensionamento de máquinas virtuais para protegê-los contra ataques.

### <a name="vm-157--access-to-and-use-of-vulnerability-management-systems-must-be-restricted-to-authorized-personnel"></a>VM-15.7 – O acesso e o uso de sistemas de gerenciamento de vulnerabilidades devem ser restritos à equipe autorizada.

- As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas
- Faça a auditoria das vulnerabilidades do SO nos seus conjuntos de dimensionamento de máquinas virtuais para protegê-los contra ataques.

> [!NOTE]
> A disponibilidade de definições específicas do Azure Policy pode variar no Azure Governamental e em outras nuvens nacionais. 

## <a name="next-steps"></a>Próximas etapas

Você examinou o mapeamento de controles da amostra de blueprint de Mídia. A seguir, acesse os seguintes artigos para saber mais sobre a visão geral e como implantar essa amostra:

> [!div class="next step action"]
> [Blueprint de Mídia – Mapeamento de controle](./control-mapping.md)
> [Blueprint de Mídia – Etapas de implantação](./deploy.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).
