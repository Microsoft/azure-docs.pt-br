---
title: Tutorial`:` Usar uma identidade gerenciada para acessar o Azure Data Lake Storage – Linux – Azure AD
description: Um tutorial que mostra como usar uma identidade gerenciada atribuída pelo sistema da VM do Linux para acessar o Azure Data Lake Storage.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: d465419dfe36fd5dd67abdef22a6f54fba69a98e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89267455"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-data-lake-store"></a>Tutorial: Usar uma identidade gerenciada atribuída pelo sistema da VM do Linux para acessar o Azure Data Lake Storage

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra como usar uma identidade gerenciada atribuída pelo sistema para uma VM (máquina virtual) do Linux para acessar o Azure Data Lake Storage. Você aprenderá como: 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Conceder à sua VM acesso ao Azure Data Lake Store.
> * Obtenha um token de acesso usando a identidade gerenciada atribuída pelo sistema da VM para acessar o Azure Data Lake Storage.

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-access"></a>Conceder acesso

Esta seção agora mostra como permitir acesso à sua VM a arquivos e pastas no Azure Data Lake Storage. Para esta etapa, você pode usar uma instância Data Lake Store existente ou criar uma nova. Para criar uma nova instância Data Lake Store usando o portal do Azure, siga o [Guia de início rápido do Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md). Também há guias de início rápido que usam a CLI do Azure e o Azure PowerShell na [documentação do Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md).

No Data Lake Storage, crie uma nova pasta e conceda permissão à identidade gerenciada atribuída pelo sistema da VM do Linux para ler, gravar e executar arquivos nesta pasta:

1. No portal do Azure, selecione **Data Lake Store** no painel esquerdo.
2. Selecione a instância Data Lake Store que você deseja usar.
3. Selecione **Data Explorer** na barra de comandos.
4. A pasta raiz da instância Data Lake Store é selecionada. Selecione **Acesso** na barra de comandos.
5. Selecione **Adicionar**.  Na caixa **Selecionar**, insira o nome da sua VM, por exemplo, **DevTestVM**. Selecione sua VM nos resultados da pesquisa e clique em **Selecionar**.
6. Clique em **Selecionar permissões**.  Selecione **Leitura** e **Execução**, adicione a **Esta pasta** e adicione como **Somente permissão de acesso**. Selecione **OK**.  A permissão deverá ser adicionada com êxito.
7. Feche o painel **Acesso**.
8. Para este tutorial, crie uma nova pasta. Selecione **Nova pasta** na barra de comandos e dê um nome para ela, por exemplo **TestFolder**.  Selecione **OK**.
9. Selecione a pasta que você criou e selecione **Acesso** na barra de comandos.
10. Semelhante à etapa 5, selecione **Adicionar**. Na caixa **Selecionar**, insira o nome da sua VM. Selecione sua VM nos resultados da pesquisa e clique em **Selecionar**.
11. Semelhante à etapa 6, selecione **Selecionar Permissões**. Selecione **Leitura**, **Gravação** e **Execução**, adicione **Esta pasta** e adicione como **Uma entrada de permissão de acesso e uma entrada de permissão padrão**. Selecione **OK**.  A permissão deverá ser adicionada com êxito.

Identidades gerenciadas para recursos do Azure agora podem executar todas as operações em arquivos na pasta que você criou. Para saber mais sobre como gerenciar acesso ao Data Lake Store, confira [Controle de acesso no Data Lake Store](../../data-lake-store/data-lake-store-access-control.md).

## <a name="get-an-access-token"></a>Obter um token de acesso 

Esta seção mostra como obter um token de acesso e chamar o sistema de arquivos do Data Lake Storage. O Azure Data Lake Storage tem suporte nativo para autenticação do Azure AD, de modo que pode aceitar diretamente os tokens de acesso obtidos usando identidades gerenciadas para recursos do Azure. Para autenticar para o sistema de arquivos do Data Lake Store, você envia um token de acesso emitido pelo Azure AD para o ponto de extremidade do sistema de arquivos do Data Lake Store. O token de acesso está localizado em um cabeçalho de autorização no formato "Portador \<ACCESS_TOKEN_VALUE\>".  Para saber mais sobre o suporte do Data Lake Store à autenticação do Azure AD, confira [Autenticação com o Data Lake Store usando o Azure Active Directory](../../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).

Neste tutorial, você se autentica na API REST do sistema de arquivos do Data Lake Store usando o cURL para fazer solicitações REST.

> [!NOTE]
> Os SDKs do cliente para o sistema de arquivos do Data Lake Storage ainda não têm suporte para identidades gerenciadas para recursos do Azure.

Para concluir essas etapas, você precisará do cliente SSH. Se você estiver usando o Windows, poderá usar o cliente SSH no [Subsistema do Windows para Linux](/windows/wsl/about). Se precisar de ajuda para configurar as chaves do cliente SSH, confira [Como usar chaves SSH com o Windows no Azure](../../virtual-machines/linux/ssh-from-windows.md) ou [Como criar e usar um par de chaves SSH pública e privada para VMs Linux no Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. No portal, navegue até sua VM Linux. Em **Visão geral**, selecione **Conectar**.  
2. Conecte-se à VM usando um cliente SSH de sua escolha. 
3. Na janela de terminal, usando cURL, faça uma solicitação para as identidades gerenciadas locais para o ponto de extremidade de recursos do Azure para obter um token de acesso para o sistema de arquivos do Data Lake Storage. O identificador de recursos para Data Lake Storage é `https://datalake.azure.net/`.  É importante incluir a barra à direita no identificador de recursos.
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -H Metadata:true   
   ```
    
   Uma resposta bem-sucedida retorna o token de acesso que você usará para autenticar-se no Data Lake Store:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. Usando o cURL, faça uma solicitação para o ponto de extremidade REST do sistema de arquivos do Data Lake Store para listar as pastas na pasta raiz. Esta é uma maneira simples de verificar se tudo está configurado corretamente. Copie o valor do token de acesso da etapa anterior. É importante que a cadeia de caracteres “Bearer” no cabeçalho de autorização tenha um “B” maiúsculo. Você pode encontrar o nome da sua instância do Data Lake Store na seção **Visão geral** do painel **Data Lake Store** no Portal do Azure.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Uma resposta bem-sucedida tem a seguinte aparência:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Agora você pode tentar carregar um arquivo para sua instância do Data Lake Store. Primeiramente, crie um arquivo para ser carregado.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. Usando o cURL, faça uma solicitação para o ponto de extremidade REST do sistema de arquivos do Data Lake Store para carregar o arquivo para a pasta que você criou anteriormente. O carregamento envolve um redirecionamento, e o cURL segue o redirecionamento automaticamente. 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    Uma resposta bem-sucedida tem a seguinte aparência:

   ```bash
   HTTP/1.1 100 Continue
   HTTP/1.1 307 Temporary Redirect
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: 756f6b24-0cca-47ef-aa12-52c3b45b954c
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
       
   HTTP/1.1 100 Continue
       
   HTTP/1.1 201 Created
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: af5baa07-3c79-43af-a01a-71d63d53e6c4
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
   ```

Usando outras APIs do sistema de arquivos do Data Lake Store, é possível acrescentar aos arquivos, baixar arquivos e muito mais.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar uma identidade gerenciada atribuída pelo sistema da VM do Linux para acessar um Azure Data Lake Storage. Para saber mais sobre o Azure Data Lake Store, confira:

> [!div class="nextstepaction"]
>[Repositório Azure Data Lake](../../data-lake-store/data-lake-store-overview.md)