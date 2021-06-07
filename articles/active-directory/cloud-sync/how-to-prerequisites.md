---
title: Pré-requisitos para a Sincronização na nuvem do Azure AD Connect no Azure AD
description: Este artigo descreve os pré-requisitos e os requisitos de hardware para a sincronização na nuvem.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0277d4ce263610576178e3844a0665ab6506fbfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579154"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-sync"></a>Pré-requisitos para a sincronização de nuvem do Azure AD Connect
Este artigo oferece diretrizes sobre como escolher e usar a sincronização na nuvem do Azure AD (Azure Active Directory) Connect como solução de identidade.

## <a name="cloud-provisioning-agent-requirements"></a>Requisitos do agente de provisionamento de nuvem
Você precisa dos seguintes itens para usar a Sincronização na nuvem do Azure AD Connect:

- Credenciais de Administrador de domínio ou Administrador corporativo para criar a gMSA (conta de serviço gerenciado de grupo) da Sincronização na nuvem do Azure AD Connect para executar o serviço do agente. 
- Uma conta de administrador de identidade híbrida para o locatário do Azure AD que não é um usuário convidado.
- Um servidor local para o agente de provisionamento com o Windows 2016 ou posterior.  Esse servidor deve ser de camada 0 e baseado no [modelo de camada administrativa do Active Directory](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).
- Configurações de firewall local.

## <a name="group-managed-service-accounts"></a>Group Managed Service Accounts
Uma Conta de serviço gerenciado de grupo é uma conta de domínio gerenciado que fornece gerenciamento automático de senhas, gerenciamento de SPN (nome da entidade de serviço) simplificado e a capacidade de delegar o gerenciamento para outros administradores, além de estendes essa funcionalidade a vários servidores.  A Sincronização na nuvem do Azure AD Connect dá suporte e usa uma gMSA para executar o agente.  Você precisará fornecer credenciais administrativas durante a instalação a fim de criar essa conta.  A conta será exibida como (domain\provAgentgMSA$).  Para obter mais informações sobre uma gMSA, confira [Contas de serviço gerenciado de grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) 

### <a name="prerequisites-for-gmsa"></a>Pré-requisitos da gMSA:
1.  O esquema do Active Directory na floresta de domínio da gMSA precisa ser atualizado para o Windows Server 2016.
2.  [Módulos das Ferramentas de Administração de Servidor Remoto do PowerShell](/windows-server/remote/remote-server-administration-tools) em um controlador de domínio
3.  Pelo menos um controlador de domínio no domínio deve estar executando o Windows Server 2016.
4.  Um servidor conectado ao domínio em que o agente está sendo instalado precisa ser o Windows Server 2016 ou posterior.

### <a name="custom-gmsa-account"></a>Conta gMSA personalizada
Se estiver criando uma conta gMSA personalizada, você precisará garantir que ela tenha as permissões a seguir.

|Tipo |Nome |Acesso |Aplica-se A| 
|-----|-----|-----|-----|
|Allow |Conta gMSA |Leia todas as propriedades |Objetos de dispositivo descendente| 
|Allow |Conta gMSA|Leia todas as propriedades |Objetos descendentes de InetOrgPerson| 
|Allow |Conta gMSA |Leia todas as propriedades |Objetos de computador descendentes| 
|Allow |Conta gMSA |Leia todas as propriedades |Objetos descendentes de foreignSecurityPrincipal| 
|Allow |Conta gMSA |Controle total |Objetos de grupo descendentes| 
|Allow |Conta gMSA |Leia todas as propriedades |Objetos de usuário descendentes| 
|Allow |Conta gMSA |Leia todas as propriedades |Objetos de contato descendentes| 
|Allow |Conta gMSA |Criar/excluir objetos de usuário|Este objeto e todos os objetos descendentes| 

Para ver as etapas para atualizar um agente existente para usar uma conta de gMSA, confira [Contas de serviço gerenciado de grupo](how-to-install.md#group-managed-service-accounts).

### <a name="in-the-azure-active-directory-admin-center"></a>No centro de administração do Azure Active Directory

1. Crie uma conta de administrador de identidade híbrida somente em nuvem em seu locatário do Azure AD. Dessa forma, você pode gerenciar a configuração do seu locatário se seus serviços locais falhem ou ficarem indisponíveis. Saiba mais sobre como [adicionar uma conta de administrador de identidade híbrida somente em nuvem](../fundamentals/add-users-azure-active-directory.md). Finalizar essa etapa é essencial para garantir que você não seja bloqueado de seu locatário.
1. Adicione um ou mais [nomes de domínio personalizados](../fundamentals/add-custom-domain.md) ao seu locatário do Azure AD. Os usuários podem entrar com um desses nomes de domínio.

### <a name="in-your-directory-in-active-directory"></a>No seu diretório no Active Directory

Execute a [ferramenta IdFix](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) para preparar os atributos de diretório para sincronização.

### <a name="in-your-on-premises-environment"></a>Em seu ambiente local

1. Identifique um servidor de host conectado ao domínio que executa o Windows Server 2016 ou superior com o mínimo de 4 GB de RAM e o runtime do .NET 4.7.1+.

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

- A filtragem de escopo de grupo para sincronização delta não dá suporte a mais de 1500 membros.
- Quando você exclui um grupo que é usado como parte de um filtro de escopo de grupo, os usuários membros do grupo não são excluídos. 
- Quando você renomeia a UO ou o grupo que está no escopo, a sincronização delta não remove os usuários.

### <a name="provisioning-logs"></a>Logs de provisionamento
- Os logs de provisionamento não diferenciam claramente as operações de criação e de atualização.  Você pode ver uma operação de criação para uma atualização e uma operação de atualização para uma criação.

### <a name="group-re-naming-or-ou-re-naming"></a>Renomeação de grupo ou de UO
- Se você renomear um grupo ou uma UO no AD que está no escopo de uma configuração, o trabalho de sincronização em nuvem não será capaz de reconhecer a alteração de nome no AD. O trabalho não entrará em quarentena e permanecerá íntegro.


## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é a sincronização de nuvem do Azure AD Connect?](what-is-cloud-sync.md)