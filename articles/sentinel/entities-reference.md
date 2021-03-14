---
title: Referência de tipos de entidade do Azure Sentinel | Microsoft Docs
description: Este artigo exibe os tipos de entidade Sentinela do Azure e seus identificadores necessários.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 17a4df3037f9922d92fca924de0d246458cfa08e
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102456205"
---
# <a name="azure-sentinel-entity-types-reference"></a>Referência de tipos de entidade do Azure Sentinel

## <a name="entity-types-and-identifiers"></a>Tipos de entidade e identificadores

A tabela a seguir mostra **os tipos de entidade** disponíveis atualmente para mapeamento no Azure Sentinel e os **atributos** disponíveis **como identificadores** para cada tipo de entidade, que aparece na lista suspensa **identificadores** na seção [mapeamento de entidade](map-data-fields-to-entities.md) do assistente de [regra de análise](tutorial-detect-threats-custom.md).

Cada um dos identificadores na coluna **identificadores necessários** é minimamente necessário para identificar sua entidade. No entanto, um identificador necessário não pode, por si só, ser suficiente para fornecer identificação *exclusiva* . Quanto mais identificadores forem usados, maior a probabilidade de identificação exclusiva. Você pode usar até três identificadores para um mapeamento de entidade única.

Para obter melhores resultados-para identificação exclusiva garantida, você deve usar identificadores da coluna **identificadores mais fortes** sempre que possível. O uso de vários identificadores fortes permite a correlação entre identificadores fortes de diferentes fontes de dados e esquemas. Isso, por sua vez, permite que o Azure Sentinel forneça informações mais abrangentes para uma determinada entidade.

| Tipo de entidade | Identificadores | Identificadores necessários | Identificadores mais fortes |
| - | - | - | - |
| [**Conta de usuário**](#user-account)<br>*Considerar* | Nome<br>FullName<br>Domínio do NT<br>DnsDomain<br>UPNSuffix<br>Sid<br>AadTenantId<br>AadUserId<br>PUID<br>IsDomainJoined<br>DisplayName<br>ObjectGuid | FullName<br>Sid<br>Nome<br>AadUserId<br>PUID<br>ObjectGuid | Nome + domínio do NT<br>Nome + UPNSuffix<br>AADUserId<br>Sid |
| [**Host**](#host) | DnsDomain<br>Domínio do NT<br>HostName<br>FullName<br>NetBiosName<br>Azureid<br>OMSAgentID<br>OSFamily<br>OSVersion<br>IsDomainJoined | FullName<br>HostName<br>NetBiosName<br>Azureid<br>OMSAgentID | Nome do host + domínio do NT<br>Nome do host + DnsDomain<br>NetBiosName + domínio do NT<br>NetBiosName + DnsDomain<br>Azureid<br>OMSAgentID |
| [**Endereço IP**](#ip-address)<br>*IP* | Endereço | Endereço | |
| [**Malware**](#malware) | Nome<br>Categoria | Nome | |
| [**Arquivo**](#file) | Diretório<br>Nome | Nome | |
| [**Processo**](#process) | ProcessId<br>CommandLine<br>ElevationToken<br>CreationTimeUtc | CommandLine<br>ProcessId | |
| [**Aplicativo de nuvem**](#cloud-application)<br>*(CloudApplication)* | AppId<br>Nome<br>InstanceName | AppId<br>Nome | |
| [**Nome de domínio**](#domain-name)<br>*DNS* | DomainName | DomainName | |
| [**Recurso do Azure**](#azure-resource) | ResourceId | ResourceId | |
| [**Hash do arquivo**](#file-hash)<br>*FileHash* | Algoritmo<br>Valor | Algoritmo + valor | |
| [**Chave do registro**](#registry-key) | Hive<br>Chave | Hive<br>Chave | Hive + chave |
| [**Valor do Registro**](#registry-value) | Nome<br>Valor<br>ValueType | Nome | |
| [**Grupo de segurança**](#security-group) | DistinguishedName<br>SID<br>ObjectGuid | DistinguishedName<br>SID<br>ObjectGuid | |
| [**URL**](#url) | Url | Url | |
| [**Dispositivo IoT**](#iot-device) | IoTHub<br>DeviceId<br>DeviceName<br>IoTSecurityAgentId<br>DeviceType<br>Fonte<br>SourceRef<br>Fabricante<br>Modelar<br>OperatingSystem<br>IpAddress<br>MacAddress<br>Protocolos<br>SerialNumber | IoTHub<br>DeviceId | IoTHub + DeviceID |
| [**Nas**](#mailbox) | MailboxPrimaryAddress<br>DisplayName<br>Sufixo<br>ExternalDirectoryObjectId<br>RiskLevel | MailboxPrimaryAddress | |
| [**Cluster de emails**](#mail-cluster) | NetworkMessageIds<br>CountByDeliveryStatus<br>CountByThreatType<br>CountByProtectionStatus<br>Ameaças<br>Consulta<br>Querytime<br>MailCount<br>IsVolumeAnomaly<br>Fonte<br>ClusterSourceIdentifier<br>ClusterSourceType<br>ClusterQueryStartTime<br>ClusterQueryEndTime<br>ClusterGroup | Consulta<br>Fonte | Consulta + origem |
| [**Mensagem de email**](#mail-message) | Destinatário<br>URLs<br>Ameaças<br>Remetente<br>P1Sender<br>P1SenderDisplayName<br>P1SenderDomain<br>SenderIP<br>P2Sender<br>P2SenderDisplayName<br>P2SenderDomain<br>Recebi<br>NetworkMessageId<br>InternetMessageId<br>Assunto<br>BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5<br>AntispamDirection<br>Entregaaction<br>DeliveryLocation<br>Linguagem<br>ThreatDetectionMethods | NetworkMessageId<br>Destinatário | NetworkMessageId + destinatário |
| [**Email de envio**](#submission-mail) | Submissão<br>SubmissionDate<br>Emissor<br>NetworkMessageId<br>Timestamp<br>Destinatário<br>Remetente<br>SenderIp<br>Assunto<br>ReportType | Submissão<br>NetworkMessageId<br>Destinatário<br>Emissor |  |
|

## <a name="entity-type-schemas"></a>Esquemas de tipo de entidade

Veja a seguir uma visão mais detalhada dos esquemas completos de cada tipo de entidade. Você observará que muitos desses esquemas incluem links para outros tipos de entidade – por exemplo, o esquema de conta de usuário inclui um link para o tipo de entidade de host, uma vez que um atributo de uma conta de usuário é o host em que ele está definido. Essas entidades vinculadas externamente não podem ser usadas como identificadores para mapeamento de entidade, mas são muito úteis para fornecer uma imagem completa das entidades em páginas de entidade e o grafo de investigação.

> [!NOTE]
> Um ponto de interrogação após o valor na coluna **tipo** indica que o campo é anulável.

## <a name="user-account"></a>Conta de usuário

*Nome da entidade: conta*

| Campo | Type | Descrição |
| ----- | ---- | ----------- |
| Type | String | considerar |
| Nome | String | O nome da conta. Esse campo deve conter apenas o nome sem nenhum domínio adicionado a ele. |
| *FullName* | *N/A* | *Não faz parte do esquema, incluído para compatibilidade com versões anteriores com a versão antiga do mapeamento de entidade.*
| Domínio do NT | String | O nome de domínio NETBIOS como ele aparece no formato de alerta – domínio \ nome_de_usuário Exemplos: Finance, Autoridade NT |
| DnsDomain | String | O nome DNS do domínio totalmente qualificado. Exemplos: finance.contoso.com |
| UPNSuffix | String | O sufixo do nome principal do usuário para a conta. Em alguns casos, esse também é o nome de domínio. Exemplos: contoso.com |
| Host | Entidade | O host que contém a conta, se for uma conta local. |
| Sid | String | O identificador de segurança da conta, como S-1-5-18. |
| AadTenantId | Volume? | A ID de locatário do Azure AD, se conhecida. |
| AadUserId | Volume? | A ID de objeto da conta do Azure AD, se conhecida. |
| PUID | Volume? | A ID de usuário do Azure AD Passport, se conhecida. |
| IsDomainJoined | Bool? | Determina se esta é uma conta de domínio. |
| DisplayName | String | O nome de exibição da conta. |
| ObjectGuid | Volume? | O atributo objectGUID é um atributo de valor único que é o identificador exclusivo do objeto, atribuído por Active Directory. |
|

Identificadores fortes de uma entidade de conta:

- Nome + UPNSuffix
- AadUserId
- Sid + host (necessário para SIDs de contas Builtin)
- Sid (exceto para SIDs de contas Builtin)
- Nome + domínio do NT (a menos que domínio do NT seja um domínio interno, por exemplo "grupo de trabalho")
- Nome + host (se domínio do NT for um domínio interno, por exemplo "grupo de trabalho")
- Nome + DnsDomain
- PUID
- ObjectGuid

Identificadores fracos de uma entidade de conta:

- Nome

## <a name="host"></a>Host

| Campo | Type | Descrição |
| ----- | ---- | ----------- |
| Type | String | hospedeira |
| DnsDomain | String | O domínio DNS ao qual este host pertence. Deve conter o sufixo DNS completo para o domínio, se conhecido. |
| Domínio do NT | String | O domínio do NT ao qual este host pertence. |
| HostName | String | O nome do host sem o sufixo de domínio. |
| *FullName* | *N/A* | *Não faz parte do esquema, incluído para compatibilidade com versões anteriores com a versão antiga do mapeamento de entidade.*
| NetBiosName | String | O nome do host (anterior ao Windows 2000). |
| IoTDevice | Entidade | A entidade do dispositivo IoT (se esse host representar um dispositivo IoT). |
| Azureid | String | A ID de recurso do Azure da VM, se conhecida. |
| OMSAgentID | String | A ID do agente do OMS, se o host tiver o agente do OMS instalado. |
| OSFamily | Enumera? | Um dos seguintes valores: <li>Linux<li>Windows<li>Android<li>IOS |
| OSVersion | String | Uma representação de texto livre do sistema operacional.<br>Esse campo destina-se a manter versões específicas, que são mais refinadas do que OSFamily, ou valores futuros sem suporte pela enumeração OSFamily. |
| IsDomainJoined | Bool | Determina se este host pertence a um domínio. |
|

Identificadores fortes de uma entidade de host:
- Nome do host + domínio do NT
- Nome do host + DnsDomain
- NetBiosName + domínio do NT
- NetBiosName + DnsDomain
- Azureid
- OMSAgentID
- IoTDevice (sem suporte para mapeamento de entidade)

Identificadores fracos de uma entidade de host:
- HostName
- NetBiosName

## <a name="ip-address"></a>Endereço IP

*Nome da entidade: IP*

| Campo | Type | Descrição |
| ----- | ---- | ----------- |
| Type | String | IP |
| Endereço | String | O endereço IP como cadeia de caracteres, por exemplo, 127.0.0.1 (em IPv4 ou IPv6). |
| Local | GeoLocation | O contexto de localização geográfica anexado à entidade IP. |
|

Identificadores fortes de uma entidade IP:
- Endereço

## <a name="malware"></a>Malware

| Campo | Type | Descrição |
| ----- | ---- | ----------- |
| Type | String | ataque |
| Nome | String | O nome do malware pelo fornecedor, como `Win32/Toga!rfn` . |
| Categoria | String | A categoria de malware pelo fornecedor, por exemplo, cavalo de Troia. |
| Arquivos | Lista\<Entity> | Lista de entidades de arquivo vinculadas nas quais o malware foi encontrado. Pode conter as entidades de arquivo embutidas ou como referência.<br>Consulte a entidade arquivo para obter detalhes adicionais sobre a estrutura. |
| Processos | Lista\<Entity> | Lista de entidades de processo vinculadas nas quais o malware foi encontrado. Isso geralmente seria usado quando o alerta fosse disparado na atividade de arquivo.<br>Consulte a entidade [processo](#process) para obter detalhes adicionais sobre a estrutura. |
|

Identificadores fortes de uma entidade de malware:

- Nome + categoria

## <a name="file"></a>Arquivo

| Campo | Type | Descrição |
| ----- | ---- | ----------- |
| Type | String | Grupo |
| Diretório | String | O caminho completo para o arquivo. |
| Nome | String | O nome do arquivo sem o caminho (alguns alertas podem não incluir o caminho). |
| Host | Entidade | O host no qual o arquivo foi armazenado. |
| Filehashs | Listar &lt; entidade&gt; | Os hashes de arquivo associados a este arquivo. |
|

Identificadores fortes de uma entidade de arquivo:
- Nome + diretório
- Nome + FileHash
- Nome + diretório + FileHash

## <a name="process"></a>Processo

| Campo | Type | Descrição |
| ----- | ---- | ----------- |
| Type | String | Process |
| ProcessId | String | A ID do processo. |
| CommandLine | String | A linha de comando usada para criar o processo. |
| ElevationToken | Enumera? | O token de elevação associado ao processo.<br>Valores possíveis:<li>TokenElevationTypeDefault<li>TokenElevationTypeFull<li>TokenElevationTypeLimited |
| CreationTimeUtc | DateTime? | A hora em que o processo começou a ser executado. |
| ImageFile | Entidade (arquivo) | Pode conter a entidade de arquivo embutida ou como referência.<br>Consulte a entidade arquivo para obter detalhes adicionais sobre a estrutura. |
| Conta | Entidade | A conta que executa os processos.<br>Pode conter a entidade de [conta](#user-account) embutida ou como referência.<br>Consulte a entidade da [conta](#user-account) para obter detalhes adicionais sobre a estrutura. |
| ParentProcess | Entidade (processo) | A entidade de processo pai. <br>Pode conter dados parciais, ou seja, apenas o PID. |
| Host | Entidade | O host no qual o processo estava em execução. |
| LogonSession | Entidade (HostLogonSession) | A sessão na qual o processo estava em execução. |
|

Identificadores fortes de uma entidade de processo:

- Host + ProcessId + CreationTimeUtc
- Host + ParentProcessId + CreationTimeUtc + linha de comando
- Host + ProcessId + CreationTimeUtc + ImageFile
- Host + ProcessId + CreationTimeUtc + ImageFile. FileHash

Identificadores fracos de uma entidade de processo:

- ProcessId + CreationTimeUtc + linha de comando (e nenhum host)
- ProcessId + CreationTimeUtc + ImageFile (e sem host)

## <a name="cloud-application"></a>Aplicativo de nuvem

*Nome da entidade: CloudApplication*

| Campo | Type | Descrição |
| ----- | ---- | ----------- |
| Type | String | "aplicativo de nuvem" |
| AppId | int | O identificador técnico do aplicativo. Deve ser um dos valores definidos na lista de [identificadores de aplicativo de nuvem](#cloud-application-identifiers). O valor do campo AppId é opcional. |
| Nome | String | O nome do aplicativo de nuvem relacionado. O valor do nome do aplicativo é opcional. |
| InstanceName | String | O nome da instância definida pelo usuário do aplicativo de nuvem. Geralmente, ele é usado para distinguir entre vários aplicativos do mesmo tipo que um cliente tem. |
|

Identificadores fortes de uma entidade de aplicativo de nuvem:
 - AppId (sem InstanceName)
 - Nome (sem InstanceName)
 - AppId + InstanceName
 - Nome + InstanceName

## <a name="domain-name"></a>Nome de domínio

*Nome da entidade: DNS*

| Campo | Type | Descrição |
| ----- | ---- | ----------- |
| Type | String | DNS |
| DomainName | String | O nome do registro DNS associado ao alerta. |
| IpAddress | Listar &lt; entidade (IP)&gt; | Entidades correspondentes aos endereços IP resolvidos. |
| DnsServerIp | Entidade (IP) | Uma entidade que representa o servidor DNS resolvendo a solicitação. |
| HostIpAddress | Entidade (IP) | Uma entidade que representa o cliente de solicitação DNS. |
|

Identificadores fortes de uma entidade DNS:
- DomainName + DnsServerIp + HostIpAddress

Identificadores fracos de uma entidade DNS:
- Nome_do_domínio + HostIpAddress

## <a name="azure-resource"></a>Recursos do Azure

| Campo | Type | Descrição |
| ----- | ---- | ----------- |
| Type | String | ' Azure-Resource ' |
| ResourceId | String | A ID de recurso do Azure do recurso. |
| SubscriptionId | String | A ID da assinatura do recurso. |
| TryGetResourceGroup | Bool | O valor do grupo de recursos, se existir. |
| TryGetProvider | Bool | O valor do provedor, se ele existir. |
| TryGetName | Bool | O valor de nome, se existir. |
|

Identificadores fortes de uma entidade de recursos do Azure:
- ResourceId

## <a name="file-hash"></a>Hash do arquivo

*Nome da entidade: FileHash*

| Campo | Type | Descrição |
| ----- | ---- | ----------- |
| Type | String | FileHash |
| Algoritmo | Enumeração | O tipo de algoritmo de hash. Valores possíveis:<li>Unknown<li>MD5<li>SHA1<li>SHA256<li>SHA256AC |
| Valor | String | O valor de hash. |
|

Identificadores fortes de uma entidade de hash de arquivo:
- Algoritmo + valor

## <a name="registry-key"></a>Chave do Registro

*Nome da entidade: RegistryKey*

| Campo | Type | Descrição |
| ----- | ---- | ----------- |
| Type | String | ' registry-key ' |
| Hive | Enumera? | Um dos seguintes valores:<li>HKEY_LOCAL_MACHINE<li>HKEY_CLASSES_ROOT<li>HKEY_CURRENT_CONFIG<li>HKEY_USERS<li>HKEY_CURRENT_USER_LOCAL_SETTINGS<li>HKEY_PERFORMANCE_DATA<li>HKEY_PERFORMANCE_NLSTEXT<li>HKEY_PERFORMANCE_TEXT<li>HKEY_A<li>HKEY_CURRENT_USER |
| Chave | String | O caminho da chave do registro. |
|

Identificadores fortes de uma entidade de chave do registro:
- Hive + chave

## <a name="registry-value"></a>Valor do Registro

*Nome da entidade: REGISTRYVALUE*

| Campo | Type | Descrição |
| ----- | ---- | ----------- |
| Type | String | ' registro-valor ' |
| Chave | Entidade (RegistryKey) | A entidade da chave do registro. |
| Nome | String | O nome do valor do registro. |
| Valor | String | Representação formatada pela cadeia de caracteres dos dados do valor. |
| ValueType | Enumera? | Um dos seguintes valores:<li>String<li>Binário<li>DWord<li>QWORD<li>MultiString<li>ExpandString<li>Nenhum<li>Unknown<br>Os valores devem estar em conformidade com a enumeração Microsoft. Win32. RegistryValueKind. |
|

Identificadores fortes de uma entidade de valor do registro:
- Chave + nome

Identificadores fracos de uma entidade de valor do registro:
- Nome (sem chave)

## <a name="security-group"></a>Grupo de segurança

*Nome da entidade: o The Security*

| Campo | Type | Descrição |
| ----- | ---- | ----------- |
| Type | String | ' Security-Group ' |
| DistinguishedName | String | O nome diferenciado do grupo. |
| SID | String | O atributo SID é um atributo de valor único que especifica o SID (identificador de segurança) do grupo. |
| ObjectGuid | Volume? | O atributo objectGUID é um atributo de valor único que é o identificador exclusivo do objeto, atribuído por Active Directory. |
|

Identificadores fortes de uma entidade de grupo de segurança:
 - DistinguishedName
 - SID
 - ObjectGuid

## <a name="url"></a>URL

| Campo | Type | Descrição |
| ----- | ---- | ----------- |
| Type | String | URL |
| Url | Uri | Uma URL completa à qual a entidade aponta. |
|

Identificadores fortes de uma entidade de URL:
- URL (quando uma URL absoluta)

Identificadores fracos de uma entidade de URL:
- URL (quando uma URL relativa)

## <a name="iot-device"></a>Dispositivo IoT

*Nome da entidade: IoTDevice*

| Campo | Type | Descrição |
| ----- | ---- | ----------- |
| Type | String | 'iotdevice' |
| IoTHub | Entidade (AzureResource) | A entidade AzureResource que representa o Hub IoT ao qual o dispositivo pertence. |
| DeviceId | String | A ID do dispositivo no contexto do Hub IoT. |
| DeviceName | String | O nome amigável do dispositivo. |
| IoTSecurityAgentId | Volume? | A ID do agente do *defender para IOT* em execução no dispositivo. |
| DeviceType | String | O tipo do dispositivo (' sensor de temperatura ', ' freezer ', ' turbinas de vento ', etc.). |
| Fonte | String | A origem (Microsoft/fornecedor) da entidade do dispositivo. |
| SourceRef | Entidade (URL) | Uma referência de URL para o item de origem no qual o dispositivo é gerenciado. |
| Fabricante | String | O fabricante do dispositivo. |
| Modelar | String | O modelo do dispositivo. |
| OperatingSystem | String | O sistema operacional que o dispositivo está executando. |
| IpAddress | Entidade (IP) | O endereço IP atual do dispositivo. |
| MacAddress | String | O endereço MAC do dispositivo. |
| Protocolos | Cadeia de caracteres da lista &lt;&gt; | Uma lista de protocolos com suporte no dispositivo. |
| SerialNumber | String | O número de série do dispositivo. |
|

Identificadores fortes de uma entidade de dispositivo IoT:
- IoTHub + DeviceID

Identificadores fracos de uma entidade de dispositivo IoT:
- DeviceID (sem IoTHub)

## <a name="mailbox"></a>Mailbox

| Campo | Type | Descrição |
| ----- | ---- | ----------- |
| Type | String | nas |
| MailboxPrimaryAddress | String | O endereço principal da caixa de correio. |
| DisplayName | String | O nome de exibição da caixa de correio. |
| Sufixo | String | O UPN da caixa de correio. |
| RiskLevel | Enumera? | O nível de risco desta caixa de correio. Valores possíveis:<li>Nenhum<li>Baixo<li>Médio<li>Alto |
| ExternalDirectoryObjectId | Volume? | O identificador AzureAD da caixa de correio. Semelhante ao AadUserId na entidade Account, mas essa propriedade é específica para o objeto Mailbox no lado do escritório. |
|

Identificadores fortes de uma entidade de caixa de correio:
- MailboxPrimaryAddress

## <a name="mail-cluster"></a>Cluster de emails

*Nome da entidade: MailCluster*

> [!NOTE]
> **MDO**  =  **Microsoft defender para Office 365**, anteriormente conhecido como O365 ATP (proteção avançada contra ameaças) do Office 365.

| Campo | Type | Descrição |
| ----- | ---- | ----------- |
| Type | String | ' mail-cluster ' |
| NetworkMessageIds | &lt;Cadeia de caracteres IList&gt; | As IDs de mensagem de email que fazem parte do cluster de email. |
| CountByDeliveryStatus | &lt;Cadeia de caracteres IDictionary, int&gt; | Contagem de mensagens de email por representação de cadeia de caracteres DeliveryStatus. |
| CountByThreatType | &lt;Cadeia de caracteres IDictionary, int&gt; | Contagem de mensagens de email por representação de cadeia de caracteres Threattype. |
| CountByProtectionStatus | &lt;Cadeia de caracteres IDictionary, longa&gt; | Contagem de mensagens de email por status de proteção contra ameaças. |
| Ameaças | &lt;Cadeia de caracteres IList&gt; | As ameaças de mensagens de email que fazem parte do cluster de email. |
| Consulta | String | A consulta que foi usada para identificar as mensagens do cluster de email. |
| Querytime | DateTime? | O tempo de consulta. |
| MailCount | Inteiro? | O número de mensagens de email que fazem parte do cluster de email. |
| IsVolumeAnomaly | Bool? | Determina se este é um cluster de email de anomalias de volume. |
| Fonte | String | A origem do cluster de emails (o padrão é ' O365 ATP '). |
| ClusterSourceIdentifier | String | A ID de mensagem de rede do email que é a origem deste cluster de email. |
| ClusterSourceType | String | O tipo de origem do cluster de email. Isso é mapeado para a configuração MailClusterSourceType de MDO (consulte a observação acima). |
| ClusterQueryStartTime | DateTime? | Hora de início do cluster – usada como hora de início para a consulta de contagens de cluster. Normalmente, datas até a hora de término menos a configuração DaysToLookBack de MDO (consulte a observação acima). |
| ClusterQueryEndTime | DateTime? | Hora de término do cluster – usada como hora de término da consulta de contagens de cluster. Geralmente, o tempo de recebimento dos dados de email. |
| ClusterGroup | String | Corresponde à chave de consulta Kusto usada em MDO (consulte a observação acima). |
|

Identificadores fortes de uma entidade de cluster de email:
- Consulta + origem

## <a name="mail-message"></a>Mensagem de email

*Nome da entidade: MailMessage*

| Campo | Type | Descrição |
| ----- | ---- | ----------- |
| Type | String | ' email-mensagem ' |
| Arquivos | &lt;Arquivo IList&gt; | As entidades de arquivo dos anexos desta mensagem de email. |
| Destinatário | String | O destinatário desta mensagem de email. No caso de vários destinatários, a mensagem de email é copiada e cada cópia tem um destinatário. |
| URLs | &lt;Cadeia de caracteres IList&gt; | As URLs contidas nesta mensagem de email. |
| Ameaças | &lt;Cadeia de caracteres IList&gt; | As ameaças contidas nesta mensagem de email. |
| Remetente | String | O endereço de email do remetente. |
| P1Sender | String | ID de email do usuário (delegado) que enviou este email "em nome do usuário P2 (primário)". Se o email não for enviado pelo delegado, esse valor será igual a P2Sender. |
| P1SenderDisplayName | String | Nome de exibição do usuário (delegado) que enviou este email "em nome do usuário P2 (primário)". Representado no cabeçalho de email pela propriedade "OnbehalfofSenderDisplayName". |
| P1SenderDomain | String | Domínio de email do usuário (delegado) que enviou este email "em nome do usuário P2 (primário)". Se o email não for enviado pelo delegado, esse valor será igual a P2SenderDomain. |
| P2Sender | String | Email do usuário (primário) em nome do qual este email foi enviado. |
| P2SenderDisplayName | String | Nome de exibição do usuário (primário) em nome do qual este email foi enviado. Se o email não for enviado pelo delegado, isso representará o nome de exibição do remetente. |
| P2SenderDomain | String | Domínio de email do usuário (primário) em nome do qual este email foi enviado. Se o email não for enviado pelo delegado, isso representará o domínio do remetente. |
| SenderIP | String | O endereço IP do remetente. |
| Recebi | Datetime | A data de recebimento desta mensagem. |
| NetworkMessageId | Volume? | A ID da mensagem de rede desta mensagem de email. |
| InternetMessageId | String | A ID de mensagem da Internet desta mensagem de email. |
| Assunto | String | O assunto desta mensagem de email. |
| BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5 | UInt? | Usado pelo Microsoft defender para Office 365 para localizar correspondências ou mensagens de email semelhantes. |
| AntispamDirection | Enumera? | A direcionalidade desta mensagem de email. Valores possíveis:<li>Unknown<li>Entrada<li>Saída<li>IntraOrg (interno) |
| Entregaaction | Enumera? | A ação de entrega desta mensagem de email. Valores possíveis:<li>Unknown<li>DeliveredAsSpam<li>Entregue<li>Bloqueado<li>Substituído |
| DeliveryLocation | Enumera? | O local de entrega desta mensagem de email. Valores possíveis:<li>Unknown<li>Caixa de Entrada<li>JunkFolder<li>DeletedFolder<li>Quarentena<li>Externo<li>Com falha<li>Dropped<li>Encaminhadas |
| Linguagem | String | O idioma no qual o conteúdo do email é gravado. |
| ThreatDetectionMethods | &lt;Cadeia de caracteres IList&gt; | A lista de métodos de detecção de ameaças aplicada neste email. |
|

Identificadores fortes de uma entidade de mensagem de email:
- NetworkMessageId + destinatário

## <a name="submission-mail"></a>Email de envio

*Nome da entidade: SubmissionMail*

| Campo | Type | Descrição |
| ----- | ---- | ----------- |
| Type | String | 'SubmissionMail' |
| Submissão | Volume? | A ID de envio. |
| SubmissionDate | DateTime? | Data e hora relatadas para este envio. |
| Emissor | String | O endereço de email do emissor. |
| NetworkMessageId | Volume? | A ID de mensagem de rede do email ao qual o envio pertence. |
| Timestamp | DateTime? | O carimbo de data/hora quando a mensagem é recebida (email). |
| Destinatário | String | O destinatário do email. |
| Remetente | String | O remetente do email. |
| SenderIp | String | O IP do remetente. |
| Assunto | String | O assunto do email de envio. |
| ReportType | String | O tipo de envio para a instância especificada. Isso é mapeado para lixo eletrônico, Phish, malware ou não-lixo eletrônico. |
|

Identificadores fortes de uma entidade SubmissionMail:
- Submissão, emissor, NetworkMessageId, destinatário

## <a name="cloud-application-identifiers"></a>Identificadores de aplicativo de nuvem

A lista a seguir define identificadores para aplicativos de nuvem conhecidos. O valor da ID do aplicativo é usado como um identificador de entidade de [aplicativo de nuvem](#cloud-application) .

|ID do aplicativo|Nome|
|------|----|
|10026|DocuSign|
|10395|Anaplan|
|10489|Box|
|10549|Cisco Webex|
|10618|Atlassian|
|10915|Cornerstone OnDemand|
|10921|Zendesk|
|10980|Okta|
|11042|Software Jive|
|11114|Salesforce|
|11161|Office 365|
|11162|Microsoft OneNote online|
|11394|Microsoft Online Services|
|11522|Yammer|
|11599|Amazon Web Services|
|11627|Dropbox|
|11713|Expensify|
|11770|G Suite|
|12005|SuccessFactors|
|12260|Microsoft Azure|
|12275|Workday|
|13843|LivePerson|
|13979|Concur|
|14509|ServiceNow|
|15570|Tableau|
|15600|Microsoft OneDrive for Business|
|15782|Citrix ShareFile|
|17152|Amazon|
|17865|Ariba Inc.|
|18432|Zscaler|
|19688|Xactly|
|20595|Microsoft Cloud App Security|
|20892|Microsoft SharePoint Online|
|20893|Microsoft Exchange Online|
|20940|Active Directory|
|20941|Adallom CPanel|
|22110|Google Cloud Platform|
|22930|Gmail|
|23004|Ciclo de vida do Autodesk Fusion|
|23043|Margem de atraso|
|23233|Microsoft Office Online|
|25275|Microsoft Skype for Business|
|25988|Google Docs|
|26055|Centro de administração do Microsoft Office 365|
|26060|Engrenagens de OPSWAT|
|26061|Microsoft Word online|
|26062|Microsoft PowerPoint online|
|26063|Microsoft Excel Online|
|26069|Google Drive|
|26206|Workiva|
|26311|Microsoft Dynamics|
|26318|Microsoft Azure AD|
|26320|Microsoft Office Sway|
|26321|Microsoft Delve|
|26324|Microsoft Power BI|
|27548|Microsoft Forms|
|27592|Microsoft Flow|
|27593|Microsoft PowerApps|
|28353|Workplace by Facebook|
|28373|Emulador de proxy CAS|
|28375|Microsoft Teams|
|32780|Microsoft Dynamics 365|
|33626|Google|
|34127|Microsoft AppSource|
|34667|HighQ|
|35395|Talento do Microsoft Dynamics|
|

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu sobre a estrutura, os identificadores e o esquema de entidade no Azure Sentinel.

Saiba mais sobre [entidades](entities-in-azure-sentinel.md) e [mapeamento de entidade](map-data-fields-to-entities.md). 
