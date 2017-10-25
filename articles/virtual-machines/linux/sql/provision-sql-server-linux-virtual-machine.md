---
title: Criar uma VM Linux com SQL Server 2017 no Azure | Microsoft Docs
description: "Este tutorial mostra como criar uma máquina virtual Linux com SQL Server 2017 no portal do Azure."
services: virtual-machines-linux
author: rothja
ms.author: jroth
manager: jhubbard
ms.date: 10/04/2017
ms.topic: hero-article
tags: azure-service-management
ms.devlang: na
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.technology: database-engine
ms.openlocfilehash: 5a00bd2f40024aa6fd352a839f9136f90bfd46d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="provision-a-linux-sql-server-virtual-machine-in-the-azure-portal"></a>Provisionar uma máquina virtual Linux com SQL Server no portal do Azure

> [!div class="op_single_selector"]
> * [Linux](provision-sql-server-linux-virtual-machine.md)
> * [Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)

Neste tutorial de início rápido, use o Portal do Azure para criar uma máquina virtual Linux com o SQL Server 2017 instalado.

Neste tutorial, você irá:

* [Criar uma VM Linux SQL da galeria](#create)
* [Conectar-se à nova VM com SSH](#connect)
* [Alterar a senha SA](#password)
* [Configurar para conexões remotas](#remote)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a id="create"></a>Criar uma VM Linux com o SQL Server instalado

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. No painel esquerdo, clique em **Novo**.

1. No painel **Novo**, clique em **Computador**.

1. Clique em **Ver todos os** ao lado do cabeçalho **Em destaque**.

   ![Ver todas as imagens de VM](./media/provision-sql-server-linux-virtual-machine/azure-compute-blade.png)

1. Na caixa de pesquisa, digite **SQL Server 2017**e pressione **Enter** para iniciar a pesquisa.

1. Clique no ícone **Filtrar**, limite a pesquisa a imagens **Microsoft** **baseadas em Linux** e depois clique em **Concluído**.

    ![Filtro de pesquisa para imagens de VM com SQL Server 2017](./media/provision-sql-server-linux-virtual-machine/searchfilter.png)

1. Selecione uma imagem Linux com SQL Server 2017 nos resultados da pesquisa. Este tutorial usa **Licença gratuita do SQL Server: SQL Server 2017 Developer no Red Hat Enterprise Linux 7.4**.

   > [!TIP]
   > A edição Developer permite que você teste ou desenvolva com os recursos da edição Enterprise, mas sem custos de licenciamento do SQL Server. Você só paga o custo da execução da VM Linux.

1. Clique em **Criar**.

1. Na janela **Básico**, preencha os detalhes da VM Linux. 

    ![Janela Básico](./media/provision-sql-server-linux-virtual-machine/basics.png)

    > [!Note]
    > Você tem a opção de usar uma chave pública SSH ou uma senha para autenticação. SSH é mais seguro. Para obter instruções sobre como gerar uma chave SSH, confira [Criar chaves SSH em Linux e Mac para VMs Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys).

1. Clique em **OK**.

1. Na janela **Tamanho**, escolha um tamanho de máquina. Para ver outros tamanhos, selecione **Exibir tudo**. Para saber mais sobre tamanhos de VM, confira [Tamanhos de VM Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes).

    ![Escolher um tamanho de VM](./media/provision-sql-server-linux-virtual-machine/vmsizes.png)

   > [!TIP]
   > Para desenvolvimento e testes funcionais, recomendamos um tamanho de VM **DS2** ou superior. Para testes de desempenho, use **DS13** ou superior.

1. Clique em **Selecionar**.

1. Na janela **Configurações**, você pode alterar ou manter as configurações padrão.

1. Clique em **OK**.

1. Na página **Resumo**, clique em **Comprar** para criar a VM.

## <a id="connect"></a>Conectar-se à VM Linux

Se você já usa um shell BASH, conecte-se à VM do Azure usando o comando **ssh**. No comando a seguir, substitua o nome de usuário da VM e o endereço IP para se conectar à VM Linux.

```bash
ssh azureadmin@40.55.55.555
```

Você pode encontrar o endereço IP da VM no portal do Azure.

![Endereço IP no portal do Azure](./media/provision-sql-server-linux-virtual-machine/vmproperties.png)

Se você estiver executando no Windows e não tem um shell BASH, poderá instalar um cliente SSH, como o PuTTY.

1. [Baixe e instale o PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Execute o PuTTY.

1. Na tela de configuração do PuTTY, insira o endereço IP público da VM.

1. Clique em Abrir e insira seu nome de usuário e senha nos prompts.

Para saber mais sobre como se conectar às VMs Linux, confira [Criar uma VM Linux no Azure usando o Portal](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-portal#ssh-to-the-vm).

## <a id="password"></a>Alterar a senha SA

A nova máquina virtual instala o SQL Server com uma senha SA aleatória. Você deve redefinir a senha antes de se conectar ao SQL Server com o logon de SA.

1. Depois de se conectar à VM Linux, abra um novo terminal de comando.

1. Altere a senha SA com os seguintes comandos:

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   Insira uma nova senha e confirme-a quando solicitado.

1. Reinicie o serviço SQL Server.

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>Adicionar as ferramentas ao caminho (opcional)

Vários [pacotes](sql-server-linux-virtual-machines-overview.md#packages) do SQL Server são instalados por padrão, incluindo o pacote de ferramentas de linha de comando do SQL Server. O pacote de ferramentas contém as ferramentas **sqlcmd** e **bcp**. Para sua conveniência, você pode opcionalmente adicionar o caminho de ferramentas, `/opt/mssql-tools/bin/`, à variável de ambiente **PATH**.

1. Execute os comandos a seguir para modificar o **PATH** tanto para sessões de logon quanto para sessões interativas/não logon:

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a id="remote"></a>Configurar para conexões remotas

Se você precisa se conectar remotamente ao SQL Server na VM do Azure, configure uma regra de entrada no grupo de segurança de rede. A regra permite o tráfego na porta na qual o SQL Server escuta (padrão 1433). As etapas a seguir mostram como usar o portal do Azure nesta etapa. 

1. No portal, selecione **Máquinas Virtuais** e selecione sua VM do SQL Server.

1. Na lista de propriedades, selecione **Rede**.

1. Na janela **Rede**, clique no botão **Adicionar** em **Regras de Porta de Entrada**.

   ![Regras de porta de entrada](./media/provision-sql-server-linux-virtual-machine/networking.png)

1. Na lista **Serviço**, selecione **MS SQL**.

    ![Regra de grupo de segurança MS SQL](./media/provision-sql-server-linux-virtual-machine/sqlnsgrule.png)

1. Clique em **OK** para salvar a regra para a sua VM.

### <a name="open-the-firewall-on-rhel"></a>Abrir o firewall no RHEL

Este tutorial o instruiu a criar uma VM RHEL (Red Hat Enterprise Linux). Se você quiser se conectar remotamente a VMs RHEL, precisará abrir a porta 1433 no firewall do Linux.

1. [Conecte-se](#connect) à VM RHEL.

1. No shell BASH, execute os seguintes comandos:

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd –reload
   ```

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma máquina virtual com SQL Server 2017 no Azure, pode se conectar localmente com **sqlcmd** para executar consultas Transact-SQL.

Se você configurou a VM do Azure para conexões remotas do SQL Server, também poderá se conectar remotamente. Para obter um exemplo de como se conectar remotamente ao SQL Server no Linux pelo Windows, confira [Usar SSMS no Windows para se conectar ao SQL Server no Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms). Para se conectar com o Visual Studio Code, confira [Usar o Visual Studio Code para criar e executar scripts Transact-SQL para SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode)

Para saber mais sobre o SQL Server no Linux, confira a [Visão geral do SQL Server 2017 no Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview). Para saber mais sobre como usar o SQL Server 2017 em máquinas virtuais Linux, confira [Visão geral de máquinas virtuais com SQL Server 2017 no Azure](sql-server-linux-virtual-machines-overview.md).
