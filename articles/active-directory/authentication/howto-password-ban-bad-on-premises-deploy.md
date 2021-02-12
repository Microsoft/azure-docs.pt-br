---
title: Implantar a proteção de senha do Azure AD local
description: Saiba como planejar e implantar a proteção de senha do Azure AD em um ambiente de Active Directory Domain Services local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f17f6eb913d1ea54e8db6acd369d165553e16ec
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100091033"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>Planejar e implantar a proteção de senha do Azure Active Directory local

Os usuários geralmente criam senhas que usam palavras locais comuns, como uma escola, uma equipe esportiva ou uma pessoa famosa. Essas senhas são fáceis de serem adivinhadas e fracas contra os ataques baseados em dicionário. Para impor senhas fortes em sua organização, a proteção de senha do Azure Active Directory (Azure AD) fornece uma lista de senhas excluídas global e personalizada. Uma solicitação de alteração de senha falhará se houver uma correspondência nessa lista de senhas banidas.

Para proteger seu ambiente de Active Directory Domain Services (AD DS) local, você pode instalar e configurar a proteção de senha do Azure AD para trabalhar com seu DC local. Este artigo mostra como instalar e registrar o serviço proxy de proteção de senha do Azure AD e o agente DC de proteção de senha do Azure AD em seu ambiente local.

Para obter mais informações sobre como a proteção de senha do Azure AD funciona em um ambiente local, consulte [como impor a proteção de senha do Azure ad para o Windows Server Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="deployment-strategy"></a>Estratégia de implantação

O diagrama a seguir mostra como os componentes básicos da proteção de senha do Azure AD funcionam em conjunto em um ambiente de Active Directory local:

![Como os componentes de Proteção de Senha do Azure AD trabalham em conjunto](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

É uma boa ideia examinar como o software funciona antes de implantá-lo. Para obter mais informações, consulte [visão geral conceitual da proteção de senha do Azure ad](concept-password-ban-bad-on-premises.md).

Recomendamos que você inicie as implantações no modo de *auditoria* . O modo de auditoria é a configuração inicial padrão, em que as senhas podem continuar a ser definidas. As senhas que seriam bloqueadas são registradas no log de eventos. Depois de implantar os servidores proxy e os agentes de DC no modo de auditoria, monitore o impacto que a política de senha terá nos usuários quando a política for imposta.

Durante o estágio de auditoria, muitas organizações consideram que as seguintes situações se aplicam:

* Eles precisam melhorar os processos operacionais existentes para usar senhas mais seguras.
* Os usuários geralmente usam senhas não seguras.
* Eles precisam informar os usuários sobre a próxima alteração na imposição de segurança, o possível impacto sobre eles e como escolher senhas mais seguras.

Também é possível que uma validação de senha mais forte afete sua automação de implantação do controlador de domínio Active Directory existente. Recomendamos que pelo menos uma promoção de DC e um rebaixamento de DC ocorram durante a avaliação do período de auditoria para ajudar a descobrir esses problemas. Para obter mais informações, consulte os seguintes artigos:

* [Ntdsutil.exe não pode definir uma senha fraca do modo de reparo de serviços de diretório](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [A promoção da réplica do controlador de domínio falha devido a uma senha fraca do modo de reparo do serviços de diretório](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [O rebaixamento do controlador de domínio falha devido a uma senha de administrador local fraca](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Depois que o recurso estiver sendo executado no modo de auditoria por um período razoável, você poderá alternar a configuração de *auditoria* para *impor* para exigir senhas mais seguras. O monitoramento adicional durante esse tempo é uma boa ideia.

É importante observar que a proteção de senha do Azure AD só pode validar senhas durante a alteração de senha ou as operações de definição. As senhas que foram aceitas e armazenadas no Active Directory antes da implantação da proteção de senha do Azure AD nunca serão validadas e continuarão funcionando no estado em que se encontram. Ao longo do tempo, todos os usuários e contas serão iniciados usando a proteção de senha do Azure AD senhas validadas, já que suas senhas existentes expiram normalmente. As contas configuradas com "a senha nunca expira" são isentas disso.

### <a name="multiple-forest-considerations"></a>Considerações sobre várias florestas

Não há requisitos adicionais para implantar a Proteção de Senha do Azure AD em várias florestas.

Cada floresta é configurada independentemente, conforme descrito na seção a seguir para [implantar a proteção de senha do Azure ad local](#download-required-software). Cada proxy de proteção de senha do Azure AD só pode dar suporte a controladores de domínio da floresta à qual ele está associado.

O software de proteção de senha do Azure AD em qualquer floresta não reconhece o software de proteção de senha implantado em outras florestas, independentemente das configurações de confiança Active Directory.

### <a name="read-only-domain-controller-considerations"></a>Considerações sobre o controlador de domínio somente leitura

Os eventos de alteração ou de definição de senha não são processados e persistidos em controladores de domínio somente leitura (RODCs). Em vez disso, eles são encaminhados para controladores de domínio graváveis. Você não precisa instalar o software de agente DC de proteção por senha do Azure AD em RODCs.

Além disso, não há suporte para executar o serviço de proxy de proteção de senha do Azure AD em um controlador de domínio somente leitura.

### <a name="high-availability-considerations"></a>Considerações sobre alta disponibilidade

A principal preocupação para a proteção por senha é a disponibilidade dos servidores proxy de proteção por senha do Azure AD quando os DCs em uma floresta tentam baixar novas políticas ou outros dados do Azure. Cada agente de DC de proteção de senha do Azure AD usa um algoritmo de estilo Round Robin simples ao decidir qual servidor proxy deve ser chamado. O agente ignora os servidores proxy que não estão respondendo.

Para implantações de Active Directory totalmente conectadas que têm replicação íntegra do estado da pasta SYSVOL e do diretório, dois servidores proxy de proteção por senha do Azure AD são suficientes para garantir a disponibilidade. Essa configuração resulta no download oportuno de novas políticas e outros dados. Você pode implantar servidores proxy de proteção por senha adicionais do Azure AD, se desejar.

O design do software do agente DC de proteção de senha do Azure AD atenua os problemas comuns associados à alta disponibilidade. O agente DC de proteção de senha do Azure AD mantém um cache local da política de senha baixada mais recentemente. Mesmo se todos os servidores proxy registrados ficarem indisponíveis, os agentes de DC de proteção de senha do Azure AD continuarão impõem sua política de senha armazenada em cache.

Uma frequência de atualização razoável para diretivas de senha em uma implantação grande geralmente é dias, não horas ou menos. Portanto, poucas interrupções dos servidores proxy não afetam significativamente a proteção de senha do Azure AD.

## <a name="deployment-requirements"></a>Requisitos para implantação

Para obter informações sobre licenciamento, consulte [requisitos de licenciamento da proteção por senha do Azure ad](concept-password-ban-bad.md#license-requirements).

Os seguintes requisitos básicos se aplicam:

* Todas as máquinas, incluindo controladores de domínio, que têm os componentes de proteção de senha do Azure AD instalados, devem ter o tempo de execução universal C instalado.
    * Você pode obter o tempo de execução, certificando-se de que você tenha todas as atualizações de Windows Update. Ou você pode obtê-lo em um pacote de atualização específico do sistema operacional. Para obter mais informações, consulte [atualizar para tempo de execução C universal no Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Você precisa de uma conta que tenha Active Directory privilégios de administrador de domínio no domínio raiz da floresta para registrar a floresta do Windows Server Active Directory com o Azure AD.
* O serviço de distribuição de chaves deve ser habilitado em todos os controladores de domínio no domínio que executam o Windows Server 2012 e versões posteriores. Por padrão, esse serviço é habilitado por meio do início do gatilho manual.

* A conectividade de rede deve existir entre pelo menos um controlador de domínio em cada domínio e pelo menos um servidor que hospede o serviço de proxy para a proteção de senha do Azure AD. Essa conectividade deve permitir que o controlador de domínio acesse a porta 135 do mapeador de ponto de extremidade RPC e a porta do servidor RPC no serviço de proxy.
    * Por padrão, a porta do servidor RPC é uma porta RPC dinâmica, mas pode ser configurada para [usar uma porta estática](#static).
* Todas as máquinas em que o serviço proxy de proteção de senha do Azure AD será instalado devem ter acesso à rede para os seguintes pontos de extremidade:

    |**Ponto de extremidade**|**Finalidade**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Solicitações de autenticação|
    |`https://enterpriseregistration.windows.net`|Funcionalidade de Proteção de Senha do Azure AD|

### <a name="azure-ad-password-protection-dc-agent"></a>Agente DC de proteção por senha do Azure AD

Os seguintes requisitos se aplicam ao agente DC da proteção de senha do Azure AD:

* Todos os computadores em que o software do agente DC da proteção de senha do Azure AD serão instalados devem executar o Windows Server 2012 ou posterior, incluindo as edições do Windows Server Core.
    * O domínio ou floresta Active Directory não precisa estar no nível funcional de domínio do Windows Server 2012 (DFL) ou no nível funcional de floresta (FFL). Conforme mencionado nos [princípios de design](concept-password-ban-bad-on-premises.md#design-principles), não há nenhum DFL ou FFL mínimo necessário para a execução do agente DC ou do software proxy.
* Todos os computadores que executam o agente DC de proteção de senha do Azure AD devem ter o .NET 4,5 instalado.
* Qualquer domínio Active Directory que executa o serviço de agente de DC de proteção de senha do Azure AD deve usar a replicação de Sistema de Arquivos Distribuído (DFSR) para replicação de SYSVOL.
   * Se o seu domínio ainda não estiver usando o DFSR, você deverá migrar antes de instalar a proteção de senha do Azure AD. Para obter mais informações, consulte [Guia de migração de replicação do SYSVOL: FRS para replicação do DFS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

    > [!WARNING]
    > O software de agente DC de proteção de senha do Azure AD será instalado no momento em controladores de domínio em domínios que ainda estão usando o FRS (a tecnologia predecessora para DFSR) para replicação de SYSVOL, mas o software não funcionará corretamente nesse ambiente.
    >
    > Efeitos colaterais negativos adicionais incluem arquivos individuais que falham na replicação e os procedimentos de restauração de SYSVOL que aparecem com êxito, mas falham silenciosamente na replicação de todos os arquivos.
    >
    > Migre seu domínio para usar o DFSR assim que possível, tanto para os benefícios inerentes do DFSR quanto para desbloquear a implantação da proteção de senha do Azure AD. Versões futuras do software serão desabilitadas automaticamente quando executadas em um domínio que ainda esteja usando o FRS.

### <a name="azure-ad-password-protection-proxy-service"></a>Serviço de proxy de proteção de senha do Azure AD

Os seguintes requisitos se aplicam ao serviço de proxy de proteção de senha do Azure AD:

* Todos os computadores nos quais o serviço proxy de proteção de senha do Azure AD será instalado devem executar o Windows Server 2012 R2 ou posterior, incluindo as edições do Windows Server Core.

    > [!NOTE]
    > A implantação do serviço proxy de proteção de senha do Azure AD é um requisito obrigatório para implantar a proteção de senha do Azure AD, embora o controlador de domínio possa ter conectividade de Internet direta de saída.

* Todas as máquinas em que o serviço proxy de proteção de senha do Azure AD será instalado devem ter o .NET 4,7 instalado.
    * O .NET 4,7 já deve estar instalado em um Windows Server totalmente atualizado. Se necessário, baixe e execute o instalador encontrado no [instalador offline do .NET Framework 4,7 para Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Todos os computadores que hospedam o serviço de proxy de proteção de senha do Azure AD devem ser configurados para conceder aos controladores de domínio a capacidade de fazer logon no serviço de proxy. Essa capacidade é controlada por meio da atribuição de privilégio "acessar este computador pela rede".
* Todos os computadores que hospedam o serviço de proxy de proteção de senha do Azure AD devem ser configurados para permitir o tráfego HTTP de saída TLS 1,2.
* Uma conta de *administrador global* ou de *administrador de segurança* para registrar o serviço proxy de proteção de senha do Azure AD e a floresta com o Azure AD.
* O acesso à rede deve ser habilitado para o conjunto de portas e URLs especificadas nos [procedimentos de configuração do ambiente de proxy de aplicativo](../manage-apps/application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>Pré-requisitos do atualizador do agente do Microsoft Azure AD Connect

O serviço atualizador do agente do Microsoft Azure AD Connect é instalado lado a lado com o serviço de proxy de proteção de senha do Azure AD. A configuração adicional é necessária para que o serviço de atualizador do agente do Microsoft Azure AD Connect seja capaz de funcionar:

* Se o seu ambiente usa um servidor proxy HTTP, siga as diretrizes especificadas em [trabalhar com servidores proxy locais existentes](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md).
* O serviço de atualizador do agente do Microsoft Azure AD Connect também requer as etapas do TLS 1,2 especificadas nos [requisitos de TLS](../manage-apps/application-proxy-add-on-premises-application.md#tls-requirements).

> [!WARNING]
> O proxy de proteção por senha do Azure AD e o Azure Proxy de Aplicativo do AD instalar versões diferentes do serviço de atualizador do agente Microsoft Azure AD Connect, que é o motivo pelo qual as instruções se referem ao conteúdo do proxy de aplicativo. Essas versões diferentes são incompatíveis quando instaladas lado a lado e isso impedirá que o serviço atualizador do agente entre em contato com o Azure para atualizações de software, para que você nunca instale o proxy de proteção de senha do Azure AD e o proxy de aplicativo no mesmo computador.

## <a name="download-required-software"></a>Baixar o software necessário

Há dois instaladores necessários para uma implantação de proteção de senha do Azure AD local:

* Agente DC de proteção de senha do Azure AD (*AzureADPasswordProtectionDCAgentSetup.msi*)
* Proxy de proteção de senha do Azure AD (*AzureADPasswordProtectionProxySetup.exe*)

Baixe ambos os instaladores do [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="install-and-configure-the-proxy-service"></a>Instalar e configurar o serviço de proxy

O serviço de proxy de proteção de senha do Azure AD normalmente está em um servidor membro em seu ambiente de AD DS local. Uma vez instalado, o serviço de proxy de proteção de senha do Azure AD se comunica com o Azure AD para manter uma cópia das listas de senhas globais e banidas pelo cliente para seu locatário do Azure AD.

Na próxima seção, você instalará os agentes de DC de proteção de senha do Azure AD em controladores de domínio em seu ambiente de AD DS local. Esses agentes de DC se comunicam com o serviço de proxy para obter as listas de senhas banidas mais recentes para uso durante o processamento de eventos de alteração de senha no domínio.

Escolha um ou mais servidores para hospedar o serviço de proxy de proteção de senha do Azure AD. As seguintes considerações se aplicam ao (s) servidor (es):

* Cada serviço desse tipo só pode fornecer políticas de senha para uma única floresta. O computador host deve ser Unido a qualquer domínio nessa floresta.
* Ele tem suporte para instalar o proxy no serviço em domínios raiz ou filho, ou uma combinação deles.
* Você precisa de conectividade de rede entre pelo menos um DC em cada domínio da floresta e um servidor proxy de proteção por senha.
* Você pode executar o serviço de proxy de proteção de senha do Azure AD em um controlador de domínio para teste, mas esse controlador de domínio requer conectividade com a Internet. Essa conectividade pode ser uma preocupação de segurança. Recomendamos essa configuração apenas para teste.
* Recomendamos pelo menos dois servidores proxy de proteção por senha do Azure AD por floresta para redundância, conforme observado na seção anterior sobre [considerações de alta disponibilidade](#high-availability-considerations).
* Não há suporte para executar o serviço de proxy de proteção de senha do Azure AD em um controlador de domínio somente leitura.

Para instalar o serviço de proxy de proteção de senha do Azure AD, conclua as seguintes etapas:

1. Para instalar o serviço de proxy de proteção de senha do Azure AD, execute o `AzureADPasswordProtectionProxySetup.exe` instalador de software.

    A instalação do software não requer uma reinicialização e pode ser automatizada usando procedimentos MSI padrão, como no exemplo a seguir:
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > O serviço de firewall do Windows deve estar em execução antes de instalar o `AzureADPasswordProtectionProxySetup.exe` pacote para evitar um erro de instalação.
    >
    > Se o Firewall do Windows estiver configurado para não ser executado, a solução alternativa será habilitar e executar temporariamente o serviço de firewall durante a instalação. O software proxy não tem dependência específica no firewall do Windows após a instalação.
    >
    > Se você estiver usando um firewall de terceiros, ele ainda deverá ser configurado para atender aos requisitos de implantação. Isso inclui permitir o acesso de entrada à porta 135 e à porta do servidor RPC de proxy. Para obter mais informações, consulte a seção anterior sobre [requisitos de implantação](#deployment-requirements).

1. O software de proxy de proteção de senha do Azure AD inclui um novo módulo do PowerShell, `AzureADPasswordProtection` . As etapas a seguir executam vários cmdlets deste módulo do PowerShell.

    Para usar este módulo, abra uma janela do PowerShell como administrador e importe o novo módulo da seguinte maneira:
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. Para verificar se o serviço de proxy de proteção de senha do Azure AD está em execução, use o seguinte comando do PowerShell:

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    O resultado deve mostrar o **status** *em execução*.

1. O serviço de proxy está em execução no computador, mas não tem credenciais para se comunicar com o Azure AD. Registre o servidor proxy de proteção por senha do Azure AD com o Azure AD usando o `Register-AzureADPasswordProtectionProxy` cmdlet.

    Este cmdlet requer credenciais de *administrador global* ou de *administrador de segurança* para seu locatário do Azure. Esse cmdlet também deve ser executado usando uma conta com privilégios de administrador local.

    Depois que esse comando for executado uma vez para um serviço de proxy de proteção de senha do Azure AD, as invocações adicionais serão realizadas com sucesso, mas são desnecessárias.

    O `Register-AzureADPasswordProtectionProxy` cmdlet dá suporte aos três modos de autenticação a seguir. Os dois primeiros modos dão suporte à autenticação multifator do Azure AD, mas o terceiro modo não.

    > [!TIP]
    > Pode haver um atraso perceptível antes da conclusão na primeira vez em que esse cmdlet for executado para um locatário específico do Azure. A menos que uma falha seja relatada, não se preocupe com esse atraso.

     * Modo de autenticação interativo:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Esse modo não funciona em sistemas operacionais Server Core. Em vez disso, use um dos modos de autenticação a seguir. Além disso, esse modo poderá falhar se a configuração de segurança reforçada do Internet Explorer estiver habilitada. A solução alternativa é desabilitar essa configuração, registrar o proxy e, em seguida, habilitá-lo novamente.

     * Modo de autenticação de código de dispositivo:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Quando solicitado, seguindo o link para abrir um navegador da Web e inserir o código de autenticação.

     * Modo de autenticação silenciosa (baseada em senha):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Esse modo falhará se a autenticação multifator do Azure AD for necessária para sua conta. Nesse caso, use um dos dois modos de autenticação anteriores ou, em vez disso, use uma conta diferente que não exija MFA.
        >
        > Você também poderá ver a MFA necessária se o registro de dispositivo do Azure (que é usado na capa pela proteção de senha do Azure AD) tiver sido configurado para exigir globalmente a MFA. Para solucionar esse requisito, você pode usar uma conta diferente que dá suporte a MFA com um dos dois modos de autenticação anteriores, ou pode também relaxar temporariamente o requisito de MFA de registro de dispositivo do Azure.
        >
        > Para fazer essa alteração, procure e selecione **Azure Active Directory** na portal do Azure e, em seguida, selecione **dispositivos > configurações do dispositivo**. Set **requer autenticação multifator para unir dispositivos** ao *.* Certifique-se de reconfigurar essa configuração de volta para *Sim* quando o registro for concluído.
        >
        > Recomendamos que os requisitos de MFA sejam ignorados apenas para fins de teste.

    No momento, você não precisa especificar o parâmetro *-ForestCredential* , que é reservado para funcionalidade futura.

    O registro do serviço proxy de proteção de senha do Azure AD é necessário apenas uma vez no tempo de vida do serviço. Depois disso, o serviço proxy de proteção de senha do Azure AD executará automaticamente qualquer outra manutenção necessária.

1. Agora, registre a floresta Active Directory local com as credenciais necessárias para se comunicar com o Azure usando o `Register-AzureADPasswordProtectionForest` cmdlet do PowerShell.

    > [!NOTE]
    > Se vários servidores proxy de proteção por senha do Azure AD estiverem instalados em seu ambiente, não importa qual servidor proxy você usa para registrar a floresta.

    O cmdlet requer credenciais de *administrador global* ou de *administrador de segurança* para seu locatário do Azure. Ele também requer privilégios de administrador corporativo Active Directory local. Você também deve executar esse cmdlet usando uma conta com privilégios de administrador local. A conta do Azure que é usada para registrar a floresta pode ser diferente da conta de Active Directory local.
    
    Esta etapa é executada uma vez por floresta.

    O `Register-AzureADPasswordProtectionForest` cmdlet dá suporte aos três modos de autenticação a seguir. Os dois primeiros modos dão suporte à autenticação multifator do Azure AD, mas o terceiro modo não.

    > [!TIP]
    > Pode haver um atraso perceptível antes da conclusão na primeira vez em que esse cmdlet for executado para um locatário específico do Azure. A menos que uma falha seja relatada, não se preocupe com esse atraso.

     * Modo de autenticação interativo:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Esse modo não funcionará em sistemas operacionais Server Core. Em vez disso, use um dos dois modos de autenticação a seguir. Além disso, esse modo poderá falhar se a configuração de segurança reforçada do Internet Explorer estiver habilitada. A solução alternativa é desabilitar essa configuração, registrar a floresta e, em seguida, habilitá-la novamente.  

     * Modo de autenticação de código de dispositivo:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Quando solicitado, seguindo o link para abrir um navegador da Web e inserir o código de autenticação.

     * Modo de autenticação silenciosa (baseada em senha):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Esse modo falhará se a autenticação multifator do Azure AD for necessária para sua conta. Nesse caso, use um dos dois modos de autenticação anteriores ou, em vez disso, use uma conta diferente que não exija MFA.
        >
        > Você também poderá ver a MFA necessária se o registro de dispositivo do Azure (que é usado na capa pela proteção de senha do Azure AD) tiver sido configurado para exigir globalmente a MFA. Para solucionar esse requisito, você pode usar uma conta diferente que dá suporte a MFA com um dos dois modos de autenticação anteriores, ou pode também relaxar temporariamente o requisito de MFA de registro de dispositivo do Azure.
        >
        > Para fazer essa alteração, procure e selecione **Azure Active Directory** na portal do Azure e, em seguida, selecione **dispositivos > configurações do dispositivo**. Set **requer autenticação multifator para unir dispositivos** ao *.* Certifique-se de reconfigurar essa configuração de volta para *Sim* quando o registro for concluído.
        >
        > Recomendamos que os requisitos de MFA sejam ignorados apenas para fins de teste.

       Esses exemplos só serão bem-sucedidos se o usuário conectado no momento for também um administrador de domínio Active Directory para o domínio raiz. Se esse não for o caso, você poderá fornecer credenciais de domínio alternativas por meio do parâmetro *-ForestCredential* .

    O registro da floresta Active Directory é necessário apenas uma vez no tempo de vida da floresta. Depois disso, os agentes de DC da proteção por senha do Azure AD na floresta executam automaticamente qualquer outra manutenção necessária. Após `Register-AzureADPasswordProtectionForest` o ser executado com êxito para uma floresta, as invocações adicionais do cmdlet são bem-sucedidas, mas são desnecessárias.
    
    Para `Register-AzureADPasswordProtectionForest` que o tenha sucesso, pelo menos um DC executando o Windows Server 2012 ou posterior deve estar disponível no domínio do servidor proxy de proteção de senha do Azure AD. O software do agente DC da proteção de senha do Azure AD não precisa ser instalado em nenhum controlador de domínio antes desta etapa.

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>Configurar o serviço de proxy para se comunicar por meio de um proxy HTTP

Se seu ambiente exigir o uso de um proxy HTTP específico para se comunicar com o Azure, use as etapas a seguir para configurar o serviço de proteção de senha do Azure AD.

Crie um arquivo de *AzureADPasswordProtectionProxy.exe.config* na `%ProgramFiles%\Azure AD Password Protection Proxy\Service` pasta. Inclua o seguinte conteúdo:

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

Se o proxy HTTP exigir autenticação, adicione a marca *UseDefaultCredentials* :

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

Em ambos os casos, substitua `http://yourhttpproxy.com:8080` pelo endereço e pela porta do seu servidor proxy HTTP específico.

Se o proxy HTTP estiver configurado para usar uma política de autorização, você deverá conceder acesso à conta de computador Active Directory da máquina que hospeda o serviço de proxy para proteção por senha.

Recomendamos que você pare e reinicie o serviço de proxy de proteção de senha do Azure AD depois de criar ou atualizar o arquivo de *AzureADPasswordProtectionProxy.exe.config* .

O serviço de proxy não dá suporte ao uso de credenciais específicas para se conectar a um proxy HTTP.

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>Configurar o serviço de proxy para escutar em uma porta específica

O software de agente DC de proteção por senha do Azure AD usa RPC sobre TCP para se comunicar com o serviço de proxy. Por padrão, o serviço de proxy de proteção de senha do Azure AD escuta em qualquer ponto de extremidade RPC dinâmico disponível. Você pode configurar o serviço para escutar em uma porta TCP específica, se necessário, devido à topologia de rede ou aos requisitos de firewall em seu ambiente.

<a id="static" /></a>Para configurar o serviço para ser executado em uma porta estática, use o `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet da seguinte maneira:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> Você deve parar e reiniciar o serviço proxy de proteção de senha do Azure AD para que essas alterações entrem em vigor.

Para configurar o serviço para ser executado em uma porta dinâmica, use o mesmo procedimento, mas defina *StaticPort* de volta para zero:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> Você deve parar e reiniciar o serviço proxy de proteção de senha do Azure AD para que essas alterações entrem em vigor.

O serviço de proxy de proteção de senha do Azure AD requer uma reinicialização manual após qualquer alteração na configuração de porta. Você não precisa reiniciar o serviço de agente DC de proteção de senha do Azure AD em controladores de domínio depois de fazer essas alterações de configuração.

Para consultar a configuração atual do serviço, use o cmdlet, `Get-AzureADPasswordProtectionProxyConfiguration` conforme mostrado no exemplo a seguir

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

A saída de exemplo a seguir mostra que o serviço proxy de proteção de senha do Azure AD está usando uma porta dinâmica:

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>Instalar o serviço de agente de controlador de domínio

Para instalar o serviço de agente DC de proteção de senha do Azure AD, execute o `AzureADPasswordProtectionDCAgentSetup.msi` pacote.

Você pode automatizar a instalação do software usando procedimentos MSI padrão, conforme mostrado no exemplo a seguir:

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

O `/norestart` sinalizador pode ser omitido se você preferir que o instalador reinicialize o computador automaticamente.

A instalação do software, ou desinstalação, requer uma reinicialização. Esse requisito é porque as DLLs de filtro de senha são carregadas ou descarregadas apenas por uma reinicialização.

A instalação da proteção de senha local do Azure AD é concluída depois que o software do agente DC é instalado em um controlador de domínio e esse computador é reinicializado. Nenhuma outra configuração é necessária ou possível. Os eventos de alteração de senha em relação aos DCs locais usam as listas de senhas banidas configuradas do Azure AD.

Para habilitar a proteção de senha do Azure AD local no portal do Azure ou configurar senhas excluídas personalizadas, consulte [habilitar a proteção de senha do Azure ad local](howto-password-ban-bad-on-premises-operations.md).

> [!TIP]
> Você pode instalar o agente DC da proteção de senha do Azure AD em um computador que ainda não é um controlador de domínio. Nesse caso, o serviço é iniciado e executado, mas permanece inativo até que o computador seja promovido a controlador de domínio.

## <a name="upgrading-the-proxy-service"></a>Atualizando o serviço de proxy

O serviço de proxy de proteção de senha do Azure AD dá suporte à atualização automática. A atualização automática usa o serviço de atualizador do agente Microsoft Azure AD Connect, que é instalado lado a lado com o serviço de proxy. A atualização automática está ativada por padrão e pode ser habilitada ou desabilitada usando o `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet.

A configuração atual pode ser consultada usando o `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet. Recomendamos que a configuração de atualização automática sempre esteja habilitada.

O `Get-AzureADPasswordProtectionProxy` cmdlet pode ser usado para consultar a versão de software de todos os servidores proxy de proteção de senha do Azure ad atualmente instalados em uma floresta.

### <a name="manual-upgrade-process"></a>Processo de atualização manual

Uma atualização manual é realizada executando a versão mais recente do `AzureADPasswordProtectionProxySetup.exe` instalador de software. A versão mais recente do software está disponível no [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

Não é necessário desinstalar a versão atual do serviço de proxy de proteção de senha do Azure AD-o instalador executa uma atualização in-loco. Nenhuma reinicialização deve ser necessária ao atualizar o serviço de proxy. A atualização de software pode ser automatizada usando procedimentos MSI padrão, como `AzureADPasswordProtectionProxySetup.exe /quiet` .

## <a name="upgrading-the-dc-agent"></a>Atualizando o agente de DC

Quando uma versão mais recente do software de agente de DC de proteção de senha do Azure AD estiver disponível, a atualização será realizada executando a versão mais recente do `AzureADPasswordProtectionDCAgentSetup.msi` pacote de software. A versão mais recente do software está disponível no [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

Não é necessário desinstalar a versão atual do software do agente do DC-o instalador executa uma atualização in-loco. Uma reinicialização é sempre necessária ao atualizar o software do agente de DC-esse requisito é causado pelo comportamento principal do Windows.

A atualização de software pode ser automatizada usando procedimentos MSI padrão, como `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart` .

Você pode omitir o `/norestart` sinalizador se preferir que o instalador reinicialize o computador automaticamente.

O `Get-AzureADPasswordProtectionDCAgent` cmdlet pode ser usado para consultar a versão de software de todos os agentes de DC de proteção de senha do Azure ad atualmente instalados em uma floresta.

## <a name="next-steps"></a>Próximas etapas

Agora que você instalou os serviços necessários para a proteção de senha do Azure AD nos servidores locais, [habilite a proteção de senha do Azure ad local no portal do Azure](howto-password-ban-bad-on-premises-operations.md) para concluir a implantação.
