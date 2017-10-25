---
title: Configurar o proxy Web para um dispositivo StorSimple | Microsoft Docs
description: "Saiba como usar o Windows PowerShell para StorSimple para definir configurações do proxy Web para seu dispositivo StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 6c2ca351-a7c6-4da6-ab5e-c081e6d08261
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 66ee6ce15e51b14366eac0512c899d1c425c6092
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Configurar proxy da web para seu dispositivo StorSimple
## <a name="overview"></a>Visão geral
Este tutorial descreve como usar o Windows PowerShell para StorSimple para configurar e exibir configurações de proxy Web para seu dispositivo StorSimple. As configurações de proxy Web são usadas pelo dispositivo StorSimple ao se comunicar com a nuvem. Um servidor proxy Web é usado para adicionar outra camada de segurança, filtrar conteúdo, armazenar em cache para facilitar os requisitos de largura de banda ou até mesmo ajudar com a análise.

O proxy Web é uma configuração opcional para seu dispositivo StorSimple. Você pode configurar o proxy Web apenas por meio do Windows PowerShell para StorSimple. A configuração é um processo em duas etapas da seguinte maneira:

1. Primeiro, você define as configurações de proxy Web por meio do assistente de instalação ou de cmdlets do Windows PowerShell para StorSimple.
2. Em seguida, você habilita as configurações de proxy Web definidas por meio de cmdlets do Windows PowerShell para StorSimple.

Após a configuração do proxy Web ser concluída, você pode exibir as configurações de proxy Web definidas no serviço Microsoft Azure StorSimple Manager e no Windows PowerShell para StorSimple. 

Depois de ler este tutorial, você poderá:

* Configurar um proxy Web usando cmdlets e o assistente de instalação
* Habilitar um proxy Web usando cmdlets
* Exibir configurações de proxy Web no Portal Clássico do Azure
* Solucionar erros durante a configuração do proxy Web

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Configurar o proxy Web por meio do Windows PowerShell para StorSimple
Você pode usar qualquer uma das seguintes opções para definir configurações do proxy Web:

* Assistente de instalação para guiá-lo durante as etapas de configuração.
* Cmdlets do Windows PowerShell para StorSimple.

Cada um desses métodos é discutido nas seções a seguir.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Configurar o proxy Web por meio do assistente de instalação
Você pode usar o assistente de instalação para guiá-lo durante as etapas de configuração de proxy Web. Execute as etapas a seguir para configurar o proxy Web em seu dispositivo.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Para configurar o proxy Web por meio do assistente de instalação
1. No menu de console serial, escolha a opção 1, **Fazer logon com acesso completo** e forneça a **senha de administrador do dispositivo**. Digite o seguinte comando para iniciar uma sessão do assistente de instalação:
   
    `Invoke-HcsSetupWizard`
2. Se esta for a primeira vez que usa o assistente de instalação para o registro de dispositivos, você precisará definir todas as configurações de rede necessárias até atingir a configuração de proxy Web. Se o dispositivo já estiver registrado, você poderá aceitar todas as configurações de rede definidas até alcançar a configuração de proxy Web. No assistente de instalação, quando solicitado a definir as configurações de proxy da web, digite **Sim**.
3. Para a **URL de Proxy Web**, especifique o endereço IP ou o FQDN (nome de domínio totalmente qualificado) do servidor proxy Web e número de porta TCP que você deseja que seu dispositivo use ao se comunicar com a nuvem. Use o seguinte formato:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Por padrão, o número de porta TCP 8080 é especificado.
4. Escolha o tipo de autenticação como **NTLM**, **Básica** ou **Nenhuma**. Básica é a autenticação menos segura para a configuração do servidor proxy. NTLM (NT LAN Manager) é um protocolo de autenticação complexo e de alta segurança que usa um sistema de mensagens de três vias (às vezes, quatro, se for necessária integridade adicional) para autenticar um usuário. A autenticação padrão é NTLM. Para obter mais informações, confira autenticação [Básica](http://hc.apache.org/httpclient-3.x/authentication.html) e [Autenticação NTLM](http://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **No serviço StorSimple Manager, os gráficos de monitoramento de dispositivos não funcionam quando a autenticação Básica ou NTLM é habilitada na configuração do servidor proxy para o dispositivo. Para que os gráficos de monitoramento funcionem, você precisará garantir que a autenticação seja definida como NENHUMA.**
   > 
   > 
5. Se você estiver usando autenticação, forneça um **Nome de Usuário de Proxy Web** e uma **Senha de Proxy Web**. Você também precisará confirmar a senha.
   
    ![Configurar o Proxy Web no Dispositivo1 do StorSimple](./media/storsimple-configure-web-proxy/IC751830.png)

Se você estiver registrando seu dispositivo pela primeira vez, continue com o registro. Se o dispositivo já tiver sido registrado, o assistente será fechado. As configurações definidas serão salvas.

O proxy Web agora também será habilitado. Você pode ignorar a etapa [Habilitar o proxy Web](#enable-web-proxy) e ir diretamente para [Exibir configurações de proxy Web no Portal Clássico do Azure](#view-web-proxy-settings-in-the-azure-classic-portal).

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Configurar o proxy Web por meio de cmdlets do Windows PowerShell para StorSimple
Uma forma alternativa de definir configurações de proxy Web por meio de cmdlets do Windows PowerShell para StorSimple. Execute as etapas a seguir para configurar o proxy Web.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Para configurar o proxy Web por meio de cmdlets
1. No menu do console serial, escolha a opção 1, **Efetuar login com acesso total**. Quando solicitado, forneça a **senha de administrador do dispositivo**. A senha padrão é `Password1`.
2. No prompt de comando, digite:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Forneça e confirme a senha quando for solicitado, conforme mostrado abaixo.
   
    ![Configurar o Proxy Web no Dispositivo3 do StorSimple](./media/storsimple-configure-web-proxy/IC751831.png)

O proxy Web agora está configurado e precisa ser habilitado.

## <a name="enable-web-proxy"></a>Habilitar o proxy Web
O proxy Web está desabilitado por padrão. Depois de definir as configurações de proxy Web em seu dispositivo StorSimple, você precisa usar o Windows PowerShell para StorSimple para habilitar as configurações de proxy Web.

> [!NOTE]
> **Essa etapa não será necessária se você tiver usado o assistente de instalação para configurar o proxy Web. O proxy Web é habilitado automaticamente por padrão depois de uma sessão do assistente de instalação.**
> 
> 

Execute as seguintes etapas no Windows PowerShell para StorSimple para habilitar o proxy Web em seu dispositivo:

#### <a name="to-enable-web-proxy"></a>Para habilitar o proxy Web
1. No menu do console serial, escolha a opção 1, **Efetuar login com acesso total**. Quando solicitado, forneça a **senha de administrador do dispositivo**. A senha padrão é `Password1`.
2. No prompt de comando, digite:
   
    `Enable-HcsWebProxy`
   
    Agora você habilitou a configuração de proxy Web em seu dispositivo StorSimple.
   
    ![Configurar o Proxy Web no Dispositivo4 do StorSimple](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-classic-portal"></a>Exibir configurações de proxy Web no Portal Clássico do Azure
As configurações de proxy Web são definidas por meio da interface do Windows PowerShell e não podem ser alteradas no Portal Clássico. No entanto, você pode exibir essas configurações definidas no Portal clássico. Execute as etapas a seguir para exibir o proxy Web.

#### <a name="to-view-web-proxy-settings"></a>Para exibir configurações de proxy Web
1. Navegue até **Serviço StorSimple Manager > Dispositivos**. Selecione e clique em um dispositivo e vá para **Configurar**.
2. Role para baixo na página **Configurar** até a seção **Configurações de proxy Web**. Você pode exibir as configurações de proxy Web definidas em seu dispositivo StorSimple, conforme mostrado abaixo.
   
    ![Exibir o Proxy Web no Portal de Gerenciamento](./media/storsimple-configure-web-proxy/ViewWebProxyPortal_M.png)

## <a name="errors-during-web-proxy-configuration"></a>Erros durante a configuração de proxy Web
Se as configurações de proxy Web tiverem sido definidas incorretamente, mensagens de erro serão exibidas ao usuário no Windows PowerShell para StorSimple. A tabela a seguir explica algumas dessas mensagens de erro, suas causas prováveis e as ações recomendadas.

| N° de série | Código de erro HRESULT | Possível causa raiz | Ação recomendada |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |O comando é executado no controlador passivo e não pode se comunicar com o controlador ativo. |Execute o comando no controlador ativo. Para executar o comando no controlador passivo, você precisará corrigir a conectividade de controlador passivo para ativo. Você precisará contatar o Suporte da Microsoft se essa conectividade for interrompida. |
| 2. |0x800710dd - O identificador da operação não é válido |Não há suporte para as configurações de proxy no dispositivo virtual StorSimple. |Não há suporte para as configurações de proxy no dispositivo virtual StorSimple. Elas só podem ser configuradas em um dispositivo físico StorSimple. |
| 3. |0x80070057 - Parâmetro inválido |Um dos parâmetros fornecidos para as configurações do proxy não é válido. |O URI não foi fornecido no formato correto. Use o seguinte formato: `http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706ba - O servidor RPC não está disponível |A causa raiz é uma das seguintes:</br></br>O cluster não está ativo.</br></br>O serviço Datapath não está em execução.</br></br>O comando é executado no controlador passivo e não pode se comunicar com o controlador ativo. |Contate o Suporte da Microsoft para garantir que o cluster está ativo e o serviço de caminho de dados está em execução.</br></br>Execute o comando do controlador ativo. Se quiser executar o comando no controlador passivo, você precisará garantir que o controlador passivo pode se comunicar com o controlador ativo. Você precisará contatar o Suporte da Microsoft se essa conectividade for interrompida. |
| 5. |0x800706be - falha na chamada RPC |O cluster está inoperante. |Contate o Suporte da Microsoft para garantir que o cluster está ativo. |
| 6. |0x8007138f - O recurso de cluster não foi encontrado |O recurso de cluster do serviço de plataforma não foi encontrado. Isso poderá acontecer se a instalação não tiver sido correta. |Talvez seja necessário executar uma redefinição de fábrica em seu dispositivo. Talvez seja necessário criar um recurso de plataforma. Contate o Suporte da Microsoft para obter as próximas etapas. |
| 7. |0x8007138c - O recurso de cluster não está online |Os recursos de cluster de plataforma ou caminho de dados não estão online. |Entre em contato com o Suporte da Microsoft para garantir que o recurso de serviço de plataforma e caminho de dados estão online. |

> [!NOTE]
> * A lista de mensagens de erro acima não é exaustiva. 
> * Erros relacionados às configurações de proxy Web não serão exibidos no Portal clássico do Azure do serviço StorSimple Manager. Se houver um problema com o proxy Web depois que a configuração for concluída, o status do dispositivo será alterado para **Offline** no Portal Clássico.|
> 
> 

## <a name="next-steps"></a>Próximas etapas
* Se você tiver problemas ao implantar seu dispositivo ou definir as configurações de proxy Web, consulte [Solucionar problemas de implantação do dispositivo StorSimple](storsimple-troubleshoot-deployment.md).
* Para saber como usar o serviço StorSimple Manager, acesse [Use o serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

