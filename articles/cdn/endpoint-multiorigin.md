---
title: Ponto de extremidade da CDN do Azure com várias origens (visualização)
description: Introdução ao ponto de extremidade da CDN do Azure várias origens.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: how-to
ms.date: 9/06/2020
ms.author: allensu
ms.openlocfilehash: 6e433950c04c4494201b090063b17a10e54a4822
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685764"
---
# <a name="azure-cdn-endpoint-multi-origin"></a>Ponto de extremidade da CDN do Azure: várias origens

O suporte a várias origens elimina o tempo de inatividade e estabelece redundância global. 

Ao escolher várias origens dentro de um ponto de extremidade da CDN do Azure, a redundância fornecida espalha o risco investigando a integridade de cada origem e fazendo failover, se necessário.

Configure um ou mais grupos de origem e escolha um grupo de origem padrão. Cada grupo de origem é uma coleção de uma ou mais origens que podem usar cargas de trabalho semelhantes.

> [!NOTE]
> Atualmente, esse recurso só está disponível na CDN do Azure da Microsoft. 

## <a name="create-the-origin-group"></a>Criar o grupo de origem

1. Entre no [Portal do Azure](https://portal.azure.com)

2. Selecione o perfil da CDN do Azure e, em seguida, selecione o ponto de extremidade a ser configurado para várias origens.

3. Selecione **origem** em **configurações** na configuração do ponto de extremidade:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-1.png" alt-text="Ponto de extremidade CDN" border="true":::

4. Para habilitar várias origens, você precisa de pelo menos um grupo de origem. Selecione **Criar grupo de origem**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-2.png" alt-text="Configurações de origem" border="true":::

5. Na configuração **Adicionar grupo de origem** , insira ou selecione as seguintes informações:

   | Configuração           | Valor                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Nome do grupo de origem | Insira um nome para seu grupo de origem.                                   |
   | Status da investigação      | Selecione **Habilitado**. </br> A CDN do Azure executará investigações de integridade de diferentes pontos em todo o mundo para determinar a integridade da origem. Não habilite se o grupo de origem atual não estiver ativo para evitar custo adicional.
   | Caminho de investigação        | O caminho na origem que é usado para determinar a integridade. |
   | Intervalo de investigação    | Selecione um intervalo de investigação de 1, 2 ou 4 minutos.                        |
   | Protocolo de investigação    | Selecione **http** ou **https**.                                         |
   | Método de investigação      | Selecione **cabeçalho** ou **obter**.                                           |
   | Grupo de origem padrão | Selecione a caixa para definir como grupo de origem padrão.
    
   :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-3.png" alt-text="Adicionar grupo de origem" border="true":::

6. Selecione **Adicionar**.

## <a name="add-multiple-origins"></a>Adicionar várias origens

1. Nas configurações de origem do ponto de extremidade, selecione **+ criar origem**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-5.png" alt-text="Criar origem" border="true":::

2. Insira ou selecione as seguintes informações na configuração **Adicionar origem** :

   | Configuração           | Valor                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Nome        | Insira um nome para a origem.        |
   | Tipo de origem | Selecione **armazenamento**, **serviço de nuvem**, **aplicativo Web** ou **origem personalizada**.                                   |
   | Nome do host de origem        | Selecione ou insira seu nome de host de origem.  O menu suspenso lista todas as origens disponíveis do tipo especificado na configuração anterior. Se você selecionou **origem personalizada** como seu tipo de origem, insira o domínio do servidor de origem do cliente. |
   | Cabeçalho de host de origem    | Insira o cabeçalho de host que você deseja que a CDN do Azure envie com cada solicitação ou deixe o padrão.                        |
   | Porta HTTP   | Insira a porta HTTP.                                         |
   | Porta HTTPS     | Insira a porta HTTPS.                                           |
   | Prioridade    | Insira um número entre 1 e 5.       |
   | Peso      | Insira um número entre 1 e 1000.   |

    > [!NOTE]
    > Quando uma origem é criada dentro de um grupo de origem, ela deve ser um acordo com uma prioridade e um peso. Se um grupo de origem tiver apenas uma origem, a prioridade e o peso padrão serão definidos como 1. O tráfego será roteado para as origens de prioridade mais alta se a origem estiver íntegra. Se uma origem for determinada como não íntegra, as conexões serão desviadas para outra origem na ordem de prioridade. Se duas origens tiverem a mesma prioridade, o tráfego será distribuído de acordo com o peso especificado para a origem 

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-6.png" alt-text="Adicionar origem adicional" border="true":::

3. Selecione **Adicionar**.

4. Selecione **Configurar origem** para definir o caminho de origem para todas as origens:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-7.png" alt-text="Configurar caminho de origem" border="true":::

5. Selecione **OK**.

## <a name="configure-origins-and-origin-group-settings"></a>Definir origens e configurações de grupo de origem

Depois de ter várias origens e um grupo de origem, você pode adicionar ou remover as origens em grupos diferentes. Origens dentro do mesmo grupo devem atender a cargas de trabalho semelhantes. O tráfego será distribuído para essas origens com base em seu status Íntegro, prioridade e valor de peso. 

1. Nas configurações de origem do ponto de extremidade da CDN do Azure, selecione o nome do grupo de origem que deseja configurar:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Definir origens e configurações de grupo de origem" border="true":::

2. Em **atualizar grupo de origem**, selecione **+ selecionar origem**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-9.png" alt-text="Atualizar grupo de origem" border="true":::

4. Selecione a origem a ser adicionada ao grupo na caixa de pull e selecione **OK**.

5. Verifique se a origem foi adicionada ao grupo e, em seguida, selecione **salvar**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-10.png" alt-text="Verificar origem adicional adicionada ao grupo" border="true":::

## <a name="remove-origin-from-origin-group"></a>Remover origem do grupo de origem

1. Nas configurações de origem do ponto de extremidade da CDN do Azure, selecione o nome do grupo de origem:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Remover origem do grupo" border="true":::

2. Para remover uma origem do grupo de origem, selecione o ícone de Lixeira ao lado da origem e selecione **salvar**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-11.png" alt-text="Atualizar origem de exclusão do grupo de origem" border="true":::

## <a name="override-origin-group-with-rules-engine"></a>Substituir grupo de origem pelo mecanismo de regras

Personalize como o tráfego é distribuído para grupos de origem diferentes usando o mecanismo de regras padrão.

Distribua o tráfego para um grupo diferente com base na URL da solicitação.

1. No ponto de extremidade da CDN, selecione **mecanismo de regras** em **configurações**:

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-12.png" alt-text="Mecanismo de regras" border="true":::

2. Selecione **+ Adicionar regra**.

3. Insira um nome para a regra no **nome**.

4. Selecione **+ condição** e, em seguida, selecione **caminho da URL**.

5. Na suspenso do **operador** , selecione **contém**.

6. Em **valor**, digite **/images**.

7. Selecione **+ Adicionar ação** e, em seguida, selecione **substituição de grupo de origem**.

8. Em **grupo de origem**, selecione o grupo de origem na caixa de pull.

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-13.png" alt-text="Condições do mecanismo de regras" border="true":::

Para todas as solicitações de entrada, se o caminho da URL contiver **/images**, a solicitação será atribuída ao grupo de origem na seção ação **(myoriginy)**. 

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você habilitou o ponto de extremidade da CDN do Azure com várias origens.

Para obter mais informações sobre a CDN do Azure e os outros serviços do Azure mencionados neste artigo, consulte:

* [CDN do Azure](./cdn-overview.md)
* [Recurso comparar produto da CDN do Azure](./cdn-features.md)
