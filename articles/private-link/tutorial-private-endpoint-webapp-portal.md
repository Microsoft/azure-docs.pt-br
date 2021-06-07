---
title: 'Tutorial: Conectar-se a um aplicativo Web usando um ponto de extremidade privado do Azure'
titleSuffix: Azure Private Link
description: Comece com este tutorial usando o ponto de extremidade privado do Azure para se conectar a um aplicativo Web no modo privado.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 10/19/2020
ms.openlocfilehash: 502c48a92f5b41c4434d03139335a0ce05fa451f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896966"
---
# <a name="tutorial-connect-to-a-web-app-using-an-azure-private-endpoint"></a>Tutorial: Conectar-se a um aplicativo Web usando um ponto de extremidade privado do Azure

Um ponto de extremidade privado do Azure é o bloco de construção básico para o Link Privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem em modo privado com os recursos do Link Privado.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma rede virtual e um bastion host.
> * Crie uma máquina virtual.
> * Criar um aplicativo Web.
> * Criar um ponto de extremidade privado.
> * Testar a conectividade com o ponto de extremidade privado do aplicativo Web.

Caso não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!Note]
> O ponto de extremidade privado está disponível em regiões públicas para camada PremiumV2, camada PremiumV3, aplicativos Web do Windows, aplicativos Web do Linux e plano Premium do Azure Functions (às vezes chamado de plano Elástico Premium). 

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
    | Região           | Selecione **Oeste da Europa** |

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
    | Região | Selecione **Oeste da Europa** |
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

## <a name="create-web-app"></a>Criar um aplicativo Web

Nesta seção, você criará um aplicativo Web.

1. No menu à esquerda, selecione **Criar um recurso** > **Armazenamento** > **Aplicativo Web** ou procure **Aplicativo Web** na caixa de pesquisa.

2. Na guia **Informações Básicas** de **Criar Aplicativo Web**, insira ou selecione as seguintes informações:

    | Configuração | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscription | Selecionar sua assinatura do Azure |
    | Grupo de recursos | Selecione **myResourceGroup** |
    | **Detalhes da instância** |  |
    | Nome | Insira **mywebapp**. Se o nome não estiver disponível, insira um nome exclusivo. |
    | Publicar | Selecione **Código**. |
    | Pilha de runtime | Selecione **.NET Core 3.1 (LTS)** . |
    | Sistema operacional | Selecione **Windows**. |
    | Região | Selecione **Oeste da Europa** |
    | **Plano do Serviço de Aplicativo** |  |
    | Plano do Windows (Oeste da Europa) | Selecione **Criar novo**. </br> Insira **myServicePlan** em **Nome**. |
    | SKU e tamanho | Selecione **Alterar tamanho**. </br> Selecione **P2V2** na tela **Seletor de Especificações**. </br> Escolha **Aplicar**. |
   
3. Selecione **Examinar + criar**.

4. Selecione **Criar**.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/create-web-app.png" alt-text="Guia Informações Básicas de Criar aplicativo Web no portal do Azure." border="true":::

## <a name="create-private-endpoint"></a>Criar um ponto de extremidade privado

1. No menu à esquerda, selecione **Todos os Recursos** > **mywebapp** ou o nome escolhido durante a criação.

2. Na visão geral do aplicativo Web, selecione **Configurações** > **Rede**.

3. Em **Rede**, selecione **Configurar suas conexões de ponto de extremidade privado**.

4. Selecione **+ Adicionar** na tela **Conexões de Ponto de Extremidade Privado**.

5. Insira ou selecione as seguintes informações na tela **Adicionar Ponto de Extremidade Privado**:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira **mywebappendpoint**. |
    | Subscription | Selecione sua assinatura. |
    | Rede virtual | Selecione **myVNet**. |
    | Sub-rede | Selecione **mySubnet**. |
    | Integrar com a zona DNS privado | Selecione **Sim** na barra superior. |

6. Selecione **OK**.
    

## <a name="test-connectivity-to-private-endpoint"></a>Testar a conectividade com o ponto de extremidade privado

Nesta seção, você usará a máquina virtual criada na etapa anterior para se conectar ao aplicativo Web no ponto de extremidade privado.

1. Selecione **Grupos de recursos** no painel de navegação à esquerda.

2. Selecione **myResourceGroup**.

3. Selecione **myVM**.

4. Na página de visão geral de **myVM**, escolha **Conectar** e **Bastion**.

5. Selecione o botão azul **Usar Bastion**.

6. Insira o nome de usuário e a senha que você inseriu durante a criação da máquina virtual.

7. Abra o Windows PowerShell no servidor depois de se conectar.

8. Digite `nslookup <webapp-name>.azurewebsites.net`. Substitua **\<webapp-name>** pelo nome do aplicativo Web criado nas etapas anteriores.  Você receberá uma mensagem semelhante à mostrada abaixo:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    O endereço IP privado **10.1.0.5** é retornado para o nome do aplicativo Web.  Esse endereço está na sub-rede da rede virtual criada anteriormente.

9. Abra um navegador da Web no computador local e insira a URL externa do aplicativo Web, **https://\<webapp-name>.azurewebsites.net**.

10. Confirme se você recebeu uma página **403**. Essa página indica que o aplicativo Web não está acessível externamente.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-ext-403.png" alt-text="Página 403 do endereço do aplicativo Web externo." border="true":::

11. Na conexão do bastion com **myVM**, abra o Internet Explorer.

12. Insira a URL do aplicativo Web, **https://\<webapp-name>.azurewebsites.net**.

13. Confirme se você recebeu a página padrão do aplicativo Web.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-default-page.png" alt-text="Página padrão do aplicativo Web." border="true":::

18. Feche a conexão com **myVM**.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não pretender continuar usando esse aplicativo, exclua a rede virtual, a máquina virtual e o aplicativo Web com as seguintes etapas:

1. No menu esquerdo, selecione **Grupos de recursos**.

2. Selecione **myResourceGroup**.

3. Selecione **Excluir grupo de recursos**.

4. Insira **myResourceGroup** em **DIGITE O NOME DO GRUPOS DE RECURSOS**.

5. Selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Saiba como criar um serviço de Link Privado:
> [!div class="nextstepaction"]
> [Criar um serviço de Link Privado](create-private-link-service-portal.md)
