---
title: Configurar uma ADCD (distribuição controlada por desenvolvedores de aplicativos) no IBM zD&T v1 | Microsoft Docs
description: Execute um ambiente de desenvolvimento e teste do IBM Z (zD&T) em VMs (máquinas virtuais) do Azure.
services: virtual-machines
ms.service: virtual-machines
ms.subservice: workloads
documentationcenter: ''
author: njray
manager: edprice
ms.author: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: d390cca483444dadd278d783c5f93e09a1bf1ff8
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549440"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Configurar uma ADCD (distribuição controlada por desenvolvedores de aplicativos) no IBM zD&T v1

Você pode executar um ambiente de desenvolvimento e teste do IBM Z (zD&T) em VMs (máquinas virtuais) do Azure. Esse ambiente emula a arquitetura IBM Z Series. Ele pode hospedar uma variedade de sistemas operacionais ou instalações da série Z (também chamadas de instâncias Z ou pacotes), disponibilizados por meio de grupos personalizados chamados de ADCDs (distribuições controladas por desenvolvedores de aplicativos IBM).

Este artigo mostra como configurar uma instância do ADCD em um ambiente zD&T no Azure. ADCDs criar implementações completas do sistema operacional da série Z para ambientes de desenvolvimento e teste executados no zD&T.

Como zD&T, ADCDs estão disponíveis apenas para clientes e parceiros da IBM e são exclusivamente para fins de desenvolvimento e teste. Eles não devem ser usados para ambientes de produção. Vários pacotes de instalação IBM estão disponíveis para download por meio do [Passport Advantage](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) ou [IBM Partnerworld](https://www.ibm.com/partnerworld/public).

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- O [ambiente zD&T][ibm-install-z] configurado anteriormente no Azure. Este artigo pressupõe que você esteja usando a mesma imagem de VM Ubuntu 16, 4 criada anteriormente.

- Acesso à mídia ADCD por meio do IBM PartnerWorld ou do Passport Advantage.

- Um [servidor de licenciamento](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Isso é necessário para executar o IBM zD&T. A maneira como você cria isso depende de como você licencia o software da IBM:

  - O **servidor de licenciamento baseado em hardware** requer um dispositivo de hardware USB que contenha os tokens racional necessários para acessar todas as partes do software. Você deve obtê-lo da IBM.

  - O **servidor de licenciamento baseado em software** exige que você configure um servidor centralizado para o gerenciamento das chaves de licenciamento. Esse método é preferencial e exige que você configure as chaves que você recebe da IBM no servidor de gerenciamento.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Baixar os pacotes de instalação do Passport Advantage

O acesso à mídia ADCD é necessário. As etapas a seguir pressupõem que você seja um cliente da IBM e possa usar o Passport Advantage. Os parceiros da IBM podem usar o [IBM Partnerworld](https://www.ibm.com/partnerworld/public).

> [!NOTE]
> Este artigo pressupõe que um computador Windows é usado para acessar portal do Azure e baixar a mídia IBM. Se você estiver usando uma área de trabalho do Mac ou Ubuntu, os comandos e o processo para obter a mídia IBM podem diferir ligeiramente.

1. Faça logon no [Passport Advantage](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer).

2. Selecione **downloads de software** e **acesso à mídia**.

3. Selecione **oferta do programa e número do contrato** e clique em **continuar**.

4. Insira a descrição da parte ou o número da peça e clique em **Finder**.

5. Opcionalmente, clique na lista ordem alfabética para exibir e exibir o produto por nome.

6. Selecione **todos os sistemas operacionais** no **campo sistema operacional** e **todos os idiomas** no **campo idiomas**. Em seguida, clique em **ir**.

7. Clique em **selecionar arquivos individuais** para expandir a lista e exibir a mídia individual a ser baixada.

8. Verifique os pacotes que você deseja baixar, selecione **baixar** e, em seguida, baixe os arquivos para o diretório desejado.

## <a name="upload-the-adcd-packages"></a>Carregar os pacotes ADCD

Agora que você tem os pacotes, você deve carregá-los em sua VM no Azure.

1. Na portal do Azure, inicie uma sessão **SSH** com a VM do Ubuntu que você criou. Vá para sua VM, selecione a folha **visão geral** e, em seguida, selecione **conectar**.

2. Selecione a guia **SSH** e copie o comando ssh para a área de transferência.

3. Faça logon em sua VM usando suas credenciais e o [cliente SSH](../../../linux/use-remote-desktop.md) de sua escolha. Esta demonstração usa as extensões do Linux para Windows 10, que adiciona um shell bash ao prompt de comando do Windows. A reproduzida também funciona muito bem.

4. Quando conectado, crie um diretório para carregar os pacotes IBM. Tenha em mente que o Linux diferencia maiúsculas de minúsculas. Por exemplo, esta demonstração pressupõe que os pacotes sejam carregados para:

    `/home/MyUserID/ZDT/adcd/nov2017/volumes`

5. Carregue os arquivos usando um cliente SSH, como[WinSCP](https://winscp.net/eng/index.php). Como o SCP faz parte do SSH, ele usa a porta 22, que é usada pelo SSH. Se o computador local não for Windows, você poderá digitar o [comando scp](http://man7.org/linux/man-pages/man1/scp.1.html) em sua sessão SSH.

6. Inicie o upload para o diretório de VM do Azure que você criou, que se torna o armazenamento de imagem para zD&T.

    > [!NOTE]
    > Verifique se **ADCDTOOLS.XML** está incluído no upload para o diretório **Home/myuserid/ZDT/adcd/nov2017** . Você precisará dela mais tarde.

7. Aguarde até que os arquivos sejam carregados, o que pode levar algum tempo, dependendo de sua conexão com o Azure.

8. Quando os carregamentos forem concluídos, navegue até o diretório volumes e descompacte todos os volumes **gz** :

    ```console
    gunzip \*.gz
    ```
    
![Explorador de arquivos mostrando volumes gz descompactados](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>Configurar o armazenamento de imagens

A próxima etapa é configurar zD&T para usar os pacotes carregados. O processo de armazenamento de imagens no zD&T permite que você monte e use as imagens. Ele pode usar SSH ou FTP.

1. Inicie o **zDTServer**. Para fazer isso, você deve estar no nível raiz. Insira os dois comandos a seguir na ordem:
    ```console
    sudo su -
    /opt/ibm/zDT/bin/startServer
    ```
2. Observe a saída da URL pelo comando e use essa URL para acessar o servidor Web. Ele é semelhante a:
     > https://(seu nome de VM ou endereço IP): 9443/ZDTMC/index.html
     >
     > Lembre-se de que o acesso à Web usa a porta 9443. Use isso para fazer logon no servidor Web. A ID de usuário para ZD&T é **zdtadmin** e a senha é **password**.

    ![Tela de boas-vindas do IBM zD&T Enterprise Edition](media/02-welcome.png)

3. Na página **início rápido** , em **Configurar**, selecione **armazenamento de imagem**.

     ![Tela do IBM zD&T Enterprise Edition Início Rápido](media/03-quickstart.png)

4. Na página **Configurar armazenamento de imagem** , selecione **SSH protocolo FTP**.

5. Para **nome do host**, digite **localhost** e insira o caminho do diretório para onde você carregou as imagens. Por exemplo,/home/MyUserID/ZDT/adcd/nov2017/volumes.

6. Insira a **ID de usuário** e a **senha** da VM. Não use o ZD&T ID de usuário e a senha.

7. Teste a conexão para certificar-se de que você tem acesso e, em seguida, selecione **salvar** para salvar a configuração.

## <a name="configure-the-target-environments"></a>Configurar os ambientes de destino

A próxima etapa é configurar o ambiente de destino zD&T. Esse ambiente hospedado emulado é onde as imagens são executadas.

1. Na página **início rápido** , em **Configurar**, selecione **ambientes de destino**.

2. Na página **configurar ambientes de destino** , selecione **Adicionar destino**.

3. Selecione **Linux**. A IBM dá suporte a dois tipos de ambientes, Linux e nuvem (OpenStack), mas essa demonstração é executada no Linux.

4. Na página **Adicionar ambiente de destino** , em **nome do host**, digite **localhost**. Mantenha a **porta SSH** definida como **22**.

5. Na caixa **rótulo do ambiente de destino** , insira um rótulo, como **MyCICS.**

     ![Tela Adicionar ambiente de destino](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>Configurar o ADCD e implantar

Depois de concluir as etapas de configuração anteriores, você deve configurar o zD&T para usar os pacotes e o ambiente de destino. Novamente, você usa o processo de armazenamento de imagem no zD&T, que permite montar e usar as imagens. Ele pode usar SSH ou FTP.

1. Na página **início rápido** , em **Configurar**, selecione **ADCD**. Um conjunto de instruções é exibido, informando as etapas que precisam ser concluídas para que um pacote ADCD possa ser montado. Isso explica por que nomeamos o diretório de destino da maneira que fizemos anteriormente.

2. Supondo que todas as imagens foram carregadas nos diretórios corretos, clique na **imagem do link ADCD** exibido no canto inferior direito (mostrado na etapa 7 na captura de tela a seguir).

     ![IBM zD&T Enterprise Edition – configurar a tela do ADCD](media/05-adcd.png)

## <a name="create-the-image"></a>Criar a imagem

Quando a etapa de configuração anterior for concluída, a página **criar uma imagem usando componentes do ADCD** será exibida.

1. Selecione o volume (novembro de 2017, nesse caso) para exibir os diferentes pacotes que estão nesse volume.

2. Para esta demonstração, selecione **sistema de controle de informações do cliente (CICS)-5,3**.

3. Na caixa **nome da imagem** , digite um nome para a imagem, como **imagem MyCICS**.

4. Selecione o botão **criar imagem** no canto inferior direito.

     ![IBM zD&T Enterprise Edition – criar uma imagem usando a tela de componentes do ADCD](media/06-adcd.png)

5. Na janela exibida, informando que a imagem foi implantada com êxito, escolha **implantar imagens**.

6. Na página **implantar uma imagem em um ambiente de destino** , selecione a imagem que você criou na página anterior (**imagem MyCICS**) e o ambiente de destino criado anteriormente (**MyCICS**).

7. Na próxima tela, forneça suas credenciais para a VM (ou seja, não a credencial ztadmin).

8. No painel Propriedades, insira o número de **processadores centrais (CPS)**, a quantidade de **memória do sistema (GB)** e o **diretório de implantação** para a imagem em execução. Como esta é uma demonstração, mantenha-a pequena.

9. Verifique se a caixa está selecionada para **emitir automaticamente o comando IPL para z/os após a implantação**.

     ![Tela de propriedades](media/07-properties.png)

10. Selecione **concluir**.

11. Selecione **implantar imagem** na página **implantar uma imagem em um ambiente de destino** .

Sua imagem agora pode implantar e está pronta para ser montada por um emulador de terminal 3270.

> [!NOTE]
> Se você receber um erro dizendo que não tem espaço em disco suficiente, observe que a região requer 151 GB.

Parabéns! Agora você está executando um ambiente de mainframe IBM no Azure.

## <a name="learn-more"></a>Saiba mais

- [Migração de mainframe: mitos e fatos](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM DB2 pureScale no Azure](ibm-db2-purescale-azure.md)
- [Solução de problemas](../../../troubleshooting/index.yml)
- [Desmistificando a migração do mainframe para o Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
