---
title: Usar Importação/Exportação do Microsoft Azure para transferir dados para Blobs do Azure | Microsoft Docs
description: Saiba como criar trabalhos de importação e exportação no portal do Azure para transferir dados de e para Blobs do Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: e60a58a8d2f1c69728a2d049fe1414ca1997893e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479296"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Usar o serviço de importação/exportação do Microsoft Azure para importar dados do Armazenamento de Blobs

Este artigo fornece instruções passo a passo sobre como usar o serviço de Importação/Exportação do Microsoft Azure para importar com segurança grandes quantidades de dados do Armazenamento de Blobs. Para importar dados para Blobs do Azure, o serviço exige o envio de unidades de disco criptografadas contendo dados para um datacenter do Azure.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar um trabalho de importação para transferir dados ao Armazenamento de Blobs, revise cuidadosamente e preencha a seguinte lista de pré-requisitos para esse serviço. Você deve:

- Ter uma assinatura ativa do Azure que pode ser usada para o serviço de Importação/Exportação.
- Ter pelo menos uma conta do Armazenamento do Azure com um contêiner de armazenamento. Consulte a lista de [Contas de armazenamento e tipos de armazenamento com suporte para o serviço de Importação/Exportação](storage-import-export-requirements.md). 
    - Para obter informações sobre como criar uma nova conta de armazenamento, consulte [Como criar uma conta de armazenamento](storage-quickstart-create-account.md). 
    - Para obter informações sobre o contêiner de armazenamento, acesse [Criar um contêiner de armazenamento](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
- Ter o número adequado de discos de [tipos com suporte](storage-import-export-requirements.md#supported-disks). 
- Ter um sistema Windows executando uma [versão do sistema operacional com suporte](storage-import-export-requirements.md#supported-operating-systems). 
- Habilite o BitLocker no sistema Windows. Consulte [Como habilitar o BitLocker](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
- [Baixe o WAImportExport versão 1](https://aka.ms/waiev1) no sistema Windows. Descompacte para a pasta padrão `waimportexportv1`. Por exemplo, `C:\WaImportExportV1`.
- Ter uma conta FedEx/DHL. Se você quiser usar uma operadora que não seja FedEx/DHL, contate a equipe de operações de caixa de dados do Azure em `adbops@microsoft.com`.  
    - A conta deve ser válida, deve ter saldo e ter recursos de devolução.
    - Gerar um número de controle para o trabalho de exportação.
    - Cada trabalho deve ter um número de controle separado. Não há suporte para vários trabalhos com o mesmo número de controle.
    - Se você não tiver uma conta de transportadora, vá para:
        - [Criar uma conta FedEX](https://www.fedex.com/en-us/create-account.html), ou 
        - [Criar uma conta DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>Etapa 1: Preparar as unidades

Essa etapa gera um arquivo de diário. O arquivo de diário armazena informações básicas como número de série da unidade, chave de criptografia e detalhes da conta de armazenamento. 

Execute as etapas a seguir para preparar as unidades.

1.  Conecte as unidades de disco ao sistema Windows via conectores SATA.
1.  Crie um único volume NTFS em cada unidade. Atribua uma letra da unidade ao volume. Não use pontos de montagem.
2.  Habilite a criptografia BitLocker no volume NTFS. Se estiver usando um Windows Server System, use as instruções em [Como habilitar o BitLocker no Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
3.  Copie os dados para o volume criptografado. Use arrastar e soltar, Robocopy ou qualquer outra ferramenta de cópia.
4.  Abra um PowerShell ou janela de linha de comando com privilégios administrativos. Para alterar o diretório para a pasta descompactada, execute o comando a seguir:
    
    `cd C:\WaImportExportV1`
5.  Para obter a chave do BitLocker da unidade, execute o comando a seguir:
    
    `manage-bde -protectors -get <DriveLetter>:`
6.  Para preparar o disco, execute o comando a seguir. **Dependendo do tamanho dos dados, isso pode levar de várias horas a dias.** 

    ```
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /sk:<Storage account key> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /skipwrite 
    ```
    Um arquivo de diário é criado na mesma pasta em que você executou a ferramenta. Dois outros arquivos também são criados - um arquivo *.xml* (pasta onde você executa a ferramenta) e um arquivo *drive-manifest.xml* (pasta onde os dados residem).
    
    Os parâmetros utilizados são descritos na tabela a seguir:

    |Opção  |DESCRIÇÃO  |
    |---------|---------|
    |/j:     |O nome do arquivo de diário, com a extensão .jrn. Um arquivo de diário é gerado por unidade. É recomendável utilizar o número de série do disco como o nome do arquivo de diário.         |
    |/id:     |A ID da sessão. Use um número de sessão exclusivo para cada instância do comando.      |
    |/sk:     |A chave de conta de Armazenamento do Microsoft Azure.         |
    |/t:     |A letra da unidade do disco a ser enviado. Por exemplo, unidade `D`.         |
    |/bk:     |A chave do BitLocker para a unidade. O código de acesso da saída de `manage-bde -protectors -get D:`      |
    |/srcdir:     |A letra da unidade do disco a ser enviado seguida por `:\`. Por exemplo, `D:\`.         |
    |/dstdir:     |O nome do contêiner de destino no Armazenamento do Microsoft Azure.         |
    |/skipwrite:     |A opção que especifica que não há novos dados necessários para serem copiados e que os dados existentes no disco devem ser preparados.          |
7. Repita a etapa anterior para cada disco que precisa ser enviado. Um arquivo de diário com o nome fornecido é criado para cada execução da linha de comando.
    
    > [!IMPORTANT]
    > - Juntamente com o arquivo de diário, um arquivo `<Journal file name>_DriveInfo_<Drive serial ID>.xml` também é criado na mesma pasta em que a ferramenta reside. O arquivo .xml é usado no lugar do arquivo de diário ao criar um trabalho, se o arquivo de diário for muito grande. 

## <a name="step-2-create-an-import-job"></a>Etapa 2: Criar um trabalho de importação

Execute as etapas a seguir para criar um trabalho de importação no portal do Azure.

1. Faça logon em https://portal.azure.com/.
2. Vá para **Todos os serviços > Armazenamento > Trabalhos de importação/exportação**. 
    
    ![Vá para Trabalhos de importação/exportação](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Clique em **Criar Trabalho de Importação/Exportação**.

    ![Clique em Criar Trabalho de Importação/Exportação](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. Em **Noções básicas**:

   - Selecione **Importar para o Azure**.
   - Digite um nome descritivo para o trabalho de importação. Use o nome para acompanhar o andamento dos trabalhos.
       - O nome pode conter apenas letras minúsculas, números e hifens.
       - O nome deve começar com uma letra e não pode conter espaços.
   - Selecione uma assinatura.
   - Insira ou selecione um grupo de recursos.  

     ![Criar trabalho de importação - Etapa 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. Em **Detalhes do trabalho**:

    - Carregue os arquivos de diário da unidade que você obteve durante a etapa de preparação da unidade. Se utilizou `waimportexport.exe version1`, envie um arquivo para cada unidade que você preparou. Se o tamanho do arquivo de diário exceder 2 MB, você poderá usar o `<Journal file name>_DriveInfo_<Drive serial ID>.xml` também criado com o arquivo de diário. 
    - Selecione a conta de armazenamento de destino onde os dados residirão. 
    - O local final da corrida é preenchido automaticamente com base na região da conta de armazenamento selecionada.
   
   ![Criar trabalho de importação - Etapa 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. Em **Informações sobre a remessa de devolução**:

   - Selecione a operadora na lista suspensa. Se você quiser usar uma operadora que não seja FedEx/DHL, escolha uma opção existente na lista suspensa. Entre em contato com operações de caixa de dados do Azure de equipe em `adbops@microsoft.com` com as informações sobre o carro que você planeja usar.
   - Insira um número válido de conta de operadora que você criou com essa operadora. A Microsoft usará essa conta para enviar de volta as unidades para você após a conclusão do seu trabalho de importação. Caso não tenha um número de conta, crie uma conta da operadora [FedEx](https://www.fedex.com/us/oadr/) ou [DHL](http://www.dhl.com/).
   - Forneça um nome de contato completo e válido, telefone, email, endereço, cidade, CEP, estado/município e país/região. 
        
       > [!TIP] 
       > Em vez de especificar um endereço de email para um usuário único, forneça um email de grupo. Isso garante que você receba notificações mesmo que um administrador saia.

     ![Criar o trabalho de importação - Etapa 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)
   
5. No **Resumo**:

   - Revise as informações do trabalho fornecidas no resumo. Anote o nome do trabalho e o endereço de remessa do datacenter do Azure para enviar os discos de volta ao Azure. Essas informações serão utilizadas posteriormente na etiqueta de remessa.
   - Clique em **OK** para criar o trabalho de importação.

     ![Criar o trabalho de importação - Etapa 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives"></a>Etapa 3: Enviar as unidades 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]


## <a name="step-4-update-the-job-with-tracking-information"></a>Etapa 4: Atualizar o trabalho com informações de acompanhamento

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Etapa 5: Verificar o carregamento de dados para o Azure

Acompanhe o trabalho até a conclusão. Quando o trabalho estiver concluído, verifique se os dados foram carregados no Azure. Exclua os dados locais somente depois de verificar se o upload foi realizado com êxito.

## <a name="next-steps"></a>Próximas etapas

* [Exibir o status do trabalho e da unidade](storage-import-export-view-drive-status.md)
* [Verificar os requisitos de Importação/Exportação](storage-import-export-requirements.md)


