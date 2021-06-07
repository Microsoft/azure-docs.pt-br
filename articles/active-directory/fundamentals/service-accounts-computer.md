---
title: Protegendo contas de computador | Azure Active Directory
description: Um guia para proteger contas de computador locais.
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
ms.openlocfilehash: 22faba20cb12ae755f19fe43c295d98f9b364cbe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100416920"
---
# <a name="securing-computer-accounts"></a>Como proteger contas de computador

A conta de computador, ou a conta LocalSystem, é uma conta interna e altamente privilegiada com acesso a praticamente todos os recursos no computador local. Essa conta não está associada a nenhuma conta de usuário conectada. Os serviços em execução como LocalSystem acessam recursos de rede apresentando as credenciais do computador a servidores remotos. Ele apresenta credenciais no formato <domain_name>\<computer_name> $. O nome predefinido de uma conta de computador é NT NT\Sistema. Ele pode ser usado para iniciar um serviço e fornecer contexto de segurança para esse serviço.

![[Imagem 4] (.\media\securing-service-accounts\secure-computer-accounts-image-1.png)](.\media\securing-service-accounts\secure-computer-accounts-image-1.png)

## <a name="benefits-of-using-the-computer-account"></a>Benefícios do uso da conta de computador

A conta de computador fornece os seguintes benefícios.

* **Acesso local irrestrito**: a conta de computador fornece acesso completo aos recursos locais da máquina.

* **Gerenciamento automático de senhas**: a conta de computador elimina a necessidade de alterar manualmente as senhas. Em vez disso, essa conta é membro de Active Directory e a senha da conta é alterada automaticamente. Ele também elimina a necessidade de registrar o nome da entidade de serviço para o serviço.

* **Direitos de acesso limitados fora do computador**: a ACL (lista de controle de acesso) padrão no Active Directory Domain Services permite o acesso mínimo para contas de computador. Se esse serviço fosse invadido, ele só teria acesso limitado aos recursos em sua rede.

## <a name="assess-security-posture-of-computer-accounts"></a>Avaliar a postura de segurança das contas de computador

Possíveis desafios e atenuações associadas ao usar contas de computador. 

| Problemas| Atenuações |
| - | - |
| As contas de computador estão sujeitas à exclusão e recreação quando o computador sai e reingressa no domínio.| Valide a necessidade de adicionar um computador a um grupo do AD e verificar qual conta de computador foi adicionada a um grupo usando os scripts de exemplo fornecidos nesta página.| 
| Se você adicionar uma conta de computador a um grupo, todos os serviços em execução como LocalSystem nesse computador receberão direitos de acesso do grupo.| Ser seletivo das associações de grupo da sua conta de computador. Evite fazer contas de computador membros de qualquer grupo de administradores de domínio, pois o serviço associado tem acesso completo ao Active Directory Domain Services. |
| Padrões de rede inadequados para LocalSystem| Não presuma que a conta de computador tem o acesso limitado padrão aos recursos de rede. Em vez disso, marque cuidadosamente as associações de grupo para essa conta. |
| Serviços desconhecidos em execução como LocalSystem| Verifique se todos os serviços em execução na conta LocalSystem são serviços da Microsoft ou serviços confiáveis de terceiros. |


## <a name="find-services-running-under-the-computer-account"></a>Localizar serviços em execução na conta do computador

Use o seguinte cmdlet do PowerShell para encontrar serviços em execução no contexto LocalSystem

```powershell

Get-WmiObject win32_service | select Name, StartName | Where-Object {($_.StartName -eq "LocalSystem")}
```

**Localizar contas de computadores que sejam membros de um grupo específico**

Use o seguinte cmdlet do PowerShell para localizar contas de computador que são membros de um grupo específico.

```powershell

```Get-ADComputer -Filter {Name -Like "*"} -Properties MemberOf | Where-Object {[STRING]$_.MemberOf -like "Your_Group_Name_here*"} | Select Name, MemberOf
```

**Localizar contas de computadores que são membros de grupos com privilégios**

Use o seguinte cmdlet do PowerShell para localizar contas de computador que são membros de grupos de administradores de identidade (administradores de domínio, administradores de empresa, administradores)

```powershell
Get-ADGroupMember -Identity Administrators -Recursive | Where objectClass -eq "computer"
```
## <a name="move-from-computer-accounts"></a>Mover de contas de computador

> [!IMPORTANT]
> As contas de computador são contas altamente privilegiadas e devem ser usadas somente quando o serviço precisar de acesso irrestrito aos recursos locais no computador e você não puder usar uma MSA (conta de serviço gerenciado).

* Verifique com o proprietário do serviço se o serviço pode ser executado usando uma MSA e use uma conta de serviço gerenciado de grupo (gMSA) ou uma conta de serviço gerenciado autônoma (sMSA) se o serviço oferecer suporte a ela.

* Use uma conta de usuário de domínio apenas com os privilégios necessários para executar o serviço.

## <a name="next-steps"></a>Próximas etapas 

Consulte os artigos a seguir sobre como proteger contas de serviço

* [Introdução a contas de serviço locais](service-accounts-on-premises.md)

* [Contas de serviço gerenciado de grupo seguro](service-accounts-group-managed.md)

* [Proteger contas de serviço gerenciadas autônomas](service-accounts-standalone-managed.md)

* [Proteger contas de computador](service-accounts-computer.md)

* [Proteger contas de usuário](service-accounts-user-on-premises.md)

* [Administrar contas de serviço locais](service-accounts-govern-on-premises.md)

 

 
