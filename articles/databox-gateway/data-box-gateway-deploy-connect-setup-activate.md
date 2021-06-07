---
title: Conectar, configurar e ativar o Azure Data Box Gateway no portal do Azure
description: O terceiro tutorial para implantar o Data Box Gateway instrui você a conectar, configurar e ativar seu dispositivo virtual.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: cb6ef6ac25c4afa72160ba437e0ea3b5492cfd93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96579997"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Tutorial: Conectar, configurar e ativar o Azure Data Box Gateway

## <a name="introduction"></a>Introdução

Este tutorial descreve como conectar-se, configurar e ativar seu dispositivo Data Box Gateway usando a IU da Web local. 

O processo de configuração e ativação pode levar cerca de 10 minutos para ser concluído. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Conectar-se a um dispositivo virtual
> * Configurar e ativar o dispositivo virtual

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar seu Data Box Gateway, verifique se:

* Você provisionou um dispositivo virtual e obteve uma URL conectada a ele, conforme detalhado em [Provisionar um Data Box Gateway no Hyper-V](data-box-gateway-deploy-provision-hyperv.md) ou [Provisionar um Data Box Gateway no VMware](data-box-gateway-deploy-provision-vmware.md).
* Você tem a chave de ativação do serviço Data Box Gateway que você criou para gerenciar dispositivos Data Box Gateway. Para saber mais, acesse [Preparar para implantar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Conectar-se para a configuração de interface do usuário da web local 

1. Abrir uma janela do navegador e acessar a IU da Web local do dispositivo em:
   
   https:\//endereço-IP-do-adaptador-de-rede
   
   Use a URL de conexão observada no tutorial anterior. Você verá um erro ou um aviso indicando que há um problema com o certificado de segurança do site.

2. Selecione **Continuar para essa página da Web**. Essas etapas podem variar dependendo do navegador que você está usando.
   
    ![Mensagem de erro de certificado de segurança do site](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Entrar para o interface do usuário do seu dispositivo virtual da web. A senha padrão é *Senha1*. 
   
    ![Conectar-se à IU da Web local](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. No prompt, altere a senha do dispositivo. A nova senha deve conter entre 8 e 16 caracteres. Ela precisa conter três dentre estas opções: caracteres maiúsculos, minúsculos, numéricos e especiais.

    ![Alterar a senha de dispositivo](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Você está agora no **painel** do seu dispositivo.

## <a name="set-up-and-activate-the-virtual-device"></a>Configurar e ativar o dispositivo virtual
 
O painel exibe as várias configurações necessárias para configurar e registrar o dispositivo virtual com o serviço do Data Box Gateway. O **nome do dispositivo**, **configurações de rede**, **configurações de proxy Web**, e **configurações de hora** são opcionais. São as únicas configurações obrigatórias **configurações de nuvem**.
   
![Página "Painel" da IU da Web local](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. No painel esquerdo, selecione **Nome do dispositivo** e, em seguida, insira um nome amigável para seu dispositivo. O nome amigável deve conter de 1 a 15 caracteres e ter letras, números e hifens. 

    ![Página "Nome do dispositivo" da IU da Web local](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. (Opcional) No painel esquerdo, selecione **Configurações de rede** e, em seguida, defina as configurações. No dispositivo virtual, você vê pelo menos um adaptador de rede, mas esse número pode ser maior, dependendo de quantas delas você configurou na máquina virtual subjacente. A página **Configurações de rede** para um dispositivo virtual com uma interface de rede ativada é mostrada abaixo.
    
    ![Página "Configurações de rede" da IU da Web local](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Conforme você define as configurações de rede, tenha em mente:

    - Se o DHCP estiver ativado em seu ambiente, as interfaces de rede serão configuradas automaticamente. Um endereço IP, sub-rede, gateway e DNS são atribuídos automaticamente.
    - Se o DHCP não estiver ativado, você poderá atribuir IPs estáticos, se necessário.
    - Você pode configurar sua interface de rede como IPv4.

     >[!NOTE] 
     > É recomendável que você não alterne o endereço IP local do adaptador de rede de estático para DHCP, a menos que tenha outro endereço IP para se conectar ao dispositivo. Se estiver usando um adaptador de rede e alternar para DHCP, não haverá como determinar o endereço DHCP. Se você quiser alterar para um endereço DHCP, espere até o dispositivo ser registrado com o serviço e então altere. Posteriormente, você pode visualizar os IPs de todos os adaptadores em **Propriedades do dispositivo** no portal do Azure para seu serviço.

3. (Opcional) configure seu servidor proxy da Web. Embora a configuração do proxy Web seja opcional, se você usar um proxy Web, poderá configurá-lo apenas nesta página.
   
   ![Página "Configurações de proxy da Web" da IU da Web local](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Na página **Proxy Web**, faça o seguinte:
   
   1. Na **URL do proxy Web**, digite a URL neste formato: `http://&lt;host-IP address or FQDN&gt;:Port number`. Não há suporte para URLs HTTPS.
   2. Em **Autenticação**, selecione **Nenhuma** ou **NTLM**.
   3. Se você estiver usando a autenticação, insira um **Nome de usuário** e uma **Senha**.
   4. Para validar e aplicar as configurações de proxy da Web, selecione **Aplicar**.

   > [!NOTE]
   > Não há suporte para arquivos PAC (configuração automática de proxy). Um arquivo PAC define como os navegadores da Web e outros agentes de usuário podem escolher automaticamente o servidor proxy apropriado (método de acesso) para buscar uma determinada URL.
   > Os proxies que tentam interceptar e ler todo o tráfego (e depois assinar novamente tudo com a própria certificação) não são compatíveis, pois o certificado do proxy não é confiável.
   > Normalmente, os proxies transparentes funcionam bem com o Azure Data Box Gateway.

4. (Opcional) No painel esquerdo, selecione **Configurações de hora** e, em seguida, configure o fuso horário e os servidores NTP primários e secundários para seu dispositivo. 

    Os servidores NTP são necessários, pois seu dispositivo deve sincronizar a hora para que ele possa se autenticar com seus provedores de serviço de nuvem.
    
    ![Página "Configurações de tempo" da IU da Web local](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Na página **Configurações de hora**, faça o seguinte:
    
    1. Na lista suspensa **Fuso horário**, selecione o fuso horário que corresponde à localização geográfica na qual o dispositivo está implantado.
        O fuso horário padrão para o seu dispositivo é PST. Seu dispositivo usará esse fuso horário para todas as operações agendadas.

    2. Especifique um **Servidor NTP primário** para seu dispositivo ou aceite o valor padrão de `time.windows.com`.   
        Verifique se sua rede permite que o tráfego NTP passe do data center para a Internet.

    3. Opcionalmente, na caixa **Servidor NTP secundário**, digite um servidor secundário para seu dispositivo.

    4. Para validar e aplicar as configurações de hora configuradas, selecione **Aplicar**.

6. No painel esquerdo, selecione **Configurações de nuvem** e, em seguida, ative seu dispositivo com o serviço do Data Box Gateway no portal do Azure.
    
    1. Na caixa **Chave de ativação**, digite a **chave de ativação** recebida em [Obter chave de ativação](data-box-gateway-deploy-prep.md#get-the-activation-key) para o Data Box Gateway.

    2. Selecione **Ativar**.
       
         ![Página "Configurações de nuvem" da IU da Web local](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. O dispositivo está ativado e as atualizações críticas, se disponíveis, são aplicadas automaticamente. Você verá uma notificação nesse sentido. Monitore o progresso da atualização por meio do portal do Azure.

        ![Página das "Configurações de nuvem" da IU da Web local 2](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **A caixa de diálogo também tem uma chave de recuperação que você deve copiar e salvar em um local seguro. Essa chave é usada para recuperar os dados no caso de o dispositivo não poder ser inicializado.**


    4. Talvez você precise aguardar alguns minutos para que a atualização seja concluída com êxito. Após a atualização ser concluída, conecte-se ao dispositivo. A página **Configurações de nuvem** será atualizada para indicar que o dispositivo foi ativado com êxito.

        ![Página de "Configurações de nuvem" da IU da Web local atualizada](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

A configuração do dispositivo foi concluída. Agora você pode adicionar compartilhamentos em seu dispositivo.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Conectar-se a um dispositivo virtual
> * Configurar e ativar o dispositivo virtual

Para saber como transferir dados com o seu Data Box Gateway, consulte:

> [!div class="nextstepaction"]
> [Transferir dados com o Data Box Gateway](./data-box-gateway-deploy-add-shares.md).
