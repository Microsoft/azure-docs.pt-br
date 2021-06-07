---
title: Link privado-portal do Azure-banco de dados do Azure para PostgreSQL-servidor único
description: Saiba como configurar o link privado para o banco de dados do Azure para PostgreSQL-servidor único do portal do Azure
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 01/09/2020
ms.openlocfilehash: 75defcf88128e1b100e403d74f76577525f0bdeb
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642125"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-using-portal"></a>Criar e gerenciar um link privado para o banco de dados do Azure para PostgreSQL-servidor único usando o portal

Um ponto de extremidade privado é o bloco de construção fundamental para o link privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem de forma privada com recursos de link privado.  Neste artigo, você aprenderá a usar o portal do Azure para criar uma VM em uma rede virtual do Azure e um servidor único do banco de dados do Azure para PostgreSQL com um ponto de extremidade privado do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> O recurso de link privado só está disponível para servidores do banco de dados do Azure para PostgreSQL nos tipos de preço Uso Geral ou com otimização de memória. Verifique se o servidor de banco de dados está em um desses tipos de preço.

## <a name="sign-in-to-azure"></a>Entrar no Azure
Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Criar uma VM do Azure

Nesta seção, você criará uma rede virtual e a sub-rede para hospedar a VM que é usada para acessar seu recurso de link privado (um servidor PostgreSQL no Azure).

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

> [!NOTE]
> Em alguns casos, o Banco de Dados do Azure para PostgreSQL e a sub-rede da VNet estão em assinaturas diferentes. Nesses casos, você deve garantir as seguintes configurações:
> - Certifique-se de que a assinatura tenha o provedor de recursos **Microsoft. DBforPostgreSQL** registrado. Para obter mais informações, confira [resource-manager-registration][resource-manager-portal]

## <a name="create-an-azure-database-for-postgresql-single-server"></a>Criar um servidor único do banco de dados do Azure para PostgreSQL

Nesta seção, você criará um servidor de banco de dados do Azure para PostgreSQL no Azure. 

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso**  >  **bancos** de  >  **dados do Azure para PostgreSQL**.

1. Na **opção de implantação banco de dados do Azure para PostgreSQL**, selecione **servidor único** e forneça estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. Você o criou na seção anterior.|
    | **Detalhes do servidor** |  |
    |Nome do servidor  | Insira *myserver*. Se esse nome já estiver sendo usado, crie um nome exclusivo.|
    | Nome de usuário do administrador| Insira um nome de administrador de sua escolha. |
    | Senha | Insira uma senha de sua escolha. A senha deve ter no mínimo 8 caracteres e atender a requisitos complexidade definidos. |
    | Location | Selecione uma região do Azure em que você deseja que o servidor PostgreSQL resida. |
    |Versão  | Selecione a versão do banco de dados do servidor PostgreSQL necessária.|
    | Computação + armazenamento| Selecione o tipo de preço necessário para o servidor com base na carga de trabalho. |
    |||
 
7. Selecione **OK**. 
8. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração. 
9. Quando você vir a mensagem validação aprovada, selecione **criar**. 
10. Quando vir a mensagem Validação aprovada, selecione Criar. 

## <a name="create-a-private-endpoint"></a>Criar um ponto de extremidade privado

Nesta seção, você criará um servidor PostgreSQL e adicionará um ponto de extremidade privado a ele. 

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso**  >  **rede**  >  **link privado**.
2. Em **Central de Link Privado – Visão Geral**, na opção **Criar uma conexão privada com um serviço**, selecione **Iniciar**.

    :::image type="content" source="media/concepts-data-access-and-security-private-link/privatelink-overview.png" alt-text="Visão geral do link privado":::

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
    | Tipo de recurso | Selecione **Microsoft. DBforPostgreSQL/servidores**. |
    | Recurso |Selecione *myServer*|
    |Sub-recurso de destino |Selecionar *postgresqlServer*|
    |||
7. Selecione **Avançar: configuração**.
8. Em **Criar um ponto de extremidade privado – configuração**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    |**REDE**| |
    | Rede virtual| Selecione *MyVirtualNetwork*. |
    | Sub-rede | Selecione *mySubnet*. |
    |**INTEGRAÇÃO DE DNS PRIVADO**||
    |Integrar com a zona DNS privado |Selecione **Sim** na barra superior. |
    |Zona DNS privado |Selecione *(novo) privatelink. Postgres. Database. Azure. com* |
    |||

    > [!Note] 
    > Use a zona DNS privada predefinida para seu serviço ou forneça seu nome de zona DNS preferencial. Consulte a [configuração da zona DNS dos serviços do Azure](../private-link/private-endpoint-dns.md) para obter detalhes.

1. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração. 
2. Quando vir a mensagem **Validação aprovada**, selecione **Criar**. 

    :::image type="content" source="media/concepts-data-access-and-security-private-link/show-postgres-private-link.png" alt-text="Link privado criado":::

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

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>Acessar o servidor PostgreSQL de forma privada da VM

1. Na Área de Trabalho Remota do *myVM*, abra o PowerShell.

2. Insira  `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com`. 

    Você receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
    Address:  10.1.3.4
    ```

3. Teste a conexão de link particular para o servidor PostgreSQL usando qualquer cliente disponível. No exemplo abaixo, usei o [Azure Data Studio](/sql/azure-data-studio/download) para realizar a operação.

4. Em **nova conexão**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Tipo de servidor| Selecione **PostgreSQL**.|
    | Nome do servidor| Selecionar *mydemopostgresserver.privatelink.Postgres.Database.Azure.com* |
    | Nome de usuário | Insira o nome de usuário como username@servername fornecido durante a criação do servidor PostgreSQL. |
    |Senha |Insira uma senha fornecida durante a criação do servidor PostgreSQL. |
    |SSL|Selecione **obrigatório**.|
    ||

5. Selecione Conectar.

6. Procurar bancos de dados no menu à esquerda.

7. Opcionalmente Crie ou consulte informações do servidor postgreSQL.

8. Feche a conexão de área de trabalho remota para myVm.

## <a name="clean-up-resources"></a>Limpar os recursos
Quando você terminar de usar o ponto de extremidade privado, o servidor PostgreSQL e a VM, exclua o grupo de recursos e todos os recursos que ele contém:

1. Insira *MyResource* The na caixa de **pesquisa** na parte superior do portal e selecione  *MyResource*   Bedos resultados da pesquisa.
2. Selecione **Excluir grupo de recursos**.
3. Insira MyResource GROUP para **digite o nome do grupo de recursos** e selecione **excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste "como", você criou uma VM em uma rede virtual, um banco de dados do Azure para PostgreSQL-servidor único e um ponto de extremidade privado para acesso privado. Você se conectou a uma VM da Internet e se comunica com segurança ao servidor PostgreSQL usando o link privado. Para saber mais sobre pontos de extremidade privados, confira [o que é o ponto de extremidades privado do Azure](../private-link/private-endpoint-overview.md).

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md