---
title: "Saiba como gerenciar os serviços Web do AzureML usando o Gerenciamento de API | Microsoft Docs"
description: "Um guia mostrando como gerenciar os serviços Web do AzureML usando o Gerenciamento de API."
keywords: "aprendizado de máquina, gerenciamento de api"
services: machine-learning
documentationcenter: 
author: roalexan
manager: jhubbard
editor: 
ms.assetid: 05150ae1-5b6a-4d25-ac67-fb2f24a68e8d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2017
ms.author: roalexan
ms.openlocfilehash: 53a6b18fb74db46ccb66c7c70851a9bf364e927c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="learn-how-to-manage-azureml-web-services-using-api-management"></a>Saiba como gerenciar os serviços Web do AzureML usando o Gerenciamento de API
## <a name="overview"></a>Visão geral
Este guia mostra como começar a usar rapidamente o Gerenciamento de API para gerenciar seus serviços Web do AzureML.

## <a name="what-is-azure-api-management"></a>O que é o Gerenciamento de API do Azure?
O Gerenciamento de API do Azure é um serviço do Azure que permite que você gerencie seus pontos de extremidade da API REST, definindo o acesso do usuário, a limitação de uso e o monitoramento por painel. Clique em [aqui](https://azure.microsoft.com/services/api-management/) para obter detalhes sobre o Gerenciamento de API do Azure. Clique em [aqui](../../api-management/api-management-get-started.md) para obter um guia sobre como começar a usar o Gerenciamento de API do Azure. Essa outra guia, na qual este guia se baseia, aborda mais tópicos, incluindo configurações de notificação, faixa de preços, manipulação de resposta, autenticação do usuário, criação de produtos, assinaturas de desenvolvedor e dashboarding de uso.

## <a name="what-is-azureml"></a>O que é o AzureML?
O AzureML é um serviço do Azure para aprendizado de máquina que permite facilmente criar, implantar e compartilhar soluções de análise avançadas. Clique em [aqui](https://azure.microsoft.com/services/machine-learning/) para obter detalhes sobre AzureML.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia, você precisa:

* Uma conta do Azure. Se você não tiver uma conta do Azure, clique [aqui](https://azure.microsoft.com/pricing/free-trial/) para obter detalhes sobre como criar uma conta de avaliação gratuita.
* Uma conta do AzureML. Se você não tiver uma conta do AzureML, clique [aqui](https://studio.azureml.net/) para obter detalhes sobre como criar uma conta de avaliação gratuita.
* O espaço de trabalho, o serviço e a api_key para um teste do AzureML implantado como um serviço Web. Clique [aqui](create-experiment.md) para obter detalhes sobre como criar um teste do AzureML. Clique [aqui](publish-a-machine-learning-web-service.md) para obter detalhes sobre como implantar um teste do AzureML como um serviço Web. Como alternativa, o Apêndice A traz instruções sobre como criar e testar um teste simples do AzureML e implantá-lo como um serviço Web.

## <a name="create-an-api-management-instance"></a>Criar uma instância de Gerenciamento de API
A seguir estão as etapas para usar o Gerenciamento de API para gerenciar seu serviço Web do AzureML. Primeiro, crie uma instância de serviço. Faça logon no [Portal Clássico](https://manage.windowsazure.com/) e clique em **Novo** > **Serviços de Aplicativos** > **Gerenciamento de API** > **Criar**.

![create-instance](./media/manage-web-service-endpoints-using-api-management/create-instance.png)

Especifique uma **URL**exclusiva. Este guia usa **demoazureml** – você precisará escolher algo diferente. Selecione a **Assinatura** e a **Região** da sua instância de serviço. Após fazer suas seleções, clique no botão Avançar.

![create-service-1](./media/manage-web-service-endpoints-using-api-management/create-service-1.png)

Especifique um valor para o **Nome da Organização**. Este guia usa **demoazureml** – você precisará escolher algo diferente. Insira seu endereço de email no campo **email do administrador** . Esse endereço de email é usado para notificações do sistema de Gerenciamento de API.

![create-service-2](./media/manage-web-service-endpoints-using-api-management/create-service-2.png)

Clique na caixa de seleção para criar sua instância de serviço. *Demora até 30 minutos para um novo serviço ser criado*.

## <a name="create-the-api"></a>Criar a API
Após criar a instância de serviço, a próxima etapa é criar a API. Uma API consiste em um conjunto de operações que podem ser iniciadas a partir de uma aplicativo cliente. Todas as operações de API são colocadas em proxies de serviços Web existentes. Este guia cria APIs que atua como proxy para os serviços Web RRS e BES do AzureML existentes.

As APIs são criadas e configuradas no portal do editor de API, que pode ser acessado por meio do Portal Clássico do Azure. Para acessar o portal do editor, selecione a instância de serviço.

![select-service-instance](./media/manage-web-service-endpoints-using-api-management/select-service-instance.png)

Clique em **Gerenciar** no Portal Clássico do Azure do seu serviço de Gerenciamento de API.

![manage-service](./media/manage-web-service-endpoints-using-api-management/manage-service.png)

Clique em **APIs** no menu **Gerenciamento de API** à esquerda e depois clique em **Adicionar API**.

![api-management-menu](./media/manage-web-service-endpoints-using-api-management/api-management-menu.png)

Digite **AzureML demonstração API** como o **Nome da API Web**. Digite **https://ussouthcentral.services.azureml.net** como a **URL do serviço Web**. Digite **azureml-demo** como o **sufixo da URL da API Web**. Marque **HTTPS** como o esquema de **URL da API Web**. Selecione **Iniciador** como **Produtos**. Quando terminar, clique em **Salvar** para criar a API.

![add-new-api](./media/manage-web-service-endpoints-using-api-management/add-new-api.png)

## <a name="add-the-operations"></a>Adicionar as operações
Clique em **Adicionar operação** para adicionar operações a esta API.

![add-operation](./media/manage-web-service-endpoints-using-api-management/add-operation.png)

A janela **Nova operação** será exibida e a guia **Assinatura** será selecionada por padrão.

## <a name="add-rrs-operation"></a>Adicionar operação RRS
Primeiro, crie uma operação para o serviço RRS do AzureML. Selecione **POST** como o **Verbo HTTP**. Digite **/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}** como o **Modelo de URL**. Digite **RRS Execute** como o **Nome de exibição**.

![add-rrs-operation-signature](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

Clique em **Respostas** > **ADICIONAR** à esquerda e selecione **200 OK**. Clique em **Salvar** para salvar esta operação.

![add-rrs-operation-response](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>Adicionar operações BES
Capturas de tela não estão incluídas para as operações BES pois são muito semelhantes àquelas para adicionar a operação RRS.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Enviar (mas não iniciar) um trabalho de execução em lotes
Clique em **Adicionar operação** para adicionar a operação BES do AzureML à API. Selecione **POST** para o **Verbo HTTP**. Digite **/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}** para o **Modelo de URL**. Digite **BES Submit** para o **Nome de exibição**. Clique em **Respostas** > **ADICIONAR** à esquerda e selecione **200 OK**. Clique em **Salvar** para salvar esta operação.

### <a name="start-a-batch-execution-job"></a>Iniciar um trabalho de execução em lotes
Clique em **Adicionar operação** para adicionar a operação BES do AzureML à API. Selecione **POST** para o **Verbo HTTP**. Digite **/workspaces/ {espaço} {serviço} /services/ /jobs/ {jobid} / iniciar? api-version = {apiversion}** para o **modelo de URL**. Digite **BES Start** para o **Nome de exibição**. Clique em **Respostas** > **ADICIONAR** à esquerda e selecione **200 OK**. Clique em **Salvar** para salvar esta operação.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Obter o status ou o resultado de um trabalho de execução em lotes
Clique em **Adicionar operação** para adicionar a operação BES do AzureML à API. Selecione **GET** para o **Verbo HTTP**. Digite **/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}** para o **Modelo de URL**. Digite **BES Status** para o **Nome de exibição**. Clique em **Respostas** > **ADICIONAR** à esquerda e selecione **200 OK**. Clique em **Salvar** para salvar esta operação.

### <a name="delete-a-batch-execution-job"></a>Excluir um trabalho de execução em lote
Clique em **Adicionar operação** para adicionar a operação BES do AzureML à API. Selecione **DELETE** para o **Verbo HTTP**. Digite **/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}** para o **Modelo de URL**. Digite **BES Delete** para o **Nome de exibição**. Clique em **Respostas** > **ADICIONAR** à esquerda e selecione **200 OK**. Clique em **Salvar** para salvar esta operação.

## <a name="call-an-operation-from-the-developer-portal"></a>Chamar uma operação no Portal do Desenvolvedor
As operações podem ser chamadas diretamente do Portal do Desenvolvedor, que fornece uma forma conveniente de exibir e testar as operações de uma API. Nesta etapa do guia, você chamará o método **RRS Execute** que foi adicionado à **API de demonstração do AzureML**. Clique em **Portal do desenvolvedor** no menu da parte superior direita do Portal Clássico.

![developer-portal](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

Clique em **APIs** no menu superior e depois clique em **API de demonstração do AzureML** para ver as operações disponíveis.

![demoazureml-api](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

Selecione **RRS Execute** para a operação. Clique em **Experimentar**.

![avaliar](./media/manage-web-service-endpoints-using-api-management/try-it.png)

Para os parâmetros Request, digite seu **espaço de trabalho**, **serviço**, **2.0** para **apiversion** e **true** para **detalhes**. Você pode encontrar o **espaço de trabalho** e o **serviço** no painel de serviço Web do AzureML (confira **Testar o serviço Web** no Apêndice A).

Para cabeçalhos Request, clique em **Adicionar cabeçalho** e digite **Content-Type** e **application/json**; em seguida, clique em **Adicionar cabeçalho** e digite **Authorization** e **Bearer<YOUR AZUREML SERVICE API-KEY>**. Você pode encontrar a **chave de api** no painel de serviço Web do AzureML (confira **Testar o serviço Web** no Apêndice A).

Digite **{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["Este é um bom dia"]]}}, "GlobalParameters": {}}** para o corpo da solicitação.

![azureml-demo-api](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

Clique em **Enviar**.

![Enviar](./media/manage-web-service-endpoints-using-api-management/send.png)

Após invocar uma operação, o portal do desenvolvedor exibe a **URL solicitada** do serviço back-end, o **Status de resposta**, os **Cabeçalhos de resposta** e qualquer **Conteúdo de resposta**.

![response-status](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Apêndice A - criando e testando um serviço Web do AzureML simples
### <a name="creating-the-experiment"></a>Criando o teste
Veja abaixo as etapas para criar um teste simples do AzureML e implantá-lo como um serviço Web. O serviço Web assume como entrada uma coluna de texto arbitrário e retorna um conjunto de recursos representados como números inteiros. Por exemplo:

| Texto | Texto marcado com sustenido |
| --- | --- |
| Este é um bom dia |1 1 2 2 0 2 0 1 |

Primeiro, usando um navegador de sua escolha, navegue até: [https://studio.azureml.net/](https://studio.azureml.net/) e insira suas credenciais para fazer logon. Em seguida, crie um novo teste em branco.

![search-experiment-templates](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Renomeie-o para **SimpleFeatureHashingExperiment**. Expanda **Conjuntos de dados salvos** e arraste **Resenhas de livros da Amazon** para o teste.

![simple-feature-hashing-experiment](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Expanda **Transformação de Dados** e **Manipulação** e arraste **Selecionar Colunas do Conjunto de Dados** para o teste. Conecte **Resenhas de livros da Amazon** a **Selecionar Colunas do Conjunto de Dados**.

![select-columns](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Clique em **Selecionar Colunas do Conjunto de Dados** e em **Iniciar seletor de colunas** e selecione **Col2**. Clique na marca de seleção para aplicar essas alterações.

![select-columns](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Expanda **Análise de texto** e arraste **Hash de recurso** para o teste. Conecte **Selecionar Colunas no Conjunto de Dados** para **Hash de Funcionalidade**.

![connect-project-columns](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Digite **3** para **Tamanho de Bit de Hash**. Isso criará 8 (23) colunas.

![hashing-bitsize](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

Neste ponto, talvez você queira clicar em **Executar** para verificar o teste.

![Executar](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Criar um serviço Web
Agora crie um serviço Web. Expanda **Serviço Web** e arraste **Entrada** para o teste. Conecte **Entrada** a **Hash de Funcionalidade**. Também arraste **saída** para seu teste. Conecte **Saída** a **Hash de Funcionalidade**.

![output-to-feature-hashing](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Clique em **Publicar serviço Web**.

![publish-web-service](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Clique em **Sim** para publicar o teste.

![yes-to-publish](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>Testar o serviço Web
Um serviço Web AzureML consiste em RSS (serviço de solicitação/resposta) e pontos de extremidade BES (serviço de execução em lotes). RSS é para execução síncrona. BES é para execução do trabalho assíncrono. Para testar o serviço Web com a fonte Python de exemplo a seguir, talvez seja necessário baixar e instalar o SDK do Azure para Python (consulte: [Como instalar Python](../../python-how-to-install.md)).

Você também precisará do **espaço de trabalho**, do **serviço** e da **api_key** do teste para a fonte de exemplo abaixo. Você pode encontrar o espaço de trabalho e o serviço clicando em **Solicitação/resposta** ou **Execução em lote** para o teste no painel de serviço Web.

![find-workspace-and-service](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Você pode encontrar a **api_key** clicando no teste no painel de serviço Web.

![find-api-key](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Testar ponto de extremidade RRS
##### <a name="test-button"></a>Botão de teste
Uma maneira fácil de testar o ponto de extremidade RRS é clicar em **Testar** no painel de serviço Web.

![test](./media/manage-web-service-endpoints-using-api-management/test.png)

Digite **Este é um bom dia** para **col2**. Clique na marca de seleção.

![enter-data](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Você verá algo semelhante a

![sample-output](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Exemplo de código
Outra maneira de testar seu RRS é do código cliente. Se você clicar em **Solicitação/resposta** no painel e rolar até o final, verá o código de exemplo para C#, Python e R. Também verá a sintaxe da solicitação RRS, incluindo o URI, os cabeçalhos e o corpo da solicitação.

Este guia mostra um exemplo de trabalho do Python. Você precisará modificar com o **espaço de trabalho**, o **serviço** e a **api_key** do teste.

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE API KEY>"
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

#### <a name="test-bes-endpoint"></a>Testar ponto de extremidade BES
Clique em **Execução em lote** no painel e role até o final. Você verá o código de exemplo para C#, Python e R. Também verá a sintaxe das solicitações BES para enviar um trabalho, iniciar um trabalho, obter o status ou os resultados de um trabalho e excluir um trabalho.

Este guia mostra um exemplo de trabalho do Python. Você precisa modificar com o **espaço de trabalho**, o **serviço** e a **api_key** do teste. Além disso, você precisa modificar o **nome da conta de armazenamento**, a **chave da conta de armazenamento** e o **nome do contêiner de armazenamento**. Por fim, você precisará modificar o local do **arquivo de entrada** e o local do **arquivo de saída**.

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
    with open(output_file, "w+") as f:
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
        # If you are using Python 3+, replace urllib2 with urllib.request in the follwing code
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
