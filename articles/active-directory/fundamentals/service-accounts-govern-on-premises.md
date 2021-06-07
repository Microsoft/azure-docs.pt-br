---
title: Administrando contas de serviço locais | Azure Active Directory
description: Um guia para criar e executar um processo de ciclo de vida da conta para contas de serviço
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10ea524620f810e0bf1dddc230716031bbc10e69
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642387"
---
# <a name="governing-on-premises-service-accounts"></a>Administrando contas de serviço locais

Há quatro tipos de contas de serviço locais no Windows Active Directory:

* [Contas de serviço gerenciado de grupo](service-accounts-group-managed.md) (gMSAs)

* [contas de serviço gerenciado autônomo](service-accounts-standalone-managed.md) (sMSAs)

* [Contas de computador](service-accounts-computer.md)

* [Contas de usuário funcionando como contas de serviço](service-accounts-user-on-premises.md)


É essencial controlar as contas de serviço em conjunto com: 

* Proteja contas de serviço com base em seus requisitos de caso de uso e finalidade.

* Gerencie o ciclo de vida de contas de serviço e suas credenciais.

* Avaliar as contas de serviço com base no risco que elas serão expostas e as permissões que elas contêm, 

* Certifique-se de que Active Directory e Azure Active Directory não têm contas de serviço obsoletas com permissões potencialmente de alcance.

## <a name="principles-for-creating-a-new-service-account"></a>Princípios para a criação de uma nova conta de serviço

Use os critérios a seguir ao criar uma nova conta de serviço.

| Princípios| Considerações | 
| - |- | 
| Mapeamento de conta de serviço| Vincule a conta de serviço a um único serviço, aplicativo ou script. |
| Propriedade| Verifique se há um proprietário que solicita e assume a responsabilidade pela conta. |
| Escopo| Defina o escopo claramente e antecipe a duração do uso para a conta de serviço. |
| Finalidade| Crie contas de serviço para uma única finalidade específica. |
| Privilege| Aplique o princípio de privilégio mínimo por: <br>Nunca atribuí-los a grupos internos como administradores.<br> Removendo privilégios do computador local quando apropriado.<br>Adequando o acesso e usando a delegação de Active Directory para acesso ao diretório.<br>Usando permissões de acesso granular.<br>Configurando expirações de conta e restrições baseadas em local em contas de serviço baseadas em usuário |
| Monitor e uso de auditoria| Monitore os dados de entrada e verifique se ele corresponde ao uso pretendido. Defina alertas para uso anormal. |

### <a name="enforce-least-privilege-for-user-accounts-and-limit-account-overuse"></a>Impor o privilégio mínimo para contas de usuário e limitar o uso excessivo da conta

Use as seguintes configurações com contas de usuário usadas como contas de serviço:

* [**Expiração da conta**](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps&preserve-view=true): defina a conta de serviço para expirar automaticamente um determinado tempo após o período de revisão, a menos que seja determinado que ele deve continuar

*  **LogonWorkstations**: restrinja as permissões para o local em que a conta de serviço pode entrar. Se ele for executado localmente em um computador e acessar apenas os recursos nesse computador, restrinja-o de fazer logon em qualquer outro lugar.

* [**Não é possível alterar a senha**](/powershell/module/addsadministration/set-aduser): impeça que a conta de serviço altere sua própria senha definindo o parâmetro como false.

 
## <a name="build-a-lifecycle-management-process"></a>Criar um processo de gerenciamento do ciclo de vida

Para manter a segurança de suas contas de serviço, você deve gerenciá-las desde o momento em que identificar a necessidade até que elas sejam encerradas. 

Use o processo a seguir para o gerenciamento do ciclo de vida de contas de serviço:

1. Coletar informações de uso para a conta
1. Integrar a conta de serviço e o aplicativo ao banco de dados de gerenciamento de configuração (CMDB)
1. Executar avaliação de risco ou revisão formal
1. Crie a conta de serviço e aplique restrições.
1. Agendar e executar revisões recorrentes. Ajuste as permissões e os escopos conforme necessário.
1. Desprovisione a conta quando apropriado.

### <a name="collect-usage-information-for-the-service-account"></a>Coletar informações de uso para a conta de serviço

Colete as informações comerciais relevantes para cada conta de serviço. A tabela abaixo mostra as informações mínimas a serem coletadas, mas você deve coletar tudo o que é necessário para fazer o caso comercial para a existência das contas.

| Dados| Detalhes |
| - | - |
| Proprietário| Usuário ou grupo que é que é possível para a conta de serviço |
| Finalidade| Finalidade da conta de serviço |
| Permissões (escopos)| Conjunto de permissões esperado |
| Links do banco de dados de gerenciamento de configuração (CMDB)| Conta de serviço de vínculo cruzado com script/aplicativo de destino e proprietário (s) |
| Risco| Pontuação de impacto nos riscos e nos negócios com base na avaliação de riscos de segurança |
| Tempo de vida| Tempo de vida máximo previsto para habilitar o agendamento da expiração ou da rescertificação da conta |


 

O ideal é fazer a solicitação para um autoatendimento de conta e exigir as informações relevantes. O proprietário, que pode ser um aplicativo ou proprietário de negócios, um membro de ti ou um proprietário de infraestrutura. Usar uma ferramenta como o Microsoft Forms para essa solicitação e informações associadas tornará mais fácil a portar para sua ferramenta de inventário do CMDB se a conta for aprovada.

### <a name="onboard-service-account-to-cmdb"></a>Conta de serviço integrado ao CMDB

Armazene as informações coletadas em um aplicativo do tipo CMDB. Além das informações de negócios, inclua todas as dependências a outras infraestruturas, aplicativos e processos.  Esse repositório central tornará mais fácil:

* Avalie o risco.

* Configure a conta de serviço com as restrições necessárias.

* Entenda as dependências funcionais e de segurança relevantes.

* Realize revisões regulares para segurança e necessidade contínua.

* Entre em contato com os proprietários para revisar, desativar e alterar a conta de serviço.

Considere uma conta de serviço que é usada para executar um site e tem privilégios para se conectar a um ou mais bancos de dados SQL. As informações armazenadas em seu CMDB para esta conta de serviço podem ser:

|Dados | Detalhes|
| - | - |
| Proprietário, Deputy| João, Anna Mayers |
| Finalidade| Execute a página da Web de RH e conecte-se aos bancos de dados HR. Pode representar o usuário final ao acessar bancos de dados. |
| Permissões, escopos| HR-webserver: fazer logon localmente, executar página da Web<br>HR-SQL1: fazer logon localmente, ler em todos os bancos de dados do HR *<br>HR-SQL2: fazer logon localmente, ler no banco de dados salário * |
| Centro de Custo| 883944 |
| Risco avaliado| Médio Impacto nos negócios: médio; informações particulares; Médio |
| Restrições de conta| Fazer logon em: somente servidores mencionados anteriormente; Não é possível alterar a senha; Política de MBI-Password; |
| Tempo de vida| irrestrito |
| Ciclo de revisão| Bi-anual (por proprietário, pela equipe de segurança, por privacidade) |

### <a name="perform-risk-assessment-or-formal-review-of-service-account-usage"></a>Executar avaliação de risco ou revisão formal do uso da conta de serviço

Considerando suas permissões e finalidade, avalie o risco que a conta pode representar para seu aplicativo ou serviço associado e para sua infraestrutura se ela for comprometida. Considere o risco direto e indireto. 

* Para que um adversário tenha acesso direto?

* Quais outras informações ou sistemas a conta de serviço pode acessar?

* A conta pode ser usada para conceder permissões adicionais?

* Como você saberá quando as permissões forem alteradas?

A avaliação de risco, uma vez realizada e documentada, pode ter impacto sobre:

* Restrições de conta

* Tempo de vida da conta

* Requisitos de revisão de conta (cadência e revisores)

### <a name="create-a-service-account-and-apply-account-restrictions"></a>Criar uma conta de serviço e aplicar restrições de conta

Crie uma conta de serviço somente depois que as informações relevantes estiverem documentadas em seu CMDB e você realizar uma avaliação de risco. As restrições de conta devem ser alinhadas à avaliação de risco. Considere as seguintes restrições quando for relevante para a avaliação.:

* [Expiração da conta](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps&preserve-view=true)

   * Para todas as contas de usuário usadas como contas de serviço, defina uma data de término realista e definitiva para uso. Defina isso usando o sinalizador "conta expira". Para obter mais detalhes, consulte[ set-ADAccountExpiration](/powershell/module/addsadministration/set-adaccountexpiration). 

* Fazer logon no ([LogonWorkstation](/powershell/module/addsadministration/set-aduser))

* Requisitos de [política de senha](../../active-directory-domain-services/password-policy.md)

* Criação em uma [localização de UO](/windows-server/identity/ad-ds/plan/delegating-administration-of-account-ous-and-resource-ous) que garante o gerenciamento somente para usuários com privilégios

* Configure e colete a auditoria [que detecta alterações](/windows/security/threat-protection/auditing/audit-directory-service-changes) na conta de serviço – e o [uso da conta de serviço](https://www.manageengine.com/products/active-directory-audit/how-to/audit-kerberos-authentication-events.html).

Quando estiver pronto para colocar em produção, conceda acesso à conta de serviço com segurança. 

### <a name="schedule-regular-reviews-of-service-accounts"></a>Agendar revisões regulares de contas de serviço

Configure revisões regulares de contas de serviço classificadas como média e alto risco. As revisões devem incluir: 

* Atestado de proprietário para a necessidade contínua da conta e a justificativa de privilégios e escopos.

* Examine por equipes de privacidade e segurança, incluindo a avaliação de conexões upstream e downstream.

* Dados de auditorias garantindo que ele está sendo usado apenas para fins pretendidos

### <a name="deprovision-service-accounts"></a>Desprovisionar contas de serviço

No processo de desprovisionamento, primeiro remova as permissões e o monitor e, em seguida, remova a conta, se apropriado.

Desprovisionar contas de serviço quando:

* O script ou aplicativo para o qual a conta de serviço foi criada é desativado.

* A função no script ou aplicativo, para a qual a conta de serviço é usada (por exemplo, o acesso a um recurso específico) é desativada.

* A conta de serviço foi substituída por uma conta de serviço diferente.

Depois de remover todas as permissões, use esse processo para remover a conta.

1. Depois que o aplicativo ou script associado for desprovisionado, monitore as entradas e o acesso a recursos para as contas de serviço associadas para ter certeza de que ele não será usado em outro processo. Se você tiver certeza de que ele não é mais necessário, vá para a próxima etapa.

2. Desabilite a conta de serviço de entrar e verifique se ela não é mais necessária. Criar uma política de negócios para as contas de tempo deve permanecer desabilitado.

3. Exclua a conta de serviço depois que a política permanecer desabilitada for atendida. 

   * Para o MSAs, você pode [desinstalá-lo](/powershell/module/activedirectory/uninstall-adserviceaccount?view=winserver2012-ps&preserve-view=true) usando o PowerShell ou excluí-lo manualmente do contêiner da conta de serviço gerenciado.

   * Para contas de computador ou usuário, você pode excluir manualmente a conta do no Active Directory.

## <a name="next-steps"></a>Próximas etapas
Consulte os artigos a seguir sobre como proteger contas de serviço

* [Introdução a contas de serviço locais](service-accounts-on-premises.md)

* [Contas de serviço gerenciado de grupo seguro](service-accounts-group-managed.md)

* [Proteger contas de serviço gerenciadas autônomas](service-accounts-standalone-managed.md)

* [Proteger contas de computador](service-accounts-computer.md)

* [Proteger contas de usuário](service-accounts-user-on-premises.md)

* [Administrar contas de serviço locais](service-accounts-govern-on-premises.md)