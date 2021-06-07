---
title: Introdução às contas de serviço Active Directory | Azure Active Directory
description: Uma introdução aos tipos de contas de serviço no Active Directory e como protegê-los.
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
ms.openlocfilehash: a9024bc9fbd460f403db2da8a65af1e9bd2e771b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101645609"
---
# <a name="introduction-to-active-directory-service-accounts"></a>Introdução às contas de serviço Active Directory

Um serviço tem uma identidade de segurança primária que determina os direitos de acesso para recursos locais e de rede. O contexto de segurança para um serviço Microsoft Win32 é determinado pela conta de serviço usada para iniciar o serviço. Uma conta de serviço é usada para:
* identificar e autenticar um serviço
* um serviço foi iniciado com êxito
* acessar ou executar código ou um aplicativo
* iniciar um processo. 

## <a name="types-of-on-premises-service-accounts"></a>Tipos de contas de serviço locais

Com base no seu caso de uso, você pode usar uma MSA (conta de serviço gerenciado), uma conta de computador ou uma conta de usuário para executar um serviço. Os serviços devem ser testados para confirmar que podem usar uma conta de serviço gerenciado. Se puder, você deverá usar uma.

### <a name="group-msa-accounts"></a>Contas do grupo MSA

Use [contas de serviço gerenciado de grupo](service-accounts-group-managed.md) (gMSAs) sempre que possível para os serviços em execução no seu ambiente local. o gMSAs fornece uma solução de identidade única para um serviço em execução em um farm de servidores ou por trás de um balanceador de carga de rede. Eles também podem ser usados para um serviço em execução em um único servidor. [gMSAs têm requisitos específicos que devem ser atendidos](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)

### <a name="standalone-msa-accounts"></a>Contas autônomas do MSA

Se você não puder usar um gMSA, use uma sMSA ( [contas de serviço gerenciado) autônomas](service-accounts-standalone-managed.md). sMSAs exigem pelo menos o Windows Server 2008R2. Ao contrário de gMSAs, sMSAs é executado apenas em um servidor. Eles podem ser usados para vários serviços nesse servidor.

### <a name="computer-account"></a>Conta de Computador

Se você não puder usar uma MSA, investigue usando [contas de computador](service-accounts-computer.md). A conta LocalSystem é uma conta local predefinida que tem privilégios extensos no computador local e atua como a identidade do computador na rede.   
Os serviços executados como uma conta LocalSystem acessam recursos de rede usando as credenciais da conta de computador no formato <domain_name>\<computer_name> .

NT AUTHORITY\SYSTEM é o nome predefinido para a conta LocalSystem. Ele pode ser usado para iniciar um serviço e fornecer o contexto de segurança para esse serviço.

> [!NOTE]
> Quando uma conta de computador é usada, você não pode saber qual serviço no computador está usando essa conta e, portanto, não pode auditar qual serviço está fazendo alterações. 

### <a name="user-account"></a>Conta de usuário

Se você não puder usar uma MSA, investigue usando [contas de usuário](service-accounts-user-on-premises.md). As contas de usuário podem ser uma conta de usuário de domínio ou uma conta de usuário local.

Uma conta de usuário de domínio permite que o serviço Aproveite ao máximo os recursos de segurança do serviço do Windows e do Microsoft Active Directory Domain Services. O serviço terá o acesso local e de rede concedido à conta. Ele também terá as permissões de qualquer grupo do qual a conta seja membro. As contas de serviço de domínio dão suporte à autenticação mútua Kerberos.

Uma conta de usuário local (formato de nome: ".\UserName") existe apenas no banco de dados SAM do computador host; Ele não tem um objeto de usuário em Active Directory Domain Services. Uma conta local não pode ser autenticada pelo domínio. Portanto, um serviço executado no contexto de segurança de uma conta de usuário local não tem acesso aos recursos de rede (exceto como um usuário anônimo). Os serviços em execução no contexto de usuário local não dão suporte à autenticação mútua Kerberos na qual o serviço é autenticado por seus clientes. Por esses motivos, as contas de usuário local normalmente são inadequadas para serviços habilitados para diretório.

> [!IMPORTANT]
> As contas de serviço não devem ser membros de grupos privilegiados, como permissões de confirmações de associação de grupo privilegiadas que podem ser um risco de segurança. Cada serviço deve ter sua própria conta de serviço para fins de auditoria e segurança.

## <a name="choose-the-right-type-of-service-account"></a>Escolher o tipo certo de conta de serviço


| Critérios| gMSA| sMSA| Conta de Computador| Conta de usuário |
| - | - | - | - | - |
| O aplicativo é executado em um único servidor| Sim| Sim. Usar um gMSA se possível| Sim. Usar uma MSA, se possível| Sim. Use o MSA, se possível. |
| O aplicativo é executado em vários servidores| Sim| Não| Não. A conta está vinculada ao servidor| Sim. Use o MSA, se possível. |
| O aplicativo é executado atrás de balanceadores de carga| Sim| Não| Não| Sim. Use somente se você não puder usar um gMSA |
| O aplicativo é executado no Windows Server 2008 R2| Não| Sim| Sim. Use o MSA, se possível.| Sim. Use o MSA, se possível. |
| É executado no Windows Server 2012| Sim| Sim. Usar gMSA se possível| Sim. Usar MSA se possível| Sim. Use o MSA, se possível. |
| Requisito para restringir a conta de serviço a um único servidor| Não| Sim| Sim. Usar sMSA se possível| Não. |


 

### <a name="use-server-logs-and-powershell-to-investigate"></a>Usar logs de servidor e o PowerShell para investigar

Você pode usar os logs do servidor para determinar quais servidores e quantos servidores estão sendo executados em um aplicativo.

Você pode executar o seguinte comando do PowerShell para obter uma lista da versão do Windows Server para todos os servidores em sua rede. 

```PowerShell

Get-ADComputer -Filter 'operatingsystem -like "*server*" -and enabled -eq "true"' `

-Properties Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

sort-Object -Property Operatingsystem |

Select-Object -Property Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

Out-GridView

```

## <a name="find-on-premises-service-accounts"></a>Localizar contas de serviço local

Recomendamos que você adicione um prefixo, como "svc". Para todas as contas usadas como contas de serviço. Essa convenção de nomenclatura facilitará a localização e o gerenciamento. Considere também o uso de um atributo de descrição para a conta de serviço e o proprietário da conta de serviço, que pode ser um alias de equipe ou proprietário da equipe de segurança.

A localização de contas de serviço locais é fundamental para garantir sua segurança. E pode ser difícil para contas não MSA. É recomendável revisar todas as contas que têm acesso aos seus recursos locais importantes e determinar quais contas de computador ou de usuário podem estar agindo como contas de serviço. Você também pode usar os métodos a seguir para localizar contas.

* Os artigos para cada tipo de conta têm etapas detalhadas para localizar esse tipo de conta. Para obter links para esses artigos, consulte a seção próximas etapas deste artigo.

## <a name="document-service-accounts"></a>Contas de serviço de documento

Depois de encontrar as contas de serviço no seu ambiente local, documente as informações a seguir sobre cada conta. 

* O proprietário. A pessoa responsável por manter a conta.

* O propósito. O aplicativo que a conta representa, ou outra finalidade. 

* Escopos de permissão. Quais permissões ele tem e se deve ter? E se algum grupo for membro de?

* Perfil de risco. Qual é o risco para sua empresa se essa conta for comprometida? Se tiver alto risco, use uma MSA.

* Tempo de vida previsto e atestado periódico. Quanto tempo você prevê que essa conta está sendo dinâmica? Com que frequência o proprietário deve analisar e atestar para uma necessidade contínua?

* Segurança de senha. Para contas de computador local e de usuário, onde a senha é armazenada. Certifique-se de que as senhas sejam mantidas seguras e documente quem tem acesso. Considere o uso de [Privileged Identity Management](../privileged-identity-management/pim-configure.md) para proteger senhas armazenadas. 

  

## <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir sobre como proteger contas de serviço

* [Introdução a contas de serviço locais](service-accounts-on-premises.md)

* [Contas de serviço gerenciado de grupo seguro](service-accounts-group-managed.md)

* [Proteger contas de serviço gerenciadas autônomas](service-accounts-standalone-managed.md)

* [Proteger contas de computador](service-accounts-computer.md)

* [Proteger contas de usuário](service-accounts-user-on-premises.md)

* [Administrar contas de serviço locais](service-accounts-govern-on-premises.md)

