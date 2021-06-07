---
title: Implantar a Proteção de Senha do Azure AD no local
description: Saiba como planejar e implantar a Proteção de Senha do Azure AD em um ambiente local do Active Directory Domain Services
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
ms.openlocfilehash: c8cae19bd07e1cc87a0aaa25e47cf5f431d566ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653806"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>Planejar e implantar a Proteção de Senha do Azure Active Directory local

Os usuários geralmente criam senhas que usam palavras locais comuns, como uma escola, uma equipe esportiva ou uma pessoa famosa. Essas senhas são fáceis de serem adivinhadas e fracas contra os ataques baseados em dicionário. A fim de impor senhas fortes em sua organização, a Proteção de Senha do Azure AD (Azure Active Directory) fornece uma lista global e personalizada de senhas proibidas. Uma solicitação de alteração de senha falha quando há uma correspondência na lista de senhas proibidas.

Para proteger o ambiente local do AD DS (Active Directory Domain Services), instale e configure a Proteção de Senha do Azure AD para trabalhar com o DC local. Este artigo mostra como instalar e registrar o serviço de proxy da Proteção de Senha do Azure AD e o agente de DC da Proteção de Senha do Azure AD no ambiente local.

Para obter mais informações sobre como a Proteção de Senha do Azure AD funciona em um ambiente local, confira [Como impor a Proteção de Senha do Azure AD para o Windows Server Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="deployment-strategy"></a>Estratégia de implantação

O diagrama a seguir mostra como os componentes básicos da proteção de senha do Azure AD funcionam em conjunto em um ambiente local do Active Directory:

![Como os componentes de Proteção de Senha do Azure AD trabalham em conjunto](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

É uma boa ideia analisar como o software funciona antes de implantá-lo. Para obter mais informações, confira [Visão geral conceitual da Proteção de Senha do Azure AD](concept-password-ban-bad-on-premises.md).

Recomenda-se iniciar as implantações no modo de *auditoria*. O modo de auditoria é a configuração inicial padrão, em que as senhas podem continuar a ser definidas. As senhas que seriam bloqueadas são registradas no log de eventos. Depois de implantar os servidores proxy e os agentes de DC no modo de auditoria, monitore o impacto da política de senha nos usuários quando ela for imposta.

Durante o estágio de auditoria, muitas organizações consideram as seguintes situações aplicáveis:

* Eles precisam melhorar os processos operacionais existentes para usar senhas mais seguras.
* Os usuários geralmente usam senhas não seguras.
* Eles precisam ser informados sobre a nova alteração na imposição da segurança, o possível impacto dela sobre eles e como eles podem escolher senhas mais seguras.

Também é possível que uma validação de senha mais forte afete sua automação existente de implantação de controlador de domínio do Active Directory. Recomendamos que pelo menos uma promoção e um rebaixamento de DC ocorram durante a avaliação do período de auditoria para ajudar a descobrir tais problemas. Para obter mais informações, consulte os seguintes artigos:

* [Ntdsutil.exe não pode definir uma senha fraca do Modo de Reparo de Serviços de Diretório](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [A promoção da réplica do controlador de domínio falha devido a uma senha fraca do Modo de Reparo de Serviços de Diretório](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [O rebaixamento do controlador de domínio falha devido a uma senha de Administrador local fraca](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Depois de executar o recurso no modo de auditoria por um período razoável, alterne a configuração de *Auditar* para *Impor* para exigir senhas mais seguras. O monitoramento adicional nesse período é uma boa ideia.

É importante observar que a Proteção de Senha do Azure AD pode validar senhas apenas durante operações de alteração e de definição de senhas. As senhas aceitas e armazenadas no Active Directory antes da implantação da Proteção de Senha do Azure AD nunca serão validadas e continuarão funcionando da maneira em que se encontram. Com o passar do tempo, à medida que as senhas existentes expiram normalmente, todos os usuários e contas passam a usar senhas validadas pela Proteção de Senha do Azure AD. As contas configuradas com "a senha nunca expira" não passam por isso.

### <a name="multiple-forest-considerations"></a>Considerações sobre várias florestas

Não há requisitos adicionais para implantar a Proteção de Senha do Azure AD em várias florestas.

Cada floresta é configurada de modo independente, conforme descrito na seção a seguir sobre [implantar a Proteção de Senha do Azure AD local](#download-required-software). Cada proxy de proteção de senha do Azure AD dá suporte apenas aos controladores de domínio da floresta à qual está associado.

O software de proteção de senha do Azure AD que está em uma determinada floresta não tem conhecimento de outros softwares do tipo implantados em outras florestas, independentemente das configurações de confiança do Active Directory.

### <a name="read-only-domain-controller-considerations"></a>Considerações sobre o controlador de domínio somente leitura

Os eventos de alteração ou definição de senha não são processados e persistidos em RODCs (controladores de domínio somente leitura). Eles são encaminhados para controladores de domínio graváveis. Você não precisa instalar o software do agente de DC de proteção de senha do Azure AD nos RODCs.

Além disso, não há suporte para executar o serviço de proxy de proteção de senha do Azure AD em um controlador desse tipo.

### <a name="high-availability-considerations"></a>Considerações sobre alta disponibilidade

A principal preocupação quanto à proteção de senha é a disponibilidade dos servidores proxy de proteção de senha do Azure AD quando os DCs em uma floresta tentam baixar novas políticas ou outros dados do Azure. Cada agente de DC de proteção de senha do Azure AD usa um algoritmo simples de estilo round robin para decidir qual servidor proxy deve ser chamado. O agente ignora os servidores proxy que não estão respondendo.

Para a maioria das implantações totalmente conectadas do Active Directory com replicação íntegra do estado da pasta sysvol e do diretório, dois servidores proxy de proteção de senha do Azure AD são suficientes para garantir a disponibilidade. Essa configuração resulta no download oportuno de novas políticas e outros dados. Você pode implantar servidores proxy adicionais de proteção de senha do Azure AD, caso desejado.

O design do software do agente de DC de proteção de senha do Azure AD reduz os problemas comuns associados à alta disponibilidade. O agente de DC mantém um cache local da política de senha baixada mais recentemente. Mesmo se todos os servidores proxy registrados ficarem indisponíveis, os agentes de DC de proteção de senha do Azure AD continuarão impondo sua política de senha armazenada em cache.

Uma frequência de atualização razoável para políticas de senha em uma implantação grande geralmente é da ordem de dias, não de horas ou menos. Portanto, pequenas interrupções dos servidores proxy não afetam significativamente a proteção de senha do Azure AD.

## <a name="deployment-requirements"></a>Requisitos para implantação

Para saber mais sobre o licenciamento, confira [Requisitos de licenciamento da Proteção de Senha do Azure AD](concept-password-ban-bad.md#license-requirements).

Os seguintes requisitos básicos se aplicam:

* Todos os computadores, incluindo os controladores de domínio, em que componentes da Proteção de Senha do Azure AD estão instalados precisam ter o Runtime Universal C instalado.
    * Para obter o runtime, verifique se você tem todas as atualizações do Windows Update. Ou você pode obtê-lo em um pacote de atualização específico do sistema operacional. Para saber mais, confira [Atualização do Runtime Universal C no Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* É necessária uma conta com privilégios de administrador de domínio do Active Directory no domínio raiz da floresta para registrar a floresta do Windows Server Active Directory no Azure AD.
* O Serviço de Distribuição de Chave precisa estar habilitado em todos os controladores de domínio que executam o Windows Server 2012 e versões posteriores. Por padrão, este serviço é habilitado através do acionamento manual.

* Deve haver conectividade de rede entre pelo menos um controlador de domínio em cada domínio e pelo menos um servidor que hospede o serviço de proxy de proteção de senha do Azure AD. Essa conectividade deve permitir que o controlador de domínio acesse a porta de mapeador de ponto de extremidade RPC (135) e a porta do servidor RPC no serviço de proxy.
    * A porta do servidor RPC é, por padrão, uma porta RPC dinâmica, mas é possível configurá-la para [usar uma porta estática](#static).
* Todos os computadores em que o serviço de proxy da Proteção de Senha do Azure AD será instalado precisam ter acesso de rede aos seguintes pontos de extremidade:

    |**Ponto de extremidade**|**Finalidade**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Solicitações de autenticação|
    |`https://enterpriseregistration.windows.net`|Funcionalidade de Proteção de Senha do Azure AD|

### <a name="azure-ad-password-protection-dc-agent"></a>Agente de DC de proteção de senha do Azure AD

Os seguintes requisitos se aplicam ao agente de DC de proteção de senha do Azure AD:

* Todos os computadores em que o software do agente de DC da Proteção de Senha do Azure AD será instalado devem executar o Windows Server 2012 ou posterior, incluindo as edições do Windows Server Core.
    * O domínio/floresta do Active Directory não precisa estar no DFL (nível funcional de domínio) ou no FFL (nível funcional de floresta) do Windows Server 2012. Conforme mencionado nos [Princípios de design](concept-password-ban-bad-on-premises.md#design-principles), não é necessário um DFL nem um FFL mínimo para a execução do agente de DC ou do software de proxy.
* Todos os computadores em que o serviço de proxy da Proteção de Senha do Azure AD será instalado precisam ter o .NET 4.7.2.
    * Se ele ainda não estiver instalado, baixe e execute o instalador encontrado em [instalador offline do .NET Framework 4.7.2 para Windows](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2).
* Qualquer domínio do Active Directory que execute o serviço de agente de DC de proteção de senha do Azure AD deve usar a DFSR (replicação de sistema de arquivos distribuído) para a replicação da pasta sysvol.
   * Se o seu domínio ainda não estiver usando a DFSR, migre antes de instalar a Proteção de Senha do Azure AD. Para obter mais informações, confira [Guia de Migração de Replicação SYSVOL: Replicação FRS para DFS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

    > [!WARNING]
    > Atualmente, o software do agente de DC da Proteção de Senha do Azure AD será instalado nos controladores de domínio dos domínios que ainda usam o FRS (a tecnologia anterior ao DFSR) para replicação de SYSVOL, mas o software NÃO funcionará corretamente nesse ambiente.
    >
    > Outros efeitos colaterais negativos incluem a falha na replicação de arquivos individuais e procedimentos de restauração de SYSVOL que parecem ter êxito, mas cuja replicação de todos os arquivos falha silenciosamente.
    >
    > Migre seu domínio para usar o DFSR o quanto antes, tanto pelos benefícios inerentes do DFSR quanto para desbloquear a implantação da Proteção de Senha do Azure AD. Versões futuras do software serão desabilitadas automaticamente quando executadas em um domínio que ainda usa o FRS.

### <a name="azure-ad-password-protection-proxy-service"></a>Serviço de proxy de proteção de senha do Azure AD

Os seguintes requisitos se aplicam ao serviço de proxy de proteção de senha do Azure AD:

* Todos os computadores em que o serviço de proxy da Proteção de Senha do Azure AD será instalado devem executar o Windows Server 2012 R2 ou posterior, incluindo as edições do Windows Server Core.

    > [!NOTE]
    > A implantação do serviço de proxy de proteção de senha do Azure AD é obrigatória para implantar a proteção de senha do Azure AD, embora o controlador de domínio possa ter conectividade de Internet de saída direta.

* Todos os computadores em que o serviço de proxy da Proteção de Senha do Azure AD será instalado precisam ter o .NET 4.7.2.
    * Se ele ainda não estiver instalado, baixe e execute o instalador encontrado em [instalador offline do .NET Framework 4.7.2 para Windows](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2).
* Todos os computadores que hospedam o serviço de proxy de proteção de senha do Azure AD devem ser configurados para conceder aos controladores de domínio a capacidade de fazer logon no serviço de proxy. Essa capacidade é controlada pela atribuição do privilégio "Acessar este computador pela rede".
* Todos os computadores que hospedam o serviço de proxy de proteção de senha do Azure AD devem ser configurados para permitir o tráfego HTTP de saída do TLS 1.2.
* Uma conta de *Administrador global* ou *Administrador de segurança* para registrar no Azure AD a floresta e o serviço de proxy da Proteção de Senha do Azure AD.
* O acesso à rede deve ser habilitado para o conjunto de portas e URLs especificadas nos [procedimentos de configuração do ambiente de proxy de aplicativo](../manage-apps/application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>Pré-requisitos do Atualizador de Agente do Microsoft Azure AD Connect

O serviço Atualizador de Agente do Microsoft Azure AD Connect é instalado com o serviço de proxy da Proteção de Senha do Azure AD. É necessária configuração adicional para que o serviço Atualizador de Agente do Microsoft Azure AD Connect funcione:

* Se o ambiente usa um servidor proxy HTTP, siga as diretrizes especificadas em [Trabalhar com servidores proxy locais existentes](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md).
* O serviço Atualizador de Agente do Microsoft Azure AD Connect também requer as etapas referentes ao TLS 1.2 especificadas nos [Requisitos de TLS](../manage-apps/application-proxy-add-on-premises-application.md#tls-requirements).

> [!WARNING]
> Como o proxy de proteção de senha do Azure AD e o proxy de aplicativo do Azure AD instalam versões diferentes do serviço Atualizador de Agente do Microsoft Azure AD Connect, as instruções se referem ao conteúdo do proxy de aplicativo. Essas versões diferentes são incompatíveis quando instaladas lado a lado, e fazer isso impedirá que o serviço Atualizador de Agente entre em contato com o Azure para obter atualizações de software, portanto, nunca instale o proxy da Proteção de Senha e o Proxy de Aplicativo do Azure AD no mesmo computador.

## <a name="download-required-software"></a>Baixar o software necessário

Dois instaladores são necessários para uma implantação local da Proteção de Senha do Azure AD:

* Agente de DC de proteção de senha do Azure AD (*AzureADPasswordProtectionDCAgentSetup.msi*)
* Proxy de proteção de senha do Azure AD (*AzureADPasswordProtectionProxySetup.exe*)

Baixe os dois instaladores do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="install-and-configure-the-proxy-service"></a>Instalar e configurar o serviço de proxy

O serviço de proxy de proteção de senha do Azure AD normalmente está em um servidor membro de seu ambiente local do AD DS. Uma vez instalado, o serviço de proxy de proteção de senha do Azure AD se comunica com o Azure AD para manter uma cópia das listas globais e do cliente de senhas proibidas para o locatário do Azure AD.

Na próxima seção, você instala os agentes de DC da Proteção de Senha do Azure AD em controladores de domínio no ambiente local do AD DS. Esses agentes de DC se comunicam com o serviço de proxy para obter as listas mais recentes de senhas banidas para usá-las ao processar eventos de alteração de senha no domínio.

Escolha um ou mais servidores para hospedar o serviço de proxy da Proteção de Senha do Azure AD. As seguintes considerações se aplicam aos servidores:

* Cada serviço desse tipo pode fornecer políticas de senha para somente uma floresta. O computador host precisa estar conectado a todos os domínios nessa floresta.
* Você pode instalar o serviço de proxy em domínios raiz ou filho ou em uma combinação deles.
* Você precisa ter conectividade de rede entre pelo menos um DC em cada domínio da floresta e um servidor proxy de proteção de senha.
* Você pode executar o serviço de proxy da Proteção de Senha do Azure AD em um controlador de domínio para teste, mas esse controlador de domínio precisará de conectividade com a Internet. Essa conectividade pode gerar preocupações com a segurança. Recomendamos essa configuração somente para testes.
* Recomendamos pelo menos dois servidores proxy da Proteção por Senha do Azure AD por floresta para fins de redundância, conforme observado na seção anterior de [considerações sobre alta disponibilidade](#high-availability-considerations).
* Não há suporte para executar o serviço de proxy da Proteção de Senha do Azure AD em um controlador de domínio somente leitura.

Para instalar o serviço de proxy da Proteção de Senha do Azure AD, conclua as seguintes etapas:

1. Para instalar o serviço de proxy da Proteção de Senha do Azure AD, execute o instalador de software `AzureADPasswordProtectionProxySetup.exe`.

    A instalação do software não requer reinicialização e pode ser automatizada usando procedimentos padrão de MSI, conforme o seguinte exemplo:
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > O serviço de Firewall do Windows precisa estar em execução antes da instalação do pacote `AzureADPasswordProtectionProxySetup.exe` para evitar um erro de instalação.
    >
    > Se o Firewall do Windows estiver configurado para não ser executado, a solução alternativa será habilitar temporariamente e executar o serviço de Firewall durante a instalação. O software de proxy não tem dependência específica do Firewall do Windows após a instalação.
    >
    > Se você estiver usando um firewall de terceiros, ele ainda precisará ser configurado de maneira a satisfazer os requisitos de implantação. Isso inclui permitir o acesso de entrada à porta 135 e à porta do servidor RPC do proxy. Para saber mais, confira a seção anterior de [requisitos de implantação](#deployment-requirements).

1. O software de proxy da Proteção de Senha do Azure AD inclui um novo módulo do PowerShell, `AzureADPasswordProtection`. As etapas a seguir executam vários cmdlets desse módulo do PowerShell.

    Para usá-lo, abra uma janela do PowerShell como administrador e importe o novo módulo da seguinte maneira:
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. Para verificar se o serviço de proxy da Proteção de Senha do Azure AD está em execução, use o seguinte comando do PowerShell:

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    O resultado deve mostrar o **Status** *Executando*.

1. O serviço de proxy está em execução no computador, mas não tem credenciais para se comunicar com o Azure AD. Registre o servidor proxy da Proteção de Senha do Azure AD com o Azure AD usando o cmdlet `Register-AzureADPasswordProtectionProxy`.

    Esse cmdlet requer credenciais de *Administrador global* ou *Administrador de segurança* para o locatário do Azure. Ele também precisa ser executado usando uma conta com privilégios de administrador local.

    Após esse comando ser executado com êxito uma vez, invocações adicionais também serão terão êxito, mas serão desnecessárias.

    O cmdlet `Register-AzureADPasswordProtectionProxy` dá suporte aos três modos de autenticação a seguir. Os dois primeiros modos dão suporte à Autenticação Multifator do Azure AD, enquanto o terceiro não.

    > [!TIP]
    > Poderá haver um atraso perceptível antes da conclusão na primeira vez em que esse cmdlet for executado para um locatário específico do Azure. A menos que uma falha seja relatada, não se preocupe com esse atraso.

     * Modo de autenticação interativo:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Esse modo não funciona em sistemas operacionais Server Core. Em vez disso, use um dos modos de autenticação a seguir. Além disso, esse modo poderá falhar se a Configuração de Segurança Aprimorada do Internet Explorer estiver habilitada. A solução alternativa é desabilitar essa Configuração, registrar o proxy e habilitá-la novamente.

     * Modo de autenticação de código de dispositivo:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Quando solicitado, siga o link para abrir um navegador da Web e insira o código de autenticação.

     * Modo de autenticação silenciosa (baseada em senha):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Esse modo falhará se a Autenticação Multifator do Azure AD for necessária para sua conta. Nesse caso, use um dos dois modos de autenticação anteriores ou use uma conta diferente que não exija MFA.
        >
        > A MFA também poderá ser exigida se o Registro de Dispositivo do Azure (que é usado nos bastidores pela Proteção de Senha do Azure AD) tiver sido configurado para exigir a MFA globalmente. Para contornar esse requisito, você pode usar uma conta diferente com suporte para a MFA com um dos dois modos de autenticação anteriores ou pode relaxar temporariamente o requisito de MFA do Registro de Dispositivo do Azure.
        >
        > Para fazer essa alteração, procure e selecione **Azure Active Directory** no portal do Azure e selecione **Dispositivos > Configurações do Dispositivo**. Defina **Exigir Autenticação Multifator para ingressar dispositivos** como *Não*. Não deixe de reverter essa configuração para *Sim* quando o registro for concluído.
        >
        > Recomendamos que os requisitos de MFA sejam ignorados apenas para fins de teste.

    Atualmente, não é necessário especificar o parâmetro *-ForestCredential*, que é reservado para funcionalidade futura.

    O registro do serviço de proxy da Proteção de Senha do Azure AD é necessário somente uma vez durante o tempo de vida do serviço. Depois disso, o serviço de proxy da Proteção de Senha do Azure AD executará automaticamente qualquer outra manutenção necessária.

1. Agora, registre a floresta do Active Directory local com as credenciais necessárias para se comunicar com o Azure usando o cmdlet do PowerShell `Register-AzureADPasswordProtectionForest`.

    > [!NOTE]
    > Se vários servidores proxy da Proteção de Senha do Azure AD estiverem instalados no ambiente, não fará diferença qual servidor proxy é usado para registrar a floresta.

    O cmdlet requer credenciais de *Administrador global* ou *Administrador de segurança* para o locatário do Azure. Ele também requer privilégios de Administrador corporativo do Active Directory local. Além disso, você precisa executar o cmdlet usando uma conta com privilégios de administrador local. A conta do Azure usada para registrar a floresta pode ser diferente da conta do Active Directory local.
    
    Esta etapa é executada uma vez por floresta.

    O cmdlet `Register-AzureADPasswordProtectionForest` dá suporte aos três modos de autenticação a seguir. Os dois primeiros modos dão suporte à Autenticação Multifator do Azure AD, enquanto o terceiro não.

    > [!TIP]
    > Poderá haver um atraso perceptível antes da conclusão na primeira vez em que esse cmdlet for executado para um locatário específico do Azure. A menos que uma falha seja relatada, não se preocupe com esse atraso.

     * Modo de autenticação interativo:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Esse modo não funcionará em sistemas operacionais Server Core. Em vez disso, use um dos dois modos de autenticação a seguir. Além disso, esse modo poderá falhar se a Configuração de Segurança Aprimorada do Internet Explorer estiver habilitada. A solução alternativa é desabilitar essa Configuração, registrar a floresta e habilitá-la novamente.  

     * Modo de autenticação de código de dispositivo:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Quando solicitado, siga o link para abrir um navegador da Web e insira o código de autenticação.

     * Modo de autenticação silenciosa (baseada em senha):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Esse modo falhará se a Autenticação Multifator do Azure AD for necessária para sua conta. Nesse caso, use um dos dois modos de autenticação anteriores ou use uma conta diferente que não exija MFA.
        >
        > A MFA também poderá ser exigida se o Registro de Dispositivo do Azure (que é usado nos bastidores pela Proteção de Senha do Azure AD) tiver sido configurado para exigir a MFA globalmente. Para contornar esse requisito, você pode usar uma conta diferente com suporte para a MFA com um dos dois modos de autenticação anteriores ou pode relaxar temporariamente o requisito de MFA do Registro de Dispositivo do Azure.
        >
        > Para fazer essa alteração, procure e selecione **Azure Active Directory** no portal do Azure e selecione **Dispositivos > Configurações do Dispositivo**. Defina **Exigir Autenticação Multifator para ingressar dispositivos** como *Não*. Não deixe de reverter essa configuração para *Sim* quando o registro for concluído.
        >
        > Recomendamos que os requisitos de MFA sejam ignorados apenas para fins de teste.

       Esses exemplos terão sucesso apenas se o usuário conectado no momento também for administrador de domínio do Active Directory para o domínio raiz. Se esse não for o caso, você poderá fornecer credenciais de domínio alternativas por meio do parâmetro *-ForestCredential*.

    O registro da floresta do Active Directory é necessário somente uma ver durante o tempo de vida da floresta. Depois disso, os agentes de DC da Proteção de Senha do Azure AD na floresta executarão automaticamente qualquer outra manutenção necessária. Após `Register-AzureADPasswordProtectionForest` ser executado com êxito para uma floresta, invocações adicionais do cmdlet são bem-sucedidas, mas são desnecessárias.
    
    Para que `Register-AzureADPasswordProtectionForest` tenha sucesso, pelo menos um DC que execute o Windows Server 2012 ou posterior deverá estar disponível no domínio do servidor proxy da Proteção de Senha do Azure AD. O software do agente de DC da Proteção de Senha do Azure AD não precisa ser instalado em nenhum controlador de domínio antes desta etapa.

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>Configurar o serviço de proxy para comunicação por meio de um proxy HTTP

Se o ambiente exigir o uso de um proxy HTTP específico para comunicação com o Azure, use as etapas a seguir para configurar o serviço de Proteção de Senha do Azure AD.

Crie um arquivo *AzureADPasswordProtectionProxy.exe.config* na pasta `%ProgramFiles%\Azure AD Password Protection Proxy\Service`. Inclua o seguinte conteúdo:

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

Se o proxy HTTP exigir autenticação, adicione a marca *useDefaultCredentials*:

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

Nos dois casos, substitua `http://yourhttpproxy.com:8080` pelo endereço e pela porta do servidor proxy HTTP específico.

Se o proxy HTTP estiver configurado para usar uma política de autorização, permita acesso à conta do computador do Active Directory que hospeda o serviço de proxy da proteção de senha.

Recomendamos que você pare e reinicie o serviço de proxy da Proteção de Senha do Azure AD após criar ou atualizar o arquivo *AzureADPasswordProtectionProxy.exe.config*.

O serviço de proxy não dá suporte ao uso de credenciais específicas para se conectar a um proxy HTTP.

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>Configurar o serviço de proxy para escutar uma porta específica

O software do agente de DC da Proteção de Senha do Azure AD usa RPC sobre TCP para se comunicar com o serviço de proxy. Por padrão, o serviço de proxy da Proteção de Senha do Azure AD escuta qualquer ponto de extremidade RPC dinâmico disponível. Você poderá configurar o serviço para escutar uma porta TCP específica caso seja necessário devido à topologia de rede ou aos requisitos de firewall de seu ambiente.

<a id="static" /></a>Para configurar o serviço para execução em uma porta estática, use o cmdlet `Set-AzureADPasswordProtectionProxyConfiguration` da seguinte maneira:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> Você deve parar e reiniciar o serviço de proxy da Proteção de Senha do Azure AD para que essas alterações entrem em vigor.

Para configurar o serviço para ser executado em uma porta dinâmica, use o mesmo procedimento, mas configure *StaticPort* de volta como zero:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> Você deve parar e reiniciar o serviço de proxy da Proteção de Senha do Azure AD para que essas alterações entrem em vigor.

O serviço de proxy da Proteção de Senha do Azure AD requer uma reinicialização manual após qualquer alteração na configuração da porta. Você não precisa reiniciar o serviço do agente de DC da Proteção de Senha do Azure AD em controladores de domínio depois de fazer essas alterações de configuração.

Para consultar a configuração atual do serviço, use o cmdlet `Get-AzureADPasswordProtectionProxyConfiguration` conforme mostrado no exemplo a seguir

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

A saída do seguinte exemplo mostra que o serviço de proxy da Proteção de Senha do Azure AD está usando uma porta dinâmica:

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>Instalar o serviço de agente de DC

Instale o serviço de agente de DC de proteção de senha do Azure AD, execute o pacote `AzureADPasswordProtectionDCAgentSetup.msi`.

Você pode automatizar a instalação do software usando procedimentos MSI padrão, conforme mostrado no exemplo a seguir:

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

Omita o sinalizador `/norestart` se preferir que o instalador reinicialize o computador automaticamente.

A instalação ou desinstalação do software requer uma reinicialização. Isso é necessário porque as DLLs de filtro de senha são carregadas ou descarregadas apenas por uma reinicialização.

A instalação da Proteção de Senha do Azure AD local é concluída após o software do agente de DC ser instalado em um controlador de domínio e o computador ser reinicializado. Nenhuma outra configuração é necessária ou possível. Eventos de alteração de senha nos DCs locais usam as listas de senhas proibidas configuradas do Azure AD.

Para habilitar a Proteção de Senha do Azure AD local no portal do Azure ou configurar senhas proibidas personalizadas, confira [Habilitar a Proteção de Senha do Azure AD local](howto-password-ban-bad-on-premises-operations.md).

> [!TIP]
> Você pode instalar o agente de DC de proteção de senha do Azure AD em um computador que ainda não seja um controlador de domínio. Nesse caso, o serviço é iniciado e executado, mas permanece inativo até que o computador seja promovido a controlador de domínio.

## <a name="upgrading-the-proxy-service"></a>Atualizando o serviço de proxy

O serviço de proxy de proteção de senha do Azure AD dá suporte à atualização automática. Esse tipo de atualização usa o serviço Atualizador de Agente do Microsoft Azure AD Connect, que é instalado com o serviço de proxy. A atualização automática fica ativada por padrão e pode ser habilitada ou desabilitada usando o cmdlet `Set-AzureADPasswordProtectionProxyConfiguration`.

A configuração atual pode ser consultada usando o cmdlet `Get-AzureADPasswordProtectionProxyConfiguration`. Recomenda-se manter a configuração de atualização automática sempre habilitada.

O cmdlet `Get-AzureADPasswordProtectionProxy` pode ser usado para consultar a versão de software de todos os servidores proxy de proteção de senha do Azure AD atualmente instalados em uma floresta.

### <a name="manual-upgrade-process"></a>Processo de atualização manual

Uma atualização manual é realizada executando a versão mais recente do instalador de software `AzureADPasswordProtectionProxySetup.exe`. Essa versão está disponível no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

Não é necessário desinstalar a versão atual do serviço de proxy da Proteção de Senha do Azure AD, pois o instalador executa uma atualização in-loco. A reinicialização também não é necessária ao atualizar o serviço de proxy. A atualização do software pode ser automatizada usando procedimentos MSI padrão, como `AzureADPasswordProtectionProxySetup.exe /quiet`.

## <a name="upgrading-the-dc-agent"></a>Atualizando o agente de DC

Quando houver uma versão mais recente do software do agente de DC de proteção de senha do Azure AD disponível, realize a atualização executando a versão mais recente do pacote de software `AzureADPasswordProtectionDCAgentSetup.msi`. Essa versão está disponível no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

Não é necessário desinstalar a versão atual do software do agente de DC, pois o instalador executa uma atualização in-loco. Sempre é necessária uma reinicialização ao atualizar o software do agente de DC. Esse requisito é decorrente do comportamento central do Windows.

A atualização do software pode ser automatizada usando procedimentos MSI padrão, como `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`.

Omita o sinalizador `/norestart` se quiser que o instalador reinicialize o computador automaticamente.

O cmdlet `Get-AzureADPasswordProtectionDCAgent` pode ser usado para consultar a versão de software de todos os agentes de DC de proteção de senha do Azure AD atualmente instalados em uma floresta.

## <a name="next-steps"></a>Próximas etapas

Agora que você instalou os serviços necessários para a Proteção de Senha do Azure AD nos servidores locais, [habilite a Proteção de Senha do Azure AD local no portal do Azure](howto-password-ban-bad-on-premises-operations.md) para concluir a implantação.
