---
title: 'Tutorial: usar uma função do Azure para processar documentos armazenados'
titleSuffix: Azure Cognitive Services
description: Este guia mostra como usar uma função do Azure para disparar o processamento de documentos que são carregados em um contêiner do Armazenamento de Blobs do Azure.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 03/19/2021
ms.author: lajanuar
ms.openlocfilehash: bf455d9401593b5c09fa295e492368a2a5bee240
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048685"
---
# <a name="tutorial-use-an-azure-function-to-process-stored-documents"></a>Tutorial: usar uma função do Azure para processar documentos armazenados

Você pode usar o Reconhecimento de Formulários como parte de um pipeline de processamento de dados automatizado criado com o Azure Functions. Este guia mostra como usar uma função do Azure para processar documentos que são carregados em um contêiner do Armazenamento de Blobs do Azure. Este fluxo de trabalho extrai dados de tabela de documentos armazenados usando o serviço de Layout do Reconhecimento de Formulários e salva os dados da tabela em um arquivo .csv no Azure. Em seguida, você pode exibir os dados usando o Microsoft Power BI (não abordado aqui).

> [!div class="mx-imgBorder"]
> ![diagrama de fluxo de trabalho de serviço do Azure](./media/tutorial-azure-function/workflow-diagram.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma conta de Armazenamento do Azure
> * Criar um projeto do Azure Functions
> * Extrair dados de layout de formulários carregados
> * Carregar dados de layout no Armazenamento do Azure

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Criar um recurso de Reconhecimento de Formulários"  target="_blank">crie um recurso de Reconhecimento de Formulários <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter a chave e o ponto de extremidade do Reconhecimento de Formulários. Após a implantação, clique em **Ir para o recurso**.
  * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo à API do Reconhecimento de Formulários. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
  * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.
* Um documento PDF local a ser analisado. Você pode baixar um [documento de exemplo](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout.pdf) a ser usado.
* [Python 3.8.x](https://www.python.org/downloads/) instalado.
* [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) instalado.
* [Azure Functions Core Tools](../../azure-functions/functions-run-local.md?tabs=windows%2ccsharp%2cbash#install-the-azure-functions-core-tools) instalado.
* Visual Studio Code instalado com as seguintes extensões:
  * [Extensão do Azure Functions](/azure/developer/python/tutorial-vs-code-serverless-python-01#visual-studio-code-python-and-the-azure-functions-extension)
  * [Extensão Python](https://code.visualstudio.com/docs/python/python-tutorial#_install-visual-studio-code-and-the-python-extension)

## <a name="create-an-azure-storage-account"></a>Criar uma conta do Armazenamento do Azure

[Crie uma conta do Armazenamento do Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) no portal do Azure. Selecione **StorageV2** como o Tipo de conta.

No painel esquerdo, selecione a guia **CORS** e remova a política CORS existente, se houver.

Depois de implantado, crie dois contêineres de Armazenamento de Blobs vazios, chamados **test** e **output**.

## <a name="create-an-azure-functions-project"></a>Criar um projeto do Azure Functions

Abra o Visual Studio Code. Se você tiver instalado a extensão do Azure Functions, deverá ver um logotipo do Azure no painel de navegação à esquerda. Selecione-a. Crie um projeto e, quando solicitado, crie uma pasta local **coa_new** para contê-lo.

![Botão de criação de função do VSCode](./media/tutorial-azure-function/vs-code-create-function.png)


Você será solicitado a definir várias configurações:
* No prompt **Selecionar um idioma**, selecione Python.
* No prompt **Selecionar um modelo**, escolha o gatilho do Armazenamento de Blobs do Azure. Em seguida, dê um nome ao gatilho padrão.
* No prompt **Selecionar configuração**, opte por criar configurações de aplicativo local.
* Selecione a assinatura do Azure que tem a conta de armazenamento criada. Em seguida, você precisa inserir o nome do contêiner de armazenamento (nesse caso, `test/{name}`)
* Opte por abrir o projeto na janela atual. 

![Exemplo de prompt de criação de VSCode](./media/tutorial-azure-function/vs-code-prompt.png)

Quando você concluir essas etapas, o VSCode adicionará um novo projeto do Azure Function com um script Python *\_\_init\_\_.py*. Esse script será disparado quando um arquivo for carregado no contêiner de armazenamento **test**, mas não fará nada.

## <a name="test-the-function"></a>Testar a função

Pressione F5 para executar a função básica. O VSCode solicitará que você selecione uma conta de armazenamento com a qual uma interface será criada. Selecione a conta de armazenamento criada e prossiga.

Abra o Gerenciador de Armazenamento do Azure e carregue um documento PDF de exemplo para o contêiner **Test**. Em seguida, verifique o terminal do VSCode. O script deve registrar que foi disparado pelo upload do PDF.

![Teste de terminal do VSCode](./media/tutorial-azure-function/vs-code-terminal-test.png)


Interrompa o script antes de continuar.

## <a name="add-form-processing-code"></a>Adicionar código de processamento de formulários

Em seguida, você adicionará o seu código ao script Python para chamar o serviço Reconhecimento de Formulários e analisar os documentos carregados usando a [API de Layout](concept-layout.md) do Reconhecimento de Formulários.

No VSCode, navegue até o arquivo *requirements.txt* da função. Isso especifica as dependências do script. Adicione os seguintes pacotes do Python ao arquivo:

```
cryptography
azure-functions
azure-storage-blob
azure-identity
requests
pandas
numpy
```

Em seguida, abra o script *\_\_init\_\_.py*. Adicione as seguintes declarações de `import` :

```Python
import logging
from azure.storage.blob import BlobServiceClient
import azure.functions as func
import json
import time
from requests import get, post
import os
from collections import OrderedDict
import numpy as np
import pandas as pd
```

Você pode deixar a função `main` gerada no estado em que se encontra. Você adicionará o seu código personalizado dentro dessa função.

```python
# This part is automatically generated
def main(myblob: func.InputStream):
    logging.info(f"Python blob trigger function processed blob \n"
    f"Name: {myblob.name}\n"
    f"Blob Size: {myblob.length} bytes")
```

O bloco de código a seguir chama a API de [Análise de Layout](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync) do Reconhecimento de Formulários no documento carregado. Preencha os valores de ponto de extremidade e chave. 


# <a name="version-20"></a>[versão 2.0](#tab/2-0)

```Python
# This is the call to the Form Recognizer endpoint
    endpoint = r"Your Form Recognizer Endpoint"
    apim_key = "Your Form Recognizer Key"
    post_url = endpoint + "/formrecognizer/v2.0/Layout/analyze"
    source = myblob.read()

    headers = {
    # Request headers
    'Content-Type': 'application/pdf',
    'Ocp-Apim-Subscription-Key': apim_key,
        }

    text1=os.path.basename(myblob.name)
```

# <a name="version-21-preview"></a>[versão 2.1 – versão prévia](#tab/2-1)

```Python
# This is the call to the Form Recognizer endpoint
    endpoint = r"Your Form Recognizer Endpoint"
    apim_key = "Your Form Recognizer Key"
    post_url = endpoint + "/formrecognizer/v2.1-preview.3/Layout/analyze"
    source = myblob.read()

    headers = {
    # Request headers
    'Content-Type': 'application/pdf',
    'Ocp-Apim-Subscription-Key': apim_key,
        }

    text1=os.path.basename(myblob.name)
```
---

> [!IMPORTANT]
> Acesse o portal do Azure. Se o recurso do Reconhecimento de Formulários que você criou na seção **Pré-requisitos** tiver sido implantado com êxito, clique no botão **Ir para o Recurso** em **Próximas etapas**. Encontre a chave e o ponto de extremidade na página da **chave e do ponto de extremidade** do recurso, em **gerenciamento de recursos**. 
>
> Lembre-se de remover a chave do seu código quando terminar e nunca poste-a publicamente. Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Para obter mais informações, confira o artigo [Segurança dos Serviços Cognitivos](../cognitive-services-security.md).

Em seguida, adicione código para consultar o serviço e obter os dados retornados. 


```Python
resp = requests.post(url = post_url, data = source, headers = headers)
    if resp.status_code != 202:
        print("POST analyze failed:\n%s" % resp.text)
        quit()
    print("POST analyze succeeded:\n%s" % resp.headers)
    get_url = resp.headers["operation-location"]

    wait_sec = 25
    
    time.sleep(wait_sec)
    # The layout API is async therefore the wait statement
    
    resp =requests.get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
    
    resp_json = json.loads(resp.text)
    
    
    status = resp_json["status"]
    
    
    if status == "succeeded":
        print("Layout Analysis succeeded:\n%s")
        results=resp_json
    else:
        print("GET Layout results failed:\n%s")
        quit()

    results=resp_json
```

Em seguida, adicione o código a seguir para se conectar ao contêiner **output** do Armazenamento do Azure. Preencha com seus valores para o nome e a chave da conta de armazenamento. Você pode obter a chave na guia **Chaves de acesso** do recurso de armazenamento no portal do Azure.

```Python
# This is the connection to the blob storage, with the Azure Python SDK
    blob_service_client = BlobServiceClient.from_connection_string("DefaultEndpointsProtocol=https;AccountName="Storage Account Name";AccountKey="storage account key";EndpointSuffix=core.windows.net")
    container_client=blob_service_client.get_container_client("output")
```

O código a seguir analisa a resposta retornada do Reconhecimento de Formulários, constrói um arquivo .csv e o carrega no contêiner **output**. 


> [!IMPORTANT]
> Provavelmente, você precisará editar esse código para corresponder à estrutura dos seus documentos de formulário.

```Python
# The code below is how I extract the json format into tabular data 
    # Please note that you need to adjust the code below to your form structure
    # It probably won't work out-of-box for your specific form
    pages = results["analyzeResult"]["pageResults"]

    def make_page(p):
        res=[]
        res_table=[]
        y=0
        page = pages[p]
        for tab in page["tables"]:
            for cell in tab["cells"]:
                res.append(cell)
                res_table.append(y)
            y=y+1
    
        res_table=pd.DataFrame(res_table)
        res=pd.DataFrame(res)
        res["table_num"]=res_table[0]
        h=res.drop(columns=["boundingBox","elements"])
        h.loc[:,"rownum"]=range(0,len(h))
        num_table=max(h["table_num"])
        return h, num_table, p

    h, num_table, p= make_page(0)   

    for k in range(num_table+1):
        new_table=h[h.table_num==k]
        new_table.loc[:,"rownum"]=range(0,len(new_table))
        row_table=pages[p]["tables"][k]["rows"]
        col_table=pages[p]["tables"][k]["columns"]
        b=np.zeros((row_table,col_table))
        b=pd.DataFrame(b)
        s=0
        for i,j in zip(new_table["rowIndex"],new_table["columnIndex"]):
            b.loc[i,j]=new_table.loc[new_table.loc[s,"rownum"],"text"]
            s=s+1

```

Por fim, o último bloco de código carrega a tabela extraída e os dados de texto em seu elemento do Armazenamento de Blobs.

```Python
    # Here is the upload to the blob storage
    tab1_csv=b.to_csv(header=False,index=False,mode='w')
    name1=(os.path.splitext(text1)[0]) +'.csv'
    container_client.upload_blob(name=name1,data=tab1_csv)
```

## <a name="run-the-function"></a>Executar a função

Pressione F5 para executar a função novamente. Use o Gerenciador de Armazenamento do Azure para carregar um formulário PDF de exemplo no contêiner de armazenamento **Test**. Essa ação deve disparar o script a ser executado e você deve ver o arquivo .csv resultante (exibido como uma tabela) no contêiner **output**.

Você pode conectar esse contêiner ao Power BI para criar visualizações avançadas dos dados que ele contém.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar uma Função do Azure escrita em Python para processar automaticamente documentos PDF carregados e gerar seu conteúdo em um formato mais amigável. Em seguida, saiba como usar o Power BI para exibir os dados.

> [!div class="nextstepaction"]
> [Microsoft Power BI](https://powerbi.microsoft.com/integrations/azure-table-storage/)

* [O que é o Reconhecimento de Formulários?](overview.md)
* Saiba mais sobre a [API de Layout](concept-layout.md)