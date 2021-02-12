---
title: 'Azure AD Connect: pré-requisitos e hardware | Microsoft Docs'
description: Este artigo descreve os pré-requisitos e os requisitos de hardware para Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/05/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73376994e01ed89891726a8f6e1b727f89dab2fb
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201715"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Pré-requisitos do Azure AD Connect
Este artigo descreve os pré-requisitos e os requisitos de hardware para o Azure Active Directory (Azure AD) Connect.

## <a name="before-you-install-azure-ad-connect"></a>Antes de instalar o Azure AD Connect
Antes de instalar o Azure AD Connect, aqui estão algumas coisas de que você precisará.

### <a name="azure-ad"></a>Azure AD
* Você precisa de um locatário do Azure AD. Você recebe uma [avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). Você pode usar um dos seguintes portais para gerenciar o Azure AD Connect:
  * O [portal do Azure](https://portal.azure.com).
  * O [portal do Office](https://portal.office.com).
* [Adicione e verifique o domínio](../fundamentals/add-custom-domain.md) que você planeja usar no AD do Azure. Por exemplo, se você planeja usar o contoso.com para seus usuários, verifique se esse domínio foi verificado e se você não está usando apenas o domínio padrão contoso.onmicrosoft.com.
* Um locatário do Azure AD permite, por padrão, 50.000 objetos. Quando você verifica seu domínio, o limite aumenta para 300.000 objetos. Se você precisar de mais objetos no Azure AD, abra um caso de suporte para aumentar ainda mais o limite. Se você precisar de mais de 500.000 objetos, precisará de uma licença, como Microsoft 365, Azure AD Premium ou Enterprise Mobility + Security.

### <a name="prepare-your-on-premises-data"></a>Preparar seus dados locais
* Use [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) para identificar erros como duplicatas e problemas de formatação em seu diretório antes de sincronizar para o Azure AD e Microsoft 365.
* Examine os [recursos de sincronização opcionais que você pode habilitar no Azure ad](how-to-connect-syncservice-features.md)e avalie quais recursos você deve habilitar.

### <a name="on-premises-active-directory"></a>Active Directory local
* A versão do esquema de Active Directory e o nível funcional da floresta devem ser Windows Server 2003 ou posterior. Os controladores de domínio podem executar qualquer versão, desde que a versão do esquema e os requisitos de nível de floresta sejam atendidos.
* Se você planeja usar o recurso *write-back de senha*, os controladores de domínio devem estar no Windows Server 2012 ou posterior.
* O controlador de domínio usado pelo Azure AD deve ser gravável. *Não há suporte para* o uso de um RODC (controlador de domínio somente leitura) e Azure ad Connect não segue redirecionamentos de gravação.
* Usando florestas ou domínios locais usando "pontilhado" (o nome contém um ponto ".") *Não há suporte para* nomes NetBIOS.
* Recomendamos que você [habilite a lixeira Active Directory](how-to-connect-sync-recycle-bin.md).

### <a name="powershell-execution-policy"></a>Política de execução do PowerShell
Azure Active Directory Connect executa scripts do PowerShell assinados como parte da instalação do. Verifique se a política de execução do PowerShell permitirá a execução de scripts.

A política de execução recomendada durante a instalação é "RemoteSigned".

Para obter mais informações sobre como definir a política de execução do PowerShell, consulte [Set-ExecutionPolicy](/powershell/module/microsoft.powershell.security/set-executionpolicy).


### <a name="azure-ad-connect-server"></a>Servidor do Azure AD Connect
O servidor de Azure AD Connect contém dados de identidade críticos. É importante que o acesso administrativo a esse servidor seja devidamente protegido. Siga as diretrizes em [protegendo o acesso privilegiado](/windows-server/identity/securing-privileged-access/securing-privileged-access). 

O servidor de Azure AD Connect deve ser tratado como um componente da camada 0, conforme documentado no [modelo de camada administrativa Active Directory](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) 

Para saber mais sobre como proteger seu ambiente de Active Directory, consulte [práticas recomendadas para proteger Active Directory](/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory).

#### <a name="installation-prerequisites"></a>Pré-requisitos da instalação

- Azure AD Connect deve ser instalado em um Windows Server 2012 ou posterior ingressado no domínio. 
- Azure AD Connect não pode ser instalado no Small Business Server ou no Windows Server Essentials antes de 2019 (há suporte para o Windows Server Essentials 2019). O servidor deve estar usando o Windows Server standard ou superior. 
- O servidor do Azure AD Connect deve ter uma GUI completa instalada. Não há suporte para a instalação do Azure AD Connect no Windows Server Core. 
- O servidor de Azure AD Connect não deve ter a transcrição do PowerShell Política de Grupo habilitada se você usar o assistente de Azure AD Connect para gerenciar a configuração do Serviços de Federação do Active Directory (AD FS) (AD FS). Você poderá habilitar a transcrição do PowerShell se usar o assistente de Azure AD Connect para gerenciar a configuração de sincronização. 
- Se AD FS estiver sendo implantado: 
    - Os servidores nos quais AD FS ou proxy de aplicativo Web estão instalados devem ser Windows Server 2012 R2 ou posterior. O gerenciamento remoto do Windows deve estar habilitado nesses servidores para instalação remota. 
    - Você deve configurar certificados TLS/SSL. Para obter mais informações, consulte [Gerenciando protocolos SSL/TLS e conjuntos de codificação para AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) e [gerenciar certificados SSL no AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap).
    - Você deve configurar a resolução de nome. 
- Se os seus administradores globais tiverem a MFA habilitada, a URL https://secure.aadcdn.microsoftonline-p.com *deverá* estar na lista de sites confiáveis. Você será solicitado a adicionar esse site à lista de sites confiáveis quando for solicitado um desafio de MFA e ele não tiver sido adicionado antes. Você pode usar o Internet Explorer para adicioná-la aos seus sites confiáveis.

#### <a name="harden-your-azure-ad-connect-server"></a>Proteger seu servidor de Azure AD Connect 
Recomendamos que você proteja seu servidor de Azure AD Connect para diminuir a superfície de ataque de segurança para esse componente crítico de seu ambiente de ti. Seguir essas recomendações ajudará a reduzir alguns riscos de segurança para sua organização.

- Trate Azure AD Connect o mesmo que um controlador de domínio e outros recursos da camada 0. Para obter mais informações, consulte [Active Directory modelo de camada administrativa](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).
- Restrinja o acesso administrativo ao servidor de Azure AD Connect somente a administradores de domínio ou a outros grupos de segurança rigidamente controlados.
- Crie uma [conta dedicada para todos os funcionários com acesso privilegiado](/windows-server/identity/securing-privileged-access/securing-privileged-access). Os administradores não devem navegar na Web, verificar seus emails e realizar tarefas de produtividade cotidianas com contas altamente privilegiadas.
- Siga as orientações fornecidas em [protegendo o acesso privilegiado](/windows-server/identity/securing-privileged-access/securing-privileged-access). 
- Negue o uso da autenticação NTLM com o servidor AADConnect. Aqui estão algumas maneiras de fazer isso: [restringindo o NTLM no servidor AADConnect](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-outgoing-ntlm-traffic-to-remote-servers) e [restringindo o NTLM em um domínio](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-ntlm-authentication-in-this-domain)
- Verifique se cada computador tem uma senha de administrador local exclusiva. Para obter mais informações, consulte a [solução de senha de administrador local (lapsos)](https://support.microsoft.com/help/3062591/microsoft-security-advisory-local-administrator-password-solution-laps) pode configurar senhas aleatórias exclusivas em cada estação de trabalho e servidor armazená-las em Active Directory protegidas por uma ACL. Somente usuários autorizados qualificados podem ler ou solicitar a redefinição dessas senhas de conta de administrador local. Você pode obter as interrupções para uso em estações de trabalho e servidores do [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=46899). Diretrizes adicionais para operar um ambiente com interrupções e estações de trabalho com acesso privilegiado (PAWs) podem ser encontradas em [padrões operacionais com base no princípio de origem limpa](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#operational-standards-based-on-clean-source-principle). 
- Implemente [estações de trabalho de acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) dedicadas para todos os funcionários com acesso privilegiado aos sistemas de informações da sua organização. 
- Siga estas [diretrizes adicionais](/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface) para reduzir a superfície de ataque do seu ambiente de Active Directory.


### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server usado pelo Azure AD Connect
* O Azure AD Connect requer um banco de dados do SQL Server para armazenar dados de identidade. Por padrão, um SQL Server o LocalDB 2012 Express (uma versão leve do SQL Server Express) é instalado. SQL Server Express tem um limite de tamanho de 10 GB que permite gerenciar aproximadamente 100.000 objetos. Se você precisar gerenciar um volume maior de objetos de diretório, aponte o assistente de instalação para uma instalação diferente do SQL Server. O tipo de instalação SQL Server pode afetar o [desempenho do Azure ad Connect](./plan-connect-performance-factors.md#sql-database-factors).
* Se você usar uma instalação diferente do SQL Server, esses requisitos se aplicarão:
  * Azure AD Connect dá suporte a todas as versões de SQL Server do 2012 (com a service pack mais recente) para SQL Server 2019. O banco de dados SQL do Azure *não tem suporte* como um banco de dados.
  * Deve usar uma ordenação de SQL que não diferencia maiúsculas de minúsculas. Essas ordenações são identificadas com um \_CI_ no nome. O uso de um agrupamento que diferencia maiúsculas de minúsculas identificado por \_ CS_ em seu nome *não é suportado*.
  * Você pode ter apenas um mecanismo de sincronização por instância do SQL. *Não há suporte para* o compartilhamento de uma instância do SQL com a sincronização do fim/mim, DirSync ou Azure ad Sync.

### <a name="accounts"></a>Contas
* Você deve ter uma conta de administrador global do Azure AD para o locatário do Azure AD com o qual você deseja integrar. Essa conta deve ser uma *conta corporativa ou de estudante* e não pode ser uma *conta Microsoft*.
* Se você usar [as configurações expressas](reference-connect-accounts-permissions.md#express-settings-installation) ou atualizar do DirSync, deverá ter uma conta de administrador corporativo para seu Active Directory local.
* Se você usar o caminho de instalação de configurações personalizadas, terá mais opções. Para obter mais informações, consulte [configurações de instalação personalizadas](reference-connect-accounts-permissions.md#custom-installation-settings).

### <a name="connectivity"></a>Conectividade
* O servidor do Azure AD Connect precisa da resolução de DNS para a intranet e a Internet. O servidor DNS deve conseguir resolver nomes tanto para o Active Directory local quanto para os pontos de extremidade do Azure AD.
* Se você tiver firewalls em sua intranet e precisar abrir portas entre os servidores de Azure AD Connect e seus controladores de domínio, consulte [Azure ad Connect portas](reference-connect-ports.md) para obter mais informações.
* Se o seu proxy ou firewall limitar quais URLs podem ser acessadas, as URLs documentadas em [intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) devem ser abertas. Consulte também [as URLs do portal do Azure no seu firewall ou servidor proxy](../../azure-portal/azure-portal-safelist-urls.md?tabs=public-cloud).
  * Se você estiver usando a nuvem da Microsoft na Alemanha ou na Microsoft Azure Governamental nuvem, consulte [Azure ad Connect as considerações de instâncias do serviço de sincronização](reference-connect-instances.md) para URLs.
* O Azure AD Connect (versão 1.1.614.0 ou superior) usa o TLS 1.2 por padrão para criptografar a comunicação entre o mecanismo de sincronização e o Azure AD. Se TLS 1.2 não estiver disponível no sistema operacional subjacente, o Azure AD Connect reverterá progressivamente para protocolos mais antigos (TLS 1.1 e TLS 1.0).
* Antes da versão 1.1.614.0, o Azure AD Connect usa TLS 1.0 por padrão para criptografar a comunicação entre o mecanismo de sincronização e o Azure AD. Para mudar para TLS 1.2, siga as etapas em [Habilitar TLS 1.2 no Azure Connect AD](#enable-tls-12-for-azure-ad-connect).
* Se você estiver usando um proxy de saída para se conectar à Internet, a configuração a seguir no arquivo de **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** deverá ser adicionada para o assistente de instalação e Azure ad Connect sincronização para poder se conectar à Internet e ao Azure AD. Esse texto deve ser inserido na parte inferior do arquivo. Nesse código, *&lt; PROXYADDRESS &gt;* representa o endereço IP do proxy real ou o nome do host.

    ```
        <system.net>
            <defaultProxy>
                <proxy
                usesystemdefault="true"
                proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
                bypassonlocal="true"
                />
            </defaultProxy>
        </system.net>
    ```

* Se o servidor proxy exigir autenticação, a [conta de serviço](reference-connect-accounts-permissions.md#adsync-service-account) deverá estar localizada no domínio. Use o caminho de instalação de configurações personalizadas para especificar uma [conta de serviço personalizada](how-to-connect-install-custom.md#install-required-components). Você também precisa de uma alteração diferente para machine.config. Com essa alteração no machine.config, o assistente de instalação e o mecanismo de sincronização respondem às solicitações de autenticação do servidor proxy. Em todas as páginas do assistente de instalação, excluindo a página **Configurar** , as credenciais do usuário conectado são usadas. Na página **Configurar** no final do assistente de instalação, o contexto é alternado para a [conta de serviço](reference-connect-accounts-permissions.md#adsync-service-account) que você criou. A seção machine.config deve ter a seguinte aparência:

    ```
        <system.net>
            <defaultProxy enabled="true" useDefaultCredentials="true">
                <proxy
                usesystemdefault="true"
                proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
                bypassonlocal="true"
                />
            </defaultProxy>
        </system.net>
    ```

* Se a configuração de proxy estiver sendo feita em uma instalação existente, o **serviço de sincronização de Microsoft Azure ad** precisará ser reiniciado uma vez para que o Azure ad Connect Leia a configuração de proxy e atualize o comportamento. 
* Quando o Azure AD Connect envia uma solicitação da Web ao Azure AD como parte da sincronização de diretório, o Azure AD pode levar até 5 minutos para responder. É comum que os servidores proxy tenham a configuração de tempo limite de ociosidade de conexão. Verifique se a configuração está definida para pelo menos 6 minutos ou mais.

Para obter mais informações, consulte o MSDN sobre o [elemento proxy padrão](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
Para obter mais informações quando você tiver problemas de conectividade, consulte [Solucionar problemas de conectividade](tshoot-connect-connectivity.md).

### <a name="other"></a>Outro
Opcional: Use uma conta de usuário de teste para verificar a sincronização.

## <a name="component-prerequisites"></a>Pré-requisitos do componente
### <a name="powershell-and-net-framework"></a>PowerShell e .NET Framework
O Azure AD Connect depende do Microsoft PowerShell e do .NET Framework 4.5.1. Você precisa desta versão ou de uma versão posterior instalada no seu servidor. Dependendo da versão do Windows Server, execute as seguintes ações:

* Windows Server 2012 R2
  * O Microsoft PowerShell é instalado por padrão. Nenhuma ação é necessária.
  * .NET Framework 4.5.1 e versões posteriores são oferecidas pelo Windows Update. Verifique se você instalou as atualizações mais recentes para o Windows Server no painel de controle.
* Windows Server 2012
  * A versão mais recente do Microsoft PowerShell está disponível no Windows Management Framework 4,0, disponível no [centro de download da Microsoft](https://www.microsoft.com/downloads).
  * O .NET Framework 4.5.1 e versões posteriores estão disponíveis no [centro de download da Microsoft](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Habilitar TLS 1.2 no Azure Connect AD
Antes da versão 1.1.614.0, o Azure AD Connect usa TLS 1.0 por padrão para criptografar a comunicação entre o servidor do mecanismo de sincronização e o Azure AD. Você pode configurar aplicativos .NET para usar o TLS 1,2 por padrão no servidor. Para obter mais informações sobre o TLS 1,2, consulte [Microsoft Security Advisory 2960358](/security-updates/SecurityAdvisories/2015/2960358).

1. Verifique se você tem o hotfix do .NET 4.5.1 instalado para seu sistema operacional. Para obter mais informações, consulte [Microsoft Security Advisory 2960358](/security-updates/SecurityAdvisories/2015/2960358). É possível ter esse hotfix ou uma versão posterior já instalada no servidor.

1. Para todos os sistemas operacionais, defina essa chave do registro e reinicie o servidor.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
1. Se você também quiser habilitar o TLS 1,2 entre o servidor do mecanismo de sincronização e um SQL Server remoto, verifique se você tem as versões necessárias instaladas para o [suporte a TLS 1,2 para Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Pré-requisitos para a configuração e instalação de federação
### <a name="windows-remote-management"></a>Gerenciamento Remoto do Windows
Ao usar Azure AD Connect para implantar AD FS ou o proxy de aplicativo Web (WAP), verifique estes requisitos:

* Se o servidor de destino estiver ingressado no domínio, verifique se o Windows Remote gerenciado está habilitado.
  * Em uma janela de comando do PowerShell com privilégios elevados, use o comando `Enable-PSRemoting –force` .
* Se o servidor de destino for um computador WAP não ingressado no domínio, haverá alguns requisitos adicionais:
  * No computador de destino (computador WAP):
    * Verifique se o serviço de Gerenciamento Remoto do Windows/WS-Management (WinRM) está em execução por meio do snap-in serviços.
    * Em uma janela de comando do PowerShell com privilégios elevados, use o comando `Enable-PSRemoting –force` .
  * No computador no qual o assistente está em execução (se o computador de destino não for ingressado no domínio ou for um domínio não confiável):
    * Em uma janela de comando do PowerShell com privilégios elevados, use o comando `Set-Item.WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate` .
    * No Gerenciador do servidor:
      * Adicione um host WAP de DMZ a um pool de computadores. No Gerenciador do servidor, selecione **gerenciar**  >  **adicionar servidores** e, em seguida, use a guia **DNS** .
      * Na guia **Gerenciador do servidor todos os servidores** , clique com o botão direito do mouse no servidor WAP e selecione **gerenciar como**. Insira as credenciais locais (não domínio) para o computador WAP.
      * Para validar a conectividade remota do PowerShell, na guia **Gerenciador do servidor todos os servidores** , clique com o botão direito do mouse no servidor WAP e selecione **Windows PowerShell**. Uma sessão remota do PowerShell deve ser aberta para garantir que as sessões remotas do PowerShell possam ser estabelecidas.

### <a name="tlsssl-certificate-requirements"></a>Requisitos de certificado TLS/SSL
* Recomendamos que você use o mesmo certificado TLS/SSL em todos os nós do seu farm de AD FS e todos os servidores de proxy de aplicativo Web.
* O certificado deve ser um certificado X509.
* Você pode usar um certificado autoassinado nos servidores da federação em um ambiente de laboratório de teste. Para um ambiente de produção, recomendamos que você obtenha o certificado de uma autoridade de certificação pública.
  * Se você estiver usando um certificado que não é confiável publicamente, verifique se o certificado instalado em cada servidor proxy de aplicativo Web é confiável no servidor local e em todos os servidores de Federação.
* A identidade do certificado deve corresponder ao nome do serviço de federação (por exemplo, sts.contoso.com).
  * A identidade é uma extensão de SAN (nome alternativo da entidade) do tipo dNSName ou, se não houver entradas de SAN, o nome da entidade será especificado como um nome comum.
  * Várias entradas de SAN podem estar presentes no certificado desde que uma delas corresponda ao nome do serviço de Federação.
  * Se você estiver planejando usar Workplace Join, será necessária uma SAN adicional com o valor **enterpriseregistration.** seguido pelo sufixo UPN (nome principal do usuário) da sua organização, por exemplo, enterpriseregistration.contoso.com.
* Não há suporte para certificados baseados em chaves CNG (CryptoAPI Next-Generation) e KSPs (provedores de armazenamento de chaves). Como resultado, você deve usar um certificado baseado em um CSP (provedor de serviços de criptografia) e não um KSP.
* Há suporte para certificados curinga.

### <a name="name-resolution-for-federation-servers"></a>Resolução de nome para servidores de federação
* Configure os registros DNS para o nome do AD FS (por exemplo, sts.contoso.com) para a intranet (seu servidor DNS interno) e a extranet (DNS público por meio do seu registrador de domínio). Para o registro DNS da intranet, use registros A, não registros CNAME. O uso de registros A é necessário para que a autenticação do Windows funcione corretamente em seu computador ingressado no domínio.
* Se você estiver implantando mais de um servidor de AD FS ou servidor proxy de aplicativo Web, certifique-se de que você configurou o balanceador de carga e que os registros DNS para o nome de AD FS (por exemplo, sts.contoso.com) apontam para o balanceador de carga.
* Para que a autenticação integrada do Windows funcione para aplicativos de navegador usando o Internet Explorer em sua intranet, verifique se o nome da AD FS (por exemplo, sts.contoso.com) foi adicionado à zona da intranet no Internet Explorer. Esse requisito pode ser controlado por meio de Política de Grupo e implantado em todos os seus computadores ingressados no domínio.

## <a name="azure-ad-connect-supporting-components"></a>Componentes de suporte do Azure AD Connect
Azure AD Connect instala os seguintes componentes no servidor onde Azure AD Connect está instalado. Esta lista é para uma instalação básica do Express. Se você optar por usar um SQL Server diferente na página **instalar serviços de sincronização** , o SQL Express LocalDB não será instalado localmente.

* Azure AD Connect Health
* Utilitários de linha de comando do Microsoft SQL Server 2012
* LocalDB do Microsoft SQL Server 2012 Express
* Microsoft SQL Server 2012 Native Client
* Pacote de redistribuição de Microsoft Visual C++ 2013

## <a name="hardware-requirements-for-azure-ad-connect"></a>Requisitos de hardware para o Azure AD Connect
A tabela a seguir mostra os requisitos mínimos para o Azure AD Connect computador de sincronização.

| Número de objetos no Active Directory | CPU | Memória | Tamanho do disco rígido |
| --- | --- | --- | --- |
| Menos de 10.000 |1,6 GHz |4 GB |70 GB |
| 10.000–50.000 |1,6 GHz |4 GB |70 GB |
| 50.000–100.000 |1,6 GHz |16 GB |100 GB |
| Para 100.000 ou mais objetos, a versão completa do SQL Server é necessária | | | |
| 100.000–300.000 |1,6 GHz |32 GB |300 GB |
| 300.000–600.000 |1,6 GHz |32 GB |450 GB |
| Mais de 600.000 |1,6 GHz |32 GB |500 GB |

Os requisitos mínimos para computadores que executam AD FS ou servidores proxy de aplicativo Web são:

* CPU: Dual-core de 1,6 GHz ou superior
* Memória: 2 GB ou mais
* VM do Azure: Configuração A2 ou superior

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
