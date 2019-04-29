---
title: Definições de diretiva do Azure monitoradas na Central de Segurança do Azure | Microsoft Docs
description: Definições de política do Azure monitoradas na Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: c89cb1aa-74e8-4ed1-980a-02a7a25c1a2f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 9d9369afd36f64c27cd2222cab0de5912aa913de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60909189"
---
# <a name="azure-security-policies-monitored-by-security-center"></a>Políticas de segurança do Azure monitoradas pela Central de segurança
Este artigo fornece uma lista de definições de política do Azure que você pode monitorar na Central de segurança do Azure. Para obter mais informações sobre políticas de segurança, confira [Trabalhando com políticas de segurança](tutorial-security-policy.md).

## <a name="available-security-policies"></a>Políticas de segurança disponíveis

Para saber mais sobre as políticas internas que são monitoradas pela Central de segurança, consulte a tabela a seguir:

| Política | O que faz a política |
| --- | --- |
|Logs de auditoria habilitação do diagnóstico no Azure Service Fabric e conjuntos de dimensionamento de máquina virtual|É recomendável que você habilite os logs para que uma trilha de atividade está disponível para investigação após um incidente ou comprometimento.|
|Regras de autorização em namespaces de Hubs de eventos de auditoria|Os clientes de Hubs de eventos do Azure não devem usar uma política de acesso de nível de namespace que fornece acesso a todas as filas e tópicos em um namespace. Para alinhar com o modelo de segurança de privilégio mínimo, você deve criar políticas de acesso no nível de entidade para filas e tópicos para fornecer acesso a apenas a entidade específica.|
|Auditar a existência de regras de autorização em entidades de Hubs de eventos|Audite a existência de regras de autorização em entidades de Hubs de eventos para conceder acesso de privilégio mínimo.|
|Auditar o acesso irrestrito à rede para contas de armazenamento|Audite o acesso irrestrito à rede nas configurações de firewall da conta de armazenamento. Configure regras de rede para que somente aplicativos das redes permitidas podem acessar a conta de armazenamento. Para permitir conexões da internet específicos ou em clientes locais, conceda acesso para o tráfego de redes virtuais do Azure de específicas ou para a internet pública endereço IP intervalos.|
|Auditar o uso de regras personalizadas do RBAC|As funções internas, como "Proprietário, colaborador, leitor" em vez de funções RBAC (controle) de acesso baseado em função personalizada, que estão sujeitas a erros de auditoria. Uso de funções personalizadas é tratado como uma exceção e requer análise rigorosa e modelagem de ameaças.|
|Auditar a ativação de logs de diagnóstico no Azure Stream Analytics|Habilitação de logs de auditoria e mantê-los para até um ano. Isso cria trilhas de atividades para investigação quando ocorre um incidente de segurança ou sua rede está comprometido.|
|Auditar transferência segura para contas de armazenamento|Os requisitos de transferência segura em sua conta de armazenamento de auditoria. A transferência segura é uma opção que força a sua conta de armazenamento a aceitar somente solicitações de conexões seguras (HTTPS). Uso de HTTPS garante a autenticação entre o servidor e o serviço. Ele também protege os dados em trânsito contra ataques de camada de rede, como o man-in-the-middle, interceptação e sequestro de sessão.|
|Auditoria de provisionamento de um administrador do Active Directory do Azure para SQL Server|Auditoria de provisionamento de um administrador do Azure Active Directory (Azure AD) para o SQL Server habilitar a autenticação do Azure AD. Autenticação do Azure AD dá suporte a gerenciamento de permissões simplificado e gerenciamento centralizado de identidades de usuários de banco de dados e outros serviços da Microsoft.|
|Regras de autorização de auditoria em namespaces de Barramento de Serviço do Microsoft Azure|Clientes do barramento de serviço do Azure não devem usar uma política de acesso de nível de namespace que fornece acesso a todas as filas e tópicos em um namespace. Para alinhar com o modelo de segurança de privilégio mínimo, crie políticas de acesso no nível de entidade para filas e tópicos para fornecer acesso a apenas a entidade específica.|
|Auditoria de ativação de logs de diagnóstico no Barramento de Serviço|Habilitação de logs de auditoria e mantê-los para cima em um ano. Isso cria trilhas de atividades para investigação quando ocorre um incidente de segurança ou sua rede está comprometido.|
|Auditar a configuração da propriedade ClusterProtectionLevel para EncryptAndSign no Service Fabric|O Service Fabric fornece três níveis de proteção para a comunicação de nó para nó que usa um certificado de cluster primário: Nenhum, assinar e EncryptAndSign. Defina o nível de proteção para garantir que todas as mensagens de nó a nó sejam criptografadas e assinadas digitalmente.|
|Auditar o uso do Azure Active Directory para autenticação de cliente no Service Fabric|Auditar o uso de autenticação de cliente somente por meio do Azure AD no Service Fabric.|
|Auditoria de habilitação de logs de diagnóstico para o serviço de pesquisa|Habilitação de logs de auditoria e mantê-los para até um ano. Isso cria trilhas de atividades para investigação quando ocorre um incidente de segurança ou sua rede está comprometido.|
|Auditar a habilitação de apenas conexões seguras com o Cache do Azure para Redis|Auditoria habilitando apenas de conexões por meio de SSL para o Cache do Azure para Redis. O uso de conexões seguras garante a autenticação entre o servidor e o serviço. Ele também protege os dados em trânsito contra ataques de camada de rede, como o man-in-the-middle, interceptação e sequestro de sessão.|
|Auditoria de Habilitação de logs de diagnóstico em Aplicativo Lógico do Azure|Habilitação de logs de auditoria e mantê-los para até um ano. Isso cria trilhas de atividades para investigação quando ocorre um incidente de segurança ou sua rede está comprometido.|
|Auditoria de ativação de logs de diagnóstico no Key Vault|Habilitação de logs de auditoria e mantê-los para até um ano. Isso cria trilhas de atividades para investigação quando ocorre um incidente de segurança ou sua rede está comprometido.|
|Habilitar logs de diagnóstico em Hubs de eventos de auditoria|Habilitação de logs de auditoria e mantê-los para até um ano. Isso cria trilhas de atividades para investigação quando ocorre um incidente de segurança ou sua rede está comprometido.|
|Auditoria de ativação de logs de diagnóstico no Azure Data Lake Store|Habilitação de logs de auditoria e mantê-los até um ano. Isso cria trilhas de atividades para investigação quando ocorre um incidente de segurança ou sua rede está comprometido.|
|Auditar a habilitação dos logs de diagnóstico no Data Lake Analytics|Habilitação de logs de auditoria e mantê-los para até um ano. Isso cria trilhas de atividades para investigação quando ocorre um incidente de segurança ou sua rede está comprometido.|
|Auditar o uso de contas de armazenamento clássicas|Use o Gerenciador de recursos do Azure para suas contas de armazenamento para fornecer melhorias de segurança. Estão incluídos: <br>-Mais forte controle de acesso (RBAC)<br>-Auditoria melhor<br>– Governança e implantação com base no Resource Manager do azure<br>-Acesso a identidades gerenciadas<br>-Acesso ao Azure Key Vault para segredos<br>-Autenticação baseada em AD azure<br>-Suporte para marcas e os grupos de recursos para facilitar o gerenciamento de segurança|
|Auditar o uso de máquinas virtuais clássicas|Use o Azure Resource Manager para suas máquinas virtuais para fornecer melhorias de segurança.  Estão incluídos: <br>-Mais forte controle de acesso (RBAC)<br>-Auditoria melhor<br>– Governança e implantação com base no Resource Manager do azure<br>-Acesso a identidades gerenciadas<br>-Acesso ao Azure Key Vault para segredos<br>-Autenticação baseada em AD azure<br>-Suporte para marcas e os grupos de recursos para facilitar o gerenciamento de segurança|
|Auditar a configuração de regras de alerta de métrica em contas do lote|Fazer auditoria da configuração de regras de alerta de métrica em contas de lote do Azure para habilitar a métrica necessária.|
|Auditar a ativação de logs de diagnóstico em contas em lote|Habilitação de logs de auditoria e mantê-los para até um ano. Isso cria trilhas de atividades para investigação quando ocorre um incidente de segurança ou sua rede está comprometido.|
|Habilitação da criptografia de variáveis de conta de automação de auditoria|É importante habilitar a criptografia de ativos de variável de conta de automação do Azure quando você armazena dados confidenciais.|
|Habilitação de logs de diagnóstico no serviço de aplicativo de auditoria|Auditoria de ativação de logs de diagnóstico no aplicativo. Isso cria trilhas de atividades para investigação quando ocorre um incidente de segurança ou sua rede está comprometido.|
|Auditar o status da Transparent Data Encryption|Auditoria de status de criptografia de dados transparente para bancos de dados SQL.|
|Auditar as configurações de auditoria de nível do SQL Server|Audite a existência de auditoria no nível do servidor do SQL.|
|\[Versão prévia]: monitora o banco de dados SQL não criptografado na Central de Segurança do Azure|A Central de segurança do Azure monitora não criptografados SQL servers ou bancos de dados, conforme recomendado.|
|\[Versão prévia]: monitora o banco de dados SQL não auditado na Central de Segurança do Azure|Central de segurança do Azure monitora servidores SQL e bancos de dados que não têm auditoria SQL ligada conforme recomendado.|
|\[Versão prévia]: monitora atualizações de sistemas ausentes na Central de Segurança do Azure|A Central de segurança do Azure monitora as atualizações de sistema de segurança ausentes em seus servidores, conforme recomendado.|
|\[Versão prévia]: audita a criptografia de blob ausente para contas de armazenamento|Auditar contas de armazenamento que não usam a criptografia de blob. Isso se aplica somente aos tipos de recursos de armazenamento da Microsoft, não o armazenamento de outros provedores. A Central de segurança do Azure monitora o acesso just-in-time à rede de possíveis conforme recomendado.|
|\[Versão prévia]: Monitorar o acesso just-in-time a rede possível na Central de segurança do Azure|A Central de segurança do Azure monitora o acesso just-in-time à rede de possíveis conforme recomendado.|
|\[Versão prévia]: monitora a possível lista de permissões de aplicativo na Central de Segurança do Azure|A Central de segurança do Azure monitora a configuração de lista de permissões de aplicativo possível.|
|\[Versão prévia]: monitora o acesso permissivo à rede na Central de Segurança do Azure|Central de segurança do Azure monitora rede grupos de segurança que têm regras muito permissivas, como recomendado.|
|\[Versão prévia]: monitora a vulnerabilidades do sistema operacional na Central de Segurança do Azure|A Central de segurança do Azure monitora os servidores que não satisfazem a linha de base configurada conforme recomendado.| 
|\[Versão prévia]: monitora o Endpoint Protection ausente na Central de Segurança do Azure|A Central de segurança do Azure monitora os servidores que não têm um agente do Microsoft System Center Endpoint Protection instalado, conforme recomendado.|
|\[Versão prévia]: Monitorar discos VM não criptografados na Central de segurança do Azure|Central de segurança do Azure monitora as máquinas virtuais que não tem criptografia de disco habilitada como recomendado.|
|\[Versão prévia]: Monitorar vulnerabilidades de VM na Central de segurança do Azure|Monitorar vulnerabilidades detectadas pela solução de avaliação de vulnerabilidade e as VMs que não têm uma solução de avaliação de vulnerabilidade na Central de segurança do Azure, conforme recomendado.|
|\[Versão prévia]: monitora o aplicativo Web desprotegido na Central de Segurança do Azure|Central de segurança do Azure monitora aplicativos web que não têm a proteção de firewall do aplicativo web conforme recomendado.|
|\[Versão prévia]: monitora pontos de extremidade de rede desprotegidos na Central de Segurança do Azure|Central de segurança do Azure monitora os pontos de extremidade que não têm a proteção de firewall próxima geração, conforme recomendado de rede.|
|\[Versão prévia]: Monitora os resultados da avaliação de vulnerabilidade do SQL na Central de Segurança do Azure|Avaliação de vulnerabilidade de monitor examinar os resultados e recomendações corrigir as vulnerabilidades do banco de dados.|
|\[Versão prévia]: audita o número máximo de proprietários de uma assinatura|É recomendável que você designe até três proprietários de assinaturas para reduzir a possibilidade de violação por um proprietário comprometido.|
|\[Versão prévia]: audita o número mínimo de proprietários da assinatura|É recomendável que você designar mais de um proprietário de assinatura para garantir o acesso de administrador redundância.|
|\[Versão prévia]: audita contas com permissões de proprietário que não são habilitadas para MFA em uma assinatura|A autenticação multifator (MFA) deve ser habilitada para todas as contas de assinatura que tem permissões de proprietário para impedir uma violação de contas ou recursos.|
|\[Versão prévia]: audita contas com permissões de gravação que não são habilitadas para MFA em uma assinatura|A autenticação multifator deve ser habilitada para todas as contas de assinatura que tenham permissões de gravação para impedir violação de contas ou recursos.|
|\[Versão prévia]: audita contas com permissões de leitura que não são habilitadas para MFA em uma assinatura|A autenticação multifator deve ser habilitada para todas as contas de assinatura que tem permissões de leitura para evitar a violação de contas ou recursos.|
|\[Versão prévia]: audita contas descontinuadas com permissões de proprietário em uma subscrição|Contas preteridas que têm permissões de proprietário devem ser removidas da sua assinatura. Contas preteridas foram bloqueadas de entrar no.|
|\[Versão prévia]: audita contas descontinuadas em uma subscrição|Contas descontinuadas devem ser removidas de suas assinaturas. Contas preteridas foram bloqueadas de entrar no.|
|\[Versão prévia]: audita contas externas com permissões de proprietário em uma assinatura|Contas externas com permissões de proprietário devem ser removidas de sua assinatura para impedir o acesso de permissões.|
|\[Versão prévia]: audita contas externas com permissões de gravação em uma assinatura|Contas externas que têm as permissões devem ser removidas de sua assinatura para impedir o acesso não monitorado de gravação.|
|\[Versão prévia]: audita contas externas com permissões de leitura em uma assinatura|Contas externas que tem permissões de leitura devem ser removidas de sua assinatura para impedir o acesso não monitorado.|




## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como configurar políticas de segurança na Central de Segurança. Para saber mais sobre a Central de segurança, consulte os artigos a seguir.

* [Guia de planejamento e operações da Central de Segurança do Azure](security-center-planning-and-operations-guide.md): Saiba como planejar e entender as considerações de design na Central de segurança do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): Saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciar e responder a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): Saiba como gerenciar e responder aos alertas de segurança.
* [Monitorar soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md): Saiba como monitorar o status da integridade das soluções dos parceiros.
* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md): Obtenha respostas para perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/): Encontre postagens no blog sobre a conformidade e segurança do Azure.

Para saber mais sobre a política do Azure, consulte [o que é o Azure Policy?](../governance/policy/overview.md).
