---
title: Solucionar problemas Azure Data Factory UX
description: Saiba como solucionar problemas de Azure Data Factory UX.
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: b423b008453ca5f1b76e5c277f5a434dbf14cdb1
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382830"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Solucionar problemas Azure Data Factory UX

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de solução de problemas para o Azure Data Factory UX.

## <a name="adf-ux-not-loading"></a>A UX do ADF não está sendo carregada

> [!NOTE]
> O Azure Data Factory UX oficialmente dá suporte ao Microsoft Edge e ao Google Chrome. Usar outros navegadores da Web pode levar a um comportamento inesperado ou não documentado.

### <a name="third-party-cookies-blocked"></a>Cookies de terceiros bloqueados

O ADF UX usa cookies de navegador para manter a sessão do usuário e habilitar experiências de desenvolvimento e monitoramento interativas. É possível que seu navegador bloqueie cookies de terceiros porque você está usando uma sessão Incognito ou tem um bloqueador de anúncios habilitado. O bloqueio de cookies de terceiros pode causar problemas ao carregar o portal, como ser redirecionado para uma página em branco, ' https://adf.azure.com/accesstoken.html ' ou receber uma mensagem de aviso informando que os cookies de terceiros estão bloqueados. Para resolver esse problema, habilite as opções de cookies de terceiros no seu navegador usando as seguintes etapas:

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>Permitir todos os cookies

1. Visite **Chrome://Settings/cookies** em seu navegador.
1. Opção Selecionar **permitir todos os cookies** 

    ![Permitir todos os cookies no Chrome](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. Atualize o UX do ADF e tente novamente.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Permitir que somente a UX do ADF use cookies
Se você não quiser permitir todos os cookies, você pode opcionalmente permitir apenas o ADF UX:
1. Visite **Chrome://Settings/cookies**.
1. Selecione **Adicionar** em **sites que sempre podem usar a opção cookies** 

    ![Adicionar a UX do ADF a sites permitidos no Chrome](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. Adicione o site **ADF.Azure.com** , marque a opção **todos os cookies** e salve. 

    ![Permitir todos os cookies do site de UX do ADF](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. Atualize o UX do ADF e tente novamente.

### <a name="microsoft-edge"></a>Microsoft Edge

1. Visite **Edge://Settings/Content/cookies** em seu navegador.
1. Verifique se permitir que os **sites salvem e leiam dados de cookie** estão habilitados e se a opção **bloquear cookies de** terceiros está desabilitada 

    ![Permitir todos os cookies no Edge](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. Atualize o UX do ADF e tente novamente.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Permitir que somente a UX do ADF use cookies

Se você não quiser permitir todos os cookies, você pode opcionalmente permitir apenas o ADF UX:

1. Visite **Edge://Settings/Content/cookies**.
1. Na seção **permitir** , selecione **Adicionar** e adicionar site **ADF.Azure.com** . 

    ![Adicionar a UX do ADF a sites permitidos no Edge](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. Atualize o UX do ADF e tente novamente.

## <a name="connection-failed-on-adf-ux"></a>Falha na conexão na UX do ADF

Às vezes, você veria erros de "falha na conexão" na UX do ADF semelhante à captura de tela abaixo, depois de clicar em **testar conexão**, **Visualizar**, etc.

![Falha na conexão](media/data-factory-ux-troubleshoot-guide/connection-failed.png)

Nesse caso, você pode primeiro tentar a mesma operação com o modo de navegação InPrivate em seu navegador.

Se ainda não estiver funcionando, no navegador, pressione F12 para abrir **ferramentas para desenvolvedores**. Vá para a guia **rede** , marque **desabilitar cache**, repita a operação com falha e localize a solicitação com falha (em vermelho).

![Solicitação com falha](media/data-factory-ux-troubleshoot-guide/failed-request.png)

Em seguida, localize o **nome do host** (nesse caso, **DPNORTHEUROPE.svc.DATAFACTORY.Azure.com**) da **URL de solicitação** da solicitação com falha.

Digite o **nome do host** diretamente na barra de endereços do seu navegador. Se você vir 404 no navegador, isso geralmente significa que o lado do cliente está OK e o problema está no lado do serviço do ADF. Arquivo um tíquete de suporte com a **ID da atividade** da mensagem de erro de UX do ADF.

![Recurso não encontrado](media/data-factory-ux-troubleshoot-guide/status-code-404.png)

Caso contrário, você verá um erro semelhante abaixo no navegador, isso geralmente significa que você tem algum problema no lado do cliente. Siga as etapas de solução de problemas.

![Erro no lado do cliente](media/data-factory-ux-troubleshoot-guide/client-side-error.png)

Abra o **prompt de comando** e digite **nslookup dpnortheurope.svc.datafactory.Azure.com**. Uma resposta normal deve se parecer com A seguinte:

![Resposta de comando 1](media/data-factory-ux-troubleshoot-guide/command-response-1.png)

Se você vir uma resposta DNS normal, entre em contato com o suporte de ti local para verificar as configurações de firewall em se a conexão HTTPS com esse nome de host está bloqueada ou não. Se o problema não puder ser resolvido, execute um tíquete de suporte com a **ID da atividade** da mensagem de erro do ADF UX.

Se você vir algo mais do que isso, isso geralmente significa que há algo errado com o servidor DNS ao resolver o nome DNS. Geralmente, alterar o ISP (provedor de serviços de Internet) ou DNS (por exemplo, para o 8.8.8.8 de DNS do Google) pode ser uma solução alternativa possível para tentar. Se o problema persistir, você poderá experimentar ainda mais **nslookup datafactory.Azure.com** e **nslookup Azure.com** para ver em qual nível sua resolução de DNS falhou e enviar todas as informações para o suporte de ti local ou seu ISP para solução de problemas. Se eles acreditarem que o problema ainda está no lado da Microsoft, execute um tíquete de suporte com a **ID da atividade** da mensagem de erro de UX do ADF.

![Resposta do comando 2](media/data-factory-ux-troubleshoot-guide/command-response-2.png)

## <a name="change-linked-service-type-in-datasets"></a>Alterar o tipo de serviço vinculado em conjuntos de os

O conjunto de arquivos de formato de arquivo pode ser usado com todos os conectores baseados em arquivo, por exemplo, você pode configurar um conjunto de parquet no blob do Azure ou Azure Data Lake Storage Gen2. Observe que cada conector dá suporte a diferentes conjuntos de configurações relacionadas ao armazenamento de dados na atividade e com um modelo de aplicativo diferente. 

Na interface do usuário de criação do ADF, quando você usa um conjunto de arquivos de formato de arquivo em uma atividade, incluindo copiar, Pesquisar, GetMetadata, excluir atividades – e no conjunto de texto, você deseja apontar para um serviço vinculado de um tipo diferente do atual (por exemplo, alternar do sistema de arquivos para ADLS Gen2), você verá a seguinte mensagem de aviso. Para ter certeza de que ele é um comutador limpo, após seu consentimento, os pipelines e as atividades, que fazem referência a esse conjunto de dados, serão modificados para usar o novo tipo também, e quaisquer configurações existentes do repositório, que são incompatíveis com o novo tipo, serão apagadas, pois não se aplicam mais.

Para saber mais sobre quais configurações de armazenamento de dados com suporte para cada conector, você pode ir para o artigo do conector correspondente-> Propriedades da atividade de cópia para ver a lista detalhada de propriedades. Consulte [Amazon S3](connector-amazon-simple-storage-service.md), [blob do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [armazenamento de arquivos do Azure](connector-azure-file-storage.md), [sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

![Mensagem de aviso](media/data-factory-ux-troubleshoot-guide/warning-message.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda com a solução de problemas, experimente estes recursos:

* [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Solicitações de recurso do Data Factory](https://feedback.azure.com/forums/270578-data-factory)
* [Fórum do Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Informações do Twitter sobre o Data Factory](https://twitter.com/hashtag/DataFactory)
* [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/)
* [Página de P e R da Microsoft](/answers/topics/azure-data-factory.html)