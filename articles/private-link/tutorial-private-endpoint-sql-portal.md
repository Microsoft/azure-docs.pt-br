---
title: 'Tutorial: como se conectar a um SQL Server do Azure usando um Ponto de Extremidade Privado do Azure – Portal'
description: Use este tutorial para aprender como criar um SQL Server do Azure com um ponto de extremidade privado usando o portal do Azure.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 8bb227bdc4bbcdf986c2ed64e1f89e1dfa289b83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554965"
---
# <a name="tutorial-connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-portal"></a>Tutorial: como se conectar a um SQL Server do Azure usando um Ponto de Extremidade Privado do Azure – portal do Azure

Um ponto de extremidade privado do Azure é o bloco de construção básico para o Link Privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem em modo privado com os recursos do Link Privado.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma rede virtual e um bastion host.
> * Crie uma máquina virtual.
> * Criar um SQL Server do Azure e um ponto de extremidade privado.
> * Testar a conectividade com o ponto de extremidade privado do SQL Server.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.


## <a name="create-a-virtual-network-and-bastion-host"></a>Criar uma rede virtual e um bastion host

Nesta seção, você criará uma rede virtual, uma sub-rede e um bastion host. 

O bastion host será usado para se conectar com segurança à máquina virtual para testar o ponto de extremidade privado.

1. No canto superior esquerdo da tela, selecione **Criar um recurso > Rede > Rede virtual** ou pesquise por **Rede virtual** na caixa de pesquisa.

2. Em **Criar rede virtual**, insira ou selecione estas informações na guia **Básico**:

    | **Configuração**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscription     | Selecionar sua assinatura do Azure                                  |
    | Grupo de recursos   | Selecione **CreateSQLEndpointTutorial-rg** |
    | **Detalhes da instância** |                                                                 |
    | Nome             | Insira **myVNet**                                    |
    | Região           | Selecione **Leste dos EUA** |

3. Selecione a guia **Endereços IP** ou selecione o botão **Avançar: Endereços IP** na parte inferior da página.

4. Na guia **Endereços IP**, insira estas informações:

    | Configuração            | Valor                      |
    |--------------------|----------------------------|
    | Espaço de endereço IPv4 | Insira **10.1.0.0/16** |

5. Em **Nome da sub-rede**, selecione a palavra **padrão**.

6. Em **Editar sub-rede**, insira estas informações:

    | Configuração            | Valor                      |
    |--------------------|----------------------------|
    | Nome da sub-rede | Insira **mySubnet** |
    | Intervalo de endereços da sub-rede | Insira **10.1.0.0/24** |

7. Clique em **Salvar**.

8. Selecione a guia **Segurança**.

9. Em **BastionHost**, selecione **Habilitar**. Insira estas informações:

    | Configuração            | Valor                      |
    |--------------------|----------------------------|
    | Nome do bastion | Insira **myBastionHost** |
    | Espaço de endereço da AzureBastionSubnet | Insira **10.1.1.0/24** |
    | Endereço IP público | Selecione **Criar novo**. </br> Em **Nome**, insira **myBastionIP**. </br> Selecione **OK**. |


8. Selecione a guia **Revisar + criar** ou o botão **Revisar + criar**.

9. Selecione **Criar**.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Nesta seção, você criará uma máquina virtual que será usada para testar o ponto de extremidade privado.

1. No lado superior esquerdo do portal, selecione **Criar um recurso** > **Computação** > **Máquina virtual** ou pesquise **Máquina virtual** na caixa de pesquisa.
   
2. Em **Criar uma máquina virtual**, digite ou selecione os valores na guia **Informações Básicas**:

    | Configuração | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscription | Selecionar sua assinatura do Azure |
    | Grupo de recursos | Selecione **CreateSQLEndpointTutorial** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **myVM** |
    | Região | Selecione **Leste dos EUA** |
    | Opções de disponibilidade | Selecione **Nenhuma redundância infraestrutura necessária** |
    | Imagem | Selecione **Windows Server 2019 Datacenter – Gen1** |
    | Instância do Azure Spot | Selecione **Não** |
    | Tamanho | Escolha o tamanho da VM ou use a configuração padrão |
    | **Conta de administrador** |  |
    | Nome de Usuário | Insira um nome de usuário |
    | Senha | Insira uma senha |
    | Confirmar senha | Insira novamente a senha |

3. Selecione a guia **Rede** ou selecione **Avançar: Discos**, em seguida, **Avançar: Rede**.
  
4. Na guia Rede, selecione ou insira:

    | Configuração | Valor |
    |-|-|
    | **Interface de rede** |  |
    | Rede virtual | **myVNet** |
    | Sub-rede | **mySubnet** |
    | IP público | Selecione **Nenhum**. |
    | Grupo de segurança de rede da NIC | **Basic**|
    | Porta de entrada públicas | Selecione **Nenhum**. |
   
5. Selecione **Examinar + criar**. 
  
6. Examine as configurações e selecione **Criar**.

## <a name="create-an-azure-sql-server-and-private-endpoint"></a><a name ="create-a-private-endpoint"></a>Criar um SQL Server do Azure e um ponto de extremidade privado

Nesta seção, você criará um SQL Server no Azure. 

1. No lado superior esquerdo da tela no portal do Azure, selecione **Criar um recurso** > **Bancos de Dados** > **Banco de Dados SQL**.

1. Na guia **Básico** de **Criar Banco de Dados SQL**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **CreateSQLEndpointTutorial**. Você criou esse grupo de recursos na seção anterior.|
    | **Detalhes do banco de dados** |  |
    | Nome do banco de dados  | Insira **mysqldatabase**. Se esse nome já estiver sendo usado, crie um nome exclusivo. |
    | Servidor | Selecione **Criar novo**. |

6. Em **Novo servidor**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome do servidor  | Insira **mysqlserver**. Se esse nome já estiver sendo usado, crie um nome exclusivo.|
    | Logon de administrador do servidor | Insira um nome de administrador de sua escolha. |
    | Senha | Insira uma senha de sua escolha. A senha deve ter no mínimo 8 caracteres e atender a requisitos complexidade definidos. |
    | Location | Selecione **Leste dos EUA** |
    
7. Selecione **OK**.

8. Selecione a guia **Rede** ou escolha o botão **Avançar: Rede**.

9. Na guia **Rede**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **Conectividade de rede** | |
    | Método de conectividade | Selecione **Ponto de extremidade privado**. |
   
10. Selecione **+ Adicionar ponto de extremidade privado** em **Pontos de extremidade privados**.

11. Em **Criar ponto de extremidade privado**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **CreateSQLEndpointTutorial**. |
    | Location | Selecione **Leste dos EUA**. |
    | Nome | Insira **myPrivateSQLendpoint**. |
    | Sub-recurso de destino | Selecione **SQLServer**. |
    | **Rede** |  |
    | Rede virtual | Selecione **myVNet**. |
    | Sub-rede | Selecione **mySubnet**. |
    | **Integração de DNS privado** | |
    | Integrar com a zona DNS privado | Mantenha o padrão **Sim**. |
    | Zona DNS privado | Mantenha o padrão **(Novo) privatelink.database.windows.net**. |

12. Selecione **OK**. 

13. Selecione **Examinar + criar**.

14. Selecione **Criar**.

## <a name="test-connectivity-to-private-endpoint"></a>Testar a conectividade com o ponto de extremidade privado

Nesta seção, você usará a máquina virtual criada na etapa anterior para se conectar ao SQL Server pelo ponto de extremidade privado.

1. Selecione **Grupos de recursos** no painel de navegação à esquerda.

2. Selecione **CreateSQLEndpointTutorial**.

3. Selecione **myVM**.

4. Na página de visão geral de **myVM**, escolha **Conectar** e **Bastion**.

5. Selecione o botão azul **Usar Bastion**.

6. Insira o nome de usuário e a senha que você inseriu durante a criação da máquina virtual.

7. Abra o Windows PowerShell no servidor depois de se conectar.

8. Digite `nslookup <sqlserver-name>.database.windows.net`. Substitua **\<sqlserver-name>** pelo nome do SQL Server criado nas etapas anteriores.  Você receberá uma mensagem semelhante à mostrada abaixo:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.1.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    O endereço IP privado **10.1.0.5** é retornado para o nome do SQL Server.  Esse endereço está na sub-rede da rede virtual criada anteriormente.


9. Instale o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) em **myVM**.

10. Abra o **SQL Server Management Studio**.

4. Em **Conectar-se ao servidor**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Tipo de servidor | Selecione **Mecanismo de Banco de Dados**.|
    | Nome do servidor | Insira **\<sqlserver-name>.database.windows.net** |
    | Autenticação | Selecione **Autenticação do SQL Server**. |
    | Nome de usuário | Insira o nome de usuário que você inseriu durante a criação do servidor |
    | Senha | Insira a senha que você inseriu durante a criação do servidor |
    | Lembrar senha | Selecione **Sim** na barra superior. |

1. Selecione **Conectar**.
2. Procurar bancos de dados no menu à esquerda.
3. (Opcional) Crie ou consulte informações em **mysqldatabase**.
4. Feche a Conexão da Área de Trabalho Remota com **myVM**. 

## <a name="clean-up-resources"></a>Limpar os recursos 
Quando terminar de usar o ponto de extremidade privado, o SQL Server e a VM, exclua o grupo de recursos e todos os recursos que ele contém: 
1. Insira **CreateSQLEndpointTutorial** na caixa de **Pesquisa** na parte superior do portal e selecione **CreateSQLEndpointTutorial** nos resultados da pesquisa. 
2. Selecione **Excluir grupo de recursos**. 
3. Insira CreateSQLEndpointTutorial em **DIGITAR O NOME DO GRUPO DE RECURSOS** e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou:

* Uma rede virtual e um bastion host.
* Uma máquina virtual.
* SQL Server do Azure com um ponto de extremidade privado.

Você usou a máquina virtual para testar a conectividade seguramente com o SQL Server no ponto de extremidade privado.

Como próxima etapa, talvez você também esteja interessado no cenário de arquitetura de um **aplicativo Web com conectividade privada com o banco de dados SQL do Azure**, que conecta um aplicativo Web fora da rede virtual ao ponto de extremidade privado de um banco de dados.
> [!div class="nextstepaction"]
> [Aplicativo Web com conectividade privada com o banco de dados SQL do Azure](/azure/architecture/example-scenario/private-web-app/private-web-app)