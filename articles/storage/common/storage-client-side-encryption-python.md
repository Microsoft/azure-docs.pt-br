---
title: Criptografia do lado do cliente com Python para o Armazenamento do Microsoft Azure | Microsoft Docs
description: A Biblioteca do cliente do Armazenamento do Azure para Python dá suporte à criptografia do lado do cliente para segurança máxima para seus aplicativos do Armazenamento do Azure.
services: storage
author: tamram
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 05/11/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: cd8ba51b960703fa25371d874ed2bb50e7df2fde
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360046"
---
# <a name="client-side-encryption-with-python-for-microsoft-azure-storage"></a>Criptografia do lado do cliente com Python para o Armazenamento do Microsoft Azure
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Visão geral
A [Biblioteca do cliente do Armazenamento do Azure para Python](https://pypi.python.org/pypi/azure-storage) dá suporte à criptografia de dados em aplicativos cliente antes do upload no Armazenamento do Azure e à descriptografia de dados durante o download para o cliente.

> [!NOTE]
> A biblioteca do Python do Armazenamento do Azure está em preview.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Criptografia e descriptografia com a técnica de envelope
Os processos de criptografia e descriptografia seguem a técnica de envelope.

### <a name="encryption-via-the-envelope-technique"></a>Criptografia com a técnica de envelope
A criptografia com a técnica de envelope funciona da seguinte maneira:

1. A biblioteca do cliente do Armazenamento do Azure gera um CEK (Chave de Criptografia de Conteúdo) que é uma chave simétrica de uso único.
2. Os dados do usuário são criptografados usando esse CEK.
3. O CEK é empacotada (criptografada) usando o KEK (Chave de Criptografia de Chave). A KEK é identificada por um identificador de chave e pode ser um par de chaves assimétricas ou uma chave simétrica, que é gerenciada localmente.
   A biblioteca de cliente de armazenamento em si nunca tem acesso ao KEK. Ela simplesmente invoca o algoritmo de empacotamento de chave fornecido pela KEK. Os usuários podem escolher usar provedores personalizados para desempacotamento/quebra da chave, se desejado.
4. Os dados criptografados, em seguida, são carregados para o serviço de Armazenamento do Azure. A chave empacotada junto com alguns metadados adicionais de criptografia está armazenada como metadados (em um blob) ou interpolada com os dados criptografados (fila de mensagens e entidades de tabela).

### <a name="decryption-via-the-envelope-technique"></a>Descriptografia com a técnica de envelope
A descriptografia com a técnica de envelope funciona da seguinte maneira:

1. A biblioteca de cliente presume que o usuário está gerenciando a KEK (chave de criptografia de chave) localmente. O usuário não precisa conhecer a chave específica que foi usada para criptografia. Em vez disso, um resolvedor de chave, que resolve diferentes identificadores de chaves, pode ser configurado e usado.
2. A biblioteca de cliente baixa os dados criptografados junto com demais materiais de criptografia armazenados no serviço.
3. O CEK (chave de criptografia de conteúdo) empacotado é então desempacotado usando KEK. Novamente aqui, a biblioteca de cliente não tem acesso a KEK. Ela simplesmente invoca o algoritmo de descompactação do provedor personalizado.
4. A CEK (chave de criptografia de conteúdo) é então usada para descriptografar os dados de usuário criptografados.

## <a name="encryption-mechanism"></a>Mecanismo de criptografia
A biblioteca de cliente de armazenamento usa [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) para criptografar os dados do usuário. Especificamente, o modo [CBC (Encadeamento de Blocos de Criptografia)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) com AES. Cada serviço funciona ligeiramente diferente, portanto, discutiremos cada uma deles aqui.

### <a name="blobs"></a>Blobs
Atualmente, a biblioteca de cliente dá suporte à criptografia somente de blobs completos. Especificamente, a criptografia tem suporte quando os usuários utilizam os métodos **create**\*. Para downloads, os downloads completos e de intervalo têm suporte e a paralelização do download e do upload está disponível.

Durante a criptografia, a biblioteca de cliente gerará um vetor de inicialização aleatório (IV) de 16 bytes, juntamente com uma chave de criptografia aleatória de conteúdo (CEK) de 32 bytes e executará a criptografia de envelope dos dados blob usando essas informações. O CEK encapsulado e alguns metadados adicionais de criptografia são armazenadas como metadados com o blob criptografado no serviço de blob.

> [!WARNING]
> Se você estiver editando ou carregando seus próprios metadados para o blob, deverá garantir que esses metadados sejam preservado. Se você carregar novos metadados sem esses metadados, o CEK encapsulado, IV e outros metadados serão perdidos e o conteúdo do blob nunca mais poderá ser recuperado.
> 
> 

Baixar um blob criptografado envolve a recuperação do conteúdo do blob inteiro usando os métodos de conveniência **get**\*. O CEK encapsulado é desempacotado e usado em conjunto com o IV (armazenado como metadados de blob neste caso) para retornar os dados descriptografados para os usuários.

Baixar um intervalo arbitrário (métodos**get*** com parâmetros de intervalo passados) no blob criptografado envolve o ajuste do intervalo fornecido pelos usuários para obter uma pequena quantidade de dados adicionais que podem ser usados para descriptografar com êxito o intervalo solicitado.

Os blobs de blocos e os blobs de páginas podem ser criptografados/descriptografadas usando este esquema. No momento, não há suporte para a criptografia de blobs de acréscimo.

### <a name="queues"></a>Filas
Uma vez que a fila de mensagens pode ser de qualquer formato, a biblioteca de cliente define um formato personalizado que inclui o vetor de inicialização (IV) e a chave de criptografia do conteúdo criptografado (CEK) no texto da mensagem.

Durante a criptografia, a biblioteca de cliente gera um IV aleatório de 16 bytes com um CEK aleatória de 32 bytes e executa criptografia de envelope do texto da mensagem de fila usando essas informações. O CEK encapsulado e alguns metadados adicionais de criptografia são adicionados à mensagem da fila criptografada. Essa mensagem modificada (mostrada abaixo) é armazenada no serviço.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

Durante a descriptografia, a chave encapsulada é extraída da mensagem da fila e envolta. O IV também é extraído da mensagem da fila e usado juntamente com a chave desencapsulada para descriptografar os dados de mensagem da fila. Observe que os metadados de criptografia são pequeno (abaixo de 500 bytes), por isso embora contem para o limite de 64 KB para uma mensagem da fila, tal impacto é administrável.

### <a name="tables"></a>Tabelas
A biblioteca de cliente dá suporte à criptografia de propriedades de entidade para as operações de inserção e substituição.

> [!NOTE]
> Atualmente não há suporte para a mesclagem. Como um subconjunto de propriedades pode ter sido criptografado anteriormente usando uma chave diferente, simplesmente mesclar as novas propriedades e atualizar os metadados resultará em perda de dados. Mesclar requer fazer chamadas de serviço extra para ler a entidade já existente no serviço ou usar uma nova chave por propriedade, os quais não são ambos adequados por motivos de desempenho.
> 
> 

Criptografia de dados de tabela funciona da seguinte maneira:

1. Os usuários especificam as propriedades que devem ser criptografadas.
2. A biblioteca de cliente gera um vetor de inicialização aleatório (IV) de 16 bytes com uma chave de criptografia aleatória de conteúdo (CEK) de 32 bytes para cada entidade e executa a criptografia de envelope sobre as propriedades individuais que devem ser criptografadas, derivando uma nova IV por propriedade. A propriedade criptografada é armazenada como dados binários.
3. O CEK encapsulado e alguns metadados adicionais de criptografia, em seguida, são armazenados como duas propriedades reservadas adicionais. A primeira propriedade reservada (\_ClientEncryptionMetadata1) é uma propriedade de cadeia de caracteres que contém informações sobre o IV, versão e chave encapsulada. A segunda propriedade reservada (\_ClientEncryptionMetadata2) é uma propriedade binária que armazena as informações sobre as propriedades criptografadas. As informações nessa segunda propriedade (\_ClientEncryptionMetadata2) são criptografadas.
4. Devido a essas propriedades reservadas adicionais necessárias para a criptografia, os usuários agora podem ter apenas 250 propriedades personalizadas, em vez de 252. O tamanho total da entidade deve ser menor que 1 MB.

   Observe que somente as propriedades de cadeia de caracteres podem ser criptografadas. Se outros tipos de propriedades precisarem ser criptografados, elas devem ser convertidas em cadeias de caracteres. As cadeias de caracteres criptografadas são armazenadas no serviço como propriedades binárias e são convertidas novamente em cadeias de caracteres (cadeias de caracteres brutas, não EntityProperties com tipo EdmType.STRING) após a descriptografia.

   Para tabelas, além da política de criptografia, os usuários devem especificar as propriedades que devem ser criptografadas. Isso pode ser feito ao armazenar essas propriedades em objetos TableEntity com o tipo definido como EdmType.STRING e a criptografia definida como true ou ao definir a encryption_resolver_function no objeto tableservice. Um resolvedor de criptografia é uma função que usa uma chave de partição, a chave de linha e o nome da propriedade e retorna um valor booliano que indica se essa propriedade deve ser criptografada. Durante a criptografia, a biblioteca de cliente usará essas informações para decidir se uma propriedade deve ser criptografada durante a gravação para a transmissão. O representante também oferece a possibilidade de lógica em torno de como as propriedades são criptografadas. (Por exemplo, se X, então criptografar a propriedade A; caso contrário, criptografar as propriedades A e B.) Observe que não é necessário fornecer essas informações durante a leitura ou ap consultar entidades.

### <a name="batch-operations"></a>Operações em lote
Uma política de criptografia se aplica a todas as linhas no lote. A biblioteca de cliente gerará internamente um novo IV e CEK aleatórios por linha no lote. Os usuários também podem optar por criptografar propriedades diferentes para cada operação em lote definindo esse comportamento no resolvedor de criptografia.
Se um lote for criado como um gerenciador de contexto por meio do método batch() de tableservice, a política de criptografia do tableservice será aplicada automaticamente ao lote. Se um lote for criado explicitamente chamando o construtor, a política de criptografia deverá ser passada como um parâmetro e deixada sem modificações durante o tempo de vida do lote.
Observe que as entidades são criptografadas conforme elas são inseridas no lote usando a política de criptografia do lote (as entidades NÃO são criptografadas no momento da confirmação do lote usando a política de criptografia do tableservice).

### <a name="queries"></a>Consultas
> [!NOTE]
> Como as entidades são criptografadas, é possível executar consultas que filtram uma propriedade criptografada.  Se você tentar, os resultados estarão incorretos, porque o serviço tentará comparar dados criptografados com dados não criptografados.
> 
> 
> Para executar operações de consulta, você deve especificar que um resolvedor de chave é capaz de resolver todas as chaves no conjunto de resultados. Se uma entidade contida no resultado da consulta não puder ser resolvida para um provedor, a biblioteca de cliente gerará um erro. Para qualquer consulta que realiza as projeções de lado do servidor, a biblioteca de cliente adicionará as propriedades de metadados de criptografia especiais (\_ClientEncryptionMetadata1 e \_ClientEncryptionMetadata2) por padrão para as colunas selecionadas.
> 
> [!IMPORTANT]
> Lembre-se dos seguintes pontos importantes ao usar a criptografia no lado do cliente:
> 
> * Durante a leitura ou gravação de um blob criptografado, use comandos de carregamento de blob completos e comandos de download de blob completos/em intervalos. Evite gravar em um blob criptografado usando operações de protocolo tais como Put Block, Put Block List, Write Pages ou Clear Pages; caso contrário, você pode corromper o blob criptografado e torná-lo ilegível.
> * Para tabelas, existe uma restrição semelhante. Tenha cuidado para não atualizar propriedades criptografadas sem atualizar os metadados de criptografia.
> * Se você definir os metadados no blob criptografado, poderá substituir os metadados relacionados à criptografia necessários para a descriptografia, uma vez que a definição de metadados não é aditiva. Isso também ocorre em instantâneos. Evite especificar metadados ao criar um instantâneo de um blob criptografado. Se for necessário definir os metadados, lembre-se de chamar o método **get_blob_metadata** primeiro para obter os metadados de criptografia atuais e evitar gravações simultâneas enquanto os metadados estão sendo definidos.
> * Habilite o sinalizador **require_encryption** no objeto de serviço para usuários que devem trabalhar apenas com dados criptografados. Saiba mais logo abaixo.

A biblioteca de cliente de armazenamento espera a KEK fornecida e o resolvedor de chave para implementar a interface a seguir. [Cofre de Chaves do Azure](https://azure.microsoft.com/services/key-vault/) ao gerenciamento de Python KEK está pendente e será integrado a essa biblioteca quando estiver concluído.

## <a name="client-api--interface"></a>API do cliente / Interface
Depois que um objeto de serviço de armazenamento (isto é, blockblobservice) tiver sido criado, o usuário poderá atribuir valores aos campos que constituem uma política de criptografia: key_encryption_key, key_resolver_function e require_encryption. Os usuários podem fornecer somente uma KEK, somente um resolvedor ou ambos. key_encryption_key é o tipo de chave básico que é identificado usando um identificador de chave e que fornece a lógica para empacotamente/desempacotamento. key_resolver_function é usado para resolver uma chave durante o processo de descriptografia. Ele retorna uma KEK válida dado um identificador de chave. Isso fornece aos usuários a capacidade de escolher entre várias chaves que são gerenciadas em vários locais.

A KEK deve implementar os seguintes métodos para criptografar dados com êxito:

* wrap_key(cek): Empacota a CEK (bytes) especificada usando um algoritmo de preferência do usuário. Retorna a chave empacotada.
* get_key_wrap_algorithm(): Retorna o algoritmo usado para empacotar as chaves.
* get_kid(): Retorna a ID da chave de cadeia de caracteres para essa KEK.
  A KEK deve implementar os seguintes métodos para descriptografar dados com êxito:
* unwrap_key(cek, algoritmo): Retorna o formulário não empacotado da CEK especificada usando o algoritmo especificado pela cadeia de caracteres.
* get_kid(): Retorna uma ID da chave de cadeia de caracteres para essa KEK.

O resolvedor de chave deve pelo menos implementar um método que, dada uma ID de chave, retorna a KEK correspondente implementando a interface acima. Apenas este método deve ser atribuído à propriedade key_resolver_function no objeto de serviço.

* Para criptografia, a chave é usada sempre e a ausência de uma chave resultará em um erro.
* Para descriptografar:

  * O resolvedor de chave é invocado se especificado para obter a chave. Se o resolvedor for especificado, mas não tiver um mapeamento para o identificador de chave, um erro será gerado.
  * Se o resolvedor não for especificado, mas uma chave for especificada, a chave será usada se o identificador corresponder ao identificador da chave necessária. Se o identificador não corresponder, um erro será gerado.

    Os exemplos de criptografia no Azure. Storage. Samples demonstram um cenário de ponta a ponta mais detalhado para BLOBs, filas e tabelas.
      As implementações de exemplo da KEK e do resolvedor de chave são fornecidas nos arquivos de exemplo como KeyWrapper e KeyResolver respectivamente.

### <a name="requireencryption-mode"></a>Modo RequireEncryption
Os usuários podem habilitar opcionalmente um modo de operação no qual todos os downloads e uploads devem ser criptografados. Nesse modo, as tentativas de carregamento de dados sem uma política de criptografia ou o download de dados que não são criptografados no serviço falharão no cliente. O sinalizador **require_encryption** no objeto de serviço controla esse comportamento.

### <a name="blob-service-encryption"></a>Criptografia do serviço Blob
Defina os campos de política de criptografia no objeto blockblobservice. Todo o resto será tratado pela biblioteca de cliente internamente.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_block_blob_service.key_encryption_key = kek
my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

# Upload the encrypted contents to the blob.
my_block_blob_service.create_blob_from_stream(
    container_name, blob_name, stream)

# Download and decrypt the encrypted contents from the blob.
blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)
```

### <a name="queue-service-encryption"></a>Criptografia do serviço Fila
Defina os campos de política de criptografia no objeto queueservice. Todo o resto será tratado pela biblioteca de cliente internamente.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_queue_service.key_encryption_key = kek
my_queue_service.key_resolver_funcion = key_resolver.resolve_key

# Add message
my_queue_service.put_message(queue_name, content)

# Retrieve message
retrieved_message_list = my_queue_service.get_messages(queue_name)
```

### <a name="table-service-encryption"></a>Criptografia do serviço Tabela
Além de criar uma política de criptografia e defini-la nas opções de solicitação, você precisa especificar **encryption_resolver_function** no **tableservice** ou definir o atributo encrypt na EntityProperty.

### <a name="using-the-resolver"></a>Usando o resolvedor

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Define the encryption resolver_function.


def my_encryption_resolver(pk, rk, property_name):
    if property_name == 'foo':
        return True
    return False


# Set the KEK and key resolver on the service object.
my_table_service.key_encryption_key = kek
my_table_service.key_resolver_funcion = key_resolver.resolve_key
my_table_service.encryption_resolver_function = my_encryption_resolver

# Insert Entity
my_table_service.insert_entity(table_name, entity)

# Retrieve Entity
# Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
my_table_service.get_entity(
    table_name, entity['PartitionKey'], entity['RowKey'])
```

### <a name="using-attributes"></a>Usando atributos
Conforme mencionado acima, uma propriedade pode ser marcada para criptografia armazenando-a em um objeto EntityProperty e definindo o campo encrypt.

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```

## <a name="encryption-and-performance"></a>Criptografia e desempenho
Observe que criptografar seu armazenamento de dados resulta em uma sobrecarga adicional no desempenho. O IV e a chave de conteúdo devem ser gerados, o próprio conteúdo deve ser criptografado e os metadados adicionais devem ser formatados e carregados. Essa sobrecarga poderá variar dependendo da quantidade de dados que está sendo criptografada. Recomendamos que os clientes sempre testem seus aplicativos a fim de verificar o desempenho durante o desenvolvimento.

## <a name="next-steps"></a>Próximas etapas
* Baixar a [Biblioteca do cliente do Armazenamento do Azure para o pacote Java PyPi](https://pypi.python.org/pypi/azure-storage)
* Baixar o [Código-fonte da Biblioteca do cliente do Armazenamento do Azure para Python do GitHub](https://github.com/Azure/azure-storage-python)
