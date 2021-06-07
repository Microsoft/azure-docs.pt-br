---
title: API do Horizon
description: Este guia descreve os métodos de horizonte comumente usados.
ms.date: 1/5/2021
ms.topic: article
ms.openlocfilehash: b65f7663df29e2c82faa5d1aeec3b820d5fbaf70
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786780"
---
# <a name="horizon-api"></a>API do Horizon 

Este guia descreve os métodos de horizonte comumente usados.

### <a name="getting-more-information"></a>Obtendo mais informações

Para obter mais informações sobre como trabalhar com o horizonte e a plataforma defender para IoT, consulte as seguintes informações:

- Para o SDK do ODE (ambiente de desenvolvimento aberto) do horizonte, entre em contato com seu representante do defender para IoT.
- Para obter suporte e informações de solução de problemas, entre em contato com <support@cyberx-labs.com> .

- Para acessar o guia do usuário do defender para IoT no console do defender para IoT, selecione :::image type="icon" source="media/references-horizon-api/profile.png"::: e, em seguida, selecione **baixar guia do usuário**.


## `horizon::protocol::BaseParser`

Abstract para todos os plug-ins. Isso consiste em dois métodos:

- Para processar filtros de plug-in definidos acima de você. Dessa forma, o horizonte sabe como se comunicar com o analisador.
- Para processar os dados reais.

## `std::shared_ptr<horizon::protocol::BaseParser> create_parser()`

A primeira função que é chamada para o plug-in cria uma instância do analisador para o horizonte para reconhecê-lo e registrá-lo.

### <a name="parameters"></a>Parâmetros 

Nenhum.

### <a name="return-value"></a>Retornar valor

shared_ptr à instância do analisador.

## `std::vector<uint64_t> horizon::protocol::BaseParser::processDissectAs(const std::map<std::string, std::vector<std::string>> &) const`

Essa função será chamada para cada plug-in registrado acima. 

Na maioria dos casos, isso estará vazio. Lance uma exceção para que o horizonte saiba que algo errado aconteceu.

### <a name="parameters"></a>Parâmetros 

- Um mapa que contém a estrutura de dissect_as, conforme definido na config.jsde outro plug-in que deseja registrar sobre você.

### <a name="return-value"></a>Retornar valor 

Uma matriz de uint64_t, que é o registro processado em um tipo de uint64_t. Isso significa que, no mapa, você terá uma lista de portas cujos valores serão os uin64_t.

## `horizon::protocol::ParserResult horizon::protocol::BaseParser::processLayer(horizon::protocol::management::IProcessingUtils &,horizon::general::IDataBuffer &)`

A função main. Especificamente, a lógica do plug-in, cada vez que um novo pacote atinge o analisador. Essa função será chamada, tudo relacionado ao processamento de pacotes deve ser feito aqui.

### <a name="considerations"></a>Considerações

Seu plug-in deve ser thread-safe, pois essa função pode ser chamada de threads diferentes. Uma boa abordagem seria definir tudo na pilha.

### <a name="parameters"></a>Parâmetros

- A unidade de controle do SDK responsável por armazenar os dados e criar objetos relacionados ao SDK, como ILayer e campos.
- Um auxiliar para ler os dados do pacote bruto. Ele já está definido com a ordem de byte que você definiu no config.jsem.

### <a name="return-value"></a>Retornar valor 

O resultado do processamento. Isso pode ser *êxito*, *malformado* ou de *sanidade*.

## `horizon::protocol::SanityFailureResult: public horizon::protocol::ParserResult`

Marca o processamento como saneamento falha, o que significa que o pacote não é reconhecido pelo protocolo atual e o horizonte deve passá-lo para outro analisador, se houver algum registrado nos mesmos filtros.

## `horizon::protocol::SanityFailureResult::SanityFailureResult(uint64_t)`

Construtor

### <a name="parameters"></a>Parâmetros 

- Define o código de erro usado pelo horizonte para registro em log, conforme definido no config.jsem.

## `horizon::protocol::MalformedResult: public horizon::protocol::ParserResult`

Resultado malformado, indicado, já reconhecemos o pacote como nosso protocolo, mas alguma validação deu errado (bits reservados estão ativados ou algum campo está ausente).

## `horizon::protocol::MalformedResult::MalformedResult(uint64_t)`

Construtor

### <a name="parameters"></a>Parâmetros  

- Código de erro, conforme definido em config.jsem.

## `horizon::protocol::SuccessResult: public horizon::protocol::ParserResult`

Notifica o horizonte do processamento bem-sucedido. Quando bem-sucedido, o pacote foi aceito, os dados pertencem a nós e todos os dados foram extraídos.

## `horizon::protocol::SuccessResult()`

Construtor. Um resultado básico foi criado com êxito. Isso significa que não sabemos a direção ou quaisquer outros metadados referentes ao pacote.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection)`

Construtor.

### <a name="parameters"></a>Parâmetros 

- A direção do pacote, se identificado. Os valores podem ser *solicitação* ou *resposta*.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection, const std::vector<uint64_t> &)`

Construtor.

### <a name="parameters"></a>Parâmetros

- A direção do pacote, se o identificamos, pode ser *solicitação*, *resposta*.
- Avisos. Esses eventos não terão falha, mas o horizonte será notificado.

## `horizon::protocol::SuccessResult(const std::vector<uint64_t> &)`

Construtor.

### <a name="parameters"></a>Parâmetros 

-  Avisos. Esses eventos não terão falha, mas o horizonte será notificado.

## `HorizonID HORIZON_FIELD(const std::string_view &)`

Converte uma referência baseada em cadeia de caracteres em um nome de campo (por exemplo, function_code) para o Horizonteid.

### <a name="parameters"></a>Parâmetros 

- Cadeia de caracteres a ser convertida.

### <a name="return-value"></a>Retornar valor

- Horizonteid criado a partir da cadeia de caracteres.

## `horizon::protocol::ILayer &horizon::protocol::management::IProcessingUtils::createNewLayer()`

Cria uma nova camada para que o horizonte saiba que o plug-in deseja armazenar alguns dados. Esta é a unidade de armazenamento base que você deve usar.

### <a name="return-value"></a>Retornar valor

Uma referência a uma camada criada, para que você possa adicionar dados a ela.

## `horizon::protocol::management::IFieldManagement &horizon::protocol::management::IProcessingUtils::getFieldsManager()`

Obtém o objeto de gerenciamento de campo, que é responsável pela criação de campos em diferentes objetos, por exemplo, em ILayer.

### <a name="return-value"></a>Retornar valor

Uma referência ao gerente.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, uint64_t)`

Cria um novo campo numérico de 64 bits na camada com a ID solicitada.

### <a name="parameters"></a>Parâmetros 

- A camada que você criou anteriormente.
- Horizonteid criado pela macro **HORIZON_FIELD** .
- O valor bruto que você deseja armazenar.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::string)`

Cria um novo campo de cadeia de caracteres de na camada com a ID solicitada. A memória será movida, portanto, tenha cuidado. Você não poderá usar esse valor novamente.

### <a name="parameters"></a>Parâmetros  

- A camada que você criou anteriormente.
- Horizonteid criado pela macro **HORIZON_FIELD** .
- O valor bruto que você deseja armazenar.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::vector<char> &)`

Cria um novo campo de valor bruto (matriz de bytes) de na camada, com a ID solicitada. A memória será movida, portanto, seja cauteloso, você não poderá usar esse valor novamente.

### <a name="parameters"></a>Parâmetros

- A camada que você criou anteriormente.
- Horizonteid criado pela macro **HORIZON_FIELD** .
- O valor bruto que você deseja armazenar.

## `horizon::protocol::IFieldValueArray &horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, horizon::protocol::FieldValueType)`

Cria um campo de valor de matriz (matriz) na camada do tipo especificado com a ID solicitada.

### <a name="parameters"></a>Parâmetros

- A camada que você criou anteriormente.
- Horizonteid criado pela macro **HORIZON_FIELD** .
- O tipo de valores que serão armazenados dentro da matriz.

### <a name="return-value"></a>Retornar valor

Referência a uma matriz à qual você deve acrescentar valores.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, uint64_t)`

Acrescenta um novo valor inteiro à matriz criada anteriormente.

### <a name="parameters"></a>Parâmetros

- A matriz criada anteriormente.
- O valor bruto a ser armazenado na matriz.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::string)`

Acrescenta um novo valor de cadeia de caracteres à matriz criada anteriormente. A memória será movida, portanto, seja cauteloso, você não poderá usar esse valor novamente.

### <a name="parameters"></a>Parâmetros

- A matriz criada anteriormente.
- Valor bruto a ser armazenado na matriz.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::vector<char> &)`

Anexa um novo valor bruto à matriz criada anteriormente. A memória será movida, portanto, seja cauteloso, você não poderá usar esse valor novamente.

### <a name="parameters"></a>Parâmetros

- A matriz criada anteriormente.
- Valor bruto a ser armazenado na matriz.

## `bool horizon::general::IDataBuffer::validateRemainingSize(size_t)`

Verifica se o buffer contém pelo menos X bytes.

### <a name="parameters"></a>Parâmetros

O número de bytes que devem existir.

### <a name="return-value"></a>Retornar valor

True se o buffer contiver pelo menos X bytes. Caso contrário, será `False`.

## `uint8_t horizon::general::IDataBuffer::readUInt8()`

Lê o valor de uint8 (1 byte), do buffer, de acordo com a ordem de bytes.

### <a name="return-value"></a>Retornar valor

O valor lido do buffer.

## `uint16_t horizon::general::IDataBuffer::readUInt16()`

Lê o valor UInt16 (2 bytes) do buffer, de acordo com a ordem de bytes.

### <a name="return-value"></a>Retornar valor

O valor lido do buffer.

## `uint32_t horizon::general::IDataBuffer::readUInt32()`

Lê o valor UInt32 (4 bytes) do buffer de acordo com a ordem de bytes.

### <a name="return-value"></a>Retornar valor

O valor lido do buffer.

## `uint64_t horizon::general::IDataBuffer::readUInt64()`

Lê o valor UInt64 (8 bytes) do buffer, de acordo com a ordem de bytes.

### <a name="return-value"></a>Retornar valor

O valor lido do buffer.

## `void horizon::general::IDataBuffer::readIntoRawData(void *, size_t)`

As leituras na memória pré-configurada, de um tamanho especificado, realmente copiarão os dados para sua região de memória.

### <a name="parameters"></a>Parâmetros 

- A região da memória na qual os dados são copiados.
- Tamanho da região da memória, esse parâmetro também definiu quantos bytes serão copiados.

## `std::string_view horizon::general::IDataBuffer::readString(size_t)`

Lê uma cadeia de caracteres do buffer.

### <a name="parameters"></a>Parâmetros 

- O número de bytes que devem ser lidos.

### <a name="return-value"></a>Retornar valor

A referência à região de memória da cadeia de caracteres.

## `size_t horizon::general::IDataBuffer::getRemainingData()`

Informa quantos bytes são deixados no buffer.

### <a name="return-value"></a>Retornar valor

Tamanho restante do buffer.

## `void horizon::general::IDataBuffer::skip(size_t)`

Ignora X bytes no buffer.

### <a name="parameters"></a>Parâmetros

- Número de bytes a serem ignorados.
