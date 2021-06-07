---
title: Protegendo contas de serviço baseadas em usuário | Azure Active Directory
description: Um guia para proteger contas de usuário locais.
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
ms.openlocfilehash: e484bdda33142024f2067649eaa67042fe7776f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100416911"
---
# <a name="securing-user-based-service-accounts-in-active-directory"></a>Protegendo contas de serviço baseadas em usuário no Active Directory

As contas de usuário locais são a abordagem tradicional para proteger os serviços em execução no Windows. Use essas contas como um último recurso quando gMSAs (contas de serviço gerenciado global) e sMSAs (contas de serviço gerenciado) autônomas não são suportadas pelo seu serviço. Consulte Visão geral das contas de serviço locais para obter informações sobre como selecionar o melhor tipo de conta a ser usada. Além disso, investigue se você pode mover seu serviço para usar uma conta de serviço do Azure como uma identidade gerenciada ou uma entidade de serviço. 

As contas de usuário locais podem ser criadas para fornecer um contexto de segurança para serviços e conceder privilégios necessários para que os serviços acessem recursos locais e de rede. Eles exigem gerenciamento de senha manual, assim como qualquer outra conta de usuário do Active Directory (AD). Os administradores de serviço e domínio são necessários para observar processos de gerenciamento de senhas fortes para manter essas contas seguras.

Ao usar uma conta de usuário como uma conta de serviço, use-a somente para um único serviço. Nomeie-o de forma a tornar claro que é uma conta de serviço e para qual serviço. 

## <a name="benefits-and-challenges"></a>Benefícios e desafios

Benefícios

As contas de usuário locais são o tipo de conta mais versátil para uso com serviços. As contas de usuário usadas como contas de serviço podem ser controladas por todas as políticas que regem as contas de usuário normais. Dito isso, use-os somente se você não puder usar um MSA. Além disso, avalie se uma conta de computador é uma opção melhor. 

Desafios com contas de usuário locais

Os desafios a seguir estão associados ao uso de contas de usuário locais.

| Desafios| Atenuações |
| - | - |
| O gerenciamento de senhas é um processo manual que pode levar à segurança mais fraca e ao tempo de inatividade do serviço.| Garanta que a complexidade e a alteração de senha sejam governadas por um processo robusto que garanta atualizações regulares com uma senha forte. <br> Coordenar a alteração de senha com uma atualização de senha no serviço, o que resultará em tempo de inatividade do serviço. |
| A identificação de contas de usuário locais que estão agindo como contas de serviço pode ser difícil.| Documentar e manter registros de contas de serviço implantados em seu ambiente. <br> Acompanhe o nome da conta e os recursos aos quais eles têm acesso atribuído. <br> Considere adicionar um prefixo de svc_ a todas as contas de usuário usadas como contas de serviço. |


## <a name="find-on-premises-user-accounts-used-as-service-accounts"></a>Localizar contas de usuário locais usadas como contas de serviço

As contas de usuário locais são assim como qualquer outra conta de usuário do AD. Consequentemente, pode ser difícil encontrar essas contas, pois não há um atributo único de uma conta de usuário que a identifique como uma conta de serviço. 

Recomendamos que você crie uma Convenção de nomenclatura facilmente identificável para qualquer conta de usuário usada como uma conta de serviço.

Por exemplo, adicione "Service-" como um prefixo e nomeie o serviço: "Service-HRDataConnector".

Você pode usar alguns dos indicadores abaixo para encontrar essas contas de serviço, no entanto, isso pode não encontrar todas essas contas.

* Contas confiáveis para delegação.

* Contas com nomes de entidade de serviço.

* Contas cuja senha está definida para nunca expirar.

Você pode executar os seguintes comandos do PowerShell para localizar as contas de usuário locais criadas para serviços.

### <a name="find-accounts-trusted-for-delegation"></a>Localizar contas confiáveis para delegação

```PowerShell

Get-ADObject -Filter {(msDS-AllowedToDelegateTo -like '*') -or (UserAccountControl -band 0x0080000) -or (UserAccountControl -band 0x1000000)} -prop samAccountName,msDS-AllowedToDelegateTo,servicePrincipalName,userAccountControl | select DistinguishedName,ObjectClass,samAccountName,servicePrincipalName, @{name='DelegationStatus';expression={if($_.UserAccountControl -band 0x80000){'AllServices'}else{'SpecificServices'}}}, @{name='AllowedProtocols';expression={if($_.UserAccountControl -band 0x1000000){'Any'}else{'Kerberos'}}}, @{name='DestinationServices';expression={$_.'msDS-AllowedToDelegateTo'}}

```

### <a name="find-accounts-with-service-principle-names"></a>Localizar contas com nomes de entidade de serviço

```PowerShell

Get-ADUser -Filter * -Properties servicePrincipalName | where {$_.servicePrincipalName -ne $null}

```

 

### <a name="find-accounts-with-passwords-set-to-never-expire"></a>Localizar contas com senhas definidas para nunca expirar

```PowerShell

Get-ADUser -Filter * -Properties PasswordNeverExpires | where {$_.PasswordNeverExpires -eq $true}

```


Você também pode auditar o acesso a recursos confidenciais e arquivar logs de auditoria em um sistema SIEM (gerenciamento de informações e eventos de segurança). Usando sistemas como o Azure Log Analytics ou o Azure Sentinel, você pode pesquisar e analisar contas e serviços.

## <a name="assess-security-of-on-premises-user-accounts"></a>Avaliar a segurança de contas de usuário locais

Avalie a segurança de suas contas de usuário locais sendo usadas como contas de serviço usando os seguintes critérios:

* O que é a política de gerenciamento de senha?

* A conta é membro de qualquer grupo com privilégios?

* A conta tem acesso de leitura/gravação a recursos importantes?

### <a name="mitigate-potential-security-issues"></a>Reduzir possíveis problemas de segurança

A tabela a seguir mostra possíveis problemas de segurança e atenuações correspondentes para contas de usuário locais.

| Problemas de segurança| Atenuações |
| - | - |
| Gerenciamento de senhas|* Garanta que a complexidade e a alteração da senha sejam governadas por um processo robusto que garanta atualizações regulares com requisitos de senha forte. <br> * Coordene a alteração de senha com uma atualização de senha para minimizar o tempo de inatividade do serviço. |
| A conta é membro de grupos com privilégios.| Examine as associações de grupo. Remova a conta de grupos privilegiados. Conceda à conta somente os direitos e as permissões que ele requer para executar seu serviço (consulte o fornecedor do serviço). Por exemplo, você pode negar a entrada localmente ou negar a entrada interativa. |
| A conta tem acesso de leitura/gravação a recursos confidenciais.| Auditar o acesso a recursos confidenciais. Arquive logs de auditoria para um SIEM (Azure Log Analytics ou Azure Sentinel) para análise. Corrigir permissões de recurso se um nível de acesso indesejável for detectado. |


## <a name="move-to-more-secure-account-types"></a>Mover para tipos de conta mais seguras

A Microsoft não recomenda que os clientes usem contas de usuário locais como contas de serviço. Para qualquer serviço que use esse tipo de conta, avalie se ele pode ser configurado para usar um gMSA ou um sMSA.

Além disso, avalie se o próprio serviço pode ser movido para o Azure para que tipos de conta de serviço mais seguros possam ser usados. 

## <a name="next-steps"></a>Próximas etapas
Consulte os artigos a seguir sobre como proteger contas de serviço

* [Introdução a contas de serviço locais](service-accounts-on-premises.md)

* [Contas de serviço gerenciado de grupo seguro](service-accounts-group-managed.md)

* [Proteger contas de serviço gerenciadas autônomas](service-accounts-standalone-managed.md)

* [Proteger contas de computador](service-accounts-computer.md)

* [Proteger contas de usuário](service-accounts-user-on-premises.md)

* [Administrar contas de serviço locais](service-accounts-govern-on-premises.md)

 
