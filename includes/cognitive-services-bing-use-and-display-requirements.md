2017-07-07

Esses requisitos de uso e exibição se aplicam a sua implementação do conteúdo e a informações associadas (por exemplo, relações, metadados e outros sinais) disponíveis por meio de chamadas para Pesquisa do Bing na Web, Pesquisa de Imagem, Pesquisa de Vídeo, Pesquisa de Notícias e APIs de Pesquisa de Identidade, APIs de Verificação Ortográfica do Bing e de Sugestão Automática do Bing. Detalhes de implementação relacionados a esses requisitos podem ser encontrados na documentação para recursos específicos e resultados.

## <a name="1-bing-spell-check-api-and-bing-autosuggest-api"></a>1. API DE VERIFICAÇÃO ORTOGRÁFICA DO BING e API DE SUGESTÃO AUTOMÁTICA DO BING.

Você não deve:

•   copiar, armazenar ou armazenar em cache os dados recebidos da Verificação Ortográfica do Bing ou das APIs de Sugestão Automática do Bing; ou 
  
•   usar os dados recebidos das APIs de Verificação Ortográfica do Bing ou de Sugestão Automática do Bing como parte de aprendizado de máquina ou atividade algorítmica semelhante para treinar, avaliar ou melhorar os serviços novos ou existentes que você ou terceiros podem oferecer.

## <a name="2-search-apis"></a>2. APIs DE PESQUISA

Os requisitos nas Seções de 3 a 7 se aplicam à Pesquisa da Web do Bing, Pesquisa de Imagem, Pesquisa de Vídeo, APIs de Pesquisa de Notícias e API de Pesquisa de Entidade do Bing (as "**APIs de Pesquisa**"). As seguintes definições se aplicam às Seções de 3 a 7:

•   "resposta" refere-se a uma categoria de resultados retornados em uma resposta. Por exemplo, uma resposta da API de Pesquisa da Web do Bing pode incluir respostas nas categorias de resultados de página da Web, imagens, vídeo e notícias;  
  
•   "resposta" significa quaisquer respostas e dados associados recebidos em resposta a uma única chamada a uma API de Pesquisa;  
  
•   "resultado" se refere a um item de informação em uma resposta. Por exemplo, o conjunto de dados conectados com um artigo de notícias único é um resultado em uma resposta de notícias.

## <a name="3-bing-entity-search-api"></a>3. API DE PESQUISA DE ENTIDADE DO BING

Os dados retornados da API de Pesquisa de Entidade do Bing devem ser:

•   oferecidos somente aos usuários localizados nos Estados Unidos;  

•   exibidos apenas para responder ao interesse de pesquisa do usuário final na entidade (por exemplo, consulta indicada pelo usuário);  
  
•   acompanhados por um link visível para a URL do bing.com fornecida na resposta que permite que o usuário navegue para os resultados da pesquisa da consulta relevante no bing.com;  
  
•   usados de acordo com quaisquer outras combinações de medidas apropriadas para garantir que o uso de dados recebidos das APIs de Pesquisa de Entidade não violem leis ou direitos de terceiros aplicáveis. Por exemplo,  
  
  - garantindo que as imagens de miniaturas têm tamanho de miniatura em proporção à tela do usuário; ou  
    
  - se depender de uma licença Creative Commons, garantindo a licença e a atribuição de texto adequadas.

Você não deve:

•   copiar, armazenar ou armazenar em cache os dados recebidos da API de Pesquisa de Entidade do Bing; ou  
  
•   usar dados recebidos da API de Pesquisa de Entidade do Bing como parte de aprendizado de máquina ou atividade algorítmica semelhante para treinar, avaliar ou melhorar os serviços novos ou existentes que você ou terceiros podem oferecer.

## <a name="4-bing-web-search-image-search-news-search-and-video-search-apis"></a>4. PESQUISA DA WEB DO BING, PESQUISA DE IMAGEM, PESQUISA DE NOTÍCIAS e APIS DE PESQUISA DE VÍDEO:

**Experiência de pesquisa da Internet.** Todos os dados retornados em respostas da Web, APIs de Pesquisa de Imagem, Notícias e Vídeo só podem ser usados em experiências de pesquisa da Internet. Uma experiência de pesquisa da Internet significa que o conteúdo é exibido, conforme aplicável:

•   é relevante e responsivo à consulta direta do usuário final ou outra indicação de interesse de pesquisa do usuário e à intenção (por exemplo, consulta de pesquisa indicada pelo usuário);  
  
•   ajuda os usuários a localizar e navegar até as fontes de dados (por exemplo, as URLs fornecidas são implementadas como hiperlinks para que o conteúdo ou a atribuição seja um link clicável exibido visivelmente com os dados);  
  
•   inclui vários resultados para o usuário final selecionar (por exemplo, vários resultados da resposta de notícias são exibidos ou todos os resultados, se poucos forem retornados);  
  
•   é limitado a um valor apropriado à finalidade de pesquisa (por exemplo, imagem de miniaturas têm tamanho de miniatura em proporção à tela do usuário);  
  
•   inclui uma indicação visível para o usuário final que o conteúdo consiste em resultados da pesquisa da Internet (por exemplo, uma instrução de que o conteúdo é "da Web"); e  
  
•   inclui qualquer outra combinação de medidas apropriadas para garantir que o uso de dados recebidos das APIs de pesquisa não viole leis ou direitos de terceiros aplicáveis. Consulte seus consultores jurídicos para saber quais medidas podem ser apropriadas.

A única exceção para o requisito de experiência de pesquisa da Internet é para a descoberta de URL, conforme descrito na seção 7 (detecção de URL de não exibição) abaixo.

**Geral.** Você não deve: 

•   copiar, armazenar ou armazenar em cache os dados de respostas (exceto pela retenção, até a extensão permitida pela seção "Continuidade do Serviço" abaixo);  
  
•   modificar o conteúdo de resultados (exceto reformatá-los de uma forma que não viole qualquer outro requisito);  
  
•   omitir a atribuição e as URLs associadas ao conteúdo de resultado;  
  
•   reordenar (incluindo por omissão) resultados exibidos em uma resposta quando uma ordem ou classificação é fornecida;  
  
•   exibir outros tipos de conteúdo em qualquer parte de uma resposta de uma maneira que possa levar a um usuário final a acreditar que o outro conteúdo faz parte da resposta;  
  
•   exibir anúncios que não são fornecidos pela Microsoft em qualquer página que exiba qualquer parte de uma resposta;  
  
•   usar dados recebidos das APIs de Pesquisa como parte de aprendizado de máquina ou atividade algorítmica semelhante para treinar, avaliar ou melhorar os serviços novos ou existentes que você ou terceiros podem oferecer.

**Publicidade.** A publicidade (caso fornecida pela Microsoft ou outro provedor) não deve ser exibida com respostas (i) das APIs de imagem, notícias ou pesquisa de vídeo; ou (ii) que são principalmente filtradas ou limitadas (ou unicamente) para resultados de resultados de imagens, notícias e/ou vídeo de outras APIs de pesquisa.

**Identidade visual.** Você atribuirá cada resposta (ou parte de uma resposta) exibida para a Microsoft conforme descrito em https://go.microsoft.com/fwlink/?linkid=833278, a menos que a Microsoft especifique o contrário para seu uso particular.

## <a name="5-transferring-responses"></a>5. Transferindo respostas.

Se você habilitar um usuário a transferir uma resposta de uma API de Pesquisa para outro usuário, como por meio de um aplicativo de mensagens ou postagem em mídia social, o seguinte será aplicável:

•   As respostas transferidas devem:  
  
  - Consistem em conteúdo que não foi modificado em relação ao conteúdo das respostas exibidas ao usuário de transferência (alterações de formatação são permitidas);  
    
  - Não incluir dados no formulário de metadados;  
    
  - Idioma de exibição, indicando que a resposta foi obtido por meio de uma experiência de pesquisa da Internet da plataforma Bing (por exemplo, "Da plataforma Bing", "Saiba mais sobre essa imagem no Bing" ou "Explore essa imagem no Bing" ou com o uso do logotipo do Bing);  
    
  - Exiba em destaque a consulta completa usada para gerar a resposta; e  
    
  - Inclua um link de destaque ou atribuição semelhante para a fonte subjacente da resposta, seja diretamente ou por meio de bing.com ou m.bing.com.  
    
•   Você não pode automatizar a transferência de respostas. Uma transferência deve ser iniciada por uma ação do usuário, evidenciando claramente uma tentativa de transferir uma resposta.  
  
•   Só é possível habilitar um usuário a transferir as respostas obtidas como resultado da consulta do usuário de transferência.

## <a name="6-continuity-of-service"></a>6. Continuidade do serviço.

Você não deve copiar, armazenar ou armazenar em cache os dados de respostas da API de Pesquisa. No entanto, para permitir a continuidade de acesso ao serviço e a renderização de dados, você pode manter os resultados somente sob as seguintes condições:

**Dispositivo.** Você pode permitir que um usuário final mantenha os resultados em um dispositivo para o que for menor: (i) 24 horas a partir da hora da consulta ou (ii) até que um usuário envie outra consulta para resultados atualizados, contanto que resultados retidos possam ser usados somente:

•   para permitir que o usuário final acesse os resultados retornados anteriormente para o usuário final no dispositivo (por exemplo, no caso de interrupção de serviço); ou  
  
•   para armazenar os resultados retornados para sua consulta proativa personalizada prevendo as necessidades do usuário final com base em sinais do usuário final (por exemplo, no caso de interrupção de serviço prevista).

**Servidor.** Você pode manter os resultados específicos para um único usuário final com segurança em um servidor que controla e exibir os resultados mantidos somente:

•   para permitir que o usuário final acesse um relatório histórico de resultados retornado anteriormente para esse usuário em sua solução, desde que os resultados não possam ser (i) retidos por mais de 21 dias a partir do momento da consulta inicial do usuário final e (ii) exibidos em resposta a uma consulta nova ou repetida de um usuário final; ou  
  
•   para armazenar os resultados retornados para sua consulta proativa personalizada prevendo as necessidades do usuário final, com base em sinais do usuário final para o que for menor: (i) 24 horas a partir da hora da consulta ou (ii) até que um usuário final envie outra consulta para resultados atualizados.

Sempre que retidos, os resultados para um usuário específico não podem ser associados aos resultados de outro usuário, ou seja, os resultados de cada usuário devem ser retidos e entregues separadamente.

**Geral.** Para obter toda a apresentação dos resultados retidos, faça o seguinte:

•   inclua um aviso criptografado visível do horário em que a consulta foi enviada,  
  
•   apresente ao usuário um botão ou meios semelhantes para consultar novamente e obter resultados atualizados,  
  
•   mantenha a identidade visual do Bing na apresentação dos resultados e  
  
•   exclua (e atualize com uma nova consulta, se necessário) os resultados armazenados em períodos de tempo especificados.

## <a name="7-non-display-url-discovery"></a>7. Descoberta de URL não de exibição.

Você só pode usar as respostas da pesquisa em uma experiência de pesquisa não na Internet para o único propósito de descoberta de URLs de fontes de informação responsivas a uma consulta do usuário ou cliente. Você pode copiar essas URLs em um relatório ou uma resposta semelhante que você fornece (i) somente para esse usuário ou cliente, em resposta à consulta específica e (ii) que inclui conteúdo adicional significativo e valioso que é relevante para a consulta. Os requisitos nas seções 2 a 6 desses requisitos de uso e exibição não se aplicam ao uso de não exibição, exceto: 

•   Você não deve armazenar em cache, copiar ou armazenar dados ou conteúdo de ou derivado da resposta de pesquisa, exceto a cópia de URL limitada descrita acima;  
  
•   Você deve garantir que o uso de dados (inclusive as URLs) recebidos das APIs de Pesquisa não viole leis ou direitos de terceiros; e  
  
•   Você não deve usar os dados (inclusive as URLs) recebidos das APIs de Pesquisa como parte de qualquer índice de pesquisa ou aprendizado de máquina ou atividade algorítmica semelhante para criar, treinar, avaliar ou melhorar os serviços que você ou terceiros podem oferecer.

