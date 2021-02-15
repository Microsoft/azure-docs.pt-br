---
title: Copiar ou mover dados para o armazenamento do Azure usando AzCopy v10 | Microsoft Docs
description: AzCopy é um utilitário de linha de comando que você pode usar para copiar dados para, de ou entre as contas de armazenamento. Este artigo ajuda você a baixar o AzCopy, conectar-se à sua conta de armazenamento e, em seguida, transferir arquivos.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: bec221f762213fa8576ccef30e9b6a27aa708f01
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944140"
---
# <a name="get-started-with-azcopy"></a>Introdução ao AzCopy

AzCopy é um utilitário de linha de comando que você pode usar para copiar blobs ou arquivos de ou para uma conta de armazenamento. Este artigo ajuda você a baixar o AzCopy, conectar-se à sua conta de armazenamento e, em seguida, transferir arquivos.

> [!NOTE]
> AzCopy **V10** é a versão com suporte no momento do AzCopy.
>
> Se você precisar usar uma versão anterior do AzCopy, consulte a seção [usar a versão anterior do AzCopy](#previous-version) deste artigo.

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>Baixar o AzCopy

Primeiro, baixe o arquivo executável AzCopy V10 em qualquer diretório em seu computador. AzCopy V10 é apenas um arquivo executável, portanto, não há nada a ser instalado.

- [Windows de 64 bits](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows de 32 bits](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Esses arquivos são compactados como um arquivo zip (Windows e Mac) ou um arquivo tar (Linux). Para baixar e descompactar o arquivo tar no Linux, consulte a documentação para sua distribuição do Linux.

> [!NOTE]
> Se você quiser copiar dados de e para o serviço de [armazenamento de tabelas do Azure](../tables/table-storage-overview.md) , instale o [AzCopy versão 7,3](https://aka.ms/downloadazcopynet).


## <a name="run-azcopy"></a>Executar AzCopy

Para sua conveniência, considere adicionar o local do diretório do executável AzCopy ao caminho do sistema para facilitar o uso. Dessa forma, você pode digitar `azcopy` de qualquer diretório em seu sistema.

Se você optar por não adicionar o diretório AzCopy ao seu caminho, terá que alterar os diretórios para o local do seu executável AzCopy e digitar `azcopy` ou `.\azcopy` em prompts de comando do Windows PowerShell.

Para ver uma lista de comandos, digite `azcopy -h` e pressione a tecla Enter.

Para saber mais sobre um comando específico, apenas inclua o nome do comando (por exemplo: `azcopy list -h` ).

> [!div class="mx-imgBorder"]
> ![Ajuda embutida](media/storage-use-azcopy-v10/azcopy-inline-help.png)


Para encontrar a documentação de referência detalhada para cada comando e parâmetro de comando, consulte [azcopy](storage-ref-azcopy.md)

> [!NOTE] 
> Como proprietário de sua conta de armazenamento do Azure, você não recebe automaticamente permissões para acessar dados. Antes de poder fazer qualquer coisa significativa com o AzCopy, você precisa decidir como fornecerá credenciais de autorização para o serviço de armazenamento. 

<a id="choose-how-youll-provide-authorization-credentials"></a>

## <a name="authorize-azcopy"></a>Autorizar AzCopy

Você pode fornecer credenciais de autorização usando o Azure Active Directory (AD) ou usando um token SAS (assinatura de acesso compartilhado).

Use esta tabela como um guia:

| Tipo de armazenamento | Método de autorização atualmente com suporte |
|--|--|
|**Armazenamento de Blobs** | Azure AD e SAS |
|**Armazenamento de BLOBs (namespace hierárquico)** | Azure AD e SAS |
|**Armazenamento de arquivos** | Somente SAS |

#### <a name="option-1-use-azure-active-directory"></a>Opção 1: usar Azure Active Directory

Essa opção está disponível somente para armazenamento de BLOBs. Usando Azure Active Directory, você pode fornecer credenciais uma vez, em vez de ter que acrescentar um token SAS a cada comando.  

> [!NOTE]
> Na versão atual, se você planeja copiar BLOBs entre contas de armazenamento, precisará acrescentar um token SAS a cada URL de origem. Você pode omitir o token SAS somente da URL de destino. Para obter exemplos, consulte [copiar BLOBs entre contas de armazenamento](#transfer-data).

Para autorizar o acesso usando o Azure AD, consulte [autorizar o acesso a BLOBs com AzCopy e Azure Active Directory (Azure AD)](storage-use-azcopy-authorize-azure-active-directory.md).

#### <a name="option-2-use-a-sas-token"></a>Opção 2: usar um token SAS

Você pode acrescentar um token SAS a cada URL de origem ou de destino que usa em seus comandos AzCopy.

Esse comando de exemplo copia recursivamente os dados de um diretório local para um contêiner de BLOB. Um token SAS fictício é acrescentado ao final da URL do contêiner.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Para saber mais sobre tokens SAS e como obter um, consulte [usando assinaturas de acesso compartilhado (SAS)](./storage-sas-overview.md).

<a id="transfer-data"></a>

## <a name="transfer-data"></a>Transferir dados

Depois de ter autorizado sua identidade ou obtido um token SAS, você pode começar a transferir dados.

Para encontrar comandos de exemplo, consulte qualquer um desses artigos.

| Serviço | Artigo |
|--------|-----------|
|Armazenamento do Blobs do Azure |[Carregar arquivos no armazenamento de BLOBs do Azure](storage-use-azcopy-blobs-upload.md)<br><br>[Baixar BLOBs do armazenamento de BLOBs do Azure](storage-use-azcopy-blobs-download.md)<br><br>[Copiar BLOBs entre contas de armazenamento do Azure](storage-use-azcopy-blobs-copy.md)<br><br>[Sincronizar com o armazenamento de BLOBs do Azure](storage-use-azcopy-blobs-synchronize.md)|
|Arquivos do Azure |[Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)|
|Amazon S3|[Transferir dados com o AzCopy e os buckets do Amazon S3](storage-use-azcopy-s3.md)|
|Armazenamento Azure Stack|[Transferir dados com AzCopy e armazenamento de Azure Stack](/azure-stack/user/azure-stack-storage-transfer#azcopy)|

## <a name="use-in-a-script"></a>Usar em um script

#### <a name="obtain-a-static-download-link"></a>Obter um link de download estático

Ao longo do tempo, o [link de download](#download-and-install-azcopy) do AzCopy apontará para novas versões do AzCopy. Se o script baixar AzCopy, o script poderá parar de funcionar se uma versão mais recente do AzCopy modificar os recursos dependentes do seu script.

Para evitar esses problemas, obtenha um link estático (inalterável) para a versão atual do AzCopy. Dessa forma, o script baixará a mesma versão exata do AzCopy cada vez que for executado.

Para obter o link, execute este comando:

| Sistema operacional  | Comando |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> Para o Linux, `--strip-components=1` no `tar` comando Remove a pasta de nível superior que contém o nome da versão e, em vez disso, extrai o binário diretamente para a pasta atual. Isso permite que o script seja atualizado com uma nova versão do `azcopy` atualizando apenas a `wget` URL.

A URL aparece na saída deste comando. O script pode então baixar o AzCopy usando essa URL.

| Sistema operacional  | Comando |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

#### <a name="escape-special-characters-in-sas-tokens"></a>Caracteres especiais de escape em tokens SAS

Em arquivos em lotes que têm a `.cmd` extensão, você terá que escapar os `%` caracteres que aparecem nos tokens SAS. Você pode fazer isso adicionando um caractere adicional ao `%` lado `%` dos caracteres existentes na cadeia de caracteres do token SAS.

#### <a name="run-scripts-by-using-jenkins"></a>Executar scripts usando Jenkins

Se você planeja usar o [Jenkins](https://jenkins.io/) para executar scripts, certifique-se de colocar o comando a seguir no início do script.

```
/usr/bin/keyctl new_session
```

## <a name="use-in-azure-storage-explorer"></a>Usar no Gerenciador de Armazenamento do Azure

[Gerenciador de armazenamento](https://azure.microsoft.com/features/storage-explorer/) usa AzCopy para executar todas as suas operações de transferência de dados. Você pode usar [Gerenciador de armazenamento](https://azure.microsoft.com/features/storage-explorer/) se quiser aproveitar as vantagens de desempenho do AzCopy, mas preferir usar uma interface gráfica do usuário em vez da linha de comando para interagir com seus arquivos.

Gerenciador de Armazenamento usa sua chave de conta para executar operações, portanto, depois de entrar no Gerenciador de Armazenamento, você não precisará fornecer credenciais de autorização adicionais.

<a id="previous-version"></a>

## <a name="configure-optimize-and-fix"></a>Configurar, otimizar e corrigir

Consulte [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="use-a-previous-version"></a>Usar uma versão anterior

Se você precisar usar a versão anterior do AzCopy, consulte um dos links a seguir:

- [AzCopy no Windows (v8)](/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy no Linux (v7)](/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>Próximas etapas

Se você tiver dúvidas, problemas ou comentários gerais, envie-os [na página do GitHub](https://github.com/Azure/azure-storage-azcopy) .
