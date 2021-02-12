---
title: Considerações de implantação para a autenticação multifator do Azure AD
description: Saiba mais sobre as considerações e a estratégia de implantação para a implementação bem-sucedida da autenticação multifator do Azure AD
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9ee81abd7cd0268a7cbd6b16aa6065ec7b54bef
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861299"
---
# <a name="plan-an-azure-ad-multi-factor-authentication-deployment"></a>Planejar uma implantação da autenticação multifator do Azure AD

As pessoas estão se conectando a recursos organizacionais em cenários cada vez mais complexos. Elas se conectam por dispositivos corporativos, pessoais e públicos dentro e fora da rede corporativa usando smartphones, tablets, PCs e laptops, geralmente em várias plataformas. Nesse mundo sempre conectado, de vários dispositivos e plataformas, a segurança das contas de usuário é mais importante do que nunca. As senhas usadas em dispositivos, redes e plataformas, não importa o quanto sejam complexas, são insuficientes para garantir a segurança das contas dos usuários, especialmente quando eles reutilizam senhas entre contas. Um phishing sofisticado e outros ataques de engenharia social podem resultar na publicação e venda de nomes de usuário e senhas na dark web.

A [MFA (autenticação multifator) do Azure ad](concept-mfa-howitworks.md) ajuda a proteger o acesso a dados e aplicativos. Ela fornece uma camada adicional de segurança usando uma segunda forma de autenticação. As organizações podem usar o [Acesso Condicional](../conditional-access/overview.md) para fazer com que a solução se adeque às suas necessidades específicas.

Este guia de implantação mostra como planejar e testar uma distribuição de autenticação multifator do Azure AD.

Para ver rapidamente a autenticação multifator do Azure AD em ação e, em seguida, voltar para entender as considerações de implantação adicionais:

> [!div class="nextstepaction"]
> [Habilitar a Autenticação Multifator do Azure AD](tutorial-enable-azure-mfa.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar uma implantação da autenticação multifator do Azure AD, há itens de pré-requisito que devem ser considerados.

| Cenário | Pré-requisito |
| --- | --- |
| Ambiente de identidade **somente em nuvem** com autenticação moderna | **Nenhuma tarefa de pré-requisito adicional** |
| Cenários de identidade **híbrida** | O [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) é implantado e as identidades de usuário são sincronizadas ou federadas com o Active Directory Domain Services local e com o Azure Active Directory. |
| Aplicativos herdados locais publicados para acesso à nuvem | O [Proxy de Aplicativo](../manage-apps/application-proxy.md) do Azure AD é implantado. |
| Usando o Azure AD MFA com autenticação RADIUS | Um [NPS (Servidor de Políticas de Rede)](howto-mfa-nps-extension.md) é implantado. |
| Usuários do Microsoft Office 2010 ou anterior ou do Apple Mail para iOS 11 ou anterior | Atualizem para o [Microsoft Office 2013 ou posterior](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) e Apple Mail para iOS 12 ou posterior. O Acesso Condicional não tem suporte dos protocolos de autenticação herdada. |

## <a name="plan-user-rollout"></a>Planejar a distribuição aos usuários

Seu plano de distribuição da MFA inclui uma implementação piloto seguida por ondas de implementação que estão dentro da capacidade de suporte. Comece a distribuição aplicando suas políticas de Acesso Condicional a um pequeno grupo de usuários piloto. Depois de avaliar o efeito nos usuários piloto, os processos usados e os comportamentos de registro, você pode adicionar mais grupos à política ou adicionar mais usuários aos grupos existentes.

### <a name="user-communications"></a>Comunicações aos usuários

É essencial informar os usuários, em comunicações planejadas, sobre as próximas alterações, requisitos de registro do Azure AD MFA e quaisquer ações de usuário necessárias. Recomendamos que as comunicações sejam desenvolvidas em conjunto com os representantes de dentro de sua organização, como os departamentos de Comunicações, Gerenciamento de Alterações ou Recursos Humanos.

A Microsoft fornece [modelos de comunicação](https://aka.ms/mfatemplates) e [documentação do usuário final](../user-help/security-info-setup-signin.md) para ajudar na elaboração de suas comunicações. Você pode enviar usuários para [https://myprofile.microsoft.com](https://myprofile.microsoft.com) a fim de que se registrem diretamente, selecionando os links de **Informações de Segurança** nessa página.

## <a name="deployment-considerations"></a>Considerações de implantação

A autenticação multifator do Azure AD é implantada pela imposição de políticas com acesso condicional. Uma política de Acesso Condicional pode exigir que os usuários executem a Autenticação Multifator quando certos critérios são cumpridos, como:

* Todos os usuários, um usuário específico, um membro de um grupo ou com função atribuída
* Um aplicativo de nuvem específico está sendo acessado
* Plataforma de dispositivos
* Estado do dispositivo
* Local de rede ou endereço IP localizado geograficamente
* Aplicativos cliente
* Risco de entrada (requer Identity Protection)
* Dispositivo em conformidade
* Dispositivo adicionado ao Azure AD híbrido
* Aplicativos cliente aprovados

Use cartazes personalizáveis e modelos de email em [materiais de distribuição da Autenticação Multifator](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) para distribuí-la em sua organização.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Habilitar Autenticação Multifator com acesso condicional

As políticas de Acesso Condicional impõem o registro, exigindo que usuários não registrados concluam o registro no primeiro acesso, uma consideração de segurança importante.

[Azure ad Identity Protection](../identity-protection/howto-identity-protection-configure-risk-policies.md) contribui para uma política de registro e para detecção de riscos automatizada e políticas de correção para a história de autenticação multifator do Azure AD. As políticas podem ser criadas para forçar alterações de senha quando há uma ameaça de identidade comprometida ou exigir a MFA quando um acesso é considerado arriscado pelos seguintes [eventos](../identity-protection/overview-identity-protection.md):

* Credenciais vazadas
* Entradas de endereços IP anônimos
* Viagem impossível a locais atípicos
* Entradas de locais desconhecidos
* Entradas de dispositivos infectados
* Acessos de endereços IP com atividade suspeita

Algumas das detecções de risco feitas pelo Azure Active Directory Identity Protection ocorrem em tempo real e outros exigem processamento offline. Os administradores podem escolher bloquear os usuários de comportamento suspeito e corrigir manualmente, exigir uma alteração de senha ou exigir uma Autenticação Multifator como parte de suas políticas de Acesso Condicional.

## <a name="define-network-locations"></a>Definir os locais de rede

Recomendamos que as organizações usem o Acesso Condicional para definir sua rede usando [localizações nomeadas](../conditional-access/location-condition.md#named-locations). Caso sua organização esteja usando o Identity Protection, considere o uso de políticas baseadas em risco em vez de localizações nomeadas.

### <a name="configuring-a-named-location"></a>Configurar uma localização nomeada

1. Abra o **Azure Active Directory** no portal do Azure
2. selecione **Segurança**
3. Em **Gerenciar**, escolha **Localizações Nomeadas**
4. Selecione **Novo Local**
5. No campo **Nome**, forneça um nome significativo
6. Selecione se você está definindo o local usando *Intervalos de IP* ou *Países/regiões*
   1. Caso use *Intervalos de IP*
      1. Decida se deseja *Marcar como local confiável*. Conectar de um local confiável diminui o risco de entrada do usuário. Só marque esse local como confiável se souber que os intervalos de IP inseridos estão estabelecidos e são confiáveis na sua organização.
      2. Especificar os intervalos de IP
   2. Caso use *Países/regiões*
      1. Expanda o menu suspenso e selecione os países ou regiões que você deseja definir para essa localização nomeada.
      2. Decida se deseja *Incluir áreas desconhecidas*. As áreas desconhecidas são endereços IP que não podem ser mapeados para um país/região.
7. Escolha **Criar**

## <a name="plan-authentication-methods"></a>Planejar métodos de autenticação

Os administradores podem escolher os [métodos de autenticação](../authentication/concept-authentication-methods.md) que desejam disponibilizar aos usuários. É importante permitir mais de um método de autenticação, para que os usuários tenham um modo de backup caso o principal não esteja disponível. Os seguintes métodos estão disponíveis para que os administradores habilitem:

> [!TIP]
> A Microsoft recomenda usar o Microsoft Authenticator (aplicativo móvel) como o método principal para a autenticação multifator do Azure AD para uma experiência de usuário mais segura e aprimorada. O aplicativo Microsoft Authenticator também [atende](https://azure.microsoft.com/resources/microsoft-nist/) ao National Institute of Standards and Technology Authenticator Assurance Levels. 

### <a name="notification-through-mobile-app"></a>Notificação pelo aplicativo móvel

Uma notificação por push será enviada ao aplicativo Microsoft Authenticator em seu dispositivo móvel. O usuário vê a notificação e seleciona **Aprovar** para concluir a verificação. As notificações por push em um aplicativo móvel fornecem a opção menos invasiva para os usuários. Elas também são a opção mais confiável e segura, pois usam uma conexão de dados em vez de telefonia.

> [!NOTE]
> Caso sua organização tenha funcionários trabalhando ou viajando para a China, a **Notificação por aplicativo móvel** em **dispositivos Android** não funciona nesse país/região. Métodos alternativos devem ser disponibilizados para esses usuários.

### <a name="verification-code-from-mobile-app"></a>Código de verificação de aplicativo móvel

Um aplicativo como o Microsoft Authenticator gera um novo código de verificação OATH a cada 30 segundos. O usuário digita o código de verificação na interface de entrada. A opção de aplicativo móvel pode ser usada independentemente de o telefone ter ou não um sinal de celular ou de dados.

### <a name="call-to-phone"></a>Ligue para o telefone

Uma chamada de voz automática é feita para o usuário. O usuário atende à chamada e pressiona **#** no teclado do telefone para aprovar a autenticação. A chamada para telefone é um excelente método de backup para notificação ou código de verificação de um aplicativo móvel.

### <a name="text-message-to-phone"></a>Mensagem de texto para telefone

Uma mensagem de texto com um código de verificação é enviada ao usuário, e é solicitado que ele insira o código de verificação na interface de acesso.

### <a name="choose-verification-options"></a>Escolher opções de verificação

1. Navegue até **do Azure Active Directory**, **Usuários**, **Autenticação Multifator**.

   ![Acessando o portal de Autenticação Multifator na folha de usuários do Azure Active Directory no portal do Azure](media/howto-mfa-getstarted/users-mfa.png)

1. Na nova guia que é aberta, navegue até **configurações do serviço**.
1. Em **opções de verificação**, verifique todas as caixas para os métodos disponíveis aos usuários.

   ![Configurar métodos de verificação na guia configurações do serviço de Autenticação Multifator](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Clique em **Save**.
1. Feche a guia **configurações de serviço**.

## <a name="plan-registration-policy"></a>Planejar política de registro

Os administradores devem determinar como os usuários registrarão seus métodos. As organizações devem [habilitar a nova experiência de registro combinada](howto-registration-mfa-sspr-combined.md) para MFA do Azure AD e redefinição de senha de autoatendimento (SSPR). A SSPR permite que os usuários redefinam a senha de maneira segura usando os mesmos métodos da Autenticação Multifator. Recomendamos esse registro combinado porque é uma ótima experiência para os usuários, que podem se registrar de uma só vez em ambos os serviços. Habilitar os mesmos métodos para SSPR e MFA do Azure AD permitirá que os usuários sejam registrados para usar os dois recursos.

### <a name="registration-with-identity-protection"></a>Registro com o Identity Protection

Caso sua organização esteja usando o Azure Active Directory Identity Protection, [configure a política de registro com MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md) para solicitar que os usuários se registrem na próxima vez que entrarem de forma interativa.

### <a name="registration-without-identity-protection"></a>Registro sem o Identity Protection

Caso sua organização não tenha licenças que habilitem o Identity Protection, os usuários serão solicitados a se registrar na próxima vez que a MFA for pedida na entrada. Os usuários não poderão ser registrados na MFA se não usarem aplicativos protegidos com ela. É importante que todos os usuários se registrem para que atores nocivos não consigam adivinhar a senha de um usuário nem se registrar na MFA em nome dele, assumindo o controle efetivo da conta.

#### <a name="enforcing-registration"></a>Impor o registro

Com o uso das etapas a seguir, uma política de Acesso Condicional pode obrigar os usuários a se registrarem na Autenticação Multifator

1. Crie um grupo e adicione todos os usuários que não estão registrados no momento.
2. Com o Acesso Condicional, imponha a Autenticação Multifator a esse grupo para acesso a todos os recursos.
3. Periodicamente, reavalie os membros do grupo e remova os usuários que se registraram.

Você pode identificar usuários registrados e não registrados do Azure AD MFA com comandos do PowerShell que dependem do [módulo do PowerShell do MSOnline](/powershell/azure/active-directory/install-msonlinev1).

#### <a name="identify-registered-users"></a>Identificar usuários registrados

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Identificar usuários não registrados

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Converter usuários da MFA individual para a MFA baseada em Acesso Condicional

Se os usuários tiverem sido habilitados usando a autenticação multifator habilitada por usuário e impostas, o PowerShell a seguir poderá ajudá-lo a fazer a conversão para o acesso condicional com base na autenticação multifator do Azure AD.

Execute este PowerShell em uma janela do ISE ou salve como um arquivo `.PS1` para ser executado localmente.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Alteramos recentemente o comportamento e o script do PowerShell acima de forma adequada. Anteriormente, o script excluía os métodos de MFA, desabilitava a MFA e restaurava os métodos. Isso não é mais necessário, agora que o comportamento padrão para desabilitar não limpa os métodos.

## <a name="plan-conditional-access-policies"></a>Planejar as políticas de Acesso Condicional

Para planejar sua estratégia de política de Acesso Condicional, que determinará quando a MFA e outros controles são necessários, confira [Políticas de Acesso Condicional comuns](../conditional-access/concept-conditional-access-policy-common.md).

É importante que você impeça o bloqueio inadvertido do seu locatário do Azure Active Directory. Você pode reduzir o impacto da falta acidental de acesso administrativo ao [criar duas ou mais contas emergenciais de acesso para seu locatário](../roles/security-emergency-access.md) e excluí-las de sua política de Acesso Condicional.

### <a name="create-conditional-access-policy"></a>Criar uma política de Acesso Condicional

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta de administrador global.
1. Procure **Azure Active Directory** > **Segurança** > **Acesso Condicional**.
1. Selecione **Nova política**.
   ![Criar uma política de Acesso Condicional para habilitar a MFA para usuários do portal do Azure no grupo piloto](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. .Forneça um nome significativo para a política.
1. Em **usuários e grupos**:
   * Na guia **Incluir**, selecione o botão de opção **Todos os usuários**
   * Na guia **Excluir**, marque a caixa para **Usuários e grupos** e escolha suas contas de acesso emergencial.
   * Clique em **Concluído**.
1. Em **Aplicativos na nuvem**, selecione o botão de opção **Todos os aplicativos na nuvem**.
   * OPCIONALMENTE: Na guia **Excluir**, escolha os aplicativos na nuvem para os quais sua organização não exige o MFA.
   * Clique em **Concluído**.
1. Na seção **Condições**:
   * OPCIONALMENTE: Se você tiver habilitado o Azure Identity Protection, poderá optar por avaliar o risco de conexão como parte da política.
   * OPCIONALMENTE: Se você tiver configurado localizações confiáveis ou nomeadas, poderá especificar para incluir ou excluir essas localizações da política.
1. Em **Concessão**, verifique se o botão de opção **Conceder acesso** está selecionado.
    * Marque a caixa para **Exigir autenticação multifator**.
    * Clique em **Selecionar**.
1. Ignore a seção **Sessão**.
1. Defina a alternância **Habilitar política** para **Ativado**.
1. Clique em **Criar**.

## <a name="plan-integration-with-on-premises-systems"></a>Planejar a integração com sistemas locais

Alguns aplicativos herdados e locais que não são autenticados diretamente no Azure AD exigem etapas adicionais para usar a MFA, incluindo:

* Aplicativos locais herdados, que precisarão usar o proxy de aplicativo.
* Aplicativos RADIUS locais, que precisarão usar o adaptador da MFA com o servidor NPS.
* Aplicativos AD FS locais, que precisarão usar o adaptador da MFA com AD FS 2016 ou mais recente.

Aplicativos que se autenticam diretamente com o Azure AD e têm autenticação moderna (WS-Fed, SAML, OAuth, OpenID Connect) podem usar as políticas de Acesso Condicional diretamente.

### <a name="use-azure-ad-mfa-with-azure-ad-application-proxy"></a>Usar o Azure AD MFA com o Azure Proxy de Aplicativo do AD

Os aplicativos que residem localmente podem ser publicados em seu locatário do Azure AD por meio do [azure proxy de aplicativo do AD](../manage-apps/application-proxy.md) e podem aproveitar a autenticação multifator do Azure ad se estiverem configurados para usar a pré-autenticação do Azure AD.

Esses aplicativos estão sujeitos a políticas de acesso condicional que impõem a autenticação multifator do Azure AD, assim como qualquer outro aplicativo integrado ao Azure AD.

Da mesma forma, se a autenticação multifator do Azure AD for imposta para todas as entradas de usuário, os aplicativos locais publicados com o Azure Proxy de Aplicativo do AD serão protegidos.

### <a name="integrating-azure-ad-multi-factor-authentication-with-network-policy-server"></a>Integrando a autenticação multifator do Azure AD com o servidor de políticas de rede

A extensão do servidor de políticas de rede (NPS) para o Azure AD MFA adiciona recursos de MFA baseados em nuvem à sua infraestrutura de autenticação usando seus servidores existentes. Com a extensão do NPS, você pode adicionar uma chamada telefônica, mensagem de texto ou verificação de aplicativo por telefone a seu fluxo de autenticação existente. Essa integração tem as seguintes limitações:

* Com o protocolo CHAPv2, somente as notificações por push do aplicativo autenticador e as chamadas de voz têm suporte.
* As políticas de Acesso Condicional não podem ser aplicadas.

A extensão NPS atua como um adaptador entre o RADIUS e o Azure AD MFA baseado em nuvem para fornecer um segundo fator de autenticação para proteger [VPN](howto-mfa-nps-extension-vpn.md), [área de trabalho remota conexões de gateway](howto-mfa-nps-extension-rdg.md)ou outros aplicativos compatíveis com RADIUS. Os usuários que se registram para o MFA do Azure AD nesse ambiente serão desafiados em todas as tentativas de autenticação, a falta de políticas de acesso condicional significa que a MFA é sempre necessária.

#### <a name="implementing-your-nps-server"></a>Implementar o servidor NPS

Se você já tiver uma instância do NPS implantada e em uso, [a referência integrará sua infraestrutura de NPS existente com a autenticação multifator do Azure ad](howto-mfa-nps-extension.md). Caso esteja configurando o NPS pela primeira vez, confira [NPS (Servidor de Políticas de Rede)](/windows-server/networking/technologies/nps/nps-top) para obter instruções. As diretrizes de solução de problemas podem ser encontradas no artigo [resolver mensagens de erro da extensão do NPS para a autenticação multifator do Azure ad](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>Preparar o NPS para usuários não registrados na Autenticação Multifator

Escolha o que acontece quando usuários que não estão registrados com a MFA tentam se autenticar. Use a configuração de registro `REQUIRE_USER_MATCH` no caminho de registro `HKLM\Software\Microsoft\AzureMFA` para controlar o comportamento do recurso. Essa configuração tem uma única opção de definição.

| Chave | Valor | Padrão |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | VERDADEIRO/FALSO | Não definido (equivalente a TRUE) |

A finalidade dessa configuração é determinar o que fazer quando um usuário não estiver inscrito na MFA. Os efeitos da alteração dessa configuração estão listados na tabela a seguir.

| Configurações | Status do usuário na MFA | Efeitos |
| --- | --- | --- |
| A chave não existe | Não registrado | O desafio de MFA não foi bem-sucedido |
| Valor definido como Verdadeiro/não-definido | Não registrado | O desafio de MFA não foi bem-sucedido |
| Chave definida como Falsa | Não registrado | Autenticação sem MFA |
| Chave definida como Falsa ou Verdadeira | Registrado | É preciso autenticar com MFA |

### <a name="integrate-with-active-directory-federation-services"></a>Integração com os Serviços de Federação do Active Directory (AD FS)

Se sua organização for federada com o Azure AD, você poderá usar a [autenticação multifator do Azure ad para proteger AD FS recursos](multi-factor-authentication-get-started-adfs.md), tanto localmente quanto na nuvem. O MFA do Azure AD permite que você reduza as senhas e forneça uma maneira mais segura de autenticar. A partir do Windows Server 2016, agora você pode configurar o Azure AD MFA para autenticação primária.

Ao contrário do AD FS no Windows Server 2012 R2, o adaptador de MFA do Azure AD AD FS 2016 integra-se diretamente ao Azure AD e não requer um servidor Azure MFA local. O adaptador de MFA do Azure AD é integrado ao Windows Server 2016 e não há necessidade de uma instalação adicional.

Ao usar o Azure AD MFA com AD FS 2016 e o aplicativo de destino está sujeito à política de acesso condicional, há considerações adicionais:

* O Acesso Condicional está disponível quando o aplicativo é uma terceira parte confiável do Azure AD, federado com os AD FS 2016 ou mais recente.
* O Acesso Condicional não está disponível quando o aplicativo é uma terceira parte confiável para os AD FS 2016 ou AD FS 2019 e é gerenciado ou federado com os AD FS 2016 ou AD FS 2019.
* O acesso condicional também não está disponível quando AD FS 2016 ou AD FS 2019 está configurado para usar o Azure AD MFA como o método de autenticação principal.

#### <a name="ad-fs-logging"></a>Registro em log dos AD FS

O log padrão dos AD FS 2016 e 2019 no Registro de Segurança do Windows e no log do Administrador dos AD FS contém informações sobre as solicitações de autenticação e seu sucesso ou falha. Os dados do log de eventos dentro desses eventos indicarão se o Azure AD MFA foi usado. Por exemplo, um Evento de Auditoria ID 1200 dos AD FS pode conter:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Renovar e gerenciar certificados

Em cada servidor de AD FS, no computador local meu repositório, haverá um certificado de MFA do Azure AD autoassinado intitulado OU = Microsoft AD FS Azure MFA, que contém a data de expiração do certificado. Verifique o período de validade desse certificado em cada servidor AD FS para determinar essa data.

Se o período de validade de seus certificados estiver perto da expiração, [gere e verifique um novo certificado de MFA em cada servidor AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

As diretrizes a seguir detalham como gerenciar os certificados do Azure AD MFA em seus servidores de AD FS. Quando você configura AD FS com o Azure AD MFA, os certificados gerados por meio do `New-AdfsAzureMfaTenantCertificate` cmdlet do PowerShell são válidos por dois anos. Renove e instale os certificados renovados antes da expiração para evitar interrupções no serviço deMFA.

## <a name="implement-your-plan"></a>Implementar seu plano

Agora que você planejou sua solução, poderá implementá-la seguindo as etapas abaixo:

1. Atender a todos os pré-requisitos necessários
   1. Implantar o [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) em qualquer cenário híbrido
   1. Implantar o [Proxy de Aplicativo do AD do Azure](../manage-apps/application-proxy.md) em qualquer aplicativo local publicado para acesso à nuvem
   1. Implantar [NPS](/windows-server/networking/technologies/nps/nps-top) para qualquer autenticação RADIUS
   1. Garantir que os usuários atualizaram para as versões com suporte do Microsoft Office com autenticação moderna habilitada
1. Configurar os [métodos de autenticação](#choose-verification-options) escolhidos
1. Definir seus [locais de rede nomeados](../conditional-access/location-condition.md#named-locations)
1. Selecionar grupos para começar a distribuir a MFA.
1. Configurar [política de Acesso Condicional](#create-conditional-access-policy)
1. Configurar política de registro com MFA
   1. [MFA e SSPR combinadas](howto-registration-mfa-sspr-combined.md)
   1. Com [Identity Protection](../identity-protection/howto-identity-protection-configure-mfa-policy.md)
1. Enviar comunicações aos usuários e pedir para que se registrem em [https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Controlar quem está registrado](#identify-non-registered-users)

> [!TIP]
> Os usuários governamentais de nuvem podem se registrar em [https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>Gerenciar sua solução

Relatórios para MFA do Azure AD

A autenticação multifator do Azure AD fornece relatórios por meio do portal do Azure:

| Relatório | Location | Descrição |
| --- | --- | --- |
| Alertas de fraudes e uso | Microsoft Azure AD > Entradas | Fornece informações sobre o uso geral, resumo do usuário e detalhes do usuário, assim como um histórico de alertas de fraude enviados durante o intervalo de datas especificado. |

## <a name="troubleshoot-mfa-issues"></a>Solucionar problemas da MFA

Encontre soluções para problemas comuns com o Azure AD MFA no [artigo solução de problemas da autenticação multifator do Azure ad](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) no centro de suporte da Microsoft.

## <a name="next-steps"></a>Próximas etapas

Para ver a autenticação multifator do Azure AD em ação, conclua o seguinte tutorial:

> [!div class="nextstepaction"]
> [Habilitar a Autenticação Multifator do Azure AD](tutorial-enable-azure-mfa.md)
