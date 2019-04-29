---
title: Configuração de servidor iSCSI da Matriz Virtual do Microsoft Azure StorSimple | Microsoft Docs
description: Descreve como realizar a configuração inicial, registrar seu servidor iSCSI do StorSimple e concluir a configuração do dispositivo.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 4db116d1-978b-48e8-b572-a719a8425dbc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: 5d3525952ec09474d60618c4f99138cef1fce57a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61416784"
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>Implantar o StorSimple Virtual Array — configurar como um servidor iSCSI por meio do portal do Azure

![fluxo do processo de instalação iscsi](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Visão geral

Este tutorial de implantação se aplica ao Microsoft Azure StorSimple Virtual Array. Este tutorial descreve como executar a instalação inicial, registrar o servidor iSCSI do StorSimple, concluir a configuração do dispositivo e, em seguida, criar, montar, inicializar e formatar volumes em seu servidor iSCSI da Matriz Virtual StorSimple. 

Os procedimentos descritos aqui levam um intervalo de aproximadamente 30 minutos a 1 hora para concluir. As informações publicadas nesse artigo aplicam-se somente a Matrizes Virtuais StorSimple.

## <a name="setup-prerequisites"></a>Pré-requisitos de configuração

Antes de configurar e de instalar a Matriz Virtual StorSimple, verifique se:

* Você provisionou uma matriz virtual e se conectou a ela, conforme descrito em [Implantar a Matriz Virtual StorSimple – Provisionar uma matriz virtual no Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou [Implantar a Matriz Virtual StorSimple – Provisionar uma matriz virtual no VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Você tem a chave de registro do serviço Gerenciador de Dispositivos StorSimple que você criou para gerenciar Matrizes Virtuais StorSimple. Para obter mais informações, confira a **Etapa 2: Obter a chave de registro do serviço** em [Implantar o StorSimple Virtual Array – Preparar o portal](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
* Se essa for a segunda matriz virtual ou a subsequente que você está registrando com um serviço Gerenciador de Dispositivos StorSimple existente, você deverá ter a chave de criptografia de dados do serviço. Essa chave foi gerada quando o primeiro dispositivo foi registrado com êxito com esse serviço. Caso tenha perdido essa chave, veja **Obter a chave de criptografia de dados de serviço** em [Usar a interface do usuário da Web para administrar o StorSimple Virtual Array](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Configuração passo a passo

Use as instruções passo a passo a seguir para preparar e configurar sua Matriz Virtual StorSimple:

* [Etapa 1: Concluir a configuração da IU da Web local e registrar seu dispositivo](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* Etapa 2: Concluir a configuração obrigatória do dispositivo
* [Etapa 3: Adicionar um volume](#step-3-add-a-volume)
* [Etapa 4: Montar, inicializar e formatar um volume](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Etapa 1: Concluir a configuração da IU da Web local e registrar seu dispositivo

#### <a name="to-complete-the-setup-and-register-the-device"></a>Para concluir a configuração e registrar o dispositivo

1. Abra uma janela do navegador. Para conectar-se ao tipo de interface do usuário da Web:
   
    `https://<ip-address of network interface>`
   
    Use a URL de conexão observada na etapa anterior. Você verá um erro informando que há um problema com o certificado de segurança do site. Clique em **Continuar para essa página da Web**.
   
    ![erro de certificado de segurança](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Entre na interface do usuário da Web de seu dispositivo virtual como **StorSimpleAdmin**. Digite a senha do administrador do dispositivo que você alterou na Etapa 3: Iniciar o dispositivo virtual em [Implantar o StorSimple Virtual Array – Provisionar um dispositivo virtual no Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) ou [Implantar o StorSimple Virtual Array – Provisionar um dispositivo virtual no VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
    ![Página de entrada](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. Você será levado à página **Inicial** . Esta página descreve as várias configurações necessárias para configurar e registrar o dispositivo virtual com o serviço Gerenciador de Dispositivos StorSimple. Observe que **Configurações de rede**, **Configurações de proxy Web** e **Configurações de hora** são opcionais. As únicas configurações obrigatórias são as **Configurações do dispositivo** e **Configurações de nuvem**.
   
    ![Página inicial](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. Na página **Configurações de rede**, em **Interfaces de rede**, DATA 0 será configurado automaticamente para você. Cada interface de rede é definida por padrão para obter um endereço IP automaticamente (DHCP). Assim, um endereço IP, a sub-rede e gateway serão atribuídos automaticamente (tanto para IPv4 quanto para IPv6).
   
    Já que planeja implantar o dispositivo como um servidor iSCSI (para provisionar o armazenamento em bloco), recomendamos desabilitar a opção **Obter endereço IP automaticamente** e configurar endereços IP estáticos.
   
    ![Página de configurações de rede](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Se você adicionou mais de uma interface de rede durante o provisionamento do dispositivo, você pode configurá-las aqui. Observe que você pode configurar a interface de rede apenas como IPv4 ou como IPv4 e IPv6. Não há suporte para configurações somente IPv6.
5. Os servidores DNS são necessários porque eles são usados quando o dispositivo tenta se comunicar com seus provedores de serviço de armazenamento de nuvem, ou então para resolver seu dispositivo por nome, se ele estiver configurado como um servidor de arquivos. Na página **Configurações de rede**, em **Servidores DNS**:
   
   1. Um servidor DNS primário e um secundário serão configurados automaticamente. Se você optar por configurar endereços IP estáticos, você pode especificar servidores DNS. Para alta disponibilidade, recomendamos que você configure um servidor DNS primário e um secundário.
   2. Clique em **Aplicar**. Isso aplicará e validará as configurações de rede.
6. Na página **Configurações do dispositivo** :
   
   1. Atribua um **Nome** exclusivo ao seu dispositivo. Esse nome pode ter de 1 a 15 caracteres e pode conter letras, números e hifens.
   2. Clique no ícone do **Servidor iSCSI** ![ícone do Servidor iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) para o **Tipo** de dispositivo que você está criando. Um servidor iSCSI permitirá a você provisionar armazenamento em bloco.
   3. Especifique se deseja que este dispositivo seja ingressado no domínio. Se o dispositivo é um servidor iSCSI, ingressar no domínio é opcional. Se optar por não ingressar o servidor iSCSI em um domínio, clique em **Aplicar**, aguarde até que as configurações sejam aplicadas e vá para a próxima etapa.
      
       Se você quiser adicionar o dispositivo a um domínio. Insira um **Nome de domínio** e clique em **Aplicar**.
      
      > [!NOTE]
      > Se estiver ingressando em seu servidor iSCSI em um domínio, certifique-se de que sua matriz virtual esteja em sua própria unidade organizacional (UO) do Microsoft Azure Active Directory e que não haja objetos de política de grupo (GPO) aplicados a ele.
      > 
      > 
   4. Uma caixa de diálogo aparecerá. Insira suas credenciais de domínio no formato especificado. Clique no ícone de verificação  ![ícone de verificação](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). As credenciais de domínio serão verificadas. Você verá uma mensagem de erro se as credenciais estiverem incorretas.
      
       ![credenciais](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. Clique em **Aplicar**. Isso aplicará e validará as configurações do dispositivo.
7. Opcionalmente, configure seu servidor proxy da Web. Embora a configuração do proxy da Web seja opcional, saiba que se você usar um proxy da Web, só poderá configurá-lo aqui.
   
    ![configurar o proxy Web](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    Na página **Proxy Web** :
   
   1. Forneça a **URL do proxy Web** neste formato: *http:\//host-IP endereço* ou *FQDN: porta número*. Observe que não há suporte para URLs HTTPS.
   2. Especifique a **Autenticação** como **Básica** ou **Nenhuma**.
   3. Se estiver usando a autenticação, também será necessário fornecer um **Nome de Usuário** e uma **Senha**.
   4. Clique em **Aplicar**. Isso validará e aplicará as configurações de proxy Web definidas.
8. Opcionalmente, defina as configurações de hora para seu dispositivo, como o fuso horário e os servidores NTP primários e secundários. Os servidores NTP são necessários, pois seu dispositivo deve sincronizar a hora para que ele possa se autenticar com seus provedores de serviço de nuvem.
   
    ![Configurações de hora](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    Na página **Configurações de hora** :
   
   1. Na lista suspensa, selecione o **Fuso horário** com base na localização geográfica em que o dispositivo está sendo implantado. O fuso horário padrão para o seu dispositivo é PST. Seu dispositivo usará esse fuso horário para todas as operações agendadas.
   2. Especifique um **Servidor NTP primário** para seu dispositivo ou aceite o valor padrão de time.windows.com. Verifique se sua rede permite que o tráfego NTP passe do data center para a Internet.
   3. Opcionalmente, especifique um **Servidor NTP secundário** para o dispositivo.
   4. Clique em **Aplicar**. Isso validará e aplicará as configurações de hora definidas.
9. Defina as configurações de nuvem para seu dispositivo. Nesta etapa, você concluirá a configuração de dispositivo local e, em seguida, registrará o dispositivo com o serviço Gerenciador de Dispositivos StorSimple.
   
   1. Insira a **Chave de registro do serviço** que você obteve na **Etapa 2: Obter a chave de registro do serviço** em [Implantar o StorSimple Virtual Array – Preparar o portal](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
   2. Se este não for o primeiro dispositivo que você está registrando nesse serviço, será necessário fornecer a **Chave de criptografia de dados do serviço**. Essa chave é necessária com a chave de registro do serviço para registrar dispositivos adicionais no serviço Gerenciador de Dispositivos StorSimple. Para obter mais informações, veja [Obter a chave de criptografia de dados do serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) na interface do usuário da Web local.
   3. Clique em **Registrar**. Isso reiniciará o dispositivo. Talvez seja necessário aguardar de 2 a 3 minutos até que o dispositivo seja registrado com êxito. Depois que o dispositivo for reiniciado, você será levado à página de entrada.
      
      ![Registrar dispositivo](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Volte para o portal do Azure.
11. Navegue até a folha **Dispositivos** do seu serviço. Se você tiver muitos recursos, clique em **Todos os recursos**, clique no nome do serviço (procure, se necessário) e clique em **Dispositivos**.
12. Na folha **Dispositivos** , verifique se o dispositivo conectou com êxito o serviço pesquisando o status. O status do dispositivo deve estar **Pronto para configurar**.
    
    ![Registrar dispositivo](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>Etapa 2: Configurar o dispositivo como servidor iSCSI

Execute as etapas a seguir no portal do Azure para concluir a configuração obrigatória do dispositivo.

#### <a name="to-configure-the-device-as-iscsi-server"></a>Para configurar o dispositivo como servidor iSCSI

1. Vá para o serviço Gerenciador de Dispositivos do StorSimple e vá para **Gerenciamento > Dispositivos**. Na folha **Dispositivos** , selecione o dispositivo que você acabou de criar. Este dispositivo deve ser mostrado como **Pronto para configurar**.
   
    ![Configurar dispositivo como servidor iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Clique no dispositivo e você verá uma mensagem de cabeçalho indicando que o dispositivo está pronto para a instalação.
   
    ![Configurar dispositivo como servidor iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Clique em **Configurar** na barra de comandos do dispositivo. Isso abre a folha **Configurar**. Na folha **Configurar**, faça o seguinte:
   
   * O nome do servidor iSCSI é preenchido automaticamente.
   * Verifique se a criptografia de armazenamento em nuvem está definida como **Habilitada**. Isso faz com que os dados enviados do dispositivo para a nuvem sejam criptografados.
   * Especifique uma chave de criptografia de 32 caracteres e grave-a em um aplicativo de gerenciamento de chaves para referência futura.
   * Selecione uma conta de armazenamento para ser usada com seu dispositivo. Nessa assinatura, você pode selecionar uma conta de armazenamento existente ou clicar **Adicionar** para escolher uma conta de uma assinatura diferente.
     
     ![Configurar dispositivo como servidor iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Clique em **Configurar** para concluir a configuração do servidor iSCSI.
   
    ![Configurar dispositivo como servidor iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. Você será informado de que a criação do servidor iSCSI está em andamento. Depois que o servidor iSCSI é criado com êxito, a folha **Dispositivos** é atualizada e o status do dispositivo correspondente fica **Online**.
   
    ![Configurar dispositivo como servidor iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>Etapa 3: Adicionar um volume

1. Na folha **Dispositivos**, selecione o dispositivo que você acabou de configurar como um servidor iSCSI. Clique em **...** (como alternativa, clique nesta linha com o botão direito do mouse) e, no menu de contexto, selecione **Adicionar volume**. Você também pode clicar em **+ Adicionar volume** na barra de comandos. Isso abre a folha **Adicionar volume**.
   
    ![Adicionar um volume](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. Na folha **Adicionar volume**, faça o seguinte:
   
   * No campo **Nome do volume**, insira um nome exclusivo para o seu volume. O nome deve ser uma cadeia de caracteres contendo entre 3 e 127 caracteres.
   * Na lista suspensa **Tipo**, especifique se deseja criar um volume **Em camadas** ou **Fixado localmente**. Para as cargas de trabalho que exigem garantias locais, latências baixas e um melhor desempenho, selecione **volume** **Localmente afixado**. Para todos os outros dados, selecione **Volume** em **camadas**.
   * No campo **Capacidade**, especifique o tamanho do volume. Um volume em camadas deve ter entre 500 GB e 5 TB e um volume fixado localmente deve ter entre 50 GB e 500 GB.
     
     Um volume fixado localmente é provisionado estaticamente e garante que os dados primários no volume permaneçam como locais para o dispositivo e não sejam divulgados na nuvem.
     
     Um volume em camadas, por outro lado, é provisionado dinamicamente. Quando você cria um volume em camadas, aproximadamente 10% do espaço é provisionado na camada de local e 90% do espaço é provisionado na nuvem. Por exemplo, se você provisionar um volume de 1 TB, 100 GB residiria no espaço local e 900 GB seria usado na nuvem quando os dados fossem distribuídos em camadas. Isso, por sua vez, implica que, se você ficar sem todo o espaço local no dispositivo, você não poderá provisionar um compartilhamento em camadas (porque 10% não estarão disponíveis).
     
     ![Adicionar um volume](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * Clique em **Hosts conectados**, selecione um ACR (registro de controle de acesso) correspondente ao iniciador iSCSI ao qual você deseja conectar esse volume e clique em **Selecionar**. <br><br> 
3. Para adicionar um novo host conectado, clique em **Adicionar novo**, insira um nome para o host e seu IQN (nome qualificado) iSCSI e clique em **Adicionar**. Se você não tiver o IQN, vá para [Apêndice A: Obter o IQN de um host do Windows Server](#appendix-a-get-the-iqn-of-a-windows-server-host).
   
      ![Adicionar um volume](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. Ao concluir a configuração de seu volume, clique em **OK**. Um volume será criado com as configurações especificadas e você verá uma notificação. Por padrão, monitoramento e backup estarão habilitados para o volume.
   
     ![Adicionar um volume](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. Para confirmar se o volume foi criado com êxito, vá para a folha **Volumes** . Você deve ver o volume listado.
   
   ![Adicionar um volume](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Etapa 4: Montar, inicializar e formatar um volume

Execute as etapas a seguir para montar, inicializar e formatar os volumes StorSimple em um host do Windows Server.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Para montar, inicializar e formatar um volume

1. Abra o aplicativo **Iniciador iSCSI** no servidor apropriado.
2. Na janela **Propriedades do Iniciador iSCSI** na guia **Descoberta**, clique em **Descobrir Portal**.
   
    ![Descobrir Portal](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. Na caixa de diálogo **Descobrir Portal de Destino**, forneça o endereço IP de seu adaptador de rede habilitado para iSCSI e clique em **OK**.
   
    ![Endereço IP](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. Na janela **Propriedades do Iniciador iSCSI** na guia **Destinos**, localize os **Destinos descobertos**. (Cada volume será um destino descoberto.) O status do dispositivo deve aparecer como **Inativo**.
   
    ![destinos descobertos](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Selecione um dispositivo de destino e clique em **Conectar**. Após o dispositivo ter sido conectado, o status deverá mudar para **Conectado**. (Para obter mais informações sobre como usar o iniciador iSCSI da Microsoft, veja [Instalando e configurando o iniciador iSCSI da Microsoft][1]).
   
    ![selecionar dispositivo de destino](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. No host do Windows, aperte a tecla do logotipo do Windows + X e depois clique em **Executar**.
7. Na caixa de diálogo **Executar**, digite **Diskmgmt.msc**. Clique em **OK** e a caixa de diálogo **Gerenciamento de Disco** será exibida. O painel do lado direito exibirá os volumes do seu host.
8. Na janela **Gerenciamento de Disco** , os volumes montados serão exibidos conforme exibido na ilustração a seguir. Clique com o botão direito no volume descoberto (clique no nome do disco) e depois clique em **Online**.
   
    ![Gerenciamento de Disco](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Clique com o botão direito do mouse e selecione **Inicializar Disco**.
   
    ![inicializar disco 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. Na caixa de diálogo, selecione os discos a serem inicializados e clique em **OK**.
    
    ![inicializar disco 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. O assistente de Novo Volume Simples é iniciado. Selecione um tamanho de disco e clique em **Avançar**.
    
    ![assistente de novo volume 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. Atribua uma letra da unidade ao volume e clique em **Avançar**.
    
    ![assistente de novo volume 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Insira os parâmetros para formatar o volume. **No Windows Server, há suporte somente para NTFS.** Defina o tamanho da unidade de alocação como 64K. Forneça um rótulo para o volume. É uma melhor prática recomendada que esse nome seja idêntico ao nome do volume fornecido em sua Matriz Virtual StorSimple. Clique em **Avançar**.
    
    ![assistente de novo volume 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Verifique os valores do volume e clique em **Concluir**.
    
    ![assistente de novo volume 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    Os volumes serão exibidos como **Online** on the **Gerenciamento de Disco** .
    
    ![volumes online](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Próximas etapas

Aprenda como [usar a interface do usuário da Web local para administrar sua StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Apêndice A: Obter o IQN de um host do Windows Server

Execute as etapas a seguir para obter o iSCSI IQN (Nome Qualificado) de um host do Windows que está executando o Windows Server 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Para obter o IQN do host do Windows

1. Inicie o iniciador Microsoft iSCSI no host do Windows.
2. Na janela **Propriedades do Iniciador iSCSI**, na guia **Configuração** selecione e copie a cadeia de caracteres do campo **Nome do Iniciador**.
   
    ![Propriedades do Iniciador iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Salve esta cadeia de caracteres.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



