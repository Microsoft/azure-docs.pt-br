---
title: Tutorial – Implantar o Azure Spring Cloud em uma rede virtual
description: Implantar o Azure Spring Cloud em uma rede virtual (injeção de VNet).
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: 9d72d60bd3a1ef23b8122b2bc5ba4f0c5c701254
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587716"
---
# <a name="tutorial-deploy-azure-spring-cloud-in-a-virtual-network"></a>Tutorial: Implantar o Azure Spring Cloud em uma rede virtual

**Este artigo aplica-se a:** ✔️ Java ✔️ C#

Este tutorial explica como implantar uma instância do Azure Spring Cloud em sua rede virtual. Essa implantação às vezes é chamada de injeção de VNet.

A implantação permite:

* Isolamento de aplicativos e runtime de serviço do Azure Spring Cloud da Internet em sua rede corporativa.
* Interação do Azure Spring Cloud com sistemas em data centers locais ou serviços do Azure em outras redes virtuais.
* Permitir que clientes controlem comunicações de rede de entrada e saída para o Azure Spring Cloud.

## <a name="prerequisites"></a>Prerequisites

Registrar o provedor de recursos **Microsoft.AppPlatform** e **Microsoft.ContainerService** do Azure Spring Cloud de acordo com as instruções em [Registrar provedor de recursos no portal do Azure](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) ou executando o seguinte comando da CLI do Azure:

```azurecli
az provider register --namespace Microsoft.AppPlatform
az provider register --namespace Microsoft.ContainerService
```

## <a name="virtual-network-requirements"></a>Requisitos de rede virtual

A rede virtual na qual você implanta sua instância do Azure Spring Cloud deve atender aos seguintes requisitos:

* **Localização**: a rede virtual deve residir na mesma localização que a instância do Azure Spring Cloud.
* **Assinatura**: a rede virtual deve estar na mesma assinatura que a instância do Azure Spring Cloud.
* **sub-redes**: a rede virtual deve incluir duas sub-redes dedicadas a uma instância do Azure Spring Cloud:

    * Uma para o runtime do serviço.
    * Uma para seus aplicativos de microsserviço do Spring Boot.
    * Há uma relação um-para-um entre essas sub-redes e uma instância do Azure Spring Cloud. Use uma nova sub-rede para cada instância de serviço que você implantar. Cada sub-rede só pode incluir uma instância de serviço.
* **Espaço de endereço**: O CIDR bloqueia até */28* para a sub-rede de runtime do serviço e a sub-rede de aplicativos de microsserviço do Spring Boot.
* **Tabela de rotas**: as sub-redes não devem ter uma tabela de rotas existente associada.

Os procedimentos a seguir descrevem a configuração da rede virtual para conter a instância do Azure Spring Cloud.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Se você já tiver uma rede virtual para hospedar a instância do Azure Spring Cloud, pule as etapas 1, 2 e 3. Você pode começar da etapa 4 para preparar sub-redes para a rede virtual.

1. No menu do portal do Azure, selecione **Criar um recurso**. No Azure Marketplace, selecione **Rede** > **Rede virtual**.

1. Na caixa de diálogo **Criar rede virtual**, insira ou selecione as seguintes informações:

    |Configuração          |Valor                                             |
    |-----------------|--------------------------------------------------|
    |Subscription     |Selecione sua assinatura.                         |
    |Resource group   |Selecione seu grupo de recursos ou crie um.  |
    |Name             |Insira **azure-spring-cloud-vnet**.                 |
    |Location         |Selecione **Leste dos EUA**.                               |

1. Selecione **Avançar: Endereços IP**.

1. Para o espaço de endereço IPv4, insira **10.1.0.0/16**.

1. Selecione **Adicionar sub-rede**. Em seguida, insira **service-runtime-subnet** para **Nome da sub-rede** e **10.1.0.0/28** para **Intervalo de endereços de sub-rede**. Em seguida, selecione **Adicionar**.

1. Selecione **Adicionar sub-rede** novamente e insira **Nome da sub-rede** e **Intervalo de endereços da sub-rede**. Por exemplo, insira **apps-subnet** e **10.1.1.0/28**. Em seguida, selecione **Adicionar**.

1. Selecione **Examinar + criar**. Deixe o restante com os valores padrão e selecione **Criar**.

## <a name="grant-service-permission-to-the-virtual-network"></a>Conceder permissão de serviço para a rede virtual

Selecione a rede virtual **azure-spring-cloud-vnet** que você criou anteriormente.

1. Selecione **Controle de Acesso (IAM)** e escolha **Adicionar** > **Adicionar atribuição de função**.

    ![Captura de tela que mostra a tela do Controle de acesso.](./media/spring-cloud-v-net-injection/access-control.png)

1. Na caixa de diálogo **Adicionar atribuição de função**, insira ou selecione as seguintes informações:

    |Configuração  |Valor                                             |
    |---------|--------------------------------------------------|
    |Função     |Selecione **Proprietário**.                                 |
    |Selecionar   |Insira **Provedor de Recursos do Azure Spring Cloud**.   |

    Em seguida, selecione **Provedor de Recursos do Azure Spring Cloud** e selecione **Salvar**.

    ![Captura de tela que mostra a seleção do Provedor de Recursos do Azure Spring Cloud.](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

Você também pode fazer essa etapa executando o seguinte comando da CLI do Azure:

```azurecli
VIRTUAL_NETWORK_RESOURCE_ID=`az network vnet show \
    --name ${NAME_OF_VIRTUAL_NETWORK} \
    --resource-group ${RESOURCE_GROUP_OF_VIRTUAL_NETWORK} \
    --query "id" \
    --output tsv`

az role assignment create \
    --role "Owner" \
    --scope ${VIRTUAL_NETWORK_RESOURCE_ID} \
    --assignee e8de9221-a19c-4c81-b814-fd37c6caf9d2
```

## <a name="deploy-an-azure-spring-cloud-instance"></a>Implantar uma instância do Azure Spring Cloud

Para implantar uma instância do Azure Spring Cloud na rede virtual:

1. Abra o [Portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa superior, pesquise **Azure Spring Cloud**. Selecione **Azure Spring Cloud** nos resultados.

1. Na página do **Azure Spring Cloud**, clique em **+ Adicionar**.

1. Preencha o formulário na página **Criar** do Azure Spring Cloud.

1. Selecione o mesmo grupo de recursos e região que a rede virtual.

1. Para **Nome** em **Detalhes do Serviço**, selecione **azure-spring-cloud-vnet**.

1. Selecione a guia **Rede** e selecione os seguintes valores:

    |Configuração                                |Valor                                             |
    |---------------------------------------|--------------------------------------------------|
    |Implantar em sua rede virtual     |Selecione **Sim** na barra superior.                                   |
    |Rede virtual                        |Selecione **azure-spring-cloud-vnet**.               |
    |Sub-rede do runtime de serviço                 |Selecione **service-runtime-subnet**.                |
    |Sub-rede de aplicativos de microsserviço do Spring Boot   |Selecione **apps-subnet**.                           |

    ![Captura de tela que mostra a guia Rede na página Criar do Azure Spring Cloud.](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. Selecione **Examinar e criar**.

1. Verifique suas especificações e selecione **Criar**.

    ![Captura de tela que mostra a verificação das especificações.](./media/spring-cloud-v-net-injection/verify-specifications.png)

Após a implantação, dois grupos de recursos adicionais serão criados em sua assinatura para hospedar os recursos de rede para a instância do Azure Spring Cloud. Acesse a **Página Inicial** e selecione **Grupos de recursos** nos itens de menu superiores para localizar os novos grupos de recursos a seguir.

O grupo de recursos nomeado como **ap-svc-rt_{nome da instância de serviço}_{região da instância de serviço}** contém recursos de rede para o runtime de serviço da instância de serviço.

  ![Captura de tela que mostra o runtime de serviço.](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

O grupo de recursos nomeado como **ap-app_{nome da instância de serviço}_{região da instância de serviço}** contém recursos de rede para os aplicativos de microsserviço do Spring Boot da instância de serviço.

  ![Captura de tela que mostra o grupo de recursos de aplicativos.](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Esses recursos de rede estão conectados à sua rede virtual criada na imagem anterior.

  ![Captura de tela que mostra a rede virtual com dispositivos conectados.](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > Os grupos de recursos são totalmente gerenciados pelo serviço do Azure Spring Cloud. *Não* exclua nem modifique manualmente nenhum recurso.

## <a name="limitations"></a>Limitações

Um pequeno intervalo de sub-rede salva endereços IP, mas traz limitações para o número máximo de instâncias de aplicativo que a instância do Azure Spring Cloud pode conter.

| CIDR de sub-rede de aplicativo | IPs totais | IPs disponíveis | Máximo de instâncias do aplicativo                                        |
| --------------- | --------- | ------------- | ------------------------------------------------------------ |
| / 28             | 16        | 8             | <p> Aplicativo com 1 núcleo:  96 <br/> Aplicativo com 2 núcleos: 48<br/>  Aplicativo com 3 núcleos: 32 <br/> Aplicativo com 4 núcleos: 24 </p> |
| / 27             | 32        | 24            | <p> Aplicativo com 1 núcleo:  228<br/> Aplicativo com 2 núcleos: 144<br/>  Aplicativo com 3 núcleos: 96 <br/>  Aplicativo com 4 núcleos: 72</p> |
| / 26             | 64        | 56            | <p> Aplicativo com 1 núcleo:  500<br/> Aplicativo com 2 núcleos: 336<br/>  Aplicativo com 3 núcleos: 224<br/>  Aplicativo com 4 núcleos: 168</p> |
| / 25             | 128       | 120           | <p> Aplicativo com 1 núcleo:  500<br> Aplicativo com 2 núcleos:  500<br>  Aplicativo com 3 núcleos:  480<br>  Aplicativo com 4 núcleos: 360</p> |
| /24             | 256       | 248           | <p> Aplicativo com 1 núcleo:  500<br/> Aplicativo com 2 núcleos:  500<br/>  Aplicativo com 3 núcleos: 500<br/>  Aplicativo com 4 núcleos: 500</p> |

Para sub-redes, cinco endereços IP são reservados pelo Azure e pelo menos quatro endereços são necessários para o Azure Spring Cloud. Pelo menos nove endereços IP são necessários; portanto /29 e /30 não são operacionais.

Para uma sub-rede de runtime de serviço, o tamanho mínimo é /28. Esse tamanho não tem nenhuma influência sobre o número de instâncias de aplicativo.

## <a name="next-steps"></a>Próximas etapas

[Implantar o aplicativo no Azure Spring Cloud em sua VNet](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>Veja também

- [Solução de problemas do Azure Spring Cloud na VNET](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Responsabilidades do cliente para executar o Azure Spring Cloud na VNET](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)
