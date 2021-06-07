---
title: Pesquisar conteúdo de armazenamento de BLOBs do Azure criptografado
titleSuffix: Azure Cognitive Search
description: Saiba como indexar e extrair texto de documentos criptografados no armazenamento de BLOBs do Azure com o Azure Pesquisa Cognitiva.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.openlocfilehash: 4bab8def514df21d948d67f3cfba846c43917be2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96530928"
---
# <a name="how-to-index-encrypted-blobs-using-blob-indexers-and-skillsets-in-azure-cognitive-search"></a>Como indexar BLOBs criptografados usando indexadores de BLOB e habilidades no Azure Pesquisa Cognitiva

Este artigo mostra como usar o [pesquisa cognitiva do Azure](search-what-is-azure-search.md) para indexar documentos que foram previamente criptografados no [armazenamento de BLOBs do Azure](../storage/blobs/storage-blobs-introduction.md) usando [Azure Key Vault](../key-vault/general/overview.md). Normalmente, um indexador não pode extrair conteúdo de arquivos criptografados porque ele não tem acesso à chave de criptografia. No entanto, aproveitando a habilidade personalizada do [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) seguida pelo [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md), você pode fornecer acesso controlado à chave para descriptografar os arquivos e ter conteúdo extraído deles. Isso desbloqueia a capacidade de indexar esses documentos sem comprometer o status de criptografia dos documentos armazenados.

Começando com documentos inteiros previamente criptografados (texto não estruturado), como PDF, HTML, DOCX e PPTX no armazenamento de BLOBs do Azure, este guia usa o postmaster e as APIs REST de pesquisa para executar as seguintes tarefas:

> [!div class="checklist"]
> * Defina um pipeline que descriptografa os documentos e extraia o texto deles.
> * Defina um índice para armazenar a saída.
> * Execute o pipeline para criar e carregar o índice.
> * Explore os resultados usando a pesquisa de texto completo e uma sintaxe de consulta avançada.

Caso não tenha uma assinatura do Azure, abra uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Este exemplo supõe que você já carregou seus arquivos no armazenamento de BLOBs do Azure e os criptografou no processo. Se precisar de ajuda para obter os arquivos carregados e criptografados inicialmente, confira [este tutorial](../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md) para saber como fazer isso.

+ [Armazenamento do Azure](https://azure.microsoft.com/services/storage/)
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) na mesma assinatura que o pesquisa cognitiva do Azure. O cofre de chaves deve ter a proteção de exclusão e **limpeza** **reversível** habilitada.
+ [Pesquisa cognitiva do Azure](search-create-service-portal.md) em uma [camada Faturável](search-sku-tier.md#tier-descriptions) (básica ou acima, em qualquer região)
+ [Azure Function](https://azure.microsoft.com/services/functions/)
+ [Aplicativo Postman para a área de trabalho](https://www.getpostman.com/)

## <a name="1---create-services-and-collect-credentials"></a>1-criar serviços e coletar credenciais

### <a name="set-up-the-custom-skill"></a>Configurar a habilidade personalizada

Este exemplo usa o projeto [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) de exemplo do repositório GitHub [Azure Search Power Skills](https://github.com/Azure-Samples/azure-search-power-skills) . Nesta seção, você implantará a habilidade em uma função do Azure para que ela possa ser usada em um configurador de habilidades. Um script de implantação interno cria um recurso de função do Azure chamado iniciando com **psdbf-function-app-** e carrega a habilidade. Você será solicitado a fornecer uma assinatura e um grupo de recursos. Certifique-se de escolher a mesma assinatura na qual sua instância do Azure Key Vault reside.

Operacionalmente, a habilidade DecryptBlobFile pega a URL e o token SAS para cada blob como entradas e gera o arquivo baixado e descriptografado usando o contrato de referência de arquivo que o Azure Pesquisa Cognitiva espera. Lembre-se de que o DecryptBlobFile precisa da chave de criptografia para executar a descriptografia. Como parte da configuração, você também criará uma política de acesso que concede acesso de função DecryptBlobFile à chave de criptografia no Azure Key Vault.

1. Clique no botão **implantar no Azure** encontrado na [página de aterrissagem do DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile#deployment), que abrirá o modelo do Resource Manager fornecido dentro do portal do Azure.

1. Selecione **a assinatura na qual sua instância do Azure Key Vault existe** (este guia não funcionará se você selecionar uma assinatura diferente) e selecione um grupo de recursos existente ou crie um novo (se você criar um novo, também precisará selecionar uma região na qual deseja implantá-lo).

1. Selecione **revisão + criar**, certifique-se de que você concorda com os termos e, em seguida, selecione **criar** para implantar a função do Azure.

    ![Modelo de ARM no portal](media/indexing-encrypted-blob-files/arm-template.jpg "Modelo de ARM no portal")

1. Aguarde até que a implantação seja concluída.

1. Navegue até sua instância de Azure Key Vault no Portal. [Crie uma política de acesso](../key-vault/general/assign-access-policy-portal.md) no Azure Key Vault que concede acesso à chave para a habilidade personalizada.
 
    1. Em **configurações**, selecione **políticas de acesso** e, em seguida, selecione **Adicionar política de acesso**
     
       ![Adicionar política de acesso ao keyvault](media/indexing-encrypted-blob-files/keyvault-access-policies.jpg "Políticas de acesso do keyvault")

    1. Em **Configurar do modelo**, selecione **Azure data Lake Storage ou armazenamento do Azure**.

    1. Para a entidade de segurança, selecione a instância de função do Azure que você implantou. Você pode procurá-lo usando o prefixo de recurso que foi usado para criá-lo na etapa 2, que tem um valor de prefixo padrão de **psdbf-function-app**.

    1. Não selecione nada para a opção de aplicativo autorizado.
     
        ![Modelo de política de acesso de adição de keyvault](media/indexing-encrypted-blob-files/keyvault-add-access-policy.jpg "Modelo de política de acesso do keyvault")

    1. Certifique-se de clicar em **salvar** na página políticas de acesso antes de navegar para realmente adicionar a política de acesso.
     
         ![Política de acesso para salvar do keyvault](media/indexing-encrypted-blob-files/keyvault-save-access-policy.jpg "Salvar política de acesso do keyvault")

1. Navegue até a função **psdbf-function-app** no portal e anote as seguintes propriedades, pois você precisará delas mais tarde no guia:

    1. A URL da função, que pode ser encontrada em **Essentials** na página principal da função.
    
        ![URL da função](media/indexing-encrypted-blob-files/function-uri.jpg "Onde encontrar a URL da função do Azure")

    1. O código de chave do host, que pode ser encontrado navegando até **chaves de aplicativo**, clicando para mostrar a chave **padrão** e copiando o valor.
     
        ![Código de chave do host de função](media/indexing-encrypted-blob-files/function-host-key.jpg "Onde encontrar o código de chave do host da função do Azure")

### <a name="cognitive-services"></a>Serviços Cognitivos

O enriquecimento e a execução do Configurador de ia são apoiados por serviços cognitivas, incluindo Análise de Texto e Pesquisa Visual Computacional para processamento de imagem e linguagem natural. Caso seu objetivo seja concluir um protótipo ou um projeto real, neste ponto, você poderá provisionar os Serviços Cognitivos (na mesma região da Pesquisa Cognitiva do Azure), de modo que você possa anexá-lo às operações de indexação.

Para este exercício, no entanto, ignore o provisionamento de recursos, porque Pesquisa Cognitiva do Azure pode se conectar aos Serviços Cognitivos nos bastidores e fornecer a você 20 transações gratuitas por execução de indexador. Depois que ele processa 20 documentos, o indexador falhará, a menos que uma chave de serviços cognitivas seja anexada ao configurador de habilidades. Para projetos maiores, planeje o provisionamento dos Serviços Cognitivos no nível S0 pago conforme o uso. Para obter mais informações, confira [Anexar Serviços Cognitivos](cognitive-search-attach-cognitive-services.md). Observe que uma chave de serviços cognitivas é necessária para executar um configurador com mais de 20 documentos, mesmo que nenhuma de suas habilidades cognitivas selecionadas se conecte a serviços cognitivas (como com o qualificable fornecido se nenhuma habilidade for adicionada a ele).

### <a name="azure-cognitive-search"></a>Pesquisa Cognitiva do Azure

O último componente é o Pesquisa Cognitiva do Azure, que você pode [criar no portal](search-create-service-portal.md). Você pode usar a camada gratuita para concluir este guia. 

Assim como acontece com a função do Azure, Reserve um tempo para coletar a chave de administração. Além disso, quando começar a estruturar as solicitações, você precisará fornecer a chave de API de administração e o ponto de extremidade usados para autenticar cada solicitação.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Obter uma URL e uma chave de API de administração para a Pesquisa Cognitiva do Azure

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha o nome de seu serviço de pesquisa. Você pode confirmar o nome do serviço examinando a URL do ponto de extremidade. Se a URL do ponto de extremidade for `https://mydemo.search.windows.net`, o nome do serviço será `mydemo`.

2. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

   ![Obter o nome do serviço e as chaves de consulta e de administrador](media/search-get-started-javascript/service-name-and-keys.png)

Todas as solicitações exigem uma api-key no cabeçalho de cada solicitação enviada a seu serviço. Uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que a manipula.

## <a name="2---set-up-postman"></a>2 – Configurar o Postman

Instale e configure o Postman.

### <a name="download-and-install-postman"></a>Baixar e instalar o Postman

1. Baixe o [código-fonte da coleção do Postman](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json).
1. Selecione **Arquivo** > **Importar** para importar o código-fonte para o Postman.
1. Selecione a guia **Coleções** e, em seguida, selecione o botão **...** (reticências).
1. Selecione **Editar**. 
   
   ![Aplicativo do Postman mostrando navegação](media/indexing-encrypted-blob-files/postman-edit-menu.jpg "Ir para o menu Editar no Postman")
1. Na caixa de diálogo **Editar**, selecione a guia **Variáveis**. 

Na guia **Variáveis**, você pode adicionar valores que o Postman insere sempre que encontra uma variável específica dentro de chaves duplas. Por exemplo, o Postman substituirá o símbolo `{{admin-key}}` pelo valor atual que você definir para `admin-key`. O Postman faz essa substituição nas URLs, nos cabeçalhos, no corpo da solicitação e assim por diante. 

Para obter o valor de `admin-key` , use a chave de API de administração de pesquisa cognitiva do Azure que você anotou anteriormente. Defina `search-service-name` como o nome do serviço de pesquisa cognitiva do Azure que você está usando. Defina `storage-connection-string` usando o valor na guia **chaves de acesso** da sua conta de armazenamento e defina `storage-container-name` como o nome do contêiner de BLOBs na conta de armazenamento em que os arquivos criptografados são armazenados. Defina `function-uri` para a URL da função do Azure que você anotou anteriormente e defina `function-code` para o código de chave do host de função do Azure que você anotou anteriormente. Deixe os valores padrão para os outros valores.

![Guia de variáveis de aplicativo do Postman](media/indexing-encrypted-blob-files/postman-variables-window.jpg "Janela de variáveis do Postman")

| Variável    | Onde obter |
|-------------|-----------------|
| `admin-key` | Na página **Chaves** do serviço da Pesquisa Cognitiva do Azure.  |
| `search-service-name` | O nome do serviço da Pesquisa Cognitiva do Azure. A URL é `https://{{search-service-name}}.search.windows.net`. |
| `storage-connection-string` | Na conta de armazenamento, na guia **Chaves de Acesso**, selecione **key1** > **Cadeia de conexão**. |
| `storage-container-name` | O nome do contêiner de BLOB que contém os arquivos criptografados a serem indexados. |
| `function-uri` |  Na função do Azure, em **Essentials** na página principal. |
| `function-code` | Na função do Azure, navegando para **chaves de aplicativo**, clicando para mostrar a chave **padrão** e copiando o valor. |
| `api-version` | Deixe como **2020-06-30**. |
| `datasource-name` | Deixe como **Encrypted-BLOBs-DS**. |
| `index-name` | Deixe como **Encrypted-BLOBs-IDX**. |
| `skillset-name` | Deixe como **Encrypted-BLOBs-SS**. |
| `indexer-name` | Deixe como **Encrypted-BLOBs-IXR**. |

### <a name="review-the-request-collection-in-postman"></a>Examinar a coleção de solicitações no Postman

Ao executar este guia, você deve emitir quatro solicitações HTTP:

- **Solicitação PUT para criar o índice**: este índice armazena os dados usados e retornados pela Pesquisa Cognitiva do Azure.
- **Pós-solicitação para criar a fonte de** arquivos: essa fonte de origem conecta o serviço de pesquisa cognitiva do Azure à sua conta de armazenamento e, portanto, arquivos de blob criptografados. 
- **PUT Solicitar para criar o** configurador de habilidades: o configurador especifica a definição de habilidade personalizada para a função do Azure que descriptografará os dados do arquivo de BLOB e um [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) para extrair o texto de cada documento depois que ele tiver sido descriptografado.
- **Solicitação PUT para criar o indexador**: A execução do indexador lê os dados, aplica o conjunto de habilidades e armazena os resultados. É necessário executar essa solicitação por último.

O [código-fonte](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json) contém uma coleção de postmaster que tem as quatro solicitações, bem como algumas solicitações úteis de acompanhamento. Para emitir as solicitações, no postmaster, selecione a guia para as solicitações e selecione **Enviar** para cada uma delas.

## <a name="3---monitor-indexing"></a>3-monitorar a indexação

A indexação e o enriquecimento são iniciados assim que você envia a solicitação Criar Indexador. Dependendo de quantos documentos estão em sua conta de armazenamento, a indexação pode demorar um pouco. Para descobrir se o indexador ainda está em execução, use a solicitação **obter status do indexador** fornecida como parte da coleção do postmaster e examine a resposta para saber se o indexador está em execução ou para exibir informações de erro e aviso.  

Se você estiver usando a camada gratuita, a seguinte mensagem será esperada: `"Could not extract content or metadata from your document. Truncated extracted text to '32768' characters"` . Essa mensagem aparece porque a indexação de blob na camada gratuita tem um [limite de 32K na extração de caracteres](search-limits-quotas-capacity.md#indexer-limits). Você não verá essa mensagem para esse conjunto de dados em camadas superiores. 

## <a name="4---search"></a>4-Pesquisar

Depois que a execução do indexador for concluída, você poderá executar algumas consultas para verificar se os dados foram descriptografados e indexados com êxito. Navegue até o serviço de Pesquisa Cognitiva do Azure no portal e use o [Gerenciador de pesquisa](search-explorer.md) para executar consultas nos dados indexados.

## <a name="next-steps"></a>Próximas etapas

Agora que você indexou com êxito os arquivos criptografados, você pode [iterar nesse pipeline adicionando habilidades mais cognitivas](cognitive-search-defining-skillset.md). Isso lhe permitirá enriquecer e obter informações adicionais sobre seus dados.

Se você estiver trabalhando com dados criptografados duplicados, talvez queira investigar os recursos de criptografia de índice disponíveis no Azure Pesquisa Cognitiva. Embora o indexador precise de dados descriptografados para fins de indexação, uma vez que o índice exista, ele pode ser criptografado usando uma chave gerenciada pelo cliente. Isso garantirá que seus dados sejam sempre criptografados quando em repouso. Para obter mais informações, consulte [Configurar chaves gerenciadas pelo cliente para criptografia de dados no Azure pesquisa cognitiva](search-security-manage-encryption-keys.md).