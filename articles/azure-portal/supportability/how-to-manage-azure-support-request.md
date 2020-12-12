---
title: Gerenciar uma solicitação de suporte do Azure
description: Descreve como exibir solicitações de suporte, enviar mensagens, alterar o nível de severidade da solicitação, compartilhar informações de diagnóstico com o suporte do Azure, reabrir uma solicitação de suporte fechada e carregar arquivos.
tags: billing
ms.assetid: 86697fdf-3499-4cab-ab3f-10d40d3c1f70
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 882dfaa802638efd98eaf6f12a33a77a9727adc2
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359057"
---
# <a name="manage-an-azure-support-request"></a>Gerenciar uma solicitação de suporte do Azure

Depois de [criar uma solicitação de suporte do Azure](how-to-create-azure-support-request.md), você pode gerenciá-la no [portal do Azure](https://portal.azure.com), que é abordado neste artigo. Você também pode criar e gerenciar solicitações programaticamente, usando a [API REST do tíquete de suporte do Azure](/rest/api/support).

## <a name="view-support-requests"></a>Exibir todas as solicitações de suporte

Exiba os detalhes e o status das solicitações de suporte acessando **ajuda + suporte**  >   **todas as solicitações de suporte**.

:::image type="content" source="media/how-to-manage-azure-support-request/all-requests-lower.png" alt-text="Todas as solicitações de suporte":::

Nessa página, você pode pesquisar, filtrar e classificar solicitações de suporte. Selecione uma solicitação de suporte para exibir detalhes, incluindo sua severidade e todas as mensagens associadas à solicitação.

## <a name="send-a-message"></a>Enviar uma mensagem

1. Na página **todas as solicitações de suporte** , selecione a solicitação de suporte.

1. Na página **solicitação de suporte** , selecione **nova mensagem**.

1. Insira sua mensagem e selecione **Enviar**.

## <a name="change-the-severity-level"></a>Alterar o nível de severidade

> [!NOTE]
> O nível de severidade máximo depende do seu [plano de suporte](https://azure.microsoft.com/support/plans).
>

1. Na página **todas as solicitações de suporte** , selecione a solicitação de suporte.

1. Na página **solicitação de suporte** , selecione **alterar**.

    :::image type="content" source="media/how-to-manage-azure-support-request/change-severity.png" alt-text="Alterar severidade da solicitação de suporte":::

1. O portal do Azure mostra uma das duas telas, dependendo se sua solicitação já está atribuída a um engenheiro de suporte:

    - Se sua solicitação não tiver sido atribuída, você verá uma tela semelhante à seguinte. Selecione um novo nível de severidade e selecione **alterar**.

        :::image type="content" source="media/how-to-manage-azure-support-request/unassigned-can-change-severity.png" alt-text="Selecione um novo nível de severidade":::

    - Se sua solicitação tiver sido atribuída, você verá uma tela semelhante à seguinte. Selecione **OK** e, em seguida, crie uma [nova mensagem](#send-a-message) para solicitar uma alteração no nível de severidade.

        :::image type="content" source="media/how-to-manage-azure-support-request/assigned-cant-change-severity.png" alt-text="Não é possível selecionar um novo nível de severidade":::

## <a name="share-diagnostic-information-with-azure-support"></a>Compartilhar informações de diagnóstico com o suporte do Azure

Quando você cria uma solicitação de suporte, por padrão, a opção **compartilhar informações de diagnóstico** é selecionada. Isso permite que o suporte do Azure colete [informações de diagnóstico](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) dos recursos do Azure:

* Não é possível desmarcar essa opção após a criação de uma solicitação.

* Se você tiver desmarcado a opção ao criar uma solicitação, poderá selecioná-la depois que a solicitação for criada.

    1. Na página **todas as solicitações de suporte** , selecione a solicitação de suporte.
    
    1. Na página **solicitação de suporte** , selecione **conceder permissão** e, em seguida, selecione **Sim** e **OK**.
    
        :::image type="content" source="media/how-to-manage-azure-support-request/grant-permission-manage.png" alt-text="Conceder permissões para informações de diagnóstico":::

## <a name="upload-files"></a>Carregar arquivos

Você pode usar a opção de carregamento de arquivo para carregar arquivos de diagnóstico ou quaisquer outros arquivos que você considere relevantes para uma solicitação de suporte.

1. Na página **todas as solicitações de suporte** , selecione a solicitação de suporte.

1. Na página **solicitação de suporte** , procure localizar o arquivo e, em seguida, selecione **carregar**. Repita o processo se você tiver vários arquivos.

    :::image type="content" source="media/how-to-manage-azure-support-request/file-upload.png" alt-text="Carregar arquivo":::

### <a name="file-upload-guidelines"></a>Diretrizes de upload de arquivo

Siga estas diretrizes ao usar a opção de carregamento de arquivo:

* Para proteger sua privacidade, não inclua nenhuma informação pessoal em seu upload.
* O nome do arquivo deve ter menos de 110 caracteres.
* Não é possível carregar mais de um arquivo.
* Os arquivos não podem ser maiores que 4 MB.
* Todos os arquivos devem ter uma extensão de nome de arquivo, como *. docx* ou *. xlsx*. A tabela a seguir mostra as extensões de nome de arquivo que são permitidas para upload.

| 0-9, A-C     | D-G   | H-M         | N-P   | R-T      | U-W        | X-Z     |
|-------------|-------|-------------|-------|----------|------------|---------|
| .7z         | .dat  | . Har        | .odx  | .rar     | .tdb       | .xlam   |
| .a          | .db   | .hwl        | .oft  | .rdl     | .tdf       | .xlr    |
| .abc        | .DMP  | .ics        | .old  | .rdlc    | .text      | .xls    |
| .adm        | .do_  | .ini        | .one  | .re_     | .thmx      | .xlsb   |
| .aspx       | .doc  | .java       | .osd  | .reg     | .tif       | .xlsm   |
| .ATF        | .docm | .jpg        | .OUT  | .remove  | .trc       | .xlsx   |
| .b          | .docx | .LDF        | .p1   | .ren     | .TTD       | .xlt    |
| .ba_        | .dotm | .letterhead | .pcap | .rename  | .tx_       | .xltx   |
| .bak        | .dotx | .lnk        | .pdb  | .rft     | .txt       | .xml    |
| .bat        | .dtsx | .lo_        | .pdf  | .rpt     | .uccapilog | .xmla   |
| .blg        | .eds  | .log        | .piz  | .rte     | .uccplog   | .xps    |
| .CA_        | .emf  | .lpk        | .pmls | .rtf     | .udcx      | .xsd    |
| .CAB        | .eml  | .manifest   | .png  | .run     | .vb_       | .xsn    |
| .cap        | .emz  | .master     | .potx | .saz     | .vbs_      | .xxx    |
| .catx       | .err  | .mdmp       | .ppt  | .sql     | .vcf       | .z_     |
| .CFG        | .etl  | .mof        | .pptm | .sqlplan | .vsd       | .z01    |
| .compressed | .evt  | .mp3        | .pptx | .stp     | .wdb       | .z02    |
| .Config     | .evtx | .mpg        | .prn  | .svclog  | .wks       | .zi     |
| .cpk        | .EX   | .ms_        | .psf  | -        | .wma       | .zi_    |
| .cpp        | .ex_  | .msg        | .pst  | -        | .wmv       | .zip    |
| .cs         | .ex0  | .msi        | .pub  | -        | .wmz       | .zip_   |
| .CSV        | .FRD  | .mso        | -     | -        | .wps       | .zipp   |
| .cvr        | .gif  | .msu        | -     | -        | .wpt       | .zipped |
| -           | .guid | .nfo        | -     | -        | .wsdl      | .zippy  |
| -           | .gz   | -           | -     | -        | .wsp       | .zipx   |
| -           | -     | -           | -     | -        | .wtl       | .zit    |
| -           | -     | -           | -     | -        | -          | .zix    |
| -           | -     | -           | -     | -        | -          | .zzz    |

## <a name="reopen-a-closed-request"></a>Reabrir uma solicitação fechada

Se você precisar reabrir uma solicitação de suporte fechada, crie uma [nova mensagem](#send-a-message), que reabrirá automaticamente a solicitação.

## <a name="next-steps"></a>Próximas etapas

[Como criar uma solicitação de suporte do Azure](how-to-create-azure-support-request.md)

[API REST do tíquete de suporte do Azure](/rest/api/support)
