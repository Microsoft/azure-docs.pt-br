---
title: Protegendo contas de serviço gerenciados autônomos | Azure Active Directory
description: Um guia para proteger contas de serviço gerenciados autônomos.
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
ms.openlocfilehash: e2b3079407774c3d36fe5515b39e964018f9087e
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102548845"
---
# <a name="securing-standalone-managed-service-accounts"></a>Protegendo contas de serviço gerenciados autônomos

As contas de serviço gerenciado autônomo (sMSAs) são contas de domínio gerenciadas usadas para proteger um ou mais serviços em execução em um servidor. Eles não podem ser reutilizados em vários servidores. o sMSAs fornece gerenciamento automático de senhas, gerenciamento de SPN (nome da entidade de serviço) simplificado e a capacidade de delegar o gerenciamento a outros administradores. 

No Active Directory, sMSAs estão vinculados a um servidor específico que executa um serviço. Você pode encontrar essas contas listadas no snap-in Active Directory usuários e computadores do console de gerenciamento Microsoft.

![Uma captura de tela do snap-in Active Directory usuários e computadores mostrando a UO contas de serviço gerenciado.](./media/securing-service-accounts/secure-standalone-msa-image-1.png)

As contas de serviço gerenciado foram introduzidas com o Windows Server 2008R2 Active Directory esquema e exigem um nível mínimo de sistema operacional de 2008R2 do Windows Server. 

## <a name="benefits-of-using-smsas"></a>Benefícios do uso do sMSAs

o sMSAs oferece maior segurança do que as contas de usuário usadas como contas de serviço, ao mesmo tempo em que reduz a sobrecarga administrativa:

* Configurando senhas fortes. sMSAs usam senhas complexas geradas aleatoriamente de 240 bytes. A complexidade e o comprimento das senhas de sMSA minimizam a probabilidade de um serviço ser comprometido por ataques de força bruta ou de dicionário.

* Faça o ciclo de senhas regularmente. O Windows altera automaticamente a senha sMSA a cada 30 dias. Os administradores de serviço e de domínio não precisam agendar alterações de senha nem gerenciar o tempo de inatividade associado.

* Simplificando o gerenciamento de SPN. Os nomes da entidade de serviço são atualizados automaticamente se o nível funcional do domínio (DFL) for o Windows Server 2008 R2. Por exemplo, o nome da entidade de serviço é atualizado automaticamente nos seguintes cenários:

   * A conta de computador host foi renomeada. 

   * O nome DNS do computador host é alterado.

   * Ao adicionar ou remover um Sam-AccountName ou parâmetros DNS-hostname adicionais usando o [PowerShell](/powershell/module/addsadministration/set-adserviceaccount)

## <a name="when-to-use-smsas"></a>Quando usar o sMSAs

o sMSAs pode simplificar as tarefas de gerenciamento e segurança. Use sMSAs quando você tiver um ou mais serviços implantados em um único servidor e não puder usar um gMSA. 

> [!NOTE] 
> Embora você possa usar o sMSAs para mais de um serviço, é recomendável que cada serviço tenha sua própria identidade para fins de auditoria. 

Se o criador do software não puder informá-lo se ele pode usar um MSA, você deve testar seu aplicativo. Para fazer isso, crie um ambiente de teste e verifique se ele pode acessar todos os recursos necessários. Consulte [criar e instalar um sMSA](/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) para obter instruções passo a passo.

### <a name="assess-security-posture-of-smsas"></a>Avaliar a postura de segurança do sMSAs

sMSAs são inerentemente mais seguras do que as contas de usuário padrão, que exigem o gerenciamento de senhas em andamento. No entanto, é importante considerar o escopo de acesso do sMSAs como parte de sua postura de segurança geral.

A tabela a seguir mostra como reduzir possíveis problemas de segurança apresentados pelo sMSAs.

| Problemas de segurança| Atenuações |
| - | - |
| sMSA é um membro de grupos com privilégios|Remova o sMSA de grupos com privilégios elevados (como administradores de domínio). <br> Use o modelo com privilégios mínimos e conceda ao sMSA somente os direitos e as permissões que ele requer para executar seus serviços. <br> Se você não tiver certeza das permissões necessárias, consulte o criador de serviços. |
| sMSA tem acesso de leitura/gravação a recursos confidenciais.|Auditar o acesso a recursos confidenciais. Arquive logs de auditoria para um SIEM (Azure Log Analytics ou Azure Sentinel) para análise. <br> Corrigir permissões de recurso se um nível de acesso indesejável for detectado. |
| Por padrão, a frequência de substituição de senha sMSA é de 30 dias| A política de grupo pode ser usada para ajustar a duração dependendo dos requisitos de segurança da empresa. <br> * Você pode definir a duração da expiração da senha usando o caminho a seguir. <br>Computador \ \ \ \ \ \ \ \ \ \ \ \ Options\Domain membro: duração máxima da senha |



### <a name="challenges-with-smsas"></a>Desafios com o sMSAs

Os desafios associados ao sMSAs são os seguintes:

| Desafios| Atenuações |
| - | - |
| Eles podem ser usados em um único servidor.| Use gMSAs se você precisar usar a conta entre servidores. |
| Eles não podem ser usados em domínios.| Use gMSAs se você precisar usar a conta entre domínios. |
| Nem todos os aplicativos dão suporte a sMSAs.| Use gMSAs se possível. Se não usar uma conta de usuário padrão ou uma conta de computador conforme recomendado pelo criador de aplicativos. |


## <a name="find-smsas"></a>Localizar sMSAs

Em qualquer controlador de domínio, execute DSA. msc e expanda o contêiner contas de serviço gerenciado para exibir todos os sMSAs. 

O comando do PowerShell a seguir retorna todos os sMSAs e gMSAs no domínio Active Directory. 

`Get-ADServiceAccount -Filter *`

O comando a seguir retorna apenas sMSAs no domínio Active Directory.

`Get-ADServiceAccount -Filter * | where { $_.objectClass -eq "msDS-ManagedServiceAccount" }`

## <a name="manage-smsas"></a>Gerenciar sMSAs

Você pode usar os seguintes cmdlets Active Directory PowerShell para gerenciar sMSAs:

`Get-ADServiceAccount`

` Install-ADServiceAccount`

` New-ADServiceAccount`

` Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Ininstall-ADServiceAccount`

## <a name="move-to-smsas"></a>Mover para sMSAs

Se um serviço de aplicativo oferecer suporte a sMSA, mas não gMSAs, e estiver usando uma conta de usuário ou conta de computador no momento para o contexto de segurança, [crie e instale um sMSA](/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) no servidor. 

Idealmente, mova os recursos para o Azure e use as identidades gerenciadas do Azure ou entidades de serviço.

 

## <a name="next-steps"></a>Próximas etapas
Consulte os artigos a seguir sobre como proteger contas de serviço

* [Introdução a contas de serviço locais](service-accounts-on-premises.md)

* [Contas de serviço gerenciado de grupo seguro](service-accounts-group-managed.md)

* [Proteger contas de serviço gerenciadas autônomas](service-accounts-standalone-managed.md)

* [Proteger contas de computador](service-accounts-computer.md)

* [Proteger contas de usuário](service-accounts-user-on-premises.md)

* [Administrar contas de serviço locais](service-accounts-govern-on-premises.md)

