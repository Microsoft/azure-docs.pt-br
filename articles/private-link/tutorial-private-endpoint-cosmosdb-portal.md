---
title: 'Tutorial: Conectar-se a uma conta do Azure Cosmos usando um ponto de extremidade privado do Azure'
titleSuffix: Azure Private Link
description: Comece com este tutorial usando o ponto de extremidade privado do Azure para se conectar a uma conta do Azure Cosmos no modo privado.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 9/25/2020
ms.openlocfilehash: 3a7e75641f6bb84b490231fcd06e04c3cbad06d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99063460"
---
# <a name="tutorial-connect-to-an-azure-cosmos-account-using-an-azure-private-endpoint"></a>Tutorial: Conectar-se a uma conta do Azure Cosmos usando um Ponto de Extremidade Privado do Azure

Um ponto de extremidade privado do Azure é o bloco de construção básico para o Link Privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem em modo privado com os recursos do Link Privado.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma rede virtual e um bastion host.
> * Crie uma máquina virtual.
> * Criar uma conta do Cosmos DB com um ponto de extremidade privado.
> * Testar a conectividade com o ponto de extremidade privado da conta do Cosmos DB.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-virtual-network-and-bastion-host"></a>Criar uma rede virtual e um bastion host

Nesta seção, você criará uma rede virtual, uma sub-rede e um bastion host. 

O bastion host será usado para se conectar com segurança à máquina virtual para testar o ponto de extremidade privado.

1. No canto superior esquerdo da tela, selecione **Criar um recurso > Rede > Rede virtual** ou pesquise por **Rede virtual** na caixa de pesquisa.

2. Em **Criar rede virtual**, insira ou selecione estas informações na guia **Básico**:

    | **Configuração**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscription     | Selecionar sua assinatura do Azure                                  |
    | Grupo de recursos   | Selecione **myResourceGroup** |
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
    | Grupo de recursos | Selecione **myResourceGroup** |
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

## <a name="create-a-cosmos-db-account-with-a-private-endpoint"></a>Criar uma conta do Cosmos DB com um ponto de extremidade privado

Nesta seção, você vai criar uma conta do Cosmos DB e configurar o ponto de extremidade privado.

1. No menu à esquerda, selecione **Criar um recurso** > **Bancos de Dados** > **Conta do Cosmos DB** ou pesquise **Conta do Cosmos DB** na caixa de pesquisa.

2. Na guia **Informações Básicas** de **Criar conta do Cosmos DB**, insira ou selecione as seguintes informações:

    | Configuração | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscription | Selecione sua assinatura do Azure. |
    | Grupo de recursos | Selecione **myResourceGroup**. |
    | **Detalhes da instância** |  |
    | Nome da conta | Insira **mycosmosdb**. Se o nome não estiver disponível, insira um nome exclusivo. |
    | API | Selecione **Core (SQL)**. |
    | Location | Selecione **Leste dos EUA**. |
    | Modo de capacidade | Mantenha o padrão **Taxa de transferência provisionada**. |
    | Aplicar desconto por nível gratuito | Mantenha o padrão **Não Aplicar**. |
    | Redundância geográfica | Mantenha o padrão **Desabilitar**. |
    | Gravações de várias regiões | Mantenha o padrão **Desabilitar**. |
   
3. Selecione a guia **Rede** ou escolha o botão **Avançar: Rede**.

4. Na guia **Rede**, insira ou selecione as seguintes informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **Conectividade de rede** | |
    | Método de conectividade | Selecione **Ponto de extremidade privado**. |
    | **Configurar o firewall** | |
    | Permitir acesso pelo portal do Azure | Mantenha o padrão **Permitir**. |
    | Permitir acesso do meu IP | Mantenha o padrão **Negar**. |

5. Em **Ponto de extremidade privado**, selecione **+ Adicionar**.

6. Em **Criar ponto de extremidade privado**, insira ou selecione as seguintes informações:

    | Configuração | Valor                                          |
    |-----------------------|----------------------------------|
    | Subscription | Selecionar sua assinatura do Azure |
    | Grupo de recursos | Selecione **myResourceGroup** |
    | Location | Selecione **Leste dos EUA** |
    | Nome | Insira **myPrivateEndpoint** |
    | Sub-recurso de destino | Mantenha o padrão **Núcleo (SQL)** |
    | **Rede** |  |
    | Rede virtual | Selecione **myVNet** |
    | Sub-rede | Selecione **mySubnet** |
    | **Integração de DNS privado** |
    | Integrar com a zona DNS privado | Mantenha o padrão **Sim** |
    | Zona DNS privado | Mantenha o padrão (Novo) privatelink.documents.azure.com |

7. Selecione **OK**.

8. Selecione **Examinar + criar**.

9. Selecione **Criar**.

### <a name="add-a-database-and-a-container"></a>Adicionar um banco de dados e um contêiner

1. Selecione **Ir para recurso** ou, no menu à esquerda do portal do Azure, selecione **Todos os Recursos** > **mycosmosdb**.

2. No menu à esquerda, selecione **Data Explorer**.

3. Na janela **Data Explorer**, selecione **Novo Contêiner**.

4. Em **Adicionar Contêiner**, insira ou selecione as seguintes informações:

    | Configuração | Valor |
    | ------- | ----- |
    | ID do banco de dados | Mantenha o padrão **Criar**. </br> Insira **mydatabaseid** na caixa de texto. |
    | Taxa de transferência (400 a 100.000 RU/s) | Mantenha o padrão **Manual**. </br> Insira **400** na caixa de texto. |
    | ID do contêiner | Insira **mycontainerid** |
    | Chave de partição | Insira **/mykey** |

5. Selecione **OK**.

6. Na seção **Configurações** da conta do CosmosDB, selecione **Chaves**.

7. Selecione Copiar na **CADEIA DE CONEXÃO PRIMÁRIA**.

## <a name="test-connectivity-to-private-endpoint"></a>Testar a conectividade com o ponto de extremidade privado

Nesta seção, você usará a máquina virtual criada na etapa anterior para se conectar à conta do Cosmos DB no ponto de extremidade privado.

1. Selecione **Grupos de recursos** no painel de navegação à esquerda.

1. Selecione **myResourceGroup**.

1. Selecione **myVM**.

1. Na página de visão geral de **myVM**, escolha **Conectar** e **Bastion**.

1. Selecione o botão azul **Usar Bastion**.

1. Insira o nome de usuário e a senha que você inseriu durante a criação da máquina virtual.

1. Abra o Windows PowerShell no servidor depois de se conectar.

1. Insira `nslookup <cosmosdb-account-name>.documents.azure.com` e valide a resolução de nomes. Substitua **\<cosmosdb-account-name>** pelo nome da conta do Cosmos DB criada nas etapas anteriores. 

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mycosmosdb8675.privatelink.documents.azure.com
    Address:  10.1.0.5
    Aliases:  mycosmosdb8675.documents.azure.com
    ```
    Um endereço IP privado igual a **10.1.0.5** é retornado para o nome da conta do Cosmos DB.  Esse endereço está na sub-rede da rede virtual criada anteriormente.
    
1. Obtenha sua cadeia de conexão primária do Azure Cosmos DB do portal. Uma cadeia de conexão válida está no formato:
   
   Para contas da API do SQL: `https://<accountName>.documents.azure.com:443/;AccountKey=<accountKey>;` Para a API do Azure Cosmos DB para MongoDB: `mongodb://<accountName>:<accountKey>@cdbmongo36.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false`

1. Instale o [Gerenciador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) na máquina virtual.

1. Selecione **Concluir** após a instalação do **Gerenciador de Armazenamento do Microsoft Azure**.  Mantenha a caixa marcada para abrir o aplicativo.

1. Na tela **Conectar-se ao Armazenamento do Azure**, selecione **Cancelar**.

1. No Gerenciador de Armazenamento, selecione o botão direito do mouse em **Contas do Cosmos DB** e escolha **Conectar-se ao Cosmos DB**.

1. Mantenha o padrão de **SQL** em **Selecionar API**.

1. Na caixa, em **Cadeia de Conexão**, cole a cadeia de conexão da conta do Cosmos DB que você copiou nas etapas anteriores.

1. Selecione **Avançar**.

1. Confirme se as configurações estão corretas em **Resumo da Conexão**.  

1. Selecione **Conectar**.

1. Feche a conexão com **myVM**.


## <a name="clean-up-resources"></a>Limpar os recursos

Se você não pretende continuar usando este aplicativo, exclua a rede virtual, a máquina virtual e a conta do Cosmos DB com as seguintes etapas:

1. No menu esquerdo, selecione **Grupos de recursos**.

2. Selecione **myResourceGroup**.

3. Selecione **Excluir grupo de recursos**.

4. Insira **myResourceGroup** em **DIGITE O NOME DO GRUPOS DE RECURSOS**.

5. Selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou:

* Uma rede virtual e um bastion host.
* Uma máquina virtual.
* Uma conta do Cosmos DB.

Saiba como criar um serviço de Link Privado:
> [!div class="nextstepaction"]
> [Criar um serviço de Link Privado](create-private-link-service-portal.md)
