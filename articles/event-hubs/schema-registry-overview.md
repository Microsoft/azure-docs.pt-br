---
title: Registro de Esquema do Azure nos Hubs de Eventos (versão prévia)
description: Neste artigo, você terá uma visão geral da compatibilidade do Registro de Esquema com os Hubs de Eventos do Azure (Versão Prévia).
ms.topic: overview
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: a876651b76aa259754623854b8fc4a7c6c8a939e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92330488"
---
# <a name="azure-schema-registry-in-event-hubs-preview"></a>Registro de Esquema do Azure nos Hubs de Eventos (versão prévia)
Em muitos cenários de streaming de eventos e mensagens, o conteúdo tem dados estruturados que estão sendo serializados ou desserializados com um formato controlado por esquema, como o Apache Avro. Tanto os remetentes quanto os destinatários podem querer validar a integridade dos dados com um documento de esquema, como o esquema JSON. Para formatos controlados por esquema, disponibilizar o esquema para o consumidor da mensagem é um pré-requisito para que ele consiga desserializar os dados. 

O **Registro de Esquema do Azure** é um recurso dos Hubs de Eventos que funciona como um repositório central para documentos de esquema para aplicativos voltados para mensagens e controlados por eventos. É uma ferramenta flexível para aplicativos de consumidor e produtor trocarem dados sem precisar gerenciar e compartilhar o esquema entre si e evoluir em taxas diferentes. O Registro de Esquema também conta com uma estrutura de governança simples para esquemas reutilizáveis e define a relação entre os esquemas por meio de um constructo de agrupamento (grupos de esquemas).

> [!NOTE]
> - O recurso de **Registro de Esquema** está em **versão prévia** no momento e não é recomendado para cargas de trabalho de produção.
> - O recurso está disponível somente nas camadas de serviço **Standard** e **Dedicado**, e não na **Básica**.

Com estruturas de serialização controladas por esquema, como o Apache Avro, externalizar os metadados de serialização em esquemas compartilhados também pode ajudar a reduzir drasticamente a sobrecarga por mensagem de informações de tipo e nomes de campo incluídos em cada conjunto de dados. Esse é o caso dos formatos marcados, como o JSON. Ter esquemas armazenados junto com os eventos e dentro da infraestrutura de eventos garante que os metadados necessários para serialização/desserialização sempre estejam ao alcance e os esquemas não sejam colocados no lugar incorreto. 

## <a name="event-hubs-namespace"></a>Namespace do Hubs de Eventos
Um namespace dos Hubs de Eventos agora pode hospedar grupos de esquema junto com hubs de eventos (ou tópicos do Kafka). É possível hospedar um registro de esquema e ter vários grupos de esquemas. Embora esteja hospedado nos Hubs de Eventos do Azure, o registro de esquema pode ser usado universalmente com todos os serviços de mensagem do Azure e outros agentes de eventos ou mensagens. Cada um desses grupos de esquema é um repositório separado e seguro para um conjunto de esquemas. É possível alinhar os grupos com um aplicativo específico ou uma unidade organizacional. 

## <a name="schema-groups"></a>Grupos de esquemas
O grupo de esquemas é um grupo lógico de esquemas semelhantes baseado nos seus critérios de negócios. Um grupo de esquemas pode conter várias versões de um esquema. Em um grupo de esquemas, a configuração de imposição de compatibilidade ajuda a garantir que as versões mais recentes de esquemas sejam compatíveis com as anteriores.

O limite de segurança imposto pelo mecanismo de agrupamento ajuda a garantir que segredos comerciais não vazem acidentalmente por meio dos metadados em situações em que o namespace é compartilhado entre vários parceiros. Também permite que os proprietários de aplicativo gerenciem os esquemas independentemente de outros aplicativos que compartilham o mesmo namespace.


## <a name="schemas"></a>Esquemas
Os esquemas definem o contrato entre os produtores e os consumidores. Um esquema definido em um registro de esquema dos Hubs de Eventos ajuda a gerenciar o contrato fora dos dados de evento. Portanto, elimina a sobrecarga de conteúdo. Um esquema tem nome, tipo (por exemplo, registro, matriz etc.), modo de compatibilidade (nenhum, com versões posteriores, com versões anteriores, total) e tipo de serialização (somente o Avro por enquanto). É possível criar várias versões de um esquema, bem como recuperar e usar uma versão específica de um. 

## <a name="client-sdks"></a>SDKs do cliente
É possível usar uma das bibliotecas a seguir que incluem um serializador Avro para serializar e desserializar conteúdos com identificadores de esquema do Registro de Esquema e dados codificados no Avro.

- [.NET – Microsoft.Azure.Data.SchemaRegistry.ApacheAvro](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro)
- [Java – azure-data-schemaregistry-avro](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/schemaregistry/azure-data-schemaregistry-avro/)
- [Python – azure-schemaregistry-avroserializer](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer)
- [JavaScript – @azure/schema-registry-avro](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro)
- [Apache Kafka](https://github.com/Azure/azure-schema-registry-for-kafka/) – Execute serializadores e desserializadores Apache Avro integrados ao Kafka e compatíveis com o Registro de Esquema do Azure. O serializador de cliente Apache Kafka do cliente Java para o Registro de Esquema do Azure pode ser usado em cenários do Apache Kafka e com implantações ou serviços de nuvem baseados no Apache Kafka®. 

A imagem seguinte mostra o fluxo de informações do registro de esquema com os Hubs de Eventos: 

:::image type="content" source="./media/schema-registry-overview/flow-diagram.png" alt-text="Fluxograma":::

## <a name="standard-vs-dedicated-limits"></a>Limites – Standard vs. Dedicado
Para os limites (por exemplo, o número de grupos de esquemas em um namespace) que são iguais e diferentes para as camadas de serviço Standard e Dedicado dos Hubs de Eventos, confira [Limites do Registro de Esquema](../azure-resource-manager/management/azure-subscription-service-limits.md#schema-registry-limitations)

## <a name="azure-role-based-access-control"></a>Controle de acesso baseado em função do Azure
Ao acessar o Registro de esquema programaticamente, você precisa registrar um aplicativo no Azure AD (Azure Active Directory) e adicionar a entidade de segurança dele a uma das funções do Azure RBAC (controle de acesso baseado em função do Azure):

| Função | Descrição | 
| ---- | ----------- | 
| Proprietário | Ler, gravar e excluir grupos e esquemas do Registro de Esquema. |
| Colaborador | Ler, gravar e excluir grupos e esquemas do Registro de Esquema. |
| [Leitor do Registro de Esquema (Versão Prévia)](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Ler e listar os grupos e os esquemas do Registro de Esquema. |
| [Colaborador do Registro de Esquema (Versão Prévia)](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Ler, gravar e excluir grupos e esquemas do Registro de Esquema. |

Para ver instruções sobre como criar o registro de um aplicativo usando o portal do Azure, confira [Registrar um aplicativo com o Azure AD](../active-directory/develop/quickstart-register-app.md). Anote a ID do cliente (ID do aplicativo), a ID do locatário e o segredo a ser usado no código. 

## <a name="next-steps"></a>Próximas etapas

- Para saber como criar um registro de esquema no portal do Azure, confira [Criar um registro de esquema dos Hubs de Eventos no portal do Azure](create-schema-registry.md).
- Veja os exemplos de **biblioteca de clientes Avro do Registro de Esquema** a seguir.
    - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro/tests/Samples)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/schemaregistry/azure-data-schemaregistry-avro/src/samples)
    - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro/samples )
    - [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer/samples )
    - [Integração do Kafka ao Avro para o Registro de Esquema do Azure](https://github.com/Azure/azure-schema-registry-for-kafka/tree/master/csharp/avro/samples)
