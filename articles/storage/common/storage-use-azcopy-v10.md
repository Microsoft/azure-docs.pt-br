---
title: Copiar ou mover dados para o armazenamento do Azure usando o AzCopy v10 | Microsoft Docs
description: O AzCopy é um utilitário de linha de comando que você pode usar para copiar dados para, de ou entre contas de armazenamento. Este artigo ajuda você a baixar o AzCopy, conecte-se à sua conta de armazenamento e, em seguida, transferir arquivos.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 94aca33b2f12c1c39297221a856296dcca052b0f
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565795"
---
# <a name="get-started-with-azcopy"></a>Introdução ao AzCopy

O AzCopy é um utilitário de linha de comando que você pode usar para copiar blobs ou arquivos de ou para uma conta de armazenamento. Este artigo ajuda você a baixar o AzCopy, conecte-se à sua conta de armazenamento e, em seguida, transferir arquivos.

> [!NOTE]
> AzCopy **V10** é a versão com suporte no momento do AzCopy.
>
> Se você precisar usar o AzCopy **v8.1**, consulte o [usar a versão anterior do AzCopy](#previous-version) seção deste artigo.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Baixar o AzCopy

Primeiro, baixe o arquivo executável do AzCopy V10 para qualquer diretório em seu computador. 

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

> [!NOTE]
> Se você deseja copiar dados de e para seus [o armazenamento de tabelas](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) de serviço e, em seguida, instale [AzCopy versão 7.3](https://aka.ms/downloadazcopynet).

## <a name="run-azcopy"></a>Executar o AzCopy

Para sua conveniência, considere adicionar o local do diretório do executável AzCopy ao caminho do sistema para facilidade de uso. Dessa forma, você pode digitar `azcopy` de qualquer diretório em seu sistema.

Se você optar por não adicionar o diretório do AzCopy ao caminho, você terá que altere os diretórios para o local do seu arquivo executável do AzCopy e digite `azcopy` ou `.\azcopy` nos prompts de comando do Windows PowerShell.

Para ver uma lista de comandos, digite `azcopy -h` e, em seguida, pressione a tecla ENTER.

Para saber mais sobre um comando específico, basta incluir o nome do comando (por exemplo: `azcopy list -h`).

![Ajuda embutida](media/storage-use-azcopy-v10/azcopy-inline-help.png)

> [!NOTE] 
> Como um proprietário de sua conta de armazenamento do Azure, você não é automaticamente atribuídas permissões para acessar dados. Antes de poder fazer qualquer coisa significativa com o AzCopy, você precisa decidir como você vai fornecer as credenciais de autorização para o serviço de armazenamento. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>Escolha como você irá fornecer as credenciais de autorização

Você pode fornecer credenciais de autorização usando o Azure Active Directory (AD) ou usando um token de assinatura de acesso compartilhado (SAS).

Use esta tabela como um guia:

| Tipo de armazenamento | Método com suporte no momento de autorização |
|--|--|
|**Armazenamento de Blobs** | Azure AD & SAS |
|**Armazenamento de BLOBs (namespace referiremos)** | Azure AD & SAS |
|**Armazenamento de arquivos** | Somente a SAS |

### <a name="option-1-use-azure-ad"></a>Opção 1: Usar o Azure AD

Usando o Azure AD, você pode fornecer credenciais de uma vez, em vez de precisar acrescentar um token SAS para cada comando.  

O nível de autorização que você precisa se baseia se você planeja carregar arquivos ou apenas baixá-los.

Se você quiser baixar arquivos, em seguida, verifique se que o [leitor de dados de Blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) foi atribuído a sua identidade de usuário ou entidade de serviço. 

> [!NOTE]
> As identidades dos usuários e entidades de serviço são um tipo de *entidade de segurança*, portanto, usaremos o termo *entidade de segurança* para o restante deste artigo.

Se você quiser carregar arquivos, em seguida, verifique se que uma dessas funções foi atribuída à entidade de segurança:

- [Colaborador de dados de blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Proprietário de Dados do Blob de Armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Essas funções podem ser atribuídas à sua identidade em qualquer um desses escopos:

- Contêiner (sistema de arquivos)
- Conta de armazenamento
- Grupo de recursos
- Assinatura

Para saber como verificar e atribuir funções, consulte [conceder acesso a dados do Azure blob e fila com o RBAC no portal do Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE] 
> Tenha em mente que as atribuições de função RBAC podem levar até cinco minutos para propagar.

Você não precisa ter uma dessas funções atribuídas à entidade de segurança, se a entidade de segurança é adicionada à lista de controle de acesso (ACL) do contêiner de destino ou diretório. Na ACL, sua entidade de segurança precisa de permissão de gravação no diretório de destino e permissão de execução no contêiner e cada diretório pai.

Para obter mais informações, consulte [controle de acesso no armazenamento do Azure Data Lake Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-a-user-identity"></a>Autenticar uma identidade de usuário

Depois de verificar que sua identidade de usuário recebeu o nível de autorização necessária, abra um prompt de comando, digite o seguinte comando e, em seguida, pressione a tecla ENTER.

```azcopy
azcopy login
```

Se você pertencer a mais de uma organização, inclua a ID de locatário da organização à qual pertence a conta de armazenamento.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Substitua o `<tenant-id>` espaço reservado com a ID de locatário da organização à qual pertence a conta de armazenamento. Para localizar a ID de locatário, selecione **Azure Active Directory > Propriedades > ID de diretório** no portal do Azure.

Esse comando retorna um código de autenticação e a URL de um site. Abra o site, forneça o código e, em seguida, escolha o botão **Avançar**.

![Criar um contêiner](media/storage-use-azcopy-v10/azcopy-login.png)

Uma janela de entrada será exibida. Nessa janela, entre em sua conta do Azure usando suas credenciais de conta do Azure. Depois de entrar com êxito, feche a janela do navegador e comece a usar o AzCopy.

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>Autenticar uma entidade de serviço

Isso é uma ótima opção se você planeja usar o AzCopy dentro de um script que é executado sem interação do usuário. 

Antes de executar esse script, você precisa entrar interativamente pelo menos uma vez para que você possa fornecer o AzCopy com as credenciais de sua entidade de serviço.  Essas credenciais são armazenadas em um arquivo criptografado e protegido para que seu script não precisa fornecer essas informações confidenciais.

Você pode entrar em sua conta usando um segredo do cliente ou usando a senha de um certificado que está associado com o registro do aplicativo da entidade de serviço. 

Para saber mais sobre como criar entidade de serviço, consulte [como: Usar o portal para criar um aplicativo e uma entidade de serviço do Azure AD que possa acessar recursos](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Para saber mais sobre entidades de serviço em geral, consulte [aplicativo e objetos de entidade de serviço no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

##### <a name="using-a-client-secret"></a>Usando um segredo do cliente

Comece definindo o `AZCOPY_SPA_CLIENT_SECRET` variável de ambiente para o segredo do cliente da entidade de serviço do registro do aplicativo. 

> [!NOTE]
> Certifique-se de definir esse valor em seu prompt de comando e não no ambiente de configurações de variável do sistema operacional. Dessa forma, o valor está disponível apenas para a sessão atual.

Este exemplo mostra como você poderia fazer isso no PowerShell.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Considere usar um prompt, conforme mostrado neste exemplo. Dessa forma, o segredo do cliente não aparecerão no histórico de comandos do seu console. 

Em seguida, digite o seguinte comando e, em seguida, pressione a tecla ENTER.

```azcopy
azcopy login --service-principal --application-id <application-id>
```

Substitua o `<application-id>` espaço reservado com a ID do aplicativo da entidade de serviço do registro do aplicativo.

##### <a name="using-a-certificate"></a>Usando um certificado

Se você preferir usar suas próprias credenciais para autorização, você pode carregar um certificado para o registro do aplicativo e, em seguida, usar esse certificado para fazer logon.

Além de carregar seu certificado para o registro do aplicativo, você também precisará ter uma cópia do certificado salvo para a máquina ou VM onde o AzCopy será executado. Esta cópia do certificado deve estar no. PFX ou. PEM Formatar e deve incluir a chave privada. A chave privada deve ser protegido por senha. Se você estiver usando o Windows e seu certificado existe somente em um repositório de certificados, certifique-se de exportar o certificado para um arquivo PFX (incluindo a chave privada). Para obter diretrizes, consulte [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

Em seguida, defina o `AZCOPY_SPA_CERT_PASSWORD` variável de ambiente para a senha do certificado.

> [!NOTE]
> Certifique-se de definir esse valor em seu prompt de comando e não no ambiente de configurações de variável do sistema operacional. Dessa forma, o valor está disponível apenas para a sessão atual.

Este exemplo mostra como você poderia fazer isso no PowerShell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Em seguida, digite o seguinte comando e, em seguida, pressione a tecla ENTER.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file>
```

Substitua o `<path-to-certificate-file>` espaço reservado com o caminho totalmente qualificado ou relativo para o arquivo de certificado. AzCopy salva o caminho para esse certificado, mas ele não salvar uma cópia do certificado, certifique-se de manter esse certificado no lugar.

> [!NOTE]
> Considere usar um prompt, conforme mostrado neste exemplo. Dessa forma, sua senha não aparecerão no histórico de comandos do seu console. 

### <a name="option-2-use-a-sas-token"></a>Opção 2: Usar um token SAS

Você pode acrescentar um token SAS para cada URL de origem ou destino que usam em seus comandos de AzCopy.

Recursivamente de comando neste exemplo copia dados de um diretório local para um contêiner de blob. Um token SAS fictício é acrescentado ao final das da URL do contêiner.

```azcopy
azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Para saber mais sobre tokens SAS e como obtê-lo, consulte [usando assinaturas de acesso (SAS) compartilhado](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="transfer-files"></a>Transferir arquivos

Depois de autenticado sua identidade ou obtido um token SAS, você pode começar a transferência de arquivos.

Para localizar os comandos de exemplo, consulte qualquer um destes artigos.

- [Transferir dados com o AzCopy e o Armazenamento de Blobs](storage-use-azcopy-blobs.md)

- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)

- [Transferir dados com o AzCopy e os buckets do Amazon S3](storage-use-azcopy-s3.md)

- [Transferir dados com armazenamento AzCopy e o Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>Use o AzCopy em um script

Antes de executar esse script, você precisa entrar interativamente pelo menos uma vez para que você possa fornecer o AzCopy com as credenciais de sua entidade de serviço.  Essas credenciais são armazenadas em um arquivo criptografado e protegido para que seu script não precisa fornecer essas informações confidenciais. Para obter exemplos, consulte o [autenticar a entidade de serviço](#service-principal) seção deste artigo.

Ao longo do tempo, o AzCopy [baixar link](#download-and-install-azcopy) apontará para novas versões do AzCopy. Se seu script baixa AzCopy, o script poderá parar de funcionar se uma versão mais recente do AzCopy modifica os recursos que depende de seu script. 

Para evitar esses problemas, obter um link estático (sem alteração) para a versão atual do AzCopy. Dessa forma, seu script baixa a exatamente a mesma versão do AzCopy cada vez que ele é executado.

Para obter o link, execute este comando:

| Sistema operacional  | Comando |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> Para o Linux, `--strip-components=1` sobre o `tar` comando remove a pasta de nível superior que contém o nome da versão e, em vez disso, extrai o binário diretamente para a pasta atual. Isso permite que o script a ser atualizada com uma nova versão do `azcopy` apenas atualizando o `wget` URL.

A URL é exibida na saída desse comando. Seu script, em seguida, pode baixar o AzCopy usando essa URL.

| Sistema operacional  | Comando |
|--------|-----------|
| **Linux** | `wget -O azcopyv10.tar https://azcopyvnext.azureedge.net/release20190301/azcopy_linux_amd64_10.0.8.tar.gz tar -xf azcopyv10.tar --strip-components=1 ./azcopy` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

## <a name="use-azcopy-in-storage-explorer"></a>Use o AzCopy no Gerenciador de armazenamento

Se você deseja aproveitar as vantagens de desempenho do AzCopy, mas você prefere usar o Gerenciador de armazenamento em vez de linha de comando para interagir com seus arquivos, em seguida, habilite o AzCopy no Gerenciador de armazenamento. 

No Gerenciador de armazenamento, escolha **versão prévia**->**Use AzCopy para Download e Upload do Blob aprimorado**.

![Habilitar o AzCopy como um mecanismo de transferência no Gerenciador de armazenamento do Azure](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> Você não precisa habilitar essa configuração se você tiver habilitado um namespace hierárquico em sua conta de armazenamento. Isso ocorre porque o Gerenciador de armazenamento usa automaticamente o AzCopy em contas de armazenamento que têm um namespace hierárquico.  

O Gerenciador de armazenamento usa sua chave de conta para executar operações, portanto, depois de entrar no Gerenciador de armazenamento, você não precisará fornecer credenciais de autorização adicional.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>Usar a versão anterior do AzCopy

Se você precisar usar a versão anterior do AzCopy (AzCopy v8.1), consulte um dos links a seguir:

- [AzCopy no Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy no Linux (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configurar, otimizar e solucionar problemas de AzCopy

Consulte [configurar, otimizar e solucionar problemas de AzCopy](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>Próximas etapas

Se você tiver dúvidas, problemas ou comentários gerais, enviá-los [no GitHub](https://github.com/Azure/azure-storage-azcopy) página.
