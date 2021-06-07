---
title: Chave do formato de evento comum (CEF) e mapeamento de campo CommonSecurityLog
description: Este artigo mapeia as chaves de CEF para os nomes de campo correspondentes no CommonSecurityLog no Azure Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.date: 03/16/2021
ms.openlocfilehash: 6c23fe86af030d371e12914062bb9558e8db3484
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104776127"
---
# <a name="cef-and-commonsecuritylog-field-mapping"></a>Mapeamento de campo CEF e CommonSecurityLog

As tabelas a seguir mapeiam nomes de campo de formato de evento comum (CEF) para os nomes que eles usam no CommonSecurityLog do Azure Sentinel e podem ser úteis quando você estiver trabalhando com uma fonte de dados CEF no Azure Sentinel.

Para obter mais informações, consulte [conectar sua solução externa usando o formato de evento comum](connect-common-event-format.md).

## <a name="a---c"></a>A – C

|Nome da chave CEF  |Nome do campo CommonSecurityLog  |Descrição  |
|---------|---------|---------|
| Act    |    <a name="deviceaction"></a> Dispositivoaction     |  A ação mencionada no evento.       |
|   app  |    ApplicationProtocol     |  O protocolo usado no aplicativo, como HTTP, HTTPS, SSHv2, Telnet, POP, IMPA, IMAPs e assim por diante.   |
| cnt    |    EventCount     |  Uma contagem associada ao evento, mostrando quantas vezes o mesmo evento foi observado.       |
| | | |

## <a name="d"></a>D

|Nome da chave CEF  |Nome do CommonSecurityLog  |Descrição  |
|---------|---------|---------|
|Fornecedor do dispositivo     |  DeviceVendor       | Cadeia de caracteres que, junto com as definições de produto e versão do dispositivo, identifica exclusivamente o tipo de dispositivo de envio.       |
|Produto do dispositivo     |   DeviceProduct      |   Cadeia de caracteres que, junto com as definições de fornecedor e versão do dispositivo, identifica exclusivamente o tipo de dispositivo de envio.        |
|Versão do dispositivo     |   DeviceVersion      |      Cadeia de caracteres que, junto com as definições de produto e fornecedor do dispositivo, identifica exclusivamente o tipo de dispositivo de envio.     |
|DeviceEventClassID     |   DeviceEventClassID     |   Cadeia de caracteres ou inteiro que serve como um identificador exclusivo por tipo de evento.      |
| destinationDnsDomain    | DestinationDnsDomain        |   A parte DNS do FQDN (nome de domínio totalmente qualificado).      |
| destinationServiceName | DestinationServiceName | O serviço que é alvo do evento. Por exemplo, `sshd`.|
| destinationTranslatedAddress | DestinationTranslatedAddress | Identifica o destino traduzido referido pelo evento em uma rede IP, como um endereço IP IPv4. |
| destinationTranslatedPort | DestinationTranslatedPort | Porta, após a tradução, como um firewall. <br>Números de porta válidos: `0` - `65535` |
| deviceDirection | <a name="communicationdirection"></a> CommunicationDirection | Todas as informações sobre a direção que a comunicação observada levou. Valores válidos:  <br>- `0` = Entrada <br>- `1` = Saída |
| deviceDnsDomain | DeviceDnsDomain | A parte do domínio DNS do FQDN (nome de domínio totalmente qualificado) |
| deviceExternalID | DeviceExternalID | Um nome que identifica exclusivamente o dispositivo que gera o evento. |
| deviceFacility | DeviceFacility | O recurso que gera o evento.|
| deviceInboundInterface | DeviceInboundInterface |A interface na qual o pacote ou os dados inseriram o dispositivo.  |
| deviceNtDomain | DeviceNtDomain | O domínio do Windows do endereço do dispositivo |
| deviceOutboundInterface | DeviceOutboundInterface |Interface na qual o pacote ou os dados saíram do dispositivo. |
| devicePayloadId |DevicePayloadId |Identificador exclusivo para a carga associada ao evento. |
| deviceProcessName | ProcessName | Nome do processo associado ao evento. <br><br>Por exemplo, no UNIX, o processo que gera a entrada do syslog. |
| deviceTranslatedAddress | DeviceTranslatedAddress | Identifica o endereço de dispositivo traduzido ao qual o evento se refere, em uma rede IP. <br><br>O formato é um endereço IPv4. |
| dhost |DestinationHostName | O destino ao qual o evento se refere em uma rede IP.  <br>O formato deve ser um FQDN associado ao nó de destino, quando um nó está disponível. Por exemplo, `host.domain.com` ou `host`. |
| dmac | DestinationMacAddress | O endereço MAC de destino (FQDN) |
| dntdom | DestinationNTDomain | O nome de domínio do Windows do endereço de destino.|
| dpid | DestinationProcessId |A ID do processo de destino associado ao evento.|
| dpriv | DestinationUserPrivileges | Define os privilégios do uso de destino. <br>Valores válidos: `Admninistrator` , `User` , `Guest` |
| dproc | DestinationProcessName | O nome do processo de destino do evento, como `telnetd` ou `sshd.` |
| DPT | DestinationPort | Porta de destino. <br>Valores válidos: `*0` - `65535` |
| DST | DestinationIP | O endereço IpV4 de destino ao qual o evento se refere em uma rede IP. |
| dtz | DeviceTimeZon | Fuso horário do dispositivo que gera o evento |
| DUID |DestinationUserId | Identifica o usuário de destino por ID. |
| duser | DestinationUserName |Identifica o usuário de destino por nome.|
| dvc | DeviceAddress | O endereço IPv4 do dispositivo que gera o evento. |
| dvchost | DeviceName | O FQDN associado ao nó do dispositivo, quando um nó está disponível. Por exemplo, `host.domain.com` ou `host`.| 
| dvcmac | DeviceMacAddress | O endereço MAC do dispositivo que gera o evento. |
| dvcpid | ID do Processo | Define a ID do processo no dispositivo que gera o evento. |

## <a name="e---i"></a>E-I

|Nome da chave CEF  |Nome do CommonSecurityLog  |Descrição  |
|---------|---------|---------|
|end     |  EndTime       | A hora em que a atividade relacionada ao evento terminou.        |
|externalId    |   ExternalId      | Uma ID usada pelo dispositivo de origem. Normalmente, esses valores têm valores crescentes que são associados a um evento.        |
|filecriartime     |  Filecriartime      | Hora em que o arquivo foi criado.        |
|fileHash     |   FileHash      |   Hash de um arquivo.      |
|fileId     |   FileID      |Uma ID associada a um arquivo, como inode.         |
| filemodificatime | Filemodificatime |Hora em que o arquivo foi modificado pela última vez. |
| filePath | FilePath | Caminho completo do arquivo, incluindo o nome de arquivo. Por exemplo: `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` ou `/usr/bin/zip`.|
| permissão de |Permissão de |As permissões do arquivo. |
| FileType | FileType | Tipo de arquivo, como pipe, soquete e assim por diante.|
|fname | FileName| O nome do arquivo, sem o caminho. |
| fsize | FileSize | O tamanho do arquivo. |
|Host    |  Computador       | Host, do syslog        |
|in     |  ReceivedBytes      |Número de bytes transferidos de entrada.         |
| | | |

## <a name="m---p"></a>M-P

|Nome da chave CEF  |Nome do CommonSecurityLog  |Descrição  |
|---------|---------|---------|
|msg   |  Mensagem       | Uma mensagem que fornece mais detalhes sobre o evento.        |
|Nome     |  Atividade       |   Uma cadeia de caracteres que representa uma descrição legível e compreensível do evento.     |
|oldFileCreateTime     |  OldFileCreateTime       | Hora em que o arquivo antigo foi criado.        |
|oldFileHash     |   OldFileHash      |   Hash do arquivo antigo.      |
|oldFileId     |   OldFileId     |   E a ID associada ao arquivo antigo, como o inode.      |
| oldFileModificationTime | OldFileModificationTime |Hora em que o arquivo antigo foi modificado pela última vez. |
| oldFileName |  OldFileName |Nome do arquivo antigo. |
| oldFilePath | OldFilePath | Caminho completo para o arquivo antigo, incluindo o nome de arquivo. <br>Por exemplo, `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` ou `/usr/bin/zip`.|
| oldFilePermission | OldFilePermission |Permissões do arquivo antigo. |
|oldFileSize | OldFileSize | Tamanho do arquivo antigo.|
| oldFileType | OldFileType | Tipo de arquivo do arquivo antigo, como um pipe, soquete e assim por diante.|
| out | SentBytes | Número de bytes transferidos de saída. |
| Resultado | Resultado | Resultado do evento, como `success` ou `failure` .|
|proto    |  Protocolo       | Protocolo de transporte que identifica o protocolo de camada 4 usado. <br><br>Os valores possíveis incluem nomes de protocolo, como `TCP` ou `UDP` .        |
| | | |

## <a name="r---t"></a>R-T

|Nome da chave CEF  |Nome do CommonSecurityLog  |Descrição  |
|---------|---------|---------|
|Motivo     |  Motivo      |O motivo pelo qual um evento de auditoria foi gerado. <br><br>Por exemplo, `Bad password` ou `Unknown user`.         |
|Solicitação     |   RequestURL      | A URL acessada para uma solicitação HTTP, incluindo o protocolo. Por exemplo, `http://www/secure.com`        |
|requestClientApplication     |   RequestClientApplication      |   O agente do usuário associado à solicitação.      |
| requestContext | RequestContext | Descreve o conteúdo do qual a solicitação se originou, como o referenciador HTTP. |
| requestCookies | RequestCookies |Cookies associados à solicitação. |
| requestMethod | RequestMethod | O método usado para acessar uma URL. <br><br>Os valores válidos incluem métodos como `POST` , `GET` e assim por diante. |
| rt | Confirmação de recebimento | A hora em que o evento relacionado à atividade foi recebido. |
|Severidade     |  <a name="logseverity"></a> LogSeverity       |  Uma cadeia de caracteres ou um inteiro que descreve a importância do evento.<br><br> Valores de cadeia de caracteres válidos:,,, `Unknown` `Low` `Medium` `High` , `Very-High` <br><br>Os valores inteiros válidos são: `0` - `3` = Low, `4` - `6` = Medium, `7` - `8` = High, `9` - `10` = Very-High |
| shost    | SourceHostName        |Identifica a origem à qual o evento se refere em uma rede IP. O formato deve ser um DQDN (nome de domínio totalmente qualificado) associado ao nó de origem, quando um nó está disponível. Por exemplo, `host` ou `host.domain.com`. |
| smac | SourceMacAddress | Endereço MAC de origem. |
| sntdom | SourceNTDomain | O nome de domínio do Windows para o endereço de origem. |
| sourceDnsDomain | SourceDnsDomain | A parte do domínio DNS do FQDN completo. |
| sourceServiceName | SourceServiceName | O serviço responsável por gerar o evento. |
| sourceTranslatedAddress | SourceTranslatedAddress | Identifica a fonte traduzida à qual o evento se refere em uma rede IP. |
| sourceTranslatedPort | SourceTranslatedPort | Porta de origem após a conversão, como um firewall. <br>Os números de porta válidos são `0`  -  `65535` . |
| spid | SourceProcessId | A ID do processo de origem associado ao evento.|
| spriv | SourceUserPrivileges | Os privilégios do usuário de origem. <br><br>Os valores válidos incluem: `Administrator` , `User` , `Guest` |
| SPROC | SourceProcessName | O nome do processo de origem do evento.|
| spt | SourcePort | O número da porta de origem. <br>Os números de porta válidos são `0`  -  `65535` . |
| src | SourceIP |A origem à qual um evento se refere em uma rede IP, como um endereço IPv4. |
| iniciar | StartTime | A hora em que a atividade que o evento faz referência foi iniciada. |
| suid | SourceUserID | Identifica o usuário de origem por ID. |
| type | EventType | Tipo de evento. Os valores de valor incluem: <br>- `0`: evento base <br>- `1`: agregado <br>- `2`: evento de correlação <br>- `3`: evento de ação <br><br>**Observação**: esse evento pode ser omitido para eventos base. |
| | | |

## <a name="unmapped-fields"></a>Campos não mapeados

Os seguintes nomes de campo **CommonSecurityLog** não têm mapeamentos em chaves CEF:


|Nome do campo CommonSecurityLog  |Descrição  |
|---------|---------|
|**OriginalLogSeverity**     |  Sempre vazio, com suporte para integração com CiscoASA. <br>Para obter detalhes sobre os valores de severidade de log, consulte o campo [LogSeverity](#logseverity) .       |
|**RemoteIP**     |     O endereço IP remoto. <br>Esse valor se baseia no campo [CommunicationDirection](#communicationdirection) , se possível.     |
|**RemotePort**     |   A porta remota. <br>Esse valor se baseia no campo [CommunicationDirection](#communicationdirection) , se possível.      |
|**SimplifiedDeviceAction**     |   Simplifica o valor de [Deviceaction](#deviceaction) para um conjunto estático de valores, mantendo o valor original no campo [deviceaction](#deviceaction) . <br>Por exemplo: `Denied`  >  `Deny` .      |
|     |         |


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [conectar sua solução externa usando o formato de evento comum](connect-common-event-format.md).
