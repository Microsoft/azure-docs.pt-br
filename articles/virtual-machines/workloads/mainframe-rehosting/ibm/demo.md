---
title: Defina o backup de um aplicativo desenvolvedores controlado por distribuição (ADCD) no IBM zD & T v1 | Microsoft Docs
description: Execute um ambiente de ambiente de teste (zD & T) e o IBM Z Development em máquinas virtuais do Azure (VMs).
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: f8af19056a343abdbafcd4ead8b072330cb41fd9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61438397"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Defina o backup de um aplicativo desenvolvedores controlado por distribuição (ADCD) no IBM zD & T v1

Você pode executar um ambiente de ambiente de teste (zD & T) e o IBM Z Development em máquinas virtuais do Azure (VMs). Esse ambiente emula a arquitetura da IBM Z Series. Ele pode hospedar uma variedade de sistemas operacionais de série de Z ou instalações (também chamadas pacotes ou instâncias de Z), que são disponibilizadas por meio de pacotes personalizados chamados a IBM aplicativo desenvolvedores controlado distribuições (ADCDs).

Este artigo mostra como configurar uma instância ADCD em um zD & ambiente T no Azure. ADCDs criar implementações completas de sistema operacional de série de Z para ambientes de desenvolvimento e testes que são executados no zD & T.

Como zD & T, ADCDs estão disponíveis somente para parceiros e clientes da IBM e são exclusivamente para fins de teste e desenvolvimento. Eles não devem ser usadas para ambientes de produção. Vários pacotes de instalação do IBM estão disponíveis para download por meio [vantagem do Passport](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) ou [IBM PartnerWorld](https://www-356.ibm.com/partnerworld/wps/servlet/ContentHandler/isv_com_sys_zos_adcd).

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- O [zD & ambiente T] [ ibm-install-z] configurado anteriormente no Azure. Este artigo pressupõe que você está usando a mesma imagem de VM do Ubuntu 16.04 criada anteriormente.

- Acesso à mídia ADCD por meio do IBM PartnerWorld ou vantagem do Passport.

- Um [servidor de licenciamento](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Isso é necessário para executar a IBM zD & T. A maneira de criá-lo depende de como você licencia o software da IBM:

  - **Servidor de licenciamento com base em hardware** requer um dispositivo de hardware USB que contém os Tokens racional necessárias para acessar todas as partes do software. Esse deve ser obtido da IBM.

  - **Servidor de licenciamento baseado em software** exige que você configurar um servidor centralizado para o gerenciamento das chaves de licenciamento. Esse método é preferido e exige que você configure as chaves que você receber da IBM no servidor de gerenciamento.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Baixar os pacotes de instalação do Passport vantagem

Acesso à mídia ADCD é necessário. As etapas a seguir pressupõem que são um clientes da IBM e pode usar a vantagem do Passport. IBM os parceiros podem usar [IBM PartnerWorld](https://www-356.ibm.com/partnerworld/wps/servlet/ContentHandler/isv_com_sys_zos_adcd).

> [!NOTE]
> Este artigo pressupõe que um PC Windows é usado para acessar o portal do Azure e baixar a mídia da IBM. Se você estiver usando uma área de trabalho Mac ou Ubuntu, o processo para obter a mídia da IBM e comandos podem diferir ligeiramente.

1. Faça logon no [Passport vantagem](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer).

2. Selecione **Downloads de Software** e **acesso à mídia**.

3. Selecione **número da oferta e o contrato do programa**e clique em **continuar**.

4. Insira a descrição do componente ou o número de peça e clique em **Finder**.

5. Opcionalmente, clique na lista de ordem alfabética para exibir e visualizar o produto por nome.

6. Selecione **todos os sistemas operacionais** na **campo do sistema operacional**, e **todos os idiomas** no **campo idiomas**. Em seguida, clique em **acesse**.

7. Clique em **selecionar arquivos individuais** para expandir a lista e exibir a mídia individual para baixar.

8. Verifique se o pacote (s) que você deseja fazer o download, selecione **baixar**e, em seguida, baixe os arquivos para o diretório que você deseja.

## <a name="upload-the-adcd-packages"></a>Carregar o pacote de ADCD (s)

Agora que você tem o pacote (s), você deve carregá-los à sua VM no Azure.

1. No portal do Azure, inicie uma **ssh** sessão com a VM do Ubuntu que você criou. Vá para sua VM, selecione a **visão geral** folha e, em seguida, selecione **Connect**.

2. Selecione o **SSH** guia e, em seguida, copie o comando ssh para a área de transferência.

3. Faça logon em sua VM usando suas credenciais e o [cliente SSH](/azure/virtual-machines/linux/use-remote-desktop) de escolha. Esta demonstração usa extensões do Linux para Windows 10, que adiciona um shell bash para o prompt de comando do Windows. PuTTY funciona tão bem.

4. Quando estiver conectado, crie um diretório para carregar os pacotes da IBM. Tenha em mente Linux é diferencia maiusculas de minúsculas. Por exemplo, esta demonstração pressupõe que os pacotes são carregados para:

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. Carregue os arquivos usando um cliente SSH, como[WinSCP](https://winscp.net/eng/index.php). Como o SCP é uma parte do SSH, ele usa a porta 22, que é o SSH usa. Se seu computador local não for Windows, você pode digitar o [comando scp](http://man7.org/linux/man-pages/man1/scp.1.html) na sua sessão do SSH.

6. Iniciar o upload para o diretório de VM do Azure que você criou, que se torna o armazenamento de imagens para zD & T.

    > [!NOTE]
    > Certifique-se de que **ADCDTOOLS. XML** está incluído no carregamento para o **home/MyUserID/ZDT/adcd/nov2017** directory. Você precisará dela mais tarde.

7. Aguarde até que os arquivos a serem carregados, o que podem levar algum tempo dependendo da sua conexão para o Azure.

8. Quando os carregamentos forem concluídos, navegue até o diretório de volumes e descompactar todos os **gz** volumes:

    ```
        gunzip \*.gz
    ```
    
![Explorador de arquivos mostrando descompactado gz volumes](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>Configurar o armazenamento de imagem

A próxima etapa é configurar zD & T para usar o pacote carregado (s). O processo de armazenamento de imagem dentro de zD & T permite montar e usar as imagens. Ele pode usar o SSH ou FTP.

1. Iniciar o **zDTServer**. Para fazer isso, você deve ser no nível raiz. Insira dois comandos a seguir na ordem:
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. Observe a URL de saída pelo comando e use essa URL para acessar o servidor web. Ele é semelhante a:
     > https://(Your VM Name or IP Address):9443/ZDTMC/index.HTML
     >
     > Lembre-se de que o acesso via web usa a porta 9443. Use isso para fazer logon no servidor web. É a ID de usuário para ZD & tar **zdtadmin** e a senha é **senha**.

    ![IBM zD & tela T Enterprise Edition bem-vindo ao](media/02-welcome.png)

3. Sobre o **início rápido** página, em **configurar**, selecione **armazenamento de imagem**.

     ![IBM zD & tela de início rápido do T Enterprise Edition](media/03-quickstart.png)

4. Sobre o **configurar o armazenamento de imagem** página, selecione **SSH File Transfer Protocol**.

5. Para **nome do Host**, digite **Localhost** e insira o caminho do diretório para onde você carregou as imagens. Por exemplo, /home/MyUserID/ZDT/adcd/nov2017/volumes.

6. Insira o **ID de usuário** e **senha** para a VM. Não use o ZD & T ID de usuário e senha.

7. Testar a conexão para garantir que você tem acesso e, em seguida, selecione **salvar** para salvar a configuração.

## <a name="configure-the-target-environments"></a>Configurar os ambientes de destino

A próxima etapa é configurar o ambiente de destino do T & zD. Esse ambiente hospedado emulado é onde suas imagens é executado.

1. Sobre o **início rápido** página, em **configurar**, selecione **ambientes de destino**.

2. Sobre o **configurar ambientes de destino** página, selecione **adicionar um destino**.

3. Selecione **Linux**. IBM oferece suporte a dois tipos de ambientes, Linux e Cloud(OpenStack), mas esta demonstração é executado no Linux.

4. Sobre o **adicionar ambiente de destino** página, para **nome do Host**, insira **localhost**. Manter **porta SSH** definido como **22**.

5. No **rótulo do ambiente de destino** , digite um rótulo, como **MyCICS.**

     ![Adicionar a tela de ambiente de destino](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>Configurar ADCD e implantar

Depois de concluir as etapas de configuração anterior, você deve configurar zD & T para usar o ambiente de pacote (s) e de destino. Novamente, você pode usar o processo de armazenamento de imagem no zD & T, que lhe permite montar e usar as imagens. Ele pode usar o SSH ou FTP.

1. Sobre o **início rápido** página, em **configurar**, selecione **ADCD**. Um conjunto de instruções é exibida, informando a você as etapas que precisam ser concluídas antes que um pacote ADCD pode ser montado. Isso explica por que nomeamos o diretório de destino como fizemos anteriormente.

2. Supondo que todas as imagens foram carregadas nos diretórios corretos, clique o **imagem de ADCD** link exibido no canto inferior direito (mostrado na etapa 7 na seguinte captura de tela).

     ![IBM zD & T Enterprise Edition - tela Configurar ADCD](media/05-adcd.png)

## <a name="create-the-image"></a>Criar a imagem

Quando a etapa de configuração anterior for concluída, o **criar uma imagem usando componentes ADCD** página será exibida.

1. Selecione o volume (novembro de 2017 neste caso) para exibir os diferentes pacotes que estão nesse volume.

2. Para esta demonstração, selecione **cliente informações de controle de sistema (CICS) - 5.3**.

3. No **nome da imagem** , digite um nome para a imagem, como **MyCICS imagem**.

4. Selecione o **criar imagem** botão no canto inferior direito.

     ![IBM zD & T Enterprise Edition - criar uma imagem usando a tela componentes ADCD](media/06-adcd.png)

5. Na janela que aparece, informando que a imagem foi implantada com êxito, escolha **implantar imagens**.

6. Sobre o **implantar uma imagem em um ambiente de destino** , selecione a imagem que você criou na página anterior (**MyCICS imagem**) e o ambiente de destino criado anteriormente (**MyCICS**).

7. Na próxima tela, forneça suas credenciais para a VM (ou seja, não a ztadmin credencial).

8. No painel Propriedades, insira o número de **processadores Central (CPs)**, a quantidade de **(GB) de memória do sistema**e o **diretório de implantação** para a imagem em execução. Como essa é uma demonstração, mantenha pequenos.

9. Verifique se a caixa está marcada para **automaticamente o problema IPL comando para z/OS, depois de implantar**.

     ![Tela de propriedades](media/07-properties.png)

10. Selecione **completa**.

11. Selecione **implantar imagem** da **implantar uma imagem em um ambiente de destino** página.

Sua imagem agora pode implantar e está pronta para ser montados por um emulador de terminal 3270.

> [!NOTE]
> Se você receber um erro informando que você não tem espaço em disco suficiente, observe que a região requer 151 Gb.

Parabéns! Agora você está executando um ambiente de mainframe IBM no Azure.

## <a name="learn-more"></a>Saiba mais

- [Migração de mainframe: mitos e fatos](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM DB2 pureScale no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [Solução de problemas](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Desmistificando mainframe para a migração do Azure](https://azure.microsoft.com/en-us/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
