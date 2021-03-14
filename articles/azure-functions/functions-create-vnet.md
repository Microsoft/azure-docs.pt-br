---
title: Usar pontos de extremidade privados para integrar Azure Functions a uma rede virtual
description: Este tutorial mostra como conectar uma função a uma rede virtual do Azure e bloqueá-la usando pontos de extremidade privados.
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: 3dd5e700b3081f1c1ef8e4601385c707a5738321
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102630462"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-by-using-private-endpoints"></a>Tutorial: integrar Azure Functions com uma rede virtual do Azure usando pontos de extremidade privados

Este tutorial mostra como usar Azure Functions para se conectar a recursos em uma rede virtual do Azure usando pontos de extremidade privados. Você criará uma função usando uma conta de armazenamento bloqueada por trás de uma rede virtual. A rede virtual usa um gatilho de fila do barramento de serviço.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Crie um aplicativo de funções no plano Premium.
> * Crie recursos do Azure, como o barramento de serviço, a conta de armazenamento e a rede virtual.
> * Bloqueie sua conta de armazenamento por trás de um ponto de extremidade privado.
> * Bloqueie o barramento de serviço por trás de um ponto de extremidade privado.
> * Implante um aplicativo de funções que usa o barramento de serviço e os gatilhos HTTP.
> * Bloqueie seu aplicativo de funções por trás de um ponto de extremidade privado.
> * Teste para ver se seu aplicativo de funções é seguro dentro da rede virtual.
> * Limpe os recursos.

## <a name="create-a-function-app-in-a-premium-plan"></a>Criar um aplicativo de funções em um plano Premium

Você criará um aplicativo de funções do .NET no plano Premium porque este tutorial usa C#. Também há suporte para outros idiomas no Windows. O plano Premium fornece escala sem servidor enquanto dá suporte à integração de rede virtual.

1. No menu portal do Azure ou na **Home** Page, selecione **criar um recurso**.

1. Na página **novo** , selecione aplicativo de funções de **computação**  >  .

1. Na página **noções básicas** , use a tabela a seguir para definir as configurações do aplicativo de funções.

    | Configuração      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **Assinatura** | Sua assinatura | Assinatura sob a qual esse novo aplicativo de funções é criado. |
    | **[Grupo de Recursos](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nome do novo grupo de recursos em que você criará seu aplicativo de funções. |
    | **Nome do aplicativo de funções** | Nome globalmente exclusivo | Nome que identifica seu novo aplicativo de funções. Os caracteres válidos são `a-z` (não diferencia maiúsculas de minúsculas), `0-9` e `-`.  |
    |**Publicar**| Código | Escolha publicar arquivos de código ou um contêiner do Docker. |
    | **Pilha de runtime** | .NET | Este tutorial usa o .NET. |
    |**Região**| Região preferencial | Escolha uma [região](https://azure.microsoft.com/regions/) perto de você ou perto de outros serviços que suas funções acessam. |

1. Selecione **Avançar: Hospedagem**. Na página **Hospedagem**, insira as configurações a seguir.

    | Configuração      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Conta de armazenamento](../storage/common/storage-account-create.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento usada pelo seu aplicativo de funções. Os nomes de conta de armazenamento devem ter entre 3 e 24 caracteres. Eles podem conter apenas números e letras minúsculas. Use também uma conta existente, que precisará atender aos [requisitos da conta de armazenamento](./storage-considerations.md#storage-account-requirements). |
    |**Sistema operacional**| Windows | Este tutorial usa o Windows. |
    | **[Plano](./functions-scale.md)** | Premium | Plano de hospedagem que define como os recursos são alocados para seu aplicativo de funções. Por padrão, quando você seleciona **Premium**, um novo plano do serviço de aplicativo é criado. O **SKU e o tamanho** padrão são **ep1**, em que o *EP* representa o _elástico Premium_. Para obter mais informações, consulte a lista de [SKUs Premium](./functions-premium-plan.md#available-instance-skus).<br/><br/>Ao executar funções JavaScript em um plano Premium, escolha uma instância que tenha menos vCPUs. Para obter mais informações, confira [Escolher planos Premium de núcleo único](./functions-reference-node.md#considerations-for-javascript-functions).  |

1. Selecione **Avançar: Monitoramento**. Na página **Monitoramento**, insira as configurações a seguir.

    | Configuração      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | Padrão | Crie um recurso de Application Insights do mesmo nome de aplicativo na região com suporte mais próximo. Expanda essa configuração se você precisar alterar o **nome do novo recurso** ou armazenar seus dados em um **local** diferente em uma [Geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/). |

1. Selecione **Examinar + criar** para examinar as seleções de configuração do aplicativo.

1. Na página **revisar + criar** , examine suas configurações. Em seguida, selecione **criar** para provisionar e implantar o aplicativo de funções.

1. No canto superior direito do portal, selecione o ícone **notificações** e observe a mensagem **implantação bem-sucedida** .

1. Selecione **Ir para recursos** para exibir o novo aplicativo de funções. Você também pode selecionar **Fixar no painel**. A fixação torna mais fácil retornar a esse recurso de aplicativo de função no seu painel.

Parabéns! Você criou com êxito seu aplicativo de funções Premium.

## <a name="create-azure-resources"></a>Criar recursos do Azure

Em seguida, você criará uma conta de armazenamento, um barramento de serviço e uma rede virtual. 
### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Suas redes virtuais precisarão de uma conta de armazenamento separada da que você criou com seu aplicativo de funções.

1. No menu portal do Azure ou na **Home** Page, selecione **criar um recurso**.

1. Na **nova** página, pesquise conta de *armazenamento*. Em seguida, selecione **Criar**.

1. Na guia **noções básicas** , use a tabela a seguir para definir as configurações da conta de armazenamento. Todas as outras configurações podem usar os valores padrão.

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Assinatura** | Sua assinatura | A assinatura na qual os recursos são criados. | 
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | O grupo de recursos que você criou com seu aplicativo de funções. |
    | **Nome** | mysecurestorage| O nome da conta de armazenamento à qual o ponto de extremidade privado será aplicado. |
    | **[Região](https://azure.microsoft.com/regions/)** | myFunctionRegion | A região em que você criou seu aplicativo de funções. |

1. Selecione **Examinar + criar**. Após a conclusão da validação, selecione **criar**.

### <a name="create-a-service-bus"></a>Criar um barramento de serviço

1. No menu portal do Azure ou na **Home** Page, selecione **criar um recurso**.

1. Na página **novo** , procure barramento de *serviço*. Em seguida, selecione **Criar**.

1. Na guia **noções básicas** , use a tabela a seguir para definir as configurações do barramento de serviço. Todas as outras configurações podem usar os valores padrão.

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Assinatura** | Sua assinatura | A assinatura na qual os recursos são criados. |
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | O grupo de recursos que você criou com seu aplicativo de funções. |
    | **Nome** | myServiceBus| O nome do barramento de serviço ao qual o ponto de extremidade privado será aplicado. |
    | **[Região](https://azure.microsoft.com/regions/)** | myFunctionRegion | A região em que você criou seu aplicativo de funções. |
    | **Tipo de preço** | Premium | Escolha esta camada para usar pontos de extremidade privados com o barramento de serviço do Azure. |

1. Selecione **Examinar + criar**. Após a conclusão da validação, selecione **criar**.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Os recursos do Azure neste tutorial se integram ao ou são colocados em uma rede virtual. Você usará pontos de extremidade privados para conter o tráfego de rede na rede virtual.

O tutorial cria duas sub-redes:
- **Default**: sub-rede para pontos de extremidade privados. Os endereços IP privados são fornecidos por essa sub-rede.
- **funções**: sub-rede para Azure Functions integração de rede virtual. Essa sub-rede é delegada para o aplicativo de funções.

Crie a rede virtual para a qual o aplicativo de funções se integra:

1. No menu portal do Azure ou na **Home** Page, selecione **criar um recurso**.

1. Na **nova** página, procure *rede virtual*. Em seguida, selecione **Criar**.

1. Na guia **noções básicas** , use a tabela a seguir para definir as configurações de rede virtual.

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Assinatura** | Sua assinatura | A assinatura na qual os recursos são criados. | 
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | O grupo de recursos que você criou com seu aplicativo de funções. |
    | **Nome** | myVirtualNet| O nome da rede virtual à qual seu aplicativo de funções se conectará. |
    | **[Região](https://azure.microsoft.com/regions/)** | myFunctionRegion | A região em que você criou seu aplicativo de funções. |

1. Na guia **endereços IP** , selecione **Adicionar sub-rede**. Use a tabela a seguir para definir as configurações de sub-rede.

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="Captura de tela da exibição criar configuração de rede virtual.":::

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Nome da sub-rede** | funções | O nome da sub-rede à qual seu aplicativo de funções se conectará. | 
    | **Intervalo de endereços da sub-rede** | 10.0.1.0/24 | O intervalo de endereços da sub-rede. Na imagem anterior, observe que o espaço de endereço IPv4 é 10.0.0.0/16. Se o valor fosse 10.1.0.0/16, o intervalo de endereços de sub-rede recomendado seria 10.1.1.0/24. |

1. Selecione **Examinar + criar**. Após a conclusão da validação, selecione **criar**.

## <a name="lock-down-your-storage-account"></a>Bloquear sua conta de armazenamento

Os pontos de extremidade privados do Azure são usados para se conectar a recursos específicos do Azure usando um endereço IP privado. Essa conexão garante que o tráfego de rede permaneça dentro da rede virtual escolhida e o acesso esteja disponível somente para recursos específicos. 

Crie pontos de extremidade privados para armazenamento de arquivos do Azure e armazenamento de BLOBs do Azure usando sua conta de armazenamento:

1. Em sua nova conta de armazenamento, no menu à esquerda, selecione **rede**.

1. Na guia **conexões do ponto de extremidade privado** , selecione **ponto de extremidade privado**.

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="Captura de tela de como criar pontos de extremidade privados para a conta de armazenamento.":::

1. Na guia **noções básicas** , use as configurações de ponto de extremidade particular mostradas na tabela a seguir.

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Assinatura** | Sua assinatura | A assinatura na qual os recursos são criados. | 
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Escolha o grupo de recursos que você criou com seu aplicativo de funções. | |
    | **Nome** | ponto de extremidade de arquivo | O nome do ponto de extremidade privado para arquivos da sua conta de armazenamento. |
    | **[Região](https://azure.microsoft.com/regions/)** | myFunctionRegion | Escolha a região em que você criou sua conta de armazenamento. |

1. Na guia **recurso** , use as configurações do ponto de extremidade privado mostradas na tabela a seguir.

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Assinatura** | Sua assinatura | A assinatura na qual os recursos são criados. | 
    | **Tipo de recurso**  | Microsoft.Storage/storageAccounts | O tipo de recurso para contas de armazenamento. |
    | **Recurso** | mysecurestorage | A conta de armazenamento que você criou. |
    | **Sub-recurso de destino** | arquivo | O ponto de extremidade privado que será usado para arquivos da conta de armazenamento. |

1. Na guia **configuração** , para a configuração **sub-rede** , escolha **padrão**.

1. Selecione **Examinar + criar**. Após a conclusão da validação, selecione **criar**. Os recursos na rede virtual agora podem se comunicar com os arquivos de armazenamento.

1. Crie outro ponto de extremidade privado para BLOBs. Na guia **recursos** , use as configurações mostradas na tabela a seguir. Para todas as outras configurações, use os mesmos valores que você usou para criar o ponto de extremidade privado para arquivos.

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Assinatura** | Sua assinatura | A assinatura na qual os recursos são criados. | 
    | **Tipo de recurso**  | Microsoft.Storage/storageAccounts | O tipo de recurso para contas de armazenamento. |
    | **Recurso** | mysecurestorage | A conta de armazenamento que você criou. |
    | **Sub-recurso de destino** | blob | O ponto de extremidade privado que será usado para BLOBs da conta de armazenamento. |

## <a name="lock-down-your-service-bus"></a>Bloquear o barramento de serviço

Crie o ponto de extremidade privado para bloquear seu barramento de serviço:

1. No novo barramento de serviço, no menu à esquerda, selecione **rede**.

1. Na guia **conexões do ponto de extremidade privado** , selecione **ponto de extremidade privado**.

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="Captura de tela de como ir para pontos de extremidade privados para o barramento de serviço.":::

1. Na guia **noções básicas** , use as configurações de ponto de extremidade particular mostradas na tabela a seguir.

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Assinatura** | Sua assinatura | A assinatura na qual os recursos são criados. | 
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | O grupo de recursos que você criou com seu aplicativo de funções. |
    | **Nome** | SB-ponto de extremidade | O nome do ponto de extremidade privado para arquivos da sua conta de armazenamento. |
    | **[Região](https://azure.microsoft.com/regions/)** | myFunctionRegion | A região em que você criou sua conta de armazenamento. |

1. Na guia **recurso** , use as configurações do ponto de extremidade privado mostradas na tabela a seguir.

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Assinatura** | Sua assinatura | A assinatura na qual os recursos são criados. | 
    | **Tipo de recurso**  | Microsoft.ServiceBus/namespaces | O tipo de recurso para o barramento de serviço. |
    | **Recurso** | myServiceBus | O barramento de serviço criado anteriormente no tutorial. |
    | **Sub-recurso de destino** | namespace | O ponto de extremidade privado que será usado para o namespace do barramento de serviço. |

1. Na guia **configuração** , para a configuração **sub-rede** , escolha **padrão**.

1. Selecione **Examinar + criar**. Após a conclusão da validação, selecione **criar**. 

Os recursos na rede virtual agora podem se comunicar com o barramento de serviço.

## <a name="create-a-file-share"></a>Criar um compartilhamento de arquivo

1. Na conta de armazenamento que você criou, no menu à esquerda, selecione **compartilhamentos de arquivos**.

1. Selecione **+ compartilhamentos de arquivos**. Para os fins deste tutorial, nomeie os *arquivos* de compartilhamento de arquivo.

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="Captura de tela de como criar um compartilhamento de arquivos na conta de armazenamento.":::

1. Selecione **Criar**.

## <a name="get-the-storage-account-connection-string"></a>Obter a cadeia de conexão da conta de armazenamento

1. Na conta de armazenamento que você criou, no menu à esquerda, selecione **chaves de acesso**.

1. Selecione **Mostrar chaves**. Copie e salve a cadeia de conexão da **key1**. Você precisará dessa cadeia de conexão ao definir as configurações do aplicativo.

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="Captura de tela de como obter uma cadeia de conexão da conta de armazenamento.":::

## <a name="create-a-queue"></a>Criar uma fila

Crie a fila onde o gatilho do barramento de serviço Azure Functions receberá eventos:

1. No barramento de serviço, no menu à esquerda, selecione **filas**.

1. Selecione **Políticas de acesso compartilhado**. Para os fins deste tutorial, nomeie a *fila* de lista.

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="Captura de tela de como criar uma fila do barramento de serviço.":::

1. Selecione **Criar**.

## <a name="get-a-service-bus-connection-string"></a>Obter uma cadeia de conexão do barramento de serviço

1. No barramento de serviço, no menu à esquerda, selecione políticas de **acesso compartilhado**.

1. Selecione **RootManageSharedAccessKey**. Copie e salve a **cadeia de conexão primária**. Você precisará dessa cadeia de conexão ao definir as configurações do aplicativo.

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="Captura de tela de como obter uma cadeia de conexão do barramento de serviço.":::

## <a name="integrate-the-function-app"></a>Integrar o aplicativo de funções

Para usar seu aplicativo de funções com redes virtuais, você precisa associá-la a uma sub-rede. Você usará uma sub-rede específica para a integração de rede virtual Azure Functions. Você usará a sub-rede padrão para outros pontos de extremidade privados criados neste tutorial.

1. Em seu aplicativo de funções, no menu à esquerda, selecione **rede**.

1. Em **Integração VNET**, selecione **Clique aqui para configurar**.

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="Captura de tela de como ir para a integração de rede virtual.":::

1. Selecione **Adicionar VNet**.

1. Em **rede virtual**, selecione a rede virtual que você criou anteriormente.

1. Selecione a sub-rede do **Functions** que você criou anteriormente. Seu aplicativo de funções agora está integrado à sua rede virtual!

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="Captura de tela de como conectar um aplicativo de funções a uma sub-rede.":::

## <a name="configure-your-function-app-settings"></a>Definir as configurações do aplicativo de funções

1. Em seu aplicativo de funções, no menu à esquerda, selecione **configuração**.

1. Para usar seu aplicativo de funções com redes virtuais, atualize as configurações do aplicativo mostradas na tabela a seguir. Para adicionar ou editar uma configuração, selecione **+ nova configuração de aplicativo** ou o ícone **Editar** na coluna mais à direita da tabela configurações do aplicativo. Quando terminar, selecione **Salvar**.

    :::image type="content" source="./media/functions-create-vnet/10-configure-app-settings.png" alt-text="Captura de tela de como definir as configurações do aplicativo de funções para pontos de extremidade privados.":::

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | A cadeia de conexão da conta de armazenamento que você criou. Essa cadeia de conexão de armazenamento é da seção [obter a cadeia de conexão da conta de armazenamento](#get-the-storage-account-connection-string) . Essa configuração permite que seu aplicativo de funções use a conta de armazenamento seguro para operações normais em tempo de execução. | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | A cadeia de conexão da conta de armazenamento que você criou. Essa configuração permite que seu aplicativo de funções use a conta de armazenamento seguro para arquivos do Azure, que é usada durante a implantação. |
    | **WEBSITE_CONTENTSHARE** | files | O nome do compartilhamento de arquivos que você criou na conta de armazenamento. Use essa configuração com WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | Crie essa configuração de aplicativo para a cadeia de conexão do seu barramento de serviço. Essa cadeia de conexão de armazenamento é da seção [obter uma cadeia de conexão do barramento de serviço](#get-a-service-bus-connection-string) .|
    | **WEBSITE_CONTENTOVERVNET** | 1 | Crie essa configuração de aplicativo. Um valor de 1 permite que seu aplicativo de funções seja dimensionado quando sua conta de armazenamento é restrita a uma rede virtual. |
    | **WEBSITE_DNS_SERVER** | 168.63.129.16 | Crie essa configuração de aplicativo. Quando seu aplicativo se integra a uma rede virtual, ele usará o mesmo servidor DNS que a rede virtual. Seu aplicativo de funções precisa dessa configuração para que possa trabalhar com as zonas privadas do DNS do Azure. É necessário quando você usa pontos de extremidade privados. Essa configuração e WEBSITE_VNET_ROUTE_ALL enviarão todas as chamadas de saída do seu aplicativo para sua rede virtual. |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | Crie essa configuração de aplicativo. Quando seu aplicativo se integra a uma rede virtual, ele usa o mesmo servidor DNS que a rede virtual. Seu aplicativo de funções precisa dessa configuração para que possa trabalhar com as zonas privadas do DNS do Azure. É necessário quando você usa pontos de extremidade privados. Essa configuração e WEBSITE_DNS_SERVER enviarão todas as chamadas de saída do seu aplicativo para sua rede virtual. |

1. Na exibição **configuração** , selecione a guia **configurações de tempo de execução da função** .

1. Defina **monitoramento de escala de tempo de execução** como **ativado**. Em seguida, selecione **Salvar**. O dimensionamento controlado por tempo de execução permite que você conecte funções de gatilho não HTTP a serviços que são executados dentro de sua rede virtual.

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="Captura de tela de como habilitar o dimensionamento controlado por tempo de execução para Azure Functions.":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger"></a>Implantar um gatilho do barramento de serviço e um gatilho HTTP

1. No GitHub, vá para o repositório de exemplo a seguir. Ele contém um aplicativo de funções e duas funções, um gatilho HTTP e um gatilho de fila do barramento de serviço.

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. Na parte superior da página, selecione **bifurcação** para criar uma bifurcação desse repositório em sua própria conta ou organização do github.

1. Em seu aplicativo de funções, no menu à esquerda, selecione **central de implantação**. Em seguida, selecione **configurações**.

1. Na guia **configurações** , use as configurações de implantação mostradas na tabela a seguir.

    | Configuração      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Origem** | GitHub | Você deve ter criado um repositório GitHub para o código de exemplo na etapa 2. | 
    | **Organização**  | myOrganization | A organização na qual o seu repositório está verificado. Em geral, é sua conta. |
    | **Repositório** | myrepositório | O repositório que você criou para o código de exemplo. |
    | **Branch** | main | A ramificação principal do repositório que você criou. |
    | **Pilha de runtime** | .NET | O código de exemplo está em C#. |

1. Clique em **Salvar**. 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="Captura de tela de como implantar código Azure Functions por meio do Portal.":::

1. A implantação inicial pode levar alguns minutos. Quando seu aplicativo for implantado com êxito, na guia **logs** , você verá uma mensagem de status **êxito (ativa)** . Se necessário, atualize a página.

Parabéns! Você implantou com êxito o aplicativo de funções de exemplo.

## <a name="lock-down-your-function-app"></a>Bloquear seu aplicativo de funções

Agora, crie o ponto de extremidade privado para bloquear seu aplicativo de funções. Esse ponto de extremidade privado conectará seu aplicativo de funções de forma privada e segura à sua rede virtual usando um endereço IP privado. 

Para obter mais informações, consulte a [documentação do ponto de extremidade particular](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).

1. Em seu aplicativo de funções, no menu à esquerda, selecione **rede**.

1. Em **conexões de ponto de extremidade privado**, selecione **configurar suas conexões de ponto de extremidade privado**.

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="Captura de tela de como navegar para um ponto de extremidade particular do aplicativo de funções.":::

1. Selecione **Adicionar**.

1. No painel que é aberto, use as seguintes configurações de ponto de extremidade privado:

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="Captura de tela de como criar um ponto de extremidade particular do aplicativo de funções. O nome é functionapp-Endpoint. A assinatura é ' Private Test sub CACHHAI '. A rede virtual é MyVirtualNet-tutorial. A sub-rede é padrão.":::

1. Selecione **OK** para adicionar o ponto de extremidade privado. 
 
Parabéns! Você protegeu com êxito seu aplicativo de funções, barramento de serviço e conta de armazenamento adicionando pontos de extremidade privados!

### <a name="test-your-locked-down-function-app"></a>Testar seu aplicativo de funções bloqueados

1. No aplicativo de funções, no menu à esquerda, selecione **funções**.

1. Selecione **ServiceBusQueueTrigger**.

1. No menu à esquerda, selecione **Monitor**. 
 
Você verá que não é possível monitorar seu aplicativo. Seu navegador não tem acesso à rede virtual e, portanto, não pode acessar diretamente os recursos dentro da rede virtual. 
 
Aqui está uma maneira alternativa de monitorar sua função usando Application Insights:

1. No aplicativo de funções, no menu à esquerda, selecione **Application insights**. Em seguida, selecione **exibir dados de Application insights**.

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="Captura de tela de como exibir o Application insights para um aplicativo de funções.":::

1. No menu à esquerda, selecione **métricas em tempo real**.

1. Abra uma nova guia. No barramento de serviço, no menu à esquerda, selecione **filas**.

1. Selecione sua fila.

1. No menu à esquerda, selecione Gerenciador do **barramento de serviço**. Em **Enviar**, para **tipo de conteúdo**, escolha **texto/simples**. Em seguida, insira uma mensagem. 

1. Selecione **Enviar** para enviar a mensagem.

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="Captura de tela de como enviar mensagens do barramento de serviço usando o Portal.":::

1. Na guia **métricas ao vivo** , você deve ver que o gatilho de fila do barramento de serviço foi acionado. Se não tiver, reenvie a mensagem do **Gerenciador do barramento de serviço**.

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="Captura de tela de como exibir mensagens usando métricas em tempo real para aplicativos de funções.":::

Parabéns! Você testou com êxito a configuração do aplicativo de funções com pontos de extremidade privados.

## <a name="understand-private-dns-zones"></a>Entender as zonas DNS privadas
Você usou um ponto de extremidade privado para se conectar aos recursos do Azure. Você está se conectando a um endereço IP privado em vez do ponto de extremidade público. Os serviços existentes do Azure são configurados para usar um DNS existente para se conectar ao ponto de extremidade público. Você deve substituir a configuração de DNS para se conectar ao ponto de extremidade privado.

Uma zona DNS privada é criada para cada recurso do Azure que foi configurado com um ponto de extremidade privado. Um registro DNS é criado para cada endereço IP privado associado ao ponto de extremidade privado.

As seguintes zonas DNS foram criadas neste tutorial:

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um aplicativo de funções Premium, uma conta de armazenamento e um barramento de serviço. Você protegeu todos esses recursos por trás de pontos de extremidade privados. 

Use os links a seguir para saber mais sobre os recursos de rede disponíveis:

> [!div class="nextstepaction"]
> [Opções de rede no Azure Functions](./functions-networking-options.md)


> [!div class="nextstepaction"]
> [Azure Functions plano Premium](./functions-premium-plan.md)
