---
title: Perguntas frequentes sobre o serviço de Importação/Exportação do Microsoft Azure | Microsoft Docs
description: Leia as respostas para as perguntas frequentes sobre o serviço de Importação/Exportação do Microsoft Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 07/18/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 109f541157716ae4f9b195d0a3ed02a1d8c91960
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314119"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Serviço de Importação/Exportação do Microsoft Azure: perguntas frequentes 
A seguir, são apresentadas as perguntas e respostas que você pode ter ao utilizar o serviço de Importação/Exportação do Microsoft Azure para transferir dados ao Armazenamento do Microsoft Azure. As perguntas e respostas são organizadas nas seguintes categorias:

- Sobre o serviço de Importação/Exportação
- Preparar os discos para importação/exportação
- Trabalhos de Importação/Exportação
- Discos de envio
- Diversos 

## <a name="about-importexport-service"></a>Sobre o serviço de Importação/Exportação

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Posso copiar o armazenamento de Arquivos do Azure usando o serviço de Importação/Exportação do Microsoft Azure?

Sim. O serviço de Importação/Exportação do Microsoft Azure dá suporte para importação no armazenamento de Arquivos do Azure. Ele não dá suporte à exportação de Arquivos do Azure no momento.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>O serviço de Importação/Exportação do Microsoft Azure está disponível para assinaturas de CSP?

Sim. O serviço de Importação/Exportação do Microsoft Azure dá suporte para assinaturas de CSP (Provedores de Soluções na Nuvem).

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-o365"></a>Posso usar o serviço de Importação/Exportação do Microsoft Azure para copiar caixas de correio PST e dados do SharePoint para O365?

Sim. Para obter mais informações, acesse [Importar arquivos PST ou dados do SharePoint para Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Posso usar o serviço de Importação/Exportação do Microsoft Azure para copiar meus backups offline para o Serviço de Backup do Azure?

Sim. Para obter mais informações, acesse [Fluxo de trabalho de backup offline no Backup do Microsoft Azure](../../backup/backup-azure-backup-import-export.md).

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Posso comprar unidades para trabalhos de importação/exportação da Microsoft?

Nº É necessário enviar suas próprias unidades para trabalhos de importação e exportação.


## <a name="preparing-disks-for-importexport"></a>Preparar discos para importação/exportação

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Posso ignorar a etapa de preparação da unidade para um trabalho de importação? Posso preparar uma unidade sem copiar?

Nº Qualquer unidade utilizada para importar dados deve ser preparada usando a ferramenta WAImportExport do Azure. Use a ferramenta para também copiar dados para a unidade.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Preciso executar alguma preparação de disco ao criar um trabalho de exportação?

Nº Alguns pré-testes são recomendados. Para verificar o número de discos necessários, use o comando PreviewExport da ferramenta WAImportExport. Para obter mais informações, consulte [Visualizando o uso da unidade para um trabalho de exportação](https://msdn.microsoft.com/library/azure/dn722414.aspx). O comando ajuda a visualizar o uso da unidade para os blobs selecionados, com base no tamanho das unidades que você vai utilizar. Além disso, verifique se é possível ler e gravar no disco rígido enviado para o trabalho de exportação.

## <a name="importexport-jobs"></a>Trabalhos de Importação/Exportação

### <a name="can-i-cancel-my-job"></a>Posso cancelar o trabalho?
Sim. É possível cancelar um trabalho quando o status estiver **Criando** ou **Enviando**. Além desses estágios, o trabalho não pode ser cancelado e continua até o estágio final.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Por quanto tempo posso visualizar o status de trabalhos concluídos no portal do Azure?
Você pode exibir o status dos trabalhos concluídos por até 90 dias. Os trabalhos concluídos são excluídos após 90 dias.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Se eu quiser importar ou exportar mais de 10 unidades, o que devo fazer?
Um trabalho de importação ou exportação pode referenciar apenas 10 unidades em um único trabalho. Para enviar mais de 10 unidades, você deve criar vários trabalhos. As unidades associadas ao mesmo trabalho devem ser enviadas juntas no mesmo pacote. Para obter mais informações e diretrizes quando a capacidade de dados abrange vários trabalhos de importação de disco, entre em contato com Suporte da Microsoft. 

### <a name="the-uploaded-blob-shows-status-as-lease-expired-what-should-i-do"></a>O blob carregado mostra o status como "Concessão expirada". O que devo fazer?
Ignore o campo “Concessão expirada”. A Importação/Exportação assume a concessão do blob durante o upload para garantir que nenhum outro processo possa atualizar o blob em paralelo. A Concessão Expirada indica que a Importação/Exportação não está mais carregando nele e o blob está disponível para uso. 

## <a name="shipping-disks"></a>Discos de envio

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Qual é o número máximo de HDD para um envio?
Não há limite para o número de HDDs em uma remessa. Se os discos pertencerem a vários trabalhos, é recomendável que você: 
- Rotule os discos com nomes de trabalho correspondentes.
- atualize os trabalhos com um número de rastreamento com sufixo -1, -2 etc.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>Devo incluir algo diferente do HDD no pacote?
Envie apenas os discos rígidos no pacote de remessa. Não inclua itens como cabos de alimentação ou cabos USB.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>Tenho que enviar minhas unidades usando FedEx ou DHL?
É enviar unidades para o datacenter do Azure usando uma operadora conhecida como FedEx, DHL, UPS ou Serviço Postal. No entanto, para devolver-lhe a remessa de unidades partir do datacenter, você deverá fornecer:

- Um número de conta da FedEx nos EUA e na UE, ou
- Um número de conta DHL nas regiões da Ásia e Austrália.

> [!NOTE]
> Os data centers na Índia exigem uma letra de declaração em seu papel timbrado (Delivery Challan) para retornar as unidades. Para organizar a passagem de entrada necessária, você também deve marcar a seleção com a transportadora selecionada e compartilhar os detalhes com o datacenter.

### <a name="are-there-any-restrictions-with-shipping-my-drive-internationally"></a>Há alguma restrição ao enviar minha unidade internacionalmente?
Observe que a mídia física que está enviando talvez precise cruzar fronteiras internacionais. Você é responsável por garantir que seus dados e mídia física sejam importados e/ou exportados de acordo com as leis aplicáveis. Antes de enviar a mídia física, verifique com seus consultores se a mídia e os dados podem ser enviados legalmente ao data center identificado. Isso ajudará a garantir que eles cheguem à Microsoft pontualmente.

### <a name="are-there-any-special-requirements-for-delivering-my-disks-to-a-datacenter"></a>Há algum requisito especial para entregar meus discos a um datacenter?

Os requisitos dependem das restrições específicas do datacenter do Azure.
- Há alguns sites, que exigem que um número de ID de entrada do datacenter da Microsoft seja gravado na remessa por motivos de segurança. Antes de enviar suas unidades ou discos para o datacenter, entre em contato com oadbops@microsoft.comAzure data Box Operations () para obter esse número. Sem esse número, o pacote será rejeitado.
- Os data centers na Índia exigem os detalhes pessoais do driver, como o cartão de ID do governo ou prova de não. (por exemplo, PAN, AADHAR, DL), nome, contato e número da chapa do carro para obter uma passagem de entrada de portão. Para evitar atrasos de entrega, informe a sua operadora sobre esses requisitos.


### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>Ao criar um trabalho, o endereço para entrega é um local diferente do local da minha conta de armazenamento. O que devo fazer?

Alguns locais de armazenamento de conta são mapeados para os locais de entrega alternativos. Locais de envio disponíveis anteriormente também podem ser temporariamente mapeados para locais alternativos. Sempre verifique o endereço de envio fornecido durante a criação do trabalho antes de enviar suas unidades.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>Ao enviar minha unidade, a operadora solicita o endereço do contato e o número de telefone do data center. O que devo fornecer?

O número de telefone e o endereço DC são fornecidos como parte da criação do trabalho.


## <a name="miscellaneous"></a>Diversos

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>O que acontece se eu enviar acidentalmente um HDD que não esteja em conformidade com os requisitos com suporte?

O data center do Azure devolverá a unidade que não estiver em conformidade com os requisitos com suporte. Se apenas algumas unidades no pacote atenderem aos requisitos de suporte, elas serão processadas e as unidades que não atenderem aos requisitos serão retornadas para você.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>O serviço formata as unidades antes de devolvê-las?

Nº Todas as unidades são criptografadas com o BitLocker.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Como posso acessar dados importados por este serviço?

Use o Portal do Azure ou o [Gerenciador de Armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) para acessar os dados na conta de armazenamento do Azure.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>Depois que a importação for concluída, como ficam meus dados na conta de armazenamento? Minha hierarquia do diretório é preservada?

Ao preparar um disco rígido para um trabalho de importação, o destino é especificado pelo campo DstBlobPathOrPrefix no CSV do conjunto de dados. É o contêiner de destino na conta de armazenamento para o qual os dados do disco rígido serão copiados. Dentro desse contêiner de destino, diretórios virtuais são criados para as pastas do disco rígido e blobs são criados para os arquivos. 

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Se uma unidade tiver arquivos que já existam na minha conta de armazenamento, o serviço substituirá os blobs ou arquivos existentes?

Depende. Ao preparar a unidade, você pode especificar se os arquivos de destino devem ser substituídos ou ignorados usando o campo no arquivo CSV chamado Disposition:<rename|no-overwrite|overwrite>. Por padrão, o serviço renomeia os novos arquivos em vez de substituir os blobs ou arquivos existentes.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>A ferramenta WAImportExport é compatível com sistemas operacionais de 32 bits?
Nº A ferramenta WAImportExport só é compatível com o sistema de operacional do Windows de 64 bits. Para obter uma lista completa do SO com suporte, acesse [Sistemas Operacionais com Suporte](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements). 


### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Qual é o Blob de Blocos máximo e o Tamanho do Blob de Páginas com suporte pela Importação/Exportação do Microsoft Azure?

O tamanho máximo de Blob de Blocos é de aproximadamente 4,768 TB ou 5.000.000 MB.
O tamanho máximo de blob de páginas é 8 TB.


### <a name="does-azure-importexport-support-aes-256-encryption"></a>A Importação/Exportação do Microsoft Azure tem suporte para criptografia AES-256?
O serviço de Importação/Exportação do Microsoft Azure usa a criptografia AES-128 bitlocker por padrão. É possível alterar isso para AES-256 criptografando manualmente com bitlocker antes que os dados sejam copiados. 

- Se estiver usando [WAImportExport V1](https://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip), abaixo está um comando de exemplo
    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
    ```
- Se usar [WAImportExport V2](https://www.microsoft.com/download/details.aspx?id=55280), especifique "AlreadyEncrypted" e forneça a chave no CSV do conjunto de unidades.
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    ```

## <a name="next-steps"></a>Próximas etapas

* [O que é a Importação/Exportação do Microsoft Azure?](storage-import-export-service.md)


