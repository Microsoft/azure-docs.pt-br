---
title: Instalar o ambiente de desenvolvimento/teste do IBM zD&T no Azure | Microsoft Docs
description: Implante o ambiente de teste e desenvolvimento IBM Z (zD&T) na infraestrutura como serviço (IaaS) da VM (máquina virtual) do Azure.
services: virtual-machines-linux
ms.service: virtual-machines-linux
ms.subservice: workloads
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: 9958fa07d6e814cdf886c3f559d57ae1e397264a
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625074"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Instalar o ambiente de desenvolvimento/teste do IBM zD&T no Azure

Para criar um ambiente de desenvolvimento/teste para cargas de trabalho de mainframe em sistemas IBM Z, você pode implantar o ambiente de desenvolvimento e teste IBM Z (zD&T) na infraestrutura como um serviço (IaaS) do Azure Virtual Machine (VM).

Com o zD&T, você pode aproveitar as economias de custo da plataforma x86 para seus ambientes de desenvolvimento e teste menos críticos e, em seguida, enviar as atualizações de volta para um ambiente de produção do sistema Z. Para obter mais informações, consulte as [instruções de instalação do IBM ZD&T](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL).

O Azure e o Azure Stack dão suporte às seguintes versões:

- zD&T Personal Edition
- zD&T Parallel Sysplex
- zD&T Enterprise Edition

Todas as edições do zD&T são executadas somente em sistemas Linux x86, não no Windows Server. O Enterprise Edition tem suporte em qualquer Red Hat Enterprise Linux (RHEL) ou Ubuntu/Debian. As imagens de VM RHEL e Debian estão disponíveis para o Azure.

Este artigo mostra como configurar o zD&T Enterprise Edition no Azure para que você possa usar o servidor Web zD&T Enterprise Edition para criação e gerenciamento de ambientes. A instalação do zD&T não instala nenhum ambiente. Você deve criá-los separadamente como pacotes de instalação. Por exemplo, as distribuições controladas por desenvolvedores de aplicativos (ADCD) são imagens de volume de ambientes de teste. Eles estão contidos em imagens zip na distribuição de mídia disponível da IBM. Consulte como [configurar um ambiente ADCD no Azure](demo.md).

Para obter mais informações, consulte a [visão geral do zD&T](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) no IBM Knowledge Center.

Este artigo mostra como configurar o ambiente de desenvolvimento e teste do Z (zD&T) Enterprise Edition no Azure. Em seguida, você pode usar o servidor Web zD&T Enterprise Edition para criar e gerenciar ambientes baseados em Z no Azure.

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> A IBM permite que o zD&T Enterprise Edition seja instalado somente em ambientes de desenvolvimento/teste —*não* ambientes de produção.

- Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Você precisa de acesso à mídia, que está disponível somente para clientes e parceiros da IBM. Para obter mais informações, entre em contato com seu representante IBM ou consulte as informações de contato no site do [zD&T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) .

- Um [servidor de licenciamento](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Isso é necessário para o acesso aos ambientes. A maneira como você cria isso depende de como você licencia o software da IBM:

     - O **servidor de licenciamento baseado em hardware** requer um dispositivo de hardware USB que contenha os tokens racional necessários para acessar todas as partes do software. Você deve obtê-lo da IBM.

     - O **servidor de licenciamento baseado em software** exige que você configure um servidor centralizado para o gerenciamento das chaves de licenciamento. Esse método é preferencial e exige que você configure as chaves que você recebe da IBM no servidor de gerenciamento.

## <a name="create-the-base-image-and-connect"></a>Criar a imagem base e conectar

1. Em portal do Azure, [crie uma VM](../../../linux/quick-create-portal.md) com a configuração do sistema operacional desejada. Este artigo pressupõe uma VM B4ms (com 4 vCPUs e 16 GB de memória) executando o Ubuntu 16, 4.

2. Depois que a VM for criada, abra as portas de entrada 22 para SSH, 21 para FTP e 9443 para o servidor Web.

3. Obtenha as credenciais SSH mostradas na folha **visão geral** da VM por meio do botão **conectar** . Selecione a guia **SSH** e copie o comando logon SSH para a área de transferência.

4. Faça logon em um [shell bash](../../../../cloud-shell/quickstart.md) do seu computador local e cole o comando. Ele estará no formato **SSH \<user id\> \@ \<IP Address\>**. Quando solicitado a fornecer suas credenciais, insira-as para estabelecer uma conexão com o diretório base.

## <a name="copy-the-installation-file-to-the-server"></a>Copiar o arquivo de instalação para o servidor

O arquivo de instalação do servidor Web é **ZDT \_ instalar o \_ EE \_ v 12.0.0.1. tgz**. Ele está incluído na mídia fornecida pela IBM. Você deve carregar esse arquivo para a VM do Ubuntu.

1. Na linha de comando, digite o seguinte comando para verificar se tudo está atualizado na imagem recém-criada:

    ```
    sudo apt-get update
    ```

2. Crie o diretório para instalação:

    ```
    mkdir ZDT
    ```

3. Copie o arquivo do computador local para a VM:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> Esse comando copia o arquivo de instalação para o diretório ZDT em seu diretório base, que varia dependendo se o cliente executa o Windows ou o Linux.

## <a name="install-the-enterprise-edition"></a>Instalar o Enterprise Edition

1. Vá para o diretório ZDT e descompacte o \_ arquivo ZDT de instalação do \_ EE \_ v 12.0.0.1. tgz usando os seguintes comandos:

    ```
    cd ZDT
    tar zxvf ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Execute o instalador:

    ```
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.x86_64
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Selecione **1** para instalar o servidor corporativo.

4. Pressione **Enter** e leia os contratos de licença com cuidado. No final da licença, digite **Sim** para continuar.

5. Quando solicitado a alterar a senha para o usuário recém-criado, **ibmsys1**, use o comando **sudo passwd ibmsys1** e insira a nova senha.

6. Para verificar se a instalação foi bem-sucedida, insira

    ```
    dpkg -l | grep zdtapp
    ```

7. Verifique se a saída contém a cadeia de caracteres **zdtapp 12.0.0.0**, indicando que a gasolina do pacote foi instalada com êxito

### <a name="starting-enterprise-edition"></a>Iniciando o Enterprise Edition

Tenha em mente que quando o servidor Web é iniciado, ele é executado sob o zD&T ID de usuário que foi criado durante o processo de instalação.

1. Para iniciar o servidor Web, use a ID de usuário raiz para executar o seguinte comando:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Copie a saída da URL pelo script, que tem a seguinte aparência:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Cole a URL em um navegador da Web para abrir o componente de gerenciamento para a instalação do zD&T.

## <a name="next-steps"></a>Próximas etapas

[Configurar uma ADCD (distribuição controlada por desenvolvedores de aplicativos) no IBM zD&T v1](./demo.md)
