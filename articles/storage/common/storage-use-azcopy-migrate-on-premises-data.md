---
title: 'Tutorial: migrar dados locais para o Armazenamento do Azure usando o AzCopy | Microsoft Docs'
description: Neste tutorial, você usará o AzCopy para migrar ou copiar dados de e para blobs, tabelas e arquivos. Migre dados com facilidade do armazenamento local para o Armazenamento do Azure.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 682394329205d74859c1af0c0a68a37539da7872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98881021"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-with-azcopy"></a>Tutorial: migrar dados do local para a nuvem com o AzCopy

O AzCopy é uma ferramenta de linha de comando para copiar dados para ou do Armazenamento de Blobs do Azure, Arquivos do Azure e Armazenamento de Tabelas do Azure, usando comandos simples. Os comandos são projetados para desempenho ideal. Ao usar AzCopy, você pode copiar dados entre um sistema de arquivos e uma conta de armazenamento, ou entre contas de armazenamento. O AzCopy pode ser usado para copiar dados localmente para uma conta de armazenamento.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento. 
> * Use o AzCopy para carregar todos os seus dados.
> * Modifique os dados para fins de teste.
> * Crie uma tarefa agendada ou trabalho de Cron para identificar novos arquivos a serem carregados.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, baixe a última versão do AzCopy. Confira [Introdução ao AzCopy](storage-use-azcopy-v10.md).

Se você está no Windows, precisa ter o [Schtasks](/windows/win32/taskschd/schtasks), já que este tutorial o usa para agendar uma tarefa. Os usuários do Linux usarão o comando crontab.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Criar um contêiner

A primeira etapa é criar um contêiner, já que os blobs sempre devem ser carregados em um contêiner. Contêineres são usados como uma forma de organizar grupos de blobs, assim como pastas são usadas em um computador para organizar arquivos.

Execute estas etapas para criar um contêiner:

1. Selecione o botão **Contas de armazenamento** na página principal e, em seguida, selecione a conta de armazenamento que você criou.
2. Selecione **Blobs** em **Serviços** e, em seguida, selecione **Contêiner**.

   ![Captura de tela mostrando a criação de contêiner](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Nomes de contêiner devem começar com uma letra ou número. Eles podem conter apenas letras, números e o caractere de hífen (-). Para obter mais regras sobre como nomear blobs e contêineres, confira [Nomenclatura e referência de contêineres, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="download-azcopy"></a>Baixar o AzCopy

Baixe o arquivo executável do AzCopy V10.

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Coloque o arquivo do AzCopy em qualquer lugar do computador. Adicione a localização do arquivo à variável do caminho do sistema, de modo que você possa referenciar esse arquivo executável em qualquer pasta do computador.

## <a name="authenticate-with-azure-ad"></a>Autenticar com o Azure AD

Primeiro, atribua a função [Colaborador de Dados do Blob de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor) à sua identidade. Confira [Usar o portal do Azure para atribuir uma função do Azure para acesso aos dados de blob e de fila](./storage-auth-aad-rbac-portal.md).

Em seguida, abra um prompt de comando, digite o comando a seguir e pressione a tecla ENTER.

```azcopy
azcopy login
```

Esse comando retorna um código de autenticação e a URL de um site. Abra o site, forneça o código e, em seguida, escolha o botão **Avançar**.

![Captura de tela mostrando o prompt de logon](media/storage-use-azcopy-v10/azcopy-login.png)

Uma janela de entrada será exibida. Nessa janela, entre em sua conta do Azure usando suas credenciais de conta do Azure. Depois de entrar com êxito, feche a janela do navegador e comece a usar o AzCopy.

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>Carregar conteúdo de uma pasta para o Armazenamento de Blobs

Você pode usar AzCopy para carregar todos os arquivos em uma pasta para Armazenamento de Blobs em [Windows](./storage-use-azcopy-v10.md) ou [Linux](./storage-use-azcopy-v10.md). Para carregar todos os blobs em uma pasta, digite o seguinte comando do AzCopy:

```AzCopy
azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive=true
```

* Substitua o espaço reservado `<local-folder-path>` pelo caminho para uma pasta que contenha arquivos (por exemplo: `C:\myFolder` ou `/mnt/myFolder`).

* Substitua o espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

* Substitua o espaço reservado `<container-name>` pelo nome do contêiner criado.

Para carregar o conteúdo do diretório especificado no Armazenamento de Blobs recursivamente, especifique a opção `--recursive`. Quando você executa o AzCopy com essa opção, todas as subpastas e seus arquivos são carregados também.

## <a name="upload-modified-files-to-blob-storage"></a>Carregar os arquivos modificados no Armazenamento de Blobs

Use o AzCopy para carregar arquivos com base na hora da última modificação. 

Para testar isso, modifique ou crie novos arquivos em seu diretório de origem para fins de teste. Em seguida, use o comando `sync` do AzCopy.

```AzCopy
azcopy sync "<local-folder-path>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true
```

* Substitua o espaço reservado `<local-folder-path>` pelo caminho para uma pasta que contenha arquivos (por exemplo: `C:\myFolder` ou `/mnt/myFolder`).

* Substitua o espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

* Substitua o espaço reservado `<container-name>` pelo nome do contêiner criado.

Para saber mais sobre o comando `sync`, confira [Sincronizar arquivos](./storage-use-azcopy-v10.md#transfer-data).

## <a name="create-a-scheduled-task"></a>Criar uma tarefa agendada

Você pode criar uma tarefa agendada ou trabalho de Cron que execute um script de comando AzCopy. O script identifica e carrega novos dados locais para o armazenamento em nuvem em um intervalo de tempo específico.

Copie o comando AzCopy para um editor de texto. Atualize os valores de parâmetro do comando AzCopy para os valores apropriados. Salve o arquivo como `script.sh` (Linux) ou `script.bat` (Windows) para AzCopy. 

Esses exemplos pressupõem que a pasta seja chamada `myFolder`, que o nome de sua conta de armazenamento seja `mystorageaccount` e que o nome do contêiner seja `mycontainer`.

> [!NOTE]
> O exemplo do Linux acrescenta um token SAS. Você precisará fornecer um no comando. A versão atual do AzCopy V10 não dá suporte à autorização do Azure AD em trabalhos do Cron.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
azcopy sync "/mnt/myfiles" "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-05-30T06:57:40Z&st=2019-05-29T22:57:40Z&spr=https&sig=BXHippZxxx54hQn%2F4tBY%2BE2JHGCTRv52445rtoyqgFBUo%3D" --recursive=true
```

# <a name="windows"></a>[Windows](#tab/windows)

```bash
azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true
```

---

Neste tutorial, [Schtasks](/windows/win32/taskschd/schtasks) é usado para criar uma tarefa agendada no Windows. O comando [Crontab](http://crontab.org/) é usado para criar um trabalho Cron no Linux.

 **Schtasks** permite que um administrador crie, exclua, consulte, altere, execute e finalize as tarefas agendadas em um computador local ou remoto. **Cron** permite que os usuários do Linux e Unix executem comandos ou scripts em uma data e hora especificadas usando [expressões Cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="linux"></a>[Linux](#tab/linux)

Para criar um trabalho Cron no Linux, digite o seguinte comando em um terminal:

```bash
crontab -e
*/5 * * * * sh /path/to/script.sh
```

Especificar a expressão Cron `*/5 * * * *` no comando indica que o script de shell `script.sh` deve ser executado a cada cinco minutos. Você pode agendar o script para ser executado em um momento específico diariamente, mensalmente ou anualmente. Para saber mais sobre como definir a data e hora para a execução do trabalho, consulte [expressões Cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="windows"></a>[Windows](#tab/windows)

Para criar uma tarefa agendada no Windows, digite o seguinte comando no prompt de comando ou no PowerShell:

Esse exemplo pressupõe que o script esteja localizado na unidade raiz do computador, mas o script pode estar em qualquer lugar desejado.

```cmd
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\script.bat
```

O comando usa:
- O parâmetro `/SC` para especificar um agendamento a ser executado a cada um minuto.
- O parâmetro `/MO` para especificar um intervalo de cinco minutos.
- O parâmetro `/TN` para especificar o nome da tarefa.
- O parâmetro `/TR` para especificar o caminho para o arquivo `script.bat`.

Para saber mais sobre como criar uma tarefa agendada no Windows, consulte [Schtasks](/previous-versions/orphan-topics/ws.10/cc772785(v=ws.10)#BKMK_minutes).

---

Para validar que o trabalho cron/tarefa agendada seja executada corretamente, crie novos arquivos no seu diretório `myFolder`. Aguarde cinco minutos para confirmar que os novos arquivos foram carregados para sua conta de armazenamento. Vá para o diretório de log para exibir os logs de saída da tarefa agendada ou do trabalho Cron.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as maneiras de mover dados locais para o Armazenamento do Azure e vice-versa, siga este link:

* [Mover dados de e para o Armazenamento do Azure](./storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).  

Para obter mais informações sobre o AzCopy, confira um destes artigos:

* [Introdução ao AzCopy](storage-use-azcopy-v10.md)

* [Transferir dados com o AzCopy e o Armazenamento de Blobs](./storage-use-azcopy-v10.md#transfer-data)

* [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)

* [Transferir dados com o AzCopy e os buckets do Amazon S3](storage-use-azcopy-s3.md)
 
* [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)
