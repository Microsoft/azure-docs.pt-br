---
title: Disponibilidade de região e residência de dados para os Serviços de Comunicação do Azure
description: Saiba mais sobre a residência de dados e assuntos relacionados à residência de dados nos Serviços de Comunicação do Azure
author: chpalm
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: beb8b81710cf4728259a8eb1df920cd74efce3ce
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728123"
---
# <a name="region-availability-and-data-residency"></a>Disponibilidade de região e residência de dados

Os Serviços de Comunicação do Azure estão comprometidos em ajudar nossos clientes a atender aos requisitos de privacidade e de dados pessoais deles. Como desenvolvedor usando os Serviços de Comunicação com uma relação direta com os seres humanos que usam o aplicativo, você é potencialmente um controlador dos dados deles. Como os Serviços de Comunicação do Azure estão armazenando e criptografando esses dados inativos em seu nome, provavelmente somos o processador desses dados. Esta página resume como o serviço retém os dados e como você pode identificar, exportar e excluir esses dados.

## <a name="data-residency"></a>Residência de dadosResidência de dados

Ao criar um recurso dos Serviços de Comunicação, você especifica uma **geografia** (não um data center do Azure). Todos os dados armazenados pelos Serviços de Comunicação em repouso serão mantidos nessa geografia, em um data center selecionado internamente pelos Serviços de Comunicação. Os dados podem transitar ou serem processados em outras regiões geográficas. Esses pontos de extremidade globais são necessários para fornecer uma experiência de alto desempenho e baixa latência aos usuários finais, independentemente da localização deles.

## <a name="data-residency-and-events"></a>Eventos e residência de dados

Qualquer tópico do sistema de Grade de Eventos configurado com os Serviços de Comunicação do Azure será criado em uma localização global. Para dar suporte à entrega confiável, um tópico global do sistema de Grade de Eventos pode armazenar os dados de evento em qualquer data center da Microsoft. Ao configurar a Grade de Eventos com os Serviços de Comunicação do Azure, você está fornecendo os seus dados de evento para a Grade de Eventos, que é um recurso do Azure sob o seu controle. Embora os Serviços de Comunicação do Azure possam ser configurados para utilizar a Grade de Eventos do Azure, você será responsável por gerenciar o seu recurso de Grade de Eventos e os dados armazenados nele.

## <a name="relating-humans-to-azure-communication-services-identities"></a>Como relacionar pessoas às identidades dos Serviços de Comunicação do Azure

Seu aplicativo gerencia a relação entre usuários humanos e identidades do Serviço de Comunicação. Quando você quiser excluir dados de um usuário humano, deverá excluir dados que envolvam todas as identidades do Serviço de Comunicação correlacionadas ao usuário.

Há duas categorias de dados do Serviço de Comunicação:
- **Dados de API.** Esses dados são criados e gerenciados pelas APIs do Serviço de Comunicação, um exemplo típico de mensagens que são gerenciadas por mensagens de Chat por meio de APIs do Chat.
- **Logs do Azure Monitor** Esses dados são criados pelo serviço e gerenciados por meio da plataforma de dados do Azure Monitor. Esses dados incluem telemetria e métricas para ajudar você a entender o uso dos Serviços de Comunicação. Isso não é gerenciado pelas APIs do Serviço de Comunicação.

## <a name="api-data"></a>Dados de API

### <a name="identities"></a>Identidades

Os Serviços de Comunicação do Azure mantêm um diretório de identidades e usam a API [DeleteIdentity](/rest/api/communication/communicationidentity/delete) para removê-los. A exclusão de uma identidade revogará todos os tokens de acesso associados e excluirá as mensagens de chat. Para obter mais informações sobre como remover uma identidade, [confira esta página](../quickstarts/access-tokens.md).

- DeleteIdentity

### <a name="azure-resource-manager"></a>Azure Resource Manager

Usando as APIs do portal do Azure ou do Azure Resource Manager com os Serviços de Comunicação, você pode criar dados pessoais. [Use esta página para saber como gerenciar dados pessoais em sistemas do Azure Resource Manager.](../../azure-resource-manager/management/resource-manager-personal-data.md)

### <a name="telephone-number-management"></a>Gerenciamento de números de telefone

Os Serviços de Comunicação do Azure mantêm um diretório de números de telefone associados a um recurso dos Serviços de Comunicação. Use as [APIs de Administração de Número de Telefone](/rest/api/communication/phonenumberadministration) para recuperar números de telefone e excluí-los:

- `Get All Phone Numbers`
- `Release Phone Number`

### <a name="chat"></a>Chat

Os threads e as mensagens do chat são retidos até que sejam explicitamente excluídos. Um thread totalmente ocioso, sem participantes, será excluído automaticamente após 30 dias. Use [APIs de Chat](/rest/api/communication/chat/chatthread) para obter, listar, atualizar e excluir mensagens.

- `Get Thread`
- `Get Message`
- `List Messages`
- `Update Message`
- `Delete Thread`
- `Delete Message`

### <a name="sms"></a>sms

Mensagens SMS enviadas e recebidas são processadas temporariamente pelo serviço e não são retidas.

### <a name="pstn-voice-calling"></a>Chamada de voz PSTN

A comunicação de áudio e vídeo é processada rapidamente pelo serviço e nenhum dado é mantido no recurso que não sejam os logs do Azure Monitor.

### <a name="internet-voice-and-video-calling"></a>Chamadas de voz e vídeo pela Internet

A comunicação de áudio e vídeo é processada rapidamente pelo serviço e nenhum dado é mantido no recurso que não sejam os logs do Azure Monitor.

## <a name="azure-monitor-and-log-analytics"></a>Azure Monitor e Log Analytics

Os Serviços de Comunicação do Azure alimentarão o Azure Monitor dados de log para compreender a integridade operacional e a utilização do serviço. Alguns desses logs incluem identidades e números de telefone do Serviço de Comunicação como dados de campo. Para excluir dados potencialmente pessoais, [use estes procedimentos para o Azure Monitor](../../azure-monitor/logs/personal-data-mgmt.md). Talvez seja interessante configurar [o período de retenção padrão para o Azure Monitor](../../azure-monitor/logs/manage-cost-storage.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Solicitações do Titular dos Dados do Azure para GDPR e CCPA](/microsoft-365/compliance/gdpr-dsr-azure)
- [Central de Confiabilidade da Microsoft](https://www.microsoft.com/trust-center/privacy/data-location)
- [Mapa Interativo do Azure – onde estão os dados do meu cliente?](https://azuredatacentermap.azurewebsites.net/)
