---
title: Azure Data Box de solução de problemas para usar a interface REST | Microsoft Docs
description: Descreve como solucionar problemas encontrados no Azure Data Box quando a cópia de dados é por meio da interface REST.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: ee2820d78e95924e09a0219753f87d6910c0e736
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782949"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Solucionar problemas relacionados ao armazenamento de blobs de caixa de dados do Azure

Essas informações de detalhes do artigo sobre como solucionar problemas que você pode ver ao usar o armazenamento de blobs de caixa de dados por meio da interface REST em sua caixa de dados para copiar dados. Superfície esses problemas quando você estiver usando o armazenamento de blobs de caixa de dados com outros aplicativos ou bibliotecas de cliente, como a biblioteca do Gerenciador de armazenamento do Azure, o AzCopy ou o armazenamento do Azure para Python.

## <a name="errors-seen-in-azure-storage-explorer"></a>Erros exibidos no Gerenciador de armazenamento do Azure

Esta seção fornece detalhes sobre alguns dos problemas enfrentados ao usar o Gerenciador de armazenamento do Azure com o armazenamento de blobs de caixa de dados.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|Não é possível recuperar os recursos filho. O valor de um dos cabeçalhos HTTP não está no formato correto.|Dos **edite** menu, selecione **APIs do destino do Azure Stack**. <br>Reinicie o Gerenciador de armazenamento do Azure.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Verifique se o nome do ponto de extremidade `<accountname>.blob.<serialnumber>.microsoftdatabox.com` é adicionada ao arquivo de hosts no seguinte caminho: <li>`C:\Windows\System32\drivers\etc\hosts` no Windows, ou </li><li> `/etc/hosts` no Linux.</li>|
|Não é possível recuperar os recursos filho. <br>Detalhes: o certificado autoassinado |Importe o certificado SSL para seu dispositivo para o Gerenciador de armazenamento do Azure: <li>Baixe o certificado do portal do Azure. Para obter mais informações, acesse [baixar o certificado](data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>Dos **edite** menu, selecione **certificados SSL** e, em seguida, selecione **importar certificados**.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Erros exibidos no AzCopy para Windows

Esta seção fornece detalhes sobre alguns dos problemas enfrentados ao usar o AzCopy para Windows com o armazenamento de blobs de caixa de dados.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|Comando AzCopy parece parar de responder por um minuto antes de exibir este erro: <br>Falha ao enumerar diretório https://... O nome remoto não pôde ser resolvido `<accountname>.blob.<serialnumber>.microsoftdatabox.com`|Verifique se o nome do ponto de extremidade `<accountname>.blob.<serialnumber>.microsoftdatabox.com` é adicionado ao arquivo de hosts em: `C:\Windows\System32\drivers\etc\hosts`.|
|Comando AzCopy parece parar de responder por um minuto antes de exibir este erro: <br>Local de origem de análise de erro. A conexão subjacente estava fechada: Não foi possível estabelecer a relação de confiança para o canal seguro SSL/TLS.|Importe o certificado SSL para seu dispositivo no repositório de certificados do sistema. Para obter mais informações, acesse [baixar o certificado](data-box-deploy-copy-data-via-rest.md#download-certificate).|


## <a name="errors-seen-in-azcopy-for-linux"></a>Erros exibidos no AzCopy para Linux

Esta seção fornece detalhes sobre alguns dos problemas enfrentados ao usar o AzCopy para Linux com o armazenamento de Blob de caixa de dados.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|Comando AzCopy parece parar de responder por 20 minutos antes de exibir este erro: <br>Erro ao analisar o local de origem `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`. Não há tal dispositivo ou endereço|Verifique se o nome do ponto de extremidade `<accountname>.blob.<serialnumber>.microsoftdatabox.com` é adicionado ao arquivo de hosts em: `/etc/hosts`.|
|Comando AzCopy parece parar de responder por 20 minutos antes de exibir este erro: <br>Local de origem de análise de erro... Não foi possível estabelecer a conexão SSL.|Importe o certificado SSL para seu dispositivo no repositório de certificados do sistema. Para obter mais informações, acesse [baixar o certificado](data-box-deploy-copy-data-via-rest.md#download-certificate).|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Erros exibidos na biblioteca de armazenamento do Azure para Python

Esta seção apresenta detalhes sobre alguns dos principais problemas enfrentados durante a implantação do Data Box Disk ao usar um cliente Linux para cópia de dados.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|O valor de um dos cabeçalhos HTTP não está no formato correto. |Não há suporte para a versão instalada da biblioteca de armazenamento do Microsoft Azure para Python pela caixa de dados. Consulte os requisitos de armazenamento de Blob de caixa de dados do Azure para as versões com suporte.|
|… [SSL: CERTIFICATE_VERIFY_FAILED] …|Antes de executar o Python, defina a variável de ambiente REQUESTS_CA_BUNDLE para o caminho do arquivo de certificado codificado em Base64 SSL (consulte como [baixar o certificado]()). <br>Por exemplo: <br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>Como alternativa, adicione o certificado ao repositório de certificados do sistema e, em seguida, defina essa variável de ambiente para o caminho do repositório. <br> Por exemplo, no Ubuntu: <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Erros comuns

Esses erros não são específicos para qualquer aplicativo.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|A conexão expire. |Entre no dispositivo Data Box e verifique se ele está desbloqueado. Sempre que o dispositivo é reiniciado, ele permanece bloqueado até que alguém faz logon.|

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [requisitos de sistema de armazenamento de Blob de caixa de dados](data-box-system-requirements-rest.md).
