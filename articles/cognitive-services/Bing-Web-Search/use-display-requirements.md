---
title: Usar e exibir requisitos para as APIs de Pesquisa do Bing
titleSuffix: Azure Cognitive Services
description: Os requisitos para exibir resultados da pesquisa das APIs de Pesquisa do Bing em seus aplicativos.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 4300860039b76bd076f19acfc252f59ef10d462b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96351837"
---
# <a name="bing-search-api-use-and-display-requirements"></a>Requisitos de uso e exibição da API de Pesquisa do Bing

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Esses requisitos de uso e exibição se aplicam a qualquer implementação do conteúdo e informações associadas das seguintes APIs de Pesquisa do Bing, incluindo relações, metadados e outros sinais.

- Pesquisa Personalizada do Bing
- Pesquisa de Entidade do Bing
- Pesquisa de Imagem do Bing
- Pesquisa de Notícias do Bing
- Pesquisa de Vídeo do Bing
- Pesquisa Visual do Bing
- Pesquisa na Web do Bing
- Verificação Ortográfica do Bing
- Sugestão Automática do Bing

## <a name="definitions"></a>Definições


|Termo  |Descrição  |
|---------|---------|
|Resposta     | Uma categoria de resultados retornados em uma resposta. Por exemplo, uma resposta da API de Pesquisa de Web do Bing pode incluir respostas nas categorias de resultados de página da Web, imagens, vídeo, visual e notícias. |
|Resposta     | Quaisquer respostas e dados associados recebidos em resposta a uma única chamada a uma API de Pesquisa. |
|Resultado    | Um item de informação em uma resposta. Por exemplo, o conjunto de dados conectados com um artigo de notícias único é um resultado em uma resposta de notícias. |
|APIs de pesquisa    | coletivamente, a Pesquisa Personalizada do Bing, a Pesquisa de Entidade, a Pesquisa de Imagem, a Pesquisa de Notícias, a Pesquisa de Vídeo, a Pesquisa Visual, a Pesquisa de Empresas Locais e as APIs de Pesquisa na Web. |

## <a name="bing-spell-check-and-bing-autosuggest-api-restrictions"></a>Restrições da API de Verificação Ortográfica do Bing e API de Sugestão Automática do Bing

Não:

- Copiar, armazenar ou armazenar em cache os dados recebidos da Verificação Ortográfica do Bing ou das APIs de Sugestão Automática do Bing.
- Use os dados recebidos das APIs de Verificação Ortográfica do Bing e Sugestão Automática do Bing como parte de qualquer aprendizado de máquina ou atividade algorítima semelhante. Não use esses dados para treinar, avaliar ou melhorar os serviços de novos ou existentes que você ou terceiros podem oferecer.

## <a name="bing-search-apis"></a>APIs de Pesquisa do Bing

> [!NOTE]
> Os requisitos desta seção se aplicam a apenas às APIs de Pesquisa, o que não inclui a Verificação Ortográfica do Bing nem a Sugestão Automática do Bing. 

### <a name="internet-search-experience-requirements"></a>Requisitos da experiência de pesquisa da Internet

Todos os dados retornados em respostas só podem ser usados em experiências de pesquisa da Internet. Uma experiência de pesquisa da Internet significa que o conteúdo é exibido: 

- É relevante e responsivo à consulta direta do usuário final ou a outra intenção e indicação de interesse de pesquisa do usuário (por exemplo, consulta de pesquisa indicada pelo usuário). 

- Ajuda os usuários a localizar fontes de dados da resposta e navegar até elas. Por exemplo, fornecendo links clicáveis de hiperlinks na resposta.

- Inclui vários resultados dentre os quais o usuário pode selecionar. 

- Estão em um posicionamento que permite aos usuários pesquisar.

- Inclui uma indicação visível de que o conteúdo é um resultado de pesquisa da Internet. Por exemplo, uma instrução de que o conteúdo é "da Web".

- Inclui qualquer outra medida apropriada para garantir que os dados da API de Pesquisa do Bing não violem leis ou direitos de terceiros aplicáveis. Consulte seus consultores jurídicos para saber quais medidas podem ser apropriadas.

A única exceção para esses requisitos de experiência de pesquisa da Internet é para a descoberta de URL, conforme descrito posteriormente neste artigo. 

### <a name="restrictions"></a>Restrições

Não:

- Copiar, armazenar ou armazenar em cache os dados de respostas (exceto pela retenção, até a extensão permitida pela seção [continuidade do serviço](#continuity-of-service). 

- Use os dados recebidos das APIs de pesquisa como parte de qualquer atividade algorítmica semelhante ou de aprendizado de máquina. Não use esses dados para treinar, avaliar ou melhorar os serviços de novos ou existentes que você ou terceiros podem oferecer.

- Modificar o conteúdo de resultados (exceto reformatá-los de uma forma que não viole qualquer outro requisito), a menos que exigido por lei ou acordado com a Microsoft. 

- Omitir informações de atribuição e as URLs associadas ao conteúdo de resultado.

- Reordenar, incluindo por omissão, resultados exibidos em uma resposta quando uma ordem ou classificação é fornecida, a menos que exigido por lei ou acordado pela Microsoft. 

    > [!NOTE]
    > Esse requisito não se aplica à reordenação implementada por meio do portal para a API de Pesquisa Personalizada do Bing.

- Exibir outros tipos de conteúdo em qualquer parte de uma resposta de uma maneira que possa levar a um usuário a acreditar que o outro conteúdo faz parte da resposta. 

- Exibir anúncios que não são fornecidos pela Microsoft em qualquer página que exiba qualquer parte de uma resposta. 

- Exibir anúncios em páginas com respostas:
    - Das APIs de Pesquisa de Imagem do Bing, de Pesquisa de Notícias, de Pesquisa de Vídeo ou de Pesquisa Visual
    - Que são filtradas ou limitadas principalmente (ou unicamente) a resultados da pesquisa de imagens, notícias e/ou vídeos ou objetos visuais.

### <a name="notices-and-branding"></a>Notificações e identidade visual 
Você deve

- Proeminentemente incluem um hiperlink funcional para a [Declaração de Privacidade da Microsoft](https://go.microsoft.com/fwlink/?LinkId=521839), perto de cada ponto na experiência do usuário (UX) que oferece a capacidade de uma consulta de pesquisa de entrada de um usuário. Hiperlink de rótulo **Declaração de Privacidade da Microsoft**.

- Exibir em proeminentemente a identidade visual do Bing, consistente com as [Diretrizes de Uso da Marca Registrada do Bing](https://go.microsoft.com/fwlink/?linkid=833278), perto de cada ponto de UX que oferece a capacidade de uma consulta de pesquisa de entrada a um usuário. Essa marca deve claramente indicar ao usuário que a Microsoft está incluindo a experiência de pesquisa da Internet.

- Você pode atribuir cada resposta (ou parte de uma resposta) exibida na pesquisa do Bing na Web, pesquisa de imagem, pesquisa de notícias, pesquisa de vídeo e APIs de pesquisa Visual para a Microsoft, a menos que a Microsoft especifique o contrário na gravação para seu uso. Isso é descrito em [Diretrizes de uso de Marca Registrada do Bing](https://go.microsoft.com/fwlink/?linkid=833278). 

Não:

- Atribua respostas (ou partes de respostas) exibidas na API de Pesquisa Personalizada do Bing para a Microsoft, a menos que a Microsoft especifique o contrário por escrito para seu uso específico.

### <a name="transferring-responses"></a>Transferindo respostas

Se você habilitar um usuário a transferir uma resposta de uma API de Pesquisa para outro usuário, como por meio de um aplicativo de mensagens ou postagem em mídia social, o seguinte será aplicável: 

- As respostas transferidas devem:
  - Consistem em conteúdo que não foi modificado em relação ao conteúdo das respostas exibidas ao usuário de transferência. Alterações de formatação são permitidas.
  - Não incluir dados no formulário de metadados.
  - Para respostas de Web do Bing, imagem, notícias, vídeo e APIs Visual, idioma de exibição, indicando que a resposta foi obtida por meio de uma experiência de pesquisa da internet ativada pelo Bing. Por exemplo, você pode exibir a linguagem, como "Powered by Bing" ou "Saiba mais informações sobre essa imagem no Bing”, ou você pode usar o logotipo do Bing.
  - Para respostas da API de Pesquisa Personalizada do Bing, exiba o idioma indicando que a resposta foi obtida através de uma experiência de pesquisa na internet. Por exemplo, você pode exibir o idioma como “Saber mais sobre esse resultado de pesquisa”.
  - Exiba em destaque a consulta completa usada para gerar a resposta.
  - Inclua um link de destaque ou atribuição semelhante para a fonte subjacente da resposta, seja diretamente ou por meio do mecanismos de pesquisa (bing.com, m.bing.com, ou seu serviço de pesquisa personalizado, conforme aplicável).
- Você não pode automatizar a transferência de respostas. Uma transferência deve ser iniciada por uma ação do usuário, evidenciando claramente uma tentativa de transferir uma resposta.
- Só é possível habilitar um usuário a transferir as respostas que forem exibidas em resposta à consulta do usuário de transferência.

### <a name="continuity-of-service"></a>Continuidade do serviço 

Não copie, armazene ou armazene em cache os dados de respostas da API de Pesquisa. No entanto, para permitir a continuidade de acesso ao serviço e a renderização de dados, você pode manter os resultados somente sob as seguintes condições:

#### <a name="device"></a>Dispositivo

Você pode permitir que um usuário mantenha os resultados em um dispositivo para o que for menor: (i) 24 horas a partir da hora da consulta ou (ii) até que um usuário envie outra consulta para resultados atualizados, contanto que resultados retidos possam ser usados somente:

- Para permitir que o usuário acesse os resultados retornados anteriormente para o usuário no dispositivo (por exemplo, no caso de interrupção de serviço).
- Para armazenar os resultados retornados para sua consulta proativa personalizada prevendo as necessidades do usuário com base em sinais do usuário (por exemplo, no caso de interrupção de serviço prevista).

#### <a name="server"></a>Servidor

Você pode manter os resultados específicos para um único usuário com segurança em um servidor que controla e exibir os resultados mantidos somente:

- Para habilitar o usuário para acessar um relatório histórico de resultados previamente retornado ao usuário em sua solução. Os resultados podem não ser (i) mantidos por mais de 21 dias a partir do momento da consulta inicial do usuário final e (ii) exibidos em resposta à consulta de um usuário novo ou repetido.
- Para armazenar os resultados retornados para sua consulta proativa personalizada para prever as necessidades do usuário, com base em sinais do usuário final. Você pode armazenar esses resultados para o menor do que (i) 24 horas da hora da consulta, ou (ii) até que um usuário envie outra consulta para obter os resultados atualizados.

Sempre que forem retidos, osresultados para um usuário específico não podem ser misturados com os resultados de outro usuário. Ou seja, os resultados de cada usuário devem ser retidos e entregues separadamente.

### <a name="general"></a>Geral 

Para obter toda a apresentação dos resultados retidos, faça o seguinte:

- Inclua um aviso criptografado visível do horário em que a consulta foi enviada.
- Apresente ao usuário um botão ou meios semelhantes para consultar novamente e obter resultados atualizados. 
- Mantenha a identidade visual do Bing na apresentação dos resultados.
- Exclua (e atualize com uma nova consulta, se necessário) os resultados armazenados em períodos de tempo especificados.

### <a name="non-display-url-discovery"></a>Descoberta de URL não de exibição 

Você só pode usar as respostas da pesquisa em uma experiência de pesquisa não na Internet para o único propósito de descoberta de URLs de fontes de informação responsivas a uma consulta do usuário ou cliente. Você pode copiar essas URLs em um relatório ou uma resposta semelhante que você fornecer:

- Somente para esse usuário ou cliente, em resposta à consulta.
- Somente se ele inclui significativo conteúdo adicional valioso, relevante para a consulta.

As seções anteriores de APIs de Pesquisa usam e exibem requisitos que não se aplicam a esse uso de não exibição, exceto para o seguinte: 

- Você não deve armazenar em cache, copiar ou armazenar dados ou conteúdo de ou derivado da resposta de pesquisa, exceto a cópia de URL limitada descrita anteriormente.
- Você deve garantir que o uso de dados (inclusive as URLs) recebidos das APIs de Pesquisa não viole leis ou direitos de terceiros.
- Não use os dados (inclusive as URLs) recebidos das APIs de Pesquisa como parte de qualquer índice de pesquisa ou aprendizado de máquina ou atividade algorítmica semelhante. Não use esses dados para treinar, avaliar ou melhorar os serviços que você ou terceiros podem oferecer.

## <a name="gdpr-compliance"></a>Conformidade de GDPR  

Com relação a alguns dados pessoais sujeitos à Regulação de Proteção de Dados Gerais da União Europeia (GDPR) e que é processada em conexão com as chamadas para as APIs de Pesquisa, API de Verificação Ortográfica do Bing ou API de Sugestão Automática do Bing, você entende que você e a Microsoft são controladores de dados independentes da GDPR. Você é responsável independentemente de sua conformidade com a GDPR.