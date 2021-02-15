---
title: Conceder acesso limitado a dados com SAS (assinaturas de acesso compartilhado)
titleSuffix: Azure Storage
description: Saiba mais sobre como usar SAS (assinaturas de acesso compartilhado) para delegar acesso aos recursos de armazenamento do Azure, incluindo BLOBs, filas, tabelas e arquivos.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/28/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 8812ab091037bcb3144a7fed53a449c3f5de353a
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/29/2020
ms.locfileid: "97802500"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)

Uma SAS (assinatura de acesso compartilhado) fornece acesso delegado seguro aos recursos em sua conta de armazenamento. Com uma SAS, você tem controle granular sobre como um cliente pode acessar seus dados. Por exemplo:
 
- Quais recursos o cliente pode acessar.

- Quais são as permissões que eles têm para esses recursos.

- Por quanto tempo a SAS é válida.

## <a name="types-of-shared-access-signatures"></a>Tipos de assinaturas de acesso compartilhado

O armazenamento do Azure dá suporte a três tipos de assinaturas de acesso compartilhado:

- SAS de delegação de usuário

- SAS do serviço

- SAS da conta

### <a name="user-delegation-sas"></a>SAS de delegação de usuário

Uma SAS de delegação de usuário é protegida com as credenciais do Azure Active Directory (AD do Azure) e também pelas permissões especificadas para a SAS. Uma SAS de delegação de usuário se aplica somente ao armazenamento de BLOB.

Para obter mais informações sobre a SAS de delegação de usuário, consulte [criar uma delegação de usuário de SAS (API REST)](/rest/api/storageservices/create-user-delegation-sas).

### <a name="service-sas"></a>SAS do serviço

Uma SAS de serviço é protegida com a chave da conta de armazenamento. Uma SAS de serviço Delega acesso a um recurso em apenas um dos serviços de armazenamento do Azure: armazenamento de BLOBs, armazenamento de filas, armazenamento de tabelas ou arquivos do Azure.

Para obter mais informações sobre a SAS do serviço, consulte [criar um serviço SAS (API REST)](/rest/api/storageservices/create-service-sas).

### <a name="account-sas"></a>SAS da conta

Uma SAS de conta é protegida com a chave da conta de armazenamento. Uma SAS de conta delega acesso a recursos em um ou mais dos serviços de armazenamento. Todas as operações disponíveis por meio de uma SAS de delegação de serviço ou de usuário também estão disponíveis por meio de uma SAS de conta. 

Você também pode delegar o acesso ao seguinte:

- Operações de nível de serviço (por exemplo, as operações de **serviço Get/Set** e **Get Service stats** ). 

- Operações de leitura, gravação e exclusão que não são permitidas com uma SAS de serviço. 

Para obter mais informações sobre a SAS da conta, [crie uma SAS da conta (API REST)](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> A Microsoft recomenda que você use as credenciais do Azure AD quando possível como uma prática recomendada de segurança, em vez de usar a chave de conta, que pode ser mais facilmente comprometida. Quando o design do aplicativo exigir assinaturas de acesso compartilhado para acesso ao armazenamento de BLOB, use as credenciais do Azure AD para criar uma SAS de delegação de usuário quando possível para segurança superior. Para obter mais informações, consulte [autorizar o acesso a BLOBs e filas usando o Azure Active Directory](storage-auth-aad.md).

Uma assinatura de acesso compartilhado pode ter uma das duas formas a seguir:

- **SAS ad hoc**. Quando você cria uma SAS ad hoc, a hora de início, a hora de expiração e as permissões são especificadas no URI de SAS. Qualquer tipo de SAS pode ser uma SAS ad hoc.

- **SAS de serviço com política de acesso armazenada**. Uma política de acesso armazenada é definida em um contêiner de recursos, que pode ser um contêiner de BLOBs, uma tabela, uma fila ou um compartilhamento de arquivos. A política de acesso armazenada pode ser usada para gerenciar restrições para uma ou mais assinaturas de acesso compartilhado do serviço. Quando você associa uma SAS de serviço a uma política de acesso armazenada, a SAS herda as restrições &mdash; de hora de início, hora de expiração e permissões &mdash; definidas para a política de acesso armazenada.

> [!NOTE]
> Uma SAS de delegação de usuário ou uma SAS de conta deve ser uma SAS ad hoc. As políticas de acesso armazenadas não têm suporte para a SAS de delegação de usuário ou a SAS da conta.

## <a name="how-a-shared-access-signature-works"></a>Como funciona uma assinatura de acesso compartilhado

Uma assinatura de acesso compartilhado é um URI assinado que aponta para um ou mais recursos de armazenamento. O URI inclui um token que contém um conjunto especial de parâmetros de consulta. O token indica como os recursos podem ser acessados pelo cliente. Um dos parâmetros de consulta, a assinatura, é construído a partir dos parâmetros de SAS e assinado com a chave que foi usada para criar a SAS. Esta assinatura é usada pelo armazenamento do Azure para autorizar o acesso ao recurso de armazenamento.

> [!NOTE]
> Não é possível auditar a geração de tokens SAS. Qualquer usuário que tenha privilégios para gerar um token SAS, seja usando a chave de conta ou por meio de uma atribuição de função RBAC do Azure, pode fazer isso sem o conhecimento do proprietário da conta de armazenamento. Tenha cuidado para restringir as permissões que permitem aos usuários gerar tokens SAS. Para impedir que os usuários gerem uma SAS assinada com a chave de conta para cargas de trabalho de BLOB e fila, você pode não permitir o acesso de chave compartilhada à conta de armazenamento. Para obter mais informações, consulte [impedir autorização com chave compartilhada](shared-key-authorization-prevent.md).

### <a name="sas-signature-and-authorization"></a>Assinatura e autorização SAS

Você pode assinar um token SAS com uma chave de delegação de usuário ou com uma chave de conta de armazenamento (chave compartilhada). 

#### <a name="signing-a-sas-token-with-a-user-delegation-key"></a>Assinando um token SAS com uma chave de delegação de usuário

Você pode assinar um token SAS usando uma *chave de delegação de usuário* que foi criada usando as credenciais do Azure Active Directory (AD do Azure). Uma SAS de delegação de usuário é assinada com a chave de delegação de usuário.

Para obter a chave e, em seguida, criar a SAS, uma entidade de segurança do Azure AD deve ser atribuída a uma função do Azure que inclui a `Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey` ação. Para obter mais informações, consulte [criar uma delegação de usuário (API REST)](/rest/api/storageservices/create-user-delegation-sas).

#### <a name="signing-a-sas-token-with-an-account-key"></a>Assinando um token SAS com uma chave de conta

Uma SAS de serviço e uma SAS de conta são assinadas com a chave da conta de armazenamento. Para criar uma SAS que é assinada com a chave de conta, um aplicativo deve ter acesso à chave de conta.

Quando uma solicitação inclui um token SAS, essa solicitação é autorizada com base em como o token SAS é assinado. A chave de acesso ou as credenciais que você usa para criar um token SAS também são usadas pelo armazenamento do Azure para conceder acesso a um cliente que possui a SAS.

A tabela a seguir resume como cada tipo de token SAS é autorizado.

| Tipo de SAS | Tipo de autorização |
|-|-|
| SAS de delegação de usuário (somente armazenamento de BLOBs) | AD do Azure |
| SAS do serviço | Chave compartilhada |
| SAS da conta | Chave compartilhada |

A Microsoft recomenda usar uma SAS de delegação de usuário quando possível para segurança superior.

### <a name="sas-token"></a>Token SAS

O token SAS é uma cadeia de caracteres que você gera no lado do cliente, por exemplo, usando uma das bibliotecas de cliente do armazenamento do Azure. O token SAS não é acompanhado pelo armazenamento do Azure de nenhuma forma. Você pode criar um número ilimitado de tokens SAS no lado do cliente. Depois de criar uma SAS, você pode distribuí-la aos aplicativos cliente que exigem acesso aos recursos em sua conta de armazenamento.

Os aplicativos cliente fornecem o URI de SAS para o armazenamento do Azure como parte de uma solicitação. Em seguida, o serviço verifica os parâmetros SAS e a assinatura para verificar se ele é válido. Se o serviço verificar que a assinatura é válida, a solicitação será autorizada. Caso contrário, a solicitação será recusada com o código de erro 403 (proibido).

Aqui está um exemplo de um URI de SAS de serviço, mostrando o URI de recurso e o token SAS. Como o token SAS é composto pela cadeia de caracteres de consulta de URI, o URI de recurso deve ser seguido primeiro por um ponto de interrogação e, em seguida, pelo token SAS:

![Componentes de um URI de SAS de serviço](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>Quando usar uma assinatura de acesso compartilhado

Use uma SAS para fornecer acesso seguro aos recursos em sua conta de armazenamento para qualquer cliente que não tenha permissões para esses recursos.

Um cenário comum em que uma SAS é útil é um serviço onde os usuários leem e gravam seus próprios dados na sua conta de armazenamento. Em um cenário em que uma conta de armazenamento armazena dados do usuário, há dois padrões de design comuns:

1. Os clientes carregam e baixam dados por meio de um serviço de proxy front-end, que executa a autenticação. Esse serviço de proxy de front-end permite a validação de regras de negócio. Mas, para grandes quantidades de dados ou transações de alto volume, criar um serviço que pode ser dimensionado para corresponder a demanda pode ser caro ou difícil.

   ![Diagrama do cenário: serviço de proxy front-end](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

2. Um serviço leve autentica o cliente conforme necessário e gera uma SAS. Depois que o aplicativo cliente recebe a SAS, ele pode acessar os recursos da conta de armazenamento diretamente. As permissões de acesso são definidas pela SAS e para o intervalo permitido pela SAS. A SAS reduz a necessidade de roteamento de todos os dados por meio do serviço de proxy front-end.

   ![Diagrama do cenário: serviço do provedor de SAS](./media/storage-sas-overview/sas-storage-provider-service.png)

Muitos serviços reais podem usar uma combinação dessas duas abordagens. Por exemplo, alguns dados podem ser processados e validados por meio do proxy de front-end. Outros dados são salvos e/ou lidos diretamente usando SAS.

Além disso, uma SAS é necessária para autorizar o acesso ao objeto de origem em uma operação de cópia em determinados cenários:

- Quando você copia um blob para outro blob que reside em uma conta de armazenamento diferente. 
  
  Outra opção é usar uma SAS para também autorizar acesso ao blob de destino.

- Quando você copia um arquivo para outro arquivo que reside em uma conta de armazenamento diferente. 

  Outra opção é usar uma SAS para também autorizar acesso ao arquivo de destino.

- Quando você copia um blob para um arquivo ou um arquivo para um blob. 

  Você deve usar uma SAS mesmo se os objetos de origem e destino residirem na mesma conta de armazenamento.

## <a name="best-practices-when-using-sas"></a>Práticas recomendadas ao usar SAS

Ao usar assinaturas de acesso compartilhado nos seus aplicativos, você precisará estar ciente de dois riscos possíveis:

- Se ocorrer perda de uma SAS, ela poderá ser usada por qualquer pessoa que a obtiver, o que poderá comprometer a sua conta de armazenamento.

- Se uma SAS fornecida para um aplicativo cliente expirar e o aplicativo não conseguir recuperar uma nova SAS do seu serviço, a funcionalidade do aplicativo poderá ser prejudicada.

As recomendações a seguir para usar assinaturas de acesso compartilhado podem ajudar a atenuar esses riscos:

- **Sempre use HTTPS** para criar ou distribuir uma SAS. Se uma SAS é passada por HTTP e interceptada, um invasor que executa um ataque man-in-the-Middle é capaz de ler a SAS. Em seguida, eles podem usar essa SAS da mesma forma que o usuário pretendido. Isso pode comprometer potencialmente dados confidenciais ou permitir corrupção de dados pelo usuário mal-intencionado.

- **Use uma SAS de delegação de usuário quando possível.** Uma SAS de delegação de usuário fornece segurança superior a uma SAS de serviço ou a uma SAS de conta. Uma SAS de delegação de usuário é protegida com as credenciais do Azure AD, para que você não precise armazenar sua chave de conta com seu código.

- **Ter um plano de revogação em vigor para uma SAS.** Verifique se você está preparado para responder se uma SAS está comprometida.

- **Defina uma política de acesso armazenada para uma SAS de serviço.** As políticas de acesso armazenadas oferecem a opção de revogar permissões para uma SAS de serviço sem a necessidade de regenerar as chaves da conta de armazenamento. Defina o vencimento para um momento muito distante no futuro (ou infinito) e certifique-se de que ela seja atualizada regularmente para uma data ainda mais além no futuro.

- **Use tempos de expiração de curto prazo em uma SAS do serviço SAS ad hoc ou SAS da conta.** Dessa forma, mesmo se uma SAS for comprometida, ela será válida apenas por um breve período. Esta prática será especialmente importante se você não puder fazer referência a uma política de acesso armazenada. Períodos de vencimento breves também limitam a quantidade de dados que podem ser gravados em um blob, limitando o tempo disponível para carregá-los.

- **Faça com que os clientes renovem a SAS automaticamente, se necessário.** Os clientes devem renovar a SAS bem antes da expiração, para que haja tempo para novas tentativas, caso o serviço que fornece a SAS não esteja disponível. Isso pode ser desnecessário em alguns casos. Por exemplo, você pode pretender que a SAS seja usada para um pequeno número de operações imediatas e de curta duração. Espera-se que essas operações sejam concluídas dentro do período de validade. Como resultado, você não está esperando que a SAS seja renovada. No entanto, se você tiver um cliente que está rotineiramente fazendo solicitações via SAS, a possibilidade de expiração entrará em cena. 

- **Tenha cuidado com a hora de início da SAS.** Se você definir a hora de início de uma SAS para a hora atual, as falhas poderão ocorrer intermitentemente pelos primeiros minutos. Isso ocorre devido a diferentes máquinas que têm tempos de corrente ligeiramente diferentes (conhecidos como distorção de relógio). Em geral, defina a hora de início para pelo menos 15 minutos no passado. Ou então, simplesmente não a defina, o que a tornará imediatamente válida em todos os casos. Em geral, o mesmo também se aplica à hora de vencimento – lembre-se de que pode haver até 15 minutos de defasagem horária em um dos dois sentidos em qualquer solicitação. Para clientes que usam uma versão REST anterior à 2012-02-12, a duração máxima de uma SAS que não faz referência a uma política de acesso armazenada é de 1 hora. Todas as políticas que especificam um período maior que 1 hora falharão.

- **Tenha cuidado com o formato de data e hora SAS.** Para alguns utilitários (como AzCopy), você precisa que os formatos de data e hora sejam ' +% Y-% m-% dT% H:%M:% SZ '. Esse formato inclui especificamente os segundos. 

- **Seja específico com o recurso a ser acessado.** Uma prática recomendada de segurança consiste em fornecer os privilégios mínimos necessários a um usuário. Se um usuário precisar apenas de acesso de leitura a uma única entidade, conceda-lhe acesso de leitura a essa única entidade, e não acesso de leitura/gravação/exclusão a todas as entidades. Isso também ajuda a reduzir o dano caso uma SAS seja comprometida, pois a SAS terá menos poder nas mãos de um invasor.

- **Entenda que sua conta será cobrada por qualquer uso, incluindo por meio de uma SAS.** Se você fornecer acesso de gravação a um blob, um usuário poderá optar por carregar um blob de 200 GB. Se você também tiver concedido o acesso de leitura, será possível baixá-la 10 vezes, incorrendo em 2 TB de custos de saída. Como mencionado, forneça permissões limitadas para ajudar a reduzir a ações em potencial de usuários mal-intencionados. Use SAS de curta duração para reduzir essa ameaça (mas, tenha cuidado com a defasagem horária na hora de término).

- **Validar os dados gravados usando uma SAS.**  Quando um aplicativo cliente gravar dados na sua conta de armazenamento, tenha em mente de que poderá haver problemas com esses dados. Se você planeja validar dados, execute essa validação depois que os dados são gravados e antes de serem usados pelo seu aplicativo. Essa prática também protegerá contra dados corrompidos ou mal-intencionados que estiverem sendo gravados na sua conta por um usuário que adquiriu a SAS de forma adequada ou por um usuário que estiver explorando uma SAS vazada.

- **Saiba quando não usar uma SAS.** Às vezes, os riscos associados a uma determinada operação em relação à sua conta de armazenamento superam os benefícios do uso de uma SAS. Para essas operações, crie um serviço de camada intermediária que grave na sua conta de armazenamento após a validação, a autenticação e a auditoria da regra de negócio. Além disso, algumas vezes é mais simples de gerenciar o acesso de outras maneiras. Por exemplo, se quiser tornar todos os blobs de um contêiner publicamente legíveis, você poderá tornar o contêiner Público, em vez de fornecer uma SAS para o acesso de cada cliente.

- **Use Azure Monitor e logs de armazenamento do Azure para monitorar seu aplicativo.** As falhas de autorização podem ocorrer devido a uma interrupção no serviço do provedor SAS. Eles também podem ocorrer de uma remoção inadvertida de uma política de acesso armazenada. Você pode usar o Azure Monitor e o log da análise de armazenamento para observar qualquer pico nesses tipos de falhas de autorização. Para obter mais informações, consulte [métricas de armazenamento do Azure em log de Azure monitor](../blobs/monitor-blob-storage.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json) e [análise de armazenamento do Azure](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="get-started-with-sas"></a>Introdução à SAS

Para começar a usar as assinaturas de acesso compartilhado, consulte os artigos a seguir para cada tipo de SAS.

### <a name="user-delegation-sas"></a>SAS de delegação de usuário

- [Criar uma SAS de delegação de usuário para um contêiner ou BLOB com o PowerShell](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Criar uma SAS de delegação de usuário para um contêiner ou BLOB com o CLI do Azure](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Criar uma SAS de delegação de usuário para um contêiner ou BLOB com .NET](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>SAS do serviço

- [Criar uma SAS de serviço para um contêiner ou BLOB com .NET](../blobs/sas-service-create.md)

### <a name="account-sas"></a>SAS da conta

- [Criar uma SAS de conta com .NET](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Próximas etapas

- [Delegar acesso com uma assinatura de acesso compartilhado (API REST)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Criar uma SAS de delegação de usuário (API REST)](/rest/api/storageservices/create-user-delegation-sas)
- [Criar uma SAS de serviço (API REST)](/rest/api/storageservices/create-service-sas)
- [Criar uma SAS de conta (API REST)](/rest/api/storageservices/create-account-sas)
