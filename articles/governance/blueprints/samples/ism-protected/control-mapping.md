---
title: Controles do exemplo de blueprint ISM PROTECTED do governo australiano
description: Mapeamento de controle de exemplo do blueprint ISM PROTECTED do governo australiano. Cada controle é mapeado para uma ou mais definições do Azure Policy que auxiliam na avaliação.
ms.date: 04/02/2021
ms.topic: sample
ms.openlocfilehash: ecb6a912d0b1f319022f6b7deff68e1495dccbca
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106386442"
---
# <a name="control-mapping-of-the-australian-government-ism-protected-blueprint-sample"></a>Mapeamento de controle da amostra de blueprint ISM PROTECTED do governo australiano

O artigo a seguir fornece detalhes sobre como a amostra de blueprint ISM PROTECTED do governo australiano do Azure Blueprints é mapeada para os controles ISM PROTECTED. Para obter mais informações sobre os controles, confira [ISM PROTECTED](https://www.cyber.gov.au/ism).

Os mapeamentos a seguir são para os controles **ISM PROTECTED**. Use a navegação no lado direito para ir diretamente para um mapeamento de controle específico. Muitos dos controles mapeados são implementados com uma iniciativa do [Azure Policy](../../../policy/overview.md). Para examinar a iniciativa completa, abra **Política** no portal do Azure e selecione a página **Definições**. Em seguida, localize e selecione a iniciativa de política interna **\[Versão prévia\]: Auditar os controles PROTEGIDOS do ISM do governo australiano e implantar Extensões de VM específicas para dar suporte à iniciativa de política integrados dos requisitos de auditoria**.

> [!IMPORTANT]
> Cada controle abaixo está associado com uma ou mais definições do [Azure Policy](../../../policy/overview.md). Essas políticas podem ajudar você a [avaliar a conformidade](../../../policy/how-to/get-compliance-data.md) com o controle. No entanto, geralmente não há uma correspondência um para um ou completa entre um controle e uma ou mais políticas. Dessa forma, **Conformidade** no Azure Policy refere-se somente às próprias políticas. Não garante que você está totalmente em conformidade com todos os requisitos de um controle. Além disso, o padrão de conformidade inclui controles que não são abordados por nenhuma definição do Azure Policy no momento. Portanto, a conformidade no Azure Policy é somente uma exibição parcial do status de conformidade geral. As associações entre controles e definições do Azure Policy desta amostra de blueprint de conformidade podem ser alteradas ao longo do tempo. Para exibir o histórico de alterações, confira o [Histórico de Confirmações do GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ism-protected/control-mapping.md).


## <a name="location-constraints"></a>Restrições de localização

Este blueprint ajuda a restringir a localização da implantação de todos os recursos e grupos de recursos para a "Austrália Central", a "Austrália Central2", o "Leste da Austrália" e o "Sudeste da Austrália" atribuindo as seguintes definições de Azure Policy:

- Locais permitidos (foram embutidos em código para "Austrália Central", "Austrália Central2", "Leste da Austrália" e "Sudeste da Austrália")
- Locais permitidos para grupos de recursos (foram embutidos em código para "Austrália Central", "Austrália Central2", "Leste da Austrália" e "Sudeste da Austrália")

## <a name="guidelines-for-personnel-security---access-to-systems-and-their-resources"></a>Diretrizes para segurança do pessoal – acesso a sistemas e seus recursos

### <a name="0414-personnel-granted-access-to-a-system-and-its-resources-are-uniquely-identifiable"></a>0414 O pessoal concedeu acesso a um sistema e seus recursos são exclusivamente identificáveis

- O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura
- A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura
- O MFA deve ser habilitado em contas com permissões de leitura em sua assinatura

### <a name="1503-standard-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1503 O acesso padrão a sistemas, aplicativos e repositórios de dados é limitado ao necessário para a equipe realizar suas tarefas

- Um máximo de três proprietários deve ser designado para sua assinatura
- Deve haver mais de um proprietário atribuído à sua assinatura
- Mostrar os resultados da auditoria das VMs do Windows nas quais o grupo Administradores contém qualquer um dos membros especificados
- Implantar os pré-requisitos para auditar as VMs do Windows nas quais o grupo Administradores contém qualquer um dos membros especificados

### <a name="1507-privileged-access-to-systems-applications-and-data-repositories-is-validated-when-first-requested-and-revalidated-on-an-annual-or-more-frequent-basis"></a>1507 O acesso privilegiado a sistemas, aplicativos e repositórios de dados é validado quando solicitado pela primeira vez e revalidado anualmente ou com mais frequência

- Mostrar os resultados da auditoria das VMs do Windows nas quais o grupo Administradores contém qualquer um dos membros especificados
- Implantar os pré-requisitos para auditar as VMs do Windows nas quais o grupo Administradores contém qualquer um dos membros especificados

### <a name="1508-privileged-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1508 O acesso privilegiado padrão a sistemas, aplicativos e repositórios de dados é limitado ao necessário para a equipe realizar suas tarefas

- Um máximo de três proprietários deve ser designado para sua assinatura
- Deve haver mais de um proprietário atribuído à sua assinatura
- Mostrar os resultados da auditoria das VMs do Windows nas quais o grupo Administradores contém qualquer um dos membros especificados
- Implantar os pré-requisitos para auditar as VMs do Windows nas quais o grupo Administradores contém qualquer um dos membros especificados
- O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais

### <a name="0415-the-use-of-shared-user-accounts-is-strictly-controlled-and-personnel-using-such-accounts-are-uniquely-identifiable"></a>0415 O uso de contas de usuário compartilhadas é estritamente controlado e a equipe que usa essas contas é exclusivamente identificável

- Mostrar os resultados da auditoria das VMs do Windows nas quais o grupo Administradores contém qualquer um dos membros especificados
- Implantar os pré-requisitos para auditar as VMs do Windows nas quais o grupo Administradores contém qualquer um dos membros especificados

### <a name="0445-privileged-users-are-assigned-a-dedicated-privileged-account-to-be-used-solely-for-tasks-requiring-privileged-access"></a>0445 Os usuários privilegiados são atribuídos a uma conta privilegiada dedicada a ser usada exclusivamente para tarefas que exigem acesso privilegiado

- Mostrar os resultados da auditoria das VMs do Windows nas quais o grupo Administradores contém qualquer um dos membros especificados
- Implantar os pré-requisitos para auditar as VMs do Windows nas quais o grupo Administradores contém qualquer um dos membros especificados

### <a name="0430-access-to-systems-applications-and-data-repositories-is-removed-or-suspended-on-the-same-day-personnel-no-longer-have-a-legitimate-requirement-for-access"></a>0430 O acesso a sistemas, aplicativos e repositórios de dados é removido ou suspenso no mesmo dia em que o funcionário não tem mais uma necessidade legítima de acesso

- As contas preteridas devem ser removidas de sua assinatura
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura

### <a name="0441-when-personnel-are-granted-temporary-access-to-a-system-effective-security-controls-are-put-in-place-to-restrict-their-access-to-only-information-required-for-them-to-undertake-their-duties"></a>0441 Quando a equipe recebe acesso temporário a um sistema, são estabelecidos controles de segurança efetivos para restringir o acesso a apenas as informações necessárias para que eles realizem suas tarefas

- As contas externas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de gravação devem ser removidas de sua assinatura
- As contas preteridas devem ser removidas de sua assinatura
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura

## <a name="guidelines-for-system-hardening---operating-system-hardening"></a>Diretrizes para a proteção do sistema – proteção do sistema operacional

### <a name="1407-the-latest-version-n-or-n-1-version-of-an-operating-system-is-used-for-standard-operating-environments-soes"></a>1407 A versão mais recente (N) ou a versão N-1 de um sistema operacional é usada para SOEs (ambientes operacionais padrão)

- As atualizações do sistema devem ser instaladas em suas máquinas
- As atualizações do sistema nos conjuntos de dimensionamento de máquinas virtuais devem ser instaladas

### <a name="0380-unneeded-operating-system-accounts-software-components-services-and-functionality-are-removed-or-disabled"></a>0380 Contas, software, componentes, serviços e funcionalidades desnecessários do sistema operacional são removidos ou desabilitados

- As contas preteridas devem ser removidas de sua assinatura
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura

### <a name="1490-an-application-whitelisting-solution-is-implemented-on-all-servers-to-restrict-the-execution-of-executables-software-libraries-scripts-and-installers-to-an-approved-set"></a>1490 Uma solução de lista de permissões de aplicativo é implementada em todos os servidores para restringir a execução de executáveis, bibliotecas de software, scripts e instaladores a um conjunto aprovado

- Os controles de aplicativos adaptáveis devem ser habilitados em máquinas virtuais

### <a name="1417-antivirus-software-is-implemented-on-workstations-and-servers-and-configured-with-signature-based-detection-enabled-and-set-to-a-high-level-heuristic-based-detection-enabled-and-set-to-a-high-level-detection-signatures-checked-for-currency-and-updated-on-at-least-a-daily-basis-automatic-and-regular-scanning-configured-for-all-fixed-disks-and-removable-media"></a>1417 O software antivírus é implementado em estações de trabalho e servidores e configurado com: detecção baseada em assinatura habilitada e definida como um nível alto, detecção baseada em heurística habilitada e definida como um alto nível, assinaturas de detecção verificadas para a moeda e atualizadas pelo menos todos os dias, verificação automática e regular configurada para todos os discos fixos e mídia removível

- A extensão IaaSAntimalware da Microsoft deve ser implantada em servidores do Windows
- A solução de proteção de ponto de extremidade deve ser instalada nos conjuntos de dimensionamento de máquinas virtuais
- monitora o Endpoint Protection ausente na Central de Segurança do Azure

## <a name="guidelines-for-system-hardening---authentication-hardening"></a>Diretrizes para proteção do sistema – proteção de autenticação

### <a name="1546-users-are-authenticated-before-they-are-granted-access-to-a-system-and-its-resources"></a>1546 Os usuários são autenticados antes de receberem acesso a um sistema e os respectivos recursos

- Auditar o acesso irrestrito à rede para contas de armazenamento
- Os clusters do Service Fabric só devem usar o Azure Active Directory para autenticação de cliente
- Mostrar os resultados da auditoria das VMs do Linux que permitem conexões remotas em contas sem senhas
- Implantar pré-requisitos para auditar as VMs do Linux que permitem conexões remotas em contas sem senhas
- Mostrar os resultados da auditoria das VMs do Linux que têm contas sem senhas
- Implantar pré-requisitos para auditar as VMs do Linux que têm contas que não usam senhas

### <a name="0974-multi-factor-authentication-is-used-to-authenticate-standard-users"></a>0974 A autenticação multifator é usada para autenticar usuários padrão

- O MFA deve ser habilitado em contas com permissões de leitura em sua assinatura

### <a name="1173-multi-factor-authentication-is-used-to-authenticate-all-privileged-users-and-any-other-positions-of-trust"></a>1173 A autenticação multifator é usada para autenticar todos os usuários privilegiados e os outros cargos de confiança

- O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura
- A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura

### <a name="0421-passphrases-used-for-single-factor-authentication-are-a-minimum-of-14-characters-with-complexity-ideally-as-4-random-words"></a>0421 As senhas usadas para a autenticação de fator único têm pelo menos 14 caracteres com complexidade, idealmente, como quatro palavras aleatórias

- Mostrar os resultados da auditoria das configurações de VMs do Windows em 'Configurações de Segurança – Políticas de Contas'
- Implantar pré-requisitos para auditar as configurações de VMs do Windows em 'Configurações de Segurança – Políticas de Contas'

## <a name="guidelines-for-system-management---system-administration"></a>Diretrizes para gerenciamento de sistema – administração do sistema

### <a name="1384-multi-factor-authentication-is-used-to-authenticate-users-each-time-they-perform-privileged-actions"></a>1384 A autenticação multifator é usada para autenticar usuários cada vez que eles executam ações privilegiadas

- O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura
- A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura
- O MFA deve ser habilitado em contas com permissões de leitura em sua assinatura

### <a name="1386-management-traffic-is-only-allowed-to-originate-from-network-zones-that-are-used-to-administer-systems-and-applications"></a>1386 O tráfego de gerenciamento só tem permissão para se originar de zonas de rede usadas para administrar sistemas e aplicativos

- O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais
- A depuração remota deve ser desligada para aplicativos de API
- A depuração remota deve ser desativada para o aplicativos de funções
- A depuração remota deve ser desativada para aplicativos Web

## <a name="guidelines-for-system-management---system-patching"></a>Diretrizes para gerenciamento de sistema – aplicação de patch do sistema

### <a name="1144-security-vulnerabilities-in-applications-and-drivers-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1144 Vulnerabilidades de segurança em aplicativos e drivers avaliadas como impondo um risco extremo são corrigidas, atualizadas ou atenuadas dentro de 48 horas depois de serem identificadas por fornecedores, terceiros independentes, gerentes de sistema ou usuários

- As vulnerabilidades nos bancos de dados SQL devem ser corrigidas
- A avaliação da vulnerabilidade deve ser habilitada nos servidores SQL
- A avaliação de vulnerabilidades deve ser habilitada nas instâncias gerenciadas do SQL
- A Avaliação de Vulnerabilidades deve estar habilitada nos computadores
- As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas
- As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades
- As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas
- As vulnerabilidades nas configurações de segurança do contêiner devem ser corrigidas
- As configurações de Avaliação de Vulnerabilidade do SQL Server devem conter um endereço de email para receber os relatórios de verificação

### <a name="0940-security-vulnerabilities-in-applications-and-drivers-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>0940 Vulnerabilidades de segurança em aplicativos e drivers avaliadas como impondo um risco alto são corrigidas, atualizadas ou atenuadas dentro de duas semanas depois de serem identificadas por fornecedores, terceiros independentes, gerentes de sistema ou usuários

- As vulnerabilidades nos bancos de dados SQL devem ser corrigidas
- A avaliação da vulnerabilidade deve ser habilitada nos servidores SQL
- A avaliação de vulnerabilidades deve ser habilitada nas instâncias gerenciadas do SQL
- a Avaliação de Vulnerabilidades deve estar habilitada nas Máquinas Virtuais
- As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas
- As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades
- As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas
- As vulnerabilidades nas configurações de segurança do contêiner devem ser corrigidas
- As configurações de Avaliação de Vulnerabilidade do SQL Server devem conter um endereço de email para receber os relatórios de verificação

### <a name="1472-security-vulnerabilities-in-applications-and-drivers-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1472 Vulnerabilidades de segurança em aplicativos e drivers avaliadas como impondo um risco moderado ou baixo são corrigidas, atualizadas ou atenuadas dentro de um mês depois de serem identificadas por fornecedores, terceiros independentes, gerentes de sistema ou usuários

- As vulnerabilidades nos bancos de dados SQL devem ser corrigidas
- A avaliação da vulnerabilidade deve ser habilitada nos servidores SQL
- A avaliação de vulnerabilidades deve ser habilitada nas instâncias gerenciadas do SQL
- a Avaliação de Vulnerabilidades deve estar habilitada nas Máquinas Virtuais
- As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas
- As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades
- As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas
- As vulnerabilidades nas configurações de segurança do contêiner devem ser corrigidas
- As configurações de Avaliação de Vulnerabilidade do SQL Server devem conter um endereço de email para receber os relatórios de verificação

### <a name="1494-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1494 Vulnerabilidades de segurança em sistemas operacionais e firmware avaliadas como impondo um risco extremo são corrigidas, atualizadas ou atenuadas dentro de 48 horas depois de serem identificadas por fornecedores, terceiros independentes, gerentes de sistema ou usuários

- As vulnerabilidades nos bancos de dados SQL devem ser corrigidas
- A avaliação da vulnerabilidade deve ser habilitada nos servidores SQL
- A avaliação de vulnerabilidades deve ser habilitada nas instâncias gerenciadas do SQL
- a Avaliação de Vulnerabilidades deve estar habilitada nas Máquinas Virtuais
- As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas
- As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades
- As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas
- As vulnerabilidades nas configurações de segurança do contêiner devem ser corrigidas
- As configurações de Avaliação de Vulnerabilidade do SQL Server devem conter um endereço de email para receber os relatórios de verificação

### <a name="1495-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1495 Vulnerabilidades de segurança em sistemas operacionais e firmware avaliadas como impondo um risco alto são corrigidas, atualizadas ou atenuadas dentro de duas semanas depois de serem identificadas por fornecedores, terceiros independentes, gerentes de sistema ou usuários

- As vulnerabilidades nos bancos de dados SQL devem ser corrigidas
- A avaliação da vulnerabilidade deve ser habilitada nos servidores SQL
- A avaliação de vulnerabilidades deve ser habilitada nas instâncias gerenciadas do SQL
- a Avaliação de Vulnerabilidades deve estar habilitada nas Máquinas Virtuais
- As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas
- As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades
- As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas
- As vulnerabilidades nas configurações de segurança do contêiner devem ser corrigidas
- As configurações de Avaliação de Vulnerabilidade do SQL Server devem conter um endereço de email para receber os relatórios de verificação

### <a name="1496-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1496 Vulnerabilidades de segurança em firmware e sistemas operacionais avaliadas como impondo um risco moderado ou baixo são corrigidas, atualizadas ou atenuadas dentro de um mês depois de serem identificadas por fornecedores, terceiros independentes, gerentes de sistema ou usuários

- As vulnerabilidades nos bancos de dados SQL devem ser corrigidas
- A avaliação da vulnerabilidade deve ser habilitada nos servidores SQL
- A avaliação de vulnerabilidades deve ser habilitada nas instâncias gerenciadas do SQL
- a Avaliação de Vulnerabilidades deve estar habilitada nas Máquinas Virtuais
- As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas
- As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades
- As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas
- As vulnerabilidades nas configurações de segurança do contêiner devem ser corrigidas
- As configurações de Avaliação de Vulnerabilidade do SQL Server devem conter um endereço de email para receber os relatórios de verificação

## <a name="guidelines-for-system-management---data-backup-and-restoration"></a>Diretrizes para gerenciamento de sistema – backup e restauração de dados

### <a name="1511-backups-of-important-information-software-and-configuration-settings-are-performed-at-least-daily"></a>1511 Backups de informações importantes, configurações e definições de software são realizados pelo menos diariamente

- Auditar máquinas virtuais sem a recuperação de desastre configurada

## <a name="guidelines-for-system-monitoring---event-logging-and-auditing"></a>Diretrizes para monitoramento do sistema – log e auditoria de eventos

### <a name="1405-a-centralised-logging-facility-is-implemented-and-systems-are-configured-to-save-event-logs-to-the-centralised-logging-facility-as-soon-as-possible-after-each-event-occurs"></a>1405 Uma instalação de registro em log centralizada é implementada e os sistemas são configurados para salvar logs de evento na instalação de registro em log centralizada assim que possível após a ocorrência de cada evento

- As assinaturas do Azure devem ter um perfil de log para o Log de Atividades

### <a name="0582-the-following-events-are-logged-for-operating-systems-access-to-important-data-and-processes-application-crashes-and-any-error-messages-attempts-to-use-special-privileges-changes-to-accounts-changes-to-security-policy-changes-to-system-configurations-domain-name-system-dns-and-hypertext-transfer-protocol-http-requests-failed-attempts-to-access-data-and-system-resources-service-failures-and-restarts-system-startup-and-shutdown-transfer-of-data-to-external-media-user-or-group-management-use-of-special-privileges"></a>0582 Os seguintes eventos são registrados em log para sistemas operacionais: acesso a dados e processos importantes, falhas do aplicativo e mensagens de erro, tentativas de usar privilégios especiais, alterações em contas, alterações na política de segurança, alterações nas configurações do sistema, DNS (sistema de nomes de domínio) e solicitações HTTP, tentativas com falha de acessar dados e recursos do sistema, falhas de serviço e reinicializações, inicialização e desligamento do sistema, transferência de dados para mídia externa, gerenciamento de usuário ou grupo, uso de privilégios especiais

- \[Versão Prévia\]: Auditar a implantação do Agente do Log Analytics – imagem de VM (sistema operacional) não listada
- Auditar a implantação do Agente do Log Analytics no VMSS – imagem de VM (sistema operacional) não listada
- Auditar o espaço de trabalho do Log Analytics para a VM – Relatar incompatibilidade
- Configuração de diagnóstico de auditoria

### <a name="1537-the-following-events-are-logged-for-databases-access-to-particularly-important-information-addition-of-new-users-especially-privileged-users-any-query-containing-comments-any-query-containing-multiple-embedded-queries-any-query-or-database-alerts-or-failures-attempts-to-elevate-privileges-attempted-access-that-is-successful-or-unsuccessful-changes-to-the-database-structure-changes-to-user-roles-or-database-permissions-database-administrator-actions-database-logons-and-logoffs-modifications-to-data-use-of-executable-commands"></a>1537 Os seguintes eventos são registrados em log para bancos de dados: acesso a informações particularmente importantes, adição de novos usuários, em especial, usuários privilegiados, qualquer consulta que contenha comentários, qualquer consulta que contenha várias consultas inseridas, alertas ou falhas de consulta ou banco de dados, tentar elevar privilégios, tentar acessar com êxito ou sem êxito, alterações na estrutura do banco de dados, alterações em funções de usuário ou permissões de banco de dados, ações do administrador de banco de dados, logons de banco de dados e logoffs, modificações em dados, uso de comandos executáveis

- A Segurança de Dados Avançada deve ser habilitada nos servidores SQL
- Configuração de diagnóstico de auditoria
- A Segurança de Dados Avançada deve ser habilitada nas instâncias gerenciadas do SQL

## <a name="guidelines-for-system-monitoring---vulnerability-management"></a>Diretrizes para monitoramento do sistema – gerenciamento de vulnerabilidades

### <a name="0911-vulnerability-assessments-and-penetration-tests-are-conducted-by-suitably-skilled-personnel-before-a-system-is-deployed-after-a-significant-change-to-a-system-and-at-least-annually-or-as-specified-by-the-system-owner"></a>0911 Avaliações de vulnerabilidades e testes de penetração são conduzidos por pessoal capacitado antes de um sistema ser implantado, após uma alteração significativa em um sistema e pelo menos anualmente ou conforme especificado pelo proprietário do sistema

- As vulnerabilidades nos bancos de dados SQL devem ser corrigidas
- A avaliação da vulnerabilidade deve ser habilitada nos servidores SQL
- A avaliação de vulnerabilidades deve ser habilitada nas instâncias gerenciadas do SQL
- a Avaliação de Vulnerabilidades deve estar habilitada nas Máquinas Virtuais
- As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas
- As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades
- As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas
- As vulnerabilidades nas configurações de segurança do contêiner devem ser corrigidas

## <a name="guidelines-for-database-systems-management---database-servers"></a>Diretrizes para gerenciamento de sistemas de banco de dados – servidores de banco de dados

### <a name="1425-hard-disks-of-database-servers-are-encrypted-using-full-disk-encryption"></a>1425 Discos rígidos de servidores de banco de dados são criptografados usando a criptografia de disco total

- A criptografia de disco deve ser aplicada em máquinas virtuais
- A Transparent Data Encryption em bancos de dados SQL deve ser habilitada

### <a name="1277-information-communicated-between-database-servers-and-web-applications-is-encrypted"></a>1277 Informações comunicadas entre servidores de banco de dados e aplicativos Web são criptografadas

- Apenas conexões seguras com o Cache Redis devem ser habilitadas
- A transferência segura para contas de armazenamento deve ser habilitada
- Mostrar os resultados da auditoria dos servidores Web do Windows que não estão usando protocolos de comunicação segura
- Implantar pré-requisitos para auditar os servidores Web do Windows que não estão usando protocolos de comunicação segura

## <a name="guidelines-for-database-systems-management---database-management-system-software"></a>Diretrizes para gerenciamento de sistemas de banco de dados – software de sistema de gerenciamento de banco de dados

### <a name="1260-default-database-administrator-accounts-are-disabled-renamed-or-have-their-passphrases-changed"></a>1260 Contas de administrador de banco de dados padrão estão desabilitadas, renomeadas ou têm suas senhas alteradas

- Um administrador do Azure Active Directory deve ser provisionado para servidores SQL

### <a name="1262-database-administrators-have-unique-and-identifiable-accounts"></a>1262 Os administradores de banco de dados têm contas exclusivas e identificáveis

- Um administrador do Azure Active Directory deve ser provisionado para servidores SQL

### <a name="1261-database-administrator-accounts-are-not-shared-across-different-databases"></a>1261 Contas de administrador de banco de dados não são compartilhadas entre diferentes bancos

- Um administrador do Azure Active Directory deve ser provisionado para servidores SQL

### <a name="1263-database-administrator-accounts-are-used-exclusively-for-administrative-tasks-with-standard-database-accounts-used-for-general-purpose-interactions-with-database"></a>1263 Contas de administrador de banco de dados são usadas exclusivamente para tarefas administrativas, com contas de banco de dados padrão usadas para interações de uso geral com o banco de dados

- Um administrador do Azure Active Directory deve ser provisionado para servidores SQL

### <a name="1264-database-administrator-access-is-restricted-to-defined-roles-rather-than-accounts-with-default-administrative-permissions-or-all-permissions"></a>1264 O acesso de administrador de banco de dados é restrito a funções definidas, em vez de contas com permissões administrativas padrão ou todas as permissões

- Um administrador do Azure Active Directory deve ser provisionado para servidores SQL

## <a name="guidelines-for-using-cryptography---cryptographic-fundamentals"></a>Diretrizes para usar criptografia – conceitos básicos de criptografia

### <a name="0459-encryption-software-used-for-data-at-rest-implements-full-disk-encryption-or-partial-encryption-where-access-controls-will-only-allow-writing-to-the-encrypted-partition"></a>0459 O software de criptografia usado para os dados em repouso implementa a criptografia de disco completo ou a criptografia parcial em que os controles de acesso só permitirão gravar na partição criptografada

- A criptografia de disco deve ser aplicada em máquinas virtuais

## <a name="guidelines-for-using-cryptography---transport-layer-security"></a>Diretrizes para usar criptografia – segurança da camada de transporte

### <a name="1139-only-the-latest-version-of-tls-is-used"></a>1139 Somente a versão mais recente do TLS é usada

- A última versão do TLS deve ser usada no aplicativo de API
- A última versão do TLS deve ser usada no aplicativo Web
- A última versão do TLS deve ser usada no aplicativo de funções
- Implantar pré-requisitos para auditar os servidores Web do Windows que não estão usando protocolos de comunicação segura
- Mostrar os resultados da auditoria dos servidores Web do Windows que não estão usando protocolos de comunicação segura

## <a name="guidelines-for-data-transfers-and-content-filtering---content-filtering"></a>Diretrizes para transferências de dados e filtragem de conteúdo – filtragem de conteúdo

### <a name="1288-antivirus-scanning-using-multiple-different-scanning-engines-is-performed-on-all-content"></a>1288 A verificação antivírus, usando vários mecanismos de verificação diferentes, é executada em todo o conteúdo

- A extensão IaaSAntimalware da Microsoft deve ser implantada em servidores do Windows
- A solução de proteção de ponto de extremidade deve ser instalada nos conjuntos de dimensionamento de máquinas virtuais
- monitora o Endpoint Protection ausente na Central de Segurança do Azure

## <a name="guidelines-for-data-transfers-and-content-filtering---web-application-development"></a>Diretrizes para transferências de dados e filtragem de conteúdo – desenvolvimento de aplicativos Web

### <a name="1552-all-web-application-content-is-offered-exclusively-using-https"></a>1552 Todo o conteúdo do aplicativo Web é oferecido exclusivamente usando HTTPS

- O aplicativo de funções deve ser acessível apenas por HTTPS
- O aplicativo de API só deve estar acessível via HTTPS
- Aplicativo Web deve ser acessível somente por HTTPS
- Apenas conexões seguras com o Cache Redis devem ser habilitadas

### <a name="1424-web-browser-based-security-controls-are-implemented-for-web-applications-in-order-to-help-protect-both-web-applications-and-their-users"></a>1424 Os controles de segurança baseados em navegador da Web são implementados para aplicativos Web para ajudar a proteger os aplicativos Web e seus usuários

- O CORS não deve permitir que todos os recursos acessem seus aplicativos Web

## <a name="guidelines-for-network-management---network-design-and-configuration"></a>Diretrizes para gerenciamento de rede – design e configuração de rede

### <a name="0520-network-access-controls-are-implemented-on-networks-to-prevent-the-connection-of-unauthorised-network-devices"></a>0520 Os controles de acesso à rede são implementados em redes para evitar a conexão de dispositivos de rede não autorizados

- Auditar o acesso irrestrito à rede para contas de armazenamento

### <a name="1182-network-access-controls-are-implemented-to-limit-traffic-within-and-between-network-segments-to-only-those-that-are-required-for-business-purposes"></a>1182 Os controles de acesso à rede são implementados para limitar o tráfego dentro e entre os segmentos de rede apenas ao necessário para fins empresariais

- As máquinas virtuais para a Internet devem ser protegidas com Grupos de Segurança de Rede
- Auditar o acesso irrestrito à rede para contas de armazenamento
- As recomendações da Proteção de Rede Adaptável devem ser aplicadas nas máquinas virtuais para a Internet

## <a name="guidelines-for-network-management---service-continuity-for-online-services"></a>Diretrizes para gerenciamento de rede – Continuidade de serviço para serviços online

### <a name="1431-denial-of-service-attack-prevention-and-mitigation-strategies-are-discussed-with-service-providers-specifically-their-capacity-to-withstand-denial-of-service-attacks-any-costs-likely-to-be-incurred-by-customers-resulting-from-denial-of-service-attacks-thresholds-for-notifying-customers-or-turning-off-their-online-services-during-denial-of-service-attacks-pre-approved-actions-that-can-be-undertaken-during-denial-of-service-attacks-denial-of-service-attack-prevention-arrangements-with-upstream-providers-to-block-malicious-traffic-as-far-upstream-as-possible"></a>1431 As estratégias de prevenção e mitigação de ataque de negação de serviço são discutidas com provedores de serviço, especificamente: sua capacidade de resistir a ataques de negação de serviço, os custos que provavelmente serão incorridos pelos clientes devido a ataques de negação de serviço, limites para notificar os clientes ou desativar seus serviços online durante ataques de negação de serviço, ações previamente aprovadas que podem ser executadas durante ataques de negação de serviço, disposições de prevenção de ataque de negação de serviço com provedores upstream para bloquear o tráfego mal-intencionado o mais upstream possível

- A Proteção contra DDoS Standard deve ser habilitada


> [!NOTE]
> A disponibilidade de definições específicas do Azure Policy pode variar no Azure Governamental e em outras nuvens nacionais. 

## <a name="next-steps"></a>Próximas etapas

Outros artigos sobre blueprints e como usá-los:

> [!div class="nextstepaction"]
> [Blueprint ISM PROTECTED – Visão geral](./index.md)
> [Blueprint ISM PROTECTED – Etapas de implantação](./deploy.md)

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).
