---
title: Instalar o IBM zD & o ambiente de desenvolvimento/teste T no Azure | Microsoft Docs
description: Implante o IBM Z Development e ambiente de teste (zD & T) na infraestrutura de máquina Virtual (VM) do Azure como um serviço (IaaS).
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 2f5520213e7d8792c89f5445d470987323173dc3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859334"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Instalar o IBM zD & o ambiente de desenvolvimento/teste T no Azure

Para criar um ambiente de desenvolvimento/teste para cargas de trabalho de mainframe no IBM Z Systems, você pode implantar IBM Z Development e ambiente de teste (zD & T) na infraestrutura de máquina Virtual (VM) do Azure como um serviço (IaaS).

Com zD & T, você pode aproveitar a economia de custo do x86 de plataforma para seus ambientes de desenvolvimento e teste menos críticos e por push as atualizações de volta para um ambiente de produção do sistema Z. Para obter mais informações, consulte o [instruções de instalação do IBM ZD & T](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL).

Azure e o Azure Stack suportam as seguintes versões:

- zD & T Personal Edition
- zD & T Parallel Sysplex
- zD & T Enterprise Edition

Todas as edições do zD & T executam apenas em x86 sistemas Linux, não o Windows Server. Há suporte para o Enterprise Edition no Red Hat Enterprise Linux (RHEL) ou Ubuntu/Debian. Imagens RHEL e VM Debian estão disponíveis para o Azure.

Este artigo mostra como configurar zD & T Enterprise Edition no Azure, você pode usar o zD & servidor de web T Enterprise Edition para a criação e gerenciamento de ambientes. ZD & T a instalação não instala nenhum ambiente. Você deve criá-los separadamente como pacotes de instalação. Por exemplo, o aplicativo desenvolvedores controlado distribuições (ADCD) são imagens de volume dos ambientes de teste. Eles estão contidos em imagens de zip na distribuição de mídia disponível da IBM. Veja como [configurar um ambiente ADCD no Azure](demo.md).

Para obter mais informações, consulte o [zD & visão geral de T](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) no Centro de dados de Conhecimento da IBM.

Este artigo mostra como configurar o ambiente de teste (zD & T) Enterprise Edition no Azure e o desenvolvimento de Z. Em seguida, você pode usar o zD & servidor de web T Enterprise Edition para criar e gerenciar ambientes baseados no Z no Azure.

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> IBM permite zD & T Enterprise Edition para ser instalado em ambientes de desenvolvimento/teste apenas —*não* ambientes de produção.

- Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Você precisa ter acesso à mídia, que está disponível apenas para parceiros e clientes da IBM. Para obter mais informações, entre em contato com seu representante da IBM ou consulte as informações de contato sobre o [zD & T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) site.

- Um [servidor de licenciamento](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Isso é necessário para acesso aos ambientes. A maneira de criá-lo depende de como você licencia o software da IBM:

     - **Servidor de licenciamento com base em hardware** requer um dispositivo de hardware USB que contém os Tokens racional necessárias para acessar todas as partes do software. Esse deve ser obtido da IBM.

     - **Servidor de licenciamento baseado em software** exige que você configurar um servidor centralizado para o gerenciamento das chaves de licenciamento. Esse método é preferido e exige que você configure as chaves que você receber da IBM no servidor de gerenciamento.

## <a name="create-the-base-image-and-connect"></a>Criar a imagem base e conecte-se

1. No portal do Azure, [criar uma VM](/azure/virtual-machines/linux/quick-create-portal) com a configuração de sistema operacional que você deseja. Este artigo pressupõe que uma VM B4ms (com 4 vCPUs e 16 GB de memória) executando o Ubuntu 16.04.

2. Depois que a VM é criada, abra as portas de entrada 22 para SSH, 21 para FTP e 9443 para o servidor web.

3. Obter as credenciais SSH mostradas na **visão geral** folha da VM por meio de **Connect** botão. Selecione o **SSH** guia e copie o comando de logon SSH para a área de transferência.

4. Faça logon em uma [shell Bash](/azure/cloud-shell/quickstart) do seu computador local e cole o comando. Ele terá o formato **ssh\<id de usuário\>\@\<endereço IP\>**. Quando solicitado a fornecer suas credenciais, digite-os para estabelecer uma conexão ao seu diretório inicial.

## <a name="copy-the-installation-file-to-the-server"></a>Copie o arquivo de instalação para o servidor

O arquivo de instalação para o servidor web está **ZDT\_instale\_EE\_V12.0.0.1.tgz**. Ele está incluído na mídia fornecida pela IBM. Você deve carregar esse arquivo para sua VM do Ubuntu.

1. Na linha de comando, digite o seguinte comando para verificar se que tudo está atualizado da imagem recém-criada:

    ```
    sudo apt-get update
    ```

2. Crie o diretório para instalar em:

    ```
    mkdir ZDT
    ```

3. Copie o arquivo em seu computador local para a VM:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> Este comando copia o arquivo de instalação para o diretório ZDT em seu diretório inicial, que varia de acordo com seu cliente executa o Windows ou Linux.

## <a name="install-the-enterprise-edition"></a>Instale o Enterprise Edition

1. Vá para o diretório ZDT e descompactar o ZDT\_instale\_EE\_arquivo V12.0.0.1.tgz usando os seguintes comandos:

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Execute o instalador:

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Selecione **1** para instalar o servidor corporativo.

4. Pressione **Enter** e ler os contratos de licença com cuidado. No final da licença, insira **Sim** para continuar.

5. Quando for solicitado a alterar a senha para o usuário recém-criado **ibmsys1**, use o comando **sudo passwd ibmsys1** e insira a nova senha.

6. Para verificar se a instalação foi bem-sucedida insira

    ```
    dpkg -l | grep zdtapp
    ```

7. Verifique se a saída contém a cadeia de caracteres **zdtapp 12.0.0.0**, indicando que o gás do pacote foi instalado com êxito

### <a name="starting-enterprise-edition"></a>Starting Enterprise Edition

Tenha em mente que quando o servidor web é iniciado, ele é executado sob a ID de usuário zD & T que foi criada durante o processo de instalação.

1. Para iniciar o servidor web, use a ID de usuário raiz para executar o comando a seguir:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Copie a URL de saída pelo script, que se parece com:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Cole a URL em um navegador da web para abrir o componente de gerenciamento para seus zD e instalação de T.

## <a name="next-steps"></a>Próximas etapas

[Defina o backup de um aplicativo desenvolvedores controlado por distribuição (ADCD) no IBM zD & T v1](./demo.md)
