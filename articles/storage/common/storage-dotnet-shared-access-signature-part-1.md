---
title: Usando SAS (Assinaturas de Acesso Compartilhado) no Armazenamento do Azure | Microsoft Docs
description: Saiba mais sobre como usar SAS (Assinaturas de Acesso Compartilhado) para delegar acesso a recursos de Armazenamento do Azure, incluindo blobs, filas, tabelas e arquivos.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/18/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 2b3c2ed7f2914374ac94783511f2992ae5755967
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67302321"
---
# <a name="using-shared-access-signatures-sas"></a>Usando SAS (Assinaturas de Acesso Compartilhado)

Uma SAS (Assinatura de Acesso Compartilhado) fornece uma maneira de conceder acesso limitado a objetos na sua conta de armazenamento a outros clientes, sem precisar expor sua chave de conta. Neste artigo, fornecemos uma visão geral do modelo SAS, examinamos as práticas recomendadas de SAS e vemos alguns exemplos.

Para exemplos de código adicionais usando SAS, além daqueles apresentados aqui, confira [Introdução ao Armazenamento de Blobs do Azure no.NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/) e outros exemplos disponíveis na biblioteca de [Exemplos de Código do Azure](https://azure.microsoft.com/documentation/samples/?service=storage). Você pode baixar os aplicativos de exemplo e executá-los ou procurar o código no GitHub.

## <a name="what-is-a-shared-access-signature"></a>O que é uma assinatura de acesso compartilhado?

Uma assinatura de acesso compartilhado fornece acesso delegado aos recursos da sua conta de armazenamento. Com uma SAS, você pode conceder aos clientes acesso aos recursos em sua conta de armazenamento, sem compartilhar as chaves de conta. Este é o ponto principal do uso de assinaturas de acesso compartilhado em seus aplicativos: uma SAS é uma maneira segura de compartilhar seus recursos de armazenamento sem comprometer as chaves da conta.

[!INCLUDE [storage-recommend-azure-ad-include](../../../includes/storage-recommend-azure-ad-include.md)]

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

Uma SAS oferece um controle granular sobre que tipo de acesso você concede aos clientes que têm a SAS, incluindo:

* O intervalo no qual a SAS é válida, incluindo a hora de início e a hora de expiração.
* As permissões concedidas pelas SAS. Por exemplo, um SAS para um blob pode conceder permissões de leitura e gravação nesse blob, mas não permissões de exclusão.
* Um endereço IP opcional ou intervalo de endereços IP dos quais o Armazenamento do Azure aceitará a SAS. Por exemplo, você pode especificar um intervalo de endereços IP que pertencem à sua organização.
* O protocolo pelo qual o Armazenamento do Azure aceitará a SAS. Você pode usar esse parâmetro opcional para restringir o acesso a clientes usando HTTPS.

## <a name="when-should-you-use-a-shared-access-signature"></a>Quando você deve usar uma assinatura de acesso compartilhado?

Você pode usar uma SAS quando desejar fornecer acesso aos recursos da sua conta de armazenamento a um cliente que não possui as chaves de acesso da sua conta de armazenamento. As chaves da conta de armazenamento incluem uma chave de acesso primária e uma chave secundária, que concedem acesso administrativo à sua conta e a todos os recursos que ela contém. Expor qualquer uma dessas chaves torna sua conta vulnerável a um possível uso mal-intencionado ou negligente. As assinaturas de acesso compartilhado são uma alternativa segura que permite que os clientes leiam, gravem e excluam dados da sua conta de armazenamento de acordo com as permissões explicitamente concedidas por você e sem precisar de uma chave de conta.

Um cenário comum em que uma SAS é útil é um serviço onde os usuários leem e gravam seus próprios dados na sua conta de armazenamento. Em um cenário em que uma conta de armazenamento armazena dados do usuário, há dois padrões de design comuns:

1. Os clientes carregam e baixam dados por meio de um serviço de proxy front-end, que executa a autenticação. Esse serviço de proxy front-end tem a vantagem de permitir a validação de regras de negócio, mas, para grandes quantidades de dados ou transações de alto volume, a criação de um serviço que possa ser dimensionado de acordo com a demanda pode ser difícil ou dispendiosa.

   ![Diagrama do cenário: Serviço de proxy de front-end](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png)   

1. Um serviço leve autentica o cliente conforme necessário e gera uma SAS. Depois que o cliente recebe a SAS, ele pode acessar os recursos da conta de armazenamento diretamente com as permissões definidas pela SAS e para o intervalo permitido pela SAS. A SAS reduz a necessidade de roteamento de todos os dados por meio do serviço de proxy front-end.

   ![Diagrama do cenário: Serviço de provedor SAS](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png)   

Muitos serviços reais podem usar uma combinação dessas duas abordagens. Por exemplo, alguns dados podem ser processados e validados por meio do proxy front-end, enquanto outros são salvos e/ou lidos diretamente usando SAS.

Além disso, você precisará usar uma SAS para autorizar acesso ao objeto de origem em uma operação de cópia em determinados cenários:

* Quando você copia um blob para outro que reside em uma conta de armazenamento diferente, deve usar uma SAS para autorizar acesso ao blob de origem. Outra opção é usar uma SAS para também autorizar acesso ao blob de destino.
* Quando você copia um arquivo para outro que reside em uma conta de armazenamento diferente, deve usar uma SAS para autorizar acesso ao arquivo de origem. Outra opção é usar uma SAS para também autorizar acesso ao arquivo de destino.
* Quando você estiver copiando um blob em um arquivo, ou um arquivo em um blob, use uma SAS para autorizar acesso ao objeto de origem, mesmo se os objetos de origem e destino residirem dentro da mesma conta de armazenamento.

## <a name="types-of-shared-access-signatures"></a>Tipos de assinaturas de Acesso compartilhado.

É possível criar dois tipos de assinaturas de acesso compartilhado:

* **SAS de Serviço.** A SAS de serviço delega acesso a um recurso em apenas um dos serviços de armazenamento: o serviço Blob, Fila, Tabela ou Arquivo. Veja [Construindo uma SAS de serviço](https://msdn.microsoft.com/library/dn140255.aspx) e [Exemplos de SAS de serviço](https://msdn.microsoft.com/library/dn140256.aspx) para obter informações detalhadas sobre como construir o token SAS de serviço.
* **SAS de Conta.** A SAS de conta delega acesso a recursos em um ou mais dos serviços de armazenamento. Todas as operações disponíveis através de um serviço SAS também estão disponíveis por meio de uma SAS de conta. Além disso, com a SAS de conta, você pode delegar acesso a operações que se aplicam a um determinado serviço, como **Obter/Definir propriedades de serviço** e **Obter status do serviço**. Você também pode delegar acesso a operações de leitura, gravação e exclusão em contêineres de blob, tabelas, filas e compartilhamentos de arquivos que não são permitidos com um SAS de serviço. Consulte [Constructing an Account SAS](https://msdn.microsoft.com/library/mt584140.aspx) (Construindo uma SAS de conta) para obter informações detalhadas sobre como construir o token SAS de conta.

## <a name="how-a-shared-access-signature-works"></a>Como funciona uma assinatura de acesso compartilhado

Uma assinatura de acesso compartilhado é um URI assinado que aponta para um ou mais recursos de armazenamento e inclui um token que contém um conjunto especial de parâmetros de consulta. O token indica como os recursos podem ser acessados pelo cliente. Um desses parâmetros de consulta, a assinatura, é construído a partir dos parâmetros SAS e assinado com a chave de conta. Esta assinatura é usada pelo armazenamento do Azure para autorizar o acesso ao recurso de armazenamento.

Aqui está um exemplo de um URI SAS, mostrando o URI do recurso e o token SAS:

![Componentes de um URI de SAS](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-uri.png)   

O token de SAS é uma cadeia de caracteres gerada no lado do *cliente* (consulte a seção [Exemplos de SAS](#sas-examples) para ver exemplos de código). O token de SAS gerado pela biblioteca de clientes de armazenamento, por exemplo, não é controlado pelo Armazenamento do Azure de nenhuma forma. Você pode criar um número ilimitado de tokens SAS no lado do cliente.

Quando um cliente fornece um URI SAS para o Armazenamento do Azure como parte de uma solicitação, o serviço confere os parâmetros SAS e a assinatura para verificar se ele é válido para autenticar a solicitação. Se o serviço verificar que a assinatura é válida, a solicitação será autorizada. Caso contrário, a solicitação será recusada com o código de erro 403 (proibido).

## <a name="shared-access-signature-parameters"></a>Parâmetros de assinatura de acesso compartilhado

A SAS de conta e os tokens SAS de serviço incluem alguns parâmetros comuns e também usam alguns parâmetros que são diferentes.

### <a name="parameters-common-to-account-sas-and-service-sas-tokens"></a>Parâmetros comuns a tokens SAS de conta e SAS de serviço

* **Versão da API** Um parâmetro opcional que especifica a versão do serviço de armazenamento a ser usada para executar a solicitação.
* **Versão do serviço** Um parâmetro obrigatório que especifica a versão do serviço de armazenamento a ser usada para autorizar a solicitação.
* **Hora de início.** É a hora em que a SAS torna-se válida. A hora de início de uma assinatura de acesso compartilhado é opcional. Se a hora de início for omitida, a SAS entrará em vigor imediatamente. A hora inicial deve ser expressa em UTC (Tempo Universal Coordenado), com um designador de UTC especial (“Z”), por exemplo `1994-11-05T13:15:30Z`.
* **Hora de expiração.** É a hora após a qual a SAS não é mais válida. As melhores práticas sugerem que você especifique uma hora de expiração para uma SAS ou associe-a a uma política de acesso armazenada. A hora de vencimento deve ser expressa em UTC (Tempo Universal Coordenado), com um designador de UTC especial (“Z”), por exemplo `1994-11-05T13:15:30Z` (veja mais abaixo).
* **Permissões.** As permissões especificadas nas SAS indicam quais operações o cliente pode executar com o recurso de armazenamento usando a SAS. As permissões disponíveis são diferentes entre SAS de conta e de serviço.
* **IP.** Um parâmetro opcional que especifica um endereço IP ou um intervalo de endereços IP fora do Azure (veja a seção [Estado de configuração da sessão de roteamento](../../expressroute/expressroute-workflows.md#routing-session-configuration-state) da Rota Expressa) do qual as solicitações serão aceitas.
* **Protocolo.** Um parâmetro opcional que especifica o protocolo permitido para uma solicitação. Os valores possíveis são HTTPS e HTTP (`https,http`), que é o valor padrão, ou somente HTTPS (`https`). Observe que somente HTTP não é um valor permitido.
* **Assinatura.** A assinatura é construída com os parâmetros especificados como parte do token e depois criptografada. A assinatura é usada para autorizar acesso a recursos de armazenamento específicos.

### <a name="parameters-for-a-service-sas-token"></a>Parâmetros para um token SAS de serviço

* **Recurso de armazenamento.** Os recursos de armazenamento para os quais é possível delegar acesso com SAS de serviço incluem:
  * Contêineres e blobs
  * Compartilhamentos de arquivos e arquivos
  * Filas
  * Tabelas e intervalos de entidades de tabela.

### <a name="parameters-for-an-account-sas-token"></a>Parâmetros para um token SAS de conta

* **Serviço ou serviços.** Uma SAS de conta pode delegar acesso a um ou mais serviços de armazenamento. Por exemplo, você pode criar uma SAS de conta que delegada acesso aos serviços de arquivo e Blob. Ou você pode criar uma SAS que delega acesso a todos os quatro serviços (Blob, fila, tabela e arquivo).
* **Tipos de recurso de armazenamento.** Uma SAS de conta se aplica a uma ou mais classes de recursos de armazenamento, em vez de a um recurso específico. Você pode criar uma SAS de conta para delegar acesso a:
  * APIs de nível de serviço, que são chamadas em relação ao recurso da conta de armazenamento. Os exemplos incluem **Obter/Definir Propriedades do Serviço**, **Obter Estatísticas do Serviço** e **Listar Contêineres/Filas/Tabelas/Compartilhamentos**.
  * APIs de nível de contêiner, que são chamadas em relação a objetos de contêiner para cada serviço: tabelas, filas, contêineres de blob e compartilhamentos de arquivos. Os exemplos incluem **Criar/Excluir Contêiner**, **Criar/Excluir Fila**, **Criar/Excluir Tabela**, **Criar/Excluir Compartilhamento** e **Listar Blobs/Arquivos e Diretórios**.
  * APIs de nível de objeto, que são chamadas em relação a blobs, mensagens de fila, entidades de tabela e arquivos. Por exemplo, **Colocar Blob**, **Consultar Entidade**, **Obter Mensagens** e **Criar Arquivo**.

## <a name="examples-of-sas-uris"></a>Exemplos de URIs de SAS

### <a name="service-sas-uri-example"></a>Exemplo de URI de SAS de serviço

Este é um exemplo de um URI SAS de serviço que fornece permissões de leitura e gravação para um blob. A tabela divide cada parte do URI para explicar como ele também contribui para a SAS:

```
https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2015-04-05&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D
```

| NOME | Parte SAS | DESCRIÇÃO |
| --- | --- | --- |
| URI do blob |`https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt` |O endereço do blob. Observe que o uso de HTTPS é altamente recomendável. |
| Versão dos serviços de armazenamento |`sv=2015-04-05` |Para os serviços de armazenamento de versão 12-02-2012 e posterior, este parâmetro indica a versão a ser usada. |
| Hora de início |`st=2015-04-29T22%3A18%3A26Z` |Especificado no horário UTC. Se você quiser que a SAS seja imediatamente válida, omita a hora de início. |
| Hora de expiração |`se=2015-04-30T02%3A23%3A26Z` |Especificado no horário UTC. |
| Resource |`sr=b` |O recurso é um blob. |
| Permissões |`sp=rw` |As permissões concedidas pelas SAS incluem Ler (r) e Gravar (w). |
| Intervalo IP |`sip=168.1.5.60-168.1.5.70` |O intervalo de endereços IP do qual uma solicitação será aceita. |
| Protocol |`spr=https` |São permitidas somente solicitações usando HTTPS. |
| Signature |`sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D` |Usada para autorizar o acesso ao blob. A assinatura é um HMAC computado em uma cadeia-para-assinar e uma chave que usa o algoritmo SHA256 e depois codificado usando a codificação Base64. |

### <a name="account-sas-uri-example"></a>Exemplo de URI de SAS de conta

Veja aqui um exemplo de uma SAS de conta que usa os mesmos parâmetros comuns no token. Como esses parâmetros estão descritos acima, eles não serão descritos aqui. Somente os parâmetros específicos para a SAS de conta são descritos na tabela a seguir.

```
https://myaccount.blob.core.windows.net/?restype=service&comp=properties&sv=2015-04-05&ss=bf&srt=s&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=F%6GRVAZ5Cdj2Pw4tgU7IlSTkWgn7bUkkAg8P6HESXwmf%4B
```

| NOME | Parte SAS | DESCRIÇÃO |
| --- | --- | --- |
| URI de recurso |`https://myaccount.blob.core.windows.net/?restype=service&comp=properties` |O ponto de extremidade de serviço Blob, com parâmetros para obter as propriedades do serviço (quando chamado com GET) ou definir as propriedades do serviço (quando chamado com SET). |
| Serviços |`ss=bf` |A SAS se aplica a serviços de arquivo e Blob |
| Tipos de recurso |`srt=s` |A SAS se aplica a operações de nível de serviço. |
| Permissões |`sp=rw` |As permissões concedem acesso a operações de leitura e gravação. |

Considerando que as permissões são restritas ao nível de serviço, as operações acessíveis com essa SAS são **Obter Propriedades do Serviço Blob** (leitura) e **Definir Propriedades do Serviço Blob** (gravação). Contudo, com um URI de recurso diferente, o mesmo token SAS pode também ser usado para delegar acesso a **Obter status do serviço Blob** (leitura).

## <a name="controlling-a-sas-with-a-stored-access-policy"></a>Controlando uma SAS com uma política de acesso armazenada

Uma assinatura de acesso compartilhado pode assumir uma destas duas formas:

* **SAS ad hoc:** Quando você cria uma SAS ad hoc, a hora de início, a hora de vencimento e as permissões para a SAS são especificadas no URI da SAS (ou implícitas, quando a hora de início é omitida). Esse tipo de SAS pode ser criada como uma SAS de conta ou de serviço.
* **SAS com política de acesso armazenada:** Uma política de acesso armazenada é definida em um contêiner de recurso – um contêiner de blob, uma tabela, uma fila ou um compartilhamento de arquivos – e pode ser usada para gerenciar as restrições de uma ou mais assinaturas de acesso compartilhado. Quando você associa uma SAS a uma política de acesso armazenada, ela herda as restrições – a hora de início, a hora de expiração e as permissões – definidas para a política de acesso armazenada.

> [!NOTE]
> Atualmente, uma SAS de conta deve ser uma SAS ad hoc. As SAS de conta ainda não dão suporte a políticas de acesso armazenadas.

A diferença entre as duas formas é importante para um cenário fundamental: revogação. Como um URI de SAS é uma URL, qualquer pessoa que obtiver a SAS poderá usá-lo, independentemente de quem o criou originalmente. Se uma SAS for publicada publicamente, ela poderá ser usada por qualquer pessoa no mundo. Uma SAS concede acesso aos recursos para qualquer um que a possua até que ocorra um destes quatro fatores:

1. A hora de expiração especificada na SAS é atingida.
2. A hora de expiração especificada na política de acesso armazenada referenciada pelas SAS é atingida (se uma política de acesso armazenada for referenciada e especificar uma hora de expiração). Isso pode ocorrer porque o intervalo transcorreu ou porque você modificou a política de acesso armazenada para ter uma hora de expiração no passado, que é uma maneira de revogar a SAS.
3. A política de acesso armazenada referenciada pelas SAS é excluída, que é uma outra maneira de revogar a SAS. Observe que, se você recriar a política de acesso armazenada exatamente com o mesmo nome, todos os tokens SAS existentes serão novamente válidos de acordo com as permissões associadas a essa política de acesso armazenada (supondo que a hora de expiração não tenha passado na SAS). Se você estiver pretendendo revogar a SAS, certifique-se de usar um nome diferente se recriar a política de acesso com uma hora de expiração no futuro.
4. A chave de conta usada para criar as SAS é regenerada. Regenerar uma chave de conta causará uma falha de autorização de todos os componentes do aplicativo que usam essa chave até que eles sejam atualizados para usar a outra chave de conta válida ou a chave de conta recém-regenerada.

> [!IMPORTANT]
> Um URI de assinatura de acesso compartilhado é associado com a chave de conta usada para criar a assinatura e a política de acesso armazenado associada (se houver). Se nenhuma política de acesso armazenado for especificada, a única maneira de revogar uma assinatura de acesso compartilhado é alterar a chave da conta.

## <a name="authenticating-from-a-client-application-with-a-sas"></a>Autenticação de um aplicativo cliente com uma SAS

Um cliente que tem uma SAS pode usar a SAS para autorizar uma solicitação em relação a uma conta de armazenamento para a qual ele não tem as chaves da conta. Uma SAS pode ser incluída em uma cadeia de conexão ou usada diretamente por meio do construtor ou método apropriado.

### <a name="using-a-sas-in-a-connection-string"></a>Usando uma SAS em uma cadeia de conexão

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

### <a name="using-a-sas-in-a-constructor-or-method"></a>Usar uma SAS em um construtor ou método

Vários construtores de biblioteca de cliente do Armazenamento do Azure e sobrecargas de método oferecem um parâmetro de SAS, para que você possa autorizar uma solicitação de serviço com uma SAS.

Por exemplo, aqui, um URI SAS é usado para criar uma referência para um blob de blocos. A SAS fornece as únicas credenciais necessárias para a solicitação. A referência de blob de blocos é usada para uma operação de gravação:

```csharp
string sasUri = "https://storagesample.blob.core.windows.net/sample-container/" +
    "sampleBlob.txt?sv=2015-07-08&sr=b&sig=39Up9JzHkxhUIhFEjEH9594DJxe7w6cIRCg0V6lCGSo%3D" +
    "&se=2016-10-18T21%3A51%3A37Z&sp=rcw";

CloudBlockBlob blob = new CloudBlockBlob(new Uri(sasUri));

// Create operation: Upload a blob with the specified name to the container.
// If the blob does not exist, it will be created. If it does exist, it will be overwritten.
try
{
    MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
    msWrite.Position = 0;
    using (msWrite)
    {
        await blob.UploadFromStreamAsync(msWrite);
    }

    Console.WriteLine("Create operation succeeded for SAS {0}", sasUri);
    Console.WriteLine();
}
catch (StorageException e)
{
    if (e.RequestInformation.HttpStatusCode == 403)
    {
        Console.WriteLine("Create operation failed for SAS {0}", sasUri);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
    else
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}

```

## <a name="best-practices-when-using-sas"></a>Práticas recomendadas ao usar SAS

Ao usar assinaturas de acesso compartilhado nos seus aplicativos, você precisará estar ciente de dois riscos possíveis:

* Se ocorrer perda de uma SAS, ela poderá ser usada por qualquer pessoa que a obtiver, o que poderá comprometer a sua conta de armazenamento.
* Se uma SAS fornecida para um aplicativo cliente expirar e o aplicativo não conseguir recuperar uma nova SAS do seu serviço, a funcionalidade do aplicativo poderá ser prejudicada.

As recomendações a seguir para usar assinaturas de acesso compartilhado podem ajudar a atenuar esses riscos:

1. **Sempre use HTTPS** para criar ou distribuir uma SAS. Se uma SAS for passada por HTTP e interceptada, um invasor que estiver realizando um ataque intermediário poderá lê-la e, em seguida, usá-la exatamente como o usuário previsto, o que poderá comprometer dados confidenciais ou gerar dados corrompidos pelo usuário mal-intencionado.
2. **Faça referência às políticas de acesso armazenadas sempre que possível.** As políticas de acesso armazenadas permitem que você revogue permissões sem precisar regenerar as chaves de conta de armazenamento. Defina o vencimento para um momento muito distante no futuro (ou infinito) e certifique-se de que ela seja atualizada regularmente para uma data ainda mais além no futuro.
3. **Use horas de expiração de curto prazo em uma SAS ad hoc.** Dessa forma, mesmo se uma SAS for comprometida, ela será válida apenas por um breve período. Esta prática será especialmente importante se você não puder fazer referência a uma política de acesso armazenada. Períodos de vencimento breves também limitam a quantidade de dados que podem ser gravados em um blob, limitando o tempo disponível para carregá-los.
4. **Faça com que os clientes renovem a SAS automaticamente, se necessário.** Os clientes devem renovar a SAS bem antes da expiração, para que haja tempo para novas tentativas, caso o serviço que fornece a SAS não esteja disponível. Se o SAS se destinar a ser usado para um pequeno número de operações imediatas e de curta duração que devem ser concluídas no período de expiração, isso poderá ser desnecessário, pois não se espera que a SAS seja renovada. No entanto, se você tiver um cliente que está sempre fazendo solicitações via SAS, surge a possibilidade de expiração. A principal consideração consiste em equilibrar a necessidade de curta duração da SAS (como mencionado acima) com a necessidade de garantir que o cliente solicite renovação com antecedência suficiente (para evitar uma interrupção devido ao vencimento da SAS antes de uma renovação bem-sucedida).
5. **Tenha cuidado com a hora de início da SAS.** Se você definir a hora de início de uma SAS para **agora**, poderão ser observadas falhas intermitentemente para os primeiros minutos devido à defasagem horária (diferenças na hora atual de acordo com computadores diferentes). Em geral, defina a hora de início para pelo menos 15 minutos no passado. Ou então, simplesmente não a defina, o que a tornará imediatamente válida em todos os casos. Em geral, o mesmo também se aplica à hora de vencimento – lembre-se de que pode haver até 15 minutos de defasagem horária em um dos dois sentidos em qualquer solicitação. Para os clientes que usam uma versão de REST anterior à 2012-02-12, a duração máxima de uma SAS que não faz referência a uma política de acesso armazenada é 1 hora, e qualquer política que especifique um período maior do que esse falhará.
6. **Seja específico com o recurso a ser acessado.** Uma prática recomendada de segurança consiste em fornecer os privilégios mínimos necessários a um usuário. Se um usuário precisar apenas de acesso de leitura a uma única entidade, conceda-lhe acesso de leitura a essa única entidade, e não acesso de leitura/gravação/exclusão a todas as entidades. Isso também ajuda a reduzir o dano caso uma SAS seja comprometida, pois a SAS terá menos poder nas mãos de um invasor.
7. **É importante que você entenda que a sua conta será cobrada por qualquer uso, incluindo o realizado com a SAS.** Se você fornecer acesso de gravação a um blob, um usuário poderá optar por carregar um blob de 200 GB. Se você também tiver concedido o acesso de leitura, será possível baixá-la 10 vezes, incorrendo em 2 TB de custos de saída. Como mencionado, forneça permissões limitadas para ajudar a reduzir a ações em potencial de usuários mal-intencionados. Use SAS de curta duração para reduzir essa ameaça (mas, tenha cuidado com a defasagem horária na hora de término).
8. **Valide os dados gravados com a SAS.** Quando um aplicativo cliente gravar dados na sua conta de armazenamento, tenha em mente de que poderá haver problemas com esses dados. Se o seu aplicativo necessitar de que esses dados sejam validados ou autorizados antes que estejam prontos para uso, você deverá realizar essa validação depois que os dados forem gravados e antes que eles sejam usados pelo seu aplicativo. Essa prática também protegerá contra dados corrompidos ou mal-intencionados que estiverem sendo gravados na sua conta por um usuário que adquiriu a SAS de forma adequada ou por um usuário que estiver explorando uma SAS vazada.
9. **Não use sempre SAS.** Às vezes, os riscos associados a uma determinada operação em relação à sua conta de armazenamento superam os benefícios da SAS. Para essas operações, crie um serviço de camada intermediária que grave na sua conta de armazenamento após a validação, a autenticação e a auditoria da regra de negócio. Além disso, algumas vezes é mais simples de gerenciar o acesso de outras maneiras. Por exemplo, se quiser tornar todos os blobs de um contêiner publicamente legíveis, você poderá tornar o contêiner Público, em vez de fornecer uma SAS para o acesso de cada cliente.
10. **Use a Análise de Armazenamento para monitorar seu aplicativo.** Você pode usar log e métricas para observar qualquer pico nas falhas de autenticação devido a uma interrupção no seu serviço de provedor de SAS ou à remoção acidental de uma política de acesso armazenada. Consulte o [Blog da Equipe de Armazenamento do Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) para obter informações adicionais.

## <a name="sas-examples"></a>Exemplos de SAS

Abaixo estão alguns exemplos de ambos os tipos de assinatura de acesso compartilhado: SAS de conta e SAS de serviço.

Para executar esses exemplos em C#, você precisa fazer referência aos seguintes pacotes NuGet em seu projeto:

* [Biblioteca de cliente de armazenamento do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage), versão 6.x ou posterior (para usar a conta SAS).
* [Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager)

Para outros exemplos que mostram como criar e testar uma SAS, confira [Exemplos de Código do Azure para Armazenamento](https://azure.microsoft.com/documentation/samples/?service=storage).

### <a name="example-create-and-use-an-account-sas"></a>Exemplo: Criar e usar uma conta de SAS

O exemplo de código a seguir cria uma SAS de conta que é válida para os serviços de arquivo e de blob e dá ao cliente permissões de leitura, gravação e listagem de permissões para acessar as APIs de nível de serviço. A SAS de conta restringe o protocolo para HTTPS, para que a solicitação deva ser feita com HTTPS.

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use your shared key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

Para usar a SAS de conta e acessar as APIs de nível de serviço para o serviço Blob, construa um objeto de cliente Blob usando a SAS e o ponto de extremidade de armazenamento de Blob para sua conta de armazenamento.

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "account-name", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

### <a name="example-create-a-stored-access-policy"></a>Exemplo: Criar uma política de acesso armazenada

O código a seguir cria uma política de acesso armazenada em um contêiner. Você pode usar a política de acesso para especificar restrições para um serviço de SAS no contêiner ou seus blobs.

```csharp
private static async Task CreateSharedAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new shared access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
        // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

### <a name="example-create-a-service-sas-on-a-container"></a>Exemplo: Criar um serviço SAS em um contêiner

O código a seguir cria uma SAS em um contêiner. Se o nome de uma política de acesso armazenada existente for fornecido, essa política está associada com a SAS. Se nenhuma política de acesso armazenado for fornecida, o código cria uma SAS ad hoc no contêiner.

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container, setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case, all of the constraints for the
        // shared access signature are specified on the stored access policy, which is provided by name.
        // It is also possible to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for blob container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

### <a name="example-create-a-service-sas-on-a-blob"></a>Exemplo: Criar um serviço SAS em um blob

O código a seguir cria uma SAS em um blob. Se o nome de uma política de acesso armazenada existente for fornecido, essa política está associada com a SAS. Se nenhuma política de acesso armazenado for fornecida, o código cria uma SAS ad hoc no blob.

```csharp
private static string GetBlobSasUri(CloudBlobContainer container, string blobName, string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob, setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints for the
        // shared access signature are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

## <a name="conclusion"></a>Conclusão

As assinaturas de acesso compartilhado são úteis para fornecer permissões limitadas para a sua conta de armazenamento aos clientes que não devem ter a chave de conta. Desse modo, elas são uma parte vital do modelo de segurança para qualquer aplicativo que utilize o Armazenamento do Azure. Se você seguir as práticas recomendadas listadas aqui, poderá usar a SAS para oferecer mais flexibilidade de acesso aos recursos da sua conta de armazenamento, sem comprometer a segurança do seu aplicativo.

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar o acesso de leitura anônimo aos contêineres e blobs](../blobs/storage-manage-access-to-resources.md)
* [Delegando acesso com uma assinatura de acesso compartilhado](https://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Introdução à Tabela e à Fila SAS](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
