---
title: "Portal do usuário para o servidor Azure MFA | Microsoft Docs"
description: "Esta é a página de autenticação do Azure Multi-Factor Authentication que descreve como começar com o Azure MFA e o Portal do Usuário."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 06b419fa-3507-4980-96a4-d2e3960e1772
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: joflore
ms.reviewer: alexwe
ms.custom: it-pro
ms.openlocfilehash: a4eb403d3d21b7dbe63c2645b488a7bddb6d39fd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="user-portal-for-the-azure-multi-factor-authentication-server"></a>Portal do usuário para o Servidor de Autenticação Multifator do Azure

O portal do usuário é um site do IIS que permite aos usuários se registrar na MF (Autenticação Multifator do Azure) e manter suas contas. Um usuário pode alterar o número de telefone, alterar o PIN ou optar por ignorar a verificação em duas etapas durante a próxima entrada.

Os usuários entram no portal do usuário com o seu nome de usuário normal e uma senha e, depois, concluem uma chamada de verificação em duas etapas ou respondem a perguntas de segurança para concluir a autenticação. Se o registro do usuário for autorizado, o usuário configurará seu número de telefone e PIN na primeira vez que entrar no portal do usuário.

Os Administradores do portal do usuário podem receber uma configuração e permissão para adicionar novos usuários e atualizar usuários existentes.

Dependendo do seu ambiente, convém implantar o portal do usuário no mesmo servidor do Servidor de Autenticação Multifator do Azure ou em outro servidor voltado para a internet.

![Portal do usuário MFA](./media/multi-factor-authentication-get-started-portal/portal.png)

> [!NOTE]
> O portal do usuário está disponível apenas com o Servidor Autenticação Multifator. Se você usar Autenticação Multifator na nuvem, encaminhe os seus usuários para [Configurar sua conta para verificação em duas etapas](./end-user/multi-factor-authentication-end-user-first-time.md) ou [Gerenciar as configurações de verificação em duas etapas](./end-user/multi-factor-authentication-end-user-manage-settings.md).

## <a name="install-the-web-service-sdk"></a>Instalar o SDK de Serviço Web

Seja qual for o cenário, se o SDK de Serviço Web da Autenticação Multifator do Azure ainda **não** estiver instalado no Servidor da MFA (Autenticação Multifator) do Azure, conclua as etapas a seguir.

1. Abra o console do Servidor de Autenticação Multifator.
2. Vá para o **SDK do serviço Web** e selecione **Instalar o SDK do serviço Web**.
3. Conclua a instalação usando os padrões, a não ser que você precise alterá-los por algum motivo.
4. Associe um certificado SSL ao site no IIS.

Se você tem dúvidas sobre como configurar um Certificado SSL em um servidor IIS, confira o artigo [Como configurar o SSL no IIS](https://docs.microsoft.com/en-us/iis/manage/configuring-security/how-to-set-up-ssl-on-iis).

O SDK de Serviço Web deve ser protegido com um certificado SSL. Um certificado autoassinado é suficiente para essa finalidade. Importe o certificado para o repositório de "Autoridades de Certificação Confiáveis" da conta do Computador Local no servidor Web do Portal do Usuário, para que ele confie no certificado ao iniciar a conexão SSL.

![SDK do serviço Web de configuração do Servidor MFA](./media/multi-factor-authentication-get-started-portal/sdk.png)

## <a name="deploy-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>Implantar o Portal do Usuário no servidor que o Servidor Autenticação Multifator do Azure

Os seguintes pré-requisitos são necessários para instalar o portal do usuário no **mesmo servidor** que o Servidor de Autenticação Multifator do Azure:

* O IIS, incluindo a compatibilidade da metabase do IIS 6 e ASP.NET (para o IIS 7 ou superior)
* Uma conta com direitos de administrador para o computador e o Domínio, se aplicável. A conta precisa ter permissões para criar grupos de segurança do Active Directory.
* Proteja o portal do usuário com um certificado SSL.
* Proteja o SDK de Serviço Web da Autenticação Multifator do Azure com um certificado SSL.

Para implantar o portal do usuário, siga estas etapas:

1. Abra o console do Servidor de Autenticação Multifator do Azure, clique no ícone **Portal do Usuário** no menu à esquerda e clique em **Instalar Portal do Usuário**.
2. Conclua a instalação usando os padrões, a não ser que você precise alterá-los por algum motivo.
3. Associar um certificado SSL ao site no IIS

   > [!NOTE]
   > Esse Certificado SSL é geralmente um Certificado SSL assinado publicamente.

4. Abra um navegador da web em qualquer computador e navegue até a URL onde o portal do usuário foi instalado (por exemplo, https://mfa.contoso.com/MultiFactorAuth ). Certifique-se de que nenhum aviso de certificado ou erro seja exibido.

![Instalação do portal de usuário do Servidor MFA](./media/multi-factor-authentication-get-started-portal/install.png)

Se você tem dúvidas sobre como configurar um Certificado SSL em um servidor IIS, confira o artigo [Como configurar o SSL no IIS](https://docs.microsoft.com/en-us/iis/manage/configuring-security/how-to-set-up-ssl-on-iis).

## <a name="deploy-the-user-portal-on-a-separate-server"></a>Implantar o portal do usuário em um servidor separado

Se o servidor onde o Servidor de Autenticação Multifator do Azure está sendo executado não é voltado para a internet, você deve instalar o portal do usuário em um **servidor separado, voltado para a Internet**.

Se sua organização usa o aplicativo Microsoft Authenticator como um dos métodos de verificação e deseja implantar o portal do usuário em seu próprio servidor, complete os seguintes requisitos:

* Use a v6.0 ou superior do Servidor de Autenticação Multifator do Azure.
* Instale portal do usuário em um servidor da Web da Internet executando o Microsoft Internet Information Services (IIS) 6.x ou superior.
* Ao usar o IIS 6.x, certifique-se de que o ASP.NET v2.0.50727 esteja instalado, registrado e definido como **Permitido**.
* Ao usar o IIS 7.x ou superior, o IIS, incluindo autenticação básica, ASP.NET e compatibilidade de metabase com o IIS 6.
* Proteja o portal do usuário com um certificado SSL.
* Proteja o SDK de Serviço Web da Autenticação Multifator do Azure com um certificado SSL.
* Certifique-se de que o portal do usuário deve ser capaz de conectar-se ao SDK de Serviço Web do Autenticação Multifator do Azure por SSL.
* Certifique-se de que o portal do usuário pode realizar a autenticação para o SDK do Serviço Web Autenticação Multifator do Azure usando as credenciais de uma conta de serviço no grupo de segurança "PhoneFactor Admins". Essa conta de serviço e o grupo devem existir no Active Directory se o Servidor de Autenticação Multifator do Azure for executado em um servidor ingressado em um domínio. Essa conta de serviço e o grupo existem localmente no Servidor Azure Multi-Factor Authentication se ele não tiver ingressado em um domínio.

A instalação do portal do usuário em um servidor diferente do Servidor de Autenticação Multifator do Azure exige as seguintes etapas:

1. **No Servidor MFA**, navegue até o caminho de instalação (exemplo: C:\Arquivos de Progrtama\Servidor de Autenticação Multifator) e copie o arquivo **MultiFactorAuthenticationUserPortalSetup64** em um local acessível para o servidor da Internet onde ele será instalado.
2. **No servidor Web voltado para a Internet**, execute o arquivo de instalação MultiFactorAuthenticationUserPortalSetup64 como administrador, mude o site, se desejar, e encurte o nome do diretório virtual, se desejar.
3. Associe um certificado SSL ao site no IIS.

   > [!NOTE]
   > Esse Certificado SSL é geralmente um Certificado SSL assinado publicamente.

4. Navegue até **C:\inetpub\wwwroot\MultiFactorAuth**
5. Edite o arquivo Web.Config no Bloco de Notas

    * Localize a chave **"USE_WEB_SERVICE_SDK"** e altere **value="false"** para **value="true"**
    * Localize a chave **"WEB_SERVICE_SDK_AUTHENTICATION_USERNAME"** e altere **value=""** para **value="DOMAIN\User"**, em que DOMAIN/User é uma conta de serviço que faz parte do Grupo “Administradores do PhoneFactor”.
    * Localize a chave **"WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD"** e altere **value=""** para **value="Password"**, em que Password é a senha da conta de serviço inserida na linha anterior.
    * Localize o valor **https://www.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx** e altere essa URL de espaço reservado para a URL do SDK do Serviço Web instalado na etapa 2.
    * Salve o arquivo Web.config e feche o Bloco de Notas.

6. Abra um navegador da web em qualquer computador e navegue até a URL onde o portal do usuário foi instalado (por exemplo, https://mfa.contoso.com/MultiFactorAuth ). Certifique-se de que nenhum aviso de certificado ou erro seja exibido.

Se você tem dúvidas sobre como configurar um Certificado SSL em um servidor IIS, confira o artigo [Como configurar o SSL no IIS](https://docs.microsoft.com/en-us/iis/manage/configuring-security/how-to-set-up-ssl-on-iis).

## <a name="configure-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Configurar o portal do usuário no Servidor de Autenticação Multifator do Microsoft Azure

Agora que o portal do usuário está instalado, você precisa configurar o Servidor Autenticação Multifator do Azure para trabalhar com o portal.

1. No console do Servidor de Autenticação Multifator do Azure, clique no ícone **Portal do Usuário**. Na guia Configurações, insira a URL no Portal do Usuário na caixa de texto **URL do Portal do Usuário**. Se a funcionalidade de email tiver sido habilitada, essa URL é incluída em emails que são enviados aos usuários quando eles são importados para o Servidor de Autenticação Multifator do Azure.
2. Escolha as configurações que você deseja usar no Portal do Usuário. Por exemplo, se os usuários tiverem permissão para escolher seus métodos de autenticação, verifique se **Permitir que usuários selecionem o método** está selecionado junto com os métodos que eles podem escolher.
3. Defina quem devem ser os administradores na guia **Administradores**. Você pode criar permissões administrativas completas usando as listas suspensas e caixas de seleção nas caixas Adicionar/Editar.

Configuração opcional:
- **Perguntas de Segurança**: defina perguntas de segurança aprovadas para seu ambiente e o idioma em que elas aparecem.
- **Sessões Passadas**: configure a integração do portal do usuário com um site baseado em formulário usando a MFA.
- **IPs confiáveis**: permita que os usuários ignorem a MFA ao autenticar de uma lista de IPs ou intervalos confiáveis.

![Configuração do portal de usuário do Servidor MFA](./media/multi-factor-authentication-get-started-portal/config.png)

O servidor Azure Multi-Factor Authentication fornece várias opções para o portal do usuário. A tabela a seguir fornece uma lista dessas opções e uma explicação de como elas são usadas.

| Configurações do Portal de Usuário | Descrição |
|:--- |:--- |
| URL do portal do usuário | Insira a URL de onde o portal está sendo hospedado. |
| Autenticação primária | Especifique o tipo de autenticação a ser usado ao entrar no portal. Autenticação do Windows, Radius ou LDAP. |
| Permitir que os usuários façam logon | Permita que os usuários insiram um nome de usuário e uma senha na página de entrada para o portal do usuário. Se essa opção não estiver selecionada, as caixas ficarão esmaecidas. |
| Permitir registro de usuário | Permita que o usuário se registre na Autenticação Multifator levando-os para uma tela de instalação que solicita informações adicionais, como número de telefone. O prompt de telefone de backup permite que os usuários especifiquem um número de telefone secundário. O prompt de token OATH permite que os usuários especifiquem um token OATH de terceiros. |
| Permitir que os usuários iniciem Desvio Único | Permitir que os usuários iniciem um desvio único. Se um usuário definir essa opção, ela terá efeito na próxima vez em que ele entrar. O prompt de ignorar segundos fornece ao usuário uma caixa para que ele possa alterar o padrão de 300 segundos. Caso contrário, o desvio único é válido somente por 300 segundos. |
| Permitir que os usuários selecionem o método | Permita aos usuários especificar o método de contato principal. Esse método pode ser uma chamada telefônica, uma mensagem de texto, um aplicativo móvel ou token OATH. |
| Permitir que os usuários selecionem o idioma | Permita que os usuários alterem o idioma usado para chamada telefônica, mensagem de texto, aplicativo móvel ou token OATH. |
| Permitir que os usuários ativem aplicativos móveis | Permita que os usuários gerem um código de ativação para concluir o processo de ativação de aplicativo móvel que seja usado com o servidor.  Você também pode definir o número de dispositivos em que eles podem ativar o aplicativo, entre 1 e 10. |
| Usar perguntas de segurança para fallback | Permita perguntas de segurança caso a verificação em duas etapas falhe. Você pode especificar o número de perguntas de segurança que devem ser respondidas com sucesso. |
| Permitir que os usuários associem token OATH de terceiros | Permita que os usuários especifiquem um token OATH de terceiros. |
| Usar token OATH para fallback | Permita o uso de um token OATH caso a verificação em duas etapas não seja bem-sucedida. Você também pode especificar o tempo limite da sessão em minutos. |
| Habilitar o registro em log | Habilite o registro em log no portal do usuário. Os arquivos de log estão localizados em: C:\Program Files\Multi-Factor Authentication Server\Logs. |

Essas configurações se tornarão visíveis para o usuário no portal quando forem habilitadas e ele se conectar ao portal do usuário.

![Configurações do Portal de Usuário](./media/multi-factor-authentication-get-started-portal/portalsettings.png)

### <a name="self-service-user-enrollment"></a>Registro de usuário de autoatendimento

Se desejar que os usuários entrem e se registrem, você deverá selecionar as opções **Permitir que usuários façam logon** e **Permitir registro de usuário** na guia Configurações. Lembre-se de que as configurações selecionadas afetam a experiência de entrada do usuário.

Por exemplo, quando um usuário entra no portal do usuário pela primeira vez, é levado para a página de Configuração de Usuário do Autenticação Multifator do Azure. Dependendo de como você configurou o Azure Multi-Factor Authentication, o usuário poderá selecionar o método de autenticação.

Se o método de verificação Chamada de Voz tiver sido selecionado ou houver pré-configuração para usar esse método, a página solicitará que o usuário insira seu número de telefone principal e extensão, se aplicável. Eles também podem ser autorizados a inserir um número de telefone de backup.

![Registrar números de telefone principal e de backup](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Se o usuário for solicitado a usar um PIN quando autenticar, a página também lhe pedirá para criar um PIN. Depois de digitar seu número de telefone e PIN (se aplicável), o usuário clica no botão **Telefonar para Mim para Autenticar**. O Autenticação Multifator do Azure realiza uma chamada telefônica de verificação para o número de telefone principal do usuário. O usuário deve responder à chamada telefônica, inserir o PIN (se aplicável) e pressionar # para passar para a próxima etapa do processo de auto-registro.

Se o usuário selecionar o método de verificação de Mensagem de Texto ou tiver sido pré-configurado para usar esse método, a página solicitará ao usuário seu número de telefone celular. Se o usuário for solicitado a usar um PIN quando autenticar, a página também lhe pedirá para inserir um PIN.  Depois de digitar seu número de telefone e PIN (se aplicável), o usuário clica no botão **Enviar uma Mensagem de Texto para Autenticar**. A Autenticação Multifator do Microsoft Azure envia uma verificação SMS para o celular do usuário. O usuário recebe a mensagem de texto com uma OTP (senha de uso único) e responde à mensagem com esta OTP e seu PIN (se aplicável).

![SMS do portal do usuário](./media/multi-factor-authentication-get-started-portal/text.png)

Se o usuário selecionar o método de verificação de Aplicativo Móvel, a página solicitará que o usuário instale o aplicativo Microsoft Authenticator em seu dispositivo e gere um código de ativação. Depois de instalar o aplicativo, o usuário clica no botão Gerar Código de Ativação.

> [!NOTE]
> Para usar o aplicativo Microsoft Authenticator, o usuário deve habilitar notificações por push para seu dispositivo.

A página exibe um código de ativação e uma URL junto com uma imagem de código de barras. Se o usuário for solicitado a usar um PIN quando autenticar, a página também lhe pedirá para inserir um PIN. O usuário insere o código de ativação e a URL no aplicativo Microsoft Authenticator ou usa o scanner de código de barras para digitalizar a imagem do código de barras e clica no botão Ativar.

Após concluir a ativação, o usuário clica no botão **Autenticar Agora**. O Autenticação Multifator do Azure executa uma verificação do aplicativo móvel do usuário. O usuário deve inserir seu PIN (se aplicável) e pressionar o botão Autenticar em seu aplicativo móvel para passar para a próxima etapa do processo de auto-registro.

Se os administradores tiverem configurado o servidor Azure Multi-Factor Authentication para coletar perguntas e respostas de segurança, o usuário será levado para a página Perguntas de Segurança. O usuário deve selecionar quatro perguntas de segurança e fornecer respostas às perguntas selecionadas.

![Perguntas de segurança do portal do usuário](./media/multi-factor-authentication-get-started-portal/secq.png)

O autorregistro do usuário agora está concluído e o usuário está conectado ao portal do usuário. Os usuários podem entrar no portal do usuário a qualquer momento no futuro para alterar seus números de telefone, PINs, métodos de autenticação e perguntas de segurança se alteração dos métodos for permitida pelos seus administradores.

## <a name="next-steps"></a>Próximas etapas

- [Implantar o serviço Web do aplicativo móvel do Servidor de Autenticação Multifator do Azure](multi-factor-authentication-get-started-server-webservice.md)