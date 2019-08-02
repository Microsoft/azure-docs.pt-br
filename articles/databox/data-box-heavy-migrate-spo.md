---
title: Usar o Azure Data Box Heavy para migrar o conteúdo do compartilhamento de arquivos para o SharePoint Online| Microsoft Docs
description: Usar este tutorial para aprender a migrar o conteúdo do compartilhamento de arquivos para o Share Point Online usando o Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/18/2019
ms.author: alkohli
ms.openlocfilehash: 4955b28dff3193a95950912562cc3b6ec789479d
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325265"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>Usar o Azure Data Box Heavy para migrar o conteúdo do compartilhamento de arquivos para o SharePoint Online

Use o Azure Data Box Heavy e a Ferramenta de Migração do SharePoint (SPMT) para migrar facilmente o conteúdo de seu compartilhamento de arquivos para o SharePoint Online e o OneDrive. Com o Data Box Heavy, você pode remover a dependência do link da WAN (rede de longa distância) para transferir os dados.

O Azure Data Box da Microsoft é um serviço que permite solicitar um dispositivo no portal do Microsoft Azure. Você pode copiar terabytes de dados de seus servidores para o dispositivo. Depois de enviá-los de volta para a Microsoft, seus dados serão copiados para o Azure. Dependendo do tamanho dos dados que pretende transferir, você pode escolher entre:

- [Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) com capacidade utilizável de 35 TB por pedido para conjuntos de dados que vão de pequenos a médios.
- [Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) com capacidade utilizável de 80 TB por dispositivo para conjuntos de dados que vão de médios a grandes.
- [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-overview) com capacidade utilizável de 770 TB por dispositivo para conjuntos de dados grandes.

Este artigo fala especificamente sobre como usar o Data Box Heavy para migrar o conteúdo do compartilhamento de arquivos para o SharePoint Online.

## <a name="requirements-and-costs"></a>Requisitos e custos

### <a name="for-data-box-heavy"></a>Para o Data Box Heavy

- O Data Box Heavy só está disponível para as ofertas EA (Contrato Enterprise), CSP (Provedor de Soluções na Nuvem) ou ofertas do Azure Sponsorship. Caso sua assinatura não se enquadre em nenhum dos tipos acima, contate o Suporte da Microsoft para atualizá-la ou confira os [preços de assinaturas do Azure](https://azure.microsoft.com/pricing/).
- Há um valor a ser pago para usar o Data Box Heavy. Lembre-se de analisar os [preços do Data Box Heavy](https://azure.microsoft.com/pricing/details/databox/heavy/).


### <a name="for-sharepoint-online"></a>Para o SharePoint Online

- Analise os [Requisitos mínimos da Ferramenta de Migração do SharePoint (SPMT)](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

## <a name="workflow-overview"></a>Visão geral do fluxo de trabalho

Esse fluxo de trabalho exige que você execute etapas no Azure Data Box Heavy e no SharePoint Online.
As etapas a seguir são relacionadas ao Data Box Heavy.

1. Solicite o Azure Data Box Heavy.
2. Receba e configure seu dispositivo.
3. Copie dados de seu compartilhamento de arquivos local para a pasta Arquivos do Azure no dispositivo.
4. Depois que a cópia for concluída, envie o dispositivo de volta seguindo as instruções.
5. Aguarde até que os dados sejam completamente carregados no Azure.

As etapas a seguir são relacionadas ao SharePoint Online.

6. Criar uma VM no portal do Azure e monte o compartilhamento de arquivos do Azure nela.
7. Instale a ferramenta SPMT na VM do Azure.
8. Execute a ferramenta SPMT usando o compartilhamento de arquivos do Azure como a *fonte*.
9. Conclua as etapas finais da ferramenta.
10. Verifique e confirme seus dados.

## <a name="use-data-box-heavy-to-copy-data"></a>Usar o Data Box Heavy para copiar dados

Execute as seguintes etapas para copiar dados para o Data Box Heavy.

1. [Solicite o Data Box Heavy](data-box-heavy-deploy-ordered.md).
2. Depois de recebê-lo, [Configure o Data Box Heavy](data-box-heavy-deploy-set-up.md). Cabeie e configure os dois nós no dispositivo.
3. [Copie dados para o Azure Data Box Heavy](data-box-heavy-deploy-copy-data.md). Enquanto estiver copiando, não se esqueça dos seguintes procedimentos:

    - Use somente a pasta *StorageAccountName_AzFile* no Data Box Heavy para copiar os dados. Isso ocorre porque você deseja que os dados acabem em um compartilhamento de arquivos do Azure e não em blobs de blocos ou de páginas.
    - Copie os arquivos para uma pasta na pasta *StorageAccountName_AzFile*. Uma subpasta dentro da pasta *StorageAccountName_AzFile* cria um compartilhamento de arquivo. Os arquivos copiados diretamente para a pasta *StorageAccountName_AzFile* falham e são carregados como blobs de blocos. Esse é o compartilhamento de arquivos que você montará na VM na próxima etapa.
    - Copie dados para os dois nós do Data Box Heavy.
3. Execute a opção [Preparar para o envio](data-box-heavy-deploy-picked-up.md#prepare-to-ship) no dispositivo. A preparação bem-sucedida para o envio garantirá um carregamento eficaz dos arquivos para o Azure.
4. [Retorne o dispositivo](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back).
5. [Verifique o carregamento de dados no Azure](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure).

## <a name="use-spmt-to-migrate-data"></a>Usar SPMT para migrar dados

Após receber a confirmação da equipe de dados do Azure que sua cópia de dados foi concluída, continue migrando seus dados para o SharePoint Online.

Para ter melhor desempenho e conectividade, recomendamos que você crie uma máquina virtual (VM) do Azure.

1. Faça logon no portal do Azure e [Crie uma máquina virtual](../virtual-machines/windows/quick-create-portal.md).
2. [Monte o compartilhamento de arquivos do Azure na VM](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer).
3. [Baixe a ferramenta de Migração do SharePoint](https://spmtreleasescus.blob.core.windows.net/install/default.htm) e instale-a na VM do Azure.
4. Inicie a Ferramenta de Migração do SharePoint. Clique em **Entrar** e insira seu nome de usuário e senha do Office 365.
5. Quando a solicitação **Onde estão os dados?** for exibida, escolha **Compartilhamento de arquivos**. Insira o caminho para o compartilhamento de arquivos do Azure onde os dados estão localizados.
6. Siga os prompts restantes como de costume, incluindo seu local de destino. Para saber mais, acesse [Como usar a Ferramenta de Migração do SharePoint](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

> [!IMPORTANT]
> - A velocidade na qual os dados são ingeridos no SharePoint Online é afetada por vários fatores, independentemente de você já tiver seus dados no Azure. A compreensão desses fatores ajudará você a planejar e maximizar a eficiência da migração.  Para saber mais, acesse [Velocidade da migração do SharePoint Online e do OneDrive](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed).
> - Existe o risco de perder as atuais permissões de arquivos quando migrar os dados para o SharePoint Online. Você também pode perder alguns metadados como *Criado por* e *Data da modificação*.

## <a name="next-steps"></a>Próximas etapas

[Solicitar o Data Box Heavy](./data-box-heavy-deploy-ordered.md)