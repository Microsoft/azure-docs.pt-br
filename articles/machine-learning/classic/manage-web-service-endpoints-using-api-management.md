---
title: 'ML Studio (clássico): gerenciar serviços Web usando o gerenciamento de API-Azure'
description: Um guia mostrando como gerenciar os serviços Web do AzureML usando o Gerenciamento de API. Gerencie seus pontos de extremidade da API REST definindo o acesso do usuário, a limitação de uso e o monitoramento por painel.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/03/2017
ms.openlocfilehash: 293643ab5354c51142baaf281b1845cfc9fa6f23
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518021"
---
# <a name="manage-azure-machine-learning-studio-classic-web-services-using-api-management"></a>Gerenciar serviços Web do Azure Machine Learning Studio (clássico) usando o Gerenciamento de API

**APLICA-SE A:**  ![Aplica-se a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (clássico) ![Não se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


## <a name="overview"></a>Visão geral
Este guia mostra como começar rapidamente a usar o Gerenciamento de API para gerenciar os serviços Web do Azure Machine Learning Studio (clássico).

## <a name="what-is-azure-api-management"></a>O que é o Gerenciamento de API do Azure?
O Gerenciamento de API do Azure é um serviço do Azure que permite que você gerencie seus pontos de extremidade da API REST, definindo o acesso do usuário, a limitação de uso e o monitoramento por painel. Confira o [site de gerenciamento de API do Azure](https://azure.microsoft.com/services/api-management/) para obter mais detalhes. Para começar a usar o Gerenciamento de API do Azure, confira o [guia de importação e publicação](../../api-management/import-and-publish.md). Essa outra guia, na qual este guia se baseia, aborda mais tópicos, incluindo configurações de notificação, faixa de preços, manipulação de resposta, autenticação do usuário, criação de produtos, assinaturas de desenvolvedor e dashboarding de uso.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia, você precisa:

* Uma conta do Azure.
* Uma conta do AzureML.
* O workspace, o serviço e a api_key para um teste do AzureML implantado como um serviço Web. Para obter detalhes sobre como criar um experimento do AzureML, confira o [Início rápido do Studio](create-experiment.md). Para obter informações sobre como implantar um experimento do Studio (clássico) como um serviço Web, confira as [instruções de implantação do Studio](deploy-a-machine-learning-web-service.md) para obter detalhes sobre como implantar um experimento do AzureML como um serviço Web. Como alternativa, o Apêndice A traz instruções sobre como criar e testar um teste simples do AzureML e implantá-lo como um serviço Web.

## <a name="create-an-api-management-instance"></a>Criar uma instância de Gerenciamento de API

Você pode gerenciar seu serviço Web do Azure Machine Learning com uma instância de Gerenciamento de API.

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **+ Criar um recurso**.
3. Na caixa de pesquisa, digite "Gerenciamento de API" e selecione o recurso de "Gerenciamento de API".
4. Clique em **Criar**.
5. O valor **Nome** será usado para criar uma URL exclusiva (este exemplo usa "demoazureml").
6. Selecione uma **Assinatura**, um **Grupo de recursos** e um **Local** para sua instância de serviço.
7. Especifique um valor para **Nome da organização** (este exemplo usa "demoazureml").
8. Insira seu **Email do administrador** – este email será usado para notificações do sistema de Gerenciamento de API.
9. Clique em **Criar**.

Pode levar até 30 minutos para um novo serviço ser criado.

![Captura de tela mostra a caixa de diálogo P I Management Service com as opções necessárias para criar um serviço.](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>Criar a API
Após criar a instância de serviço, a próxima etapa é criar a API. Uma API consiste em um conjunto de operações que podem ser iniciadas a partir de uma aplicativo cliente. Todas as operações de API são colocadas em proxies de serviços Web existentes. Este guia cria APIs que atua como proxy para os serviços Web RRS e BES do AzureML existentes.

Para criar a API:

1. No portal do Azure, abra a instância de serviço que você criou.
2. No painel de navegação esquerdo, selecione **APIs**.

   ![api-management-menu](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Clique em **Adicionar API**.
2. Insira um **nome de API Web** (este exemplo usa a "API de demonstração do AzureML").
3. Para **URL do serviço Web**, digite "`https://ussouthcentral.services.azureml.net`".
4. Insira um **sufixo de URL da API Web". Isso se tornará a última parte da URL que os clientes usarão para enviar solicitações à instância de serviço (este exemplo usa "azureml-demo").
5. Para o **esquema de URL da API Web**, selecione **HTTPS**.
6. Para **Produtos**, selecione **Inicial**.
7. Clique em **Save** (Salvar).


## <a name="add-the-operations"></a>Adicionar as operações

Operações são adicionadas e configuradas em uma API no Portal do editor. Para acessar o portal do publicador, clique em **Portal do publicador** no portal do Azure para seu serviço de Gerenciamento de API, selecione **APIs**, **Operações** e, em seguida, clique em **Adicionar operação**.

![add-operation](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

A janela **Nova operação** será exibida e a guia **Assinatura** será selecionada por padrão.

## <a name="add-rrs-operation"></a>Adicionar operação RRS
Primeiro, crie uma operação para o serviço RRS do AzureML:

1. Para o **verbo HTTP**, selecione **POST**.
2. Para o **modelo de URL**, digite "`/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}`".
3. Insira um **Nome de exibição** (este exemplo usa "RRS Execute").

   ![Captura de tela mostra a página de assinatura onde você pode inserir um nome de exibição.](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Clique em **Respostas** > **ADICIONAR** à esquerda e selecione **200 OK**.
5. Clique em **Salvar** para salvar esta operação.

   ![Captura de tela mostra a operação R R S executar página com um botão salvar.](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>Adicionar operações BES

> [!NOTE]
> Capturas de tela não estão incluídas aqui para as operações BES, pois são muito semelhantes àquelas para adicionar a operação RRS.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Enviar (mas não iniciar) um trabalho de execução em lotes

1. Clique em **Adicionar operação** para adicionar uma operação BES à API.
2. Para o **verbo HTTP**, selecione **POST**.
3. Para o **modelo de URL**, digite "`/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}`".
4. Insira um **Nome de exibição** (este exemplo usa "BES Submit").
5. Clique em **Respostas** > **ADICIONAR** à esquerda e selecione **200 OK**.
6. Clique em **Save** (Salvar).

### <a name="start-a-batch-execution-job"></a>Iniciar um trabalho de execução em lotes

1. Clique em **Adicionar operação** para adicionar uma operação BES à API.
2. Para o **verbo HTTP**, selecione **POST**.
3. Para o **verbo HTTP**, digite "`/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}`".
4. Insira um **Nome de exibição** (este exemplo usa "BES Start").
6. Clique em **Respostas** > **ADICIONAR** à esquerda e selecione **200 OK**.
7. Clique em **Save** (Salvar).

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Obter o status ou o resultado de um trabalho de execução em lotes

1. Clique em **Adicionar operação** para adicionar uma operação BES à API.
2. Para o **verbo HTTP**, selecione **GET**.
3. Para o **modelo de URL**, digite "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`".
4. Insira um **Nome de exibição** (este exemplo usa "BES Status").
6. Clique em **Respostas** > **ADICIONAR** à esquerda e selecione **200 OK**.
7. Clique em **Save** (Salvar).

### <a name="delete-a-batch-execution-job"></a>Excluir um trabalho de execução em lote

1. Clique em **Adicionar operação** para adicionar uma operação BES à API.
2. Para o **verbo HTTP**, selecione **DELETE**.
3. Para o **modelo de URL**, digite "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`".
4. Insira um **Nome de exibição** (este exemplo usa "BES Delete").
5. Clique em **Respostas** > **ADICIONAR** à esquerda e selecione **200 OK**.
6. Clique em **Save** (Salvar).

## <a name="call-an-operation-from-the-developer-portal"></a>Chamar uma operação no portal do Desenvolvedor

As operações podem ser chamadas diretamente do Portal do Desenvolvedor, que fornece uma forma conveniente de exibir e testar as operações de uma API. Nesta etapa, você chamará o método **RRS Execute** que foi adicionado à **API de demonstração do AzureML**. 

1. Clique em **Portal do desenvolvedor**.

   ![Captura de tela mostra o link do portal do desenvolvedor.](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Clique em **APIs** no menu superior e depois clique em **API de demonstração do AzureML** para ver as operações disponíveis.

   ![Captura de tela mostra o link P e r de demonstração do Azure M L.](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Selecione **RRS Execute** para a operação. Clique em **Experimentar**.

   ![Captura de tela mostra a demonstração do Azure M para a caixa de diálogo P I com a execução de R R S execute selected e um botão try.](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. Para os **Parâmetros de solicitação**, digite seu **workspace** e **serviço**, digite “2.0” para **apiversion** e “true” para os **detalhes**. Você pode encontrar o **workspace** e o **serviço** no painel de serviço Web do AzureML (confira **Testar o serviço Web** no Apêndice A).

   Para **Cabeçalhos de solicitação**, clique em **Adicionar cabeçalho** e digite "Content-Type" e "application/json". Clique em **Adicionar cabeçalho** novamente e digite "Autorização" e "Portador *\<your service API-KEY\>* ". Você pode encontrar a API-KEY no painel de serviço Web do AzureML (confira **Testar o serviço Web** no Apêndice A).

   Para o **corpo da solicitação**, digite `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}`.

   ![Captura de tela mostra os parâmetros de solicitação P da demonstração do Azure M L, cabeçalhos de solicitação, corpo da solicitação e autorização.](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Clique em **Enviar**.

   ![Captura de tela mostra um botão enviar.](./media/manage-web-service-endpoints-using-api-management/send.png)

Após invocar uma operação, o portal do desenvolvedor exibe a **URL solicitada** do serviço back-end, o **Status de resposta**, os **Cabeçalhos de resposta** e qualquer **Conteúdo de resposta**.

![Captura de tela mostra o portal do desenvolvedor exibindo o status da resposta, latência de resposta, cabeçalhos de resposta e conteúdo de resposta.](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Apêndice A - criando e testando um serviço Web do AzureML simples
### <a name="creating-the-experiment"></a>Criando o teste
Veja abaixo as etapas para criar um teste simples do AzureML e implantá-lo como um serviço Web. O serviço Web assume como entrada uma coluna de texto arbitrário e retorna um conjunto de recursos representados como números inteiros. Por exemplo:

| Texto | Texto marcado com sustenido |
| --- | --- |
| Este é um bom dia |1 1 2 2 0 2 0 1 |

Primeiro, usando um navegador de sua escolha, navegue até: [https://studio.azureml.net/](https://studio.azureml.net/) e insira suas credenciais para fazer logon. Em seguida, crie um novo teste em branco.

![Captura de tela mostra uma nova página com experimento selecionado e uma pesquisa de texto.](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Renomeie-o para **SimpleFeatureHashingExperiment**. Expanda **Conjuntos de dados salvos** e arraste **Resenhas de livros da Amazon** para o teste.

![A captura de tela mostra exemplos no lado esquerdo e um painel de SimpleFeatureHashingExperiment à direita com a instrução para arrastar itens aqui.](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Expanda **Transformação de Dados** e **Manipulação** e arraste **Selecionar Colunas do Conjunto de Dados** para o teste. Conecte **Resenhas de livros da Amazon** a **Selecionar Colunas do Conjunto de Dados**.

![Conectar o módulo de conjunto de dados de resenhas de livros a um módulo de Colunas do Projeto](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Clique em **Selecionar Colunas do Conjunto de Dados** e em **Iniciar seletor de colunas** e selecione **Col2**. Clique na marca de seleção para aplicar essas alterações.

![Selecionar colunas usando nomes de coluna](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Expanda **Análise de texto** e arraste **Hash de recurso** para o teste. Conecte **Selecionar Colunas no Conjunto de Dados** para **Hash de Funcionalidade**.

![Captura de tela mostra um item de hash de recurso que está sendo adicionado ao espaço de trabalho.](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Digite **3** para **Tamanho de Bit de Hash**. Isso criará 8 (23) colunas.

![Captura de tela mostra Propriedades com hash de recurso selecionado e você pode inserir bits de hash.](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

Neste ponto, talvez você queira clicar em **Executar** para verificar o teste.

![Captura de tela mostra um botão Executar.](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Criar um serviço Web
Agora crie um serviço Web. Expanda **Serviço Web** e arraste **Entrada** para o teste. Conecte **Entrada** a **Hash de Funcionalidade**. Também arraste **saída** para seu teste. Conecte **Saída** a **Hash de Funcionalidade**.

![Captura de tela mostra o espaço de trabalho após as alterações especificadas.](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Clique em **Publicar serviço Web**.

![Captura de tela mostra um botão publicar serviço WEB.](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Clique em **Sim** para publicar o teste.

![Captura de tela mostra uma mensagem de confirmação e a opção para publicar ou não.](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>Testar o serviço Web
Um serviço Web AzureML consiste em RSS (serviço de solicitação/resposta) e pontos de extremidade BES (serviço de execução em lotes). RSS é para execução síncrona. BES é para execução do trabalho assíncrono. Para testar o serviço Web com a fonte Python de exemplo abaixo, talvez seja necessário baixar e instalar o SDK do Azure para Python (confira: [Como instalar o Python](/azure/developer/python/azure-sdk-install)).

Você também precisará do **workspace**, do **serviço** e da **api_key** do teste para a fonte de exemplo abaixo. Você pode encontrar o workspace e o serviço clicando em **Solicitação/resposta** ou **Execução em lote** para o teste no painel de serviço Web.

![Captura de tela mostra o painel de solicitação onde você pode encontrar os valores de espaço de trabalho e de serviço.](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Você pode encontrar a **api_key** clicando no teste no painel de serviço Web.

![Captura de tela mostra o experimento no painel do serviço Web, onde você pode encontrar a chave P I.](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Testar ponto de extremidade RRS
##### <a name="test-button"></a>Botão de teste
Uma maneira fácil de testar o ponto de extremidade RRS é clicar em **Testar** no painel de serviço Web.

![Captura de tela mostra o experimento no painel do serviço Web que tem o botão testar.](./media/manage-web-service-endpoints-using-api-management/test.png)

Digite **Este é um bom dia** para **col2**. Clique na marca de seleção.

![Captura de tela mostra a caixa de diálogo inserir dados a prever, onde você pode inserir texto como o exemplo, esse é um bom dia.](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Você verá algo semelhante a

![Captura de tela mostra o resultado do experimento, que lê esse é um bom dia e vários dígitos entre aspas.](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Exemplo de código
Outra maneira de testar seu RRS é do código cliente. Se você clicar em **Solicitação/resposta** no painel e rolar até o final, verá o código de exemplo para C#, Python e R. Também verá a sintaxe da solicitação RRS, incluindo o URI, os cabeçalhos e o corpo da solicitação.

Este guia mostra um exemplo de trabalho do Python. Você precisará modificar com o **workspace**, o **serviço** e a **api_key** do teste.

```python
import urllib2
import json
workspace = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE WORKSPACE ID>"
service = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE SERVICE ID>"
api_key = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE API KEY>"
data = {
"Inputs": {
    "input1": {
        "ColumnNames": ["Col2"],
        "Values": [ [ "This is a good day" ] ]
    },
},
"GlobalParameters": { }
}
url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
body = str.encode(json.dumps(data))
try:
    req = urllib2.Request(url, body, headers)
    response = urllib2.urlopen(req)
    result = response.read()
    print "result:" + result
        except urllib2.HTTPError, error:
    print("The request failed with status code: " + str(error.code))
    print(error.info())
    print(json.loads(error.read()))
```

#### <a name="test-bes-endpoint"></a>Testar ponto de extremidade BES
Clique em **Execução em lote** no painel e role até o final. Você verá o código de exemplo para C#, Python e R. Também verá a sintaxe das solicitações BES para enviar um trabalho, iniciar um trabalho, obter o status ou os resultados de um trabalho e excluir um trabalho.

Este guia mostra um exemplo de trabalho do Python. Você precisa modificar com o **workspace**, o **serviço** e a **api_key** do teste. Além disso, você precisa modificar o **nome da conta de armazenamento**, a **chave da conta de armazenamento** e o **nome do contêiner de armazenamento**. Por fim, você precisará modificar o local do **arquivo de entrada** e o local do **arquivo de saída**.

```python
import urllib2
import json
import time
from azure.storage import *
workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
service = "<REPLACE WITH YOUR SERVICE ID>"
api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
input_blob_name = "mydatablob.csv"
output_blob_name = "myresultsblob.csv"
def printHttpError(httpError):
print("The request failed with status code: " + str(httpError.code))
print(httpError.info())
print(json.loads(httpError.read()))
return
def saveBlobToFile(blobUrl, resultsLabel):
print("Reading the result from " + blobUrl)
try:
    response = urllib2.urlopen(blobUrl)
except urllib2.HTTPError, error:
    printHttpError(error)
    return
with open(output_file, "wb+") as f:
    f.write(response.read())
print(resultsLabel + " have been written to the file " + output_file)
return
def processResults(result):
first = True
results = result["Results"]
for outputName in results:
    result_blob_location = results[outputName]
    sas_token = result_blob_location["SasBlobToken"]
    base_url = result_blob_location["BaseLocation"]
    relative_url = result_blob_location["RelativeLocation"]
    print("The results for " + outputName + " are available at the following Azure Storage location:")
    print("BaseLocation: " + base_url)
    print("RelativeLocation: " + relative_url)
    print("SasBlobToken: " + sas_token)
    if (first):
        first = False
        url3 = base_url + relative_url + sas_token
        saveBlobToFile(url3, "The results for " + outputName)
return

def invokeBatchExecutionService():
url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
print("Uploading the input to blob storage...")
data_to_upload = open(input_file, "r").read()
blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
print "Uploaded the input to blob storage"
input_blob_path = "/" + storage_container_name + "/" + input_blob_name
connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
payload =  {
    "Input": {
        "ConnectionString": connection_string,
        "RelativeLocation": input_blob_path
    },
    "Outputs": {
        "output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
    },
    "GlobalParameters": {
    }
}
    body = str.encode(json.dumps(payload))
headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
print("Submitting the job...")
# submit the job
req = urllib2.Request(url + "?api-version=2.0", body, headers)
try:
    response = urllib2.urlopen(req)
except urllib2.HTTPError, error:
    printHttpError(error)
    return
result = response.read()
job_id = result[1:-1] # remove the enclosing double-quotes
print("Job ID: " + job_id)
# start the job
print("Starting the job...")
req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
try:
    response = urllib2.urlopen(req)
except urllib2.HTTPError, error:
    printHttpError(error)
    return
url2 = url + "/" + job_id + "?api-version=2.0"

while True:
    print("Checking the job status...")
    # If you are using Python 3+, replace urllib2 with urllib.request in the following code
    req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    result = json.loads(response.read())
    status = result["StatusCode"]
    print "status:" + status
    if (status == 0 or status == "NotStarted"):
        print("Job " + job_id + " not yet started...")
    elif (status == 1 or status == "Running"):
        print("Job " + job_id + " running...")
    elif (status == 2 or status == "Failed"):
        print("Job " + job_id + " failed!")
        print("Error details: " + result["Details"])
        break
    elif (status == 3 or status == "Cancelled"):
        print("Job " + job_id + " cancelled!")
        break
    elif (status == 4 or status == "Finished"):
        print("Job " + job_id + " finished!")
        processResults(result)
        break
    time.sleep(1) # wait one second
return
invokeBatchExecutionService()
```