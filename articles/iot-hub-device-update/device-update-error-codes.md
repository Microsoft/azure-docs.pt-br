---
title: Códigos de erro do cliente para atualização do dispositivo para o Hub IoT do Azure | Microsoft Docs
description: Este documento fornece uma tabela de códigos de erro do cliente para vários componentes de atualização de dispositivo.
author: chrisjlin
ms.author: lichris
ms.date: 2/18/2021
ms.topic: reference
ms.service: iot-hub-device-update
ms.openlocfilehash: dbdddc7cee0c3664a83501ba619a38e1cc44e1f3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200348"
---
# <a name="device-update-for-iot-hub-error-codes"></a>Atualização de dispositivo para códigos de erro do Hub IoT

Este documento fornece uma tabela de códigos de erro para vários componentes de atualização de dispositivo. Isso deve ser usado como uma referência para os usuários que desejam tentar analisar seus próprios códigos de erro para diagnosticar e solucionar problemas.

Há dois componentes principais do lado do cliente que podem gerar códigos de erro: o agente de atualização de dispositivo e o agente de otimização de entrega.

## <a name="device-update-agent"></a>Agente de atualização de dispositivo

### <a name="resultcode-and-extendedresultcode"></a>ResultCode e ExtendedResultCode

A atualização do dispositivo para relatórios de interface PnP principal do Hub IoT `ResultCode` e `ExtendedResultCode` , que pode ser usada para diagnosticar falhas. [Saiba mais](device-update-plug-and-play.md) sobre a interface PnP do núcleo de atualização de dispositivo.

#### <a name="resultcode"></a>ResultCode

`ResultCode` é um código de status geral e segue a Convenção de código de status http.
[Saiba mais](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) sobre códigos de status http.

#### <a name="extendedresultcode"></a>ExtendedResultCode

`ExtendedResultCode` é um inteiro com informações de erro codificadas.

Provavelmente, você verá o `ExtendedResultCode` como um inteiro assinado na interface PNP. Para decodificar o `ExtendedResultCode` , converta o inteiro assinado em hexadecimal não assinado. Somente os quatro primeiros bytes de `ExtendedResultCode` são usados e estão no formato `F` `FFFFFFF` em que o primeiro Nibble é o código de **recurso** e o restante dos bits são o código de **erro**.

**Códigos de recursos**

| Código de instalação     | Descrição  |
|-------------------|--------------|
| D                 | Erro gerado do SDK do|
| E                 | O código de erro é um errno |


Por exemplo:

`ExtendedResultCode` é `-536870781`

A representação hexadecimal não assinada de `-536870781` é `FFFFFFFF E0000083` .

| Ignorar    | Código de instalação  | Código de erro   |
|-----------|----------------|--------------|
| FFFFFFFF  | E              | 0000083      |

`0x83` em Hex está `131` em decimal, que é o valor de errno para `ENOLCK` .

## <a name="delivery-optimization-agent"></a>Agente de otimização de entrega
A tabela a seguir lista os códigos de erro pertencentes ao componente de otimização de entrega do cliente de atualização de dispositivo. O componente do é responsável por baixar o conteúdo de atualização no dispositivo IoT.

O código de erro pode ser obtido examinando as exceções geradas em resposta a uma chamada à API. Todos os códigos de erro podem ser identificados pelo prefixo 0x80D0.

| Código de erro  | Erro de cadeia de caracteres                       | Type                 | Descrição |
|-------------|------------------------------------|----------------------|-------------|
| 0x80D01001L | DO_E_NO_SERVICE                    | N/D                  | A otimização de entrega não pôde fornecer o serviço |
| 0x80D02002L | DO_E_DOWNLOAD_NO_PROGRESS          | Baixar trabalho         | O download de um arquivo não viu nenhum progresso dentro do período definido |
| 0x80D02011L | DO_E_UNKNOWN_PROPERTY_ID           | Baixar trabalho         | SetProperty () ou GetProperty () chamado com uma ID de propriedade desconhecida |
| 0x80D02012L | DO_E_READ_ONLY_PROPERTY            | Baixar trabalho         | Não é possível chamar SetProperty () em uma propriedade somente leitura |
| 0x80D02013L | DO_E_INVALID_STATE                 | Baixar trabalho         | A ação solicitada não é permitida no estado do trabalho atual. O trabalho pode ter sido cancelado ou concluído. Ele está em um estado somente leitura agora. |
| 0x80D02018L | DO_E_FILE_DOWNLOADSINK_UNSPECIFIED | Baixar trabalho         | Não é possível iniciar um download porque nenhum coletor de download (o arquivo local ou a interface de fluxo) foi especificado |
| 0x80D02200L | DO_E_DOWNLOAD_NO_URI               | Interface IDODownload| O download foi iniciado sem fornecer um URI |
| 0x80D03805L | DO_E_BLOCKED_BY_NO_NETWORK         | Condições temporárias | Download pausado devido à perda de conectividade de rede |

## <a name="device-update-content-service"></a>Serviço de conteúdo de atualização de dispositivo
A tabela a seguir lista os códigos de erro pertencentes ao componente de serviço de conteúdo do serviço de atualização de dispositivo. O componente de serviço de conteúdo é responsável por lidar com a importação de conteúdo de atualização.

| Código de erro                    | Erro de cadeia de caracteres                                                               | Próximas etapas                         |
|-------------------------------|----------------------------------------------------------------------------|------------------------------------|
| "UpdateAlreadyExists"         | Já existe uma atualização com a mesma identidade.                              | Verifique se você está importando uma atualização que ainda não foi importada para esta instância da atualização do dispositivo para o Hub IoT. |
| "DuplicateContentImport"      | Conteúdo idêntico importado simultaneamente várias vezes.                  | O mesmo que para UpdateAlreadyExists. |
| "CannotProcessImportManifest" | Erro ao processar o manifesto de importação.                                          | Consulte [importar conceitos](./import-concepts.md) e importar documentação de [atualização](./import-update.md) para a formatação apropriada do manifesto de importação. |
| "CannotDownload"              | Não é possível baixar o manifesto de importação.                                           | Verifique se a URL do arquivo de manifesto de importação ainda é válida. |
| "CannotParse"                 | Não é possível analisar o manifesto de importação.                                              | Verifique a precisão do manifesto de importação em relação ao esquema definido na documentação de [atualização de importação](./import-update.md) . |
| "UnsupportedVersion"          | Não há suporte para a versão de esquema de manifesto de importação.                           | Verifique se o manifesto de importação está usando o esquema mais recente definido na documentação de [atualização de importação](./import-update.md) . |
| "UpdateLimitExceeded"         | Erro ao importar a atualização devido ao limite excedido.                              | Você atingiu um limite para o número de provedores, nomes ou versões diferentes permitidos em sua instância de atualização de dispositivo para o Hub IoT. Exclua algumas atualizações de sua instância e tente novamente. |
| "Updateprovider"              | Não é possível importar um novo provedor de atualização.                                       | Você atingiu um limite no número de __provedores__ diferentes permitidos em sua instância de atualização de dispositivo para o Hub IOT. Exclua algumas atualizações de sua instância e tente novamente. |
| "Updatename"                  | Não é possível importar um novo nome de atualização para o provedor especificado.                | Você atingiu um limite para o número de __nomes__ diferentes permitidos em um provedor em sua instância de atualização de dispositivo para o Hub IOT. Exclua algumas atualizações de sua instância e tente novamente. |
| "UpdateVersion"               | Não é possível importar uma nova versão de atualização para o provedor e o nome especificados.    | Você atingiu um limite no número de diferentes __versões__ permitidas em um provedor e nome em sua instância de atualização de dispositivo para o Hub IOT. Exclua algumas atualizações com esse nome de sua instância e tente novamente. |
| "UpdateProviderCompatibility" | Não é possível importar o provedor de atualização adicional com a compatibilidade especificada. | Ao definir as propriedades do fabricante do dispositivo e de compatibilidade do modelo de dispositivo em um manifesto de importação, tenha em mente que a atualização do dispositivo para o Hub IoT dá suporte a uma única combinação de provedor e nome para um determinado fabricante/modelo. Isso significa que se você tentar usar as mesmas propriedades de compatibilidade de fabricante/modelo com mais de uma combinação de provedor/nome, verá esses erros. Para resolver isso, certifique-se de que todas as atualizações de um determinado dispositivo (conforme definido pelo fabricante/modelo) usem o mesmo provedor e nome. Embora não seja necessário, convém considerar tornar o provedor o mesmo que o fabricante e o mesmo nome que o modelo, apenas para simplificar. |
| "UpdateNameCompatibility"     | Não é possível importar o nome de atualização adicional com a compatibilidade especificada.     | O mesmo que para UpdateProviderCompatibility. ContentLimitNamespaceCompatibility. |
| "UpdateVersionCompatibility"  | Não é possível importar a versão de atualização adicional com a compatibilidade especificada.  | O mesmo que para UpdateProviderCompatibility. ContentLimitNamespaceCompatibility. |
| "CannotProcessUpdateFile"     | Erro ao processar o arquivo de origem.                                              |                                    |
| "ContentFileCannotDownload"   | Não é possível baixar o arquivo de origem.                                               | Verifique se a URL do arquivo (s) de atualização ainda é válida. |

**[Próxima etapa: solucionar problemas com a atualização do dispositivo](.\troubleshoot-device-update.md)**
