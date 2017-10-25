---
title: Habilitar acesso remoto ao SharePoint com o Proxy de Aplicativo do Azure AD | Microsoft Docs
description: "Aborda os conceitos básicos sobre como integrar um servidor do SharePoint local com o Proxy de Aplicativo do Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 2b4ad3e7bda1346e606b2c185c204154b8f19f87
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Habilitar acesso remoto ao SharePoint com o Proxy de Aplicativo do Azure AD

Este artigo descreve como integrar um servidor local do SharePoint ao proxy de aplicativo do Azure AD (Azure Active Directory).

Para habilitar o acesso remoto ao SharePoint com o Proxy de aplicativo do Azure AD, siga as seções neste artigo passo a passo.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já tenha o SharePoint 2013 ou mais recente no seu ambiente. Além disso, considere os seguintes pré-requisitos:

* O SharePoint inclui suporte nativo a Kerberos. Portanto, os usuários que acessam sites internos remotamente por meio do Proxy de Aplicativo do Azure AD podem supor que terão uma experiência de SSO (logon único).

* Esse cenário inclui algumas mudanças na configuração do seu servidor do SharePoint. É recomendável usar um ambiente de preparo. Dessa forma, você pode fazer atualizações no servidor de preparo primeiro e depois facilitar um ciclo de testes antes de passar para o de produção.

* É necessário SSL na URL publicada. Você precisa ter o SSL habilitado no site interno a fim de assegurar que os links sejam enviados/mapeados corretamente. Se você ainda não configurou o SSL, consulte [Configurar o SSL para o SharePoint 2013](https://blogs.msdn.microsoft.com/fabdulwahab/2013/01/20/configure-ssl-for-sharepoint-2013) para obter instruções. Além disso, verifique se o computador conector confia no certificado que você emite. (O certificado não precisa ser emitido publicamente.)

## <a name="step-1-set-up-single-sign-on-to-sharepoint"></a>Parte 1: configurar o logon único para o SharePoint

Para aplicativos locais que usam a autenticação do Windows, você pode obter SSO (logon único) usando o protocolo de autenticação Kerberos e um recurso chamado KCD (delegação restrita de Kerberos). A KCD, quando configurada, permite que o conector do Proxy de Aplicativo obtenha um token do Windows para um usuário, mesmo que ele não tenha entrado no Windows diretamente. Para saber mais sobre a KCD, confira [Visão geral da delegação restrita de Kerberos](https://technet.microsoft.com/library/jj553400.aspx).

Para configurar o KCD para um servidor do SharePoint, use os procedimentos nas seções sequenciais a seguir:

### <a name="ensure-that-sharepoint-is-running-under-a-service-account"></a>Certificar-se de que SharePoint está em execução como uma conta de serviço

Primeiro, certifique-se de que o SharePoint está em execução em uma conta de serviço e não no sistema local, serviço local ou serviço de rede. Faça isso para que você possa anexar SPNs (nomes da entidade de serviço) a uma conta válida. Os SPNs são a maneira como o protocolo Kerberos identifica serviços diferentes. E você precisará da conta mais tarde para configurar a KCD.

> [!NOTE]
Você precisará ter uma conta do Azure AD criada anteriormente para o serviço. Sugerimos que você permita uma alteração automática de senha. Para obter mais informações sobre o conjunto completo de etapas e a solução de problemas, confira [Configurar a alteração automática da senha no SharePoint 2013](https://technet.microsoft.com/library/ff724280.aspx).

Para garantir que seus sites estejam em execução em uma conta de serviço definida, realize as seguintes etapas:

1. Abra o site **Administração Central do SharePoint 2013**.
2. Vá para **Segurança** e selecione **Configurar contas de serviço**.
3. Selecione **Pool de Aplicativos Web – SharePoint – 80**. As opções podem ser ligeiramente diferentes com base no nome do seu pool Web ou caso o pool Web use o SSL por padrão.

  ![Opções para configurar uma conta de serviço](./media/application-proxy-remote-sharepoint/service-web-application.png)

4. Se o campo **Selecionar uma conta para este componente** estiver definido como **Serviço Local** ou **Serviço de Rede**, você precisará criar uma conta. Caso contrário, você já poderá passar para a próxima etapa.
5. Escolha **Registrar uma nova conta gerenciada**. Depois que a conta é criada, é preciso definir o **Pool de Aplicativos Web** antes de poder usá-la.

### <a name="configure-sharepoint-for-kerberos"></a>Configurar o SharePoint para Kerberos

Você usa a KCD para executar o logon único no servidor do SharePoint.

Para configurar o site do SharePoint para autenticação Kerberos:

1. Abra o site **Administração Central do SharePoint 2013**.
2. Vá para **Gerenciamento de Aplicativos**, selecione **Gerenciar aplicativos Web** e selecione seu site do SharePoint. Neste exemplo, é **SharePoint - 80**.

  ![Selecionar o site do SharePoint](./media/application-proxy-remote-sharepoint/manage-web-applications.png)

3. Clique em **Provedores de Autenticação** na barra de ferramentas.
4. Na caixa **Provedores de Autenticação**, clique em **Zona Padrão** para exibir as configurações.
5. Na caixa de diálogo **Editar Autenticação**, role para baixo até ver **Tipos de Autenticação de Declarações**. Certifique-se de que as opções **Habilitar autenticação do Windows** e **Autenticação integrada do Windows** estejam selecionadas.
6. Na caixa suspensa para o campo de Autenticação integrada do Windows, certifique-se de que **Negociar (Kerberos)** esteja selecionada.

  ![Caixa de diálogo Editar Autenticação](./media/application-proxy-remote-sharepoint/service-edit-authentication.png)

7. Na parte inferior da caixa de diálogo **Editar Autenticação**, clique em **Salvar**.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Definir um nome da entidade de serviço para a conta de serviço do SharePoint

Antes de configurar a KCD, você precisa identificar o serviço do SharePoint em execução como a conta de serviço que você configurou. Identificar o serviço definindo um SPN. Para saber mais, confira [Service Principal Names](https://technet.microsoft.com/library/cc961723.aspx) (Nomes da entidade de serviço).

O formato SPN é:

```
<service class>/<host>:<port>
```

No formato SPN:

* _service class_ é um nome exclusivo para o serviço. Para o SharePoint, você usa **HTTP**.

* _host_ é o domínio totalmente qualificado ou o nome NetBIOS do host no qual o serviço está em execução. Para um site do SharePoint, esse texto pode ser a URL do site, dependendo da versão do IIS que você está usando.

* _port_ é opcional.

Se o FQDN do servidor do SharePoint for:

```
sharepoint.demo.o365identity.us
```

Nesse caso, o SPN será:

```
HTTP/sharepoint.demo.o365identity.us demo
```

Talvez você precise definir SPNs para sites específicos em seu servidor. Para obter mais informações, confira [Configurar a autenticação Kerberos](https://technet.microsoft.com/library/cc263449(v=office.12).aspx). Preste muita atenção à seção "Criar nomes da entidade de serviço para seus aplicativos Web usando a autenticação Kerberos".

A maneira mais fácil de você definir SPNs é seguir os formatos de SPN que podem já estar presentes para seus sites. Copie esses SPNs a serem registrados na conta de serviço. Para fazer isso:

1. Navegue até o site com o SPN de outro computador.
 Quando você faz isso, o conjunto relevante de tíquetes Kerberos são armazenados em cache no computador. Esses tíquetes contêm o SPN do local de destino para o qual você navegou.

2. Podemos extrair o SPN desse site usando uma ferramenta chamada [Klist](http://web.mit.edu/kerberos/krb5-devel/doc/user/user_commands/klist.html). Em uma janela Comando em execução no mesmo contexto do usuário que acessou o site no navegador, execute o seguinte comando:
```
Klist
```
Klist retorna então o conjunto de SPNs de destino. Neste exemplo, o valor realçado é o SPN necessário:

  ![Resultados do Klist de exemplo](./media/application-proxy-remote-sharepoint/remote-sharepoint-target-service.png)

4. Agora que você tem o SPN, certifique-se de que ele está configurado corretamente na conta de serviço que você configurou anteriormente para o aplicativo Web. Execute o comando a seguir no prompt de comando como um administrador do domínio:

 ```
 setspn -S http/sharepoint.demo.o365identity.us demo\sp_svc
 ```

 Esse comando define o SPN para a conta de serviço do SharePoint em execução como _demo\sp_svc_.

 Substitua _http/sharepoint.demo.o365identity.us_ pelo SPN do seu servidor e _demo\sp_svc_ pela conta de serviço no seu ambiente. O comando Setspn pesquisa pelo SPN antes de adicioná-lo. Nesse caso, você poderá ver um erro **Valor de SPN duplicado**. Caso esse erro seja exibido, verifique se o valor está associado à conta de serviço.

Você pode verificar se o SPN foi adicionado executando o comando Setspn com a opção -I. Para saber mais sobre esse comando, confira [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-set-as-a-trusted-delegate-to-sharepoint"></a>Certificar-se de que o conector está definido como um delegado confiável para o SharePoint

Configure a KCD para que o serviço Proxy de Aplicativo do Azure AD seja capaz de delegar identidades do usuário ao serviço do SharePoint. Configure a KCD habilitando o conector Proxy de Aplicativo para recuperar tíquetes Kerberos para seus usuários que foram autenticados no Azure AD. Em seguida, esse servidor passa o contexto ao aplicativo de destino, ou o SharePoint, nesse caso.

Para configurar a KCD, repita as etapas a seguir para cada computador conector:

1. Faça logon como um administrador de domínio em um DC e abra **Usuários e Computadores do Active Directory**.
2. Encontre o computador em que o conector está em sendo executado. Neste exemplo, ele é o mesmo servidor do SharePoint.
3. Clique duas vezes no computador e então clique na guia **Delegação**.
4. Verifique se as configurações de delegações estão definidas como **Confiar neste computador para delegação somente para os serviços especificados**. Então, selecione **Usar qualquer protocolo de autenticação**.

  ![Configurações de delegação](./media/application-proxy-remote-sharepoint/delegation-box.png)

5. Clique no botão **Adicionar**, clique em **Usuários ou Computadores** e localize a conta de serviço.

  ![Adicionar o SPN à conta de serviço](./media/application-proxy-remote-sharepoint/users-computers.png)

6. Na lista de SPNs, selecione aquele que você criou anteriormente para a conta de serviço.
7. Clique em **OK**. Clique em **OK** novamente para salvar as alterações.

## <a name="step-2-enable-remote-access-to-sharepoint"></a>Etapa 2: Habilitar acesso remoto ao SharePoint

Agora que você habilitou o SharePoint para Kerberos e configurou a KCD, você está pronto para publicar o farm do SharePoint para acesso remoto por meio do Proxy de Aplicativo do Azure AD.

1. Publique seu site do SharePoint com as seguintes configurações. Para obter instruções passo a passo, consulte [Publicar aplicativos usando o Proxy de Aplicativo do Azure AD](application-proxy-publish-azure-portal.md). 
   - **URL interna**: a URL do site do SharePoint internamente, como **https://SharePoint/**. Neste exemplo, certifique-se de usar **https**
   - **Método de pré-autenticação**: Azure Active Directory
   - **Converter URL em cabeçalhos**: NÃO

   >[!TIP]
   >O SharePoint usa o valor _Cabeçalho de Host_ para pesquisar o site. Ele também gera links com base nesse valor. O resultado final é que qualquer link que o SharePoint gere será uma URL publicada corretamente definida para usar a URL externa. Configurar o valor para **SIM** também permite que o conector encaminhe a solicitação ao aplicativo de back-end. No entanto, configurar o valor como **NÃO** significa que o conector não enviará o nome de host interno. Em vez disso, o conector envia o cabeçalho do host como a URL publicada para o aplicativo de back-end.

   ![Publicar SharePoint como aplicativo](./media/application-proxy-remote-sharepoint/publish-app.png)

2. Depois que seu aplicativo for publicado, defina as configurações de logon único com as seguintes etapas:

   1. Na página de aplicativo no portal, selecione **Logon único**.
   2. Para o modo de logon único, selecione **Autenticação Integrada do Windows**.
   3. Defina o SPN do Aplicativo Interno para o valor que você definiu anteriormente. Para esse exemplo, seria **http/sharepoint.demo.o365identity.us**.

   ![Configurar a Autenticação Integrada do Windows para SSO](./media/application-proxy-remote-sharepoint/configure-iwa.png)

3. Para concluir a configuração de seu aplicativo, vá para a seção **Usuários e grupos** e atribua usuários ao acessar este aplicativo. 

## <a name="step-3-ensure-that-sharepoint-knows-about-the-external-url"></a>Etapa 3: assegurar que o SharePoint saiba sobre a URL externa

A última etapa é assegurar que o SharePoint possa localizar o site com base na URL externa, assim, ele renderizará links baseados nessa URL externa. Faça isso configurando mapeamentos alternativos de acesso para o site do SharePoint.

1. Abra o site **Administração Central do SharePoint 2013**.
2. Em **Configurações do Sistema**, selecione **Configurar Mapeamentos Alternativos de Acesso**. A caixa Mapeamentos Alternativos de Acesso é aberta.

  ![Caixa Mapeamentos Alternativos de Acesso](./media/application-proxy-remote-sharepoint/alternate-access1.png)

3. Na lista suspensa ao lado de **Coleções de Mapeamentos Alternativos de Acesso**, selecione **Alterar Coleção de Mapeamentos Alternativos de Acesso**.
4. Selecione o seu site, por exemplo **SharePoint – 80**.
5. Você pode optar por adicionar a URL publicada como uma URL interna ou uma URL pública. Este exemplo usa uma URL pública como a extranet.
6. Clique em **Editar URLs Públicas** no caminho **Extranet** e insira a URL Externa que foi criada quando você publicou o aplicativo. Por exemplo, **https://sharepoint-iddemo.msappproxy.net**.

  ![Inserindo o caminho](./media/application-proxy-remote-sharepoint/alternate-access3.png)

7. Clique em **Salvar**.

Agora você pode acessar o site do SharePoint externamente por meio do Proxy de Aplicativo do Azure AD.

## <a name="next-steps"></a>Próximas etapas

- [Trabalhando com domínios personalizados no Proxy de Aplicativo do Azure AD](active-directory-application-proxy-custom-domains.md)
- [Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-understand-connectors.md)

