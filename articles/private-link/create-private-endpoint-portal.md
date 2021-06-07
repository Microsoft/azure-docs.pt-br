---
title: Início Rápido – Criar um ponto de extremidade privado usando o portal do Azure
description: Use este guia de início rápido para aprender a criar um Ponto de Extremidade Privado usando o portal do Azure.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: allensu
ms.openlocfilehash: 395912ae70c5a01bd7de9a80cf8a507dd516028e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018062"
---
# <a name="quickstart-create-a-private-endpoint-using-the-azure-portal"></a>Início Rápido: Criar um ponto de extremidade privado usando o portal do Azure

Introdução ao Link Privado do Azure usando um Ponto de Extremidade Privado para se conectar com segurança a um aplicativo Web do Azure.

Neste guia de início rápido, você criará um ponto de extremidade privado para um aplicativo Web do Azure e implantará uma máquina virtual para testar a conexão privada.  

Pontos de extremidade privados podem ser criados para diferentes tipos de serviços do Azure, como o Azure SQL e o Armazenamento do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um Aplicativo Web do Azure com um **PremiumV2-tier** ou um plano de serviço de aplicativo mais alto implantado em sua assinatura do Azure.  
    * Para obter mais informações e um exemplo, confira [Início Rápido: Criar um aplicativo Web ASP.NET Core no Azure](../app-service/quickstart-dotnetcore.md). 
    * Para obter um tutorial detalhado sobre como criar um aplicativo Web e um ponto de extremidade, confira [Tutorial: Conectar-se a um aplicativo Web usando um Ponto de Extremidade Privado do Azure](tutorial-private-endpoint-webapp-portal.md).

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
    | Grupo de recursos   | Selecione **CreatePrivateEndpointQS-rg** |
    | **Detalhes da instância** |                                                                 |
    | Nome             | Insira **myVNet**                                    |
    | Região           | Selecione **Europa Ocidental**.|

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
    | Grupo de recursos | Selecione **CreatePrivateEndpointQS-rg** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **myVM** |
    | Região | Selecione **Europa Ocidental**. |
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

## <a name="create-a-private-endpoint"></a>Criar um ponto de extremidade privado

Nesta seção, você criará um Ponto de Extremidade Privado para o aplicativo Web criado na seção de pré-requisitos.

1. No lado superior esquerdo da tela no portal, selecione **Criar um recurso** > **Rede** > **Link Privado** ou, na caixa de pesquisa, digite **Link Privado**.

2. Selecione **Criar**.

3. Em **Centro de Link Privado**, selecione **Pontos de extremidade privados** no menu à esquerda.

4. Em **Pontos de extremidade privados**, selecione **+ Adicionar**.

5. Na guia **Noções básicas** de **Criar um ponto de extremidade privado**, digite ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **CreatePrivateEndpointQS-rg**. Você criou esse grupo de recursos na seção anterior.|
    | **Detalhes da instância** |  |
    | Nome  | Insira **myPrivateEndpoint**. |
    | Região | Selecione **Europa Ocidental**. |

6. Selecione a guia **Recurso** ou o botão **Avançar: Recurso** na parte inferior da página.
    
7. Em **Recurso**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Método de conexão | Selecione **Conectar-se a um recurso do Azure em meu diretório**. |
    | Subscription | Selecione sua assinatura. |
    | Tipo de recurso | Selecione **Microsoft.Web/sites**. |
    | Recurso | Selecione **\<your-web-app-name>**. </br> Substitua o nome do aplicativo Web criado nos pré-requisitos. |
    | Sub-recurso de destino | Selecione **sites**. |

8. Selecione a guia **Configuração** ou o botão **Avançar: Configuração** na parte inferior da tela.

9. Em **Configuração**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **Rede** |  |
    | Rede virtual | Selecione **myVNet**. |
    | Sub-rede | Selecione **mySubnet**. |
    | **Integração de DNS privado** |  |
    | Integrar com a zona DNS privado | Mantenha o padrão **Sim**. |
    | Subscription | Selecione sua assinatura. |
    | Zonas DNS privadas | Mantenha o padrão **(Novo) privatelink.azurewebsites.net**.
    

13. Selecione **Examinar + criar**.

14. Selecione **Criar**.

## <a name="test-connectivity-to-private-endpoint"></a>Testar a conectividade com o ponto de extremidade privado

Nesta seção, você usará a máquina virtual criada na etapa anterior para se conectar ao aplicativo Web no ponto de extremidade privado.

1. Selecione **Grupos de recursos** no painel de navegação à esquerda.

2. Selecione **CreatePrivateEndpointQS-rg**.

3. Selecione **myVM**.

4. Na página de visão geral de **myVM**, escolha **Conectar** e **Bastion**.

5. Selecione o botão azul **Usar Bastion**.

6. Insira o nome de usuário e a senha que você inseriu durante a criação da máquina virtual.

7. Abra o Windows PowerShell no servidor depois de se conectar.

8. Digite `nslookup <your-webapp-name>.azurewebsites.net`. Substitua **\<your-webapp-name>** pelo nome do aplicativo Web criado nas etapas anteriores.  Você receberá uma mensagem semelhante à mostrada abaixo:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    O endereço IP privado **10.1.0.5** é retornado para o nome do aplicativo Web.  Esse endereço está na sub-rede da rede virtual criada anteriormente.

11. Na conexão do bastion com **myVM**, abra o Internet Explorer.

12. Insira a URL do aplicativo Web, **https://\<your-webapp-name>.azurewebsites.net**.

13. Você receberá a página padrão do aplicativo Web se o aplicativo não tiver sido implantado:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Página padrão do aplicativo Web." border="true":::

18. Feche a conexão com **myVM**.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não pretender continuar usando esse aplicativo, exclua a rede virtual, a máquina virtual e o aplicativo Web com as seguintes etapas:

1. No menu esquerdo, selecione **Grupos de recursos**.

2. Selecione **CreatePrivateEndpointQS-rg**.

3. Selecione **Excluir grupo de recursos**.

4. Insira **CreatePrivateEndpointQS-rg** em **DIGITE O NOME DO GRUPO DE RECURSOS**.

5. Selecione **Excluir**.


## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou:

* Uma rede virtual e um bastion host.
* Uma máquina virtual.
* Um ponto de extremidade privado para um Aplicativo Web do Azure.

Você usou a máquina virtual para testar a conectividade seguramente com o aplicativo Web no ponto de extremidade privado.



Para obter mais informações sobre os serviços que dão suporte a um ponto de extremidade privado, confira:
> [!div class="nextstepaction"]
> [Disponibilidade de Link Privado](private-link-overview.md#availability)
