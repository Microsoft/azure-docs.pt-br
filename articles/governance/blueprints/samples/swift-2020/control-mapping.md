---
title: Controles de amostra de blueprint do SWIFT CSP-CSCF v2020
description: Mapeamento de controle da amostra de blueprint da SWIFT CSP-CSCF v2020. Cada controle é mapeado para uma ou mais definições do Azure Policy que auxiliam na avaliação.
ms.date: 04/02/2021
ms.topic: sample
ms.openlocfilehash: 9d385cfddae11b026cd93f49c1bc41ca48265a87
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106386169"
---
# <a name="control-mapping-of-the-swift-csp-cscf-v2020-blueprint-sample"></a>Mapeamento de controle da amostra de blueprint SWIFT CSP-CSCF v2020

O artigo a seguir fornece detalhes sobre como a amostra de blueprint SWIFT CSP-CSCF v2020 do Azure Blueprints é mapeada para os controles SWIFT CSP-CSCF v2020. Para obter mais informações sobre os controles, confira [SWIFT CSP-CSCF v2020](https://www.swift.com/myswift/customer-security-programme-csp).

Os mapeamentos a seguir referem-se aos controles **SWIFT CSP-CSCF v2020**. Use a navegação no lado direito para ir diretamente para um mapeamento de controle específico. Muitos dos controles mapeados são implementados com uma iniciativa do [Azure Policy](../../../policy/overview.md). Para examinar a iniciativa completa, abra **Política** no portal do Azure e selecione a página **Definições**. Em seguida, localize e selecione a iniciativa de política interna **\[Versão prévia\]: Auditar os controles SWIFT CSP-CSCF v2020 e implantar extensões de VM específicas para dar suporte aos requisitos de auditoria**.

> [!IMPORTANT]
> Cada controle abaixo está associado com uma ou mais definições do [Azure Policy](../../../policy/overview.md). Essas políticas podem ajudar você a [avaliar a conformidade](../../../policy/how-to/get-compliance-data.md) com o controle. No entanto, geralmente não há uma correspondência um para um ou completa entre um controle e uma ou mais políticas. Dessa forma, **Conformidade** no Azure Policy refere-se somente às próprias políticas. Não garante que você está totalmente em conformidade com todos os requisitos de um controle. Além disso, o padrão de conformidade inclui controles que não são abordados por nenhuma definição do Azure Policy no momento. Portanto, a conformidade no Azure Policy é somente uma exibição parcial do status de conformidade geral. As associações entre controles e definições do Azure Policy desta amostra de blueprint de conformidade podem ser alteradas ao longo do tempo. Para exibir o histórico de alterações, confira o [Histórico de Confirmações do GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/swift-2020/control-mapping.md).

## <a name="12-and-51-account-management"></a>1.2 e 5.1 Gerenciamento de Contas

Este blueprint ajuda você a examinar as contas que possam não estar em conformidade com os requisitos de gerenciamento de conta de sua organização. Este blueprint atribui definições do [Azure Policy](../../../policy/overview.md) que auditam contas externas com permissões de leitura, gravação e proprietário em contas de assinatura e preteridas. Revisando as contas auditadas por essas políticas, você pode adotar as medidas adequadas a fim de garantir que os requisitos de gerenciamento de conta sejam atendidos.

- As contas preteridas devem ser removidas de sua assinatura
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de leitura devem ser removidas de sua assinatura
- As contas externas com permissões de gravação devem ser removidas de sua assinatura

## <a name="26-51-64-and-65a-account-management--role-based-schemes"></a>2.6, 5.1, 6.4 e 6.5A Gerenciamento de Contas | Esquemas Baseados em Função

O Azure RBAC [(controle de acesso baseado em função)](../../../../role-based-access-control/overview.md) ajuda a gerenciar quem tem acesso aos recursos no Azure. Usando o portal do Azure, você pode examinar quem tem acesso aos recursos do Azure e suas permissões. Este blueprint também atribui definições do [Azure Policy](../../../policy/overview.md) para auditar o uso da autenticação do Azure Active Directory para servidores SQL e o Service Fabric. O uso da autenticação do Azure Active Directory permite o gerenciamento simplificado de permissões e o gerenciamento centralizado de identidades dos usuários de banco de dados e de outros serviços da Microsoft. Além disso, este blueprint atribui uma definição do Azure Policy para auditar o uso de regras personalizadas do Azure RBAC. Entender o local em que as regras personalizadas do Azure RBAC são implementadas pode ajudar você a verificar a necessidade e a implementação apropriada, pois as regras personalizadas do Azure RBAC estão sujeitas a erros.

- Um administrador do Azure Active Directory deve ser provisionado para servidores SQL
- Auditar VMs que não usam discos gerenciados
- Os clusters do Service Fabric só devem usar o Azure Active Directory para autenticação de cliente

## <a name="29a--account-management--account-monitoring--atypical-usage"></a>2.9A Gerenciamento de Contas | Monitoramento/Uso Atípico de Conta

O acesso JIT (Just-In-Time) à máquina virtual bloqueia o tráfego de entrada às máquinas virtuais do Azure, reduzindo a exposição a ataques enquanto fornece acesso fácil para se conectar às VMs quando necessário. Todas as solicitações JIT para acessar máquinas virtuais são registradas no Log de Atividades, permitindo que você monitore se há uso atípico. Esse blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) que ajuda você a monitorar máquinas virtuais que podem dar suporte ao acesso Just-In-Time, mas ainda não foram configuradas.

- As portas de gerenciamento de máquinas virtuais devem ser protegidas com o controle de acesso à rede Just-In-Time

## <a name="13-51-and-64-separation-of-duties"></a>1.3, 5.1 e 6.4 Diferenciação de Direitos

Ter apenas um proprietário de assinatura do Azure não permite a redundância administrativa. Por outro lado, ter muitos proprietários de assinatura do Azure pode aumentar o potencial de uma violação por meio de uma conta de proprietário comprometida. Este blueprint ajuda você a manter um número apropriado de proprietários de assinatura do Azure por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que auditam o número de proprietários de assinaturas do Azure. Este blueprint também atribui definições do Azure Policy que ajudam a controlar a associação ao grupo Administradores em máquinas virtuais do Windows. O gerenciamento de permissões de proprietário de assinatura e administrador de máquina virtual pode ajudá-lo a implementar uma diferenciação de funções apropriada.

- Um máximo de três proprietários deve ser designado para sua assinatura
- Mostrar os resultados da auditoria das VMs do Windows em que o grupo Administradores não contém todos os membros especificados
- Implantar pré-requisitos para auditar as VMs do Windows nas quais o grupo Administradores não contém todos os membros especificados
- Deve haver mais de um proprietário atribuído à sua assinatura

## <a name="13-51-and-64-least-privilege--review-of-user-privileges"></a>1.3, 5.1 e 6.4 Privilégios Mínimos | Revisão de Privilégios do Usuário

O [Azure RBAC (controle de acesso baseado em função)](../../../../role-based-access-control/overview.md) ajuda a gerenciar quem tem acesso aos recursos no Azure. Usando o portal do Azure, você pode examinar quem tem acesso aos recursos do Azure e suas permissões. Este blueprint atribui definições do [Azure Policy](../../../policy/overview.md) para auditar as contas que devem ser priorizadas para análise. Revisar esses indicadores de conta pode ajudar a garantir que controles de privilégios mínimos estejam implementados.

- Um máximo de três proprietários deve ser designado para sua assinatura
- Mostrar os resultados da auditoria das VMs do Windows que não ingressaram no domínio especificado
- Implantar os pré-requisitos para auditar as VMs do Windows que não ingressaram no domínio especificado
- Deve haver mais de um proprietário atribuído à sua assinatura

## <a name="22-and-27-security-attributes"></a>2.2 e 2.7 Atributos de Segurança

A capacidade de classificação e descoberta de dados da Segurança de Dados Avançada do Banco de Dados SQL do Azure fornece funcionalidades para descobrir, classificar, rotular e proteger os dados confidenciais em seus bancos de dados. Pode ser usada para fornecer visibilidade em seu estado de classificação do banco de dados e para controlar o acesso a dados confidenciais no banco de dados e, além de suas bordas. A Segurança de Dados Avançada pode ajudá-lo a garantir que as informações sejam associadas aos atributos de segurança apropriados de sua organização. Este blueprint atribui definições do [Azure Policy](../../../policy/overview.md) para monitorar e impor o uso da Segurança de Dados Avançada no SQL Server.

- A Segurança de Dados Avançada deve ser habilitada nos servidores SQL
- Implantar Segurança de Dados Avançada em servidores SQL

## <a name="22-27-41-and-61-remote-access--automated-monitoring--control"></a>2.2, 2.7, 4.1 e 6.1 Acesso Remoto | Controle/Monitoramento Automatizado

Este blueprint ajuda você a monitorar e controlar o acesso remoto por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) para monitorar se a depuração remota do aplicativo do Serviço de Aplicativo do Azure está desligada e definições de política que auditam máquinas virtuais do Linux que permitem conexões remotas em contas sem senhas. Este blueprint também atribui uma definição do Azure Policy que ajuda você a monitorar o acesso irrestrito a contas de armazenamento. Monitorar esses indicadores pode ajudá-lo a garantir que os métodos de acesso remoto estejam em conformidade com sua política de segurança.

- Mostrar os resultados da auditoria das VMs do Linux que permitem conexões remotas em contas sem senhas
- Implantar pré-requisitos para auditar as VMs do Linux que permitem conexões remotas em contas sem senhas
- As contas de armazenamento devem restringir o acesso da rede
- A depuração remota deve ser desligada para o aplicativo de API
- A depuração remota deve ser desativada para o aplicativo de funções
- Depuração remota deve ser desativada para o aplicativo da Web

## <a name="13-and-64-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>1.3 e 6.4 Conteúdo dos Registros de Auditoria | Gerenciamento Centralizado do Conteúdo do Registro de Auditoria Planejada

Dados de log coletados pelo Azure Monitor são armazenados em um workspace do Log Analytics, permitindo o gerenciamento e a configuração centralizada. Este blueprint ajuda você a garantir que os eventos sejam registrados em log por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que auditam e impõem a implantação do agente do Log Analytics em máquinas virtuais do Azure.

- \[Versão Prévia\]: Auditar a implantação do Agente do Log Analytics – imagem de VM (sistema operacional) não listada
- Implantar o Agente do Log Analytics para VMs do Linux
- Implantar o Agente do Log Analytics para VMs do Windows

## <a name="22-27-and-64-response-to-audit-processing-failures"></a>2.2, 2.7 e 6.4 Resposta a Falhas de Processamento de Auditoria

Este blueprint atribui definições do [Azure Policy](../../../policy/overview.md) que monitoram configurações de auditoria e log de eventos. Monitorar essas configurações pode fornecer um indicador de falha ou de configuração incorreta do sistema de auditoria e ajudá-lo a adotar uma ação corretiva.

- A Segurança de Dados Avançada deve ser habilitada nos servidores SQL
- Configuração de diagnóstico de auditoria
- A auditoria no SQL Server deve ser habilitada

## <a name="13-and-64-audit-review-analysis-and-reporting--central-review-and-analysis"></a>1.3 e 6.4 Revisão, Análise e Relatório de Auditoria | Revisão e Análise Central

Dados de log coletados pelo Azure Monitor são armazenados em um workspace do Log Analytics, permitindo análises e relatórios centralizados. Este blueprint ajuda você a garantir que os eventos sejam registrados em log por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que auditam e impõem a implantação do agente do Log Analytics em máquinas virtuais do Azure.

- \[Versão Prévia\]: Auditar a implantação do Agente do Log Analytics – imagem de VM (sistema operacional) não listada
- Implantar o Agente do Log Analytics para VMs do Linux
- Implantar o Agente do Log Analytics para VMs do Windows

## <a name="13-22-27-64-and-65a-audit-generation"></a>1.3, 2.2, 2.7, 6.4 e 6.5A Geração de Auditoria

Este blueprint ajuda você a garantir que os eventos do sistema sejam registrados em log por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que auditam as configurações do log de auditoria em recursos do Azure.
Essas definições de política auditam e impõem a implantação do agente do Log Analytics em máquinas virtuais do Azure, bem como as configurações de auditoria para outros tipos de recursos do Azure. Essas definições de política também auditam a configuração dos logs de diagnóstico para fornecer informações sobre as operações executadas em recursos do Azure. Além disso, a auditoria e a Segurança de Dados Avançada são configuradas em servidores SQL.

- Auditar a implantação do Agente do Log Analytics – imagem de VM (sistema operacional) não listada
- Implantar o Agente do Log Analytics no VMSS (Conjuntos de Dimensionamento de VMs) do Linux
- Implantar o Agente do Log Analytics para VMs do Linux
- Implantar o Agente do Log Analytics no VMSS (Conjuntos de Dimensionamento de VMs) do Windows
- Implantar o Agente do Log Analytics para VMs do Windows
- Configuração de diagnóstico de auditoria
- Auditar configurações de Auditoria de nível do servidor SQL
- A Segurança de Dados Avançada deve ser habilitada nos servidores SQL
- Implantar Segurança de Dados Avançada em servidores SQL
- A auditoria no SQL Server deve ser habilitada
- Implantar configurações de diagnóstico para grupos de segurança de rede

## <a name="11-least-functionality--prevent-program-execution"></a>1.1 Funcionalidade Mínima | Impedir a Execução do Programa

O controle de aplicativos adaptável na Central de Segurança do Azure é uma solução de filtragem inteligente, automatizada e de ponta a ponta que pode bloquear ou impedir a execução de programas de software específicos em suas máquinas virtuais. O controle de aplicativos pode ser executado no modo de imposição, que proíbe a execução de aplicativos não aprovados. Esse blueprint atribui uma definição do Azure Policy que ajuda você a monitorar as máquinas virtuais nas quais uma lista de permissões de aplicativos é recomendada, mas ainda não foi configurada.

- Os controles de aplicativos adaptáveis para definir aplicativos seguros devem ser habilitados nos computadores

## <a name="11-least-functionality--authorized-software--whitelisting"></a>1.1 Funcionalidade Mínima | Software Autorizado/Inclusão na Lista de Permissões

O controle de aplicativos adaptável na Central de Segurança do Azure é uma solução de filtragem inteligente, automatizada e de ponta a ponta que pode bloquear ou impedir a execução de programas de software específicos em suas máquinas virtuais. O controle de aplicativo ajuda a criar listas de aplicativos aprovados para suas máquinas virtuais. Esse blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) que ajuda você a monitorar as máquinas virtuais nas quais uma lista de permissões de aplicativos é recomendada, mas ainda não foi configurada.

- Os controles de aplicativos adaptáveis para definir aplicativos seguros devem ser habilitados nos computadores

## <a name="11-user-installed-software"></a>1.1 Software Instalado pelo Usuário

O controle de aplicativos adaptável na Central de Segurança do Azure é uma solução de filtragem inteligente, automatizada e de ponta a ponta que pode bloquear ou impedir a execução de programas de software específicos em suas máquinas virtuais. O controle de aplicativos pode ajudar a impor e a monitorar a conformidade com políticas de restrição de software. Esse blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) que ajuda você a monitorar as máquinas virtuais nas quais uma lista de permissões de aplicativos é recomendada, mas ainda não foi configurada.

- Os controles de aplicativos adaptáveis para definir aplicativos seguros devem ser habilitados nos computadores
- As máquinas virtuais devem ser migradas para os novos recursos do Azure Resource Manager

## <a name="42-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>4.2 Identificação e Autenticação (Usuários Organizacionais) | Acesso à rede para Contas com Privilégios

Este blueprint ajuda você a restringir e controlar o acesso privilegiado por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) para auditar as contas com permissões de gravação e/ou proprietário que não têm a autenticação multifator habilitada. A autenticação multifator ajuda a proteger contas, mesmo que uma única informação de autenticação seja comprometida. Monitorando as contas sem a autenticação multifator habilitada, você pode identificar as contas que têm mais probabilidade de serem comprometidas.

- O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura
- O MFA deve ser habilitado em contas com permissões de gravação em sua assinatura

## <a name="42-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>4.2 Identificação e Autenticação (Usuários Organizacionais) | Acesso à Rede para Contas sem Privilégios

Esse blueprint ajuda a restringir e controlar o acesso atribuindo uma definição do [Azure Policy](../../../policy/overview.md) para auditar contas com permissões de leitura que não têm a autenticação multifator habilitada. A autenticação multifator ajuda a proteger contas, mesmo que uma única informação de autenticação seja comprometida. Monitorando as contas sem a autenticação multifator habilitada, você pode identificar as contas que têm mais probabilidade de serem comprometidas.

- O MFA deve ser habilitado em contas com permissões de leitura em sua assinatura

## <a name="23-and-41-authenticator-management"></a>2.3 e 4.1 Gerenciamento de Autenticador

Este blueprint atribui definições do [Azure Policy](../../../policy/overview.md) que auditam as máquinas virtuais do Linux que permitem conexões remotas em contas sem senhas e/ou que têm permissões incorretas definidas no arquivo de senha. Este blueprint também atribui definições de política que auditam a configuração do tipo de criptografia de senha para máquinas virtuais do Windows. O monitoramento desses indicadores ajuda você a garantir que os autenticadores do sistema estejam em conformidade com a política de identificação e autenticação de sua organização.

- Mostrar os resultados da auditoria das VMs do Linux que não têm as permissões de arquivo de senha definidas como 0644
- Implantar requisitos para auditar as VMs do Linux que não têm as permissões de arquivo de senha definidas como 0644
- Mostrar os resultados da auditoria das VMs do Linux que têm contas sem senhas
- Implantar requisitos para auditar as VMs do Linux que têm contas sem senhas
- Mostrar os resultados da auditoria das VMs do Windows que não armazenam senhas usando a criptografia reversível
- Implantar requisitos para auditar as VMs do Windows que não armazenam senhas usando a criptografia reversível

## <a name="23-and-41-authenticator-management--password-based-authentication"></a>2.3 e 4.1 Gerenciamento de Autenticador | Autenticação Baseada em Senha

Este blueprint ajuda você a impor senhas fortes por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que auditam as máquinas virtuais do Windows que não impõem a força mínima e outros requisitos de senha. O reconhecimento de máquinas virtuais que estão violando a política de força da senha ajuda você a tomar ações corretivas para garantir que as senhas de todas as contas de usuário da máquina virtual estejam em conformidade com a política de senha de sua organização.

- Mostrar os resultados da auditoria das VMs do Windows que permitir reutilizar as 24 senhas anteriores
- Mostrar os resultados da auditoria das VMs do Windows que não têm uma duração máxima da senha de 70 dias
- Mostrar os resultados da auditoria das VMs do Windows que não têm uma duração mínima da senha de 1 dia
- Mostrar os resultados da auditoria das VMs do Windows que não têm a configuração de complexidade de senha habilitada
- Mostrar os resultados da auditoria das VMs do Windows que não restringem o tamanho mínimo da senha a 14 caracteres
- Mostrar os resultados da auditoria das VMs do Windows que não armazenam senhas usando a criptografia reversível
- Implantar pré-requisitos para auditar as VMs do Windows que permitem reutilizar as 24 senhas anteriores
- Implantar pré-requisitos para auditar as VMs do Windows que não têm uma duração máxima da senha de 70 dias
- Implantar pré-requisitos para auditar as VMs do Windows que não têm uma duração mínima da senha de 1 dia
- Implantar pré-requisitos para auditar as VMs do Windows que não têm a configuração de complexidade de senha habilitada
- Implantar pré-requisitos para auditar as VMs do Windows que não restringem o tamanho mínimo da senha a 14 caracteres
- Implantar pré-requisitos para auditar as VMs do Windows que não armazenam senhas usando a criptografia reversível

## <a name="22-and-27-vulnerability-scanning"></a>2.2 e 2.7 Verificação de Vulnerabilidade

Este blueprint ajuda você a gerenciar as vulnerabilidades do sistema de informações por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que monitoram vulnerabilidades do sistema operacional, vulnerabilidades do SQL e vulnerabilidades da máquina virtual na Central de Segurança do Azure.
A Central de Segurança do Azure fornece funcionalidades de relatórios que permitem ter insights em tempo real sobre o estado de segurança de recursos implantados do Azure. Este blueprint também atribui definições de política que auditam e impõem a Segurança de Dados Avançada em servidores SQL. A segurança de dados avançada inclui recursos de avaliação de vulnerabilidade e proteção avançada contra ameaças para ajudá-lo a entender as vulnerabilidades de seus recursos implantados.

- A Segurança de Dados Avançada deve ser habilitada nos servidores SQL
- A auditoria no SQL Server deve ser habilitada
- As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas
- As vulnerabilidades nos bancos de dados SQL devem ser corrigidas 
- As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas

## <a name="13-denial-of-service-protection"></a>1.3 Proteção contra Negação de Serviço

O nível Standard da proteção contra DDoS (ataque de negação de serviço distribuído) do Azure fornece recursos adicionais e funcionalidades de mitigação com relação à camada de serviço básica. Esses recursos adicionais incluem a integração do Azure Monitor e a capacidade de examinar relatórios de mitigação pós-ataque. Esse blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) que audita se o nível padrão da proteção contra DDoS está habilitada. Compreender a diferença de funcionalidade entre as camadas de serviço pode ajudar você a selecionar a melhor solução para tratar das proteções contra negação de serviço para seu ambiente do Azure.

- A Proteção contra DDoS do Azure Standard deve estar habilitada

## <a name="11-and-61-boundary-protection"></a>1.1 e 6.1 Proteção de Limite

Este blueprint ajuda você a gerenciar e controlar o limite do sistema por meio da atribuição de uma definição do [Azure Policy](../../../policy/overview.md) que monitora as recomendações de proteção do grupo de segurança de rede na Central de Segurança do Azure. A Central de Segurança do Azure analisa padrões de tráfego de máquinas virtuais voltadas para a Internet e fornece recomendações de regras de grupo de segurança de rede para reduzir a superfície de ataque potencial. Além disso, este blueprint atribui definições de política que monitoram pontos de extremidade, contas de armazenamento e aplicativos desprotegidos. Os pontos de extremidade e os aplicativos que não estão protegidos por um firewall e as contas de armazenamento com acesso irrestrito podem permitir o acesso não intencional às informações contidas no sistema de informações.

- As recomendações da Proteção de Rede Adaptável devem ser aplicadas nas máquinas virtuais para a Internet
- O acesso pelo ponto de extremidade para a Internet deve ser restrito
- Auditar o acesso irrestrito à rede para contas de armazenamento

## <a name="29a-boundary-protection--access-points"></a>2.9A Proteção de Limite | Pontos de Acesso

O acesso JIT (Just-In-Time) à máquina virtual bloqueia o tráfego de entrada às máquinas virtuais do Azure, reduzindo a exposição a ataques enquanto fornece acesso fácil para se conectar às VMs quando necessário. O acesso JIT à máquina virtual ajuda a limitar o número de conexões externas a seus recursos no Azure. Esse blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) que ajuda você a monitorar máquinas virtuais que podem dar suporte ao acesso Just-In-Time, mas ainda não foram configuradas.

- As portas de gerenciamento de máquinas virtuais devem ser protegidas com o controle de acesso à rede Just-In-Time

## <a name="29a-boundary-protection--external-telecommunications-services"></a>2.9A Proteção de Limite | Serviços de Telecomunicações Externas

O acesso JIT (Just-In-Time) à máquina virtual bloqueia o tráfego de entrada às máquinas virtuais do Azure, reduzindo a exposição a ataques enquanto fornece acesso fácil para se conectar às VMs quando necessário. O acesso JIT à máquina virtual ajuda você a gerenciar exceções à sua política de fluxo de tráfego facilitando os processos de solicitação e aprovação de acesso. Esse blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) que ajuda você a monitorar máquinas virtuais que podem dar suporte ao acesso Just-In-Time, mas ainda não foram configuradas.

- As portas de gerenciamento de máquinas virtuais devem ser protegidas com o controle de acesso à rede Just-In-Time

## <a name="21-24-24a-25a-and-26-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>2.1, 2.4, 2.4A, 2.5A e 2.6 Confidencialidade e Integridade de Transmissão | Proteção Física Criptográfica ou Alternativa

Este blueprint ajuda você a proteger a confidencialidade e a integridade das informações transmitidas por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que ajudam a monitorar o mecanismo criptográfico implementado para protocolos de comunicação. A garantia de que a comunicação é criptografada corretamente pode ajudar você a atender aos requisitos de sua organização ou proteger as informações contra divulgação e modificação não autorizadas.

- O aplicativo de API só deve estar acessível via HTTPS
- Mostrar os resultados da auditoria dos servidores Web do Windows que não estão usando protocolos de comunicação segura
- Implantar pré-requisitos para auditar os servidores Web do Windows que não estão usando protocolos de comunicação segura
- O aplicativo de funções deve ser acessível apenas por HTTPS
- Apenas conexões seguras com o Cache Redis devem ser habilitadas
- A transferência segura para contas de armazenamento deve ser habilitada
- Aplicativo Web deve ser acessível somente por HTTPS

## <a name="22-23-25-41-and-27-protection-of-information-at-rest--cryptographic-protection"></a>2.2, 2.3, 2.5, 4.1 e 2.7 Proteção de Informações em Repouso | Proteção Criptográfica

Este blueprint ajuda você a impor sua política sobre o uso de controles de criptografia para proteger informações em repouso por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que impõem controles de criptografia específicos e auditam o uso de configurações de criptografia fraca. Entender em que local os recursos do Azure podem ter configurações de criptografia não ideais pode ajudá-lo a tomar ações corretivas para garantir que os recursos sejam configurados de acordo com a política de segurança de informações. Especificamente, as definições de política atribuídas por esse blueprint exigem criptografia para contas do Data Lake Storage; exigem Transparent Data Encryption em bancos de dados SQL e auditam quanto à falta de criptografia em bancos de dados SQL, discos de máquina virtual e variáveis de conta de automação.

- A Segurança de Dados Avançada deve ser habilitada nos servidores SQL
- Implantar Segurança de Dados Avançada em servidores SQL
- Implantar a Transparent Data Encryption no BD SQL
- A Transparent Data Encryption em bancos de dados SQL deve ser habilitada

## <a name="13-22-and-27-flaw-remediation"></a>1.3, 2.2 e 2.7 Correção de Falhas

Este blueprint ajuda você a gerenciar as falhas do sistema de informações por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que monitoram atualizações ausentes do sistema, vulnerabilidades do sistema operacional, vulnerabilidades do SQL e vulnerabilidades da máquina virtual na Central de Segurança do Azure. A Central de Segurança do Azure fornece funcionalidades de relatórios que permitem ter insights em tempo real sobre o estado de segurança de recursos implantados do Azure. Este blueprint também atribui uma definição de política que garante a aplicação de patch do sistema operacional para conjuntos de dimensionamento de máquinas virtuais.

- Exigir a aplicação automática de patch da imagem do sistema operacional em conjuntos de dimensionamento de máquinas virtuais
- As atualizações do sistema nos conjuntos de dimensionamento de máquinas virtuais devem ser instaladas
- As atualizações do sistema devem ser instaladas nas máquinas virtuais
- Auditar a implantação do Dependency Agent em conjuntos de dimensionamento de máquinas virtuais – imagem de VM (sistema operacional) não listada
- As variáveis da conta de automação devem ser criptografadas
- As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas
- As vulnerabilidades da configuração de segurança nas máquinas virtuais devem ser corrigidas
- As vulnerabilidades nos bancos de dados SQL devem ser corrigidas

## <a name="61-malicious-code-protection"></a>6.1 Proteção contra Código Mal-intencionado

Este blueprint ajuda você a gerenciar a proteção de ponto de extremidade, incluindo a proteção contra código mal-intencionado, por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que monitoram a proteção de ponto de extremidade ausente em máquinas virtuais na Central de Segurança do Azure e impõem a solução Microsoft Antimalware em máquinas virtuais do Windows.

- Implantar a extensão padrão antimalware de IaaS da Microsoft para Windows Server
- A solução de proteção de ponto de extremidade deve ser instalada nos conjuntos de dimensionamento de máquinas virtuais
- monitora o Endpoint Protection ausente na Central de Segurança do Azure
- As contas de armazenamento devem ser migradas para os novos recursos do Azure Resource Manager

## <a name="61-malicious-code-protection--central-management"></a>6.1 Proteção contra Código Mal-intencionado | Gerenciamento Central

Este blueprint ajuda você a gerenciar a proteção do ponto de extremidade, incluindo a proteção contra código mal-intencionado, por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que monitoram a proteção de ponto de extremidade ausente em máquinas virtuais na Central de Segurança do Azure. A Central de Segurança do Azure fornece funcionalidades centralizadas de gerenciamento e relatórios que permitem ter insights em tempo real sobre o estado de segurança de recursos implantados do Azure.

- A solução de proteção de ponto de extremidade deve ser instalada nos conjuntos de dimensionamento de máquinas virtuais
- monitora o Endpoint Protection ausente na Central de Segurança do Azure

## <a name="11-13-22-27-28-and-64-information-system-monitoring"></a>1.1, 1.3, 2.2, 2.7, 2.8 e 6.4 Monitoramento do Sistema de Informações

Esse blueprint ajuda a monitorar seu sistema por meio da auditoria e da imposição do registro em log e da segurança de dados em todos os recursos do Azure. Especificamente, as políticas atribuídas auditam e impõem a implantação do agente do Log Analytics e de configurações de segurança aprimoradas para bancos de dados SQL, contas de armazenamento e recursos de rede. Esses recursos podem ajudar você a detectar comportamentos anormais e indicadores de ataques para que possa tomar as devidas providências.

- Mostrar os resultados da auditoria das VMs do Windows nas quais o agente do Log Analytics não está conectado conforme o esperado
- Implantar o Agente do Log Analytics no VMSS (Conjuntos de Dimensionamento de VMs) do Linux
- Implantar o Agente do Log Analytics para VMs do Linux
- Implantar o Agente do Log Analytics no VMSS (Conjuntos de Dimensionamento de VMs) do Windows
- Implantar o Agente do Log Analytics para VMs do Windows
- A Segurança de Dados Avançada deve ser habilitada nos servidores SQL
- As configurações da Segurança de Dados Avançada para o SQL Server devem conter um endereço de email para receber alertas de segurança
- Os logs de diagnóstico no Azure Stream Analytics devem ser habilitados
- Implantar Segurança de Dados Avançada em servidores SQL
- Implantar Auditoria em servidores SQL
- Implantar o observador de rede quando redes virtuais são criadas
- Implantar a Detecção de Ameaças em servidores SQL

## <a name="22-and-28-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>2.2 e 2.8 Monitoramento do Sistema de Informações | Análise de Tráfego/Exportação Oculta

A Proteção Avançada contra Ameaças do Armazenamento do Azure detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Os alertas de proteção incluem padrões de acesso anormais, extrações/carregamentos anormais e atividade de armazenamento suspeita. Esses indicadores podem ajudá-lo a detectar vazamento oculto de informações.

- Implantar a Detecção de Ameaças em servidores SQL

> [!NOTE]
> A disponibilidade de definições específicas do Azure Policy pode variar no Azure Governamental e em outras nuvens nacionais.

## <a name="next-steps"></a>Próximas etapas

Agora que você examinou o mapeamento de controle do blueprint SWIFT CSP-CSCF v2020, acesse os seguintes artigos para saber mais sobre o blueprint e como implantar esta amostra:

> [!div class="nextstepaction"]
> [Blueprint SWIFT CSP-CSCF v2020 – Visão geral](./index.md)
> [Blueprint SWIFT CSP-CSCF v2020 – Etapas de implantação](./deploy.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).
