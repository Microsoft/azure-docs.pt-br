---
title: Habilitar o acesso remoto para Power BI com o Azure Proxy de Aplicativo do AD
description: Aborda as noções básicas sobre como integrar um Power BI local com o Proxy de Aplicativo do AD do Azure.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/25/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9b0cce11abe1076d26ac8d4c4dc57c9b57c4737
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625574"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Habilitar acesso remoto ao Power BI Mobile com o Proxy de Aplicativo do Azure AD

Este artigo discute como usar o Azure Proxy de Aplicativo do AD para habilitar o Power BI aplicativo móvel para se conectar ao Servidor de Relatórios do Power BI (PBIRS) e SQL Server Reporting Services (SSRS) 2016 e posterior. Por meio dessa integração, os usuários que estão fora da rede corporativa podem acessar seus relatórios de Power BI do aplicativo Power BI Mobile e ser protegidos pela autenticação do Azure AD. Essa proteção inclui [benefícios de segurança](application-proxy-security.md#security-benefits) como o acesso condicional e a autenticação multifator.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já implantou os serviços de relatório e o [proxy de aplicativo habilitado](application-proxy-add-on-premises-application.md).

- Habilitar o proxy de aplicativo requer a instalação de um conector em um Windows Server e a conclusão dos [pré-requisitos](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) para que o conector possa se comunicar com os serviços do Azure AD.
- Ao publicar Power BI, recomendamos que você use os mesmos domínios internos e externos. Para saber mais sobre domínios personalizados, consulte [trabalhando com domínios personalizados no proxy de aplicativo](./application-proxy-configure-custom-domain.md).
- Essa integração está disponível para o **Power bi Mobile aplicativo Ios e Android** .

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Etapa 1: configurar a delegação restrita de Kerberos (KCD)

Para aplicativos locais que usam a autenticação do Windows, você pode obter SSO (logon único) usando o protocolo de autenticação Kerberos e um recurso chamado KCD (delegação restrita de Kerberos). Quando configurado, o KCD permite que o conector do proxy de aplicativo obtenha um token do Windows para um usuário, mesmo que o usuário não tenha entrado no Windows diretamente. Para saber mais sobre o KCD, confira [visão geral da delegação restrita de Kerberos](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)) e [delegação restrita de Kerberos para logon único em seus aplicativos com o proxy de aplicativo](application-proxy-configure-single-sign-on-with-kcd.md).

Não há muito o que configurar no lado do Reporting Services. Apenas certifique-se de ter um SPN (nome da entidade de serviço) válido para permitir que a autenticação Kerberos adequada ocorra. Verifique também se o servidor de Reporting Services está habilitado para autenticação de negociação.

Para configurar o KCD para o Reporting Services, continue com as etapas a seguir.

### <a name="configure-the-service-principal-name-spn"></a>Configurar o SPN (nome da entidade de serviço)

O SPN é um identificador exclusivo para um serviço que usa a autenticação Kerberos. Você precisará verificar se tem um SPN HTTP adequado presente para seu servidor de relatório. Para obter informações sobre como configurar o SPN (Nome da Entidade de Serviço) adequado para seu servidor de relatório, consulte [Registrar um SPN (Nome da Entidade de Serviço) para um servidor de relatório](/sql/reporting-services/report-server/register-a-service-principal-name-spn-for-a-report-server).
Você pode verificar se o SPN foi adicionado executando o comando Setspn com a opção -L. Para saber mais sobre esse comando, confira [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx).

### <a name="enable-negotiate-authentication"></a>Habilitar autenticação de negociação

Para habilitar um servidor de relatório para usar a autenticação Kerberos, configure o tipo de autenticação do servidor de relatório para ser RSWindowsNegotiate. Defina essa configuração usando o arquivo de rsreportserver.config.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

Para obter mais informações, consulte [Modify a Reporting Services Configuration File (Modificar um arquivo de configuração de serviços)](/sql/reporting-services/report-server/modify-a-reporting-services-configuration-file-rsreportserver-config) e [Configurar a Autenticação do Windows no servidor de relatório](/sql/reporting-services/security/configure-windows-authentication-on-the-report-server).

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Verifique se o conector é confiável para delegação para o SPN adicionado à conta do pool de aplicativos Reporting Services
Configure o KCD para que o serviço de Proxy de Aplicativo do AD do Azure possa delegar identidades de usuário para a conta do pool de aplicativos Reporting Services. Configure a KCD habilitando o conector Proxy de Aplicativo para recuperar tíquetes Kerberos para seus usuários que foram autenticados no Azure AD. Em seguida, esse servidor passa o contexto para o aplicativo de destino ou Reporting Services nesse caso.

Para configurar o KCD, repita as seguintes etapas para cada computador do conector:

1. Entre em um controlador de domínio como um administrador de domínio e, em seguida, abra **Active Directory usuários e computadores**.
2. Encontre o computador em que o conector está em sendo executado.
3. Clique duas vezes no computador e selecione a guia **Delegação**.
4. Defina as configurações de delegações como **Confiar neste computador para delegação somente para os serviços especificados**. Em seguida, selecione **usar qualquer protocolo de autenticação**.
5. Selecione **Adicionar** e, depois, selecione **Usuários ou Computadores**.
6. Insira a conta de serviço que você está usando para Reporting Services. Esta é a conta que você adicionou o SPN para dentro da configuração do Reporting Services.
7. Clique em **OK**. Clique em **OK** novamente para salvar as alterações.

Para obter mais informações, consulte [delegação restrita de Kerberos para logon único em seus aplicativos com o proxy de aplicativo](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>Etapa 2: publicar serviços de relatório por meio do Azure Proxy de Aplicativo do AD

Você já pode configurar o Proxy de Aplicativo do Azure AD.

1. Publique os serviços de relatório por meio do proxy de aplicativo com as configurações a seguir. Para obter instruções detalhadas sobre como publicar um aplicativo por meio do Proxy de Aplicativo, confira [Publicar aplicativos usando o Proxy de Aplicativo do Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   - **URL interna**: Insira a URL para o servidor de relatório que o conector pode acessar na rede corporativa. Verifique se essa URL pode ser acessada no servidor no qual o conector está instalado. É considerada uma melhor prática usar um domínio de nível superior, como `https://servername/`, para evitar problemas com subcaminhos publicados no Proxy de Aplicativo. Por exemplo, use `https://servername/`, e não `https://servername/reports/` ou `https://servername/reportserver/`.
     > [!NOTE]
     > É recomendável usar uma conexão HTTPS segura para o servidor de relatório. Para saber mais, confira [Configurar conexões SSL em um servidor de relatórios do modo nativo](/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server).
   - **URL externa**: Insira a URL pública à qual o aplicativo móvel Power bi se conectará. Por exemplo, poderá se parecer com `https://reports.contoso.com` se um domínio personalizado for usado. Para usar um domínio personalizado, carregue um certificado para o domínio e aponte um registro DNS ao domínio padrão msappproxy.net para seu aplicativo. Para obter etapas detalhadas, confira [Trabalhar com domínios personalizados no Proxy de Aplicativo do Azure AD](application-proxy-configure-custom-domain.md).

   - **Método de pré-autenticação**: Azure Active Directory

2. Depois que seu aplicativo for publicado, defina as configurações de logon único com as seguintes etapas:

   a. Na página de aplicativo no portal, selecione **Logon único**.

   b. Para o **Modo de Logon Único**, selecione **Autenticação Integrada do Windows**.

   c. Defina o **SPN do Aplicativo Interno** para o valor que você definiu anteriormente.

   d. Escolha a **Identidade de Logon Delegada** para que o conector use em nome de seus usuários. Para saber mais, confira [Trabalhar com diferentes identidades de nuvem e locais](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities).

   e. Clique em **Salvar** para salvar as alterações.

Para concluir a configuração do aplicativo, vá para **a seção usuários e grupos** e atribua usuários para acessar este aplicativo.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>Etapa 3: modificar as URI de resposta para o aplicativo

Antes que o aplicativo móvel Power BI possa se conectar e acessar os serviços de relatório, você deve configurar o registro do aplicativo que foi criado automaticamente para você na etapa 2.

1. Na página **Visão geral** do Azure Active Directory, selecione **Registros de aplicativo**.
2. Na guia **todos os aplicativos** , pesquise o aplicativo criado na etapa 2.
3. Selecione o aplicativo e, depois, selecione **Autenticação**.
4. Adicione os seguintes URIs de Redirecionamento com base na plataforma que você está usando.

   Ao configurar o aplicativo para Power BI Mobile **Ios**, adicione os seguintes URIs de redirecionamento do tipo cliente público (Mobile & Desktop):
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`

   Ao configurar o aplicativo para Power BI Mobile **Android**, adicione os seguintes URIs de redirecionamento do tipo cliente público (Mobile & Desktop):
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D`
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > É necessário adicionar os URIs de Redirecionamento para que o aplicativo funcione corretamente. Se você estiver configurando o aplicativo para o Power BI Mobile iOS e Android, adicione o seguinte URI de redirecionamento do tipo cliente público (Mobile & Desktop) à lista de URIs de redirecionamento configurados para iOS: `urn:ietf:wg:oauth:2.0:oob` .

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>Etapa 4: conectar-se do aplicativo Power BI Mobile

1. No aplicativo móvel Power BI, conecte-se à sua instância do Reporting Services. Para fazer isso, insira a **URL externa** para o aplicativo que você publicou por meio do proxy de aplicativo.

   ![Power BI aplicativo móvel com a URL externa](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. Selecione **Conectar**. Você será direcionado para a página de entrada do Azure Active Directory.

3. Insira credenciais válidas para o seu usuário e selecione **Entrar**. Você verá os elementos do seu servidor de Reporting Services.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>Etapa 5: configurar a política do Intune para dispositivos gerenciados (opcional)

Você pode usar Microsoft Intune para gerenciar os aplicativos cliente que a força de uso da sua empresa usa. O Intune permite que você use recursos como criptografia de dados e requisitos de acesso adicionais. Para saber mais sobre o gerenciamento de aplicativos por meio do Intune, confira gerenciamento de aplicativos do Intune. Para habilitar o Power BI aplicativo móvel para trabalhar com a política do Intune, use as etapas a seguir.

1. Vá para **Azure Active Directory** e, em seguida, **registros do aplicativo**.
2. Selecione o aplicativo configurado na etapa 3 ao registrar seu aplicativo cliente nativo.
3. Na página do aplicativo, selecione **permissões de API**.
4. Clique em **Adicionar uma permissão**.
5. Em **APIs que minha organização usa**, pesquise "gerenciamento de aplicativos móveis da Microsoft" e selecione-o.
6. Adicione a permissão **DeviceManagementManagedApps. ReadWrite** ao aplicativo
7. Clique em **conceder consentimento do administrador** para conceder a permissão de acesso ao aplicativo.
8. Configure a política do Intune desejada consultando [como criar e atribuir políticas de proteção de aplicativo](/intune/app-protection-policies).

## <a name="troubleshooting"></a>Solução de problemas

Se o aplicativo retornar uma página de erro depois de tentar carregar um relatório por mais de alguns minutos, talvez seja necessário alterar a configuração de tempo limite. Por padrão, o proxy de aplicativo dá suporte a aplicativos que levam até 85 segundos para responder a uma solicitação. Para aumentar essa configuração para 180 segundos, selecione o tempo limite de back-end para **longo** na página de configurações de proxy de aplicativo para o aplicativo. Para obter dicas sobre como criar relatórios rápidos e confiáveis, consulte [práticas recomendadas de Power bi relatórios](/power-bi/power-bi-reports-performance).

Usando o Azure Proxy de Aplicativo do AD para habilitar o Power BI aplicativo móvel para se conectar ao Servidor de Relatórios do Power BI local não tem suporte com políticas de acesso condicional que exigem o aplicativo Microsoft Power BI como um aplicativo cliente aprovado.

## <a name="next-steps"></a>Próximas etapas

- [Permitir que aplicativos cliente nativos interajam com aplicativos de proxy](application-proxy-configure-native-client-application.md)
- [Exibir KPIs e relatórios locais do servidor de relatório nos aplicativos móveis do Power BI](/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
