---
title: Use o portal do Azure para solucionar problemas do Azure Stack Edge pro com GPU | Microsoft Docs
description: Descreve como solucionar problemas de Azure Stack o Edge pro GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: c6f7182fe058bacb1236ff10dfc1553d23a7e1f2
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645248"
---
# <a name="troubleshoot-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Solucionar problemas em seu dispositivo de GPU pro do Azure Stack Edge 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como solucionar problemas em seu dispositivo de GPU pro Azure Stack Edge. 


## <a name="run-diagnostics"></a>Executar diagnóstico

Para diagnosticar e solucionar quaisquer erros de dispositivo, você pode executar os testes de diagnóstico. Execute as seguintes etapas na interface do usuário da Web local do seu dispositivo para executar testes de diagnóstico.

1. Na interface do usuário da Web local, vá para **Solução de problemas > Testes de diagnóstico**. Selecione o teste que você deseja executar e selecione **Executar teste**. O teste diagnostica todos os possíveis problemas com a rede, o dispositivo, o proxy da Web, o horário ou as configurações de nuvem. Você é notificado de que o dispositivo está executando testes.

    ![Selecionar testes ](media/azure-stack-edge-gpu-troubleshoot/run-diag-1.png)
 
2. Depois de os testes terem sido concluídos, os resultados serão exibidos. 

    ![Exibir resultados do teste](media/azure-stack-edge-gpu-troubleshoot/run-diag-2.png)

    Se um teste falhar, uma URL para a ação recomendada será apresentada. Selecione a URL para exibir a ação recomendada.
 
    ![Examinar avisos de testes com falha](media/azure-stack-edge-j-series-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>Coletar o pacote de Suporte

Um pacote de log é composto por todos os logs relevantes que podem ajudar o Suporte da Microsoft a solucionar problemas no dispositivo. Você pode gerar um pacote de log por meio da interface do usuário da Web local.

Execute as seguintes etapas para coletar um pacote de suporte. 

1. Na interface do usuário da Web local, vá para **Solução de problemas > Suporte**. Selecione **criar pacote de suporte**. O sistema começará a coletar o pacote de suporte. A coleta do pacote poderá levar vários minutos.

    ![Selecionar Adicionar usuário](media/azure-stack-edge-gpu-troubleshoot/collect-logs-1.png)
 
2. Depois que o pacote de suporte for criado, selecione **baixar pacote de suporte**. Um pacote compactado é baixado no caminho que você escolheu. Você pode descompactar o pacote e exibir os arquivos de log do sistema.

    ![Selecione Adicionar usuário 2](media/azure-stack-edge-gpu-troubleshoot/collect-logs-2.png)

## <a name="gather-advanced-security-logs"></a>Coletar logs de segurança avançados

Os logs de segurança avançados podem ser logs de invasão de software ou hardware para seu dispositivo Azure Stack Edge pro.

### <a name="software-intrusion-logs"></a>Logs de invasão de software

A invasão de software ou os logs de firewall padrão são coletados para o tráfego de entrada e de saída. 

- Quando o dispositivo é transimagem na fábrica, o log de firewall padrão é habilitado. Esses logs são agrupados no pacote de suporte por padrão quando você cria um pacote de suporte por meio da interface do usuário local ou por meio da interface do Windows PowerShell do dispositivo.

- Se apenas os logs de firewall forem necessários no pacote de suporte para examinar qualquer invasão de software (NW) no dispositivo, use `-Include FirewallLog` a opção ao criar o pacote de suporte. 

- Se nenhuma opção de inclusão específica for fornecida, o log de firewall será incluído como um padrão no pacote de suporte.

- No pacote de suporte, o log de firewall é o `pfirewall.log` e fica na pasta raiz. Aqui está um exemplo do log de intrusão de software para o dispositivo Azure Stack Edge pro. 

    ```
    #Version: 1.5
    #Software: Microsoft Windows Firewall
    #Time Format: Local
    #Fields: date time action protocol src-ip dst-ip src-port dst-port size tcpflags tcpsyn tcpack tcpwin icmptype icmpcode info path
    
    2019-11-06 12:35:19 DROP UDP 5.5.3.197 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - 
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9d87 ff02::fb 5353 5353 79 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.193 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.33 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8a ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    ```

### <a name="hardware-intrusion-logs"></a>Logs de intrusão de hardware

Para detectar qualquer invasão de hardware no dispositivo, atualmente, todos os eventos do chassi, como abertura ou fechamento de chassi, são registrados. 

- O log de eventos do sistema do dispositivo é lido usando o `racadm` cmdlet. Esses eventos são então filtrados para evento relacionado ao chassi em um `HWIntrusion.txt` arquivo.

- Para obter apenas o log de intrusão de hardware no pacote de suporte, use a `-Include HWSelLog` opção ao criar o pacote de suporte. 

- Se nenhuma opção de inclusão específica for fornecida, o log de intrusão de hardware será incluído como um padrão no pacote de suporte.

- No pacote de suporte, o log de intrusão de hardware é o `HWIntrusion.txt` e fica na pasta raiz. Aqui está um exemplo do log de intrusão de hardware para o dispositivo Azure Stack Edge pro. 

    ```
    09/04/2019 15:51:23 system Critical The chassis is open while the power is off.
    09/04/2019 15:51:30 system Ok The chassis is closed while the power is off.
    ```

## <a name="use-logs-to-troubleshoot"></a>Usar logs para solucionar problemas

Quaisquer erros ocorridos durante os processos de upload e de atualização são incluídos nos arquivos do respectivo erro.

1. Para exibir os arquivos de erro, vá para o compartilhamento e selecione o compartilhamento para exibir o conteúdo. 


2. Selecione a _pasta Microsoft Data Box Edge_. Esta pasta tem duas subpastas:

    - Faça upload da pasta que contém os arquivos de log para erros de upload.
    - Atualize a pasta para erros durante a atualização.

    Aqui está um arquivo de log de exemplo para a atualização.

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. Quando você vir um erro neste arquivo (realçado no exemplo), observe o código de erro, neste caso, é 16001. Pesquise a descrição desse código de erro em relação à referência de erro a seguir.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]

## <a name="use-error-lists-to-troubleshoot"></a>Usar listas de erros para solucionar problemas

As listas de erros são compiladas de cenários identificados e podem ser usadas para diagnóstico automático e solução de problemas. 

## <a name="azure-resource-manager"></a>Azure Resource Manager

Aqui estão os erros que podem aparecer durante a configuração de Azure Resource Manager para acessar seu dispositivo. 

| **Problema/erros** |  **Resolução** | 
|------------|-----------------|
|Problemas gerais|<li>[Verifique se o dispositivo de borda está configurado corretamente](#verify-the-device-is-configured-properly).<li> [Verifique se o cliente está configurado corretamente](#verify-the-client-is-configured-properly)|
|Add-AzureRmEnvironment: ocorreu um erro ao enviar a solicitação.<br>Na linha: 1 char: 1<br>+ Add-AzureRmEnvironment-Name Az3-ARMEndpoint " https://management.dbe ...|Esse erro significa que o dispositivo pro Azure Stack Edge não está acessível ou configurado corretamente. Verifique se o dispositivo de borda e o cliente estão configurados corretamente. Para obter diretrizes, consulte a linha **problemas gerais** nesta tabela.|
|O serviço retornou um erro. Verifique InnerException para obter mais detalhes: a conexão subjacente foi fechada: não foi possível estabelecer uma relação de confiança para o canal seguro de SSL/TLS. |   Esse erro é provavelmente devido a uma ou mais etapas de traga seu próprio certificado executadas incorretamente. Você pode encontrar orientações [aqui](./azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates). |
|A operação retornou um código de status inválido ' ServiceUnavailable ' <br> O código de status de resposta não indica êxito: 503 (Serviço indisponível). | Esse erro pode ser o resultado de qualquer uma dessas condições.<li>ArmStsPool está no estado parado.</li><li>Um dos sites de serviços de token de Azure Resource Manager/segurança estão inativos.</li><li>O recurso de cluster Azure Resource Manager está inoperante.</li><br><strong>Observação:</strong> Reiniciar o dispositivo pode corrigir o problema, mas você deve coletar o pacote de suporte para que possa depurá-lo ainda mais.|
|AADSTS50126: nome de usuário ou senha inválido.<br>ID de rastreamento: 29317da9-52fc-4ba0-9778-446ae5625e5a<br>ID de correlação: 1b9752c4-8cbf-4304-a714-8a16527410f4<br>Carimbo de data/hora: 2019-11-15 09:21:57Z: o servidor remoto retornou um erro: (400) solicitação inadequada.<br>Na linha: 1 char: 1 |Esse erro pode ser o resultado de qualquer uma dessas condições.<li>Para obter um nome de usuário e senha inválidos, valide se o cliente alterou a senha de portal do Azure seguindo as etapas [aqui](/azure/azure-stack-edge-gpu-set-azure-resource-manager-password) e, em seguida, usando a senha correta.<li>Para uma ID de locatário inválida, a ID do locatário é um GUID fixo e deve ser definida como `c0257de7-538f-415c-993a-1b87a031879d`</li>|
|Connect-AzureRmAccount: AADSTS90056: o recurso está desabilitado ou não existe. Verifique o código do seu aplicativo para garantir que você especificou o URL exato do recurso que você está tentando acessar.<br>ID de rastreamento: e19bdbc9-5dc8-4a74-85c3-ac6abdfda115<br>ID de correlação: 75c8ef5a-830e-48b5-b039-595a96488ff9 timestamp: 2019-11-18 07:00:51Z: o servidor remoto retornou um erro: (400) inadequado |Os pontos de extremidade de recurso usados no `Add-AzureRmEnvironment` comando estão incorretos.|
|Não é possível obter pontos de extremidade da nuvem.<br>Verifique se você tem conexão de rede. Detalhe do erro: HTTPSConnectionPool (host = ' Management. dbg-of4k6suvm.microsoftdatabox.com ', Port = 30005): número máximo de tentativas excedidos com URL:/Metadata/Endpoints? API-Version = 2015-01-01 (causada por SSLError (SSLError ("handshake inadequado: erro ([(' rotinas SSL ', ' tls_process_server_certificate ', ' verificação de certificado falhou ')],)",))) |Esse erro aparece principalmente em um ambiente Mac/Linux e devido aos seguintes problemas:<li>Um certificado de formato PEM não foi adicionado ao repositório de certificados do Python.</li> |

### <a name="verify-the-device-is-configured-properly"></a>Verifique se o dispositivo está configurado corretamente

1. Na interface do usuário local, verifique se a rede do dispositivo está configurada corretamente.

2. Verifique se os certificados são atualizados para todos os pontos de extremidade, conforme mencionado [aqui](./azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates).

3. Obtenha o ponto de extremidade de gerenciamento e logon do Azure Resource Manager na página do **dispositivo** na interface do usuário local.

4. Verifique se o dispositivo está ativado e registrado no Azure.


### <a name="verify-the-client-is-configured-properly"></a>Verifique se o cliente está configurado corretamente

1. Valide se a versão correta do PowerShell está instalada conforme mencionado [aqui](./azure-stack-edge-gpu-connect-resource-manager.md#step-3-install-powershell-on-the-client).

2. Valide se os módulos corretos do PowerShell estão instalados conforme mencionado [aqui](./azure-stack-edge-gpu-connect-resource-manager.md#step-4-set-up-azure-powershell-on-the-client).

3. Valide se os pontos de extremidade de Azure Resource Manager e logon estão acessíveis. Você pode tentar executar ping nos pontos de extremidade. Por exemplo:

   `ping management.28bmdw2-bb9.microsoftdatabox.com`
   `ping login.28bmdw2-bb9.microsoftdatabox.com`
   
   Se eles não estiverem acessíveis, adicione entradas de arquivo de DNS/host conforme mencionado [aqui](./azure-stack-edge-gpu-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution).
   
4. Valide se os certificados de cliente estão instalados conforme mencionado [aqui](./azure-stack-edge-gpu-connect-resource-manager.md#import-certificates-on-the-client-running-azure-powershell).

5. Se o cliente estiver usando o PowerShell, você deverá habilitar a preferência de depuração para ver mensagens detalhadas executando esse comando do PowerShell. 

    `$debugpreference = "continue"`

## <a name="blob-storage-on-device"></a>Armazenamento de BLOBs no dispositivo 

Aqui estão os erros relacionados ao armazenamento de BLOBs no dispositivo Azure Stack Edge pro/Gateway do Data Box.

| **Problema/erros** |  **Resolução** | 
|--------------------|-----------------|
|Não é possível recuperar recursos filho. O valor de um dos cabeçalhos HTTP não está no formato correto.| No menu **Editar** , selecione **Azure Stack APIs de destino**. Em seguida, reinicie o Gerenciador de Armazenamento do Azure.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com`|Verifique se o nome do ponto de extremidade `<accountname>.blob.<serialnumber>.microsoftdatabox.com` foi adicionado ao arquivo de hosts neste caminho: `C:\Windows\System32\drivers\etc\hosts` no Windows ou `/etc/hosts` no Linux.|
|Não é possível recuperar recursos filho.<br> Detalhes: certificado autoassinado |Importe o certificado SSL para seu dispositivo no Gerenciador de Armazenamento do Azure: <ol><li>Baixe o certificado do portal do Azure. Para obter mais informações, consulte [baixar o certificado](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>No menu **Editar** , selecione certificados SSL e, em seguida, selecione **importar certificados**.</li></ol>|
|O comando AzCopy parece parar de responder por um minuto antes de exibir este erro:<br>`Failed to enumerate directory https://… The remote name could not be resolved <accountname>.blob.<serialnumber>.microsoftdatabox.com`|Verifique se o nome do ponto de extremidade `<accountname>.blob.<serialnumber>.microsoftdatabox.com` foi adicionado ao arquivo de hosts em: `C:\Windows\System32\drivers\etc\hosts` .|
|O comando AzCopy parece parar de responder por um minuto antes de exibir este erro:<br>`Error parsing source location. The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`. |Importe o certificado SSL do seu dispositivo para o repositório de certificados do sistema. Para obter mais informações, consulte [baixar o certificado](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).|
|O comando AzCopy parece parar de responder por 20 minutos antes de exibir este erro:<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`. |Verifique se o nome do ponto de extremidade `<accountname>.blob.<serialnumber>.microsoftdatabox.com` foi adicionado ao arquivo de hosts em: `/etc/hosts` .|
|O comando AzCopy parece parar de responder por 20 minutos antes de exibir este erro:<br>`Error parsing source location… The SSL connection could not be established`. |Importe o certificado SSL do seu dispositivo para o repositório de certificados do sistema. Para obter mais informações, consulte [baixar o certificado](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).|
|O comando AzCopy parece parar de responder por 20 minutos antes de exibir este erro:<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`|Verifique se o nome do ponto de extremidade `<accountname>.blob.<serialnumber>.microsoftdatabox.com` foi adicionado ao arquivo de hosts em: `/etc/hosts` .|
|O comando AzCopy parece parar de responder por 20 minutos antes de exibir este erro: `Error parsing source location… The SSL connection could not be established` .|Importe o certificado SSL do seu dispositivo para o repositório de certificados do sistema. Para obter mais informações, consulte [baixar o certificado](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).|
|O valor de um dos cabeçalhos HTTP não está no formato correto.|A versão instalada da biblioteca de Armazenamento do Microsoft Azure para Python não é suportada pelo Data Box. Consulte Azure Data Box requisitos de armazenamento de BLOBs para obter as versões com suporte.|
|… [SSL: CERTIFICATE_VERIFY_FAILED]...| Antes de executar o Python, defina a variável de ambiente REQUESTS_CA_BUNDLE como o caminho do arquivo de certificado SSL codificado em Base64 (consulte como [baixar o certificado](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate). Por exemplo:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer`<br>`python`<br>Como alternativa, adicione o certificado ao repositório de certificados do sistema e, em seguida, defina essa variável de ambiente como o caminho do repositório. Por exemplo, no Ubuntu: <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt`<br>`python`.|
|A conexão atinge o tempo limite.|Entre no Azure Stack Edge pro e, em seguida, verifique se ele está desbloqueado. Sempre que o dispositivo for reiniciado, ele permanecerá bloqueado até que alguém entre.|

## <a name="troubleshoot-iot-edge-errors"></a>Solucionar erros IoT Edge

[!INCLUDE [Troubleshoot IoT Edge runtime](../../includes/azure-stack-edge-iot-troubleshoot-compute.md)]


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como [solucionar problemas de ativação do dispositivo](azure-stack-edge-gpu-troubleshoot-activation.md).
