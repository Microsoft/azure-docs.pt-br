---
title: Pré-requisitos para Azure AD Connect sincronização de nuvem no Azure AD
description: Este artigo descreve os pré-requisitos e os requisitos de hardware necessários para a sincronização de nuvem.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b83c9b0ece933ad71810c50e89ae296aa218ec75
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613001"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-sync"></a>Pré-requisitos para sincronização Azure AD Connect Cloud
Este artigo fornece orientação sobre como escolher e usar o Azure Active Directory (Azure AD) Connect Cloud Sync como sua solução de identidade.

## <a name="cloud-provisioning-agent-requirements"></a>Requisitos do agente de provisionamento de nuvem
Você precisa do seguinte para usar Azure AD Connect sincronização de nuvem:

- Credenciais de administrador de domínio ou de administrador corporativo para criar o Azure AD Connect gMSA de sincronização de nuvem (conta de serviço gerenciado de grupo) para executar o serviço do Agent. 
- Uma conta de administrador de identidade híbrida para seu locatário do Azure AD que não é um usuário convidado.
- Um servidor local para o agente de provisionamento com Windows 2012 R2 ou posterior.  Esse servidor deve ser um servidor de camada 0 com base no [modelo de camada administrativa Active Directory](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).
- Configurações de firewall local.

## <a name="group-managed-service-accounts"></a>Group Managed Service Accounts
Uma conta de serviço gerenciado de grupo é uma conta de domínio gerenciado que fornece gerenciamento automático de senhas, gerenciamento de SPN (nome da entidade de serviço) simplificado, a capacidade de delegar o gerenciamento a outros administradores e também estende essa funcionalidade em vários servidores.  Azure AD Connect a sincronização de nuvem dá suporte e usa um gMSA para executar o agente.  Você será solicitado a fornecer credenciais administrativas durante a instalação, a fim de criar essa conta.  A conta será exibida como (domain\provAgentgMSA $).  Para obter mais informações sobre um gMSA, consulte [contas de serviço gerenciado de grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) 

### <a name="prerequisites-for-gmsa"></a>Pré-requisitos para gMSA:
1.  O esquema de Active Directory na floresta do domínio gMSA precisa ser atualizado para o Windows Server 2012
2.  [Módulos do RSAT do PowerShell](/windows-server/remote/remote-server-administration-tools) em um controlador de domínio
3.  Pelo menos um controlador de domínio no domínio deve estar executando o Windows Server 2012.
4.  Um servidor ingressado no domínio em que o agente está sendo instalado precisa ser o Windows Server 2012 ou posterior.

### <a name="custom-gmsa-account"></a>Conta personalizada do gMSA
Se você estiver criando uma conta personalizada do gMSA, será necessário garantir que a conta tenha as seguintes permissões.

|Type |Nome |Acesso |Aplica-se A| 
|-----|-----|-----|-----|
|Allow |Conta do gMSA |Leia todas as propriedades |Objetos de dispositivo descendente| 
|Allow |Conta do gMSA|Leia todas as propriedades |Objetos descendentes de InetOrgPerson| 
|Allow |Conta do gMSA |Leia todas as propriedades |Objetos de computador descendentes| 
|Allow |Conta do gMSA |Leia todas as propriedades |Objetos descendentes de foreignSecurityPrincipal| 
|Allow |Conta do gMSA |Controle total |Objetos de grupo descendentes| 
|Allow |Conta do gMSA |Leia todas as propriedades |Objetos de usuário descendentes| 
|Allow |Conta do gMSA |Leia todas as propriedades |Objetos de contato descendentes| 
|Allow |Conta do gMSA |Criar/excluir objetos de usuário|Este objeto e todos os objetos descendentes| 

Para obter as etapas sobre como atualizar um agente existente para usar uma conta do gMSA, consulte [contas de serviço gerenciado de grupo](how-to-install.md#group-managed-service-accounts).

### <a name="in-the-azure-active-directory-admin-center"></a>No centro de administração do Azure Active Directory

1. Crie uma conta de administrador de identidade híbrida somente em nuvem no seu locatário do Azure AD. Dessa forma, você pode gerenciar a configuração do seu locatário se seus serviços locais falhem ou ficarem indisponíveis. Saiba mais sobre como [Adicionar uma conta de administrador de identidade híbrida somente em nuvem](../fundamentals/add-users-azure-active-directory.md). Finalizar essa etapa é essencial para garantir que você não seja bloqueado de seu locatário.
1. Adicione um ou mais [nomes de domínio personalizados](../fundamentals/add-custom-domain.md) ao seu locatário do Azure AD. Os usuários podem entrar com um desses nomes de domínio.

### <a name="in-your-directory-in-active-directory"></a>No seu diretório no Active Directory

Execute a [ferramenta IdFix](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) para preparar os atributos de diretório para sincronização.

### <a name="in-your-on-premises-environment"></a>Em seu ambiente local

1. Identificar um servidor de host ingressado no domínio que executa o Windows Server 2012 R2 ou superior com o mínimo de 4 GB de RAM e o runtime do .NET 4.7.1+.

2. A política de execução do PowerShell no servidor local deve ser definida como Undefined ou RemoteSigned.

3. Se houver um firewall entre os servidores e o Azure Active Directory, configure os seguintes itens:
    - Certifique-se de que os agentes possam fazer solicitações de *saída* para o Azure AD nas seguintes portas:

      | Número da porta | Como ele é usado |
      | --- | --- |
      | **80** | Baixa as listas de CRLs (certificados revogados) enquanto valida o certificado TLS/SSL.  |
      | **443** | Lida com toda a comunicação de saída com o serviço. |
      | **8080** (opcional) | Agentes relatarão seu status a cada 10 minutos através da porta 8080, se a porta 443 não estiver disponível. Esse status é exibido no portal do Azure Active Directory. |

    - Se o firewall impõe as regras de acordo com os usuários originadores, abra essas portas para o tráfego proveniente dos serviços Windows que são executados como um serviço de rede.
    - Se o firewall ou proxy permitir especificar sufixos seguros, adicione conexões a \*.msappproxy.net e \*.servicebus.windows.net. servicebus.windows.net. Caso contrário, permita o acesso aos [Intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653), os quais são atualizados semanalmente.
    - Seus agentes devem acessar login.windows.net e login.microsoftonline.com para o registro inicial. Abra seu firewall para essas URLs também.
    - Para validação do certificado, desbloqueie as seguintes URLs: mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 e www\.microsoft.com:80. Essas URLs são usadas para a validação de certificado com outros produtos da Microsoft, então você talvez já tenha essas URLs desbloqueadas.

    >[!NOTE]
    > Não há suporte para a instalação do agente de provisionamento de nuvem no Windows Server Core.

### <a name="additional-requirements"></a>Requisitos adicionais

- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>Requisitos de TLS

> [!NOTE]
> O protocolo TLS fornece comunicações seguras. A alteração das configurações de TLS afeta toda a floresta. Para obter mais informações, consulte [Atualizar para habilitar TLS 1.1 e TLS 1.2 como protocolos seguros padrão no WinHTTP no Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi).

O Windows Server que hospeda o agente de provisionamento de nuvem do Azure AD Connect deve ter o TLS 1.2 habilitado antes da instalação.

Para habilitar o TLS 1.2, siga estas etapas.

1. Defina as seguintes chaves do registro:

    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Reinicie o servidor.

## <a name="known-limitations"></a>Limitações conhecidas

Veja a seguir as limitações conhecidas:

### <a name="delta-synchronization"></a>Sincronização de delta

- A filtragem de escopo de grupo para sincronização Delta não dá suporte a mais de 1500 membros.
- Quando você exclui um grupo que é usado como parte de um filtro de escopo de grupo, os usuários que são membros do grupo não são excluídos. 
- Quando você renomeia a UO ou o grupo que está no escopo, a sincronização Delta não removerá os usuários.

### <a name="provisioning-logs"></a>Logs de provisionamento
- Os logs de provisionamento não diferenciam claramente as operações de criação e atualização.  Você pode ver uma operação de criação para uma atualização e uma operação de atualização para uma criação.

### <a name="group-re-naming-or-ou-re-naming"></a>Renomeação de grupo ou renomeação de UO
- Se você renomear um grupo ou uma UO no AD que está no escopo de uma determinada configuração, o trabalho de sincronização de nuvem não será capaz de reconhecer a alteração de nome no AD. O trabalho não entrará em quarentena e permanecerá íntegro.


## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é Azure AD Connect a sincronização de nuvem?](what-is-cloud-sync.md)