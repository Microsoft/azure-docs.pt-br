---
title: Administrando Azure Active Directory contas de serviço
description: Princípios e procedimentos para gerenciar o ciclo de vida de contas de serviço no Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f540ab40a14af09aa8667860286021f572eb6f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587892"
---
# <a name="governing-azure-ad-service-accounts"></a>Governando contas de serviço do Azure AD

Há três tipos de contas de serviço no Azure Active Directory (AD do Azure): [identidades gerenciadas](service-accounts-managed-identities.md), [entidades de serviço](service-accounts-principal.md)e contas de usuário empregadas como contas de serviço. À medida que você cria essas contas de serviço para uso automático, elas recebem permissões para acessar recursos no Azure e no Azure AD. Os recursos podem incluir serviços de Microsoft 365, aplicativos SaaS (software como serviço), aplicativos personalizados, bancos de dados, sistemas de RH e assim por diante. O controle das contas de serviço do Azure AD significa que você gerencia a criação, as permissões e o ciclo de vida para garantir a segurança e a continuidade.

> [!IMPORTANT] 
> Não recomendamos o uso de contas de usuário como contas de serviço, pois elas são inerentemente menos seguras. Isso inclui contas de serviço locais que são sincronizadas com o Azure AD, pois elas não são convertidas em entidades de serviço. Em vez disso, recomendamos o uso de identidades gerenciadas ou entidades de serviço. Observe que, neste momento, o uso de políticas de acesso condicional não é possível com entidades de serviço, mas a funcionalidade está chegando.


## <a name="plan-your-service-account"></a>Planejar sua conta de serviço

Antes de criar uma conta de serviço ou registrar um aplicativo, documente as informações de chave da conta de serviço. Ter informações documentadas torna mais fácil monitorar e governar a conta com eficiência. É recomendável coletar os dados a seguir e controlá-los no seu banco de dados de gerenciamento de configuração centralizado (CMDB).

| Dados| Descrição| Detalhes |
| - | - | - |
| Proprietário| Usuário ou grupo que é uma conta para gerenciar e monitorar a conta de serviço.| Provisione o proprietário com as permissões necessárias para monitorar a conta e implementar uma maneira de mitigar problemas. A mitigação de problemas pode ser feita pelo proprietário ou por meio de uma solicitação para ele. |
| Finalidade| Como a conta será usada.| Mapeie a conta de serviço para um serviço, aplicativo ou script específico. Evite criar contas de serviço de uso múltiplo. |
| Permissões (escopos)| Conjunto de permissões previsto.| Documente os recursos que serão acessados e as permissões para esses recursos. |
| Link do CMDB| Link para os recursos a serem acessados e scripts em que a conta de serviço é usada.| Certifique-se de documentar os proprietários de recurso e script para que você possa comunicar todos os efeitos de upstream e downstream necessários das alterações. |
| Avaliação de risco| Risco e impacto nos negócios se a conta fosse comprometida.| Use essas informações para restringir o escopo de permissões e determinar quem deve ter acesso às informações da conta. |
| Período para revisão| O agendamento no qual a conta de serviço deve ser revisada pelo proprietário.| Use isso para agendar análises e comunicações de revisão. Documente o que deve acontecer se uma revisão não for executada por um horário específico após o período de revisão agendado. |
| Tempo de vida| Tempo de vida máximo previsto da conta.| Use isso para agendar comunicações com o proprietário e, por fim, desabilitar as contas. Sempre que possível, defina uma data de validade para as credenciais, em que as credenciais não podem ser transferidas automaticamente. |
| Nome| Nome padronizado da conta| Crie um esquema de nomeação para todas as contas de serviço para que você possa Pesquisar, classificar e filtrar facilmente as contas de serviço. |


## <a name="use-the-principle-of-least-privileges"></a>Usar o princípio de privilégios mínimos
Conceda à conta de serviço somente as permissões necessárias para executar suas tarefas e não mais. Se uma conta de serviço precisar de permissões de alto nível, por exemplo, um nível de privilégio de administrador global, avalie por que e tente reduzir as permissões necessárias.

Recomendamos as práticas a seguir para privilégios de conta de serviço.

**Permissões**

* Não atribua funções internas a contas de serviço. Em vez disso, use o [modelo de concessão de permissão OAuth2 para Microsoft Graph](/graph/api/resources/oauth2permissiongrant),

* Se a entidade de serviço tiver que ser atribuída uma função privilegiada, considere atribuir uma [função personalizada](../roles/custom-create.md) com privilégios específicos, necessários, em uma forma de limite de tempo.

* Não inclua contas de serviço como membros de qualquer grupo com permissões elevadas. 

* [Use o PowerShell para enumerar membros de funções com privilégios](/powershell/module/azuread/get-azureaddirectoryrolemember), como   
`Get-AzureADDirectoryRoleMember`e filtro para ObjectType "entidade de serviço".

   ou use  
`Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

* [Use escopos OAuth 2,0](../develop/v2-permissions-and-consent.md) para limitar a funcionalidade que uma conta de serviço pode acessar em um recurso.
* As entidades de serviço e as identidades gerenciadas podem usar escopos OAuth 2,0 em um contexto delegado que está representando um usuário conectado ou como uma conta de serviço no contexto do aplicativo. No contexto do aplicativo, não é conectado.

* Verifique os escopos solicitação de contas de serviço para obter recursos para garantir que eles sejam apropriados. Por exemplo, se uma conta estiver solicitando arquivos. ReadWrite. All, avalie se ele realmente precisa apenas de File. Read. All. Para obter mais informações sobre permissões, consulte para [Microsoft Graph referência de permissão](/graph/permissions-reference).

* Verifique se você confia no desenvolvedor do aplicativo ou API com o acesso solicitado aos seus recursos.

**Duration**

* Limite as credenciais de conta de serviço (segredo do cliente, certificado) a um período de uso previsto.

* Agendar análises periódicas o uso e a finalidade de contas de serviço. Certifique-se de que as revisões sejam realizadas antes da expiração da conta.

Depois de ter uma compreensão clara da finalidade, do escopo e das permissões necessárias, crie sua conta de serviço. 

[Criar e usar identidades gerenciadas](../../app-service/overview-managed-identity.md?tabs=dotnet)

[Criar e usar entidades de serviço](../develop/howto-create-service-principal-portal.md)

Use uma identidade gerenciada quando possível. Se você não puder usar uma identidade gerenciada, use uma entidade de serviço. Se você não puder usar uma entidade de serviço, use apenas uma conta de usuário do Azure AD.

 

## <a name="build-a-lifecycle-process"></a>Criar um processo de ciclo de vida

O gerenciamento do ciclo de vida de uma conta de serviço começa com o planejamento e termina com sua exclusão permanente. 

Este artigo abordou anteriormente a parte de planejamento e criação. Você também deve monitorar, examinar as permissões, determinar o uso contínuo de uma conta e, por fim, desprovisionar a conta.

### <a name="monitor-service-accounts"></a>Monitorar contas de serviço

Monitore proativamente suas contas de serviço para garantir que os padrões de uso da conta de serviço reflitam os padrões pretendidos e que a conta de serviço ainda seja usada ativamente.

**Colete e monitore entradas de conta de serviço usando um dos seguintes métodos:**

* Usando os logs de Sign-In do Azure AD no portal do Azure AD.

* Exportar os logs de Sign-In do Azure AD para o [armazenamento do Azure](../../storage/index.yml), [hubs de eventos do Azure](../../event-hubs/index.yml)ou [Azure monitor](../../azure-monitor/logs/data-platform-logs.md).


![Captura de tela mostrando a tela de entrada da entidade de serviço.](./media/securing-service-accounts/service-accounts-govern-azure-1.png)

**A inteligência que você deve procurar nos logs de Sign-In inclui:**

* Há contas de serviço que não entram mais no locatário?

* Os padrões de entrada das contas de serviço estão mudando?

Recomendamos que você exporte logs de entrada do Azure AD e importe-os para suas ferramentas existentes de SIEM (gerenciamento de eventos e informações de segurança), como o Azure Sentinel. Use seu SIEM para criar alertas e painéis.

### <a name="review-service-account-permissions"></a>Examinar as permissões da conta de serviço

Examine regularmente as permissões concedidas e os escopos acessados pelas contas de serviço para ver se eles podem ser reduzidos.

* Use o [PowerShell](/powershell/module/azuread/get-azureadserviceprincipaloauth2permissiongrant) para [criar automação para verificar e documentar](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) escopos para os quais o consentimento é concedido a uma conta de serviço.

* Use o PowerShell para [examinar as credenciais de entidades de serviço existentes](https://github.com/AzureAD/AzureADAssessment) e verificar sua validade.

* Não defina as credenciais da entidade de serviço como "nunca expirar".

* Use certificados ou credenciais armazenadas no cofre de chaves do Azure sempre que possível.

O exemplo gratuito do PowerShell da Microsoft coleta as concessões OAuth2 e as informações de credencial da entidade de serviço, registra-as em um arquivo de valores separados por vírgulas (CSV) e um painel de exemplo Power BI para interpretar e usar os dados. para obter mais informações, consulte [AzureAD/AzureADAssessment: ferramentas para avaliar uma configuração e estado do locatário do Azure AD (github.com)](https://github.com/AzureAD/AzureADAssessment)

### <a name="recertify-service-account-use"></a>Recertificar o uso da conta de serviço

Estabeleça um processo de revisão para garantir que as contas de serviço sejam examinadas regularmente por seus proprietários e pela equipe de segurança ou ti em intervalos regulares. 

**O processo deve incluir:**

* Como determinar o ciclo de revisão de cada conta de serviço (deve ser documentado em seu CMDB).

* As comunicações para o proprietário e a segurança ou as equipes de ti antes do início das revisões.

* O tempo e o conteúdo de comunicações de aviso se a análise for perdida.

* Instruções sobre o que fazer se os proprietários não conseguirem revisar ou responder. Por exemplo, talvez você queira desabilitar (mas não excluir) a conta até que a revisão seja concluída.

* Instruções sobre como determinar as dependências upstream e downstream e notificar outros proprietários de recursos de qualquer efeito.

**A análise deve incluir o proprietário e seu parceiro de ti certificando que:**

* A conta ainda é necessária.

* As permissões concedidas à conta são adequadas e necessárias, ou uma alteração é solicitada.

* O acesso à conta e suas credenciais são controlados.

* As credenciais que a conta usa são apropriadas, em relação ao risco com que a conta foi avaliada (o tipo de credencial e o tempo de vida da credencial)

* A pontuação de risco da conta não foi alterada desde a última rescertificação

* Uma atualização no tempo de vida esperado da conta e a próxima data de rescertificação.

### <a name="deprovision-service-accounts"></a>Desprovisionar contas de serviço

* * Desprovisionar contas de serviço nas seguintes circunstâncias: * * * *

* O script ou aplicativo para o qual a conta de serviço foi criada é desativado.

* A função no script ou aplicativo para o qual a conta de serviço é usada (por exemplo, acesso a um recurso específico) é desativada.


* A conta de serviço é substituída por uma conta de serviço diferente.

* As credenciais expiraram ou a conta não é funcional e não há reclamações.

**Os processos de desprovisionamento devem incluir as tarefas a seguir.**

1. Depois que o aplicativo ou script associado for desprovisionado, [monitore as entradas](../reports-monitoring/concept-sign-ins.md#sign-ins-report) e o acesso aos recursos pela conta de serviço.

   * Se a conta ainda estiver ativa, determine como ela está sendo usada antes de tomar as etapas subsequentes.
 
2. Se essa for uma identidade de serviço gerenciada, desabilite a conta de serviço de entrar, mas não a remova do diretório.

3. Revogar atribuições de função e concessões de consentimento OAuth2 para a conta de serviço.

4. Após um período definido e um amplo aviso aos proprietários, exclua a conta de serviço do diretório.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre como proteger contas de serviço do Azure, consulte:

[Introdução às contas de serviço do Azure](service-accounts-introduction-azure.md)

[Proteção de identidades gerenciadas](service-accounts-managed-identities.md)

[Protegendo os princípios de serviço](service-accounts-principal.md)




 

