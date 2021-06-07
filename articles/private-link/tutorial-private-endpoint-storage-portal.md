---
title: 'Tutorial: Conectar-se a uma conta de armazenamento usando um ponto de extremidade privado do Azure'
titleSuffix: Azure Private Link
description: Comece com este tutorial usando o ponto de extremidade privado do Azure para se conectar a uma conta de armazenamento no modo privado.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 9/25/2020
ms.openlocfilehash: eefef0eb61df9aa0597206b537f3d58e8b8b3213
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96484814"
---
# <a name="tutorial-connect-to-a-storage-account-using-an-azure-private-endpoint"></a>Tutorial: Conectar-se a uma conta de armazenamento usando um ponto de extremidade privado do Azure

Um ponto de extremidade privado do Azure é o bloco de construção básico para o Link Privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem em modo privado com os recursos do Link Privado.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma rede virtual e um bastion host.
> * Crie uma máquina virtual.
> * Criar uma conta de armazenamento com um ponto de extremidade privado.
> * Testar a conectividade com o ponto de extremidade privado da conta de armazenamento.

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

## <a name="create-storage-account-with-a-private-endpoint"></a>Criar uma conta de armazenamento com um ponto de extremidade privado

Nesta seção, você vai criar uma conta de armazenamento e configurar o ponto de extremidade privado.

1. No menu à esquerda, selecione **Criar um recurso** > **Armazenamento** > **Conta de armazenamento** ou pesquise **Conta de armazenamento** na caixa de pesquisa.

2. Na guia **Informações Básicas** de **Criar conta de armazenamento**, insira ou selecione as seguintes informações:

    | Configuração | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscription | Selecionar sua assinatura do Azure |
    | Grupo de recursos | Selecione **myResourceGroup** |
    | **Detalhes da instância** |  |
    | Nome da conta de armazenamento | Insira **mystorageaccount**. Se o nome não estiver disponível, insira um nome exclusivo. |
    | Location | Selecione **Leste dos EUA** |
    | Desempenho | Mantenha o padrão **Standard** |
    | Tipo de conta | Mantenha o padrão **Armazenamento (Uso Geral v2)** |
    | Replicação| Mantenha o padrão **RA-GRS (armazenamento com redundância geográfica com acesso de leitura)** |
   
3. Selecione a guia **Rede** ou escolha o botão **Avançar: Rede**.

4. Na guia **Rede**, em **Método de conectividade**, selecione **Ponto de extremidade privado**.

5. Em **Ponto de extremidade privado**, selecione **+ Adicionar**.

6. Em **Criar ponto de extremidade privado**, insira ou selecione as seguintes informações:

    | Configuração | Valor                                          |
    |-----------------------|----------------------------------|
    | Subscription | Selecionar sua assinatura do Azure |
    | Grupo de recursos | Selecione **myResourceGroup** |
    | Location | Selecione **Leste dos EUA** |
    | Nome | Insira **myPrivateEndpoint** |
    | Sub-recurso de armazenamento | Mantenha o padrão **blob** |
    | **Rede** |  |
    | Rede virtual | Selecione **myVNet** |
    | Sub-rede | Selecione **mySubnet** |
    | **Integração de DNS privado** |
    | Integrar com a zona DNS privado | Mantenha o padrão **Sim** |
    | Zona DNS privado | Mantenha o padrão (Novo) privatelink.blob.core.windows.net |

7. Selecione **OK**.

8. Selecione **Examinar + criar**.

9. Selecione **Criar**.

10. Selecione **Grupos de recursos** no painel de navegação à esquerda.

11. Selecione **myResourceGroup**.

12. Escolha a conta de armazenamento criada nas etapas anteriores.

13. Na seção **Configurações** da conta de armazenamento, selecione **Chaves de acesso**.

14. Selecione Copiar na **Cadeia de conexão** para **key1**.

## <a name="test-connectivity-to-private-endpoint"></a>Testar a conectividade com o ponto de extremidade privado

Nesta seção, você usará a máquina virtual criada na etapa anterior para se conectar à conta de armazenamento no ponto de extremidade privado.

1. Selecione **Grupos de recursos** no painel de navegação à esquerda.

2. Selecione **myResourceGroup**.

3. Selecione **myVM**.

4. Na página de visão geral de **myVM**, escolha **Conectar** e **Bastion**.

5. Selecione o botão azul **Usar Bastion**.

6. Insira o nome de usuário e a senha que você inseriu durante a criação da máquina virtual.

7. Abra o Windows PowerShell no servidor depois de se conectar.

8. Digite `nslookup <storage-account-name>.blob.core.windows.net`. Substitua **\<storage-account-name>** pelo nome da conta de armazenamento criada nas etapas anteriores.  Você receberá uma mensagem semelhante à mostrada abaixo:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mystorageaccount8675.privatelink.blob.core.windows.net
    Address:  10.1.0.5
    Aliases:  mystorageaccount8675.blob.core.windows.net
    ```

    Um endereço IP privado igual a **10.1.0.5** é retornado para o nome da conta de armazenamento.  Esse endereço está na sub-rede da rede virtual criada anteriormente.

9. Instale o [Gerenciador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) na máquina virtual.

10. Selecione **Concluir** após a instalação do **Gerenciador de Armazenamento do Microsoft Azure**.  Mantenha a caixa marcada para abrir o aplicativo.

11. Na tela **Conectar-se ao Armazenamento do Azure**, selecione **Usar uma cadeia de conexão**.

12. Selecione **Avançar**.

13. Insira o nome da conta de armazenamento das etapas anteriores em **Nome de exibição**.

14. Na caixa, em **Cadeia de conexão**, cole a cadeia de conexão da conta de armazenamento que você copiou nas etapas anteriores.

15. Selecione **Avançar**.

16. Confirme se as configurações estão corretas em **Resumo da Conexão**.  

17. Selecione **Conectar**.

18. Feche a conexão com **myVM**.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não pretende continuar usando este aplicativo, exclua a rede virtual, a máquina virtual e a conta de armazenamento com as seguintes etapas:

1. No menu esquerdo, selecione **Grupos de recursos**.

2. Selecione **myResourceGroup**.

3. Selecione **Excluir grupo de recursos**.

4. Insira **myResourceGroup** em **DIGITE O NOME DO GRUPOS DE RECURSOS**.

5. Selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Saiba como criar um serviço de Link Privado:
> [!div class="nextstepaction"]
> [Criar um serviço de Link Privado](create-private-link-service-portal.md)