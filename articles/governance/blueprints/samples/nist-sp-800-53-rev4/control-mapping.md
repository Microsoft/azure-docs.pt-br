---
title: Amostra – blueprint do NIST SP 800-53 R4 – mapeamento de controles
description: Mapeamento de controles da amostra de blueprint NIST SP 800-53 R4 para o Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: a179c5919f647b567b2109ec1a73ac8bb6cda36b
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479863"
---
# <a name="control-mapping-of-the-nist-sp-800-53-r4-blueprint-sample"></a>Mapeamento de controles da amostra de blueprint NIST SP 800-53 R4

O artigo a seguir fornece detalhes sobre como a amostra de blueprint NIST SP 800-53 R4 do Azure Blueprints é mapeada para os controles do NIST SP 800-53 R4. Para obter mais informações sobre os controles, confira [NIST SP 800-53](https://nvd.nist.gov/800-53).

Os seguintes mapeamentos referem-se aos controles do **NIST SP 800-53 (Rev. 4)** . Use a navegação no lado direito para ir diretamente para um mapeamento de controle específico. Muitos dos controles mapeados são implementados com uma iniciativa do [Azure Policy](../../../policy/overview.md). Para examinar a iniciativa completa, abra **Política** no portal do Azure e selecione a página **Definições**. Em seguida, localize e selecione a iniciativa de política interna **\[Versão prévia\]: Auditar controles NIST SP 800-53 R4 e implantar extensões de VM específicas para dar suporte a requisitos de auditoria**.

## <a name="ac-2-account-management"></a>AC-2 Gerenciamento de conta

Este blueprint ajuda a analisar contas que podem não estar em conformidade com os requisitos de gerenciamento de conta de sua organização. Este blueprint atribui definições do [Azure Policy](../../../policy/overview.md) que auditam contas externas com permissões de leitura, gravação e proprietário em contas de assinatura e preteridas. Revisando as contas auditadas por essas políticas, você pode adotar as medidas adequadas a fim de garantir que os requisitos de gerenciamento de conta sejam atendidos.

- As contas preteridas devem ser removidas de sua assinatura
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de leitura devem ser removidas de sua assinatura
- As contas externas com permissões de gravação devem ser removidas de sua assinatura

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) Gerenciamento de conta | Esquemas baseados em função

O Azure implementa o RBAC ([controle de acesso baseado em função](../../../../role-based-access-control/overview.md)) para ajudar você a gerenciar quem tem acesso aos recursos no Azure. Usando o portal do Azure, você pode examinar quem tem acesso aos recursos do Azure e suas permissões. Este blueprint também atribui definições do [Azure Policy](../../../policy/overview.md) para auditar o uso da autenticação do Azure Active Directory para servidores SQL e o Service Fabric. O uso da autenticação do Azure Active Directory permite o gerenciamento simplificado de permissões e o gerenciamento centralizado de identidades dos usuários de banco de dados e de outros serviços da Microsoft. Além disso, este blueprint atribui uma definição do Azure Policy para auditar o uso de regras RBAC personalizadas. Entender o local em que as regras RBAC personalizadas são implementadas pode ajudar você a verificar a necessidade e a implementação apropriada, pois as regras RBAC personalizadas estão sujeitas a erros.

- Um administrador do Azure Active Directory deve ser provisionado para servidores SQL
- Auditar o uso de regras personalizadas do RBAC
- Os clusters do Service Fabric só devem usar o Azure Active Directory para autenticação de cliente

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) Gerenciamento de conta | Monitoramento de conta / Uso atípico

O acesso JIT (Just-In-Time) à máquina virtual bloqueia o tráfego de entrada às máquinas virtuais do Azure, reduzindo a exposição a ataques enquanto fornece acesso fácil para se conectar às VMs quando necessário. Todas as solicitações JIT para acessar máquinas virtuais são registradas no Log de Atividades, permitindo que você monitore se há uso atípico. Esse blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) que ajuda você a monitorar máquinas virtuais que podem dar suporte ao acesso Just-In-Time, mas ainda não foram configuradas.

- O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais

## <a name="ac-4-information-flow-enforcement"></a>AC-4 Imposição de fluxo de informações

O CORS (compartilhamento de recurso de origem cruzada) pode permitir que recursos dos Serviços de Aplicativos sejam solicitados de um domínio externo. A Microsoft recomenda que você permita que apenas domínios necessários interajam com sua API, sua função e seus aplicativos Web. Este blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) para ajudá-lo a monitorar as restrições de acesso de recursos do CORS na Central de Segurança do Azure.
Ter noções básicas sobre implementações de CORS pode ajudá-lo a confirmar que os controles de fluxo de informações foram implementados.

- O CORS não deve permitir que todos os recursos acessem seu aplicativo Web

## <a name="ac-5-separation-of-duties"></a>AC-5 Separação de funções

Ter apenas um proprietário de assinatura do Azure não permite a redundância administrativa. Por outro lado, ter muitos proprietários de assinatura do Azure pode aumentar o potencial de uma violação por meio de uma conta de proprietário comprometida. Este blueprint ajuda você a manter um número apropriado de proprietários de assinatura do Azure por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que auditam o número de proprietários de assinaturas do Azure. Este blueprint também atribui definições do Azure Policy que ajudam a controlar a associação ao grupo Administradores em máquinas virtuais do Windows. O gerenciamento de permissões de proprietário de assinatura e administrador de máquina virtual pode ajudá-lo a implementar uma diferenciação de funções apropriada.

- Um máximo de três proprietários deve ser designado para sua assinatura
- Auditar as VMs do Windows nas quais o grupo Administradores contém um dos membros especificados
- Auditar as VMs do Windows nas quais o grupo Administradores não contém todos os membros especificados
- Implantar requisitos para auditar as VMs do Windows nas quais o grupo Administradores contém um dos membros especificados
- Implantar requisitos para auditar as VMs do Windows nas quais o grupo Administradores não contém todos os membros especificados
- Deve haver mais de um proprietário atribuído à sua assinatura

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>AC-6 (7) Privilégios mínimos | Análise de privilégios do usuário

O Azure implementa o RBAC ([controle de acesso baseado em função](../../../../role-based-access-control/overview.md)) para ajudar você a gerenciar quem tem acesso aos recursos no Azure. Usando o portal do Azure, você pode examinar quem tem acesso aos recursos do Azure e suas permissões. Este blueprint atribui definições do [Azure Policy](../../../policy/overview.md) para auditar as contas que devem ser priorizadas para análise. Revisar esses indicadores de conta pode ajudar a garantir que controles de privilégios mínimos estejam implementados.

- Um máximo de três proprietários deve ser designado para sua assinatura
- Auditar as VMs do Windows nas quais o grupo Administradores contém um dos membros especificados
- Auditar as VMs do Windows nas quais o grupo Administradores não contém todos os membros especificados
- Implantar requisitos para auditar as VMs do Windows nas quais o grupo Administradores contém um dos membros especificados
- Implantar requisitos para auditar as VMs do Windows nas quais o grupo Administradores não contém todos os membros especificados
- Deve haver mais de um proprietário atribuído à sua assinatura

## <a name="ac-16-security-attributes"></a>AC-16 Atributos de segurança

A capacidade de classificação e descoberta de dados da Segurança de Dados Avançada do Banco de Dados SQL do Azure fornece funcionalidades para descobrir, classificar, rotular e proteger os dados confidenciais em seus bancos de dados. Pode ser usada para fornecer visibilidade em seu estado de classificação do banco de dados e para controlar o acesso a dados confidenciais no banco de dados e, além de suas bordas. A Segurança de Dados Avançada pode ajudá-lo a garantir que as informações sejam associadas aos atributos de segurança apropriados de sua organização. Este blueprint atribui definições do [Azure Policy](../../../policy/overview.md) para monitorar e impor o uso da Segurança de Dados Avançada no SQL Server. 

- A Segurança de Dados Avançada deve ser habilitada nas instâncias gerenciadas
- A Segurança de Dados Avançada deve ser habilitada nos servidores SQL
- Implantar Segurança de Dados Avançada em servidores SQL

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) Acesso remoto | Controle / monitoramento automatizado

Este blueprint ajuda você a monitorar e controlar o acesso remoto por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) para monitorar se a depuração remota do aplicativo do Serviço de Aplicativo do Azure está desligada e definições de política que auditam máquinas virtuais do Linux que permitem conexões remotas em contas sem senhas. Este blueprint também atribui uma definição do Azure Policy que ajuda você a monitorar o acesso irrestrito a contas de armazenamento. Monitorar esses indicadores pode ajudá-lo a garantir que os métodos de acesso remoto estejam em conformidade com sua política de segurança.

- \[Versão Prévia\]: Auditar as VMs do Linux que permitem conexões remotas em contas sem senhas
- \[Versão Prévia\]: Implantar requisitos para auditar as VMs do Linux que permitem conexões remotas em contas sem senhas
- Auditar o acesso irrestrito à rede para contas de armazenamento
- A depuração remota deve ser desligada para o aplicativo de API
- A depuração remota deve ser desativada para o aplicativo de funções
- Depuração remota deve ser desativada para o aplicativo da Web

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>AU-3 (2) Conteúdo de Registros de Auditoria | Gerenciamento Centralizado do Conteúdo do Registro de Auditoria Planejada

Dados de log coletados pelo Azure Monitor são armazenados em um workspace do Log Analytics, permitindo o gerenciamento e a configuração centralizada. Este blueprint ajuda você a garantir que os eventos sejam registrados em log por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que auditam e impõem a implantação do agente do Log Analytics em máquinas virtuais do Azure.

- \[Versão Prévia\]: Auditar a implantação do Agente do Log Analytics – imagem de VM (sistema operacional) não listada
- \[Versão Prévia\]: Auditar a implantação do Agente do Log Analytics no VMSS – imagem de VM (sistema operacional) não listada
- \[Versão Prévia\]: Auditar o espaço de trabalho do Log Analytics para a VM – Relatar incompatibilidade
- \[Versão Prévia\]: Implantar o Agente do Log Analytics no VMSS (Conjuntos de Dimensionamento de VMs) do Linux
- \[Versão Prévia\]: Implantar o Agente do Log Analytics para VMs do Linux
- \[Versão Prévia\]: Implantar o Agente do Log Analytics no VMSS (Conjuntos de Dimensionamento de VMs) do Windows
- \[Versão Prévia\]: Implantar o Agente do Log Analytics para VMs do Windows

## <a name="au-5-response-to-audit-processing-failures"></a>AU-5 Resposta a Falhas de Processamento de Auditoria

Este blueprint atribui definições do [Azure Policy](../../../policy/overview.md) que monitoram configurações de auditoria e log de eventos. Monitorar essas configurações pode fornecer um indicador de falha ou de configuração incorreta do sistema de auditoria e ajudá-lo a adotar uma ação corretiva.

- Configuração de diagnóstico de auditoria
- Auditar configurações de Auditoria de nível do servidor SQL
- A Segurança de Dados Avançada deve ser habilitada nas instâncias gerenciadas
- A Segurança de Dados Avançada deve ser habilitada nos servidores SQL

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) Revisão, Análise e Relatório de Auditoria | Revisão e Análise Central

Dados de log coletados pelo Azure Monitor são armazenados em um workspace do Log Analytics, permitindo análises e relatórios centralizados. Este blueprint ajuda você a garantir que os eventos sejam registrados em log por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que auditam e impõem a implantação do agente do Log Analytics em máquinas virtuais do Azure.

- \[Versão Prévia\]: Auditar a implantação do Agente do Log Analytics – imagem de VM (sistema operacional) não listada
- \[Versão Prévia\]: Auditar a implantação do Agente do Log Analytics no VMSS – imagem de VM (sistema operacional) não listada
- \[Versão Prévia\]: Auditar o espaço de trabalho do Log Analytics para a VM – Relatar incompatibilidade
- \[Versão Prévia\]: Implantar o Agente do Log Analytics no VMSS (Conjuntos de Dimensionamento de VMs) do Linux
- \[Versão Prévia\]: Implantar o Agente do Log Analytics para VMs do Linux
- \[Versão Prévia\]: Implantar o Agente do Log Analytics no VMSS (Conjuntos de Dimensionamento de VMs) do Windows
- \[Versão Prévia\]: Implantar o Agente do Log Analytics para VMs do Windows

## <a name="au-12-audit-generation"></a>AU-12 Geração de Auditoria

Este blueprint ajuda você a garantir que os eventos do sistema sejam registrados em log por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que auditam as configurações do log de auditoria em recursos do Azure. Essas definições de política auditam e impõem a implantação do agente do Log Analytics em máquinas virtuais do Azure, bem como as configurações de auditoria para outros tipos de recursos do Azure. Essas definições de política também auditam a configuração dos logs de diagnóstico para fornecer informações sobre as operações executadas em recursos do Azure. Além disso, a auditoria e a Segurança de Dados Avançada são configuradas em servidores SQL.

- \[Versão Prévia\]: Auditar a implantação do Agente do Log Analytics – imagem de VM (sistema operacional) não listada
- \[Versão Prévia\]: Auditar a implantação do Agente do Log Analytics no VMSS – imagem de VM (sistema operacional) não listada
- \[Versão Prévia\]: Auditar o espaço de trabalho do Log Analytics para a VM – Relatar incompatibilidade
- \[Versão Prévia\]: Implantar o Agente do Log Analytics no VMSS (Conjuntos de Dimensionamento de VMs) do Linux
- \[Versão Prévia\]: Implantar o Agente do Log Analytics para VMs do Linux
- \[Versão Prévia\]: Implantar o Agente do Log Analytics no VMSS (Conjuntos de Dimensionamento de VMs) do Windows
- \[Versão Prévia\]: Implantar o Agente do Log Analytics para VMs do Windows
- Configuração de diagnóstico de auditoria
- Auditar configurações de Auditoria de nível do servidor SQL
- A Segurança de Dados Avançada deve ser habilitada nas instâncias gerenciadas
- A Segurança de Dados Avançada deve ser habilitada nos servidores SQL
- Implantar Segurança de Dados Avançada em servidores SQL
- Implantar Auditoria em servidores SQL
- Implantar configurações de diagnóstico para grupos de segurança de rede

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM-7 (2) Funcionalidade Mínima | Impedir a Execução do Programa

O controle de aplicativos adaptável na Central de Segurança do Azure é uma solução de lista de permissões inteligente, automatizada e de ponta a ponta que pode bloquear ou impedir a execução de softwares específicos em suas máquinas virtuais. O controle de aplicativos pode ser executado no modo de imposição, que proíbe a execução de aplicativos não aprovados. Esse blueprint atribui uma definição do Azure Policy que ajuda você a monitorar máquinas virtuais em que uma lista de permissões de aplicativos é recomendada, mas ainda não foi configurada.

- Os controles de aplicativos adaptáveis devem ser habilitados em máquinas virtuais

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) Funcionalidade Mínima | Um Software autorizado / Lista de Permissões

O controle de aplicativos adaptável na Central de Segurança do Azure é uma solução de lista de permissões inteligente, automatizada e de ponta a ponta que pode bloquear ou impedir a execução de softwares específicos em suas máquinas virtuais. O controle de aplicativo ajuda a criar listas de aplicativos aprovados para suas máquinas virtuais. Esse blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) que ajuda você a monitorar máquinas virtuais em que uma lista de permissões de aplicativos é recomendada, mas ainda não foi configurada.

- Os controles de aplicativos adaptáveis devem ser habilitados em máquinas virtuais

## <a name="cm-11-user-installed-software"></a>CM-11 Software Instalado pelo Usuário

O controle de aplicativos adaptável na Central de Segurança do Azure é uma solução de lista de permissões inteligente, automatizada e de ponta a ponta que pode bloquear ou impedir a execução de softwares específicos em suas máquinas virtuais. O controle de aplicativos pode ajudar a impor e a monitorar a conformidade com políticas de restrição de software. Esse blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) que ajuda você a monitorar máquinas virtuais em que uma lista de permissões de aplicativos é recomendada, mas ainda não foi configurada.

- Os controles de aplicativos adaptáveis devem ser habilitados em máquinas virtuais

## <a name="cp-7-alternate-processing-site"></a>CP-7 Site de Processamento Alternativo

O Azure Site Recovery replica cargas de trabalho em execução em máquinas virtuais de uma localização primária para uma secundária. Se ocorrer uma interrupção no site primário, a carga de trabalho fará failover para a localização secundária. Esse blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) que audita máquinas virtuais sem a recuperação de desastre configurada. Monitorar esse indicador pode ajudá-lo a garantir que os controles de contingência necessários estejam em vigor.

- Auditar máquinas virtuais sem a recuperação de desastre configurada

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) Identificação e Autenticação (Usuários Organizacionais) | Acesso à Rede para Contas com Privilégios

Este blueprint ajuda você a restringir e controlar o acesso privilegiado por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) para auditar as contas com permissões de gravação e/ou proprietário que não têm a autenticação multifator habilitada. A autenticação multifator ajuda a proteger contas, mesmo que uma única informação de autenticação seja comprometida. Monitorando as contas sem a autenticação multifator habilitada, você pode identificar as contas que têm mais probabilidade de serem comprometidas.

- O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura
- O MFA deve ser habilitado em contas com permissões de gravação em sua assinatura

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) Identificação e Autenticação (Usuários Organizacionais) | Acesso à Rede para Contas sem Privilégios

Esse blueprint ajuda a restringir e controlar o acesso atribuindo uma definição do [Azure Policy](../../../policy/overview.md) para auditar contas com permissões de leitura que não têm a autenticação multifator habilitada. A autenticação multifator ajuda a proteger contas, mesmo que uma única informação de autenticação seja comprometida. Monitorando as contas sem a autenticação multifator habilitada, você pode identificar as contas que têm mais probabilidade de serem comprometidas.

- O MFA deve ser habilitado em contas com permissões de leitura em sua assinatura

## <a name="ia-5-authenticator-management"></a>IA-5 Gerenciamento de Autenticador

Este blueprint atribui definições do [Azure Policy](../../../policy/overview.md) que auditam as máquinas virtuais do Linux que permitem conexões remotas em contas sem senhas e/ou que têm permissões incorretas definidas no arquivo de senha. Este blueprint também atribui definições de política que auditam a configuração do tipo de criptografia de senha para máquinas virtuais do Windows. Monitorar esses indicadores ajuda você a garantir que os autenticadores do sistema estejam em conformidade com a política de identificação e autenticação de sua organização.

- \[Versão Prévia\]: Auditar as VMs do Linux que não têm as permissões de arquivo de senha definidas como 0644
- \[Versão Prévia\]: Auditar as VMs do Linux que têm contas sem senhas
- \[Versão Prévia\]: Auditar as VM do Windows que não armazenam senhas usando a criptografia reversível
- \[Versão Prévia\]: Implantar requisitos para auditar as VMs do Linux que não têm as permissões de arquivo de senha definidas como 0644
- \[Versão Prévia\]: Implantar requisitos para auditar as VMs do Linux que têm contas sem senhas
- \[Versão Prévia\]: Implantar requisitos para auditar as VMs do Windows que não armazenam senhas usando a criptografia reversível

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) Gerenciamento de Autenticador | Autenticação Baseada em Senha

Este blueprint ajuda você a impor senhas fortes por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que auditam as máquinas virtuais do Windows que não impõem a força mínima e outros requisitos de senha. O reconhecimento de máquinas virtuais que estão violando a política de força da senha ajuda você a tomar ações corretivas para garantir que as senhas de todas as contas de usuário da máquina virtual estejam em conformidade com a política de senha de sua organização.

- \[Versão Prévia\]: Auditar as VMs do Windows que podem reutilizar as 24 senhas anteriores
- \[Versão Prévia\]: Auditar as VMs do Windows que não têm uma duração máxima da senha de 70 dias
- \[Versão Prévia\]: Auditar as VMs do Windows que não têm uma duração mínima da senha de 1 dia
- \[Versão Prévia\]: Auditar as VMs do Windows que não têm a configuração de complexidade de senha habilitada
- \[Versão Prévia\]: Auditar as VMs do Windows que não restringem o tamanho mínimo da senha a 14 caracteres
- \[Versão Prévia\]: Auditar as VM do Windows que não armazenam senhas usando a criptografia reversível
- \[Versão Prévia\]: Implantar requisitos para auditar as VMs do Windows que podem reutilizar as últimas 24 senhas
- \[Versão Prévia\]: Implantar requisitos para auditar as VMs do Windows que não têm uma duração máxima da senha de 70 dias
- \[Versão Prévia\]: Implantar requisitos para auditar as VMs do Windows que não têm uma duração mínima da senha de 1 dia
- \[Versão Prévia\]: Implantar requisitos para auditar as VMs do Windows que não têm a configuração de complexidade de senha habilitada
- \[Versão Prévia\]: Implantar requisitos para auditar as VMs do Windows que não restringem o tamanho mínimo da senha a 14 caracteres
- \[Versão Prévia\]: Implantar requisitos para auditar as VMs do Windows que não armazenam senhas usando a criptografia reversível

## <a name="ra-5-vulnerability-scanning"></a>RA-5 Verificação de Vulnerabilidade

Este blueprint ajuda você a gerenciar as vulnerabilidades do sistema de informações por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que monitoram vulnerabilidades do sistema operacional, vulnerabilidades do SQL e vulnerabilidades da máquina virtual na Central de Segurança do Azure. A Central de Segurança do Azure fornece funcionalidades de relatórios que permitem ter insights em tempo real sobre o estado de segurança de recursos implantados do Azure. Este blueprint também atribui definições de política que auditam e impõem a Segurança de Dados Avançada em servidores SQL. A segurança de dados avançada inclui recursos de avaliação de vulnerabilidade e proteção avançada contra ameaças para ajudá-lo a entender as vulnerabilidades de seus recursos implantados.

- A Segurança de Dados Avançada deve ser habilitada nas instâncias gerenciadas
- A Segurança de Dados Avançada deve ser habilitada nos servidores SQL
- Implantar Segurança de Dados Avançada em servidores SQL
- As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas
- As vulnerabilidades da configuração de segurança nas máquinas virtuais devem ser corrigidas
- As vulnerabilidades nos bancos de dados SQL devem ser corrigidas
- As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades

## <a name="sc-5-denial-of-service-protection"></a>SC-5 Proteção Contra Negação de Serviço

O nível padrão da proteção contra DDoS (ataque de negação de serviço distribuído) do Azure fornece recursos adicionais e recursos de mitigação com relação à camada de serviço básica. Esses recursos adicionais incluem a integração do Azure Monitor e a capacidade de examinar relatórios de mitigação pós-ataque. Esse blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) que audita se o nível padrão da proteção contra DDoS está habilitada. Compreender a diferença de funcionalidade entre as camadas de serviço pode ajudar você a selecionar a melhor solução para tratar das proteções contra negação de serviço para seu ambiente do Azure.

- A Proteção contra DDoS Standard deve ser habilitada

## <a name="sc-7-boundary-protection"></a>SC-7 Proteção de Limite

Este blueprint ajuda você a gerenciar e controlar o limite do sistema por meio da atribuição de uma definição do [Azure Policy](../../../policy/overview.md) que monitora as recomendações de proteção do grupo de segurança de rede na Central de Segurança do Azure. A Central de Segurança do Azure analisa padrões de tráfego de máquinas virtuais voltadas para a Internet e fornece recomendações de regras de grupo de segurança de rede para reduzir a superfície de ataque potencial.
Além disso, este blueprint atribui definições de política que monitoram pontos de extremidade, contas de armazenamento e aplicativos desprotegidos. Os pontos de extremidade e os aplicativos que não estão protegidos por um firewall e as contas de armazenamento com acesso irrestrito podem permitir o acesso não intencional às informações contidas no sistema de informações.

- As regras do grupo de segurança de rede para máquinas virtuais para a Internet devem ser protegidas
- O acesso pelo ponto de extremidade para a Internet deve ser restrito
- As regras NSGs para aplicativos Web em IaaS devem ser protegidas
- Auditar o acesso irrestrito à rede para contas de armazenamento

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) Proteção de Limite | Pontos de Acesso

O acesso JIT (Just-In-Time) à máquina virtual bloqueia o tráfego de entrada às máquinas virtuais do Azure, reduzindo a exposição a ataques enquanto fornece acesso fácil para se conectar às VMs quando necessário. O acesso JIT à máquina virtual ajuda a limitar o número de conexões externas a seus recursos no Azure. Esse blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) que ajuda você a monitorar máquinas virtuais que podem dar suporte ao acesso Just-In-Time, mas ainda não foram configuradas.

- O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) Proteção de Limite | Serviços de Telecomunicações Externos

O acesso JIT (Just-In-Time) à máquina virtual bloqueia o tráfego de entrada às máquinas virtuais do Azure, reduzindo a exposição a ataques enquanto fornece acesso fácil para se conectar às VMs quando necessário. O acesso JIT à máquina virtual ajuda você a gerenciar exceções à sua política de fluxo de tráfego facilitando os processos de solicitação e aprovação de acesso. Esse blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) que ajuda você a monitorar máquinas virtuais que podem dar suporte ao acesso Just-In-Time, mas ainda não foram configuradas.

- O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>SC-8 (1) Confidencialidade e integridade de transmissão | Proteção física criptográfica ou alternativa

Este blueprint ajuda você a proteger a confidencialidade e a integridade das informações transmitidas por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que ajudam a monitorar o mecanismo criptográfico implementado para protocolos de comunicação. A garantia de que a comunicação seja criptografada corretamente pode ajudá-lo a atender aos requisitos de sua organização ou proteger as informações contra divulgação e modificação não autorizadas.

- O aplicativo de API só deve estar acessível via HTTPS
- Auditar os servidores Web do Windows que não estão usando protocolos de comunicação segura
- Implantar requisitos para auditar os servidores Web do Windows que não estão usando protocolos de comunicação segura
- O aplicativo de funções deve ser acessível apenas por HTTPS
- Apenas conexões seguras com o Cache Redis devem ser habilitadas
- A transferência segura para contas de armazenamento deve ser habilitada
- Aplicativo Web deve ser acessível somente por HTTPS

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) Proteção de Informações de em Repouso | Proteção de Criptografia

Este blueprint ajuda você a impor sua política sobre o uso de controles de criptografia para proteger informações em repouso por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que impõem controles de criptografia específicos e auditam o uso de configurações de criptografia fraca. Entender em que local os recursos do Azure podem ter configurações de criptografia não ideais pode ajudá-lo a tomar ações corretivas para garantir que os recursos sejam configurados de acordo com a política de segurança de informações. Especificamente, as definições de política atribuídas por esse blueprint exigem criptografia para contas do Data Lake Storage; exigem Transparent Data Encryption em bancos de dados SQL e auditam quanto à falta de criptografia em bancos de dados SQL, discos de máquina virtual e variáveis de conta de automação.

- A Segurança de Dados Avançada deve ser habilitada nas instâncias gerenciadas
- A Segurança de Dados Avançada deve ser habilitada nos servidores SQL
- Implantar Segurança de Dados Avançada em servidores SQL
- Implantar a Transparent Data Encryption no BD SQL
- A criptografia de disco deve ser aplicada em máquinas virtuais
- Exigir a criptografia em contas do Data Lake Storage
- A Transparent Data Encryption em bancos de dados SQL deve ser habilitada

## <a name="si-2-flaw-remediation"></a>SI-2 Correção de Falhas

Este blueprint ajuda você a gerenciar as falhas do sistema de informações por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que monitoram atualizações ausentes do sistema, vulnerabilidades do sistema operacional, vulnerabilidades do SQL e vulnerabilidades da máquina virtual na Central de Segurança do Azure. A Central de Segurança do Azure fornece funcionalidades de relatórios que permitem ter insights em tempo real sobre o estado de segurança de recursos implantados do Azure. Este blueprint também atribui uma definição de política que garante a aplicação de patch do sistema operacional para conjuntos de dimensionamento de máquinas virtuais.

- Exigir a aplicação automática de patch da imagem do sistema operacional em conjuntos de dimensionamento de máquinas virtuais
- As atualizações do sistema nos conjuntos de dimensionamento de máquinas virtuais devem ser instaladas
- As atualizações do sistema devem ser instaladas nas máquinas virtuais
- As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas
- As vulnerabilidades da configuração de segurança nas máquinas virtuais devem ser corrigidas
- As vulnerabilidades nos bancos de dados SQL devem ser corrigidas
- As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades

## <a name="si-3-malicious-code-protection"></a>SI-3 Proteção contra código mal-intencionado

Este blueprint ajuda você a gerenciar a proteção de ponto de extremidade, incluindo a proteção contra código mal-intencionado, por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que monitoram a proteção de ponto de extremidade ausente em máquinas virtuais na Central de Segurança do Azure e impõem a solução Microsoft Antimalware em máquinas virtuais do Windows.

- Implantar a extensão padrão antimalware de IaaS da Microsoft para Windows Server
- A solução de proteção de ponto de extremidade deve ser instalada nos conjuntos de dimensionamento de máquinas virtuais
- monitora o Endpoint Protection ausente na Central de Segurança do Azure

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) Proteção contra código mal-intencionado | Gerenciamento central

Este blueprint ajuda você a gerenciar a proteção do ponto de extremidade, incluindo a proteção contra código mal-intencionado, por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que monitoram a proteção de ponto de extremidade ausente em máquinas virtuais na Central de Segurança do Azure. A Central de Segurança do Azure fornece funcionalidades centralizadas de gerenciamento e relatórios que permitem ter insights em tempo real sobre o estado de segurança de recursos implantados do Azure.

- A solução de proteção de ponto de extremidade deve ser instalada nos conjuntos de dimensionamento de máquinas virtuais
- monitora o Endpoint Protection ausente na Central de Segurança do Azure

## <a name="si-4-information-system-monitoring"></a>SI-4 Monitoramento do sistema de informações

Esse blueprint ajuda a monitorar seu sistema por meio da auditoria e da imposição do registro em log e da segurança de dados em todos os recursos do Azure. Especificamente, as políticas atribuídas auditam e impõem a implantação do agente do Log Analytics e de configurações de segurança aprimoradas para bancos de dados SQL, contas de armazenamento e recursos de rede. Esses recursos podem ajudar você a detectar comportamentos anormais e indicadores de ataques para que possa tomar as devidas providências.

- \[Versão Prévia\]: Auditar a implantação do Agente do Log Analytics – imagem de VM (sistema operacional) não listada
- \[Versão Prévia\]: Auditar a implantação do Agente do Log Analytics no VMSS – imagem de VM (sistema operacional) não listada
- \[Versão Prévia\]: Auditar o espaço de trabalho do Log Analytics para a VM – Relatar incompatibilidade
- \[Versão Prévia\]: Implantar o Agente do Log Analytics no VMSS (Conjuntos de Dimensionamento de VMs) do Linux
- \[Versão Prévia\]: Implantar o Agente do Log Analytics para VMs do Linux
- \[Versão Prévia\]: Implantar o Agente do Log Analytics no VMSS (Conjuntos de Dimensionamento de VMs) do Windows
- \[Versão Prévia\]: Implantar o Agente do Log Analytics para VMs do Windows
- A Segurança de Dados Avançada deve ser habilitada nas instâncias gerenciadas
- A Segurança de Dados Avançada deve ser habilitada nos servidores SQL
- Implantar Segurança de Dados Avançada em servidores SQL
- Implantar a Proteção Avançada contra Ameaças em Contas de Armazenamento
- Implantar Auditoria em servidores SQL
- Implantar o observador de rede quando redes virtuais são criadas
- Implantar a Detecção de Ameaças em servidores SQL

## <a name="si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>SI-4 (18) Monitoramento do sistema de informações | Analisar o tráfego/vazamento oculto

A Proteção Avançada contra Ameaças do Armazenamento do Azure detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Os alertas de proteção incluem padrões de acesso anormais, extrações/carregamentos anormais e atividade de armazenamento suspeita. Esses indicadores podem ajudá-lo a detectar vazamento oculto de informações.

- Implantar a Proteção Avançada contra Ameaças em Contas de Armazenamento

> [!NOTE]
> A disponibilidade de definições específicas do Azure Policy pode variar no Azure Governamental e em outras nuvens nacionais.

## <a name="next-steps"></a>Próximas etapas

Agora que você examinou o mapeamento de controle do blueprint NIST SP 800-53 R4, leia os seguintes artigos para saber mais sobre o blueprint e sobre como implantar esta amostra:

> [!div class="nextstepaction"]
> [Blueprint NIST SP 800-53 R4 – visão geral](./index.md)
> [Blueprint NIST SP 800-53 R4 – etapas de implantação](./deploy.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).
