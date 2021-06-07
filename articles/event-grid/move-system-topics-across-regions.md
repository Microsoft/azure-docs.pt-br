---
title: Mover tópicos do sistema de grade de eventos do Azure para outra região
description: Este artigo mostra como mover tópicos do sistema de grade de eventos do Azure de uma região para outra região.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: eb6029b206e7d47789371ee81e75c4e05c69ee65
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89083035"
---
# <a name="move-azure-event-grid-system-topics-to-another-region"></a>Mover tópicos do sistema de grade de eventos do Azure para outra região
Talvez você queira mover seus recursos para outra região por vários motivos. Por exemplo, para aproveitar uma nova região do Azure, para atender aos requisitos internos de políticas e governança, ou em resposta aos requisitos de planejamento de capacidade. 

Aqui estão as etapas de alto nível abordadas neste artigo: 

- **Exporte o grupo de recursos** que contém a conta de armazenamento do Azure e seu tópico do sistema associado a um modelo do Resource Manager. Você também pode exportar um modelo somente para o tópico do sistema. Se você passar por essa rota, lembre-se de mover a origem do evento do Azure (neste exemplo, uma conta de armazenamento do Azure) para a outra região antes de mover o tópico do sistema. Em seguida, no modelo exportado para o tópico System, atualize a ID externa para a conta de armazenamento na região de destino. 
- **Modifique o modelo** para adicionar a `endpointUrl` Propriedade a fim de apontar para um webhook que assina o tópico do sistema. Quando o tópico do sistema é exportado, sua assinatura (nesse caso, é um webhook) também é exportada para o modelo, mas a `endpointUrl` propriedade não é incluída. Portanto, você precisa atualizá-lo para apontar para o ponto de extremidade que assina o tópico. Além disso, atualize o valor da `location` propriedade para o novo local ou região. Para outros tipos de manipuladores de eventos, você precisa atualizar apenas o local. 
- **Use o modelo para implantar recursos** na região de destino. Você especificará nomes para a conta de armazenamento e o tópico do sistema a ser criado na região de destino. 
- **verificar a implantação**. Verifique se o webhook é invocado quando você carrega um arquivo no armazenamento de BLOBs na região de destino. 
- Para **concluir a movimentação**, exclua os recursos (origem do evento e tópico do sistema) da região de origem. 

## <a name="prerequisites"></a>Pré-requisitos
- Conclua o [início rápido: rotear eventos de armazenamento de BLOBs para o ponto de extremidade da Web com o portal do Azure](blob-event-quickstart-portal.md) na região de origem. Esta etapa é **opcional**. Faça isso para testar as etapas neste artigo. Mantenha a conta de armazenamento em um grupo de recursos separado do serviço de aplicativo e do plano do serviço de aplicativo. 
- Verifique se o serviço de grade de eventos está disponível na região de destino. Consulte [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Preparar
Para começar, exporte um modelo do Resource Manager para o grupo de recursos que contém a origem do evento do sistema (conta de armazenamento do Azure) e seu tópico do sistema associado. 

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione **grupos de recursos** no menu à esquerda. Em seguida, selecione o grupo de recursos que contém a origem do evento para o qual o tópico do sistema foi criado. No exemplo a seguir, é a conta de **armazenamento do Azure** . O grupo de recursos contém a conta de armazenamento e seu tópico do sistema associado. 

    :::image type="content" source="./media/move-system-topics-across-regions/resource-group-page.png" alt-text="Página grupo de recursos":::        
3. No menu à esquerda, selecione **Exportar modelo** em **configurações** e, em seguida, selecione **baixar** na barra de ferramentas. 

    :::image type="content" source="./media/move-system-topics-across-regions/export-template-menu.png" alt-text="Conta do armazenamento – exportar página de modelo":::        
5. Localize o arquivo **. zip** que você baixou do portal e descompacte esse arquivo em uma pasta de sua escolha. Esse arquivo zip contém arquivos JSON de modelo e parâmetros. 
1. Abra o **template.jsem** um editor de sua escolha. 
1. A URL para o webhook não é exportada para o modelo. Então, execute as seguintes etapas:
    1. No arquivo de modelo, pesquise **webhook**. 
    1. Na seção **Propriedades** , adicione um caractere de vírgula ( `,` ) no final da última linha. Neste exemplo, é `"preferredBatchSizeInKilobytes": 64`. 
    1. Adicione a `endpointUrl` propriedade com o valor definido à URL do webhook, conforme mostrado no exemplo a seguir. 

        ```json
        "destination": {
            "properties": {
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64,
                "endpointUrl": "https://mysite.azurewebsites.net/api/updates"
            },
            "endpointType": "WebHook"
        }
        ```

        > [!NOTE]
        > Para outros tipos de manipuladores de eventos, todas as propriedades são exportadas para o modelo. Você só precisa atualizar a `location` propriedade para a região de destino, conforme mostrado na próxima etapa. 
7. Atualize `location` para o recurso de **conta de armazenamento** para a região ou local de destino. Para obter códigos de localização, consulte [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/). O código de uma região é o nome da região sem espaços, por exemplo, `West US` é igual a `westus` .

    ```json
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccounts_spegridstorage080420_name')]",
    "location": "westus",
    ```
8. Repita a etapa para atualizar `location` para o recurso de **tópico do sistema** no modelo. 

    ```json
    "type": "Microsoft.EventGrid/systemTopics",
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('systemTopics_spegridsystopic080420_name')]",
    "location": "westus",
    ```
1. **Salve** o modelo. 

## <a name="recreate"></a>Recriar 
Implante o modelo para criar uma conta de armazenamento e um tópico do sistema para a conta de armazenamento na região de destino. 

1. No portal do Azure, selecione **Criar um recurso**.
2. Em **Pesquisar no Marketplace**, digite **implantação de modelo** e pressione **Enter**.
3. Selecione **implantação de modelo**.
4. Selecione **Criar**.
5. Selecione **Criar seu próprio modelo no editor**.
6. Selecione **carregar arquivo** e siga as instruções para carregar o **template.jsno** arquivo que você baixou na última seção.
7. Selecione **salvar** para salvar o modelo. 
8. Na página **implantação personalizada** , siga estas etapas. 
    1. Selecione uma **assinatura** do Azure. 
    1. Selecione um **grupo de recursos** existente na região de destino ou crie um. 
    1. Para **região**, selecione a região de destino. Se você selecionou um grupo de recursos existente, essa configuração será somente leitura.
    1. Para o **nome do tópico do sistema**, insira um nome para o tópico do sistema que será associado à conta de armazenamento.  
    1. Para o **nome da conta de armazenamento**, insira um nome para a conta de armazenamento a ser criada na região de destino. 

        :::image type="content" source="./media/move-system-topics-across-regions/deploy-template.png" alt-text="Implantar modelo do Resource Manager":::
    5. Selecione **Revisar + criar** na parte inferior da página. 
    1. Na página **revisar + criar** , examine as configurações e selecione **criar**. 

## <a name="verify"></a>Verificar
1. Depois que a implantação for realizada com sucesso, selecione **goto grupo de recursos**. 
1. Na página **grupo de recursos** , verifique se a origem do evento (neste exemplo, conta de armazenamento do Azure) e o tópico sistema são criados. 
1. Carregue um arquivo em um contêiner no armazenamento de BLOBs do Azure e verifique se o webhook recebeu o evento. Para obter mais informações, consulte [enviar um evento para o ponto de extremidade](blob-event-quickstart-portal.md#send-an-event-to-your-endpoint).

## <a name="discard-or-clean-up"></a>Descartar ou limpar
Para concluir a movimentação, exclua o grupo de recursos que contém a conta de armazenamento e seu tópico do sistema associado na região de origem.  

Se você quiser reiniciar, exclua o grupo de recursos na região de destino e repita as etapas nas seções [preparar](#prepare) e [recriar](#recreate) deste artigo.

Para excluir um grupo de recursos (origem ou destino) usando o portal do Azure:

1. Na janela de pesquisa na parte superior de portal do Azure, digite **grupos de recursos** e selecione **grupos de recursos** nos resultados da pesquisa. 
2. Selecione o grupo de recursos a ser excluído e selecione **excluir** na barra de ferramentas. 

    Excluir grupo de recursos
3. Na página confirmação, insira o nome do grupo de recursos e selecione **excluir**.  

## <a name="next-steps"></a>Próximas etapas
Você aprendeu como mover uma fonte de eventos do Azure e seu tópico do sistema associado de uma região para outra região. Consulte os artigos a seguir para mover tópicos personalizados, domínios e namespaces de parceiros entre regiões.

- [Mover tópicos personalizados entre regiões](move-custom-topics-across-regions.md). 
- [Mover domínios entre regiões](move-domains-across-regions.md). 
- [Mover namespaces de parceiro entre regiões](move-partner-namespaces-across-regions.md). 

Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, consulte o seguinte artigo: [mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md).
