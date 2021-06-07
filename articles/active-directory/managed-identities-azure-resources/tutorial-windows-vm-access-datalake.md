---
title: Tutorial`:` Usar uma identidade gerenciada para acessar o Azure Data Lake Storage – Windows – Azure AD
description: Um tutorial que mostra como usar uma identidade gerenciada atribuída pelo sistema da VM do Windows para acessar o Azure Data Lake Storage.
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
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd28ee509dc76ea0b2aca9264c591a7176ae2661
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093813"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-data-lake-store"></a>Tutorial: Usar uma identidade gerenciada atribuída pelo sistema da VM do Windows para acessar o Azure Data Lake Storage

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra como usar uma identidade gerenciada atribuída pelo sistema para uma VM (máquina virtual) do Windows para acessar um Azure Data Lake Storage. As Identidades de Serviço Gerenciadas são gerenciadas automaticamente pelo Azure e permitem a você autenticar os serviços que dão suporte à autenticação do Azure AD sem necessidade de inserir as credenciais em seu código. Você aprenderá como:

> [!div class="checklist"]
> * Conceder acesso a um Azure Data Lake Store à sua VM
> * Obter um token de acesso usando a identidade da VM e usá-lo para acessar um Azure Data Lake Store

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento sobre Identidades Gerenciadas. Se você não estiver familiarizado com as identidades gerenciadas para funcionalidades de recursos do Azure, veja esta [visão geral](overview.md). 
- Uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/).
- Permissões de "Proprietário" no escopo apropriado (sua assinatura ou grupo de recursos) para executar a criação de recursos e o gerenciamento de função necessários. Se precisar de ajuda com a atribuição de função, confira [Atribuir funções do Azure para gerenciar o acesso aos recursos de assinatura do Azure](../../role-based-access-control/role-assignments-portal.md).
- Você também precisa de uma máquina virtual do Windows com identidades gerenciadas atribuídas ao sistema habilitadas.
  - Se precisar criar uma máquina virtual para este tutorial, siga o artigo intitulado [Criar uma máquina virtual com a identidade atribuída pelo sistema habilitada](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)




## <a name="enable"></a>Habilitar

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>Conceder acesso

Agora você pode conceder o acesso a arquivos e pastas em um Azure Data Lake Store à sua VM.  Para esta etapa, é possível usar um Data Lake Store existente ou criar um novo.  Para criar um novo Data Lake Store usando o Portal do Azure, siga este [Guia de início rápido do Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md). Também há guias de início rápido que usam a CLI do Azure e o Azure PowerShell na [documentação do Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md).

No seu Data Lake Storage, crie uma nova pasta e conceda à identidade atribuída pelo sistema da VM permissão para ler, gravar e executar arquivos nesta pasta:

1. No Portal do Azure, clique em **Data Lake Store** na navegação à esquerda.
2. Clique no Data Lake Store que você deseja usar para este tutorial.
3. Clique em **Data Explorer** na barra de comandos.
4. A pasta raiz do Data Lake Store é selecionada.  Clique em **Acesso** na barra de comandos.
5. Clique em **Adicionar**.  No campo **Selecionar**, insira o nome da sua VM, por exemplo **DevTestVM**.  Clique para selecionar sua VM nos resultados da pesquisa e escolha **Selecionar**.
6. Clique em **Selecionar permissões**.  Selecione **Leitura** e **Execução**, adicione a **Esta pasta** e adicione como **Somente permissão de acesso**.  Clique em **OK**.  A permissão deverá ser adicionada com êxito.
7. Feche a folha **Acesso**.
8. Para este tutorial, crie uma nova pasta.  Clique em **Nova pasta** na barra de comandos e dê um nome para a pasta, por exemplo **TestFolder**.  Clique em **OK**.
9. Clique na pasta que você criou e escolha **Acesso** na barra de comandos.
10. Semelhante à etapa 5, clique em **Adicionar** e, no campo **Selecionar**, digite o nome da sua VM, selecione-a e clique em **Selecionar**.
11. Semelhante à etapa 6, clique em **Selecionar permissões**, selecione **Leitura**, **Gravação** e **Execução**, adicione a **Esta pasta** e acrescente como **Uma entrada de permissão de acesso e uma entrada de permissão padrão**.  Clique em **OK**.  A permissão deverá ser adicionada com êxito.

A identidade gerenciada atribuída pelo sistema da VM agora pode executar todas as operações nos arquivos da pasta que você criou.  Para saber mais sobre como gerenciar acesso ao Data Lake Store, leia este artigo sobre [Controle de acesso no Data Lake Store](../../data-lake-store/data-lake-store-access-control.md).

## <a name="access-data"></a>Acessar dados

O Azure Data Lake Storage tem suporte nativo para autenticação do Azure AD, de modo que pode aceitar diretamente os tokens de acesso obtidos usando identidades gerenciadas para recursos do Azure.  Para autenticar para o sistema de arquivos do Data Lake Store, você enviar um token de acesso emitido pelo Azure AD para o ponto de extremidade do sistema de arquivos do Data Lake Store em um cabeçalho de autorização no formato “Bearer <ACCESS_TOKEN_VALUE>”.  Para saber mais sobre o suporte do Data Lake Store à autenticação do Azure AD, leia [Autenticação com o Data Lake Store usando o Azure Active Directory](../../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)

> [!NOTE]
> Os SDKs do cliente de sistema de arquivos do Data Lake Storage SDKs ainda não têm suporte para identidades gerenciadas para recursos do Azure.  Este tutorial será atualizado quando esse suporte for adicionado ao SDK.

Neste tutorial, você se autenticará na API REST do sistema de arquivos do Data Lake Store usando o PowerShell para fazer solicitações REST. Para usar a identidade gerenciada atribuída pelo sistema da VM para autenticação, você precisa fazer as solicitações da VM.

1. No portal, navegue até **Máquinas Virtuais**, acesse a VM Windows e, em **Visão geral**, clique em **Conectar**.
2. Insira o seu **Nome de usuário** e **Senha** que você adicionou quando criou a VM do Windows. 
3. Agora que você criou uma **Conexão de Área de Trabalho Remota** com a máquina virtual, abra o **PowerShell** na sessão remota. 
4. Usando o `Invoke-WebRequest` do PowerShell, faça uma solicitação para as identidades gerenciadas locais para o ponto de extremidade do Azure para obter um token de acesso ao Azure Data Lake Storage.  O identificador de recursos para Data Lake Storage é `https://datalake.azure.net/`.  O Data Lake faz uma correspondência exata no identificador de recursos, por isso a barra à direita é importante.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -Method GET -Headers @{Metadata="true"}
   ```
    
   Converta a resposta de um objeto JSON para um objeto do PowerShell. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Extraia o token de acesso da resposta.
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. Usando 'Invoke-WebRequest' do PowerShell, faça uma solicitação para o ponto de extremidade REST do Data Lake Store para listar as pastas na pasta raiz.  Essa é uma maneira simples de verificar se que tudo está configurado corretamente.  É importante que a cadeia de caracteres “Bearer” no cabeçalho de autorização tenha um “B” maiúsculo.  Você pode encontrar o nome do seu Data Lake Store na seção **Visão geral** da folha Data Lake Store no Portal do Azure.

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   Uma resposta bem-sucedida tem a seguinte aparência:

   ```powershell
   StatusCode        : 200
   StatusDescription : OK
   Content           : {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY", "blockSize":0,"accessTime":1507934941392, "modificationTime":1507944835699,"replication":0, "permission":"770","ow..."
   RawContent        : HTTP/1.1 200 OK
                       Pragma: no-cache
                       x-ms-request-id: b4b31e16-e968-46a1-879a-3474aa7d4528
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict-Transport-Security: ma...
   Forms             : {}
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b4b31e16-e968-46a1-879a-3474aa7d4528],
                       [x-ms-webhdfs-version, 17.04.22.00], [Status, 0x0]...}
   Images            : {}
   InputFields       : {}
   Links             : {}
   ParsedHtml        : System.__ComObject
   RawContentLength  : 556
   ```

6. Agora você pode tentar carregar um arquivo para seu Data Lake Store.  Primeiramente, crie um arquivo para ser carregado.

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. Usando `Invoke-WebRequest` do PowerShell, faça uma solicitação para o ponto de extremidade REST do Data Lake Store para carregar o arquivo para a pasta criada que você criou anteriormente.  Duas etapas são necessárias para essa solicitação.  Na primeira etapa, você faz uma solicitação e obtém um redirecionamento para onde o arquivo deve ser carregado.  Na segunda etapa, ocorre o upload do arquivo propriamente dito.  Lembre-se de definir o nome da pasta e do arquivo corretamente se tiver usado valores diferentes deste tutorial. 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Se você inspecionar o valor de `$HdfsRedirectResponse`, ele deverá se parecer com a seguinte resposta:

   ```powershell
   PS C:\> $HdfsRedirectResponse

   StatusCode        : 307
   StatusDescription : Temporary Redirect
   Content           : {}
   RawContent        : HTTP/1.1 307 Temporary Redirect
                       Pragma: no-cache
                       x-ms-request-id: b7ab492f-b514-4483-aada-4aa0611d12b3
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosn...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b7ab492f-b514-4483-aada-4aa0611d12b3], 
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

   Conclua o upload enviando uma solicitação para o ponto de extremidade de redirecionamento:

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Uma resposta bem-sucedida tem a seguinte aparência:

   ```powershell
   StatusCode        : 201
   StatusDescription : Created
   Content           : {}
   RawContent        : HTTP/1.1 201 Created
                       Pragma: no-cache
                       x-ms-request-id: 1e70f36f-ead1-4566-acfa-d0c3ec1e2307
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, 1e70f36f-ead1-4566-acfa-d0c3ec1e2307],
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

Usando outras APIs do sistema de arquivos do Data Lake Store, é possível acrescentar aos arquivos, baixar arquivos e muito mais.


## <a name="disable"></a>Desabilitar

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar uma identidade gerenciada atribuída pelo sistema para uma máquina virtual do Windows para acessar um Azure Data Lake Storage. Para saber mais sobre o Azure Data Lake Store, confira:

> [!div class="nextstepaction"]
>[Repositório Azure Data Lake](../../data-lake-store/data-lake-store-overview.md)