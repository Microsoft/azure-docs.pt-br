---
title: Conectar remotamente ao seu dispositivo StorSimple | Microsoft Docs
description: Explica como configurar seu dispositivo para o gerenciamento remoto e como se conectar ao Windows PowerShell para StorSimple via HTTP ou HTTPS.
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 923377aa-f451-4656-87de-5e95a34a6a2a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b916173e127394d3ea06eded36285bdbbf884b12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Conectar remotamente ao seu dispositivo StorSimple série 8000

## <a name="overview"></a>Visão geral
Você pode usar o Windows PowerShell remotamente para se conectar ao seu dispositivo StorSimple. Ao se conectar dessa maneira, você não verá um menu. (Você verá um menu apenas se usar o console serial no dispositivo para se conectar.) Com a comunicação remota do Windows PowerShell, você se conectar a um espaço de execução específico. Também é possível especificar o idioma de exibição. 

Para obter mais informações sobre como usar o Windows PowerShell remotamente para gerenciar seu dispositivo, acesse [Usar o Windows PowerShell para StorSimple para administrar seu dispositivo StorSimple](storsimple-windows-powershell-administration.md).

Este tutorial explica como configurar seu dispositivo para o gerenciamento remoto e, em seguida, como se conectar ao Windows PowerShell para StorSimple. Você pode usar HTTP ou HTTPS para se conectar por meio de comunicação remota do Windows PowerShell. No entanto, quando estiver decidindo a forma de conexão ao Windows PowerShell para StorSimple, considere o seguinte: 

* Conectar-se diretamente ao console serial do dispositivo é seguro, mas se conectar ao console serial em comutadores de rede não é seguro. Tenha cuidado com os riscos de segurança ao se conectar ao console serial do dispositivo em comutadores de rede. 
* A conexão através de uma sessão HTTP pode oferecer mais segurança do que a conexão por meio do console serial através da rede. Embora não seja o método mais seguro, é aceitável em redes confiáveis. 
* A conexão através de uma sessão HTTPS com um certificado autoassinado é a mais segura, sendo a opção recomendada.

Você pode se conectar remotamente à interface do Windows PowerShell. No entanto, o acesso remoto ao seu dispositivo StorSimple por meio da interface do Windows PowerShell não está habilitado por padrão. Você precisa habilitar o gerenciamento remoto primeiro no dispositivo e então habilitá-lo no cliente usado para acessar seu dispositivo.

As etapas descritas neste artigo foram executadas em um sistema de host executando o Windows Server 2012 R2.

## <a name="connect-through-http"></a>Conectar-se por meio de HTTP
Conectar-se ao Windows PowerShell para StorSimple por meio de uma sessão HTTP oferece mais segurança do que se conectar por meio do console serial do dispositivo StorSimple. Embora não seja o método mais seguro, é aceitável em redes confiáveis.

Você pode usar o Portal Clássico do Azure ou o console serial para configurar o gerenciamento remoto. Escolha um dos seguintes procedimentos:

* [Usar o Portal Clássico do Azure para habilitar o gerenciamento remoto via HTTP](#use-the-azure-classic-portal-to-enable-remote-management-over-http)
* [Usar o console serial para habilitar o gerenciamento remoto via HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

Depois de habilitar o gerenciamento remoto, use o procedimento a seguir para preparar o cliente para uma conexão remota.

* [Preparar o cliente para a conexão remota](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-classic-portal-to-enable-remote-management-over-http"></a>Usar o Portal Clássico do Azure para habilitar o gerenciamento remoto via HTTP
Realize as seguintes etapas no Portal Clássico do Azure para habilitar o gerenciamento remoto via HTTP.

#### <a name="to-enable-remote-management-through-the-azure-classic-portal"></a>Para habilitar o gerenciamento remoto por meio do Portal Clássico do Azure
1. Acesse **Dispositivos** > **Configurar** do dispositivo.
2. Role para baixo até a seção **Gerenciamento Remoto** .
3. Defina **Habilitar o Gerenciamento Remoto** como **Sim**.
4. Agora, você pode optar por conectar-se usando HTTP. (O padrão é conectar-se por HTTPS.) Certifique-se de que HTTP esteja selecionado.
   
   > [!NOTE]
   > A conexão por HTTP só será aceitável em redes confiáveis.
   > 
   > 
5. Na parte inferior da página, clique em **Salvar** .

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Usar o console serial para habilitar o gerenciamento remoto via HTTP
Execute as seguintes etapas no console serial do dispositivo para habilitar o gerenciamento remoto.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Para habilitar o gerenciamento remoto por meio do console serial
1. No menu do console serial, escolha a opção 1. Para obter mais informações sobre como usar o console serial no dispositivo, vá para [Conectar-se ao Windows PowerShell para StorSimple por meio do console serial do dispositivo](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. No prompt, digite: `Enable-HcsRemoteManagement –AllowHttp`
3. Você será notificado sobre as vulnerabilidades de segurança do uso de HTTP para se conectar ao dispositivo. Quando solicitado, confirme digitando **Y**.
4. Verifique se o HTTP estiver habilitado digitando: `Get-HcsSystem`
5. Verifique se o campo **RemoteManagementMode** mostra **HttpsAndHttpEnabled**. A ilustração a seguir mostra essas configurações em PuTTY.
   
     ![HTTPS serial e HTTP habilitados](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Preparar o cliente para a conexão remota
Execute as seguintes etapas no cliente para habilitar o gerenciamento remoto.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Para preparar o cliente para a conexão remota
1. Inicie uma sessão do Windows PowerShell como administrador.
2. Digite o seguinte comando para adicionar o endereço IP do dispositivo StorSimple na lista de hosts confiáveis do cliente: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Substitua <*device_ip*> pelo endereço IP do dispositivo; por exemplo: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Digite o seguinte comando para salvar as credenciais do dispositivo em uma variável: 
   
    ```
    $cred = Get-Credential
    ```
    
4. Na caixa de diálogo que é exibida:
   
   1. Digite o nome de usuário no seguinte formato: *device_ip\SSAdmin*.
   2. Digite a senha de administrador do dispositivo que foi definida quando o dispositivo foi configurado com o assistente de instalação. A senha padrão é *Senha1*.
5. Inicie uma sessão do Windows PowerShell no dispositivo digitando o seguinte comando:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > Para criar uma sessão do Windows PowerShell para ser usada com o dispositivo virtual StorSimple, acrescente o parâmetro `–Port` e especifique a porta pública que você configurou na comunicação remota do dispositivo virtual StorSimple.
   > 
   > 
   
     Neste momento, uma sessão do Windows PowerShell remota já deve estar ativa para o dispositivo.
   
    ![PowerShell remoto usando HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Conectar-se por meio de HTTPS
A conexão ao Windows PowerShell para StorSimple por meio de uma sessão HTTPS é o método mais seguro e recomendado de se conectar remotamente ao seu dispositivo Microsoft Azure StorSimple. Os procedimentos a seguir explicam como configurar os computadores de cliente e o console seriais para que você possa usar HTTPS para se conectar ao Windows PowerShell para StorSimple.

Você pode usar o Portal Clássico do Azure ou o console serial para configurar o gerenciamento remoto. Escolha um dos seguintes procedimentos:

* [Usar o Portal Clássico do Azure para habilitar o gerenciamento remoto via HTTPS](#use-the-azure-classic-portal-to-enable-remote-management-over-https)
* [Usar o console serial para habilitar o gerenciamento remoto via HTTPS](#use-the-serial-console-to-enable-remote-management-over-https)

Depois de habilitar o gerenciamento remoto, use os procedimentos a seguir para preparar o host para um gerenciamento remoto e conecte ao dispositivo a partir do host remoto.

* [Preparar o host para gerenciamento remoto](#prepare-the-host-for-remote-management)
* [Conectar ao dispositivo a partir do host remoto](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-classic-portal-to-enable-remote-management-over-https"></a>Usar o Portal Clássico do Azure para habilitar o gerenciamento remoto via HTTPS
Realize as seguintes etapas no Portal Clássico do Azure para habilitar o gerenciamento remoto via HTTPS.

#### <a name="to-enable-remote-management-over-https-from-the-azure-classic-portal"></a>Para habilitar o gerenciamento remoto via HTTPS do Portal Clássico do Azure
1. Acesse **Dispositivos** > **Configurar** do dispositivo.
2. Role para baixo até a seção **Gerenciamento Remoto** .
3. Defina **Habilitar o Gerenciamento Remoto** como **Sim**.
4. Agora, você pode optar por conectar-se usando HTTPS. (O padrão é conectar-se por HTTPS.) Certifique-se de que HTTPS esteja selecionado. 
5. Clique em **Baixar certificado de gerenciamento remoto**. Especifique um local para salvar o arquivo. Você precisará instalar esse certificado no computador cliente ou host que usará para se conectar ao dispositivo.
6. Na parte inferior da página, clique em **Salvar** .

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Usar o console serial para habilitar o gerenciamento remoto via HTTPS
Execute as seguintes etapas no console serial do dispositivo para habilitar o gerenciamento remoto.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Para habilitar o gerenciamento remoto por meio do console serial
1. No menu do console serial, escolha a opção 1. Para obter mais informações sobre como usar o console serial no dispositivo, vá para [Conectar-se ao Windows PowerShell para StorSimple por meio do console serial do dispositivo](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. No prompt, digite: 
   
     `Enable-HcsRemoteManagement`
   
    Isso deve habilitar HTTPS em seu dispositivo.
3. Verifique se o HTTP está habilitado digitando: 
   
     `Get-HcsSystem`
   
    Certifique-se de que o campo **RemoteManagementMode** mostre **HttpsEnabled**. A ilustração a seguir mostra essas configurações em PuTTY.
   
     ![HTTPS serial habilitado](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. Na saída de `Get-HcsSystem`, copie o número de série do dispositivo e salve-o para usar depois.
   
   > [!NOTE]
   > O número de série é mapeado para o nome CN no certificado.
   > 
   > 
5. Obtenha um certificado de gerenciamento remoto, digitando: 
   
     `Get-HcsRemoteManagementCert`
   
    Um certificado similar ao seguinte aparecerá.
   
    ![Obter certificado de gerenciamento remoto](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Copie as informações do certificado de **---INICIAR CERTIFICADO---** até **---TERMINAR CERTIFICADO---** em um editor de texto como o bloco de notas e salve-o como arquivo .cer. (Você vai copiar esse arquivo para o host remoto quando preparar o host.)
   
   > [!NOTE]
   > Para gerar um novo certificado, use o cmdlet `Set-HcsRemoteManagementCert`.
   > 
   > 

### <a name="prepare-the-host-for-remote-management"></a>Preparar o host para gerenciamento remoto
Para preparar o computador host para a conexão remota que use uma sessão HTTPS, execute os seguintes procedimentos:

* [Importe o arquivo. cer no repositório de raiz do cliente ou host remoto](#to-import-the-certificate-on-the-remote-host).
* [Adicione os números de série do dispositivo ao arquivo de hosts em seu host remoto](#to-add-device-serial-numbers-to-the-remote-host).

Cada um desses procedimentos é descrito abaixo.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Para importar o certificado no host remoto
1. Clique com o botão direito do mouse no arquivo .cer e selecione **Instalar certificado**. Isso inciará o Assistente para Importação de Certificados.
   
    ![Assistente para importação de certificados 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. Em **Localização do repositório**, selecione **Computador Local** e, em seguida, clique em **Próximo**.
3. Selecione **Colocar todos os certificados no seguinte repositório** e, em seguida, clique em **Procurar**. Navegue até o repositório de raiz do seu host remoto e, em seguida, clique em **Próximo**.
   
    ![Assistente para importação de certificados 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Clique em **Concluir**. Será exibida uma mensagem que informa que a importação foi bem-sucedida.
   
    ![Assistente para importação de certificados 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Para adicionar números de série do dispositivo ao host remoto
1. Inicie o bloco de notas como administrador e abra o arquivo hosts localizado em \Windows\System32\Drivers\etc.
2. Adicione as três entradas a seguir ao arquivo hosts: **endereço IP DATA 0**, **endereço IP fixo do controlador 0** e **endereço IP fixo do controlador 1**.
3. Insira o número de série do dispositivo que você salvou anteriormente. Mapeie-o para o endereço IP conforme mostrado na imagem a seguir. Em controlador 0 e controlador 1, acrescente **Controller0** e **Controller1** no final do número de série (nome CN).
   
    ![Adicionando um Nome CN ao arquivo hosts](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Salve o arquivo hosts.

### <a name="connect-to-the-device-from-the-remote-host"></a>Conectar ao dispositivo a partir do host remoto
Use o Windows PowerShell e o SSL para inserir uma sessão SSAdmin no dispositivo pelo cliente ou host remoto. A sessão SSAdmin é mapeada para a opção 1 no menu de [console serial](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) do seu dispositivo.

Execute o procedimento a seguir no computador do qual você deseja fazer a conexão remota do Windows PowerShell.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-ssl"></a>Para inserir uma sessão SSAdmin no dispositivo usando Windows PowerShell e SSL
1. Inicie uma sessão do Windows PowerShell como administrador.
2. Adicione o endereço IP do dispositivo aos hosts confiáveis do cliente, digitando:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Em que <*device_ip*> é o endereço IP do dispositivo; por exemplo: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Crie uma nova credencial, digitando: 
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    Em que <*IP do dispositivo de destino*> é o endereço IP de DATA 0 do seu dispositivo; por exemplo, **10.126.173.90** conforme mostrado na imagem anterior do arquivo hosts. Além disso, forneça a senha de administrador do seu dispositivo.
4. Crie uma sessão, digitando:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    Para o parâmetro -ComputerName no cmdlet, forneça o <*número de série do dispositivo de destino*>. Esse número de série foi mapeado para o endereço IP de DATA 0 no arquivo de hosts em seu host remoto; por exemplo, **SHX0991003G44MT** , como mostrado na imagem a seguir.
5. Tipo: 
   
     `Enter-PSSession $session`
6. Você precisará aguardar alguns minutos e, em seguida, será conectado ao dispositivo via HTTPS por SSL. Você verá uma mensagem que indica que está conectado ao dispositivo.
   
    ![PowerShell remoto usando HTTPS e SSL](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre como [usar o Windows PowerShell para administrar seu dispositivo StorSimple](storsimple-windows-powershell-administration.md).
* Saiba mais sobre como [usar o serviço do StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

