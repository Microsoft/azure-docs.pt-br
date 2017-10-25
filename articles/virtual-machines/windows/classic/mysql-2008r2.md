---
title: "Criar uma VM clássica do Azure executando o MySQL | Microsoft Docs"
description: "Crie uma máquina virtual do Azure que executa o Windows Server 2012 R2 e o banco de dados MySQL usando o modelo de implantação clássica."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 98fa06d2-9b92-4d05-ac16-3f8e9fd4feaa
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: cynthn
ms.openlocfilehash: 11850e5ce20efae88a7af9c1d2e4761ed2b70cd7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="install-mysql-on-a-virtual-machine-created-with-the-classic-deployment-model-running-windows-server-2016"></a>Instalar o MySQL em uma máquina virtual criada com o modelo de implantação clássico que executa o Windows Server 2016
[MySQL](https://www.mysql.com) é um banco de dados SQL fonte aberto popular. Este tutorial mostra como instalar e executar a **versão de comunidade do MySQL 5.7.18** como um Servidor MySQL em uma máquina virtual que executa o **Windows Server 2016**. Sua experiência pode ser um pouco diferente em outras versões do MySQL ou do Windows Server.

Para obter instruções sobre como instalar o MySQL no Linux, consulte: [Como instalar o MySQL no Azure](../../linux/mysql-install.md).

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos.

## <a name="create-a-virtual-machine-running-windows-server-2016"></a>Criar uma máquina virtual com o Windows Server 2016 em execução
Se você ainda não tem uma VM que executa o Windows Server 2016, você pode usar este [tutorial](./tutorial.md) para criar a máquina virtual.

## <a name="attach-a-data-disk"></a>Anexar um disco de dados
Depois que a máquina virtual é criada, você pode, opcionalmente, anexar um disco de dados. Adicionar um disco de dados é recomendado para cargas de trabalho de produção e para evitar a falta de espaço na unidade do sistema operacional (C:), que inclui o sistema operacional.

Veja [Como anexar um disco de dados a uma máquina virtual do Windows](../attach-managed-disk-portal.md) e siga as instruções para anexar um disco vazio. Defina a configuração de cache de host como **Nenhum** ou **Somente leitura**.

## <a name="log-on-to-the-virtual-machine"></a>Faça logon na máquina virtual
A seguir, você [fará logon na máquina virtual](./connect-logon.md) para poder instalar o MySQL.

## <a name="install-and-run-mysql-community-server-on-the-virtual-machine"></a>Instalar e executar MySQL Community Server na máquina virtual
Siga estas etapas para instalar, configurar e executar a versão de comunidade do MySQL Server:

> [!NOTE]
> Ao fazer o download de itens usando o Internet Explorer, você pode definir a **Configuração de Segurança Reforçada** do IE como desabilitada e simplificar o processo de download. No menu Iniciar, clique em Ferramentas Administrativas/Gerenciador do Servidor/Servidor Local e, em seguida, clique na **Configuração de Segurança Reforçada** do IE e defina a configuração como Desabilitar).
>
>

1. Depois de se conectar à máquina virtual usando a Área de Trabalho Remota, clique em **Internet Explorer** no menu Iniciar.
2. Selecione o botão **Ferramentas** no canto superior direito (o ícone de engrenagem) e clique em **Opções da Internet**. Clique na guia **Segurança**, clique no ícone **Sites Confiáveis** e, em seguida, no botão **Sites**. Adicione http://*.mysql.com à lista de sites confiáveis. Clique em **Fechar** e, em seguida, em **OK**.
3. Na barra de endereços do Internet Explorer, digite https://dev.mysql.com/downloads/mysql/.
4. Use o site do MySQL para localizar e baixar a versão mais recente do instalador do MySQL para Windows. Ao escolher o Instalador do MySQL, baixe a versão que tem o conjunto completo de arquivos (por exemplo, o mysql-installer-community-5.7.18.0.msi, com um tamanho do arquivo de 352,8 MB) e salve o instalador.
5. Quando tiver terminado de baixar o instalador, clique em **Executar** para iniciar a instalação.
6. Na página **Contrato de Licença**, aceite o contrato de licença e clique em **Avançar**.
7. Na página **Escolhendo um Tipo de Instalação**, clique no tipo de instalação desejada e clique em **Avançar**. As etapas a seguir pressupõem a seleção do tipo de instalação **Somente servidor** .
8. Se a página **Verificar Requisitos** for exibida, clique em **Executar** para permitir que o instalador instale quaisquer componentes ausentes. Siga as instruções que são exibidas, como o tempo de execução do C++ Redistribuível.
9. Na página **Instalação**, clique em **Executar**. Quando a instalação for concluída, clique em **Avançar**.

10. Na página **Configuração do Produto**, clique em **Avançar**.

11. Na página **Tipo e Rede** , especifique o tipo de configuração desejada e as opções de conectividade, inclusive a porta TCP, se necessário. Selecione **Mostrar Opções Avançadas** e clique em **Próximo**.
    ![](./media/mysql-2008r2/MySQL_TypeNetworking.png)

12. Na página **Contas e Funções** , especifique uma senha forte de raiz de MySQL. Adicione mais contas de usuário do MySQL, conforme a necessidade, e clique em **Avançar**.

    ![](./media/mysql-2008r2/MySQL_AccountsRoles_Filled.png)
13. Na página **Serviço Windows**, especifique as alterações a serem aplicadas às configurações padrão para executar o servidor MySQL como um serviço Windows, conforme a necessidade e clique em **Avançar**.

    ![](./media/mysql-2008r2/MySQL_WindowsService.png)
14. As escolhas na página **Plug-ins e Extensões** são opcionais. Clique em **Avançar** para continuar.
15. Na página **Opções Avançadas**, especifique as alterações a serem aplicadas às opções de logon, conforme a necessidade e clique em **Avançar**.

    ![](./media/mysql-2008r2/MySQL_AdvOptions.png)
16. Na página **Aplicar a Configuração de Servidor**, clique em **Executar**. Quando as etapas de configuração forem concluídas, clique em **Concluir**.
17. Na página **Configuração do Produto**, clique em **Avançar**.
18. Na página **Instalação Concluída**, clique em **Copiar Log para a Área de Transferência**, se quiser examiná-lo mais tarde e clique em **Concluir**.
19. Na tela inicial, digite **mysql** e, em seguida, clique em **Cliente de Linha de Comando do MySQL 5.7**.
20. Insira a senha raiz que você especificou na etapa 12 e você verá um prompt em que poderá emitir comandos para configurar o MySQL. Para saber mais sobre os comandos e a sintaxe, confira [Manuais de referência do MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-configuration.html).

    ![](./media/mysql-2008r2/MySQL_CommandPrompt.png)
21. Você também pode configurar as definições padrão de configuração do servidor, como os diretórios e unidades de base e de dados. Para obter mais informações, confira [6.1.2 Padrões de configuração do servidor](https://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html).

## <a name="configure-endpoints"></a>Configurar pontos de extremidade

Para que o serviço MySQL esteja disponível para computadores cliente na Internet, você deve configurar um ponto de extremidade para a porta TCP e criar uma regra de Firewall do Windows. O valor padrão da porta na qual o serviço do Servidor MySQL escuta clientes do MySQL é 3306. Você pode especificar outra porta, desde que ela seja consistente com o valor fornecido na página **Tipo e Rede** (etapa 11 do procedimento anterior).

> [!NOTE]
> Para uso em produção, considere as implicações de segurança de disponibilizar o serviço do Servidor MySQL para todos os computadores na Internet. Você pode definir o conjunto de endereços IP de origem que têm permissão para usar o ponto de extremidade com uma lista de controle de acesso (ACL). Para saber mais, consulte [Como configurar pontos de extremidade para uma máquina virtual](setup-endpoints.md).
>
>

Para configurar um ponto de extremidade para o serviço do servidor MySQL:

1. No Portal do Azure, clique em **Máquinas Virtuais (clássicas)**, clique no nome de sua nova máquina virtual MySQL e, em seguida, clique em **Pontos de Extremidade**.
2. Na barra de comandos, clique em **Adicionar**.
3. Na página **Adicionar ponto de extremidade**, digite um nome exclusivo para o **Nome**.
4. Selecione **TCP** como o protocolo.
5. Digite o número da porta, como **3306**, em **Porta Pública** e **Porta Privada** e, em seguida, clique em **OK**.

## <a name="add-a-windows-firewall-rule-to-allow-mysql-traffic"></a>Adicionar uma regra do Firewall do Windows para permitir o tráfego do MySQL
Para adicionar uma regra do Firewall do Windows que permita o tráfego MySQL da Internet, execute o comando a seguir em um _prompt de comando elevado do Windows PowerShell_ na máquina virtual do servidor MySQL.

    New-NetFirewallRule -DisplayName "MySQL57" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public

## <a name="test-your-remote-connection"></a>Testar sua conexão remota
Para testar sua conexão remota com a VM do Azure que está executando o serviço do Servidor MySQL, você deve fornecer o nome DNS do serviço de nuvem que contenha o VN.

1. No Portal do Azure, clique em **Máquinas Virtuais (clássicas)**, clique no nome de sua máquina virtual do servidor MySQL e clique em **Visão Geral**.
2. No painel da máquina virtual, observe o valor **Nome DNS**. Aqui está um exemplo:

   ![](media/mysql-2008r2/MySQL_DNSName.png)
3. De um computador local executando o MySQL ou o cliente MySQL, execute o comando a seguir para fazer logon como um usuário do MySQL:

     mysql -u <yourMysqlUsername> -p -h <yourDNSname>

   Por exemplo, usando o nome de usuário do MySQL _dbadmin3_ e nome DNS _testmysql.cloudapp.net_ para a máquina virtual, você poderia iniciar o MySQL usando o comando a seguir:

     mysql -u dbadmin3 -p -h testmysql.cloudapp.net

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como executar o MySQL, consulte a [Documentação do MySQL](http://dev.mysql.com/doc/).
