---
title: 'Início Rápido: Criar uma VM Linux do SQL Server no Azure'
description: Este tutorial mostra como criar uma máquina virtual Linux com SQL Server 2017 no portal do Azure.
services: virtual-machines-sql
author: MashaMSFT
ms.date: 10/22/2019
tags: azure-service-management
ms.topic: quickstart
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 492d4a799f35d7fee4f500b48cbb31d2a9c5957f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449997"
---
# <a name="provision-a-linux-virtual-machine-running-sql-server-in-the-azure-portal"></a>Provisionar uma máquina virtual Linux executando o SQL Server no portal do Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Linux](sql-vm-create-portal-quickstart.md)
> * [Windows](../windows/sql-vm-create-portal-quickstart.md)

Neste tutorial de início rápido, você usará o portal do Azure para criar uma máquina virtual do Linux com o SQL Server 2017 instalado. Você aprenderá o seguinte: 


* [Criar uma VM Linux executando o SQL Server da galeria](#create)
* [Conectar-se à nova VM com SSH](#connect)
* [Alterar a senha SA](#password)
* [Configurar para conexões remotas](#remote)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-a-linux-vm-with-sql-server-installed"></a><a id="create"></a>Criar uma VM Linux com o SQL Server instalado

1. Entre no [portal do Azure](https://portal.azure.com/).

1. No painel esquerdo, selecione **Criar um recurso**.

1. No painel **Criar um recurso**, selecione **Computação**.

1. Selecione **Ver todos** ao lado do cabeçalho **Em destaque**.

   ![Ver todas as imagens de VM](./media/sql-vm-create-portal-quickstart/azure-compute-blade.png)

1. Na caixa de pesquisa, digite **SQL Server 2019** e pressione **Enter** para iniciar a pesquisa.

1. Limite os resultados da pesquisa selecionando **Sistema operacional** > **Red Hat**.

    ![Filtro de pesquisa para imagens de VM com SQL Server 2019](./media/sql-vm-create-portal-quickstart/searchfilter.png)

1. Selecione uma imagem Linux com SQL Server 2019 nos resultados da pesquisa. Este tutorial usa **SQL Server 2019 no RHEL74**.

   > [!TIP]
   > A edição Developer permite o teste ou o desenvolvimento com os recursos da edição Enterprise, mas sem os custos de licenciamento do SQL Server. Você só paga o custo da execução da VM Linux.

1. Selecione **Criar**. 


### <a name="set-up-your-linux-vm"></a>Configurar a VM do Linux

1. Na guia **Informações Básicas**, selecione a **Assinatura** e o **Grupo de Recursos**. 

    ![Janela Básico](./media/sql-vm-create-portal-quickstart/basics.png)

1. Em **Nome da máquina virtual**, insira um nome para a nova VM do Linux.
1. Em seguida, digite ou selecione os seguintes valores:
   * **Região**: Selecione a região do Azure ideal para você.
   * **Opções de disponibilidade**: Escolha a opção de disponibilidade e redundância que é melhor para seus aplicativos e seus dados.
   * **Alterar tamanho**: Selecione essa opção para escolher um tamanho de computador e, quando terminar, escolha **Selecionar**. Para saber mais sobre os tamanhos de VMs, confira [tamanhos de VMs](../../../virtual-machines/sizes.md).

     ![Escolher um tamanho de VM](./media/sql-vm-create-portal-quickstart/vmsizes.png)

   > [!TIP]
   > Para desenvolvimento e teste funcional, use um tamanho de VM **DS2** ou superior. Para testes de desempenho, use **DS13** ou superior.

   * **Tipo de autenticação**: Selecione **Chave pública SSH**.

     > [!Note]
     > Você tem a opção de usar uma chave pública SSH ou uma senha para autenticação. SSH é mais seguro. Para obter instruções sobre como gerar uma chave SSH, confira [Criar chaves SSH em Linux e Mac para VMs Linux no Azure](../../../virtual-machines/linux/mac-create-ssh-keys.md).

   * **Nome de usuário**: Insira o nome do Administrador da VM.
   * **Chave pública SSH**: Insira a chave pública RSA.
   * **Porta de entrada públicas**: Escolha **Permitir portas selecionadas** e selecione a porta **SSH (22)** na lista **Selecionar portas de entrada públicas**. Neste início rápido, esta etapa é necessária para se conectar e concluir a configuração do SQL Server. Se você quiser se conectar remotamente ao SQL Server, será necessário permitir manualmente o tráfego para a porta padrão (1433) usada pelo Microsoft SQL Server para conexões pela Internet, após a criação da máquina virtual.

     ![Portas de entrada](./media/sql-vm-create-portal-quickstart/port-settings.png)

1. Faça as alterações desejadas nas configurações nas guias adicionais a seguir ou mantenha as configurações padrão.
    * **Discos**
    * **Rede**
    * **Gerenciamento**
    * **Configuração de convidado**
    * **Marcas**

1. Selecione **Examinar + criar**.
1. No painel **Examinar + criar**, selecione **Criar**.

## <a name="connect-to-the-linux-vm"></a><a id="connect"></a>Conectar-se à VM Linux

Se você já usa um shell BASH, conecte-se à VM do Azure usando o comando **ssh**. No comando a seguir, substitua o nome de usuário da VM e o endereço IP para se conectar à VM Linux.

```bash
ssh azureadmin@40.55.55.555
```

Você pode encontrar o endereço IP da VM no portal do Azure.

![Endereço IP no portal do Azure](./media/sql-vm-create-portal-quickstart/vmproperties.png)

Se estiver executando no Windows e não tiver um shell Bash, você poderá instalar um cliente SSH, como o PuTTY.

1. [Baixe e instale o PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Execute o PuTTY.

1. Na tela de configuração do PuTTY, insira o endereço IP público da VM.

1. Selecione **Abrir** e insira seu nome de usuário e a senha nos prompts.

Para saber mais sobre como se conectar às VMs Linux, confira [Criar uma VM Linux no Azure usando o Portal](../../../virtual-machines/linux/quick-create-portal.md).

> [!NOTE]
> Se for exibido um alerta de segurança do PuTTY indicando que a chave do host do servidor não está sendo armazenada em cache no Registro, escolha uma das opções a seguir. Se você confia nesse host, selecione **Sim** para adicionar a chave ao cache do PuTTY e continuar a conexão. Caso deseje continuar a conexão apenas uma vez, sem adicionar a chave ao cache, selecione **Não**. Se você não confia nesse host, selecione **Cancelar** para abandonar a conexão.

## <a name="change-the-sa-password"></a><a id="password"></a> Alterar a senha SA

A nova máquina virtual instala o SQL Server com uma senha SA aleatória. Redefina essa senha antes de se conectar ao SQL Server com o logon SA.

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

Vários [pacotes](sql-server-on-linux-vm-what-is-iaas-overview.md#packages) do SQL Server são instalados por padrão, incluindo o pacote de ferramentas de linha de comando do SQL Server. O pacote de ferramentas contém as ferramentas **sqlcmd** e **bcp**. Para sua conveniência, você pode opcionalmente adicionar o caminho de ferramentas, `/opt/mssql-tools/bin/`, à variável de ambiente **PATH**.

1. Execute os seguintes comandos para modificar o **PATH** para sessões de logon e sessões interativas/que não são de logon:

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a name="configure-for-remote-connections"></a><a id="remote"></a>Configurar para conexões remotas

Se você precisa se conectar remotamente ao SQL Server na VM do Azure, configure uma regra de entrada no grupo de segurança de rede. A regra permite o tráfego na porta na qual o SQL Server escuta (padrão 1433). As etapas a seguir mostram como usar o portal do Azure nesta etapa.

> [!TIP]
> Se você tiver selecionado a porta de entrada **MS SQL (1433)** nas configurações durante o provisionamento, essas alterações foram feitas para você. Você pode ir para a próxima seção sobre como configurar o firewall.

1. No portal, selecione **Máquinas Virtuais** e selecione sua VM do SQL Server.
1. No painel de navegação à esquerda, em **Configurações**, selecione **Rede**.
1. Na janela Rede, selecione **Adicionar porta de entrada** em **Regras de Porta de Entrada**.

   ![Regras de porta de entrada](./media/sql-vm-create-portal-quickstart/networking.png)

1. Na lista **Serviço**, selecione **MS SQL**.

    ![Regra de grupo de segurança MS SQL](./media/sql-vm-create-portal-quickstart/sqlnsgrule.png)

1. Clique em **OK** para salvar a regra para a sua VM.

### <a name="open-the-firewall-on-rhel"></a>Abrir o firewall no RHEL

Este tutorial o instruiu a criar uma VM RHEL (Red Hat Enterprise Linux). Se você quiser se conectar remotamente a VMs RHEL, precisará abrir a porta 1433 no firewall do Linux.

1. [Conecte-se](#connect) à VM RHEL.

1. No shell BASH, execute os seguintes comandos:

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma máquina virtual com SQL Server 2017 no Azure, pode se conectar localmente com **sqlcmd** para executar consultas Transact-SQL.

Se você configurou a VM do Azure para conexões remotas do SQL Server, você deve conseguir se conectar remotamente. Para obter um exemplo de como se conectar remotamente ao SQL Server no Linux pelo Windows, confira [Usar SSMS no Windows para se conectar ao SQL Server no Linux](/sql/linux/sql-server-linux-develop-use-ssms). Para se conectar com o Visual Studio Code, confira [Usar o Visual Studio Code para criar e executar scripts Transact-SQL para SQL Server](/sql/linux/sql-server-linux-develop-use-vscode)

Para obter mais informações gerais sobre o SQL Server em Linux, confira [Visão geral do SQL Server 2017 em Linux](/sql/linux/sql-server-linux-overview). Para saber mais sobre como usar o SQL Server 2017 em máquinas virtuais Linux, confira [Visão geral de máquinas virtuais com SQL Server 2017 no Azure](sql-server-on-linux-vm-what-is-iaas-overview.md).