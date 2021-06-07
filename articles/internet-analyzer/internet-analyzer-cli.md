---
title: Criar um teste do Analisador de Internet usando CLI | Microsoft Docs
description: Neste artigo, saiba como criar seu primeiro teste do Analisador de Internet usando a CLI do Azure.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 46caae53ed81de335c2b9d5ddbd3fd7f89424fdd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780407"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>Criar um teste do Analisador de Internet usando a CLI (versão prévia)

Há duas maneiras de criar um recurso do Analisador de Internet - por meio do portal do Azure ou usando a [CLI](internet-analyzer-create-test-portal.md). Esta seção ajuda você a criar um novo recurso do Analisador de Internet do Azure por meio da experiência com o CLI. 


> [!IMPORTANT]
> Essa versão prévia pública é fornecida sem um SLA e não deve ser usada para cargas de trabalho de produção. Determinados recursos podem não ter suporte, podem ter restrição ou podem não estar disponíveis em todos os locais do Azure. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.
>

## <a name="before-you-begin"></a>Antes de começar

A versão prévia pública está disponível para uso global. No entanto, o armazenamento de dados está limitado ao *Oeste dos EUA 2* durante a versão prévia.

## <a name="object-model"></a>Modelo de objeto
A CLI do analisador de Internet expõe os seguintes tipos de recursos:
* **Testes** - um teste compara o desempenho do usuário final de dois pontos de extremidade da Internet (A e B) ao longo do tempo.
* **Perfis** - testes são criados em um perfil do Analisador da Internet. Os perfis permitem que os testes relacionados sejam agrupados; um único perfil pode conter um ou mais testes.
* **Pontos de extremidade pré-configurados** - configuramos pontos de extremidade com uma variedade de configurações (regiões, tecnologias de aceleração etc.). Você pode usar qualquer um desses pontos de extremidade pré-configurados em seus testes.
* **Scorecards** - um scorecard fornece resumos rápidos e significativos dos resultados da medição. Consulte [Interpretar seu scorecard](internet-analyzer-scorecard.md).
* **Série temporal** - uma série temporal mostra como uma métrica muda ao longo do tempo.

## <a name="profile-and-test-creation"></a>Criação de perfil e teste
1. Obtenha acesso à versão prévia do Analisador de Internet seguindo as instruções em **Como fazer para participar da versão prévia?** nas [Perguntas frequentes do Analisador de Internet do Azure](internet-analyzer-faq.md).
2. [Instale a CLI do Azure](/cli/azure/install-azure-cli).
3. Execute o `login` comando para iniciar uma sessão de CLI:
    ```azurecli-interactive
    az login
    ```

    Se a CLI puder abrir o navegador padrão, ela o fará e carregará uma página de entrada do Azure.
    Caso contrário, abra uma página de navegador em https://aka.ms/devicelogin e insira o código de autorização exibido no terminal.

4. Entre com suas credenciais de conta no navegador.

5. Selecione sua ID de assinatura que tenha recebido acesso à visualização pública do analisador da Internet.

    Depois de entrar, você deve ver uma lista de assinaturas associadas à sua conta do Azure. A informação de assinatura com `isDefault: true` é a assinatura ativada no momento depois de entrar. Para selecionar outra assinatura, use o comando [az account set](/cli/azure/account#az_account_set) com a ID da assinatura para a qual alternar. Para obter mais informações sobre a seleção da assinatura, confira [Usar várias assinaturas do Azure](/cli/azure/manage-azure-subscriptions-azure-cli).

    Há maneiras de entrar de modo não interativo, como mostrado em detalhes em [Entrar com a CLI do Azure](/cli/azure/authenticate-azure-cli).

6. **[Opcional]** Criar um novo grupo de recursos do Azure:
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. Instalar a extensão do Analisador da Internet da CLI do Azure:
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. Criar um novo perfil do Analisador da Internet:
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. Listar todos os pontos de extremidade pré-configurados disponíveis para o perfil recém-criado:
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. Criar um novo teste no perfil do Analisador da Internet recém criado:
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    O comando acima pressupõe que `www.contoso.com` e `www.microsoft.com` estejam hospedando a imagem de um pixel ([trans.gif](https://fpc.msedge.net/apc/trans.gif)) em caminhos personalizados. Se um caminho de objeto não for especificado explicitamente, o Analisador da Internet usará `/apc/trans.gif` como o caminho do objeto por padrão, que é onde os pontos de extremidade pré-configurados estão hospedando a imagem de um pixel. Observe também que o esquema (HTTPS/HTTP) não precisa ser especificado; o Analisador da Internet só dá suporte a pontos de extremidade HTTPS, portanto, pressupõe-se que seja HTTPS.

11. O novo teste deve aparecer sob o perfil do Analisador da Internet:
    ```azurecli-interactive
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

    Saída de exemplo:
    ````
    [
        {
            "description": null,
            "enabledState": "Enabled",
            "endpointA": {
            "endpoint": "www.contoso.com/some/path/to/1k.jpg",
            "name": "contoso"
            },
            "endpointB": {
            "endpoint": "www.microsoft.com/another/path/to/1k.jpg",
            "name": "microsoft"
            },
            "id": "/subscriptions/faa9ddd0-9137-4659-99b7-cdc55a953342/resourcegroups/MyInternetAnalyzerResourceGroup/providers/Microsoft.Network/networkexperimentprofiles/MyInternetAnalyzerProfile/experiments/MyFirstInternetAnalyzerTest",
            "location": null,
            "name": "MyFirstInternetAnalyzerTest",
            "resourceGroup": "MyInternetAnalyzerResourceGroup",
            "resourceState": "Enabled",
            "scriptFileUri": "https://fpc.msedge.net/client/v2/d8c6fc64238d464c882cee4a310898b2/ab.min.js",
            "status": "Created",
            "tags": null,
            "type": "Microsoft.Network/networkexperimentprofiles/experiments"
        }
    ]
    ````

12. Para começar a gerar medições, o arquivo JavaScript apontado pelo **scriptFileUri** do teste deve ser inserido em seu aplicativo Web. Confira instruções específicas na página [Inserir cliente do Analisador de Internet](internet-analyzer-embed-client.md).

13. Você pode monitorar o progresso do teste controlando seu valor de "status":
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. Você pode inspecionar os resultados coletados do teste gerando séries temporais ou scorecards para ele:
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>Próximas etapas

* Procure a [referência à CLI do Analisador de Internet](/cli/azure/ext/internet-analyzer/internet-analyzer) para obter a lista completa de comandos e exemplos de uso com suporte.
* Leia as [Perguntas frequentes sobre o Analisador de Internet](internet-analyzer-faq.md).
* Saiba mais sobre como inserir o [Cliente do Analisador de Internet](internet-analyzer-embed-client.md) e criar um [ponto de extremidade personalizado](internet-analyzer-custom-endpoint.md).