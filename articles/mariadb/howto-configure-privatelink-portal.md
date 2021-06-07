---
title: Link privado-portal do Azure-banco de dados do Azure para MariaDB
description: Saiba como configurar o link privado para o banco de dados do Azure para MariaDB do portal do Azure
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: how-to
ms.date: 01/09/2020
ms.openlocfilehash: 79b3c3f8eca2fa4442a7845ca4aa3921d0302453
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98659617"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-using-portal"></a>Criar e gerenciar o link privado para o banco de dados do Azure para MariaDB usando o portal

Um ponto de extremidade privado é o bloco de construção fundamental para o link privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem de forma privada com recursos de link privado.  Neste artigo, você aprenderá a usar o portal do Azure para criar uma VM em uma rede virtual do Azure e um banco de dados do Azure para MariaDB Server com um ponto de extremidade privado do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> O recurso de link privado só está disponível para o banco de dados do Azure para servidores MariaDB nos tipos de preço Uso Geral ou com otimização de memória. Verifique se o servidor de banco de dados está em um desses tipos de preço.

## <a name="sign-in-to-azure"></a>Entrar no Azure
Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Criar uma VM do Azure

Nesta seção, você criará uma rede virtual e a sub-rede para hospedar a VM que é usada para acessar seu recurso de link privado (um servidor MariaDB no Azure).

### <a name="create-the-virtual-network"></a>Criar a rede virtual
Nesta seção, você criará uma rede virtual e a sub-rede para hospedar a VM usada para acessar o recurso de Link Privado.

1. No lado superior esquerdo da tela, selecione **criar um recurso** rede redes  >    >  **virtuais**.
2. Em **Criar rede virtual**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira *MyVirtualNetwork*. |
    | Espaço de endereço | Insira *10.1.0.0/16*. |
    | Subscription | Selecione sua assinatura.|
    | Resource group | Selecione **Criar novo** e insira *myResourceGroup*, depois selecione **OK**. |
    | Location | Selecione **Europa Ocidental**.|
    | Sub-rede – Nome | Insira *mysubnet*. |
    | Sub-rede – Intervalo de endereços | Insira *10.1.0.0/24*. |
    |||
3. Deixe o restante com os valores padrão e selecione **Criar**.

### <a name="create-virtual-machine"></a>Criar máquina virtual

1. No lado superior esquerdo da tela no portal do Azure, selecione **Criar um recurso** > **Computação** > **Máquina Virtual**.

2. Em **Criar uma máquina virtual – Noções básicas**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. Você o criou na seção anterior.  |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da máquina virtual | Insira *myVm*. |
    | Região | Selecione **Europa Ocidental**. |
    | Opções de disponibilidade | Deixe o padrão **Nenhuma redundância de infraestrutura necessária**. |
    | Imagem | Selecione **Windows Server 2019 Datacenter**. |
    | Tamanho | Deixe o padrão **Standard DS1 v2**. |
    | **CONTA DE ADMINISTRADOR** |  |
    | Nome de Usuário | Insira um nome de usuário de sua escolha. |
    | Senha | Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar Senha | Reinsira a senha. |
    | **REGRAS DE PORTA DE ENTRADA** |  |
    | Porta de entrada públicas | Deixar o padrão **Nenhum**. |
    | **ECONOMIZE DINHEIRO** |  |
    | Já tem uma licença do Windows? | Deixe o padrão **Não**. |
    |||

1. Selecione **Avançar: Discos**.

1. Em **Criar uma máquina virtual – Discos**, mantenha os padrões e selecione **Avançar: Rede**.

1. Em **Criar uma máquina virtual – Rede**, selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Rede virtual | Deixe o padrão **MyVirtualNetwork**.  |
    | Espaço de endereço | Deixar o padrão **10.1.0.0/24**.|
    | Sub-rede | Deixar o padrão **mySubnet (10.1.0.0/24)** .|
    | IP público | Deixe o padrão **(novo) myVm-ip**. |
    | Porta de entrada públicas | Selecione **Permitir portas selecionadas**. |
    | Selecione as portas de entrada | Selecione **HTTP** e **RDP**.|
    |||


1. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração.

1. Quando vir a mensagem **Validação aprovada**, selecione **Criar**.

## <a name="create-an-azure-database-for-mariadb"></a>Criar um Banco de Dados do Azure para MariaDB

Nesta seção, você criará um banco de dados do Azure para o servidor MariaDB no Azure. 

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso**  >  **bancos** de  >  **dados do Azure para MariaDB**.

1. No **banco de dados do Azure para MariaDB** , forneça estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. Você o criou na seção anterior.|
    | **Detalhes do servidor** |  |
    |Nome do servidor  | Insira *myserver*. Se esse nome já estiver sendo usado, crie um nome exclusivo.|
    | Nome de usuário do administrador| Insira um nome de administrador de sua escolha. |
    | Senha | Insira uma senha de sua escolha. A senha deve ter no mínimo 8 caracteres e atender a requisitos complexidade definidos. |
    | Location | Selecione uma região do Azure onde você deseja que seu servidor MariaDB resida. |
    |Versão  | Selecione a versão do banco de dados do servidor MariaDB que é necessária.|
    | Computação + armazenamento| Selecione o tipo de preço necessário para o servidor com base na carga de trabalho. |
    |||

7. Selecione **OK**. 
8. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração. 
9. Quando você vir a mensagem validação aprovada, selecione **criar**. 
10. Quando vir a mensagem Validação aprovada, selecione Criar. 

> [!NOTE]
> Em alguns casos, o Banco de Dados do Azure para MariaDB e a sub-rede da VNet estão em assinaturas diferentes. Nesses casos, você deve garantir as seguintes configurações:
> - Certifique-se de que a assinatura tenha o provedor de recursos **Microsoft. DBforMariaDB** registrado. Para obter mais informações, confira [resource-manager-registration][resource-manager-portal]

## <a name="create-a-private-endpoint"></a>Criar um ponto de extremidade privado

Nesta seção, você criará um ponto de extremidade privado para o servidor MariaDB. 

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso**  >  **rede**  >  **link privado**.
2. Em **Central de Link Privado – Visão Geral**, na opção **Criar uma conexão privada com um serviço**, selecione **Iniciar**.

    ![Visão geral do link privado](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. Em **criar um ponto de extremidade privado-noções básicas**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. Você o criou na seção anterior.|
    | **Detalhes da Instância** |  |
    | Nome | Insira *myPrivateEndpoint*. Se esse nome já estiver sendo usado, crie um nome exclusivo. |
    |Região|Selecione **Europa Ocidental**.|
    |||
5. Selecione **Avançar: Recurso**.
6. Em **Criar um ponto de extremidade privado – Recurso**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    |Método de conexão  | Selecione conectar-se a um recurso do Azure em meu diretório.|
    | Subscription| Selecione sua assinatura. |
    | Tipo de recurso | Selecione **Microsoft. DBforMariaDB/servidores**. |
    | Recurso |Selecione *myServer*|
    |Sub-recurso de destino |Selecionar *mariadbServer*|
    |||
7. Selecione **Avançar: configuração**.
8. Em **Criar um ponto de extremidade privado – configuração**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    |**REDE**| |
    | Rede virtual| Selecione *MyVirtualNetwork*. |
    | Sub-rede | Selecione  *mySubnet*. |
    |**INTEGRAÇÃO DE DNS PRIVADO**||
    |Integrar com a zona DNS privado |Selecione **Sim** na barra superior. |
    |Zona DNS privado |Selecione *(novo) privatelink. MariaDB. Database. Azure. com* |
    |||

    > [!Note] 
    > Use a zona DNS privada predefinida para seu serviço ou forneça seu nome de zona DNS preferencial. Consulte a [configuração da zona DNS dos serviços do Azure](../private-link/private-endpoint-dns.md) para obter detalhes.

1. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração. 
2. Quando vir a mensagem **Validação aprovada**, selecione **Criar**. 

    ![Link privado criado](media/concepts-data-access-and-security-private-link/show-mariadb-private-link.png)

    > [!NOTE] 
    > O FQDN na configuração de DNS do cliente não é resolvido para o IP privado configurado. Você precisará configurar uma zona DNS para o FQDN configurado, conforme mostrado [aqui](../dns/dns-operations-recordsets-portal.md).

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Conectar-se a uma VM usando a RDP (Área de Trabalho Remota)


Depois de criar **myVm**, conecte-se a ela pela Internet da seguinte maneira: 

1. Na barra de pesquisa do portal, insira *myVm*.

1. Selecione o botão **Conectar**. Depois de selecionar o botão **Conectar**, **Conectar-se à máquina virtual** abre.

1. Selecione **Baixar Arquivo RDP**. O Azure cria um arquivo *.rdp* (protocolo RDP) e ele é baixado no computador.

1. Abra o arquivo *downloaded.rdp*.

    1. Se solicitado, selecione **Conectar**.

    1. Insira o nome de usuário e a senha que você especificou ao criar a VM.

        > [!NOTE]
        > Talvez seja necessário selecionar **Mais escolhas** > **Usar uma conta diferente** para especificar as credenciais inseridas durante a criação da VM.

1. Selecione **OK**.

1. Você pode receber um aviso do certificado durante o processo de logon. Se você receber um aviso de certificado, selecione **Sim** ou **Continuar**.

1. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para sua área de trabalho local.

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>Acessar o servidor MariaDB de forma privada da VM

1. Na Área de Trabalho Remota do *myVM*, abra o PowerShell.

2. Insira  `nslookup mydemomserver.privatelink.mariadb.database.azure.com`. 

    Você receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoMariaDBserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4
    ```

3. Teste a conexão de link privado para o servidor MariaDB usando qualquer cliente disponível. No exemplo abaixo, usei o [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) para realizar a operação.


4. Em **nova conexão**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Tipo de servidor| Selecione **MariaDB**.|
    | Nome do servidor| Selecionar *mydemoserver.privatelink.MariaDB.Database.Azure.com* |
    | Nome de usuário | Insira o nome de usuário como username@servername fornecido durante a criação do servidor MariaDB. |
    |Senha |Insira uma senha fornecida durante a criação do servidor MariaDB. |
    |SSL|Selecione **obrigatório**.|
    ||

5. Selecione **testar conexão** ou **OK**.

6. Opcionalmente Procurar bancos de dados no menu à esquerda e criar ou consultar informações do banco de MariaDB

7. Feche a conexão de área de trabalho remota para myVm.

## <a name="clean-up-resources"></a>Limpar os recursos
Quando você terminar de usar o ponto de extremidade privado, o servidor MariaDB e a VM, exclua o grupo de recursos e todos os recursos que ele contém:

1. Insira  *MyResource*   The na caixa de **pesquisa** na parte superior do portal e selecione  *MyResource*   Bedos resultados da pesquisa.
2. Selecione **Excluir grupo de recursos**.
3. Insira MyResource GROUP para **digite o nome do grupo de recursos** e selecione **excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste "como", você criou uma VM em uma rede virtual, um banco de dados do Azure para MariaDB e um ponto de extremidade privado para acesso privado. Você se conectou a uma VM da Internet e se comunica com segurança ao servidor MariaDB usando o link privado. Para saber mais sobre pontos de extremidade privados, confira [o que é o ponto de extremidades privado do Azure](../private-link/private-endpoint-overview.md).

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md