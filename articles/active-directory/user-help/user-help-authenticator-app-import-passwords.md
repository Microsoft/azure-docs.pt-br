---
title: Importar senhas para o aplicativo Microsoft Authenticator – Azure AD
description: Como importar senhas para o aplicativo de autenticação da Microsoft de gerentes de senha populares.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/28/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: ecc6580148dfba92077336a26ff9160fbe88eb2c
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806148"
---
# <a name="import-passwords-into-the-microsoft-authenticator-app"></a>Importar senhas para o aplicativo Microsoft Authenticator

Microsoft Authenticator dá suporte à importação de senhas do Google Chrome, Firefox, LastPass, Bitwarden e RoboForm. Se a Microsoft não oferecer suporte a seu Gerenciador de senhas existente, você poderá [inserir manualmente as credenciais de entrada em nosso CSV de modelo](https://go.microsoft.com/fwlink/?linkid=2134938). Para importar suas senhas existentes e gerenciá-las no aplicativo autenticador, basta exportar suas senhas do Gerenciador de senhas existente para o formato CSV (valores separados por vírgula). Em seguida, importe o CSV exportado para o autenticador em nossa extensão de navegador Chrome ou diretamente no aplicativo autenticador (Android e iOS).

## <a name="import-from-google-chrome-or-android-smart-lock"></a>Importar do Google Chrome ou Android Smart Lock

Você pode importar suas senhas do Google Chrome ou Android Smart Lock para o autenticador no seu smartphone ou em seu computador desktop. Você pode:

- [Importar do Chrome no Android e iOS](#import-from-chrome-on-android-and-ios)
- [Importar do navegador do Chrome desktop](#import-from-chrome-desktop-browser)

### <a name="import-from-chrome-on-android-and-ios"></a>Importar do Chrome no Android e iOS

Os usuários do Google Chrome em Android e telefones da Apple podem importar suas senhas diretamente de seu telefone com poucas etapas simples.

1. Instale o aplicativo autenticador em seu telefone e abra a guia **senhas** .

1. Entre no Google Chrome em seu telefone.

1. Toque no ![ menu de reticências do Google Chrome ](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png) na parte superior direita para telefones Android ou na parte inferior direita para dispositivos IOS e, em seguida, toque em **configurações.**

   Plataforma | Link
   ---------- | --------
   Android | ![Local do menu de configurações do Google Chrome](./media/user-help-authenticator-app-import-passwords/android-settings-menu.png)
   iOS | ![Ícone do menu de configurações do Google Chrome](./media/user-help-authenticator-app-import-passwords/apple-settings-menu.png)

1. Em **configurações**, abra **senhas**.

   Plataforma | Link
   ---------- | --------
   Android | ![Local do comando de senhas do Andoid Chrome](./media/user-help-authenticator-app-import-passwords/android-passwords-location.png)
   iOS | ![Local do comando de senhas do Apple Chrome](./media/user-help-authenticator-app-import-passwords/apple-passwords-location.png)

1. Em dispositivos Android, toque no ![ menu de reticências do Google Chrome ](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png) no canto superior direito para telefones Android ou na parte inferior direita para dispositivos IOS e, em seguida, toque em **Exportar senhas**.

   Plataforma | Link
   ---------- | --------
   Android | ![Local de senhas de exportação do Chrome Android](./media/user-help-authenticator-app-import-passwords/android-export-passwords-location.png)
   iOS | ![Local de senhas de exportação do Apple Chrome](./media/user-help-authenticator-app-import-passwords/apple-export-passwords-location.png)

   Você deve fornecer um PIN, uma impressão digital ou um reconhecimento facial. Confirme sua identidade e toque em **Exportar senhas** novamente para começar a exportar.

1. Depois que as senhas são exportadas, o Chrome solicita que você escolha qual aplicativo você está importando. Selecione **autenticador** para iniciar a importação de senhas. Você será informado sobre o status de importação quando ele for concluído.

   Plataforma | Link
   ---------- | --------
   Android | ![Local das senhas de importação do Chrome Android](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
   iOS | ![Local das senhas de importação do Apple Chrome](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

### <a name="import-from-chrome-desktop-browser"></a>Importar do navegador do Chrome desktop

Antes de começar, você deve instalar e entrar na extensão do [preenchimento automático da Microsoft](https://chrome.google.com/webstore/detail/microsoft-autofill/fiedbfgcleddlbcmgdigjgdfcggjcion) no navegador Chrome.

1. Abra o [Gerenciador de senhas do Google](https://passwords.google.com) em qualquer navegador. Se você ainda não fez isso, entre em sua conta do Google.

1. Selecione o ícone de engrenagem ![Ícone de engrenagem do Gerenciador de senhas de desktop](./media/user-help-authenticator-app-import-passwords/desktop-password-manager-gear.png) para abrir a página Configurações de senha.

1. Selecione **Exportar** e, na próxima página, selecione **Exportar** novamente para começar a exportar suas senhas. Forneça sua senha do Google quando solicitado para confirmar sua identidade. Você será informado sobre o status de importação quando ele for concluído.

   ![Local do comando Exportar senhas do navegador Chrome desktop](./media/user-help-authenticator-app-import-passwords/desktop-chrome-export-passwords-location.png)

1. Abra a extensão Chrome de preenchimento automático e selecione **configurações**.

   ![Local de configurações de extensão de preenchimento automático do navegador Chrome desktop](./media/user-help-authenticator-app-import-passwords/desktop-chrome-autofill-settings.png)

1. Selecione **importar dados** para abrir uma caixa de diálogo. Em seguida, selecione **escolher arquivo** para localizar e importar o arquivo CSV.

   ![Local do CSV de importação de dados no navegador Chrome desktop](./media/user-help-authenticator-app-import-passwords/desktop-chrome-import-csv.png)

## <a name="import-from-firefox"></a>Importar do Firefox

O Firefox permite exportar senhas somente do navegador da área de trabalho, portanto, verifique se você tem acesso ao navegador da área de trabalho do Firefox antes de importar senhas do Firefox.

1. Entre na versão mais recente do Firefox na área de trabalho e selecione o ![Menu "e/." do Firefox](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) menu na parte superior direita da tela.

1. Selecione **logons e senhas**.

   ![Local de logon e senhas do navegador do desktop Firefox](./media/user-help-authenticator-app-import-passwords/desktop-firefox-passwords-location.png)

1. Na página do Firefox Lockwise, selecione o ![ menu de menu de reticências do Firefox ](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) , selecione **Exportar logons** e, em seguida, confirme sua intenção selecionando **Exportar**. Você será solicitado a se identificar inserindo seu PIN, a senha do dispositivo ou examinando suas impressões digitais. Uma vez identificado com êxito, o Firefox exporta suas senhas no formato CSV para o local selecionado.

   ![Local da área de trabalho do navegador Firefox exportar senha](./media/user-help-authenticator-app-import-passwords/desktop-firefox-export-passwords-location.png)

1. Você pode importar suas senhas para o autenticador de um navegador da área de trabalho ou telefones iOS ou Android. Para importar para o aplicativo autenticador em seu telefone:

      1. Transfira o arquivo CSV exportado no seu telefone Android ou iOS usando uma maneira preferencial e segura e, em seguida, baixe-o. Em seguida, compartilhe o arquivo CSV com o aplicativo autenticador para iniciar a importação.

         Plataforma | Link
         ---------- | --------
         Android | ![Local das senhas de importação do Chrome Android](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Local das senhas de importação do Apple Chrome](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Depois de importar com êxito a senha para o autenticador, exclua o arquivo CSV de sua área de trabalho ou telefone celular.

## <a name="import-from-lastpass"></a>Importar do LastPass

O LastPass dá suporte à exportação de senhas somente de um navegador da área de trabalho, portanto, verifique se você tem acesso a um navegador de desktop antes de começar a importar senhas.

1. Entre no [site do LastPass](https://lastpass.com) e selecione **Opções avançadas** e, em seguida, selecione **Exportar**.

   ![Local da área de trabalho LastPass exportar senhas](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-export-passwords-location.png)

1. Identifique-se quando solicitado fornecendo sua senha mestra. Depois disso, você verá as senhas exportadas na página da Web.

1. Copie o conteúdo da página da Web.

1. Abra o bloco de notas (ou seu editor de texto favorito) e cole o conteúdo copiado.

1. Salve este arquivo do bloco de notas selecionando **arquivo** &gt; **salvar como**. Forneça um nome que termine com ". csv" (como LastPass.csv) em um local seguro na área de trabalho.

   ![Área de trabalho LastPass salvar arquivo CSV](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-save-import-file.png)

1. Você pode importar suas senhas para o autenticador em um navegador da área de trabalho ou em telefones iOS ou Android. Para importar para o aplicativo autenticador em seu telefone:

      1. Transfira o arquivo CSV exportado em seu smartphone usando uma maneira preferencial e segura e, em seguida, baixe-o. Em seguida, compartilhe o arquivo CSV com o aplicativo autenticador para iniciar a importação.

         Plataforma | Link
         ---------- | --------
         Android | ![Local de senhas de importação do Android LastPass](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Local das senhas de importação do Apple LastPass](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Depois de importar com êxito a senha para o autenticador, exclua o arquivo CSV de sua área de trabalho ou telefone celular.

## <a name="import-from-bitwarden"></a>Importar do Bitwarden

O Bitwarden dá suporte à exportação de senhas somente de um navegador da área de trabalho, portanto, verifique se você tem acesso a um navegador de desktop antes de começar a importar senhas.

1. Entre no https://vault.bitwarden.com/ e selecione **ferramentas** &gt; **Exportar cofre**. Escolha o formato de arquivo como CSV, forneça sua senha mestra e, em seguida, selecione **Exportar cofre** para começar a exportar.

   ![Local do cofre de exportação do Bitwarden](./media/user-help-authenticator-app-import-passwords/desktop-bitwarden-export-command-location.png)

1. Você pode importar suas senhas para o autenticador em um navegador da área de trabalho ou em telefones iOS ou Android. Para importar para o aplicativo autenticador em seu telefone:

      1. Transfira o arquivo CSV exportado em seu smartphone usando uma maneira preferencial e segura e, em seguida, baixe-o. Em seguida, compartilhe o arquivo CSV com o aplicativo autenticador para iniciar a importação.

         Plataforma | Link
         ---------- | --------
         Android | ![Local de senhas de importação do Android Bitwarden](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Local das senhas de importação do Apple Bitwarden](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Depois de importar com êxito a senha para o autenticador, exclua o arquivo CSV de sua área de trabalho ou telefone celular.

## <a name="import-from-roboform"></a>Importar do RoboForm

O RoboForm permite a exportação de senhas somente de seu aplicativo de área de trabalho, portanto, verifique se você tem acesso ao aplicativo RoboForm em uma área de trabalho antes de iniciar a importação.

1. Inicie o RoboForm do cliente de desktop e faça logon na sua conta.

1. Selecione **Opções** no menu **RoboForm** .

   ![Menu de opções do desktop RoboForm](./media/user-help-authenticator-app-import-passwords/desktop-roboform-options.png)

1. Selecione **conta &** &gt; **exportação** de dados.

   ![Local do comando de exportação do desktop RoboForm](./media/user-help-authenticator-app-import-passwords/desktop-roboform-accounts-data.png)

1. Escolha um local seguro para salvar o arquivo exportado. Selecione **logons** como o tipo de **dados** e selecione o arquivo CSV como o formato e, em seguida, selecione **Exportar**.

   ![Caixa de diálogo exportação do desktop RoboForm](./media/user-help-authenticator-app-import-passwords/desktop-roboform-export-dialog.png)

1. Confirme sua intenção e o arquivo CSV é exportado para o local selecionado.

   ![Caixa de diálogo de confirmação de exportação do RoboForm desktop](./media/user-help-authenticator-app-import-passwords/desktop-roboform-confirmation.png)

1. Você pode importar suas senhas para o autenticador em um navegador da área de trabalho ou em telefones iOS ou Android. Para importar para o aplicativo autenticador em seu telefone:

      1. Transfira o arquivo CSV exportado em seu smartphone usando uma maneira preferencial e segura e, em seguida, baixe-o. Em seguida, compartilhe o arquivo CSV com o aplicativo autenticador para iniciar a importação.

         Plataforma | Link
         ---------- | --------
         Android | ![Local de senhas de importação do Android RoboForm](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Local das senhas de importação do Apple RoboForm](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Depois de importar com êxito a senha para o autenticador, exclua o arquivo CSV de sua área de trabalho ou telefone celular.

## <a name="import-by-creating-a-csv"></a>Importar criando um CSV

Se as etapas para importar senhas do Gerenciador de senhas não estiverem listadas neste artigo, você poderá criar um CSV que pode ser usado para importar suas senhas para o autenticador. A Microsoft recomenda que você siga estas etapas em uma área de trabalho para facilitar a formatação.

1. Em sua área de trabalho, [Baixe e abra nosso modelo de importação](https://go.microsoft.com/fwlink/?linkid=2134938). Se você for um usuário do iPhone da Apple, Safari e conjunto de chaves, agora poderá pular para a etapa 4.

1. Exporte suas senhas do Gerenciador de senhas existente em um arquivo CSV não criptografado.

1. Copie as colunas relevantes do CSV exportado para o CSV de modelo e, em seguida, salve.

1. Se você não tiver um CSV exportado, poderá copiar cada logon do seu Gerenciador de senhas existente para o CSV de modelo. Não remova ou altere a linha de cabeçalho. Quando terminar, verifique a integridade dos dados antes de começar a próxima etapa.

1. Você pode importar suas senhas para o autenticador em um navegador da área de trabalho ou em telefones iOS ou Android. Para importar para o aplicativo autenticador em seu telefone:

      1. Transfira o arquivo CSV exportado em seu smartphone usando uma maneira preferencial e segura e, em seguida, baixe-o. Em seguida, compartilhe o arquivo CSV com o aplicativo autenticador para iniciar a importação.

         Plataforma | Link
         ---------- | --------
         Android | ![Local de senhas de importação de CSV do Android](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Local de senhas de importação de CSV da Apple](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Depois de importar com êxito a senha para o autenticador, exclua o arquivo CSV de sua área de trabalho ou telefone celular.

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

A causa mais comum de importações com falha é a formatação incorreta no arquivo CSV. Você pode tentar as etapas a seguir para solucionar o problema.

- Confira este artigo para ver se já damos suporte à importação de senhas do seu Gerenciador de senhas atual. Se fizermos isso, talvez você queira repetir a importação seguindo as etapas mencionadas para seu respectivo provedor.

- Se atualmente não houver suporte para importar o formato do seu Gerenciador de senhas, você poderá tentar novamente [criando o arquivo CSV manualmente](#import-by-creating-a-csv).

- Você pode verificar a integridade dos dados CSV com as seguintes sugestões:

  - A primeira linha deve conter um cabeçalho com três colunas: **URL**, **nome de usuário** e **senha**.

  - Cada linha deve conter um valor em colunas de **URL** e **senhas** .

- Você pode recriar o CSV colando seu conteúdo no arquivo de [modelo CSV](https://go.microsoft.com/fwlink/?linkid=2134938).

- Se nada mais funcionar, informe o problema usando o link **enviar comentários** nas configurações do aplicativo autenticador.
