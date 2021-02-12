---
title: 'Azure AD Connect: migrar da Federação para o PTA para o Azure AD'
description: Este artigo contém informações sobre como migrar seu ambiente de identidade híbrida da federação para autenticação de passagem.
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a10c4c0e6e40636e4803e054155d6fdaa12a9366
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96858545"
---
# <a name="migrate-from-federation-to-pass-through-authentication-for-azure-active-directory"></a>Migrar da federação para a autenticação de passagem do Azure Active Directory

Este artigo descreve como mover seus domínios de organização de AD FS (Serviços de Federação do Active Directory) para autenticação de passagem.

> [!NOTE]
> Alterar seu método de autenticação requer planejamento, teste e potencialmente tempo de inatividade. A [distribuição em etapas](how-to-connect-staged-rollout.md) fornece uma maneira alternativa de testar e migrar gradualmente da Federação para a autenticação na nuvem usando a autenticação de passagem.
> 
> Se você planeja usar a distribuição em etapas, lembre-se de desativar os recursos de distribuição em etapas quando terminar de recortar.  Para obter mais informações, consulte [migrar para a autenticação de nuvem usando a distribuição em etapas](how-to-connect-staged-rollout.md)


## <a name="prerequisites-for-migrating-to-pass-through-authentication"></a>Pré-requisitos para a migração para a autenticação de passagem

Os seguintes pré-requisitos são necessários para migrar do uso do AD FS para usar a autenticação de passagem.

### <a name="update-azure-ad-connect"></a>Atualizar o Azure AD Connect

Para concluir com êxito as etapas necessárias para migrar para o uso da autenticação de passagem, você deve ter o [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (Azure Active Directory Connect) 1.1.819.0 ou uma versão posterior. No Azure AD Connect 1.1.819.0, a maneira como a conversão de entrada é realizada muda significativamente. O tempo geral para migrar do AD FS para autenticação de nuvem nesta versão é reduzido de potencialmente horas para minutos.

> [!IMPORTANT]
> Você pode ler em documentação, ferramentas e blogs desatualizados que é necessária conversão do usuário ao converter domínios de identidade federada para identidade gerenciada. A *conversão de usuários* não é mais necessária. A Microsoft está trabalhando para atualizar a documentação e as ferramentas para refletir essa alteração.

Para atualizar Azure AD Connect, conclua as etapas em [Azure ad Connect: Atualize para a versão mais recente](./how-to-upgrade-previous-version.md).

### <a name="plan-authentication-agent-number-and-placement"></a>Planejar o posicionamento e o número de agentes de autenticação

A autenticação de passagem exige a implantação de agentes leves no servidor do Azure AD Connect e no computador local que executa o Windows Server. Para reduzir a latência, instale os agentes tão próximos dos seus controladores de domínio do Active Directory quanto for possível.

Para a maioria dos clientes, dois ou três agentes de autenticação são suficientes para fornecer alta disponibilidade e a capacidade necessária. Um locatário pode ter um máximo de 12 agentes registrados. O primeiro agente é sempre instalado no servidor do Azure AD Connect propriamente dito. Para saber mais sobre as limitações do agente e as opções de implantação do agente, consulte [autenticação de passagem do Azure AD: limitações atuais](./how-to-connect-pta-current-limitations.md).

### <a name="plan-the-migration-method"></a>Planeje o método de migração

Você pode escolher entre dois métodos para migrar de gerenciamento de identidades federadas para autenticação de passagem e SSO (logon único) contínuo. O método usado depende de como sua instância do AD FS foi originalmente configurada.

* **Azure AD Connect**. Se você originalmente tiver configurado o AD FS usando o Azure AD Connect, *deverá* alterar para autenticação de passagem usando o assistente do Azure AD Connect.

   O Azure AD Connect executa automaticamente o cmdlet **Set-MsolDomainAuthentication** quando você altera o método de entrada do usuário. O Azure AD Connect cancela automaticamente a federação todos os domínios federados verificados no locatário do Azure AD.

   > [!NOTE]
   > No momento, se você tiver usado originalmente o Azure AD Connect para configurar o AD FS, você não poderá evitar cancelar a federação de todos os domínios em seu locatário ao alterar a entrada do usuário para a autenticação de passagem.
‎
* **Azure AD Connect com PowerShell**. Você pode usar esse método somente se originalmente não tiver configurado o AD FS usando o Azure AD Connect. Para essa opção, você ainda deve alterar o método de entrada do usuário usando o Assistente do Azure AD Connect. A principal diferença dessa opção é que o assistente não executa automaticamente o cmdlet **Set-MsolDomainAuthentication**. Com essa opção, você tem controle total sobre quais domínios serão convertidos e em qual ordem.

Para entender qual método você deve usar, conclua as etapas nas seções a seguir.

#### <a name="verify-current-user-sign-in-settings"></a>Verificar as configurações atuais de entrada do usuário

1. Entre no [portal do Azure AD](https://aad.portal.azure.com/) usando uma conta de Administrador Global.
2. Na seção **Entrada do usuário**, verifique as seguintes configurações:
   * **Federação** é definida como **Habilitada**.
   * **Logon único contínuo** está definido como **Desabilitado**.
   * **Autenticação de passagem** está definida como **Desabilitada**.

   ![Captura de tela das configurações na seção de entrada do Usuário do Azure AD Connect](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image1.png)

#### <a name="verify-how-federation-was-configured"></a>Verificar como a federação foi configurada

1. No seu servidor do Azure AD Connect, abra o Azure AD Connect. Selecione **Configurar**.
2. Na página **Tarefas adicionais**, selecione **Exibir a configuração atual** e, em seguida, selecione **Avançar**.<br />
 
   ![Captura de tela da opção Exibir configuração atual na página de Tarefas adicionais](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image2.png)<br />
3. Em **tarefas adicionais > gerenciar Federação**, role até **serviços de Federação do Active Directory (AD FS) (AD FS)**.<br />

   * Se a configuração do AD FS aparecer nesta seção, você poderá presumir com segurança que o AD FS foi originalmente configurado por meio do Azure AD Connect. Você pode converter seus domínios de identidade federada em identidade gerenciada usando a opção **Alterar entrada do usuário** do Azure AD Connect. Para obter mais informações sobre o processo, consulte a seção **opção a: configurar a autenticação de passagem usando Azure ad Connect**.
   * Se o AD FS não está listado nas configurações atuais, você deve converter manualmente seus domínios de identidade federada para identidade gerenciada usando o PowerShell. Para obter mais informações sobre esse processo, consulte a seção **opção B: alternar da Federação para autenticação de passagem usando Azure ad Connect e o PowerShell**.

### <a name="document-current-federation-settings"></a>Configurações de federação atuais do documento

Para localizar as configurações de Federação atuais, execute o cmdlet **Get-MsolDomainFederationSettings**:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Exemplo:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Verifique quaisquer configurações que possam ter sido personalizadas para sua documentação de implantação e design de federação. Especificamente, procure as personalizações em **PreferredAuthenticationProtocol**, **SupportsMfa** e **PromptLoginBehavior**.

Para obter mais informações, confira estes tópicos:

* [Prompt do AD FS = suporte ao parâmetro de logon](/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](/powershell/module/msonline/set-msoldomainauthentication)

> [!NOTE]
> Se **SupportsMfa** está definido como **Verdadeiro**, você está usando uma solução de autenticação multifator localmente para injetar um desafio de segundo fator no fluxo de autenticação de usuário. Essa configuração não funciona para cenários de autenticação do Azure AD. 
>
> Em vez disso, use o serviço baseado em nuvem da autenticação multifator do Azure AD para executar a mesma função. Avalie seus requisitos de autenticação multifator com cuidado antes de continuar. Antes de converter seus domínios, certifique-se de que você entendeu como usar a autenticação multifator do Azure AD, as implicações de licenciamento e o processo de registro do usuário.

#### <a name="back-up-federation-settings"></a>Configurações de federação de backup

Embora nenhuma alteração tenha sido feita para outras partes confiáveis em seu farm do AD FS durante os processos descritos neste artigo, é recomendável que você tenha um backup válido do farm do AD FS do qual você possa restaurar. Você pode criar um backup válido atual usando a [Ferramenta de Restauração Rápida do AD FS](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool) da Microsoft gratuita. Você pode usar a ferramenta para fazer backup do AD FS e para restaurar um farm existente ou criar um novo farm.

Se você optar por não usar a ferramenta de restauração rápida AD FS, no mínimo, deverá exportar a relação de confiança de terceira parte confiável da plataforma de identidade Microsoft 365 e quaisquer regras de declaração personalizada associadas que você adicionou. Você pode exportar o objeto de confiança de terceira parte confiável e as regras de declaração associadas usando o exemplo do PowerShell a seguir:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>Considerações sobre implantação e uso do AD FS

Esta seção descreve considerações sobre implantação e detalhes de como usar o AD FS.

### <a name="current-ad-fs-use"></a>Uso do AD FS atual

Antes de converter a identidade federada para a identidade gerenciada, examine com mais detalhes como você usa atualmente AD FS para o Azure AD, Microsoft 365 e outros aplicativos (confianças de terceira parte confiável). Especificamente, considere os cenários descritos na tabela a seguir:

| If | Então |
|-|-|
| Você planeja continuar usando AD FS com outros aplicativos (além do Azure AD e Microsoft 365). | Depois de converter seus domínios, você usará tanto o AD FS quanto o Azure AD. Considere a experiência do usuário. Em alguns cenários, os usuários podem ser solicitados a autenticar duas vezes: uma vez ao Azure AD (em que um usuário obtém acesso SSO a outros aplicativos, como Microsoft 365), e novamente para todos os aplicativos que ainda estão associados a AD FS como uma relação de confiança de terceira parte confiável. |
| Sua instância do AD FS é muito personalizada e depende das configurações de personalização específicas no arquivo onload.js (por exemplo, se você tiver alterado a experiência de conexão para que os usuários usem apenas um formato **SamAccountName** para o nome de usuário, em vez de um nome UPN, ou sua organização tiver aplicado fortemente a identidade de marca à experiência de conexão). O arquivo onload.js não pode ser duplicado no Azure AD. | Antes de continuar, verifique se que o Azure AD pode atender aos seus atuais requisitos de personalização. Para obter mais informações e diretrizes, veja as seções sobre identidade visual do AD FS e personalização do AD FS.|
| Você usa o AD FS para bloquear versões anteriores de clientes de autenticação.| Considere substituir os controles de AD FS que bloqueiam versões anteriores de clientes de autenticação usando uma combinação de [controles de acesso condicional](../conditional-access/concept-conditional-access-conditions.md) e [regras de acesso para cliente do Exchange Online](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules). |
| Você exige que os usuários realizem a autenticação multifator em relação a uma solução de servidor de autenticação multifator local quando os usuários se autenticam para o AD FS.| Em um domínio de identidade gerenciada, você não pode injetar um desafio de autenticação multifator por meio da solução de autenticação multifator local no fluxo de autenticação. No entanto, você pode usar o serviço de autenticação multifator do Azure AD para autenticação multifator depois que o domínio é convertido.<br /><br /> Se os usuários não usam atualmente a autenticação multifator do Azure AD, é necessária uma etapa de registro de usuário de OneTime. Você deve preparar e comunicar o registro planejado a seus usuários. |
| Atualmente, você usa políticas de controle de acesso (regras de AuthZ) em AD FS para controlar o acesso ao Microsoft 365.| Considere substituir as políticas com as [políticas de acesso condicional](../conditional-access/overview.md) do Azure ad equivalentes e as [regras de acesso para cliente do Exchange Online](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules).|

### <a name="common-ad-fs-customizations"></a>Personalizações de comuns do AD FS

Esta seção descreve as personalizações comuns do AD FS.

#### <a name="insidecorporatenetwork-claim"></a>Declaração de InsideCorporateNetwork

O AD FS emite a declaração **InsideCorporateNetwork** se o usuário que está realizando a autenticação está dentro da rede corporativa. Essa declaração então pode ser passada para o Azure AD. A declaração é usada para ignorar a autenticação multifator com base no local da rede do usuário. Para saber como determinar se essa funcionalidade no momento está disponível no AD FS, veja [IPs confiáveis para usuários federados](../authentication/howto-mfa-adfs.md).

A declaração **InsideCorporateNetwork** não está mais disponível depois da conversão de seus domínios em autenticação de passagem. Você agora usa [localizações nomeadas no Azure AD](../reports-monitoring/quickstart-configure-named-locations.md) para substituir essa funcionalidade.

Depois de configurar os locais nomeados, você deve atualizar todas as políticas de acesso condicional que foram configuradas para incluir ou excluir a rede **todos os locais confiáveis** ou valores de **IPs confiáveis MFA** para refletir os novos locais nomeados.

Para obter mais informações sobre a condição de **local** no acesso condicional, consulte [Active Directory locais de acesso condicional](../conditional-access/location-condition.md).

#### <a name="hybrid-azure-ad-joined-devices"></a>Dispositivos ingressados no Azure AD híbrido

Ao unir um dispositivo ao Azure AD, você pode criar regras de acesso condicional que impõem que os dispositivos atendam aos seus padrões de acesso para segurança e conformidade. Além disso, os usuários podem entrar em um dispositivo usando uma conta corporativa ou escolar, em vez de uma conta pessoal. Quando você usa dispositivos ingressados no Azure AD híbrido, pode ingressar seus dispositivos ingressados em domínio do Active Directory no Azure AD. Seu ambiente federado pode ter sido configurado para usar esse recurso.

Para garantir que a junção híbrida continue funcionando para todos os dispositivos que ingressem no domínio após a conversão de seus domínios para autenticação de passagem, para clientes do Windows 10, você deve usar o Azure AD Connect para sincronizar contas de computador do Active Directory com o Azure AD.

Para contas de computador do Windows 8 e Windows 7, a associação híbrida usa SSO contínuo para registrar o computador no Azure AD. Você não precisa sincronizar contas de computador do Windows 8 e do Windows 7 como faz para dispositivos Windows 10. No entanto, você deve implantar um arquivo atualizado workplacejoin.exe (por meio de um arquivo. msi) para os clientes do Windows 8 e do Windows 7 para que eles possam ser registrados usando SSO contínuo. [Baixe o arquivo .msi](https://www.microsoft.com/download/details.aspx?id=53554).

Para obter mais informações, veja [Configurar dispositivos ingressados no Azure AD híbrido](../devices/hybrid-azuread-join-plan.md).

#### <a name="branding"></a>Consolidação da marca

Se sua organização tiver [personalizadas suas páginas de entrada no AD FS](/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) para exibir informações mais pertinentes para a organização, considere fazer [personalizações de página de entrada do Azure AD](../fundamentals/customize-branding.md) similares.

Embora personalizações similares estejam disponíveis, algumas alterações visuais em páginas de entrada devem ser esperadas após a conversão. Você talvez queira fornecer informações sobre as alterações esperadas em suas comunicações com os usuários.

> [!NOTE]
> A identidade visual da organização estará disponível somente se você comprar a licença Premium ou básica para Azure Active Directory ou se tiver uma licença de Microsoft 365.

## <a name="plan-for-smart-lockout"></a>Plano para bloqueio inteligente

O bloqueio inteligente do Azure AD protege contra ataques de senha de força bruta. Bloqueio inteligente impede que uma conta do Active Directory local seja bloqueada quando a autenticação de passagem está sendo usada e uma política de grupo de bloqueio de conta está definida no Active Directory.

Para obter mais informações, veja [Bloqueio inteligente do Azure Active Directory](../authentication/howto-password-smart-lockout.md).

## <a name="plan-deployment-and-support"></a>Planejar a implantação e o suporte

Conclua as tarefas descritas nesta seção para ajudá-lo a planejar a implantação e o suporte.

### <a name="plan-the-maintenance-window"></a>Planejar a janela de manutenção

Embora o processo de conversão de domínio seja relativamente rápido, o Azure AD pode continuar a enviar algumas solicitações de autenticação para servidores do AD FS por até quatro horas após a conversão de domínio ter sido concluída. Durante essa janela de quatro horas, e dependendo de vários caches do lado do serviço, o Azure AD pode não aceitar essas autenticações. Os usuários podem receber um erro. O usuário pode se autenticar no AD FS, mas o Azure AD não aceita mais o token emitido pelo usuário porque essa relação de confiança de federação agora foi removida.

Somente os usuários que acessam os serviços por meio de um navegador da Web durante essa janela pós-conversão antes que o cache do lado do serviço seja removido são afetados. Não é esperado que os clientes herdados (Exchange ActiveSync, Outlook 2010/2013) sejam afetados, pois o Exchange Online mantém um cache de suas credenciais por um período definido. O cache é usado para silenciosamente autenticar o usuário outra vez. O usuário não precisa retornar ao AD FS. Credenciais armazenadas no dispositivo para esses clientes são usadas para silenciosamente autenticarem a si próprios outra vez depois que esse cache é limpo. Não é esperado que os usuários recebam qualquer prompt de senha como resultado do processo de conversão de domínio.

Clientes de autenticação modernos (aplicativos do Office 2016 e Office 2013, iOS e Android) usam um token de atualização válido para obter novos tokens de acesso para acesso contínuo aos recursos, em vez de retornar para o AD FS. Esses clientes são imunes a quaisquer prompts de senha resultantes do processo de conversão de domínio. Os clientes continuarão a funcionar sem configuração adicional.

> [!IMPORTANT]
> Não desligue seu ambiente de AD FS ou remova o Microsoft 365 terceira parte confiável até verificar se todos os usuários podem se autenticar com êxito usando a autenticação de nuvem.

### <a name="plan-for-rollback"></a>Planejar a reversão

Se você encontrar um grande problema que não consiga resolver rapidamente, poderá decidir reverter a solução para federação. É importante planejar o que fazer se a implantação não for distribuída conforme o pretendido. Se a conversão do domínio ou dos usuários falhar durante a implantação ou se for preciso reverter a federação, você deverá compreender como atenuar qualquer eventual interrupção e reduzir o efeito sobre seus usuários.

#### <a name="to-roll-back"></a>Para reverter

Para planejar a reversão, verifique a documentação de implantação e o design da federação para seus detalhes de implantação específicos. O processo deve incluir estas tarefas:

* Converter domínios gerenciados em domínios federados usando o cmdlet **Convert-MSOLDomainToFederated**.
* Se necessário, configurar regras de declarações adicionais.

### <a name="plan-communications"></a>Planejar a comunicação

Uma parte importante do planejamento de implantação e suporte é garantir que os usuários sejam proativamente informados sobre alterações futuras. Os usuários devem saber com antecedência o que eles podem encontrar e o que é exigido deles.

Depois que a autenticação de passagem e o SSO contínuo são implantados, a experiência de entrada do usuário para acessar Microsoft 365 e outros recursos que são autenticados por meio de alterações do Azure AD. Os usuários que estão fora da rede veem apenas a página de entrada no Azure AD. Esses usuários não são redirecionados para a página baseada em formulários apresentada por servidores de proxy de aplicativo Web voltados para o lado externo.

Inclua os seguintes elementos em sua estratégia de comunicação:

* Notificar os usuários sobre funcionalidades futuras e lançadas usando:
   * Email e outros canais de comunicação interna.
   * Elementos visuais, como cartazes.
   * Comunicações executivas, ao vivo ou de outro tipo.
* Determinar quem vai personalizar a comunicação e quem vai enviar as comunicações e quando.

## <a name="implement-your-solution"></a>Implementar sua solução

Você planejou sua solução. Agora, agora você pode implementá-la. A implementação envolve os seguintes componentes:

* Preparar-se para SSO contínuo.
* Alterar o método de entrada para autenticação de passagem e habilitar SSO contínuo.

### <a name="step-1-prepare-for-seamless-sso"></a>Etapa 1: preparar para o SSO contínuo

Para que todos os seus dispositivos usem o SSO contínuo, você deve adicionar uma URL do Azure AD às configurações de zona da intranet dos usuários usando a política de grupo no Active Directory.

Por padrão, os navegadores da Web calculam automaticamente a zona correta, seja Internet ou intranet, de uma URL. Por exemplo, **http: \/ \/ contoso/** Maps para a zona da intranet e **http: \/ \/ intranet.contoso.com** mapeia para a zona da Internet (porque a URL contém um ponto). Os navegadores enviam tíquetes Kerberos para um ponto de extremidade da nuvem, como a URL do Azure AD, a menos que você adicione explicitamente a URL à zona da intranet do navegador.

Conclua as [etapas para distribuir](./how-to-connect-sso-quick-start.md) as alterações necessárias para seus dispositivos.

> [!IMPORTANT]
> Fazer essa alteração não modificará a maneira como os usuários entram no Azure AD. No entanto, é importante que você aplique essa configuração a todos os seus dispositivos antes de continuar. Usuários que entram em dispositivos que não receberam essa configuração simplesmente devem digitar um nome de usuário e senha para entrar no Azure AD.

### <a name="step-2-change-the-sign-in-method-to-pass-through-authentication-and-enable-seamless-sso"></a>Etapa 2: alterar o método de entrada para autenticação de passagem e habilitar o SSO contínuo

Você tem duas opções para alterar o método de entrada para autenticação de passagem e habilitar SSO contínuo.

#### <a name="option-a-configure-pass-through-authentication-by-using-azure-ad-connect"></a>Opção A: configurar A autenticação de passagem usando Azure AD Connect

Use esse método se você tiver configurado inicialmente seu ambiente do AD FS usando o Azure AD Connect. Você não poderá usar esse método se *não* tiver configurado originalmente seu ambiente do AD FS usando o Azure AD Connect.

> [!IMPORTANT]
> Depois de concluir as etapas a seguir, todos os domínios serão convertidos de identidade federada em identidade gerenciada. Para obter mais informações, examine [Planejar o método de migração](#plan-the-migration-method).

Primeiro, altere o método de entrada:

1. No servidor do Azure AD Connect, abra o Assistente do Azure AD Connect.
2. Selecione **alterar entrada do usuário** e, em seguida, selecione **Avançar**. 
3. Na página **Conectar ao Azure AD**, insira o nome de usuário e a senha de uma conta de Administrador Global.
4. Na página **Entrada do usuário**, selecione o botão **Autenticação de passagem**, selecione **Habilitar logon único** e, em seguida, selecione **Avançar**.
5. Na página **Habilitar logon único**, insira as credenciais de uma conta de administrador de domínio e, em seguida, selecione **Avançar**.

   > [!NOTE]
   > Credenciais da conta de administrador de domínio são necessárias para habilitar SSO contínuo. O processo conclui as seguintes ações, que exigem essas permissões elevadas. As credenciais da conta do Administrador de Domínio não são armazenadas no Azure AD Connect nem no Azure AD. As credenciais da conta de Administrador de Domínio são usadas somente para ativar o recurso. As credenciais são descartadas quando o processo é concluído com êxito.
   >
   > 1. Uma conta de computador denominada AZUREADSSOACC (que representa o Azure AD) é criada em sua instância do Active Directory local.
   > 2. A chave de descriptografia Kerberos da conta do computador é compartilhada com segurança com o Azure AD.
   > 3. Os dois SPNs (nomes de entidade de serviço) Kerberos são criados para representar duas URLs que são usadas durante a entrada no Azure AD.

6. Na página **Pronto para configurar**, verifique se a caixa de seleção **Iniciar o processo de sincronização quando a configuração for concluída** está marcada. Em seguida, selecione **Configurar**.<br />

   ![Captura de tela da página Pronto para configurar](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image8.png)<br />
7. No portal do Azure AD, selecione **Azure Active Directory** e, em seguida, selecione **Azure AD Connect**.
8. Verifique estas configurações:
   * **Federação** está definida como **Desabilitada**.
   * **Logon único contínuo** está definido como **Habilitado**.
   * **Autenticação de passagem** está definida como **Habilitada**.<br />

   ![Captura de tela que mostra as configurações na seção de Entrada do usuário](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image9.png)<br />

Avançar. implantar métodos de autenticação adicionais:

1. Na portal do Azure, vá para **Azure Active Directory**  >  **Azure ad Connect** e, em seguida, selecione **autenticação de passagem**.
2. Na página **Autenticação de passagem**, selecione o botão **Baixar**.
3. Na página **Baixar agente**, selecione **Aceitar os termos e baixar**.

   Agentes de autenticação adicionais começam a ser baixados. Instale o agente de autenticação secundário em um servidor ingressado no domínio. 

   > [!NOTE]
   > O primeiro agente sempre é instalado no servidor do Azure AD Connect propriamente dito, como parte das alterações de configuração feitas na seção **Entrada do usuário** da ferramenta do Azure AD Connect. Instale os agentes de autenticação adicionais em um servidor separado. É recomendável que você tenha dois ou três agentes de autenticação adicionais disponíveis. 

4. Execute a instalação do agente de autenticação. Durante a instalação, você deve inserir as credenciais de uma conta de Administrador Global.

   ![Captura de tela que mostra o botão de instalação que você usa para executar o pacote do agente de autenticação Microsoft Azure AD Connect.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image11.png)

   ![Captura de tela que mostra a página de entrada da Microsoft.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image12.png)

5. Quando o agente de autenticação estiver instalado, você poderá voltar para a página de integridade do agente de autenticação de passagem para verificar o status dos agentes adicionais.

Vá para [Testes e próximas etapas](#testing-and-next-steps).

> [!IMPORTANT]
> Ignore a seção **opção B: alternar da Federação para a autenticação de passagem usando Azure ad Connect e o PowerShell**. As etapas nesta seção não se aplicarão se você tiver escolhido a opção A para alterar o método de entrada para autenticação de passagem e habilitar SSO contínuo. 

#### <a name="option-b-switch-from-federation-to-pass-through-authentication-by-using-azure-ad-connect-and-powershell"></a>Opção B: alternar da Federação para a autenticação de passagem usando Azure AD Connect e o PowerShell

Use esta opção se você não tiver configurado inicialmente seus domínios federados usando o Azure AD Connect.

Primeiro, habilite a autenticação de passagem:

1. No Servidor do Azure AD Connect, abra o Assistente do Azure AD Connect.
2. Selecione **alterar entrada do usuário** e, em seguida, selecione **Avançar**.
3. Na página **Conectar ao Azure AD**, insira o nome de usuário e a senha de uma conta de Administrador Global.
4. Na página **Entrada do usuário**, selecione o botão **Autenticação de passagem**. Selecione **Habilitar o logon único** e selecione **Avançar**.
5. Na página **Habilitar logon único**, insira as credenciais de uma conta de administrador de domínio e, em seguida, selecione **Avançar**.

   > [!NOTE]
   > Credenciais da conta de administrador de domínio são necessárias para habilitar SSO contínuo. O processo conclui as seguintes ações, que exigem essas permissões elevadas. As credenciais da conta do Administrador de Domínio não são armazenadas no Azure AD Connect nem no Azure AD. As credenciais da conta de Administrador de Domínio são usadas somente para ativar o recurso. As credenciais são descartadas quando o processo é concluído com êxito.
   > 
   > 1. Uma conta de computador denominada AZUREADSSOACC (que representa o Azure AD) é criada em sua instância do Active Directory local.
   > 2. A chave de descriptografia Kerberos da conta do computador é compartilhada com segurança com o Azure AD.
   > 3. Os dois SPNs (nomes de entidade de serviço) Kerberos são criados para representar duas URLs que são usadas durante a entrada no Azure AD.

6. Na página **Pronto para configurar**, verifique se a caixa de seleção **Iniciar o processo de sincronização quando a configuração for concluída** está marcada. Em seguida, selecione **Configurar**.<br />

   ![Captura de tela que mostra a página Pronto para configurar e o botão Configurar](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image18.png)<br />
   As etapas a seguir ocorrem quando você seleciona **Configurar**:

   1. O primeiro agente de autenticação de passagem é instalado.
   2. O recurso de passagem é habilitado.
   3. O SSO contínuo é habilitado.

7. Verifique estas configurações:
   * **Federação** é definida como **Habilitada**.
   * **Logon único contínuo** está definido como **Habilitado**.
   * **Autenticação de passagem** está definida como **Habilitada**.
   
   ![Captura de tela que mostra as configurações a serem verificadas na seção de entrada do usuário.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image19.png)
8. Selecione **autenticação de passagem** e verifique se o status é **ativo**.<br />
   
   Se o agente de autenticação não estiver ativo, conclua algumas [etapas de solução de problemas](./tshoot-connect-pass-through-authentication.md) antes de continuar com o processo de conversão de domínio na próxima etapa. Você correrá o risco de causar uma interrupção de autenticação se converter seus domínios antes de validar que os agentes de autenticação de passagem estão instalados com êxito e que têm o status **Ativo** no portal do Azure.

Em seguida, implante agentes de autenticação adicionais:

1. Na portal do Azure, vá para **Azure Active Directory**  >  **Azure ad Connect** e, em seguida, selecione **autenticação de passagem**.
2. Na página **Autenticação de passagem**, selecione o botão **Baixar**. 
3. Na página **Baixar agente**, selecione **Aceitar os termos e baixar**.
 
   O agente de autenticação começa a ser baixado. Instale o agente de autenticação secundário em um servidor ingressado no domínio.

   > [!NOTE]
   > O primeiro agente sempre é instalado no servidor do Azure AD Connect propriamente dito, como parte das alterações de configuração feitas na seção **Entrada do usuário** da ferramenta do Azure AD Connect. Instale os agentes de autenticação adicionais em um servidor separado. É recomendável que você tenha dois ou três agentes de autenticação adicionais disponíveis.
 
4. Execute a instalação do agente de autenticação. Durante a instalação, você deve inserir as credenciais de uma conta de Administrador Global.<br />

   ![Captura de tela que mostra o botão Instalar na página do Pacote do Agente de Autenticação do Microsoft Azure AD Connect](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image23.png)<br />
   ![Captura de tela que mostra a página de entrada](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image24.png)<br />
5. Após o agente de autenticação estar instalado, você pode voltar para a página de integridade do agente de autenticação de passagem para verificar o status dos agentes adicionais.

Neste ponto, autenticação federada ainda está ativa e operacional para seus domínios. Para continuar com a implantação, você deve converter cada domínio de identidade federada na identidade gerenciada para que a autenticação de passagem comece a atender a solicitações de autenticação para o domínio.

Você não precisa converter todos os domínios ao mesmo tempo. Você pode optar por começar com um domínio de teste em seu locatário de produção ou iniciar com seu domínio que tem o menor número de usuários.

Conclua a conversão usando o módulo PowerShell do Azure AD:

1. No PowerShell, entre no Azure AD usando uma conta de Administrador Global.
2. Para converter o primeiro domínio, execute o comando a seguir:
 
   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```
 
3. No portal do AD do Azure, selecione **Azure Active Directory**  >  **Azure ad Connect**.
4. Depois de converter todos os seus domínios federados, verifique estas configurações:
   * **Federação** está definida como **Desabilitada**.
   * **Logon único contínuo** está definido como **Habilitado**.
   * **Autenticação de passagem** está definida como **Habilitada**.<br />

   ![Captura de tela que mostra as configurações na seção de entrada do usuário no portal do Azure AD.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image26.png)<br />

## <a name="testing-and-next-steps"></a>Testes e próximas etapas

Conclua as seguintes tarefas para verificar a autenticação de passagem e concluir o processo de conversão.

### <a name="test-pass-through-authentication"></a>Testar a autenticação de passagem 

Quando seu locatário usava identidade federada, os usuários eram redirecionados da página de entrada do Azure AD para seu ambiente do AD FS. Agora que o locatário está configurado para usar autenticação de passagem, em vez da autenticação federada, os usuários não são redirecionados para o AD FS. Em vez disso, os usuários entram diretamente na página de entrada do Azure AD.

Para testar a autenticação de passagem:

1. Abra o Internet Explorer no modo InPrivate para que o SSO contínuo não o conecte automaticamente.
2. Vá para a página de entrada do Office 365 ( [https://portal.office.com](https://portal.office.com/) ).
3. Insira um nome UPN do usuário e, em seguida, selecione **Avançar**. Insira o nome UPN de um usuário híbrido que foi sincronizado da instância do Active Directory local e que anteriormente usava autenticação federada. Será exibida uma página na qual você insere o nome de usuário e a senha:

   ![Captura de tela que mostra a página de entrada em que você insere um nome de usuário](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image27.png)

   ![Captura de tela que mostra a página de entrada em que você insere uma senha](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image28.png)

4. Depois de inserir a senha e selecione **Entrar**, você será redirecionado para o portal do Office 365.

   ![Captura de tela que mostra o portal do Office 365](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image29.png)

### <a name="test-seamless-sso"></a>Testar o SSO contínuo

Para testar o SSO contínuo:

1. Entre em um computador ingressado no domínio que está conectado à rede corporativa.
2. No Internet Explorer ou no Chrome, vá para uma das URLs a seguir (substitua "contoso" pelo seu domínio):

   * https:\/\/myapps.microsoft.com/contoso.com
   * https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   O usuário é brevemente redirecionado à página de entrada do Azure AD, que mostra a mensagem "Tentando conectá-lo." Não é solicitado que o usuário informe um nome de usuário ou senha.<br />

   ![Captura de tela que mostra a página de entrada e a mensagem do Azure AD](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image30.png)<br />
3. O usuário é redirecionado e conectado com êxito ao painel de acesso:

   > [!NOTE]
   > O SSO contínuo funciona em serviços Microsoft 365 que dão suporte à dica de domínio (por exemplo, myapps.microsoft.com/contoso.com). Atualmente, o portal.office.com (portal de Microsoft 365) não dá suporte a dicas de domínio. Os usuários precisarão inserir um nome UPN. Depois que um UPN tiver sido inserido, o SSO contínuo recuperará o tíquete Kerberos em nome do usuário. O usuário é conectado sem inserir uma senha.

   > [!TIP]
   > Considere implantar o [Ingresso no Azure AD híbrido no Windows 10](../devices/overview.md) para uma experiência de SSO aprimorada.

### <a name="remove-the-relying-party-trust"></a>Remover o objeto de confiança de terceira parte confiável

Depois de validar que todos os usuários e clientes são autenticados com êxito por meio do Azure AD, é seguro remover o Microsoft 365 confiança de terceira parte confiável.

Se você não usa o AD FS para outras finalidades (ou seja, para outros objetos de confiança de terceira parte confiável), é seguro encerrar o AD FS neste momento.

### <a name="rollback"></a>Reversão

Se você descobrir um grande problema e não puder resolvê-lo rapidamente, poderá reverter a solução para federação.

Veja a documentação de implantação e o design da federação para seus detalhes de implantação específicos. O processo deve envolver estas tarefas:

* Converta domínios gerenciados em autenticação federada usando o cmdlet **Convert-MSOLDomainToFederated**.
* Se necessário, configure regras de declarações adicionais.

### <a name="sync-userprincipalname-updates"></a>Sincronizar atualizações de userPrincipalName

Historicamente, atualizações ao atributo **UserPrincipalName**, que usa o serviço de sincronização do ambiente local, são bloqueadas, a menos que essas duas condições sejam verdadeiras:

* O usuário está em um domínio de identidade gerenciada (não federado).
* Não foi atribuída uma licença ao usuário.

Para saber como verificar ou habilitar esse recurso, veja [Sincronizar atualizações de userPrincipalName](./how-to-connect-syncservice-features.md).

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Substituir a chave de descriptografia do Kerberos de SSO contínuo

É importante sobrepor frequentemente a chave de descriptografia Kerberos da conta de computador AZUREADSSOACC (que representa o Azure AD). A conta de computador AZUREADSSOACC é criada na floresta do Active Directory local. É altamente recomendável que você substitua a chave de descriptografia do Kerberos pelo menos a cada 30 dias para alinhar á maneira como os membros do domínio do Active Directory enviam as alterações de senha. Não há nenhum dispositivo associado anexado ao objeto de conta de computador AZUREADSSOACC, portanto, você deverá executar a sobreposição manualmente.

Inicie a sobreposição da chave de descriptografia Kerberos de SSO contínuo no servidor local que está em execução no Azure AD Connect.

Para obter mais informações, veja [Como sobrepor a chave de descriptografia Kerberos da conta de computador AZUREADSSOACC?](./how-to-connect-sso-faq.md).

## <a name="monitoring-and-logging"></a>Monitoramento e registro em log

Monitore os servidores que executam os agentes de autenticação para manter a disponibilidade da solução. Além dos contadores de desempenho geral do servidor, os agentes de autenticação expõem objetos de desempenho que podem ajudá-lo a entender erros e estatísticas de autenticação.

Agentes de autenticação registram em log operações nos logs de eventos do Windows localizados em Application and Service Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin.

Você também pode ativar o registro em log para solução de problemas.

Para obter mais informações, veja [Solucionar problemas de autenticação de passagem do Azure Active Directory](./tshoot-connect-pass-through-authentication.md).

## <a name="next-steps"></a>Próximas etapas

* Aprenda os [Conceitos de design do Azure AD Connect](plan-connect-design-concepts.md).
* Escolha a [autenticação correta](./choose-ad-authn.md).
* Saiba mais sobre [topologias com suporte](plan-connect-design-concepts.md).
