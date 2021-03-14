---
title: Gerenciar uma solicitação de suporte do Azure
description: Descreve como exibir solicitações de suporte, enviar mensagens, alterar o nível de severidade da solicitação, compartilhar informações de diagnóstico com o suporte do Azure, reabrir uma solicitação de suporte fechada e carregar arquivos.
tags: billing
ms.assetid: 86697fdf-3499-4cab-ab3f-10d40d3c1f70
ms.topic: how-to
ms.date: 12/14/2020
ms.openlocfilehash: 4d0c03e0035f6b71a23891ac1691f5421c1bdb76
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102502511"
---
# <a name="manage-an-azure-support-request"></a>Gerenciar uma solicitação de suporte do Azure

Depois de [criar uma solicitação de suporte do Azure](how-to-create-azure-support-request.md), você pode gerenciá-la no [portal do Azure](https://portal.azure.com), que é abordado neste artigo. Você também pode criar e gerenciar solicitações programaticamente, usando a [API REST do tíquete de suporte do Azure](/rest/api/support)ou usando [CLI do Azure](/cli/azure/azure-cli-support-request).

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

| 0-9, A-C    | D-G   | H-N         | O-Q   | R-T      | U-W        | X-Z     |
|-------------|-------|-------------|-------|----------|------------|---------|
| .7z         | .dat  | . Har        | .odx  | .rar     | .uccapilog | .xlam   |
| .a          | .db   | .hwl        | .oft  | .rdl     | .uccplog   | .xlr    |
| .abc        | .DMP  | .ics        | .old  | .rdlc    | .udcx      | .xls    |
| .adm        | .do_  | .ini        | .one  | .re_     | .vb_       | .xlsb   |
| .aspx       | .doc  | .java       | .osd  | .remove  | .vbs_      | .xlsm   |
| .ATF        | .docm | .jpg        | .OUT  | .ren     | .vcf       | .xlsx   |
| .b          | .docx | .LDF        | .p1   | .rename  | .vsd       | .xlt    |
| .ba_        | .dotm | .letterhead | .pcap | .rft     | .wdb       | .xltx   |
| .bak        | .dotx | .lo_        | .pdb  | .rpt     | .wks       | .xml    |
| .blg        | .dtsx | .log        | .pdf  | .rte     | .wma       | .xmla   |
| .CA_        | .eds  | .lpk        | .piz  | .rtf     | .wmv       | .xps    |
| .CAB        | .emf  | .manifest   | .pmls | .run     | .wmz       | .xsd    |
| .cap        | .eml  | .master     | .png  | .saz     | .wps       | .xsn    |
| .catx       | .emz  | .mdmp       | .potx | .sql     | .wpt       | .xxx    |
| .CFG        | .err  | .mof        | .ppt  | .sqlplan | .wsdl      | .z_     |
| .compressed | .etl  | .mp3        | .pptm | .stp     | .wsp       | .z01    |
| .Config     | .evt  | .mpg        | .pptx | .svclog  | .wtl       | .z02    |
| .cpk        | .evtx | .ms_        | .prn  | .tdb     | -          | .zi     |
| .cpp        | .EX   | .msg        | .psf  | .tdf     | -          | .zi_    |
| .cs         | .ex_  | .mso        | .pst  | .text    | -          | .zip    |
| .CSV        | .ex0  | .msu        | .pub  | .thmx    | -          | .zip_   |
| .cvr        | .FRD  | .nfo        | -     | .tif     | -          | .zipp   |
| -           | .gif  | -           | -     | .trc     | -          | .zipped |
| -           | .guid | -           | -     | .TTD     | -          | .zippy  |
| -           | .gz   | -           | -     | .tx_     | -          | .zipx   |
| -           | -     | -           | -     | .txt     | -          | .zit    |
| -           | -     | -           | -     | -        | -          | .zix    |
| -           | -     | -           | -     | -        | -          | .zzz    |

## <a name="close-a-support-request"></a>Fechar uma solicitação de suporte

Se você precisar fechar uma solicitação de suporte, [envie uma mensagem](#send-a-message) solicitando que a solicitação seja fechada.

## <a name="reopen-a-closed-request"></a>Reabrir uma solicitação fechada

Se você precisar reabrir uma solicitação de suporte fechada, crie uma [nova mensagem](#send-a-message), que reabrirá automaticamente a solicitação.

## <a name="cancel-a-support-plan"></a>Cancelar um plano de suporte

Se você precisar cancelar um plano de suporte, consulte [cancelar um plano de suporte](../../cost-management-billing/manage/cancel-azure-subscription.md#cancel-a-support-plan).

## <a name="next-steps"></a>Próximas etapas

[Como criar uma solicitação de suporte do Azure](how-to-create-azure-support-request.md)

[API REST do tíquete de suporte do Azure](/rest/api/support)
