---
title: Usar a autenticação multifator do Azure AD com o NPS-Azure Active Directory
description: Saiba como usar os recursos de autenticação multifator do Azure AD com sua infraestrutura de autenticação do servidor de políticas de rede (NPS) existente
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/31/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 484dd8313710332660bb20d55f3dac2aa21bbc61
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98232515"
---
# <a name="integrate-your-existing-network-policy-server-nps-infrastructure-with-azure-ad-multi-factor-authentication"></a>Integre sua infraestrutura existente de NPS (servidor de políticas de rede) com a autenticação multifator do Azure AD

A extensão do NPS (servidor de políticas de rede) para a autenticação multifator do Azure AD adiciona recursos de MFA baseados em nuvem à sua infraestrutura de autenticação usando seus servidores existentes. Com a extensão do NPS, você pode adicionar verificação por chamada telefônica, mensagem de texto ou aplicativo ao fluxo de autenticação existente sem a necessidade de instalar, configurar e manter novos servidores.

A extensão do NPS atua como um adaptador entre o RADIUS e a autenticação multifator do Azure AD baseada em nuvem para fornecer um segundo fator de autenticação para usuários federados ou sincronizados.

## <a name="how-the-nps-extension-works"></a>Como funciona a extensão do NPS

Quando você usa a extensão NPS para a autenticação multifator do Azure AD, o fluxo de autenticação inclui os seguintes componentes:

1. **Servidor VPN/NAS** recebe solicitações de clientes VPN e converte-os em solicitações RADIUS para servidores NPS.
2. O **servidor NPS** se conecta a Active Directory Domain Services (AD DS) para executar a autenticação primária para as solicitações RADIUS e, após o êxito, passa a solicitação para todas as extensões instaladas.  
3. A **extensão do NPS** dispara uma solicitação para a autenticação multifator do Azure ad para a autenticação secundária. Quando a extensão receber a resposta, e se o desafio de MFA for bem-sucedido, ela concluirá a solicitação de autenticação, fornecendo ao servidor NPS os tokens de segurança que incluem uma declaração MFA, emitida pelo STS do Azure.
4. O **MFA do Azure ad** se comunica com o Azure Active Directory (Azure AD) para recuperar os detalhes do usuário e executa a autenticação secundária usando um método de verificação configurado para o usuário.

O diagrama a seguir ilustra esse fluxo de solicitação de autenticação de alto nível:

![Diagrama do fluxo de autenticação para autenticação de usuário por meio de um servidor VPN para o servidor NPS e a extensão NPS da autenticação multifator do Azure AD](./media/howto-mfa-nps-extension/auth-flow.png)

### <a name="radius-protocol-behavior-and-the-nps-extension"></a>Comportamento do protocolo RADIUS e a extensão do NPS

Como o RADIUS é um protocolo UDP, o remetente assume a perda de pacotes e aguarda uma resposta. Após um período de tempo, a conexão pode atingir o tempo limite. Nesse caso, o pacote é reenviado, pois o remetente assume que o pacote não alcançou o destino. No cenário de autenticação neste artigo, os servidores VPN enviam a solicitação e aguardam uma resposta. Se a conexão atingir o tempo limite, o servidor VPN enviará a solicitação novamente.

![Diagrama de fluxo de pacotes UDP RADIUS e solicitações após o tempo limite de resposta do servidor NPS](./media/howto-mfa-nps-extension/radius-flow.png)

O servidor NPS pode não responder à solicitação original do servidor VPN antes que a conexão expire, pois a solicitação de MFA ainda pode estar sendo processada. O usuário pode não ter respondido com êxito ao prompt do MFA, portanto, a extensão do NPS da autenticação multifator do Azure AD está aguardando a conclusão desse evento. Nessa situação, o servidor NPS identifica solicitações de servidor VPN adicionais como uma solicitação duplicada. O servidor NPS descarta essas solicitações duplicadas do servidor VPN.

![Diagrama do servidor NPS descartando solicitações duplicadas do servidor RADIUS](./media/howto-mfa-nps-extension/discard-duplicate-requests.png)

Se você examinar os logs do servidor NPS, poderá ver essas solicitações adicionais sendo descartadas. Esse comportamento é por design para proteger o usuário final de obter várias solicitações por uma única tentativa de autenticação. As solicitações descartadas no log de eventos do servidor NPS não indicam que há um problema com o servidor NPS ou com a extensão NPS da autenticação multifator do Azure AD.

Para minimizar as solicitações descartadas, recomendamos que os servidores VPN sejam configurados com um tempo limite de pelo menos 60 segundos. Se necessário, ou para reduzir solicitações descartadas nos logs de eventos, você pode aumentar o valor de tempo limite do servidor VPN para 90 ou 120 segundos.

Devido a esse comportamento de protocolo UDP, o servidor NPS pode receber uma solicitação duplicada e enviar outro prompt de MFA, mesmo depois que o usuário já tiver respondido à solicitação inicial. Para evitar essa condição de temporização, a extensão NPS da autenticação multifator do Azure AD continua a filtrar e descartar solicitações duplicadas por até 10 segundos depois que uma resposta bem-sucedida foi enviada para o servidor VPN.

![O diagrama do servidor NPS continua a descartar solicitações duplicadas do servidor VPN por dez segundos depois que uma resposta bem-sucedida é retornada](./media/howto-mfa-nps-extension/delay-after-successful-authentication.png)

Novamente, você poderá ver solicitações descartadas nos logs de eventos do servidor NPS, mesmo quando o prompt da autenticação multifator do Azure AD foi bem-sucedido. Esse é o comportamento esperado e não indica um problema com o servidor NPS ou com a extensão NPS da autenticação multifator do Azure AD.

## <a name="plan-your-deployment"></a>Planejar sua implantação

A extensão NPS controla automaticamente a redundância, de maneira que você não precisa de uma configuração especial.

Você pode criar tantos servidores NPS habilitados para autenticação multifator do Azure AD quanto necessário. Se você instalar vários servidores, use um certificado de cliente diferente para cada um deles. A criação de um certificado para cada servidor significa que você pode atualizar cada certificado individualmente e não se preocupe com o tempo de inatividade em todos os servidores.

Os servidores VPN roteiam solicitações de autenticação, portanto, precisam estar cientes dos novos servidores NPS habilitados para autenticação multifator do Azure AD.

## <a name="prerequisites"></a>Pré-requisitos

A extensão do NPS deve trabalhar com sua infraestrutura existente. Verifique se você cumpre os seguintes pré-requisitos antes de iniciar.

### <a name="licenses"></a>Licenças

A extensão do NPS para a autenticação multifator do Azure AD está disponível para clientes com [licenças para a autenticação multifator do Azure ad](./concept-mfa-howitworks.md). As licenças baseadas em consumo para a autenticação multifator do Azure AD, como licenças por usuário ou por autenticação, não são compatíveis com a extensão do NPS.

### <a name="software"></a>Software

Windows Server 2012 ou superior.

### <a name="libraries"></a>Bibliotecas

Você precisa instalar manualmente a biblioteca a seguir:

- [Pacotes Redistribuíveis do Visual C++ para Visual Studio 2015](https://www.microsoft.com/download/details.aspx?id=48145)

As bibliotecas a seguir são instaladas automaticamente com a extensão.

- [Pacotes redistribuíveis do Visual C++ para Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Módulo Microsoft Azure Active Directory para Windows PowerShell versão 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

O Módulo Microsoft Azure Active Directory para Windows PowerShell também é instalado por meio de um script de configuração que você executa como parte do processo de instalação, se ainda não estiver presente. Não há necessidade de instalar esse módulo antecipadamente se ele ainda não estiver instalado.

### <a name="azure-active-directory"></a>Azure Active Directory

Todos que usam a extensão NPS devem ser sincronizados com o Azure AD usando Azure AD Connect e devem ser registrados para MFA.

Ao instalar a extensão, você precisará da *ID do locatário* e das credenciais de administrador para seu locatário do Azure AD. Para obter a ID do locatário, conclua as seguintes etapas:

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador global do locatário do Azure.
1. Procure e selecione o **Azure Active Directory**.
1. Na página **visão geral** , as *informações do locatário* são mostradas. Ao lado da *ID do locatário*, selecione o ícone de **cópia** , conforme mostrado no seguinte exemplo de captura de tela:

   ![Obtendo a ID do locatário do portal do Azure](./media/howto-mfa-nps-extension/azure-active-directory-tenant-id-portal.png)

### <a name="network-requirements"></a>Requisitos de rede

O servidor NPS deve ser capaz de se comunicar com as seguintes URLs nas portas 80 e 443:

* *https: \/ /adnotifications.WindowsAzure.com*
* *https: \/ /login.microsoftonline.com*
* *https: \/ /Credentials.Azure.com*

Além disso, a conectividade com as seguintes URLs é necessária para concluir a [configuração do adaptador usando o script do PowerShell fornecido](#run-the-powershell-script):

* *https: \/ /login.microsoftonline.com*
* *https: \/ /provisioningapi.microsoftonline.com*
* *https: \/ /aadcdn.msauth.net*
* *https: \/ /www.powershellgallery.com*
* *https: \/ /aadcdn.msftauthimages.net*

## <a name="prepare-your-environment"></a>Prepare o seu ambiente

Antes de instalar a extensão NPS, prepare seu ambiente para lidar com o tráfego de autenticação.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Habilitar a função NPS em um servidor ingressado no domínio

O servidor NPS se conecta ao Azure AD e autentica as solicitações de MFA. Escolha um servidor para essa função. É recomendável escolher um servidor que não manipule solicitações de outros serviços, porque a extensão NPS gerará erros para solicitações que não sejam RADIUS. O servidor NPS deve ser configurado como o servidor de autenticação primário e secundário para o seu ambiente. Ele não pode fazer proxy de solicitações RADIUS para outro servidor.

1. No servidor, abra **Gerenciador do servidor**. Selecione **Assistente de adição de funções e recursos** no menu *início rápido* .
2. Para o tipo de instalação, escolha instalação baseada em **função ou recurso**.
3. Selecione a função de servidor **Serviços de Acesso e Política de Rede**. Uma janela pode ser exibida para informá-lo sobre recursos adicionais necessários para executar essa função.
4. Continue no assistente até a página de *confirmação* . Quando estiver pronto, selecione **instalar**.

Pode levar alguns minutos para instalar a função de servidor NPS. Quando terminar, continue com as seções a seguir para configurar esse servidor para tratar as solicitações RADIUS de entrada da solução de VPN.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Configurar a solução VPN para se comunicar com o servidor NPS

Dependendo da solução VPN que você usa, as etapas para configurar a política de autenticação RADIUS variam. Configure sua política de VPN para apontar para o servidor NPS RADIUS.

### <a name="sync-domain-users-to-the-cloud"></a>Sincronizar usuários de domínio com a nuvem

Esta etapa pode já estar concluída no seu locatário, mas é bom verificar se Azure AD Connect sincronizou recentemente os seus bancos de dados.

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Selecione **Azure Active Directory** > **Azure AD Connect**
3. Verifique se o status de sincronização está **Habilitado** e se a última sincronização foi há menos de uma hora.

Se você precisar iniciar uma nova rodada de sincronização, consulte [Azure ad Connect sincronização: Agendador](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Determinar quais métodos de autenticação os usuários podem usar

Há dois fatores que afetam quais métodos de autenticação estão disponíveis com uma implantação de extensão do NPS:

* O algoritmo de criptografia de senha usado entre o cliente RADIUS (VPN, servidor Netscaler ou outros) e os servidores NPS.
   - O **PAP** dá suporte a todos os métodos de autenticação da autenticação multifator do Azure ad na nuvem: chamada telefônica, mensagem de texto unidirecional, notificação de aplicativo móvel, tokens de hardware OATH e código de verificação de aplicativo móvel.
   - **CHAPV2** e **EAP** dão suporte a chamada telefônica e notificação de aplicativo móvel.

    > [!NOTE]
    > Quando você implanta a extensão do NPS, use esses fatores para avaliar quais métodos estão disponíveis para os usuários. Se o cliente RADIUS dá suporte a PAP, mas a experiência do cliente não tem campos de entrada para um código de verificação, chamada telefônica e notificação do aplicativo móvel são as duas opções com suporte.
    >
    > Além disso, independentemente do protocolo de autenticação usado (PAP, CHAP ou EAP), se o método de MFA for baseado em texto (SMS, código de verificação de aplicativo móvel ou token de hardware OATH) e exigir que o usuário insira um código ou texto no campo de entrada da interface do usuário do cliente VPN, a autenticação poderá ter sucesso. *Mas* todos os atributos RADIUS configurados na política de acesso à rede *não* são encaminhados para o cient RADIUS (o dispositivo de acesso à rede, como o gateway de VPN). Como resultado, o cliente VPN pode ter mais acesso do que você deseja que ele tenha ou menos acesso ou sem acesso.

* Os métodos de entrada que o aplicativo cliente (VPN, servidor Netscaler ou outros) pode manipular. Por exemplo, o cliente VPN tem algum meio de permitir que o usuário digite um código de verificação de um texto ou aplicativo móvel?

Você pode [desabilitar métodos de autenticação sem suporte](howto-mfa-mfasettings.md#verification-methods) no Azure.

### <a name="register-users-for-mfa"></a>Registrar usuários para a MFA

Antes de implantar e usar a extensão NPS, os usuários necessários para executar a autenticação multifator do Azure AD precisam ser registrados para MFA. Para testar a extensão ao implantá-la, você também precisa de pelo menos uma conta de teste totalmente registrada para a autenticação multifator do Azure AD.

Se você precisar criar e configurar uma conta de teste, use as seguintes etapas:

1. Entre em [https://aka.ms/mfasetup](https://aka.ms/mfasetup) com uma conta de teste.
2. Siga os prompts para configurar um método de verificação.
3. Na portal do Azure como um usuário administrador, [crie uma política de acesso condicional](howto-mfa-getstarted.md#create-conditional-access-policy) para exigir a autenticação multifator para a conta de teste.

> [!IMPORTANT]
>
> Verifique se os usuários foram registrados com êxito para a autenticação multifator do Azure AD. Se os usuários tiverem sido registrados somente para SSPR (redefinição de senha por autoatendimento), *StrongAuthenticationMethods* será habilitado para a conta deles. A autenticação multifator do Azure AD é imposta quando o *StrongAuthenticationMethods* é configurado, mesmo que o usuário seja registrado apenas para SSPR.
>
> O registro de segurança combinado pode ser habilitado para configurar o SSPR e a autenticação multifator do Azure AD ao mesmo tempo. Para obter mais informações, confira [Habilitar o registro de informações de segurança combinadas no Azure Active Directory](howto-registration-mfa-sspr-combined.md).
>
> Você também poderá [forçar os usuários a registrar novamente os métodos de autenticação](howto-mfa-userdevicesettings.md#manage-user-authentication-options) se eles tiverem habilitado anteriormente apenas SSPR.

## <a name="install-the-nps-extension"></a>Instalar a extensão NPS

> [!IMPORTANT]
> Instale a extensão do NPS em um servidor diferente do ponto de acesso VPN.

### <a name="download-and-install-the-nps-extension-for-azure-ad-mfa"></a>Baixar e instalar a extensão NPS para MFA do Azure AD

Para baixar e instalar a extensão NPS, conclua as seguintes etapas:

1. [Baixe a extensão NPS](https://aka.ms/npsmfa) do Centro de Download da Microsoft.
1. Copie o binário para o Servidor de Políticas de Rede que você deseja configurar.
1. Execute o arquivo *setup.exe* e siga as instruções de instalação. Se você encontrar erros, certifique-se de que as [bibliotecas da seção de pré-requisitos](#libraries) foram instaladas com êxito.

#### <a name="upgrade-the-nps-extension"></a>Atualizar a extensão NPS

Se você atualizar posteriormente uma instalação de extensão de NPS existente, para evitar uma reinicialização do servidor subjacente, conclua as seguintes etapas:

1. Desinstale a versão existente.
1. Execute o novo instalador.
1. Reinicie o serviço do *servidor de diretivas de rede (IAS)* .

### <a name="run-the-powershell-script"></a>Executar o script do PowerShell

O instalador cria um script do PowerShell em `C:\Program Files\Microsoft\AzureMfa\Config` (onde `C:\` é a sua unidade de instalação). Esse script do PowerShell executa as seguintes ações cada vez que é executado:

* Cria um certificado autoassinado.
* Associa a chave pública do certificado à entidade de serviço no Azure AD.
* Armazena o certificado no repositório de certificados do computador local.
* Concede acesso à chave privada do certificado para o usuário de rede.
* Reinicia o serviço NPS.

A menos que você queira usar seus próprios certificados (em vez dos certificados autoassinados gerados pelo script do PowerShell), execute o script do PowerShell para concluir a instalação da extensão do NPS. Se você instalar a extensão em vários servidores, cada servidor deverá ter seu próprio certificado.

Para fornecer recursos de balanceamento de carga ou redundância, repita essas etapas em servidores NPS adicionais, conforme desejado:

1. Abra um prompt do Windows PowerShell como administrador.
1. Altere os diretórios para onde o instalador criou o script do PowerShell:

   ```powershell
   cd "C:\Program Files\Microsoft\AzureMfa\Config"
   ```

1. Execute o script do PowerShell criado pelo instalador.

   Talvez seja necessário primeiro habilitar o TLS 1,2 para que o PowerShell possa se conectar e baixar pacotes corretamente:
   
   `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12`

   > [!IMPORTANT]
   > Para clientes que usam as nuvens Azure governamental ou Azure China 21Vianet, primeiro edite os `Connect-MsolService` cmdlets no script *AzureMfaNpsExtnConfigSetup.ps1* para incluir os parâmetros de *AzureEnvironment* para a nuvem necessária. Por exemplo, especifique *-AzureEnvironment USGovernment* ou *-AzureEnvironment AzureChinaCloud*.
   >
   > Para obter mais informações, veja [referência de parâmetro Connect-MsolService](/powershell/module/msonline/connect-msolservice#parameters).

   ```powershell
   .\AzureMfaNpsExtnConfigSetup.ps1
   ```

1. Quando solicitado, entre no Azure AD como um administrador.
1. O PowerShell solicitará a sua ID de locatário. Use o GUID da *ID de locatário* que você copiou do portal do Azure na seção pré-requisitos.
1. Uma mensagem de êxito é mostrada quando o script é concluído.  

Se o certificado do computador anterior tiver expirado e um novo certificado tiver sido gerado, você deverá excluir todos os certificados expirados. Ter certificados expirados pode causar problemas com a inicialização da extensão do NPS.

> [!NOTE]
> Se você usar seus próprios certificados em vez de gerar certificados com o script do PowerShell, certifique-se de que eles estejam alinhados com a convenção de nomenclatura do NPS. O nome da entidade deve ser **CN = \<TenantID\> , ou = extensão NPS da Microsoft**.

### <a name="microsoft-azure-government-or-azure-china-21vianet-additional-steps"></a>Etapas adicionais do Microsoft Azure Governamental ou do Azure China 21Vianet

Para clientes que usam as nuvens Azure governamental ou Azure China 21Vianet, as etapas de configuração adicionais a seguir são necessárias em cada servidor NPS.

> [!IMPORTANT]
> Defina essas configurações do registro somente se você for um cliente do Azure governamental ou do Azure China 21Vianet.

1. Se você for um cliente do Azure governamental ou do Azure China 21Vianet, abra o **Editor do registro** no servidor NPS.
1. Navegue até `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa`.
1. Para clientes do Azure governamental, defina os seguintes valores de chave:

    | Chave do Registro       | Valor |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. Para clientes do Azure China 21Vianet, defina os seguintes valores de chave:

    | Chave do Registro       | Valor |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.cn   |
    | STS_URL            | https://login.chinacloudapi.cn/   |

1. Repita as duas etapas anteriores para definir os valores de chave do Registro para cada servidor NPS.
1. Reinicie o serviço NPS para cada servidor NPS.

    Para o mínimo de impacto, coloque cada servidor NPS fora da rotação NLB (um de cada vez) e aguarde a descarga de todas as conexões.

### <a name="certificate-rollover"></a>Sobreposição de certificado

Com a versão *1.0.1.32* da extensão do NPS, agora há suporte para ler vários certificados. Essa funcionalidade ajuda a facilitar as atualizações de certificados sem interrupção antes de sua expiração. Se sua organização estiver executando uma versão anterior da extensão do NPS, atualize para a versão *1.0.1.32* ou superior.

Os certificados criados pelo script `AzureMfaNpsExtnConfigSetup.ps1` são válidos por dois anos. Monitorar certificados para expiração. Os certificados para a extensão NPS são colocados no repositório de certificados do *computador local* em *pessoal* e são *emitidos para* a ID de locatário fornecida para o script de instalação.

Quando a data de validade de um certificado está se aproximando, outro certificado deve ser criado para substituí-lo.  Esse processo é realizado executando o `AzureMfaNpsExtnConfigSetup.ps1` novamente e mantendo a mesma ID de locatário quando solicitado. Esse processo deve ser repetido em cada servidor NPS em seu ambiente.

## <a name="configure-your-nps-extension"></a>Configurar sua extensão do NPS

Com seu ambiente preparado e a extensão NPS agora instalada nos servidores necessários, você pode configurar a extensão.

Esta seção inclui considerações sobre o design e sugestões para implantações bem-sucedidas da extensão do NPS.

### <a name="configuration-limitations"></a>Limitações de configuração

- A extensão do NPS para a autenticação multifator do Azure AD não inclui ferramentas para migrar usuários e configurações do servidor MFA para a nuvem. Por esse motivo, é aconselhável usar a extensão para novas implantações, em vez da implantação existente. Se você usar a extensão em uma implantação existente, os usuários terão que executar a verificação novamente para preencher os detalhes de MFA na nuvem.  
- A extensão do NPS usa o UPN do ambiente de AD DS local para identificar o usuário na autenticação multifator do Azure AD para executar a autenticação secundária. A extensão pode ser configurada para usar um identificador diferente, como a ID de logon alternativa ou o campo de AD DS personalizado diferente do UPN. Consulte [Opções de configuração avançadas da extensão NPS para a Autenticação Multifator](howto-mfa-nps-extension-advanced.md) para obter mais informações.
- Nem todos os protocolos de criptografia dão suporte a todos os métodos de verificação.
   - O **PAP** dá suporte a chamadas telefônicas, mensagens de texto unidirecionais, notificações de aplicativo móvel e códigos de verificação de aplicativo móvel
   - **CHAPV2** e **EAP** dão suporte a chamada telefônica e notificação do aplicativo móvel

### <a name="control-radius-clients-that-require-mfa"></a>Clientes RADIUS de controle que exigem MFA

Depois de habilitar a MFA para um cliente RADIUS usando a extensão NPS, todas as autenticações desse cliente serão necessárias para executar a MFA. Se você deseja habilitar a MFA para alguns clientes RADIUS, mas outros não, você pode configurar dois servidores NPS e instalar a extensão em apenas um deles.

Configure clientes RADIUS para os quais você deseja que a MFA envie solicitações ao servidor NPS configurado com a extensão e outros clientes RADIUS para o servidor NPS não configurado com a extensão.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Preparar usuários que não são registrados na MFA

Se você tiver usuários que não são registrados na MFA, determine o que acontece quando eles tentam fazer a autenticação. Para controlar esse comportamento, use a configuração *REQUIRE_USER_MATCH* no caminho do registro *HKLM\Software\Microsoft\AzureMFA*. Essa configuração tem uma opção de configuração única:

| Chave | Valor | Padrão |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE/FALSE | Não definido (equivalente a TRUE) |

Essa configuração determina o que fazer quando um usuário não é registrado para MFA. Quando a chave não existir, não estiver definida ou estiver definida como *true* e o usuário não estiver registrado, a extensão falhará no desafio de MFA.

Quando a chave é definida como *false* e o usuário não está registrado, a autenticação prossegue sem executar a MFA. Se um usuário estiver registrado no MFA, ele deverá se autenticar com o MFA, mesmo se *REQUIRE_USER_MATCH* estiver definido como *false*.

Você pode optar por criar essa chave e defini-la como *falsa* enquanto os usuários estão integrados e talvez nem todos sejam registrados para a autenticação multifator do Azure ad ainda. Porém, como definir a chave permite que os usuários que não são registrados na MFA se conectem, você deve remover essa chave antes de ir para a produção.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="nps-extension-health-check-script"></a>Script de verificação de integridade da extensão do NPS

O script a seguir está disponível para executar as etapas básicas de verificação de integridade ao solucionar problemas da extensão do NPS.

[MFA_NPS_Troubleshooter.ps1](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Como verificar se o certificado do cliente está instalado conforme o esperado?

Procure o certificado autoassinado criado pelo instalador no repositório de certificados e verifique se a chave privada tem permissões concedidas ao usuário *NETWORK SERVICE*. O certificado tem um nome de assunto de **CN \<tenantid\> , ou = extensão NPS da Microsoft**

Os certificados autoassinados gerados pelo `AzureMfaNpsExtnConfigSetup.ps1` script têm um tempo de vida útil de dois anos. Ao verificar se o certificado está instalado, você também deve verificar se o certificado não expirou.

### <a name="how-can-i-verify-that-my-client-certificate-is-associated-to-my-tenant-in-azure-ad"></a>Como verificar se meu certificado de cliente está associado ao meu locatário no Azure AD?

Abra um prompt de comando no PowerShell e execute os seguintes comandos:

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Esses comandos imprimem todos os certificados associados ao seu locatário com a instância da extensão do NPS em sua sessão do PowerShell. Procure seu certificado exportando o certificado do cliente como um arquivo *X. 509 (. cer) codificado em base-64* sem a chave privada e compare-o com a lista do PowerShell.

O comando a seguir criará um arquivo chamado *npscertificate* na raiz da unidade *C:* em Format *. cer*.

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertificate.cer
```

Depois de executar esse comando, vá para a raiz da unidade *C:* , localize o arquivo e clique duas vezes nele. Vá para detalhes e role para baixo até "impressão digital". Compare a impressão digital do certificado instalado no servidor com esta. As impressões digitais do certificado devem corresponder.

*Válidos-de* e *válido-até* os carimbos de data/hora, que estão no formato legível, podem ser usados para filtrar inadequadamente os inadequados se o comando retornar mais de um certificado.

### <a name="why-cannot-i-sign-in"></a>Por que não consigo entrar?

Verifique se sua senha não expirou. A extensão do NPS não dá suporte à alteração de senhas como parte do fluxo de trabalho de entrada. Entre em contato com a equipe de TI da sua organização para obter mais assistência.

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Por que minhas solicitações estão falhando, com erro de token ADAL?

Esse erro pode ser causado por vários motivos. Use as seguintes etapas para solucionar problemas:

1. Reinicie o servidor NPS.
2. Verifique se o certificado do cliente está instalado conforme o esperado.
3. Verifique se o certificado está associado ao seu locatário no Azure AD.
4. Verifique se `https://login.microsoftonline.com/` pode ser acessado no servidor que está executando a extensão.

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Por que a autenticação falha, com um erro nos logs de HTTP, informando que o usuário não foi encontrado?

Verifique se o AD Connect está em execução e se o usuário está presente no ambiente de AD DS local e no Azure AD.

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Por que vejo erros de conexão HTTP nos logs, com todas as minhas autenticações falhando?

Verifique se https://adnotifications.windowsazure.com pode ser alcançado no servidor que está executando a extensão NPS.

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Por que a autenticação não está funcionando, apesar de um certificado válido estar presente?

Se o certificado do computador anterior tiver expirado e um novo certificado tiver sido gerado, exclua todos os certificados expirados. Os certificados expirados podem causar problemas com a extensão do NPS iniciando.

Para verificar se você tem um certificado válido, verifique o *repositório de certificados da conta do computador* local usando o MMC e certifique-se de que o certificado não tenha passado sua data de expiração. Para gerar um certificado recentemente válido, execute novamente as etapas em [executar o script do instalador do PowerShell](#run-the-powershell-script).

### <a name="why-do-i-see-discarded-requests-in-the-nps-server-logs"></a>Por que vejo solicitações descartadas nos logs do servidor NPS?

Um servidor VPN pode enviar solicitações repetidas para o servidor NPS se o valor de tempo limite for muito baixo. O servidor NPS detecta essas solicitações duplicadas e as descarta. Esse comportamento é por design e não indica um problema com o servidor NPS ou com a extensão NPS da autenticação multifator do Azure AD.

Para obter mais informações sobre por que você vê pacotes descartados nos logs do servidor NPS, consulte [comportamento do protocolo RADIUS e a extensão do NPS](#radius-protocol-behavior-and-the-nps-extension) no início deste artigo.

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Como gerenciar protocolos TLS/SSL e conjuntos de codificação

É recomendável que pacotes de codificação antigos e mais fracos sejam desabilitados ou removidos, a menos que sejam exigidos pela sua organização. Informações sobre como concluir essa tarefa podem ser encontradas no artigo [Gerenciando protocolos SSL/TLS e conjuntos de codificação para AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)

### <a name="additional-troubleshooting"></a>Soluções de problemas adicionais

Diretrizes de solução de problemas adicionais e possíveis soluções podem ser encontradas no artigo [resolver mensagens de erro da extensão do NPS para a autenticação multifator do Azure ad](howto-mfa-nps-extension-errors.md).

## <a name="next-steps"></a>Próximas etapas

- [Visão geral e configuração do Servidor de Políticas de Rede no Windows Server](/windows-server/networking/technologies/nps/nps-top)

- Configurar IDs alternativos para logon ou configurar uma lista de exceções para IPs que não devem executar a verificação em duas etapas nas [Opções de configuração avançadas para a extensão do NPS para autenticação multifator](howto-mfa-nps-extension-advanced.md)

- Saiba como integrar o [Gateway de Área de Trabalho Remota](howto-mfa-nps-extension-rdg.md) e os [servidores VPN](howto-mfa-nps-extension-vpn.md) usando a extensão NPS

- [Resolver mensagens de erro da extensão do NPS para a autenticação multifator do Azure AD](howto-mfa-nps-extension-errors.md)
