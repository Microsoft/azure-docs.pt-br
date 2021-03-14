---
title: Executando a exportação invocando $export comando na API do Azure para FHIR
description: Este artigo descreve como exportar dados do FHIR usando o $export
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 2/19/2021
ms.author: cavoeg
ms.openlocfilehash: 9ed78baed35312b9a33c71a3e49b7e9dca22eb9f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017647"
---
# <a name="how-to-export-fhir-data"></a>Como exportar dados do FHIR


O recurso de exportação em massa permite que os dados sejam exportados do servidor FHIR de acordo com a [especificação FHIR](https://hl7.org/fhir/uv/bulkdata/export/index.html). 

Antes de usar $export, você desejará certificar-se de que a API do Azure para FHIR está configurada para usá-la. Para definir configurações de exportação e criar conta de armazenamento do Azure, consulte [a página configurar dados de exportação](configure-export-data.md).

## <a name="using-export-command"></a>Usando o comando $export

Depois de configurar a API do Azure para FHIR para exportação, você pode usar o comando $export para exportar os dados para fora do serviço. Os dados serão armazenados na conta de armazenamento que você especificou ao configurar a exportação. Para saber como invocar o comando $export no FHIR Server, leia a documentação sobre a [especificação HL7 FHIR $Export](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

A API do Azure para FHIR dá suporte a $export nos seguintes níveis:
* [Sistema](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export): `GET https://<<FHIR service base URL>>/$export>>`
* [Paciente](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients): `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [Grupo de pacientes *](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) – a API do Azure para FHIR exporta todos os recursos relacionados, mas não exporta as características do Grupo: `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`

Quando os dados são exportados, um arquivo separado é criado para cada tipo de recurso. Para garantir que os arquivos exportados não se tornem muito grandes, criamos um novo arquivo depois que o tamanho de um único arquivo exportado se torna maior que 64 MB. O resultado é que você pode obter vários arquivos para cada tipo de recurso, que será enumerado (ou seja, paciente-1. ndjson, paciente-2. ndjson). 


> [!Note] 
> `Patient/$export` e `Group/[ID]/$export` poderá exportar recursos duplicados se o recurso estiver em um compartimento de mais de um recurso ou em vários grupos.

Além disso, a verificação do status de exportação por meio da URL retornada pelo cabeçalho de local durante o enfileiramento tem suporte junto com o cancelamento do trabalho de exportação real.

### <a name="exporting-fhir-data-to-adls-gen2"></a>Exportando dados do FHIR para ADLS Gen2

Atualmente, damos suporte a $export para ADLS Gen2 contas de armazenamento habilitadas, com a seguinte limitação:

- O usuário ainda não pode aproveitar os [namespaces hierárquicos](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) ; Não há uma maneira de direcionar a exportação para um subdiretório específico dentro do contêiner. Só fornecemos a capacidade de direcionar um contêiner específico (onde criamos uma nova pasta para cada exportação).

- Depois que uma exportação for concluída, nunca exportaremos nada para essa pasta novamente, pois as exportações subsequentes para o mesmo contêiner estarão dentro de uma pasta recém-criada.


## <a name="settings-and-parameters"></a>Configurações e parâmetros

### <a name="headers"></a>Cabeçalhos
Há dois parâmetros de cabeçalho necessários que devem ser definidos para trabalhos de $export. Os valores são definidos pela especificação de [$Export](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers)atual.
* **Accept** -Application/fhir + JSON
* **Preferir** -responder-Async

### <a name="query-parameters"></a>Parâmetros de consulta
A API do Azure para FHIR dá suporte aos seguintes parâmetros de consulta. Todos esses parâmetros são opcionais:

|Parâmetro de consulta        | Definido pela especificação de FHIR?    |  Descrição|
|------------------------|---|------------|
| \_outputFormat | Sim | Atualmente dá suporte a três valores para alinhar à especificação FHIR: Application/FHIR + ndjson, Application/ndjson ou apenas ndjson. Todos os trabalhos de exportação serão retornados `ndjson` e o valor passado não terá efeito sobre o comportamento do código. |
| \_since | Sim | Permite exportar apenas os recursos que foram modificados desde o momento fornecido |
| \_Escreva | Sim | Permite especificar quais tipos de recursos serão incluídos. Por exemplo, \_ digite = paciente retornaria apenas os recursos do paciente|
| \_typefilter | Sim | Para solicitar uma filtragem refinada, você pode usar \_ TypeFilter juntamente com o \_ parâmetro de tipo. O valor do parâmetro _typeFilter é uma lista separada por vírgulas de consultas FHIR que restringem ainda mais os resultados |
| \_Container | Não |  Especifica o contêiner dentro da conta de armazenamento configurada onde os dados devem ser exportados. Se um contêiner for especificado, os dados serão exportados para esse contêiner em uma nova pasta com o nome. Se o contêiner não for especificado, ele será exportado para um novo contêiner usando o carimbo de data/hora e a ID do trabalho. |

## <a name="secure-export-to-azure-storage"></a>Exportação segura para o armazenamento do Azure

A API do Azure para FHIR dá suporte a uma operação de exportação segura. Uma opção para executar uma exportação segura é permitir que endereços IP específicos associados à API do Azure para FHIR acessem a conta de armazenamento do Azure. Dependendo se a conta de armazenamento está no mesmo local ou em uma localização diferente da API do Azure para FHIR, as configurações são diferentes.

### <a name="when-the-azure-storage-account-is-in-a-different-region"></a>Quando a conta de armazenamento do Azure está em uma região diferente

Selecione a folha rede da conta de armazenamento do Azure no Portal. 

   :::image type="content" source="media/export-data/storage-networking.png" alt-text="Configurações de rede do armazenamento do Azure." lightbox="media/export-data/storage-networking.png":::
   
Selecione "redes selecionadas" e especifique o endereço IP na caixa **intervalo de endereços** na seção do firewall \| Adicionar intervalos de IP para permitir o acesso da Internet ou de suas redes locais. Você pode encontrar o endereço IP da tabela abaixo para a região do Azure em que a API do Azure para o serviço FHIR é provisionada.

|**Região do Azure**         |**Endereço IP público** |
|:----------------------|:-------------------|
| Leste da Austrália       | 20.53.44.80       |
| Canadá Central       | 20.48.192.84      |
| Centro dos EUA           | 52.182.208.31     |
| Leste dos EUA              | 20.62.128.148     |
| Leste dos EUA 2            | 20.49.102.228     |
| Leste dos EUA 2 EUAP       | 20.39.26.254      |
| Norte da Alemanha        | 51.116.51.33      |
| Centro-Oeste da Alemanha | 51.116.146.216    |
| Japan East           | 20.191.160.26     |
| Coreia Central        | 20.41.69.51       |
| Centro-Norte dos EUA     | 20.49.114.188     |
| Norte da Europa         | 52.146.131.52     |
| Norte da África do Sul   | 102.133.220.197   |
| Centro-Sul dos Estados Unidos     | 13.73.254.220     |
| Sudeste Asiático       | 23.98.108.42      |
| Norte da Suíça    | 51.107.60.95      |
| Sul do Reino Unido             | 51.104.30.170     |
| Oeste do Reino Unido              | 51.137.164.94     |
| Centro-Oeste dos EUA      | 52.150.156.44     |
| Europa Ocidental          | 20.61.98.66       |
| Oeste dos EUA 2            | 40.64.135.77      |

### <a name="when-the-azure-storage-account-is-in-the-same-region"></a>Quando a conta de armazenamento do Azure está na mesma região

O processo de configuração é igual ao anterior, exceto que um intervalo de endereços IP específico no formato CIDR é usado em vez disso, 100.64.0.0/10. O motivo pelo qual o intervalo de endereços IP, que inclui 100.64.0.0 – 100.127.255.255, deve ser especificado é porque o endereço IP real usado pelo serviço varia, mas estará dentro do intervalo, para cada solicitação de $export.

> [!Note] 
> É possível que um endereço IP privado dentro do intervalo de 10.0.2.0/24 possa ser usado em vez disso. Nesse caso, a operação de $export não terá sucesso. Você pode repetir a $export solicitação, mas não há nenhuma garantia de que um endereço IP dentro do intervalo de 100.64.0.0/10 será usado na próxima vez. Esse é o comportamento de rede conhecido por design. A alternativa é configurar a conta de armazenamento em uma região diferente.
    
## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a exportar recursos do FHIR usando o comando $export. Em seguida, saiba como exportar dados de identificação:
 
>[!div class="nextstepaction"]
>[Exportar dados de-identificados](de-identified-export.md)
