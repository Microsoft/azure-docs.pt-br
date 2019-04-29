---
title: Configurar a StorSimple Virtual Array como um servidor de arquivos | Microsoft Docs
description: Este terceiro tutorial na implantação de StorSimple Virtual Array lhe instrui a configurar um dispositivo virtual como servidor de arquivos.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: f609f6ff-0927-48bb-a68a-6d8985d2fe34
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a931b303e40e41bc23e8b586e1d37e600625b1a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61414843"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>Implantar o StorSimple Virtual Array — configurar como um servidor de arquivos por meio do portal do Azure
![](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Introdução
Este artigo descreve como executar a configuração inicial, registrar o servidor de arquivos do StorSimple, concluir a configuração do dispositivo, criar compartilhamentos SMB e conectar-se a eles. Este é o último artigo da série de tutoriais de implantação necessários para implantar completamente sua matriz virtual como um servidor de arquivos ou um servidor iSCSI.

O processo de preparação e configuração pode levar aproximadamente 10 minutos para ser concluído. As informações neste artigo se aplicam apenas à implantação da Matriz Virtual StorSimple. Para a implantação de dispositivos da série StorSimple 8000, vá para: [Implantar seu dispositivo da série StorSimple 8000 executando a Atualização 2](storsimple-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Pré-requisitos de configuração
Antes de configurar e de instalar a Matriz Virtual StorSimple, verifique se:

* Você provisionou uma matriz virtual e se conectou a ela conforme detalhado em [Provisionar uma Matriz Virtual StorSimple no Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) ou [Provisionar uma Matriz Virtual StorSimple no VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Você tem a chave de registro do serviço Gerenciador de Dispositivos StorSimple que você criou para gerenciar Matrizes Virtuais StorSimple. Para obter mais informações, confira a [Etapa 2: Obter a chave de registro do serviço](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) para a Matriz Virtual StorSimple.
* Se essa for a segunda matriz virtual ou a subsequente que você está registrando com um serviço Gerenciador de Dispositivos StorSimple existente, você deverá ter a chave de criptografia de dados do serviço. Essa chave foi gerada quando o primeiro dispositivo foi registrado com êxito com esse serviço. Caso tenha perdido essa chave, veja [Obter a chave de criptografia de dados de serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) do StorSimple Virtual Array.

## <a name="step-by-step-setup"></a>Configuração passo a passo
Use as instruções passo a passo a seguir para preparar e configurar sua Matriz Virtual StorSimple.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Etapa 1: Concluir a configuração da IU da Web local e registrar seu dispositivo
#### <a name="to-complete-the-setup-and-register-the-device"></a>Para concluir a configuração e registrar o dispositivo
1. Abra uma janela do navegador e conecte-se à interface do usuário da Web local. Digite:
   
   `https://<ip-address of network interface>`
   
   Use a URL de conexão observada na etapa anterior. Você verá um erro indicando que há um problema com o certificado de segurança do site. Clique em **Continuar para essa página da Web**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Entre na interface do usuário da Web da sua matriz virtual como **StorSimpleAdmin**. Digite a senha do administrador do dispositivo que você alterou na Etapa 3: Inicie a matriz virtual em [Provisionar um Matriz Virtual StorSimple no Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) ou em [Provisionar uma Matriz Virtual StorSimple no VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. Você será direcionado para a página **Início**. Esta página descreve as várias configurações necessárias para configurar e registrar a matriz virtual com o serviço Gerenciador de Dispositivos StorSimple. As **Configurações de rede**, as **Configurações de proxy Web** e as **Configurações de hora** são opcionais. As únicas configurações obrigatórias são as **Configurações do dispositivo** e **Configurações de nuvem**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. Na página **Configurações de rede**, em **Adaptadores de rede**, DATA 0 será configurado automaticamente para você. Cada interface de rede é definida por padrão para obter um endereço IP automaticamente (DHCP). Assim, um endereço IP, uma sub-rede e um gateway serão atribuídos automaticamente (tanto para IPv4 quanto para IPv6).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Se você adicionou mais de uma interface de rede durante o provisionamento do dispositivo, você pode configurá-las aqui. Observe que você pode configurar a interface de rede apenas como IPv4 ou como IPv4 e IPv6. Não há suporte para configurações somente IPv6.
5. Os servidores DNS são necessários porque eles são usados quando o dispositivo tenta se comunicar com seus provedores de serviço de armazenamento de nuvem, ou então para resolver seu dispositivo por nome caso ele esteja configurado como um servidor de arquivos. Na página **Configurações de rede**, em **Servidores DNS**:
   
   1. Um servidor DNS primário e um secundário são configurados automaticamente. Se você optar por configurar endereços IP estáticos, você pode especificar servidores DNS. Para alta disponibilidade, recomendamos que você configure um servidor DNS primário e um secundário.
   2. Clique em **Aplicar** para aplicar e validar as configurações de rede.
6. Na página **Configurações do dispositivo** :
   
   1. Atribua um **Nome** exclusivo ao seu dispositivo. Esse nome pode ter de 1 a 15 caracteres e pode conter letras, números e hifens.
   2. Clique no ícone ![](./media/storsimple-virtual-array-deploy3-fs-setup/image6.png) do **Servidor de arquivos** para o **Tipo** de dispositivo que você está criando. Um servidor de arquivos permitirá que você crie pastas compartilhadas.
   3. Como o dispositivo é um servidor de arquivos, você precisará ingressar o dispositivo em um domínio. Insira um **Nome de domínio**.
   4. Clique em **Aplicar**.
7. Uma caixa de diálogo aparecerá. Insira suas credenciais de domínio no formato especificado. Clique no ícone de verificação. As credenciais de domínio são verificadas. Você verá uma mensagem de erro se as credenciais estiverem incorretas.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Clique em **Aplicar**. Isso aplicará e validará as configurações do dispositivo.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Certifique-se de que a matriz virtual esteja em sua própria OU (unidade organizacional) do Active Directory e que nenhum GPO (objeto de política de grupo) seja aplicado a ela ou herdado. A política de grupo pode instalar aplicativos como, software antivírus, no StorSimple Virtual Array. Não há suporte para a instalação de software adicional e isso pode levar a dados corrompidos. 
   > 
   > 
9. Opcionalmente, configure seu servidor proxy da Web. Embora a configuração do proxy da Web seja opcional, saiba que se você usar um proxy da Web, só poderá configurá-lo aqui.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   Na página **Proxy Web** :
   
   1. Forneça a **URL do proxy Web** neste formato: *http://&lt;endereço IP do host ou FDQN:&gt; número da porta*. Observe que não há suporte para URLs HTTPS.
   2. Especifique a **Autenticação** como **Básica** ou **Nenhuma**.
   3. Se você estiver usando autenticação, você também precisará fornecer um **Nome de Usuário** e **Senha**.
   4. Clique em **Aplicar**. Isso validará e aplicará as configurações de proxy Web definidas.
10. Opcionalmente, defina as configurações de hora para seu dispositivo, como o fuso horário e os servidores NTP primários e secundários. Os servidores NTP são necessários, pois seu dispositivo deve sincronizar a hora para que ele possa se autenticar com seus provedores de serviço de nuvem.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    Na página **Configurações de hora** :
    
    1. Na lista suspensa, selecione o **Fuso horário** com base na localização geográfica na qual o dispositivo está sendo implantado. O fuso horário padrão para o seu dispositivo é PST. Seu dispositivo usará esse fuso horário para todas as operações agendadas.
    2. Especifique um **Servidor NTP primário** para seu dispositivo ou aceite o valor padrão de time.windows.com. Verifique se sua rede permite que o tráfego NTP passe do data center para a Internet.
    3. Opcionalmente, especifique um **Servidor NTP secundário** para o dispositivo.
    4. Clique em **Aplicar**. Isso validará e aplicará as configurações de hora definidas.
11. Defina as configurações de nuvem para seu dispositivo. Nesta etapa, você concluirá a configuração de dispositivo local e, em seguida, registrará o dispositivo com o serviço Gerenciador de Dispositivos StorSimple.
    
    1. Insira a **Chave de registro do serviço** que você obteve na [Etapa 2: Obter a chave de registro do serviço](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) para a Matriz Virtual StorSimple.
    2. Se esse for o primeiro dispositivo registrado nesse serviço, você verá a **Chave de criptografia de dados de serviço**. Copie essa chave e salve-a em um local seguro. Essa chave é necessária com a chave de registro do serviço para registrar dispositivos adicionais no serviço Gerenciador de Dispositivos StorSimple. 
       
       Se não for o primeiro dispositivo que você está registrando com esse serviço, você precisará fornecer a chave de criptografia de dados de serviço. Para obter mais informações, consulte obter a [chave de criptografia de dados de serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) em sua interface do usuário da Web local.
    3. Clique em **Registrar**. Isso reiniciará o dispositivo. Talvez seja necessário aguardar de 2 a 3 minutos até que o dispositivo seja registrado com êxito. Depois que o dispositivo for reiniciado, você será levado à página de entrada.
       
       ![](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Volte para o portal do Azure. Vá para **Todos os recursos**, procure seu serviço Gerenciador de Dispositivos do StorSimple.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. Na lista filtrada, selecione o serviço Gerenciador de Dispositivos do StorSimple e então navegue até **Gerenciamento > Dispositivos**. Na folha **Dispositivos**, verifique se o dispositivo se conectou com êxito ao serviço e se tem o status **Pronto para configurar**.
    
    ![Configurar um servidor de arquivos](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>Etapa 2: Configurar o dispositivo como servidor de arquivos
Execute as etapas a seguir no [portal do Azure](https://portal.azure.com/) para concluir a configuração obrigatória do dispositivo.

#### <a name="to-configure-the-device-as-file-server"></a>Para configurar o dispositivo como servidor de arquivos
1. Vá para o serviço Gerenciador de Dispositivos do StorSimple e vá para **Gerenciamento > Dispositivos**. Na folha **Dispositivos** , selecione o dispositivo que você acabou de criar. Este dispositivo deve ser mostrado como **Pronto para configurar**.
   
   ![Configurar um servidor de arquivos](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Clique no dispositivo e você verá uma mensagem de cabeçalho indicando que o dispositivo está pronto para a instalação.
   
    ![Configurar um servidor de arquivos](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Clique em **Configurar** na barra de comandos. Isso abrirá a folha **Configurar**. Na folha **Configurar**, faça o seguinte:
   
   1. O nome do servidor de arquivos é preenchido automaticamente.
    
   2. Verifique se a criptografia de armazenamento em nuvem está definida como **Habilitada**. Isso irá criptografar todos os dados enviados para a nuvem. 
    
   3. Uma chave AES de 256 bits é ser usada com a chave de criptografia definida pelo usuário. Especifique uma chave de 32 caracteres e, em seguida, insira novamente a chave para confirmá-la. Registre a chave em um aplicativo de gerenciamento de chaves para referência futura.
    
   4. Clique em **Definir configurações necessárias** para especificar as credenciais de conta de armazenamento a serem usadas com o dispositivo. Clique em **adicionar novo** se não houver nenhuma credencial de conta de armazenamento configurada. **Certifique-se de que a conta de armazenamento usada oferece suporte a blobs de bloco. Blobs de página não têm suporte.** Para obter mais informações sobre [blobs de blocos e blobs de página](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).
   
      ![Configurar um servidor de arquivos](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. Na folha **Adicionar credenciais de uma conta de armazenamento**, faça o seguinte: 

    1. Escolha a assinatura atual caso a conta de armazenamento esteja na mesma assinatura do que o serviço. Especifique outra se a conta de armazenamento estiver fora da assinatura do serviço. 
    
    2. Na lista suspensa, escolha uma conta de armazenamento existente. 
    
    3. O local será preenchido automaticamente com base na conta de armazenamento especificada. 
    
    4. Habilite o SSL para garantir um canal de comunicação de rede seguro entre o dispositivo e a nuvem.
    
    5. Clique em **Adicionar** para adicionar essa credencial de conta de armazenamento. 
   
        ![Configurar um servidor de arquivos](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. Depois que a credencial de conta de armazenamento tiver sido criada com êxito, a folha **Configurar** será atualizada para exibir as credenciais de conta de armazenamento especificadas. Clique em **Configurar**.
   
   ![Configurar um servidor de arquivos](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   Você verá que um servidor de arquivos está sendo criado. Quando o servidor de arquivos for criado com êxito, você será notificado.
   
   ![Configurar um servidor de arquivos](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   O status do dispositivo também será alterado para **Online**.
   
   ![Configurar um servidor de arquivos](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   Você pode prosseguir para a adição de um compartilhamento.

## <a name="step-3-add-a-share"></a>Etapa 3: Adicionar um compartilhamento
Execute as etapas a seguir no [portal do Azure](https://portal.azure.com/) para criar um compartilhamento.

#### <a name="to-create-a-share"></a>Para criar um compartilhamento
1. Selecione o dispositivo de servidor de arquivos configurado na etapa anterior e clique em **...**  (ou clique com o botão direito do mouse). No menu de contexto, selecione **Adicionar compartilhamento**. Como alternativa, você pode clicar em **+ Adicionar Compartilhamento** na barra de comandos do dispositivo.
   
   ![Adicionar um compartilhamento](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Especifique as configurações de compartilhamento a seguir:

   1. Um nome exclusivo para seu compartilhamento. O nome deve ser uma cadeia de caracteres contendo entre 3 e 127 caracteres.
    
   2. Uma **Descrição** opcional para o compartilhamento. A descrição ajudará a identificar os proprietários de compartilhamento.
    
   3. Um **Tipo** para o compartilhamento. O tipo pode ser **Em camadas** ou **Localmente afixado**, sendo que Em camadas é o padrão. Para cargas de trabalho que exigem garantias locais, menos latências e um melhor desempenho, selecione um compartilhamento **Fixado localmente** . Para todos os outros dados, selecione um compartilhamento **Em camadas** .
      Um compartilhamento fixado localmente é provisionado estaticamente e garante que os dados primários no compartilhamento permaneçam como locais para o dispositivo e não sejam divulgados na nuvem. Um compartilhamento em camadas, por outro lado, é provisionado dinamicamente. Quando você cria um volume em camadas, aproximadamente 10% do espaço é provisionado na camada local e 90% do espaço é provisionado na nuvem. Por exemplo, se você provisionar um volume de 1 TB, 100 GB residiriam no espaço local e 900 GB seriam usados na nuvem quando os dados fossem distribuídos em camadas. Isso, por sua vez, implica que se você ficar sem todo o espaço local no dispositivo, você não poderá provisionar um compartilhamento em camadas.
   
   4. No campo **Definir permissões padrão completas a**, atribua as permissões para o usuário ou para o grupo que está acessando esse compartilhamento. Especifique o nome do usuário ou grupo de usuários no *john\@contoso.com* formato. É recomendável que você use um grupo de usuários (em vez de um único usuário) para conceder privilégios de administrador para acessar esses compartilhamentos. Depois de atribuir as permissões aqui, você pode usar o Gerenciador de Arquivos para modificar essas permissões.
   
   5. Clique em **Adicionar** para criar o compartilhamento. 
    
       ![Adicionar um compartilhamento](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
       Você será notificado de que a criação do compartilhamento está em andamento.
   
       ![Adicionar um compartilhamento](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
      Depois que o compartilhamento tiver sido criado com as configurações especificadas, a folha **Compartilhamentos** será atualizada para refletir o novo compartilhamento. Por padrão, o monitoramento e o backup estão habilitados para o compartilhamento.
   
      ![Adicionar um compartilhamento](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>Etapa 4: Conectar-se ao compartilhamento
Agora, você precisará conectar-se a um ou mais compartilhamentos que você criou na etapa anterior. Execute estas etapas no host do Windows Server conectado à sua Matriz Virtual StorSimple.

#### <a name="to-connect-to-the-share"></a>Para conectar-se ao compartilhamento
1. Pressione ![](./media/storsimple-virtual-array-deploy3-fs-setup/image22.png) + R. Na janela Executar, especifique *&#92;&#92;&lt;nome do servidor de arquivos&gt;* como o caminho, substituindo *nome do servidor de arquivos* pelo nome do dispositivo que você atribuiu ao seu servidor de arquivos. Clique em **OK**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Isso abre o Gerenciador de Arquivos. Agora você deverá ser capaz de ver os compartilhamentos que criou como pastas. Selecione e clique duas vezes em um compartilhamento (pasta) para exibir o conteúdo.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. Agora você pode adicionar arquivos a esses compartilhamentos e fazer um backup.

## <a name="next-steps"></a>Próximas etapas
Aprenda como [usar a interface do usuário da Web local para administrar sua StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

