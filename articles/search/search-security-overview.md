---
title: Segurança e privacidade de dados – Azure Search
description: O Azure Search está em conformidade com as certificações SOC 2, HIPAA e outras. Conexão e criptografia de dados, autenticação e acesso de identidade por meio de identificadores de segurança do grupo e usuário nos filtros do Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/06/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 11b2fb5a246dfa8f5b1295a11cc57de36120898e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61283306"
---
# <a name="security-and-data-privacy-in-azure-search"></a>Segurança e privacidade de dados no Azure Search

Os recursos de segurança e controles de acesso abrangentes são internos no Azure Search para garantir que o conteúdo privado permaneça dessa maneira. Este artigo enumera os recursos de segurança e a conformidade com padrões internos no Azure Search.

A arquitetura de segurança do Azure Search abrange segurança física, transmissões criptografadas, armazenamento criptografado e conformidade com padrões em toda a plataforma. Operacionalmente, o Azure Search só aceita solicitações autenticadas. Opcionalmente, é possível adicionar controles de acesso por usuário ao conteúdo por meio de filtros de segurança. Este artigo aborda a segurança em cada camada, mas se concentra principalmente na proteção das operações e dos dados no Azure Search.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Conformidade com padrões: ISO 27001, SOC 2, HIPAA

O Azure Search é certificado para os seguintes padrões, conforme [anunciado em junho de 2018](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/):

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [Conformidade com SOC 2 Tipo 2](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) Para o relatório completo, acesse [Azure - e relatório do Microsoft Azure Governamental SOC 2 Tipo II](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). 
+ [Lei americana HIPAA (Health Insurance Portability and Accountability Act)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (21 CFR Parte 11)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS Nível 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)
+ [Austrália IRAP Não Classificado DLM](https://asd.gov.au/infosec/irap/certified_clouds.htm)

A conformidade com padrões se aplica a recursos geralmente disponíveis. As versões prévias dos recursos são certificadas quando mudam para disponibilidade geral e não devem ser utilizadas em soluções com requisitos de padrões estritos. A certificação de conformidade está documentada em [Visão geral de conformidade do Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) e na [Central de Confiabilidade](https://www.microsoft.com/en-us/trustcenter). 

## <a name="encrypted-transmission-and-storage"></a>Armazenamento e transmissão criptografados

A criptografia se estende em todo o pipeline de indexação: de conexões, à transmissão, até dados armazenados indexados no Azure Search.

| Camada de segurança | DESCRIÇÃO |
|----------------|-------------|
| Criptografia em trânsito <br>(HTTPS/SSL/TLS) | O Azure Search escuta na porta HTTPS 443. Na plataforma, as conexões com os serviços do Azure são criptografadas. <br/><br/>Todas as interações do cliente com o serviço do Azure Search são compatíveis com SSL/TLS 1.2.  Certifique-se de usar o TLSv1.2 para conexões SSL ao seu serviço.|
| Criptografia em repouso | A criptografia é totalmente internalizada no processo de indexação, sem nenhum impacto mensurável no tempo para conclusão da indexação ou no tamanho do índice. Isso ocorre automaticamente em toda a indexação, incluindo em atualizações incrementais em um índice que não está totalmente criptografado (criado antes de janeiro de 2018).<br><br>Internamente, a criptografia é baseada na [Criptografia do Serviço de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), usando a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) do 256 bits.|

A criptografia é interna no Azure Search, com certificados e chaves de criptografia gerenciados internamente pela Microsoft e aplicados universalmente. Não é possível ativar ou desativar a criptografia, gerenciar ou substituir suas próprias chaves ou exibir configurações de criptografia no portal ou de forma programática. 

A criptografia em repouso foi divulgada em 24 de janeiro de 2018 e se aplica a todas as camadas de serviço, incluindo serviços compartilhados (gratuitos), em todas as regiões. Para a criptografia completa, os índices criados antes dessa data precisam ser removidos e recompilados para que a criptografia ocorra. Caso contrário, somente os novos dados adicionados após 24 de janeiro são criptografados.

## <a name="azure-wide-user-access-controls"></a>Controles de acesso do usuário em todo o Azure

Vários mecanismos de segurança estão disponíveis em toda a extensão do Azure e, portanto, estão disponíveis automaticamente para os recursos do Azure Search que você cria.

+ [Bloqueios no nível de recurso ou da assinatura para impedir a exclusão](../azure-resource-manager/resource-group-lock-resources.md)
+ [RBAC (controle de acesso baseado em função) para controlar o acesso a informações e operações administrativas](../role-based-access-control/overview.md)

Todos os serviços do Azure oferecem suporte a RBAC para definir níveis de acesso consistentes em todos os serviços. Por exemplo, a exibição de dados confidenciais, como a chave do administrador, é restrita às funções de Proprietário e Colaborador, enquanto a exibição do status do serviço fica disponível para os membros de qualquer função. O RBAC fornece funções de Leitor, Colaborador e Proprietário. Por padrão, todos os administradores de serviço são membros da função Proprietário.

<a name="service-access-and-authentication"></a>

## <a name="service-access-and-authentication"></a>Acesso de serviço e autenticação

Embora o Azure Search herde as garantias de segurança da plataforma Azure, ele também fornece sua própria autenticação baseada em chave. Uma chave de api é uma cadeia de caracteres composta de letras e números gerados aleatoriamente. O tipo de chave (administrador ou consulta) determina o nível de acesso. O envio de uma chave válida é considerado uma prova de que a solicitação se origina de uma entidade confiável. 

Há dois níveis de acesso ao seu serviço de pesquisa, habilitado por dois tipos de chaves:

* Acesso admin (válido para qualquer operação de leitura e gravação do serviço)
* Acesso de consulta (válida para operações somente leitura, como consultas em relação à coleção de documentos de um índice)

As *Chaves admin* são criadas quando o serviço é provisionado. Há duas chaves de administração, designadas como *primária* e *secundária* para mantê-las de forma linear, mas na verdade elas são intercambiáveis. Cada serviço tem duas chaves admin para que você possa derrubar uma sem perder o acesso ao seu serviço. Você pode [chave de administração regenerar](search-security-api-keys.md#regenerate-admin-keys) periodicamente por segurança do Azure as práticas recomendadas, mas você não pode adicionar a contagem de chaves de administrador total. Há um máximo de duas chaves admin por serviço de pesquisa.

*Chaves de consulta* são criadas conforme necessário e são projetados para aplicativos cliente que emitem consultas. Você pode criar até 50 chaves de consulta. No código do aplicativo, você pode especificar a URL de pesquisa e uma chave de api de consulta para permitir o acesso somente leitura para a coleção de documentos de um índice específico. Juntos, o ponto de extremidade, uma chave de api para acesso somente leitura e um índice de destino definem o nível de acesso e escopo da conexão de seu aplicativo cliente.

A autenticação é necessária em cada solicitação, em que cada solicitação é composta por uma chave obrigatória, uma operação e um objeto. Quando encadeados, os dois níveis de permissão (completo e somente leitura) e o contexto (por exemplo, uma operação de consulta em um índice) são suficientes para fornecer segurança completa nas operações de serviço. Para obter mais informações sobre chaves, consulte [Criar e gerenciar api-keys](search-security-api-keys.md).

## <a name="index-access"></a>Acesso de índice

No Azure Search, um índice individual não é um objeto que possa ser protegido. Em vez disso, o acesso a um índice é determinado na camada de serviço (acesso de leitura ou gravação), junto com o contexto de uma operação.

Para acesso do usuário final, é possível estruturar solicitações de consulta para conectar usando uma chave de consulta, o que torna qualquer solicitação somente leitura e inclui o índice específico usado pelo aplicativo. Em uma solicitação de consulta, não há o conceito de adicionar índices ou acessar vários índices simultaneamente. Portanto, todas as solicitações têm um único índice de destino por definição. Como tal, a construção da solicitação de consulta em si (uma chave mais um único índice de destino) define o limite de segurança.

O acesso de administrador e de desenvolvedor aos índices não é diferenciado: ambos precisam de acesso de gravação para criar, excluir e atualizar os objetos gerenciados pelo serviço. Qualquer pessoa com uma chave de administrador para seu serviço pode ler, modificar ou excluir um índice no mesmo serviço. Para proteção contra exclusão de índices acidental ou mal-intencionada, o controle do código-fonte interno para ativos de código é a solução para reverter uma modificação ou exclusão de índice indesejada. O Azure Search tem failover dentro do cluster para garantir a disponibilidade, mas ele não armazena nem executa seu código proprietário usado para criar ou carregar índices.

Para soluções de multilocação que exigem limites de segurança no nível do índice, essas soluções normalmente incluem uma camada intermediária que os clientes usam para lidar com isolamento de índice. Para saber mais sobre o caso de uso de vários locatários, confira [Criar padrões para aplicativos SaaS multilocatários e para o Azure Search](search-modeling-multitenant-saas-applications.md).

## <a name="admin-access"></a>Acesso de administrador

[Acesso baseado em função (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) determina se você tem acesso aos controles sobre o serviço e seu conteúdo. Se você for um proprietário ou colaborador em um serviço de Azure Search, você pode usar o portal ou o PowerShell **Az.Search** módulo para criar, atualizar ou excluir objetos no serviço. Você também pode usar o [API de REST de gerenciamento do Azure Search](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

## <a name="user-access"></a>Acesso do usuário

Por padrão, o acesso de usuário a um índice é determinado pela chave de acesso na solicitação de consulta. A maioria dos desenvolvedores cria e atribui [*chaves de consulta*](search-security-api-keys.md) para solicitações de pesquisa no lado do cliente. Uma chave de consulta concede acesso de leitura para todo o conteúdo do índice.

Se você preferir o acesso granular, o controle do conteúdo por usuário, pode construir filtros de segurança em suas consultas, retornando os documentos associados a uma identidade de segurança fornecida. Em vez de funções predefinidas e atribuições de função, o controle de acesso baseado em identidade é implementado como um *filtro* que corta os resultados da pesquisa de documentos e conteúdo com base nas identidades. A tabela a seguir descreve duas abordagens para cortar resultados da pesquisa com conteúdo não autorizado.

| Abordagem | DESCRIÇÃO |
|----------|-------------|
|[Filtragem de segurança com base nos filtros de identidade](search-security-trimming-for-azure-search.md)  | Documenta o fluxo de trabalho básico para implementar o controle de acesso de identidade do usuário. Ele aborda a adição de identificadores de segurança a um índice e explica a filtragem em relação a esse campo para cortar resultados de conteúdo proibido. |
|[Filtragem de segurança com base em Identidades do Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | Este artigo aprofunda o artigo anterior, fornecendo etapas para recuperar identidades do Azure Active Directory (AAD), um dos [serviços gratuitos](https://azure.microsoft.com/free/) na plataforma de nuvem do Azure. |

## <a name="table-permissioned-operations"></a>Tabela: Operações permitidas

A tabela a seguir resume as operações permitidas no Azure Search e qual chave desbloqueia o acesso a uma determinada operação.

| Operação | Permissões |
|-----------|-------------------------|
| Criar um serviço | Titular da assinatura do Azure|
| Dimensionar um serviço | Chave de administrador, Proprietário ou Colaborador RBAC no recurso  |
| Excluir um serviço | Chave de administrador, Proprietário ou Colaborador RBAC no recurso |
| Criar, modificar e excluir objetos no serviço: <br>Índices e partes do componente (incluindo definições do analisador, perfis de pontuação, opções de CORS), indexadores, fontes de dados, sinônimos, sugestores. | Chave de administrador, Proprietário ou Colaborador RBAC no recurso  |
| Consultar um índice | Chave de consulta ou de administrador (RBAC não aplicável) |
| Informações do sistema de consulta como: retornar estatísticas, contagens e listas de objetos. | Chave de administração, RBAC no recurso (Proprietário, Colaborador, Leitor) |
| Gerenciar chaves de administrador | Chave de administração, Proprietário ou Colaborador RBAC no recurso. |
| Gerenciar chaves de consulta |  Chave de Administrador, Proprietário ou Colaborador de RBAC no recurso.  |

## <a name="physical-security"></a>Segurança física

Os data centers da Microsoft fornecem segurança física líder no setor e são compatíveis com um portfólio abrangente de padrões e regulamentos. Para saber mais, acesse a página [Data centers globais](https://www.microsoft.com/cloud-platform/global-datacenters) ou assista a um breve vídeo sobre segurança de data center.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Consulte também

+ [Introdução ao .NET (demonstra o uso de uma chave de administrador para criar um índice)](search-create-index-dotnet.md)
+ [Introdução ao REST (demonstra o uso de uma chave de administrador para criar um índice)](search-create-index-rest-api.md)
+ [Controle de acesso baseado na identidade usando filtros do Azure Search](search-security-trimming-for-azure-search.md)
+ [Controle de acesso baseado na identidade do Active Directory usando filtros do Azure Search](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtros no Azure Search](search-filters.md)