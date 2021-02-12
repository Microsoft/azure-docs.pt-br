---
title: Azure Data Box solução de problemas para usar a interface REST | Microsoft Docs
description: Descreve como solucionar problemas vistos em Azure Data Box quando a cópia de dados é por meio da interface REST.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: 17b8d6de198746a79a50c4fbda805b364212e3c4
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98796046"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Solucionar problemas relacionados ao armazenamento de BLOBs Azure Data Box

Este artigo fornece informações detalhadas sobre como solucionar problemas que você pode ver ao usar o armazenamento de BLOBs Data Box por meio da interface REST em seu Data Box para copiar dados. Esses problemas surgem quando você está usando Data Box armazenamento de BLOBs com outros aplicativos ou bibliotecas de cliente, como Gerenciador de Armazenamento do Azure, AzCopy ou biblioteca de armazenamento do Azure para Python.

## <a name="errors-seen-in-azure-storage-explorer"></a>Erros vistos no Gerenciador de Armazenamento do Azure

Esta seção detalha alguns dos problemas enfrentados ao usar Gerenciador de Armazenamento do Azure com Data Box armazenamento de BLOBs.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|Não é possível recuperar recursos filho. O valor de um dos cabeçalhos HTTP não está no formato correto.|No menu **Editar** , selecione **Azure Stack APIs de destino**. <br>Reinicie Gerenciador de Armazenamento do Azure.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Verifique se o nome do ponto de extremidade `<accountname>.blob.<serialnumber>.microsoftdatabox.com` foi adicionado ao arquivo de hosts neste caminho: <li>`C:\Windows\System32\drivers\etc\hosts` no Windows ou </li><li> `/etc/hosts` no Linux.</li>|
|Não é possível recuperar recursos filho. <br>Detalhes: certificado autoassinado |Importe o certificado TLS/SSL para o seu dispositivo no Gerenciador de Armazenamento do Azure: <li>Baixe o certificado do portal do Azure. Para obter mais informações, acesse [baixar o certificado](data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>No menu **Editar** , selecione **certificados SSL** e, em seguida, selecione **importar certificados**.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Erros vistos no AzCopy para Windows

Esta seção detalha alguns dos problemas enfrentados ao usar o AzCopy para Windows com o armazenamento de BLOBs Data Box.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|O comando AzCopy parece parar de responder por um minuto antes de exibir este erro: <br>Falha ao enumerar o diretório https://... O nome remoto não pôde ser resolvido `<accountname>.blob.<serialnumber>.microsoftdatabox.com`|Verifique se o nome do ponto de extremidade `<accountname>.blob.<serialnumber>.microsoftdatabox.com` foi adicionado ao arquivo de hosts em: `C:\Windows\System32\drivers\etc\hosts` .|
|O comando AzCopy parece parar de responder por um minuto antes de exibir este erro: <br>Erro ao analisar o local de origem. A conexão subjacente foi fechada: não foi possível estabelecer uma relação de confiança para o canal seguro de SSL/TLS.|Importe o certificado TLS/SSL para seu dispositivo no repositório de certificados do sistema. Para obter mais informações, acesse [baixar o certificado](data-box-deploy-copy-data-via-rest.md#download-certificate).|


## <a name="errors-seen-in-azcopy-for-linux"></a>Erros vistos no AzCopy para Linux

Esta seção detalha alguns dos problemas enfrentados ao usar o AzCopy para Linux com o armazenamento de BLOBs Data Box.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|O comando AzCopy parece parar de responder por 20 minutos antes de exibir este erro: <br>Erro ao analisar o local de origem `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>` . Dispositivo ou endereço inexistente|Verifique se o nome do ponto de extremidade `<accountname>.blob.<serialnumber>.microsoftdatabox.com` foi adicionado ao arquivo de hosts em: `/etc/hosts` .|
|O comando AzCopy parece parar de responder por 20 minutos antes de exibir este erro: <br>Erro ao analisar o local de origem... Não foi possível estabelecer a conexão SSL.|Importe o certificado TLS/SSL para seu dispositivo no repositório de certificados do sistema. Para obter mais informações, acesse [baixar o certificado](data-box-deploy-copy-data-via-rest.md#download-certificate).|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Erros vistos na biblioteca de armazenamento do Azure para Python

Esta seção apresenta detalhes sobre alguns dos principais problemas enfrentados durante a implantação do Data Box Disk ao usar um cliente Linux para cópia de dados.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|O valor de um dos cabeçalhos HTTP não está no formato correto. |A versão instalada da biblioteca de Armazenamento do Microsoft Azure para Python não é suportada pelo Data Box. Consulte Azure Data Box requisitos de armazenamento de BLOBs para obter as versões com suporte.|
|… [SSL: CERTIFICATE_VERIFY_FAILED]...|Antes de executar o Python, defina a variável de ambiente REQUESTS_CA_BUNDLE como o caminho do arquivo de certificado TLS codificado em Base64 (consulte como [baixar o certificado](data-box-deploy-copy-data-via-rest.md#download-certificate)). <br>Por exemplo:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>Como alternativa, adicione o certificado ao repositório de certificados do sistema e, em seguida, defina essa variável de ambiente como o caminho do repositório. <br> Por exemplo, no Ubuntu:  <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Erros comuns

Esses erros não são específicos de nenhum aplicativo.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|A conexão atinge o tempo limite. |Entre no dispositivo de Data Box e verifique se ele está desbloqueado. Sempre que o dispositivo for reiniciado, ele permanecerá bloqueado até que alguém entre.|
|A autenticação da API REST falha com o erro: o servidor falhou ao autenticar a solicitação. Verifique se o valor do cabeçalho de autorização está formado corretamente, incluindo a assinatura. ErrorCode: AuthenticationFailed. |Um dos motivos pelos quais isso pode acontecer é quando a hora do dispositivo não é sincronizada com a do Azure. Se houver uma grande distorção de tempo, a autenticação da API REST será interrompida quando você estiver tentando copiar dados para a Data Box por meio da API REST. Nessa situação, você pode abrir a porta UDP 123 de saída para permitir o acesso ao `time.windows.com` . Depois que a hora do dispositivo for sincronizada com a do Azure, a autenticação deverá ter sucesso. |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [requisitos do sistema de armazenamento de blobs data Box](data-box-system-requirements-rest.md).
