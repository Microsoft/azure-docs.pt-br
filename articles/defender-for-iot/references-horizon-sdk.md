---
title: SDK do Horizon
titleSuffix: Azure Defender for IoT
description: O SDK do horizonte permite que os desenvolvedores do Azure defender para IoT projetem plugins de dessetor que decodifiquem o tráfego de rede para que possam ser processados por programas de análise de rede do defender para IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/13/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 53aafc4146680c89dd01174ec5fde765f1cc0c01
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746012"
---
# <a name="horizon-proprietary-protocol-dissector"></a>Dessetor de protocolo proprietário do horizonte

O horizonte é um ODE (ambiente de desenvolvimento aberto) usado para proteger dispositivos IoT e ICS que executam protocolos proprietários.

Esse ambiente fornece as seguintes soluções para clientes e parceiros de tecnologia:

- Suporte ilimitado e completo para protocolos comuns, proprietários e personalizados que se desviam de qualquer padrão. 

- Um novo nível de flexibilidade e escopo para o desenvolvimento de DPI.

- Uma ferramenta que se expande de acordo com a visibilidade e o controle, sem a necessidade de atualizar o defender para versões da plataforma IoT.

- A segurança de permitir o desenvolvimento proprietário sem divulgar informações confidenciais.

O SDK do horizonte permite que os desenvolvedores do Azure defender para IoT projetem plugins de dessetor que decodifiquem o tráfego de rede para que possam ser processados por programas de análise de rede do defender para IoT.

Os dessetors de protocolo são desenvolvidos como plug-ins externos e são integrados a uma ampla gama de serviços do defender para IoT. Por exemplo, serviços que fornecem recursos de monitoramento, alertas e relatórios.

## <a name="secure-development-environment"></a>Ambiente de desenvolvimento seguro 

O ODE do horizonte permite o desenvolvimento de protocolos proprietários ou personalizados que não podem ser compartilhados fora de uma organização. Por exemplo, devido a regulamentos legais ou políticas corporativas.

Desenvolva plugins de dessetor sem: 

- revelar qualquer informação proprietária sobre como os protocolos são definidos.

- compartilhando qualquer um dos seus PCAPs confidenciais.

- violando os regulamentos de conformidade.

## <a name="customization-and-localization"></a>Personalização e localização  

O SDK dá suporte a várias opções de personalização, incluindo:

  - Texto para códigos de função. 

  - Texto de localização completo para alertas, eventos e parâmetros de protocolo. Para obter mais informações, consulte [criar arquivos de mapeamento (JSON)](#create-mapping-files-json).

  :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="Exibir alertas totalmente localizados.":::

## <a name="horizon-architecture"></a>Arquitetura de horizonte

O modelo de arquitetura inclui três camadas de produto.

:::image type="content" source="media/references-horizon-sdk/architecture.png" alt-text="https://lh6.googleusercontent.com/YFePqJv_6jbI_oy3lCQv-hHB1Qly9a3QQ05uMnI8UdTwhOuxpNAedj_55wseYEQQG2lue8egZS-mlnQZPWfFU1dF4wzGQSJIlUqeXEHg9CG4M7ASCZroKgbghv-OaNoxr3AIZtIh":::

## <a name="defender-for-iot-platform-layer"></a>Camada da plataforma do defender para IoT

Habilita a integração imediata e o monitoramento em tempo real de plugins de dessetor personalizados na plataforma defender para IoT, sem a necessidade de atualizar a versão da plataforma defender para IoT.

## <a name="defender-for-iot-services-layer"></a>Camada de serviços do defender para IoT

Cada serviço é projetado como um pipeline, dissociado de um protocolo específico, permitindo desenvolvimento mais eficiente e independente.

Cada serviço é projetado como um pipeline, dissociado de um protocolo específico. Os serviços escutam o tráfego no pipeline. Eles interagem com os dados do plug-in e o tráfego capturado pelos sensores para indexar protocolos implantados e analisar a carga de tráfego e permitem um desenvolvimento mais eficiente e independente.

## <a name="custom-dissector-layer"></a>Camada de issector personalizada 

Habilita a criação de plug-ins usando o SDK proprietário do defender para IoT (incluindo a implementação de C++ e a configuração JSON) para: 

- Definir como identificar o protocolo

- Definir como mapear os campos que você deseja extrair do tráfego e extraí-los 

- Definir como integrar com os serviços do defender para IoT

  :::image type="content" source="media/references-horizon-sdk/layers.png" alt-text="As camadas internas.":::

O defender para IoT fornece dessetors básicos para protocolos comuns. Você pode criar seus dessetors sobre esses protocolos.

## <a name="before-you-begin"></a>Antes de começar

## <a name="what-this-sdk-contains"></a>O que este SDK contém 

Este kit contém os arquivos de cabeçalho necessários para o desenvolvimento. O processo de desenvolvimento requer etapas básicas e etapas avançadas opcionais, descritas neste SDK.

Entre em contato <support@cyberx-labs.com> para obter informações sobre como receber arquivos de cabeçalho e outros recursos.

## <a name="about-the-environment-and-setup"></a>Sobre o ambiente e a instalação 

### <a name="requirements"></a>Requisitos 

- O ambiente de desenvolvimento preferencial é o Linux. Se você estiver desenvolvendo em um ambiente do Windows, considere o uso de uma VM com um sistema Linux.

- Para o processo de compilação, use GCC 7.4.0 ou superior. Use qualquer classe padrão do stdlib que tenha suporte em C++ 17.

- Defender para IoT versão 3,0 e posterior.

### <a name="process"></a>Processo

1. [Baixe](https://www.eclipse.org/) o Eclipse IDE para desenvolvedores de C/C++. Você pode usar qualquer outro IDE que preferir. Este documento orienta você pela configuração usando o IDE do eclipse.

1. Depois de iniciar o Eclipse IDE e configurar o espaço de trabalho (onde seus projetos serão armazenados), pressione **Ctrl + n** e crie-o como um projeto C++.

1. Na próxima tela, defina o nome para o protocolo que você deseja desenvolver e selecione o tipo de projeto como `Shared Library` e `AND Linux GCC` .

1. Edite as propriedades do projeto , em  >    >  **configurações da ferramenta**  >    >  configurações  >  de compilação C/C++

1. Cole os códigos de exemplo que você recebeu com o SDK e compile-os.

1. Adicione os artefatos (biblioteca, config.jsem e metadados) a um arquivo tar. gz e altere a extensão de arquivo para \<XXX> . HDP, em que é \<XXX> o nome do plug-in.

### <a name="research"></a>Pesquisa 

Antes de começar, verifique se você:

- Leia a especificação de protocolo, se disponível.

- Saiba quais campos de protocolo você planeja extrair.

- Planejou seus objetivos de mapeamento.

## <a name="about-plugin-files"></a>Sobre arquivos de plug-in 

Três arquivos são definidos durante o processo de desenvolvimento.

### <a name="json-configuration-file-required"></a>Arquivo de configuração JSON (obrigatório) 

Esse arquivo deve definir a ID de dessetor e as declarações, as dependências, os requisitos de integração, os parâmetros de validação e as definições de mapeamento para converter valores em nomes, números em texto. Para obter mais informações, consulte os links a seguir:

- [Preparar o arquivo de configuração (JSON)](#prepare-the-configuration-file-json)

- [Preparar validações de código de implementação](#prepare-implementation-code-validations)

- [Extrair metadados do dispositivo](#extract-device-metadata)

- [Conectar-se a um serviço de indexação (linha de base)](#connect-to-an-indexing-service-baseline)

### <a name="implementation-code-c-required"></a>Código de implementação: C++ (obrigatório)

O código de implementação (CPP) analisa o tráfego bruto e o mapeia para valores como serviços, classes e códigos de função. Ele extrai os campos de camada e os mapeia para os nomes de índice dos arquivos de configuração JSON. Os campos a serem extraídos de CPP são definidos no arquivo de configuração. para obter mais informações, consulte [preparar o código de implementação (C++)](#prepare-the-implementation-code-c).

### <a name="mapping-files-optional"></a>Arquivos de mapeamento (opcional)

Você pode personalizar o texto de saída do plug-in para atender às necessidades do seu ambiente corporativo.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="as":::

Você pode definir e atualizar os arquivos de mapeamento para atualizar o texto sem alterar o código. Cada arquivo pode mapear um ou vários campos:

  - Mapeamento de valores de campo para nomes, por exemplo, 1: redefinir, 2: Iniciar, 3: parar.

  - Mapeando texto para dar suporte a vários idiomas.

Para obter mais informações, consulte [criar arquivos de mapeamento (JSON)](#create-mapping-files-json).

## <a name="create-a-dissector-plugin-overview"></a>Criar um plug-in de dessetor (visão geral)

1. Examine a seção [sobre o ambiente e a instalação](#about-the-environment-and-setup) .

2.  [Prepare o código de implementação (C++)](#prepare-the-implementation-code-c). Copie o **template.jsno** arquivo e edite-o para atender às suas necessidades. Não altere as chaves. 

3. [Prepare o arquivo de configuração (JSON)](#prepare-the-configuration-file-json). Copie o arquivo **Template. cpp** e implemente um método override. Para obter mais informações, consulte [Horizonte::p rotocol:: BaseParser](#horizonprotocolbaseparser) para obter detalhes.

4. [Preparar validações de código de implementação](#prepare-implementation-code-validations).

## <a name="prepare-the-implementation-code-c"></a>Preparar o código de implementação (C++)

O arquivo CPP é um analisador responsável por:

- Validar o cabeçalho e a carga do pacote (por exemplo, o comprimento do cabeçalho ou a estrutura de carga).

- Extraindo dados do cabeçalho e da carga em campos definidos.

- Implementando a extração de campos configurados pelo arquivo JSON.

### <a name="what-to-do"></a>O que fazer

Copie o arquivo template **. cpp** e implemente um método override. Para obter mais informações, consulte [Horizonte::p rotocol:: BaseParser](#horizonprotocolbaseparser).

### <a name="basic-c-template-sample"></a>Exemplo de modelo C++ básico 

Esta seção fornece o modelo de protocolo básico, com funções padrão para um protocolo de exemplo do defender para IoT horizonte.

```C++
#include “plugin/plugin.h”
namespace {
 class CyberxHorizonSDK: public horizon::protocol::BaseParser
  public:
   std::vector<uint64_t> processDissectAs(const std::map<std::string,
                                                         std::vector<std::string>> &filters) const override {
     return std::vector<uint64_t>();
   }
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     return horizon::protocol::ParserResult();
   }
 };
}

extern "C" {
  std::shared_ptr<horizon::protocol::BaseParser> create_parser() {
    return std::make_shared<CyberxHorizonSDK>();
  }
}

```

### <a name="basic-c-template-description"></a>Descrição do modelo básico do C++  

Esta seção fornece o modelo de protocolo básico, com uma descrição das funções padrão para um protocolo de exemplo defender para IoT horizonte. 

### <a name="include-pluginpluginh"></a>#include "plugin/plugin. h"

A definição usada pelo plug-in. O arquivo de cabeçalho contém tudo o que é necessário para concluir o desenvolvimento.

### <a name="horizonprotocolbaseparser"></a>Horizonte::p rotocol:: BaseParser

A interface de comunicação entre a infraestrutura de horizonte e a camada de plug-in. Para obter mais informações, consulte [arquitetura de horizonte](#horizon-architecture) para obter uma visão geral das camadas.

O processLayer é o método usado para processar dados.

- O primeiro parâmetro no código de função é o utilitário de processamento usado para recuperar dados processados anteriormente, criar novos campos e camadas.

- O segundo parâmetro no código de função são os dados atuais passados do analisador anterior.

```C++
horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer &data) override {

```

### <a name="create_parser"></a>create_parser

Use para criar a instância do analisador.

:::image type="content" source="media/references-horizon-sdk/code.png" alt-text="https://lh5.googleusercontent.com/bRNtyLpBA3LvDXttSPbxdBK7sHiHXzGXGhLiX3hJ7zCuFhbVsbBhgJlKI6Fd_yniueQqWbClg5EojDwEZSZ219X1Z7osoa849iE9X8enHnUb5to5dzOx2bQ612XOpWh5xqg0c4vR":::

## <a name="protocol-function-code-sample"></a>Exemplo de código de função de protocolo 

Esta seção fornece um exemplo de como o número de código (2 bytes) e o comprimento da mensagem (4 bytes) são extraídos.

Isso é feito de acordo com a endian fornecida no arquivo de configuração JSON, o que significa que se o protocolo for *little endian* e o sensor for executado em um computador com pouca endian, ele será convertido.

Uma camada também é criada para armazenar dados. Use o *fieldmanager* dos utils de processamento para criar novos campos. Um campo pode ter apenas um dos seguintes tipos: *cadeia de caracteres*, *número*, *dados brutos*, *matriz* (de tipo específico) ou *complexo*. Essa camada pode conter um número, um RAW ou uma cadeia de caracteres com ID.

No exemplo abaixo, os dois campos a seguir são extraídos:

- `function_code_number`

- `headerLength`

Uma nova camada é criada e o campo extraído é copiado nela.

O exemplo a seguir descreve uma função específica, que é a lógica principal implementada para processamento de plug-in.

```C++
namespace {
 class CyberxHorizonProtocol: public horizon::protocol::BaseParser {
  public:
   
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     uint16_t codeNumber = data.readUInt16();
     uint32_t headerLength = data.readUInt32();
 
     auto &layer = ctx.createNewLayer();
 
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("code_number"),codeNumber;    
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("header_length"),headerLength);     
     return horizon::protocol::SuccessResult();
   }
 

```

### <a name="related-json-field"></a>Campo JSON relacionado 

:::image type="content" source="media/references-horizon-sdk/json.png" alt-text="O campo JSON relacionado.":::

## <a name="prepare-the-configuration-file-json"></a>Preparar o arquivo de configuração (JSON)

O SDK do horizonte usa o padrão JavaScript Object Notation (JSON), um formato leve para armazenar e transportar dados e não requer linguagens de script proprietárias.

Esta seção descreve as declarações de configuração de JSON mínimas, a estrutura relacionada e fornece um arquivo de configuração de exemplo que define um protocolo. Esse protocolo é integrado automaticamente com o serviço de descoberta de dispositivo.

## <a name="file-structure"></a>Estrutura do arquivo

O exemplo a seguir descreve a estrutura do arquivo.

:::image type="content" source="media/references-horizon-sdk/structure.png" alt-text="A amostra da estrutura do arquivo.":::

### <a name="what-to-do"></a>O que fazer

Copie o arquivo de modelo `config.json` e edite-o para atender às suas necessidades. Não altere a chave. As chaves são marcadas em vermelho no [arquivo de configuração JSON de exemplo](#sample-json-configuration-file). 

### <a name="file-naming-requirements"></a>Requisitos de nomenclatura de arquivo 

O arquivo de configuração JSON deve ser salvo como `config.json` .

### <a name="json-configuration-file-fields"></a>Campos de arquivo de configuração JSON

Esta seção descreve os campos de configuração JSON que serão definidas. Não altere os *Rótulos* dos campos.

### <a name="basic-parameters"></a>Parâmetros básicos

Esta seção descreve os parâmetros básicos.

| Rótulo de parâmetro | Descrição | Type |
|--|--|--|
| **ID** | O nome do protocolo. Exclua o padrão e adicione o nome do seu protocolo conforme ele for exibido. | String |
| **endianess** | Define como os dados de vários bytes são codificados. Use o termo "pequeno" ou "grande" apenas. Tirado da especificação de protocolo ou da gravação de tráfego | String |
| **sanity_failure_codes** | Esses são os códigos retornados do analisador quando há um conflito de sanidade em relação à identidade do código. Consulte validação do número mágico na seção C++. | String |
| **malformed_codes** | Esses são os códigos que foram identificados corretamente, mas um erro é detectado. Por exemplo, se o tamanho do campo for muito curto ou longo, ou um valor for inválido. | String |
| **dissect_as** | Uma matriz que define onde o tráfego de protocolo específico deve chegar. | TCP/UDP, porta etc. |
| **campos** | A declaração de quais campos serão extraídos do tráfego. Cada campo tem sua própria ID (nome) e tipo (numérico, Cadeia de caracteres, bruto, matriz, complexo). Por exemplo, a [função](https://docs.google.com/document/d/14nm8cyoGiaE0ODOYQd_xjULxVz9U_bjfPKkcDhOFr5Q/edit#bookmark=id.6s1zcxa9184k) de campo que é extraída no arquivo do analisador de implementação. Os campos gravados no arquivo de configuração são os únicos que podem ser adicionados à camada. |  |

### <a name="other-advanced-fields"></a>Outros campos avançados 

Esta seção descreve outros campos.

| Rótulo de parâmetro | Descrição |
|-----------------|--------|
| **listas de permissões** | Você pode indexar os valores de protocolo e exibi-los em relatórios de mineração de dados. Esses relatórios refletem sua linha de base de rede. :::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="Um exemplo da exibição de Data Mining."::: <br /> Para obter mais informações, consulte [conectar-se a um serviço de indexação (linha de base)](#connect-to-an-indexing-service-baseline) para obter detalhes. |
| **firmware** | Você pode extrair informações de firmware, definir valores de índice e disparar alertas de firmware para o protocolo de plug-in. Para obter mais informações, consulte [extrair dados de firmware](#extract-firmware-data) para obter detalhes. |
| **value_mapping** | Você pode personalizar o texto de saída do plug-in para atender às necessidades do seu ambiente corporativo definindo e atualizando arquivos de mapeamento. Por exemplo, mapeie para arquivos de idioma. As alterações podem ser facilmente implementadas em texto sem alterar ou afetar o código. Para obter mais informações, consulte [criar arquivos de mapeamento (JSON)](#create-mapping-files-json) para obter detalhes. |

## <a name="sample-json-configuration-file"></a>Arquivo de configuração JSON de exemplo

```json
{
  "id":"CyberX Horizon Protocol",
  "endianess": "big",
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
{
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ]
}


```

## <a name="prepare-implementation-code-validations"></a>Preparar validações de código de implementação

Esta seção descreve a implementação das funções de validação de código C++ e fornece o código de exemplo. Duas camadas de validação estão disponíveis:

- Sanity.

- Código malformado.

Você não precisa criar código de validação para criar um plug-in funcional. Se você não preparar o código de validação, poderá examinar os relatórios de mineração de dados de sensor como uma indicação de processamento bem-sucedido.

Os valores de campo podem ser mapeados para o texto em arquivos de mapeamento e atualizados diretamente sem afetar o processamento.

## <a name="sanity-code-validations"></a>Validações de código de sanidade 

Isso valida que o pacote transmitido corresponde aos parâmetros de validação do protocolo, o que ajuda a identificar o protocolo no tráfego.

Por exemplo, use os primeiros 8 bytes como o *número mágico*. Se a sanidade falhar, uma resposta de falha de sanidade será retornada.

Por exemplo:

```C++
  horizon::protocol::ParserResult 
processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer 
&data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }
```

Se outros plugins relevantes tiverem sido implantados, o pacote será validado em relação a eles.

## <a name="malformed-code-validations"></a>Validações de código malformadas 

Validações malformadas são usadas depois que o protocolo tiver sido validado positivamente.

Se houver uma falha ao processar os pacotes com base no protocolo, uma resposta de falha será retornada.

:::image type="content" source="media/references-horizon-sdk/failure.png" alt-text="código malformado":::

## <a name="c-sample-with-validations"></a>Exemplo de C++ com validações

De acordo com a função, o processo é executado, conforme mostrado no exemplo abaixo.

### <a name="function-20"></a>Função 20 

- Ele é processado como firmware.

- Os campos são lidos de acordo com a função.

- Os campos são adicionados à camada.

### <a name="function-10"></a>Função 10 

- A função contém outra subfunção, que é uma operação mais específica

- A subfunção é lida e adicionada à camada.

Quando isso for feito, o processamento será concluído. O valor de retorno indica se a camada de dessetor foi processada com êxito. Se foi, a camada torna-se utilizável.

```C++
#include "plugin/plugin.h"

#define FUNCTION_FIRMWARE_RESPONSE 20

#define FUNCTION_SUBFUNCTION_REQUEST 10

namespace {

class CyberxHorizonSDK: public horizon::protocol::BaseParser {

 public:

  std::vector<uint64_t> processDissectAs(const std::map<std::string,

                                                        std::vector<std::string>> &filters) const override {

    return std::vector<uint64_t>();

  }

  horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,

                                               horizon::general::IDataBuffer &data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }

    uint16_t function = data.readUInt16();

    uint32_t length = data.readUInt32();

    if (length > data.getRemaningData()) {

      return horizon::protocol::MalformedResult(0);

    }

    auto &layer = ctx.createNewLayer();

    ctx.getFieldsManager().create(layer, HORIZON_FIELD("function"), function);

    switch (function) {

      case FUNCTION_FIRMWARE_RESPONSE: {

        uint8_t modelLength = data.readUInt8();

        std::string model = data.readString(modelLength);

        uint16_t firmwareVersion = data.readUInt16();

        uint8_t nameLength = data.readUInt8();

        std::string name = data.readString(nameLength);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("model"), model);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("version"), firmwareVersion);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("name"), name);

      }

      break;

      case FUNCTION_SUBFUNCTION_REQUEST: {

       uint8_t subFunction = data.readUInt8();

       ctx.getFieldsManager().create(layer, HORIZON_FIELD("sub_function"), subFunction);

      }

      break;

    }

    return horizon::protocol::SuccessResult();

  }

};

}

extern "C" {

 std::shared_ptr<horizon::protocol::BaseParser> create_parser() {

   return std::make_shared<CyberxHorizonSDK>();

 }

}
```

## <a name="extract-device-metadata"></a>Extrair metadados do dispositivo

Você pode extrair os seguintes metadados nos ativos:

  - `Is_distributed_control_system` -Indica se o protocolo faz parte do sistema de controle distribuído. (exemplo: o protocolo SCADA deve ser false)

  - `Has_protocol_address` – Indica se há um endereço de protocolo; o endereço específico para o protocolo atual, por exemplo, identificador de unidade MODBUS

  - `Is_scada_protocol` -Indica se o protocolo é específico para redes de OT

  - `Is_router_potential` -Indica se o protocolo é usado principalmente por roteadores. Por exemplo, LLDP, CDP ou STP.

Para conseguir isso, o arquivo de configuração JSON precisa ser atualizado usando a propriedade Metadata.

## <a name="json-sample-with-metadata"></a>Exemplo de JSON com metadados 

```json

{
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 "fields": [
   {
     "id": "function",
     "type": "numeric"
},
   {
     "id": "sub_function",
     "type": "numeric"
   },
   {
     "id": "name",
     "type": "string"
   },
   {
     "id": "model",
     "type": "string"
   },
   {
     "id": "version",
     "type": "numeric"
   }
 ],
}

```

## <a name="extract-programming-code"></a>Extrair código de programação 

Quando o evento de programação ocorre, você pode extrair o conteúdo do código. O conteúdo extraído permite que você:

- Compare o conteúdo do arquivo de código em eventos de programação diferentes.

- Disparar um alerta em programação não autorizada.  

- Disparar um evento para receber o arquivo de código de programação.

  :::image type="content" source="media/references-horizon-sdk/change.png" alt-text="O log de alterações de programação.":::

  :::image type="content" source="media/references-horizon-sdk/view.png" alt-text="Exiba a programação clicando no botão.":::

  :::image type="content" source="media/references-horizon-sdk/unauthorized.png" alt-text="O alerta de programação de PLC não autorizado.":::

Para conseguir isso, o arquivo de configuração JSON precisa ser atualizado usando a `code_extraction` propriedade. 

### <a name="json-configuration-fields"></a>Campos de configuração JSON 

Esta seção descreve os campos de configuração JSON. 

- **forma**

  Indica a maneira como os arquivos de eventos de programação são recebidos. 

  TODOS (cada ação de programação fará com que todos os arquivos de código sejam recebidos mesmo se houver arquivos sem alterações).

- **file_type**  

  Indica o tipo de conteúdo do código.  

  TEXT (cada arquivo de código contém informações textuais).

- **code_data_field**
  
  Indica o campo de implementação a ser usado para fornecer o conteúdo do código.

  Campo.

- **code_name_field**

  Indica o campo de implementação a ser usado para fornecer o nome do arquivo de codificação.

  Campo.

- **size_limit**

  Indica o limite de tamanho de cada conteúdo de arquivo de codificação em BYTES, se um arquivo de código exceder o limite definido, ele será Descartado. Se esse campo não for especificado, o valor padrão será 15 milhões, que é 15 MB.

  Automática.

- **los**

  Indica informações adicionais para um arquivo de código.

  Matriz que contém objetos com duas propriedades:

    - Name (cadeia de caracteres) – indica que a chave de metadados XSense atualmente dá suporte apenas à chave de nome de usuário.
 
    - valor (campo) – indica o campo de implementação a ser usado para fornecer os dados de metadados.

## <a name="json-sample-with-programming-code"></a>Exemplo de JSON com código de programação

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "script",
      "type": "string"
    },
    {
      "id": "script_name",
      "type": "string"
    }, 
      "id": "username",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon 
                                                Protocol Function",
      "alert_text": "There was an attempt by the source to 
                        invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, 
                  for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"code_extraction": {
    "method": "ALL",
    "file_type": "TEXT",
    "code_data_field": "script",
    "code_name_field": "script_name",
    "size_limit": 15000000,
    "metadata": [
      {
        "name": "username",
        "value": "username"
      }
    ]
  }
}

```
## <a name="custom-horizon-alerts"></a>Alertas de horizonte personalizado

Alguns protocolos código de função podem indicar um erro. Por exemplo, se o protocolo controlar um contêiner com um químico específico que deve ser armazenado sempre em uma temperatura específica. Nesse caso, pode haver um código de função indicando um erro no termômetro. Por exemplo, se o código de função for 25, você poderá disparar um alerta no console Web que indica que há um problema com o contêiner. Nesse caso, você pode definir alertas de pacotes aprofundados.

Adicione o parâmetro **alertas** ao `config.json` do plug-in.

```json
“alerts”: [{
    “id”: 1,
    “message”: “Problem with thermometer at station {IPv4.src}”,
    “title”: “Thermometer problem”,
    “expression”: “{CyberXHorizonProtocol.function} == 25”
}]

```

## <a name="json-configuration-fields"></a>Campos de configuração JSON

Esta seção descreve os campos de configuração JSON. 

| Nome do campo | Descrição | Valores possíveis |
|--|--|--|
| **ID** | Representa uma única ID de alerta. Ele deve ser exclusivo neste contexto. | Valor numérico 0-10000 |
| **message** | Informações exibidas para o usuário. Esse campo permite que você use campos diferentes. | Use qualquer campo do seu protocolo ou qualquer protocolo de camada inferior. |
| **title** | O título do alerta |  |
| **expressão** | Quando você quiser que esse alerta apareça. | Use qualquer campo numérico encontrado em camadas inferiores ou a camada atual.</br></br> Cada campo deve ser wrapper com `{}` , para que o SDK o detecte como um campo, os operadores lógicos com suporte são</br> = =-Igual</br> <=-menor ou igual a</br> >=-mais ou igual a</br> >-mais de</br> <-menor que</br> ~ =-Diferente de |

## <a name="more-about-expressions"></a>Mais sobre expressões

Sempre que o SDK do horizonte chamar a expressão e ele se tornar *verdadeiro*, um alerta será disparado no sensor.

Várias expressões podem ser incluídas no mesmo alerta. Por exemplo,

`{CyberXHorizonProtocol.function} == 25 and {IPv4.src} == 269488144`.

Essa expressão valida o código da função somente quando o pacote de origem IPv4 é 10.10.10.10. Que é uma representação bruta do endereço IP na representação numérica.

Você pode usar `and` ou `or` para conectar expressões.

## <a name="json-sample-custom-horizon-alerts"></a>Alertas de horizonte personalizado de exemplo JSON

```json
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 …………………………………….
 “alerts”: [{
“id”: 1,
“message”: “Problem with thermometer at station {IPv4.src}”,
“title”: “Thermometer problem”,
“expression”: “{CyberXHorizonProtocol.function} == 25”

```

## <a name="connect-to-an-indexing-service-baseline"></a>Conectar-se a um serviço de indexação (linha de base)

Você pode indexar os valores de protocolo e exibi-los em relatórios de mineração de dados.

:::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="Uma exibição da opção Data Mining.":::

Esses valores podem ser mapeados posteriormente para textos específicos, por exemplo, mapear números como textos ou adicionar informações, em qualquer idioma.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="as":::

Para obter mais informações, consulte [criar arquivos de mapeamento (JSON)](#create-mapping-files-json) para obter detalhes.

Você também pode usar valores de protocolos analisados anteriormente para extrair informações adicionais.

Por exemplo, para o valor, que é baseado em TCP, você pode usar os valores da camada IPv4. Nessa camada, você pode extrair valores como a origem do pacote e o destino.

Para conseguir isso, o arquivo de configuração JSON precisa ser atualizado usando a `whitelists` propriedade.

## <a name="allow-list-data-mining-fields"></a>Campos da lista de permissões (Data Mining)

Os seguintes campos da lista de permissões estão disponíveis:

- nome – o nome usado para indexação.

- alert_title – um título curto e exclusivo que explica o evento.

- alert_text – informações adicionais

Várias listas de permissões podem ser adicionadas, permitindo a total flexibilidade na indexação.

## <a name="json-sample-with-indexing"></a>Exemplo de JSON com indexação 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    }

```
## <a name="extract-firmware-data"></a>Extrair dados de firmware

Você pode extrair informações de firmware, definir valores de índice e disparar alertas de firmware para o protocolo de plug-in. Por exemplo,

- Extraia a versão ou o modelo de firmware. Essas informações podem ser utilizadas ainda mais para identificar CVEs.

- Disparar um alerta quando uma nova versão de firmware for detectada.

Para conseguir isso, o arquivo de configuração JSON precisa ser atualizado usando a propriedade firmware.

## <a name="firmware-fields"></a>Campos de firmware

Esta seção descreve os campos de configuração de firmware JSON.

- **name**
  
  Indica como o campo é apresentado no console do sensor.

- **value**

 Indica o campo de implementação a ser usado para fornecer os dados. 

- **firmware_index:**

  Selecione um:  
  - **modelo**: o modelo do dispositivo. Habilita a detecção de CVEs.
  - **serial**: o número de série do dispositivo. O número de série nem sempre está disponível para todos os protocolos. Esse valor é exclusivo por dispositivo.
  - **rack**: indica o identificador do rack, se o dispositivo fizer parte de um rack.
  - **slot**: o identificador de slot, se o dispositivo fizer parte de um rack.  
  - **module_address**: Use para apresentar uma hierarquia se o módulo puder ser apresentado por trás de outro dispositivo. Aplicável, em vez disso, se uma combinação de slot de rack, que é uma apresentação mais simples.  
  - **firmware_version**: indica a versão do dispositivo. Habilita a detecção de CVEs.
  - **alert_text**: indica o texto que descreve os desvios de firmware, por exemplo, as alterações de versão.  
  - **index_by**: indica os campos usados para identificar e indexar o dispositivo. No exemplo abaixo, o dispositivo é identificado por seu endereço IP. Em determinados protocolos, um índice mais complexo pode ser usado. Por exemplo, se outro dispositivo estiver conectado e você souber como extrair seu caminho interno. Por exemplo, a ID de unidade MODBUS pode ser usada como parte do índice, como uma combinação diferente de endereço IP e o identificador de unidade.
  - **firmware_fields**: indica quais campos são campos de metadados de dispositivo. Neste exemplo, são usados os seguintes: Model, Revision e Name. Cada protocolo pode definir seus próprios dados de firmware.

## <a name="json-sample-with-firmware"></a>Exemplo de JSON com firmware 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset.   
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
}

```
## <a name="extract-device-attributes"></a>Extrair atributos do dispositivo 

Você pode aprimorar o dispositivo das informações disponíveis no dispositivo em inventário, mineração de dados e outros relatórios.

- Nome

- Tipo

- Fornecedor

- Sistema operacional

Para conseguir isso, o arquivo de configuração JSON precisa ser atualizado usando a propriedade **Properties** . 

Você pode fazer isso depois de escrever o plug-in básico e extrair os campos obrigatórios.

## <a name="properties-fields"></a>Campos de propriedades

Esta seção descreve os campos de configuração de propriedades JSON. 

**config_file** 

Contém o nome do arquivo que define como processar cada chave nos `key` campos. O próprio arquivo de configuração deve estar no formato JSON e ser incluído como parte da pasta de protocolo plugin.

Cada chave no JSON define o conjunto de ações que devem ser feitas quando você extrai essa chave de um pacote.

Cada chave pode ter:

- **Dados de pacote** – indica as propriedades que seriam atualizadas com base nos dados extraídos do pacote (o campo de implementação usado para fornecer esses dados).

- **Dados estáticos** -indica um conjunto predefinido de `property-value` ações que devem ser atualizadas.

As propriedades que podem ser configuradas neste arquivo são: 

- **Nome** -indica o nome do dispositivo.

- **Tipo** -indica o tipo de dispositivo.

- **Fornecedor** -indica o fornecedor do dispositivo.

- **Descrição** -indica o modelo de firmware do dispositivo (prioridade mais baixa do que "modelo").

- **OperatingSystem** -indica o sistema operacional do dispositivo.

### <a name="fields"></a>Campos

| Campo | Descrição |
|--|--|
| key | Indica a chave. |
| value | Indica o campo de implementação a ser usado para fornecer os dados. |
| is_static_key | Indica se o `key` campo é derivado como um valor do pacote ou é um valor predefinido. |

### <a name="working-with-static-keys-only"></a>Trabalhando somente com chaves estáticas

Se você estiver trabalhando com chaves estáticas, não precisará configurar o `config.file` . Você só pode configurar o arquivo JSON.

## <a name="json-sample-with-properties"></a>Exemplo de JSON com propriedades 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
  
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "vendor",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
"properties": {
    "config_file": "config_file_example",
"fields": [
  {
    "key": "vendor",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },
{
    "key": "name",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },

]
  }
}

```

## <a name="config_file_exapmle-json"></a>CONFIG_FILE_EXAPMLE JSON 

```json
{
"someKey": {
  "staticData": {
    "model": "FlashSystem", 
    "vendor": "IBM", 
    "type": "Storage"}
  }
  "packetData": [
    "name”  
  ]
}

```

## <a name="create-mapping-files-json"></a>Criar arquivos de mapeamento (JSON)

Você pode personalizar o texto de saída do plug-in para atender às necessidades do seu ambiente corporativo definindo e atualizando arquivos de mapeamento. As alterações podem ser facilmente implementadas em texto sem alterar ou afetar o código. Cada arquivo pode mapear um ou vários campos.

- Mapeamento de valores de campo para nomes, por exemplo 1: redefinir, 2: Iniciar, 3: parar.

- Mapeando texto para dar suporte a vários idiomas.

Dois tipos de arquivos de mapeamento podem ser definidos.

  - [Arquivo de mapeamento simples](#simple-mapping-file).

  - [Arquivo de mapeamento de dependência](#dependency-mapping-file).

    :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="rede de ether":::

    :::image type="content" source="media/references-horizon-sdk/unhandled.png" alt-text="Uma exibição dos alertas sem tratamento.":::

    :::image type="content" source="media/references-horizon-sdk/policy-violation.png" alt-text="Uma lista de violações de política conhecidas.":::

## <a name="file-naming-and-storage-requirements"></a>Requisitos de nomenclatura e armazenamento de arquivos 

Os arquivos de mapeamento devem ser salvos na pasta de metadados.

O nome do arquivo deve corresponder à ID do arquivo de configuração JSON.

:::image type="content" source="media/references-horizon-sdk/json-config.png" alt-text="Um exemplo de um arquivo de configuração JSON.":::

## <a name="simple-mapping-file"></a>Arquivo de mapeamento simples 

O exemplo a seguir apresenta um arquivo JSON básico como um valor de chave.

Quando você cria uma lista de permissões e ela contém um ou mais dos campos mapeados. O valor será convertido de um número, Cadeia de caracteres ou qualquer tipo, em para texto formatado apresentado no arquivo de mapeamento.

```json
{
  “10”: “Read”,
  “20”: “Firmware Data”,
  “3”: “Write”
}

```

## <a name="dependency-mapping-file"></a>Arquivo de mapeamento de dependência 

Para indicar que o arquivo é um arquivo de dependência, adicione a palavra-chave `dependency` à configuração de mapeamento.

```json
dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value":

```

O arquivo contém um mapeamento entre o campo de dependência e o campo de função. Por exemplo, entre a função e a sub function. A função sub muda de acordo com a função fornecida.

Na lista de permissões configurada anteriormente, não há nenhuma configuração de dependência, conforme mostrado abaixo.

```json
"whitelists": [
{
"name": "Functions",
"alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
"alert_text": "There was an attempt by the source to invoke a new function on the destination",
"fields": [
{
"name": "Source",
"value": "IPv4.src"
},
{
"name": "Destination",
"value": "IPv4.dst"
},
{
"name": "Function",
"value": "CyberXHorizonProtocol.function"
}
]
}

```

A dependência pode ser baseada em um valor ou campo específico. No exemplo a seguir, ele se baseia em um campo. Se você basear em um valor, defina o valor de extração a ser lido no arquivo de mapeamento.

No exemplo a seguir, a dependência como segue para o mesmo valor do campo.

Por exemplo, na subfunção cinco, o significado é alterado com base na função.

  - Se for uma função de leitura, cinco significa memória de leitura.

  - Se for uma função de gravação, o mesmo valor será usado para ler de um arquivo.

    ```json
    {
      “10”: {
         “5”:  “Memory”,
         “6”: “File”,
         “7” “Register”
       },
      “3”: {
       “5”: “File”,
       “7”: “Memory”,
       “6”, “Register”
      }
    }

    ```

### <a name="sample-file"></a>Arquivo de exemplo

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {"is_distributed_control_system": false, "has_protocol_address": false, "is_scada_protocol": true, "is_router_potenial": false},
  "sanity_failure_codes": { "wrong magic": 0 },
  "malformed_codes": { "not enough bytes": 0  },
  "exports_dissect_as": {  },
  "dissect_as": { "UDP": { "port": ["12345"] }},
  "fields": [{ "id": "function", "type": "numeric" }, { "id": "sub_function", "type": "numeric" },
     {"id": "name", "type": "string" }, { "id": "model", "type": "string" }, { "id": "version", "type": "numeric" }],
  "whitelists": [{
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
      "fields": [{ "name": "Source", "value": "IPv4.src" }, { "name": "Destination", "value": "IPv4.dst" },
        { "name": "Function", "value": "CyberXHorizonProtocol.function" },
        { "name": "Sub function", "value": "CyberXHorizonProtocol.sub_function", "dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value": "CyberXHorizonProtocol.model", "firmware_index": "model" },
       { "name": "Revision", "value": "CyberXHorizonProtocol.version", "firmware_index": "firmware_version" },
       { "name": "Name", "value": "CyberXHorizonProtocol.name" }]
  },
  "value_mapping": {
      "CyberXHorizonProtocol.function": {
        "file": "function-mapping"
      },
      "CyberXHorizonProtocol.sub_function": {
        "dependency": true,
        "file": "sub_function-mapping"
      }
  }
}

```

## <a name="json-sample-with-mapping"></a>Exemplo de JSON com mapeamento

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        },
        {
          “name”: “Sub function”,
          “value”: “CyberXHorizonProtocol.sub_function”,
          “dependency”: {
              “field”: “CyberXHorizonProtocol.function”
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"value_mapping": {
    "CyberXHorizonProtocol.function": {
      "file": "function-mapping"
    },
    "CyberXHorizonProtocol.sub_function": {
      "dependency": true,
      "file": "sub_function-mapping"
    }
}

```
## <a name="package-upload-and-monitor-the-plugin"></a>Empacotar, carregar e monitorar o plug-in 

Esta seção descreve como

  - Empacote seu plug-in.

  - Carregue seu plug-in.

  - Monitore e depure o plug-in para avaliar como ele está sendo executado.

Para empacotar o plug-in:

1. Adicione o **artefato** (pode ser, biblioteca, config.jsem ou metadados) a um `tar.gz` arquivo.

1. Altere a extensão de arquivo para \<XXX.hdp> , em que é \<XXX> o nome do plug-in.

Para entrar no console do horizonte:

1.  Entre na CLI do sensor como administrador, CyberX ou usuário de suporte.

2.  No arquivo: `/var/cyberx/properties/horizon.properties` altere a propriedade **interface do usuário. Enabled** para **true** ( `horizon.properties:ui.enabled=true` ).

3.  Entre no console do sensor.

4.  Selecione a opção **horizonte** no menu principal.

    :::image type="content" source="media/references-horizon-sdk/horizon.png" alt-text="Selecione a opção Horizonte no painel do lado esquerdo.":::

    O console do horizonte é aberto.

    :::image type="content" source="media/references-horizon-sdk/plugins.png" alt-text="Uma exibição do console de horizonte e todos os seus plug-ins.":::

## <a name="plugins-pane"></a>Painel plug-ins

O painel plug-ins lista:

  - Plug-ins de infraestrutura: plug-ins de infraestrutura instalados por padrão com o defender para IoT.

  - Plug-ins de aplicativo: plug-ins de aplicativo instalados por padrão com o defender para IoT e outros plug-ins desenvolvidos pelo defender para IoT ou desenvolvedores externos.
    
Habilite e desabilite plug-ins que foram carregados usando a alternância.

:::image type="content" source="media/references-horizon-sdk/toggle.png" alt-text="A alternância de CIP.":::

### <a name="uploading-a-plugin"></a>Carregando um plug-in

Depois de criar e empacotar seu plug-in, você pode carregá-lo no sensor do defender para IoT. Para obter a cobertura completa de sua rede, você deve carregar o plug-in para cada sensor em sua organização.

Para carregar:

1.  Entre no sensor.


2. Selecione **Carregar**.

    :::image type="content" source="media/references-horizon-sdk/upload.png" alt-text="Carregue seus plugins.":::

3. Navegue até o plug-in e arraste-o para a caixa de diálogo plug-in. Verifique se o prefixo é `.hdp` . O plug-in é carregado.

## <a name="plugin-status-overview"></a>Visão geral do status do plugin 

A janela **visão geral** do console de horizonte fornece informações sobre o plug-in carregado e permite que você o desabilite e habilite-os.

:::image type="content" source="media/references-horizon-sdk/overview.png" alt-text="A visão geral do console do horizonte.":::

| Campo | Descrição |
|--|--|
| Aplicativo | O nome do plug-in que você carregou. |
| :::image type="content" source="media/references-horizon-sdk/switch.png" alt-text="A opção liga e desliga."::: | Ativar **ou** **desativar** o plug-in. O defender para IoT não tratará o tráfego de protocolo definido no plug-in quando você desativar o plug-in. |
| Hora | A hora em que os dados foram analisados pela última vez. Atualizado a cada 5 segundos. |
| PPS | O número de pacotes por segundo. |
| Largura de banda | A largura de banda média detectada nos últimos 5 segundos. |
| Malforms | Validações malformadas são usadas depois que o protocolo tiver sido validado positivamente. Se houver uma falha ao processar os pacotes com base no protocolo, uma resposta de falha será retornada.   <br><br>Esta coluna indica o número de erros malform nos últimos 5 segundos. Para obter mais informações, consulte [validações de código malformado](#malformed-code-validations) para obter detalhes. |
| Warnings | Os pacotes correspondem à estrutura e à especificação, mas há um comportamento inesperado com base na configuração de aviso do plug-in. |
| Errors | O número de pacotes que falharam validações de protocolo básicas. Valida se o pacote corresponde às definições de protocolo. O número exibido aqui indica o número de erros detectados nos últimos 5 segundos. Para obter mais informações, consulte [validações de código de sanidade](#sanity-code-validations) para obter detalhes. |
| :::image type="content" source="media/references-horizon-sdk/monitor.png" alt-text="O ícone de monitor."::: | Examine os detalhes sobre malform e avisos detectados para o plug-in. |

## <a name="plugin-details"></a>Detalhes do plug-in

Você pode monitorar o comportamento do plug-in em tempo real analisando o número de *Malform* e *avisos* detectados para o plug-in. Uma opção está disponível para congelar a tela e exportar para investigação adicional

:::image type="content" source="media/references-horizon-sdk/snmp.png" alt-text="Tela do monitor SNMP.":::

Para monitorar:

Selecione o botão monitorar para o plug-in na visão geral.

Próximas etapas

Configurar sua [API do horizonte](references-horizon-api.md)
