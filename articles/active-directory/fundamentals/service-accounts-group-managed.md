---
title: Protegendo contas de serviço gerenciado de grupo | Azure Active Directory
description: Um guia para proteger contas de computador de contas de serviço gerenciado de grupo.
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
ms.openlocfilehash: f6f91adf499c62fef56a30c5f2945ba7a90a0b29
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640074"
---
# <a name="securing-group-managed-service-accounts"></a>Protegendo contas de serviço gerenciado de grupo

Contas de serviço gerenciado de grupo (gMSAs) são contas de domínio gerenciadas que são usadas para proteger serviços. o gMSAs pode ser executado em um único servidor ou em um farm de servidores, como sistemas atrás de um Load Balancer de rede (NLB) ou um servidor de Serviços de Informações da Internet (IIS). Depois de configurar seus serviços para usar uma entidade de segurança gMSA, o gerenciamento de senhas para essa conta é tratado pelo Windows.

## <a name="benefits-of-using-gmsas"></a>Benefícios do uso do gMSAs

a gMSAs oferece uma solução de identidade única com maior segurança e, ao mesmo tempo, reduz a sobrecarga administrativa:

* **Configurando senhas fortes**. gMSAs usam senhas complexas geradas aleatoriamente de 240 bytes. A complexidade e o comprimento das senhas de gMSA minimizam a probabilidade de um serviço ser comprometido por ataques de força bruta ou de dicionário.

* Faça o **ciclo de senhas regularmente**. gMSAs Shift password Management para Windows, que altera a senha a cada 30 dias. Os administradores de serviço e de domínio não precisam mais agendar alterações de senha ou gerenciar interrupções de serviço para manter as contas de serviço seguras. 

* **Suporte à implantação em farms de servidores**. A capacidade de implantar o gMSAs em vários servidores permite o suporte a soluções com balanceamento de carga em que vários hosts executam o mesmo serviço. 

* **Suporte ao gerenciamento de SPN (nome principal do servidor) simplificado**. Você pode configurar o SPN usando o PowerShell no momento da criação da conta. Além disso, os serviços que dão suporte a registros de SPN automáticos podem fazer isso em relação ao gMSA, desde que as permissões de gMSA sejam definidas corretamente. 

## <a name="when-to-use-gmsas"></a>Quando usar o gMSAs

Use gMSAs como o tipo de conta preferencial para serviços locais, a menos que um serviço, como clustering de failover, não ofereça suporte a ele.

> [!IMPORTANT]
> Você deve testar seu serviço com o gMSAs antes da implantação na produção. Para fazer isso, configure um ambiente de teste e verifique se o aplicativo pode usar o gMSA e acesse os recursos que ele precisa para acessar. Para obter mais informações, consulte [suporte para contas de serviço gerenciado de grupo](/system-center/scom/support-group-managed-service-accounts).


Se um serviço não oferecer suporte ao uso de gMSAs, sua próxima melhor opção é usar uma conta de serviço gerenciado autônomo (sMSA). o sMSAs fornece a mesma funcionalidade que um gMSA, mas destina-se à implantação somente em um único servidor.

Se você não pode usar um gMSA ou sMSA tem suporte do seu serviço, o serviço deve ser configurado para ser executado como uma conta de usuário padrão. Os administradores de serviço e domínio são necessários para observar processos de gerenciamento de senhas fortes para manter a conta segura.

## <a name="assess-the-security-posture-of-gmsas"></a>Avaliar a postura de segurança do gMSAs

gMSAs são inerentemente mais seguras do que as contas de usuário padrão, que exigem o gerenciamento de senhas em andamento. No entanto, é importante considerar o escopo de acesso de gMSAs à medida que você examina sua postura de segurança geral.

A tabela a seguir mostra possíveis problemas de segurança e atenuações para usar o gMSAs.

| Problemas de segurança| Atenuações |
| - | - |
| gMSA é um membro de grupos com privilégios. | Examine suas associações de grupo. Para fazer isso, você pode criar um script do PowerShell para enumerar todas as associações de grupo e, em seguida, filtrar um arquivo CSV resultante pelos nomes dos seus arquivos gMSA. <br>Remova o gMSA de grupos privilegiados.<br> Conceda ao gMSA somente os direitos e as permissões que ele requer para executar seu serviço (consulte o fornecedor do serviço). 
| gMSA tem acesso de leitura/gravação a recursos confidenciais. | Auditar o acesso a recursos confidenciais. Arquive logs de auditoria para um SIEM, por exemplo Log Analytics do Azure ou Azure Sentinel, para análise. Remova as permissões de recurso desnecessárias se um nível de acesso indesejável for detectado. |


## <a name="find-gmsas"></a>Localizar gMSAs

Sua organização já pode ter gMSAs criado. Execute o seguinte cmdlet do PowerShell para recuperar essas contas:

Para trabalhar com eficiência, o gMSAs deve estar na UO (unidade organizacional) contas de serviço gerenciada.

  
![Captura de tela da UO da conta de serviço gerenciado.](./media/securing-service-accounts/secure-gmsa-image-1.png)

Para localizar MSAs de serviço que talvez não estejam lá, consulte os comandos a seguir.

**Para localizar todas as contas de serviço, incluindo gMSAs e sMSAs:**


```powershell

Get-ADServiceAccount -Filter *

# This PowerShell cmdlet will return all Managed Service Accounts (both gMSAs and sMSAs). An administrator can differentiate between the two by examining the ObjectClass attribute on returned accounts.

# For gMSA accounts, ObjectClass = msDS-GroupManagedServiceAccount

# For sMSA accounts, ObjectClass = msDS-ManagedServiceAccount

# To filter results to only gMSAs:

Get-ADServiceAccount –Filter * | where $_.ObjectClass -eq "msDS-GroupManagedServiceAccount"}
```

## <a name="manage-gmsas"></a>Gerenciar gMSAs

Você pode usar os seguintes cmdlets Active Directory PowerShell para gerenciar gMSAs:

`Get-ADServiceAccount`

`Install-ADServiceAccount`

`New-ADServiceAccount`

`Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Uninstall-ADServiceAccount`

> [!NOTE]
> A partir do Windows Server 2012, os cmdlets *-ADServiceAccount funcionam com gMSAs por padrão. Para obter mais informações sobre o uso dos cmdlets acima, consulte [**introdução com contas de serviço gerenciado de grupo**](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts).

## <a name="move-to-a-gmsa"></a>Mover para um gMSA
gMSAs são o tipo de conta de serviço mais seguro para as necessidades locais. Se você puder mudar para um, deverá. Além disso, considere mover seus serviços para o Azure e suas contas de serviço para o Azure Active Directory.

1.  Verifique se a [chave raiz KDS está implantada na floresta](/windows-server/security/group-managed-service-accounts/create-the-key-distribution-services-kds-root-key). Essa operação é realizada uma única vez.

2. [Crie um novo gMSA](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts).

3. Instale o novo gMSA em cada host que executa o serviço.
   > [!NOTE] 
   > Para obter mais informações sobre a criação e a instalação do gMSA em um host, antes de configurar seu serviço para usar o gMSA, consulte [introdução com contas de serviço gerenciado de grupo](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11))

 
4. Altere a identidade do serviço para gMSA e especifique uma senha em branco.

5. Valide se o serviço está funcionando na nova identidade gMSA.

6. Exclua a identidade da conta de serviço antiga.

 

## <a name="next-steps"></a>Próximas etapas
Consulte os artigos a seguir sobre como proteger contas de serviço

* [Introdução a contas de serviço locais](service-accounts-on-premises.md)

* [Contas de serviço gerenciado de grupo seguro](service-accounts-group-managed.md)

* [Proteger contas de serviço gerenciadas autônomas](service-accounts-standalone-managed.md)

* [Proteger contas de computador](service-accounts-computer.md)

* [Proteger contas de usuário](service-accounts-user-on-premises.md)

* [Administrar contas de serviço locais](service-accounts-govern-on-premises.md)