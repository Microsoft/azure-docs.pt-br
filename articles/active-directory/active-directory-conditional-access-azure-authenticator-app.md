---
title: Azure Authenticator para Android | Microsoft Docs
description: "O aplicativo Microsoft Azure Authenticator pode ser usado para entrar e acessar os recursos de trabalho. O aplicativo Azure Authenticator notifica você sobre uma solicitação de verificação de dois fatores pendente exibindo um alerta no dispositivo móvel."
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
ms.assetid: b7ceca0d-5c9d-45c4-942c-b3a9b6bad36c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: femila
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 60a5cc2ecc550c76ca3cb1f1d4d20070b3e3b035
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-authenticator-for-android"></a>Azure Authenticator para Android
O administrador de TI pode ter recomendado usar o Microsoft Azure Authenticator para entrar e acessar os recursos de trabalho. Esse aplicativo fornece essas duas opções de entrada:

* A Multi-Factor Authentication permite proteger suas contas de trabalho ou escolar com verificação de duas etapas. Entre usando algo que você conhece (por exemplo, sua senha) e proteja a conta mais com algo que você tem (uma chave de segurança desse aplicativo). O aplicativo Azure Authenticator notifica você sobre uma solicitação de verificação de dois fatores pendente exibindo um alerta no dispositivo móvel. Basta simplesmente exibir a solicitação no aplicativo e tocar em verificar ou cancelar. Como alternativa, você precisará digitar a senha exibida no aplicativo.
* A Conta de Trabalho permite transformar seu telefone ou tablet Android em um dispositivo confiável e fornecer SSO (Logon Único) para aplicativos da empresa. Seu administrador de TI pode exigir que você adicione uma conta de trabalho para acessar os recursos da empresa. O SSO permite entrar uma vez e aproveitar automaticamente a assinatura em todos os aplicativos de que sua empresa disponibilizou para você.

## <a name="installing-the-azure-authenticator-app"></a>Instalar o aplicativo Azure Authenticator
Você pode instalar o aplicativo Azure Authenticator na Google Play Store.
As instruções para adicionar a conta de trabalho do dispositivo Samsung Android e não Samsung Android são ligeiramente diferentes. As instruções para ambos estão listadas abaixo.

## <a name="adding-the-work-account-from-samsung-android-device"></a>Adicionar a conta de trabalho do dispositivo Samsung Android
### <a name="adding-the-work-account-through-the-app-home-screen"></a>Adicionar a conta de trabalho por meio da tela inicial do aplicativo
As instruções a seguir são aplicáveis para Samsung GS3 ou superior e tablets Note 2 ou superior.

1. Na tela inicial do aplicativo, aceite o EULA (Contrato de Licença de Usuário Final).
2. Na tela Ativar Conta, clique no menu de contexto à direita e selecione **Conta de Trabalho**.
3. Na tela Adicionar Conta, selecione **Conta de Trabalho**.
4. Na tela Ativar administrador do dispositivo, clique em **Ativar**.
5. Na tela de Política de Privacidade, marque a caixa de seleção e clique em **Confirmar**.
6. Na tela de Ingresso no Local de Trabalho, digite a identificação do usuário fornecida pela sua organização e clique em **Ingressar**.
7. Para entrar no aplicativo Azure Authenticator, insira a c****onta e a senha da empresa e clique em **Entrar**.
8. A próxima tela que exibe informações sobre a MFA (Multi-Factor Authentication) serve para conceder segurança adicional e é opcional. Você verá esta tela se seu trabalho ou escola exigir autenticação de dois fatores para criar uma conta de trabalho. Ela fornece instruções para verificar sua conta.
9. A tela de Ingresso no Local de Trabalho exibe a mensagem "**Ingressar em seu local de trabalho**". O aplicativo Azure Authenticator está tentando ingressar seu dispositivo na área de trabalho.
10. Você verá a mensagem Ingresso no local de trabalho concluído na próxima tela.

> [!NOTE]
> É permitido ter uma única conta de trabalho em seu dispositivo.
> 
> 

### <a name="adding-the-work-account-from-the-settings-menu"></a>Adicionar a conta de trabalho no menu de configurações
Depois de ter instalado o aplicativo Azure Authenticator, você também poderá criar uma conta de trabalho do Gerenciador de Contas Android.

1. No menu Configurações, navegue até **Contas** e clique em **Adicionar Conta**.
2. Siga as etapas 3 a 10 no procedimento, adicionando a conta de trabalho por meio da tela principal do aplicativo, a fim de adicionar uma conta de trabalho.

## <a name="adding-the-work-account-from-a-non-samsung-android-device"></a>Adicionar a conta de trabalho do dispositivo não Samsung Android
### <a name="adding-the-work-account-through-the-app-home-screen"></a>Adicionar a conta de trabalho por meio da tela inicial do aplicativo
1. Na tela inicial do aplicativo, aceite o EULA (Contrato de Licença de Usuário Final).
2. Na tela Ativar Conta, clique no menu de contexto à direita e selecione **Conta de Trabalho**.
3. Na tela de Contas, clique em **Adicionar conta**.
4. Se você vir a tela de contas, clique em **Adicionar conta**. Se uma conta de trabalho já tiver sido criada anteriormente, você verá uma tela de Sincronização mostrando a conta de trabalho existente. Você pode manter a conta de trabalho simplesmente tocando na seta para voltar à tela inicial. Como alternativa, você pode selecionar a conta para remover e recriar uma nova conta de trabalho na tela de Ingresso no Local de Trabalho, digite a identificação do usuário fornecida pela sua organização e clique em Ingressar.
5. Para entrar no aplicativo Azure Authenticator, insira a conta e a senha da empresa e clique em **Entrar**.
6. A próxima tela que exibe informações sobre a MFA (Multi-Factor Authentication) serve para conceder segurança adicional e é opcional. Você verá esta tela se seu trabalho ou escola exigir autenticação de dois fatores para criar uma conta de trabalho. Ela fornece instruções para verificar sua conta.
7. Clique em **OK** na tela seguinte. Não altere o nome do certificado.
   a mensagem "Ingressando em seu local de trabalho". O aplicativo Azure Authenticator está tentando ingressar seu dispositivo na área de trabalho.
   Você verá a mensagem Ingresso no local de trabalho concluído na próxima tela.

> [!NOTE]
> É permitido ter uma única conta de trabalho em seu dispositivo.
> 
> 

Depois de ter instalado o aplicativo Azure Authenticator, você também poderá criar uma conta de trabalho do Gerenciador de Contas Android.

1. No menu **Configurações**, navegue até Contas e clique em **Adicionar Conta**.
2. Siga as etapas 2 a 7 no procedimento, adicionando a conta de trabalho por meio da tela** principal do aplicativo, a fim de adicionar uma conta de trabalho.

### <a name="how-to-find-out-which-version-is-installed"></a>Como descobrir qual versão está instalada
1. Você pode descobrir qual versão do aplicativo Azure Authenticator e versões de serviço ingressados estão instaladas no seu dispositivo.
2. No menu pop-up, clique em **Sobre**.
3. A tela Sobre exibe os serviços do aplicativo e as versões instaladas no seu dispositivo.

### <a name="sending-log-files-to-report-issues"></a>Enviando arquivos de log para relatar problemas
1. Siga as orientações do Support da Microsoft para relatar um incidente com o aplicativo Azure Authenticator, obtenha um número de incidente e envie os arquivos de log referentes ao número de incidente atribuído da seguinte maneira:
2. No menu pop-up, clique em **Registrar em log**.
3. Se você tiver um incidente aberto com o Suporte da Microsoft, anote o número de incidente (ele será necessário para uma etapa posterior). Se você ainda não tiver criado um incidente de suporte e quiser obter assistência, siga as diretrizes no [Suporte da Microsoft](https://support.microsoft.com/en-us/contactus) para abrir um novo incidente.
4. Na tela de registro em log, clique em **Enviar agora**.
5. Selecione o provedor de email que você deseja usar.
6. Se você já tiver um incidente aberto junto ao Suporte da Microsoft, entre em contato com o engenheiro de suporte designado para o seu problema para saber como enviar os dados do log e associá-los ao incidente. O Engenheiro de Suporte fornece informações para a linha do assunto e o endereço de email. Se você não tiver um incidente de suporte, siga as orientações no Suporte da Microsoft para abrir um novo incidente.
7. Edite as linhas **Para** e **Assunto** com as informações recebidas do Suporte da Microsoft.
8. O aplicativo Azure Authenticator anexa o arquivo de log ao email que você está enviando. Descreva o problema enfrentado, atualize a lista de destinatários (opcional) e envie o email.

### <a name="deleting-the-work-account-and-leaving-your-workplace"></a>Excluir a conta de trabalho e deixar o local de trabalho
Você pode remover a conta de trabalho que você criou a qualquer momento da seguinte maneira:

**Para excluir a conta de trabalho no menu Configurações**

1. No Gerenciador de Contas, selecione **Conta de trabalho**.
2. Na tela Conta Corporativa, em **Configurações Gerais**, selecione **Configurações de Conta – Sair de sua rede do local de trabalho**.
3. Selecione **Sair** na tela **Ingresso no Local de Trabalho**.
4. Clique em **OK** quando a mensagem "Tem certeza de que deseja deixar a área de trabalho" for exibida.
5. Isso garante que você excluiu sua conta de trabalho de seu local de trabalho.

> [!NOTE]
> É recomendável que você não use a opção de Remover Conta para excluir uma conta de trabalho, pois essa opção pode não funcionar em algumas versões anteriores do Android.
> 
> 

## <a name="uninstalling-the-app"></a>Desinstalando o aplicativo
Em um dispositivo Samsung Android, os privilégios de administrador do dispositivo devem ser removidos da seguinte maneira antes de desinstalar o 

1. Em **Configurações**, em **Sistema**, selecione **Segurança**.
2. Em **Administração de Dispositivo**, clique em **Administradores de dispositivo**. Verifique se a caixa de seleção ao lado de **Azure Authenticator** está desmarcada.

## <a name="troubleshooting"></a>Solucionar problemas
Se o **Erro do Repositório de Chaves** for exibido, ele poderá ocorrer por você não ter a tela de bloqueio configurada com um PIN. Para contornar esse problema, desinstale o aplicativo Azure Authenticator, configure um PIN para a tela de bloqueio e reinstale o aplicativo.

