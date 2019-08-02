---
title: Implantar estações de trabalho gerenciadas pelo Azure-Azure Active Directory
description: Saiba como implantar estações de trabalho seguras e gerenciadas pelo Azure para reduzir o risco de violação devido a configuração ou comprometimento incorreta.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: be9e6374d92fbb7bb1c4b5a2a9e154119c5baf87
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377499"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Implantar uma estação de trabalho segura e gerenciada pelo Azure

Agora que você [entende as estações de trabalho seguras](concept-azure-managed-workstation.md), é hora de começar o processo de implantação. Com essas diretrizes, você usa perfis definidos para criar uma estação de trabalho que seja mais segura desde o início.

![Implantação de uma estação de trabalho segura](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Você deve selecionar um perfil para poder implantar a solução. Você pode usar vários perfis simultaneamente em uma implantação e atribuí-los com marcas ou grupos.
> [!NOTE]
> Aplique qualquer um dos perfis conforme necessário por seus requisitos. Você pode mover para outro perfil atribuindo-o ao Intune.

| Perfil | Baixa | Avançado | Alto | Especializado | Protegido | Isolado |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Usuário no Azure AD | Sim | Sim | Sim | Sim | Sim | Sim |
| Gerenciado pelo Intune | Sim | Sim | Sim | Sim | Sim | Sim |
| Dispositivo-Azure AD registrado | Sim |  |  |  |  | |   |
| Dispositivo-ingressado no Azure AD |   | Sim | Sim | Sim | Sim | Sim |
| Linha de base de segurança do Intune aplicada |   | Sim <br> Elevar | Sim <br> (HighSecurity) | Sim <br> (NCSC) | Sim <br> Devidamente |  N/A |
| O hardware atende aos padrões seguros do Windows 10 |   | Sim | Sim | Sim | Sim | Sim |
| Microsoft defender ATP habilitado |   | Sim  | Sim | Sim | Sim | Sim |
| Remoção de direitos de administrador |   |   | Sim  | Sim | Sim | Sim |
| Implantação usando o Microsoft AutoPilot |   |   | Sim  | Sim | Sim | Sim |
| Aplicativos instalados somente pelo Intune |   |   |   | Sim | Sim |Sim |
| URLs restritas à lista aprovada |   |   |   | Sim | Sim |Sim |
| Internet bloqueado (entrada/saída) |   |   |   |  |  |Sim |

## <a name="license-requirements"></a>Requisitos de licença

Os conceitos abordados neste guia pressupõem que você tenha Microsoft 365 Enterprise E5 ou um SKU equivalente. Algumas das recomendações neste guia podem ser implementadas com SKUs inferiores. Para obter mais informações, consulte [Microsoft 365 Enterprise Licensing](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Para automatizar o provisionamento de licenças, considere o [Licenciamento baseado em grupo](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) para seus usuários.

## <a name="azure-active-directory-configuration"></a>Configuração de Azure Active Directory

O Azure Active Directory (AD do Azure) gerencia usuários, grupos e dispositivos para suas estações de trabalho de administrador. Você precisa habilitar serviços de identidade e recursos com uma [conta de administrador](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

Quando você cria a conta de administrador de estação de trabalho segura, você expõe a conta para sua estação de trabalho atual. Certifique-se de usar um dispositivo seguro conhecido para fazer essa configuração inicial e todas as configurações globais. Para reduzir a exposição do ataque à primeira experiência, considere seguir as [orientações para evitar infecções por malware](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection).

Você também deve exigir a autenticação multifator, pelo menos para seus administradores. Consulte [implantar a MFA baseada em nuvem](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) para obter diretrizes de implementação.

### <a name="azure-ad-users-and-groups"></a>Usuários e grupos do Azure AD

1. No portal do Azure, navegue até **Azure Active Directory** > **usuários** > **novo usuário**.
1. Crie o administrador do dispositivo seguindo as etapas no [tutorial criar usuário](https://docs.microsoft.com/Intune/quickstart-create-user).
1. Digite:
   * **Nome** -administrador de estação de trabalho segura
   * **Nome de usuário** - `secure-ws-admin@identityitpro.com`
   *  - **Administrador limitado** de função de diretório e selecione a função de **administrador do Intune** .
1. Selecione **Criar**.

Em seguida, crie dois grupos: usuários de estação de trabalho e dispositivos de estação de trabalho.

No portal do Azure, navegue até **Azure Active Directory** > **grupos** > **grupo novo**.

1. Para o grupo usuários da estação de trabalho, talvez você queira configurar o [Licenciamento baseado em grupo](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) para automatizar o provisionamento de licenças para os usuários.
1. Para o grupo usuários da estação de trabalho, digite:
   * **Tipo de grupo** -segurança
   * **Nome do grupo** -usuários de estação de trabalho segura
   * **Tipo de associação** atribuído
1. Adicione seu usuário administrador de estação de trabalho segura:`secure-ws-admin@identityitpro.com`
1. Você pode adicionar outros usuários que estarão Gerenciando estações de trabalho seguras.
1. Selecione **Criar**.

1. Para o grupo dispositivos de estação de trabalho, digite:
   * **Tipo de grupo** -segurança
   * **Nome do grupo** -estações de trabalho seguras
   * **Tipo de associação** atribuído
1. Selecione **Criar**.

### <a name="azure-ad-device-configuration"></a>Configuração de dispositivo do Azure AD

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Especificar quem pode ingressar dispositivos no Azure AD

Defina a configuração de dispositivos no Active Directory para permitir que seu grupo de segurança administrativa ingresse dispositivos no seu domínio. Para definir essa configuração do portal do Azure:

1. Vá para **Azure Active Directory** > **dispositivos** > **configurações de dispositivo**.
1. Escolha **selecionado** em **usuários pode ingressar dispositivos no Azure ad**e, em seguida, selecione o grupo "proteger usuários de estação de trabalho".

#### <a name="removal-of-local-admin-rights"></a>Remoção de direitos de administrador local

Esse método requer que os usuários das estações de trabalho VIP, DevOps e de nível seguro não tenham direitos de administrador em seus computadores. Para definir essa configuração do portal do Azure:

1. Vá para **Azure Active Directory** > **dispositivos** > **configurações de dispositivo**.
1. Selecione **nenhum** em **Administradores locais adicionais em dispositivos ingressados no Azure ad**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Exigir autenticação multifator para unir dispositivos

Para reforçar ainda mais o processo de unir dispositivos ao Azure AD:

1. Vá para **Azure Active Directory** > **dispositivos** > **configurações de dispositivo**.
1. Selecione **Sim** em **exigir autenticação multifator para ingressar em dispositivos**.
1. Clique em **Salvar**.

#### <a name="configure-mdm"></a>Configurar o MDM

No Portal do Azure:

1. Navegue até **Azure Active Directory** > **mobilidade (MDM e MAM)**  > **Microsoft Intune**.
1. Altere a configuração de **escopo do usuário do MDM** para **All**.
1. Clique em **Salvar**.

Essas etapas permitem que você gerencie qualquer dispositivo com o Intune. Para obter mais informações, [consulte início rápido do Intune: Configure o registro automático para dispositivos](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment)Windows 10. Você cria políticas de conformidade e configuração do Intune em uma etapa futura.

#### <a name="azure-ad-conditional-access"></a>Acesso condicional do Azure AD

O acesso condicional do Azure AD pode ajudar a restringir tarefas administrativas privilegiadas a dispositivos em conformidade. Membros predefinidos do grupo de **usuários de estação de trabalho segura** são necessários para executar a autenticação multifator ao entrar em aplicativos de nuvem. Uma prática recomendada é excluir contas de acesso de emergência da política. Para obter mais informações, consulte [gerenciar contas de acesso de emergência no Azure ad](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

Para configurar o acesso condicional do portal do Azure:

1. Vá para **Azure Active Directory** > **acesso** > condicional**nova política**.
1. Digite:
   * **Nome** -política necessária de dispositivo seguro
   * Atribuições
     * **Usuários e grupos**
       * Incluir- **usuários e grupos** -selecione o grupo de **usuários de estação de trabalho segura** criado anteriormente.
       * Exclude- **usuários e grupos** -selecione as contas de acesso de emergência de sua organização.
     * **Aplicativos de nuvem** – inclua **todos os aplicativos de nuvem**.
    * Controles de acesso
      * **Grant** -selecione o botão de opção **conceder acesso** .
        * **Exigir autenticação**multifator.
        * **Exigir que o dispositivo seja marcado como compatível**.
        * Para vários controles- **exigem todos os controles selecionados**.
    * Habilitar política- **ativada**.

Você tem a opção de criar políticas que bloqueiam países onde os usuários não acessarão os recursos da empresa. Para obter mais informações sobre políticas de acesso condicional com base no local IP, consulte [a condição local em Azure Active Directory acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition).

## <a name="intune-configuration"></a>Configuração do Intune

### <a name="configure-enrollment-status"></a>Configurar o status do registro

É importante garantir que sua estação de trabalho segura seja um dispositivo de limpeza confiável. Ao comprar novos dispositivos, você pode insistir que eles são conjuntos de fábrica para o [Windows 10 pro no modo S](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode), o que limita a exposição às vulnerabilidades durante o gerenciamento da cadeia de fornecedores. Depois de receber um dispositivo do seu fornecedor, você pode usar o AutoPilot para alterá-lo do modo S. As diretrizes a seguir fornecem detalhes sobre como aplicar o processo de transformação.

Para garantir que os dispositivos estejam totalmente configurados antes do uso, o Intune fornece um meio para **bloquear o uso do dispositivo até que todos os aplicativos e perfis sejam instalados**.

No **portal do Azure**:
1. Vá para **Microsoft Intune** > **registro** >  >  > do dispositivo inscrição no registro do Windows configurações padrão da página de status. > 
1. Defina **mostrar progresso da instalação do perfil de aplicativo** como **Sim**.
1. Defina **bloquear o uso do dispositivo até que todos os aplicativos e perfis sejam instalados** em **Sim**.

### <a name="create-an-autopilot-deployment-profile"></a>Criar um perfil de implantação do AutoPilot

Depois de criar um grupo de dispositivos, você deve criar um perfil de implantação para configurar os dispositivos de piloto automático.

No Intune no portal do Azure:

1. Selecione **registro** > de dispositivo**Windows registro** > perfis > de implantação**Criar perfil**.
1. Digite:
   * Nome- **perfil de implantação de estação de trabalho segura**.
   * Descrição- **implantação de estações de trabalho seguras**.
   * Defina **converter todos os dispositivos de destino para o piloto automático** para **Sim**. Essa configuração garante que todos os dispositivos na lista sejam registrados com o serviço de implantação do AutoPilot. Permitir 48 horas para o registro ser processado.
1. Selecione **Avançar**.
   * Para o **modo de implantação**, escolha autoimplantação **(visualização)** . Os dispositivos com esse perfil estão associados ao usuário que registra o dispositivo. As credenciais do usuário são necessárias para registrar o dispositivo. É essencial observar que a implantação de um dispositivo no modo de **implantação automática** permitirá que você implante laptops em um modelo compartilhado. Nenhuma atribuição de usuário ocorrerá até que o dispositivo seja atribuído a um usuário pela primeira vez. Como resultado, qualquer política de usuário, como o BitLocker, não será habilitada até que uma atribuição de usuário seja concluída. Para obter mais detalhes sobre como fazer logon em um dispositivo protegido, consulte [perfis selecionados](https://docs.microsoft.com/intune/device-profile-assign).
   * A caixa **ingressar no Azure ad como** deve mostrar o **ingresso no Azure ad** e ficar esmaecida.
   * Selecione seu Langugage (região), tipo de conta de usuário **padrão**. 
1. Selecione **Avançar**.
   * Selecione uma marca de escopo se você tiver pré-configurado uma.
1. Selecione **Avançar**.
1. Escolha **atribuições** > **atribuir a** > **grupos selecionados**. Em **Selecionar grupos a serem incluídos**, escolha **proteger usuários de estação de trabalho**.
1. Selecione **Avançar**.
1. Selecione **Criar** para criar o perfil. O perfil de implantação do AutoPilot agora está disponível para ser atribuído aos dispositivos.

O registro de dispositivo no AutoPilot fornece uma experiência de usuário diferente com base no tipo de dispositivo e na função. Em nosso exemplo de implantação, ilustramos um modelo em que os dispositivos protegidos são implantados em massa e podem ser compartilhados, mas quando usados pela primeira vez, o dispositivo é atribuído a um usuário. Para obter mais informações, consulte [registro do dispositivo de piloto automático do Intune](https://docs.microsoft.com/intune/device-enrollment).

### <a name="configure-windows-update"></a>Configurar Windows Update

Manter o Windows 10 atualizado é uma das coisas mais importantes que você pode fazer. Para manter o Windows em um estado seguro, você implanta um anel de atualização para gerenciar o ritmo que as atualizações são aplicadas às estações de trabalho. 

Esta orientação recomenda que você crie um novo anel de atualização e altere as seguintes configurações padrão:

No Portal do Azure:

1. Vá para **Microsoft Intune** > atualizações > de software os**anéis de atualização do Windows 10**.
1. Digite:
   * Nome – **atualizações de estação de trabalho gerenciada pelo Azure**
   * Canal de manutenção- **Windows Insider-rápido**
   * Adiamento de atualização de qualidade (dias)- **3**
   * Período de adiamento da atualização de recurso (dias)- **3**
   * Comportamento de atualização automática- **instalação e reinicialização automáticas sem controle do usuário final**
   * Bloquear o usuário de pausar atualizações do Windows- **Bloquear**
   * Exigir que a aprovação do usuário seja reiniciada fora do horário de trabalho- **obrigatório**
   * Permitir que o usuário reinicie (reinício envolvido)- **obrigatório**
   * Fazer a transição de usuários para a reinicialização envolvida após uma reinicialização automática (dias)- **3**
   * Adiar lembrete de reinício estabelecido (dias)- **3**
   * Definir prazo para reinicializações pendentes (dias)- **3**

1. Selecione **Criar**.
1. Na guia **atribuições** , adicione o grupo de **estações de trabalho seguras** .

Para obter informações adicionais sobre políticas de Windows Update, consulte [CSP de política-atualização](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Integração do Intune do Windows Defender ATP

O Windows Defender ATP e Microsoft Intune trabalham juntos para ajudar a evitar violações de segurança. Eles também podem limitar o impacto de violações. Os recursos do ATP fornecem detecção de ameaças em tempo real, bem como habilitam a auditoria extensiva e o registro em log dos dispositivos de ponto de extremidade.

Para configurar a integração do Windows Defender ATP e do Intune, vá para o portal do Azure.

1. Navegue até **Microsoft Intune** > conformidade > do dispositivo**Windows Defender ATP**.
1. Na etapa 1 em Configurando o **Windows Defender ATP**, selecione **conectar o Windows defender ATP a Microsoft Intune na central de segurança do Windows Defender**.
1. Na central de segurança do Windows Defender:
   1. Selecione **configurações** > **recursos avançados**.
   1. Para **Microsoft Intune conexão**, escolha **ativado**.
   1. Selecione **salvar preferências**.
1. Depois que uma conexão for estabelecida, retorne ao Intune e selecione **Atualizar** na parte superior.
1. Defina **conectar dispositivos Windows versão 10.0.15063 e superior ao Windows Defender ATP** como **ativado**.
1. Clique em **Salvar**.

Para obter mais informações, consulte [proteção avançada contra ameaças do Windows Defender](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>Concluir proteção de perfil de estação de trabalho

Para concluir com êxito a proteção da solução, baixe e execute o script apropriado. Localize os links de download para o **nível de perfil**desejado:

| Perfil | Local de download | Nome do Arquivo |
| --- | --- | --- |
| Baixa segurança | N/D |  N/D |
| Segurança aprimorada | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809).ps1 |
| Alta segurança  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Especializado | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC-Windows10 (1803) SecurityBaseline. ps1 |
| Conformidade especializada * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Protegido | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\*A conformidade especializada é um script que impõe a configuração especializada fornecida no NCSC Windows10 SecurityBaseline.

Depois que o script for executado com êxito, você poderá fazer atualizações para perfis e políticas no Intune. Os scripts para perfis avançados e seguros criam políticas e perfis para você, mas você deve atribuir a política ao seu grupo de **estações de trabalho seguras** .

* Aqui está onde você pode encontrar os perfis de configuração de dispositivo do Intune criados pelos scripts: **Portal do Azure** **Microsoft Intune**perfis deconfiguração > de dispositivo. >  > 
* Aqui está onde você pode encontrar as políticas de conformidade do dispositivo do Intune criadas pelos scripts: **Portal do Azure** **Microsoft Intune**políticas deconformidade > do dispositivo. >  > 

Para examinar as alterações feitas pelos scripts, você pode exportar os perfis. Dessa forma, você pode determinar a proteção adicional que pode ser necessária, conforme descrito na [documentação do SECCON](https://docs.microsoft.com/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework).

Execute o script `DeviceConfiguration_Export.ps1` de exportação de dados do Intune do [repositório DeviceConfiguration GiuHub](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) para exportar todos os perfis atuais do Intune.

## <a name="additional-configurations-and-hardening-to-consider"></a>Configurações e proteção adicionais a serem consideradas

Seguindo as orientações aqui, você implantou uma estação de trabalho segura. No entanto, você também deve considerar controles adicionais. Por exemplo:

* limitar o acesso a navegadores alternativos
* permitir HTTP de saída
* bloquear selecionar sites
* definir permissões para executar scripts do PowerShell personalizados

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Definir regras no provedor de serviços de configuração de firewall (CSP)

Você pode fazer alterações adicionais no gerenciamento de regras de entrada e saída, conforme necessário, para seus pontos de extremidade permitidos e bloqueados. Ao continuar a proteger a estação de trabalho segura, você pode afrouxar a restrição que nega todo o tráfego de entrada e saída. Você pode adicionar sites de saída permitidos para incluir sites confiáveis e comuns. Para obter mais informações, consulte [serviço de configuração de firewall](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp).

As recomendações padrão restritas são:

* Negar toda a entrada
* Negar todas as saídas

O projeto Spamhaus mantém [a lista de bloqueios de domínio (duplo)](https://www.spamhaus.org/dbl/): um bom recurso para usar como um ponto de partida para bloquear sites.

### <a name="manage-local-applications"></a>Gerenciar aplicativos locais

A estação de trabalho segura passa para um estado verdadeiramente protegido quando os aplicativos locais são removidos, incluindo aplicativos de produtividade. Aqui, você adiciona o Chrome como o navegador padrão e usa o Intune para limitar a capacidade do usuário de modificar o navegador e seus plug-ins.

#### <a name="deploy-applications-using-intune"></a>Implantar aplicativos usando o Intune

Em algumas situações, aplicativos como o navegador Google Chrome são necessários na estação de trabalho protegida. O exemplo a seguir fornece instruções para instalar o Chrome em dispositivos nas **estações de trabalho seguras**do grupo de segurança.

1. Baixe o pacote Chrome do instalador offline [para Windows 64-bit](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Extraia os arquivos e anote o local do `GoogleChromeStandaloneEnterprise64.msi` arquivo.
1. Na **portal do Azure** navegueaté **Microsoft Intune** > aplicativos**cliente** > aplicativos > **Adicionar**.
1. Em **tipo de aplicativo**, escolha **linha de negócios**.
1. Em **arquivo de pacote do aplicativo**, `GoogleChromeStandaloneEnterprise64.msi` selecione o arquivo do local extraído e selecione **OK**.
1. Em **informações do aplicativo**, forneça uma descrição e um Publicador. Selecione **OK**.
1. Selecione **Adicionar**.
1. Na guia **atribuições** , selecione **disponível para dispositivos registrados** em tipo de **atribuição**.
1. Em **grupos incluídos**, adicione o grupo de **estações de trabalho seguras** .
1. Selecione **OK** e, em seguida, **Salvar**.

Para obter mais diretrizes sobre como definir as configurações do Chrome, consulte [gerenciar navegador Chrome com Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Configurando o portal da empresa para aplicativos personalizados

Em um modo seguro, a instalação do aplicativo é restrita ao portal da empresa do Intune. No entanto, a instalação do portal requer acesso ao Microsoft Store. Em sua solução segura, você pode disponibilizar o portal da empresa para todos os dispositivos por meio de um modo offline.

Uma cópia gerenciada pelo Intune do [portal da empresa](https://docs.microsoft.com/Intune/store-apps-company-portal-app) fornece acesso sob demanda a ferramentas adicionais que você pode enviar por push para os usuários das estações de trabalho protegidas.

Talvez seja necessário instalar aplicativos do Windows de 32 bits ou outros aplicativos cuja implantação exija preparativos especiais. Nesses casos, a [ferramenta de preparação de conteúdo do Microsoft Win32](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) pode fornecer um arquivo de formato `.intunewin` pronto para uso para instalação.

### <a name="use-powershell-to-create-custom-settings"></a>Usar o PowerShell para criar configurações personalizadas

Você também pode usar o PowerShell para estender os recursos de gerenciamento de host. Este script de exemplo configura um plano de fundo padrão no host. É um recurso que também está disponível por meio da portal do Azure e dos perfis. O script de exemplo serve apenas para ilustrar a funcionalidade do PowerShell.

Talvez seja necessário configurar alguns controles e configurações personalizados em suas estações de trabalho seguras. Este exemplo altera o plano de fundo da estação de trabalho usando a capacidade do PowerShell de identificar facilmente o dispositivo como uma estação de trabalho segura e pronta para uso.

O script [SetDesktopBackground. ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) do Microsoft Scripting Center permite que o Windows carregue essa [imagem de plano de fundo genérica e gratuita](https://i.imgur.com/OAJ28zO.png) na inicialização.

1. Baixe o script em um dispositivo local.
1. Atualize o customerXXXX e o local de download da imagem de plano de fundo. Em nosso exemplo, substituimos customerXXXX em planos de fundo.  
1. Navegue até o **portal do Azure** > **Microsoft Intune** > **configuração de dispositivo**scriptsdo > PowerShell adicionar. > 
1. Forneça um **nome** para o script e especifique o **local do script**.
1. Selecione **Configurar**.
   1. Defina **executar este script usando as credenciais conectadas** como **Sim**.
   1. Selecione **OK**.
1. Selecione **Criar**.
1. Selecione **atribuições** > **Selecionar grupos**.
   1. Adicione as estações de **trabalho seguras**do grupo de segurança.
   1. Clique em **Salvar**.

## <a name="enroll-and-validate-your-first-device"></a>Registrar e validar seu primeiro dispositivo

1. Para registrar seu dispositivo, você precisará das seguintes informações:
   * **Número de série** -encontrado no chassi do dispositivo.
   * **ID do produto Windows** -encontrado em **sistema** > **sobre** no menu de configurações do Windows.
   * Você pode executar [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) para obter um arquivo de hash CSV com todas as informações necessárias para o registro do dispositivo.
   
     Execute `Get-WindowsAutoPilotInfo – outputfile device1.csv` para gerar as informações como um arquivo CSV que você pode importar no Intune.

     > [!NOTE]
     > O script requer direitos elevados. Ele é executado como assinado remotamente. O `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` comando permite que o script seja executado corretamente.

   * Você pode coletar essas informações entrando em um dispositivo Windows 10 versão 1809 ou superior. O revendedor de hardware também pode fornecer essas informações.
1. Na **portal do Azure**, acesse **Microsoft Intune** > **registro** > de dispositivo dispositivos de**registro** > do Windows **-gerenciar dispositivos do Windows AutoPilot**.
1. Selecione **importar** e escolha o arquivo CSV.
1. Adicione o dispositivo ao grupo de segurança **estações de trabalho seguras** .
1. No dispositivo Windows 10 que você deseja configurar, vá para **configurações** > do Windows**Atualizar & segurança** > **recuperação**.
   1. Escolha **começar** em **redefinir este computador**.
   1. Siga os prompts para redefinir e reconfigurar o dispositivo com as políticas de perfil e conformidade configuradas.

Depois de configurar o dispositivo, conclua uma revisão e verifique a configuração. Confirme se o primeiro dispositivo está configurado corretamente antes de continuar a implantação.

## <a name="assign-and-monitor"></a>Atribuir e monitorar

Para atribuir dispositivos e usuários, você precisa mapear os [perfis selecionados](https://docs.microsoft.com/intune/device-profile-assign) para seu grupo de segurança. Todos os novos usuários que precisam de permissões para o serviço também devem ser adicionados ao grupo de segurança.

Você pode monitorar perfis com o [monitoramento de perfil do Intune](https://docs.microsoft.com/intune/device-profile-monitor).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Microsoft Intune](https://docs.microsoft.com/intune/index).
* Entenda o [Azure ad](https://docs.microsoft.com/azure/active-directory/index).
