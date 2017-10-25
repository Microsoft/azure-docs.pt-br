---
title: Recursos do mecanismo de regras CDN do Azure | Microsoft Docs
description: "Documentação de referência para recursos e condições de correspondência do mecanismo de regras da CDN do Azure."
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: ec2555df27f4b709d06b660bf161f741e5b86ea6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cdn-rules-engine-features"></a>Recursos do mecanismo de regras da CDN do Azure
Este tópico lista descrições detalhadas dos recursos disponíveis para o [Mecanismo de regras](cdn-rules-engine.md)da CDN (Rede de Distribuição de Conteúdo) do Azure.

A terceira parte de uma regra é o recurso. Um recurso define o tipo de ação que será aplicado ao tipo de solicitação identificado por um conjunto de condições de correspondência.

## <a name="access"></a>Access

Esses recursos são projetados para controlar o acesso ao conteúdo.


Nome | Finalidade
-----|--------
Negar Acesso | Determina se todas as solicitações são rejeitadas com uma resposta 403 Proibido.
Autenticação de Token | Determina se a autenticação baseada em Token será aplicada a uma solicitação.
Código de Negação de Autenticação de Token | Determina o tipo de resposta que será retornado a um usuário quando uma solicitação for negada devido a autenticação baseada em token.
Autenticação de Token Ignorar Maiúsculas e Minúsculas da URL | Determina se as comparações de URL feitas por autenticação baseada em token diferenciam maiúsculas de minúsculas.
Parâmetros de Autenticação de Token | Determina se o parâmetro de cadeia de caracteres de consulta da autenticação baseada em token deve ser renomeado.

### <a name="deny-access"></a>Negar Acesso
**Finalidade**: determina se todas as solicitações são rejeitadas com uma resposta 403 Proibido.

Valor | Result
------|-------
Habilitado| Faz com que todas as solicitações que atendem aos critérios de correspondência sejam rejeitadas com uma resposta 403 Proibido.
Desabilitado| Restaura o comportamento padrão. O comportamento padrão é permitir que o servidor de origem determine o tipo de resposta que será retornado.

**Comportamento Padrão**: Desabilitado

> [!TIP]
   > Um uso possível para esse recurso é associá-lo a uma condição de correspondência de Cabeçalho de Solicitação para bloquear o acesso a referenciadores HTTP que estão usando links embutidos para seu conteúdo.

### <a name="token-auth"></a>Autenticação de Token
**Finalidade:** determina se a autenticação baseada em Token será aplicada a uma solicitação.

Se a Autenticação Baseada em Token for habilitada, somente solicitações que fornecerem um token criptografado e atenderem aos requisitos especificados por esse token serão respeitadas.

A chave de criptografia que será usada para criptografar e descriptografar valores de token é determinada pelas opções de Chave Primária e Chave de Backup na página de Autenticação de Token. Lembre-se de que as chaves de criptografia são específicas da plataforma.

Valor | Result
------|---------
Habilitado | Protege o conteúdo solicitado com Autenticação Baseada em Token. Somente as solicitações de clientes que fornecerem um token válido e atenderem aos requisitos serão respeitadas. Transações de FTP são excluídas da Autenticação Baseada em Token.
Desabilitado| Restaura o comportamento padrão. O comportamento padrão é permitir que sua configuração de Autenticação Baseada em Token determine se uma solicitação será protegida.

**Comportamento padrão:** desabilitado.

###<a name="token-auth-denial-code"></a>Código de Negação de Autenticação de Token
**Finalidade:** determina o tipo de resposta que será retornado a um usuário quando uma solicitação for negada devido a autenticação baseada em token.

Os códigos de resposta disponíveis são listados abaixo.

Código de Resposta|Nome da Resposta|Descrição
----------------|-----------|--------
301|Movido Permanentemente|Esse código de status redireciona os usuários não autorizados para a URL especificada no cabeçalho de Local.
302|Encontrado|Esse código de status redireciona os usuários não autorizados para a URL especificada no cabeçalho de Local. Esse código de status é o método padrão do setor para executar um redirecionamento.
307|Redirecionamento Temporário|Esse código de status redireciona os usuários não autorizados para a URL especificada no cabeçalho de Local.
401|Não Autorizado|Combinar esse código de status ao cabeçalho de resposta WWW-Authenticate permite solicitar autenticação ao usuário.
403|Proibido|Esta é a mensagem de status padrão 403 Proibido que um usuário não autorizado verá ao tentar acessar conteúdo protegido.
404|Arquivo Não Encontrado|Esse código de status indica que o cliente HTTP pôde se comunicar com o servidor, mas o conteúdo solicitado não foi encontrado.

#### <a name="url-redirection"></a>Redirecionamento de URL

Esse recurso dá suporte ao redirecionamento de URL para uma URL definida pelo usuário quando ele está configurado para retornar um código de status 3xx. Essa URL definida pelo usuário pode ser especificada executando as seguintes etapas:

1. Selecione um código de resposta 3xx para o recurso de Código de Negação de Autenticação de Token.
2. Selecione "Local" da opção de Nome de Cabeçalho Opcional.
3. Defina a opção de Valor de Cabeçalho Opcional para a URL desejada.

Se uma URL não estiver definida para um código de status 3xx, a página de resposta padrão para um código de status 3xx será retornada para o usuário.

O redirecionamento de URL só é aplicável para códigos de resposta 3xx.

A opção de Valor de Cabeçalho Opcional dá suporte a caracteres alfanuméricos, aspas e espaços.

#### <a name="authentication"></a>Autenticação

Esse recurso dá suporte à capacidade de incluir o cabeçalho WWW-Authenticate ao responder a uma solicitação não autorizada para conteúdo protegido por Autenticação Baseada em Token. Se o cabeçalho WWW-Authenticate tiver sido definido como "básico" em sua configuração, o usuário não autorizado será solicitado a fornecer credenciais de conta.

A configuração acima pode ser obtida executando as seguintes etapas:

1. Selecione "401" como o código de resposta para o recurso de Código de Negação de Autenticação de Token.
2. Selecione "WWW-Authenticate" da opção de Nome de Cabeçalho Opcional.
3. Defina a opção de Valor de Cabeçalho Opcional como "básica".

O cabeçalho WWW-Authenticate só é aplicável a códigos de resposta 401.

### <a name="token-auth-ignore-url-case"></a>Autenticação de Token Ignorar Maiúsculas e Minúsculas da URL
**Finalidade:** determina se as comparações de URL feitas por autenticação baseada em token diferenciam maiúsculas de minúsculas.

Os parâmetros afetados por esse recurso são:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Os valores válidos são:

Valor|Result
---|----
Habilitado|Faz com que nosso servidor de borda ignore maiúsculas e minúsculas durante a comparação de URLs para os parâmetros de Autenticação Baseada em Token.
Desabilitado|Restaura o comportamento padrão. O comportamento padrão é que comparações de URL para Autenticação de Token diferenciem maiúsculas de minúsculas.

**Comportamento padrão:** desabilitado.
 
### <a name="token-auth-parameter"></a>Parâmetros de Autenticação de Token
**Finalidade:** determina se o parâmetro de cadeia de caracteres de consulta da autenticação baseada em token deve ser renomeado.

Informações de chave:

- A opção Value define o nome do parâmetro de cadeia de caracteres de consulta por meio do qual um token pode ser especificado.
- A opção Value não pode ser definida como "ec_token".
- Certifique-se de que o nome definido na opção Valor contém apenas caracteres de URL válidos.

Valor|Result
----|----
Habilitado|A opção Value define o nome do parâmetro de cadeia de caracteres de consulta por meio do qual os tokens devem ser definidos.
Desabilitado|Um token pode ser especificado como um parâmetro de cadeia de caracteres de consulta indefinido na URL da solicitação.

**Comportamento padrão:** desabilitado. Um token pode ser especificado como um parâmetro de cadeia de caracteres de consulta indefinido na URL da solicitação.

## <a name="caching"></a>Cache

Esses recursos são projetados para personalizar quando e como o conteúdo é armazenado.

Nome | Finalidade
-----|--------
Parâmetros de Largura de Banda | Determina se os parâmetros de limitação de largura de banda (por exemplo, ec_rate e ec_prebuf) estarão ativos.
Limitação de Largura de Banda | Limita a largura de banda para a resposta fornecida por nossos servidores de borda.
Ignorar o Cache | Determina se a solicitação pode aproveitar nossa tecnologia de cache.
Tratamento de Cabeçalho Cache-Control | Controla a geração de cabeçalhos de Controle de Cache pelo servidor de borda quando o recurso Idade Máxima Externa está ativo.
Cadeia de Caracteres da Consulta da Chave de Cache | Determina se a chave de cache incluirá ou excluirá parâmetros de cadeia de caracteres de consulta associados a uma solicitação.
Regravação da Chave de Cache | Regrava a chave de cache associada a uma solicitação.
Concluir o Preenchimento do Cache | Determina o que acontece quando uma solicitação resulta em uma perda no cache parcial em um servidor de borda.
Compactar Tipos de Arquivo | Define os formatos de arquivo que serão compactados no servidor.
Idade Máxima Interna Padrão | Determina o intervalo de idade máxima padrão para a revalidação de cache do servidor de borda para o servidor de origem.
Tratamento do Cabeçalho Expira | Controla a geração de cabeçalhos Expira por um servidor de borda quando o recurso Idade Máxima Externa está ativo.
Idade Máxima Externa | Determina o intervalo de idade máxima para a revalidação de cache do navegador para o servidor de borda.
Forçar Idade Máxima Interna | Determina o intervalo de idade máxima para a revalidação de cache do servidor de borda para o servidor de origem.
Suporte a H.264 (Download Progressivo de HTTP) | Determina os tipos de formatos de arquivo H.264 que podem ser usados para transmitir conteúdo.
Respeitar Solicitação Sem Cache | Determina se solicitações sem cache de um cliente HTTP serão encaminhadas para o servidor de origem.
Ignorar Ausência de Cache de Origem | Determina se nosso CDN irá ignorar determinadas diretivas atendidas por um servidor de origem.
Ignorar Intervalos Insatisfatórios | Determina a resposta que será retornada aos clientes quando uma solicitação gerar um código de status 416 Intervalo Solicitado Insatisfatório.
Máximo de Estado Obsoleto Interno | Controla quanto tempo após o tempo de expiração normal um ativo em cache pode ser atendido por um servidor de borda quando este não puder revalidar o ativo em cache no servidor de origem.
Compartilhamento de Cache Parcial | Determina se uma solicitação pode gerar conteúdo parcialmente em cache.
Pré-validar Conteúdo Armazenado em Cache | Determina se o conteúdo armazenado em cache será qualificado para revalidação antecipada antes que o TTL expire.
Atualizar Arquivos de Cache de Zero Byte. | Determina como a solicitação de cliente HTTP para um ativo de cache de zero byte é tratado por nossos servidores de borda.
Definir Códigos de Status Armazenáveis em Cache | Define o conjunto de códigos de status que pode resultar em conteúdo armazenado em cache.
Distribuição de Conteúdo Obsoleta em Erro | Determina se o conteúdo armazenado em cache expirado será entregue quando ocorre um erro durante a revalidação do cache ou ao recuperar o conteúdo solicitado do servidor de origem do cliente.
Obsoleto Ao Revalidar | Melhora o desempenho permitindo que os nossos servidores de borda atendam clientes obsoletos para o solicitante enquanto ocorre a revalidação.
Comentário | O recurso Comentário permite a adição de detalhes a uma regra.

###<a name="bandwidth-parameters"></a>Parâmetros de Largura de Banda
**Finalidade:** determina se os parâmetros de limitação de largura de banda (por exemplo, ec_rate e ec_prebuf) estarão ativos.

Parâmetros de limitação de largura de banda determinam se a taxa de transferência de dados para uma solicitação do cliente será limitada a uma taxa personalizada.

Valor|Result
--|--
Habilitado|Permite que os nossos servidores de borda cumpram as solicitações de limitação de largura de banda.
Desabilitado|Faz com que nossos servidores de borda ignorem os parâmetros de limitação de largura de banda. O conteúdo solicitado será servido normalmente (ou seja, sem limitação de largura de banda).

**Comportamento padrão:** habilitado.

###<a name="bandwidth-throttling"></a>Limitação de Largura de Banda
**Finalidade:** limita a largura de banda para a resposta fornecida por nossos servidores de borda.

As duas opções a seguir devem ser definidas para configurar corretamente a limitação de largura de banda.

Opção|Descrição
--|--
Kbytes por segundo|Defina essa opção como a largura de banda máxima (Kb por segundo) que pode ser usada para fornecer a resposta.
Segundos de Prebuf|Defina essa opção como o número de segundos que nossos servidores de borda aguardarão até limitar a largura de banda. A finalidade desse período de tempo de largura de banda irrestrita é impedir que um player de mídia sofra problemas de intermitência ou de buffer devido à limitação de largura de banda.

**Comportamento padrão:** desabilitado.

###<a name="bypass-cache"></a>Ignorar o Cache
**Finalidade:** determina se a solicitação pode aproveitar nossa tecnologia de cache.

Valor|Result
--|--
Habilitado|Faz com que todas as solicitações sejam passadas para o servidor de origem, mesmo que o conteúdo tenha sido armazenado em cache anteriormente nos servidores de borda.
Desabilitado|Faz com que os servidores de borda armazenem ativos em cache de acordo com a política de cache definida em seus cabeçalhos de resposta.

**Comportamento padrão:**

- **HTTP Grande:** Desabilitado

<!---
- **ADN:** Enabled
--->

###<a name="cache-control-header-treatment"></a>Tratamento de Cabeçalho Cache-Control
**Finalidade:** controla a geração de cabeçalhos de Controle de Cache pelo servidor de borda quando o recurso Idade Máxima Externa está ativo.

A maneira mais fácil de obter esse tipo de configuração é colocar os recursos de Tratamento de Cabeçalho Max-Age Externo e Cache-Control na mesma instrução.

Valor|Result
--|--
Substituir|Garante que as seguintes ações ocorrerão:<br/> - Substitui o cabeçalho Cache-Control gerado pelo servidor de origem. <br/>- Adiciona o cabeçalho Cache-Control produzido pelo recurso externo Max-Age à resposta.
Passagem|Garante que o cabeçalho Cache-Control produzido pelo recurso externo Max-Age nunca seja adicionado à resposta. <br/> Se o servidor de origem produzir um cabeçalho Cache-Control, ele será passado para o usuário final. <br/> Se o servidor de origem não produzir um cabeçalho Cache-Control, essa opção poderá fazer com que o cabeçalho de resposta não contenha um cabeçalho Cache-Control.
Adicionar se Ausente|Se um cabeçalho Cache-Control não foi recebido do servidor de origem, essa opção adiciona o cabeçalho Cache-Control produzido pelo recurso Max-Age Externo. Essa opção é útil para garantir que um cabeçalho Cache-Control seja atribuído a todos os ativos.
Remover| Essa opção garante que um cabeçalho Cache-Control não seja incluído na resposta do cabeçalho. Se um cabeçalho Cache-Control já tiver sido atribuído, será removida da resposta de cabeçalho.

**Comportamento Padrão:** Substituir.

###<a name="cache-key-query-string"></a>Cadeia de Caracteres da Consulta da Chave de Cache
**Finalidade:** determina se a chave de cache incluirá ou excluirá parâmetros de cadeia de caracteres de consulta associados a uma solicitação.

Informações de chave:

- Especifique um ou mais nomes de parâmetro de cadeia de caracteres de consulta. Cada nome de parâmetro deve ser delimitado por um único espaço.
- Este recurso determina se parâmetros de cadeia de caracteres de consulta serão incluídos ou excluídos do cache-key. Informações adicionais são fornecidas para cada opção abaixo.

Tipo|Descrição
--|--
 Incluir|  Indica que cada parâmetro especificado deve ser incluído no cache-key. Uma cache-key exclusiva será gerada para cada solicitação que contém um valor exclusivo para um parâmetro de cadeia de caracteres de consulta definido neste recurso. 
 Incluir Todos  |Indica que um cache-key exclusivo será criado para cada solicitação para um ativo que inclui uma cadeia de caracteres de consulta exclusiva. Esse tipo de configuração normalmente não é recomendável porque pode levar a uma pequena porcentagem de acertos de cache. Isso aumentará a carga no servidor de origem, pois ele terá que fornecer mais solicitações. Essa configuração duplica o comportamento de cache conhecido como "unique-cache" na página de Cache de Query-String. 
 Excluir | Indica que somente os parâmetros especificados serão excluídos do cache-key. Todos os outros parâmetros de cadeia de caracteres de consulta serão incluídos no cache-key. 
 Excluir Todos  |Indica que todos os parâmetros de cadeia de caracteres de consulta serão excluídos do cache-key. Essa configuração duplica o padrão de comportamento, que é conhecido como "standard-cache" na página de Cache de Query-String. 

O Mecanismo de Regras de HTTP permite que você personalize o modo como o cache de cadeia de caracteres de consulta é implementado. Por exemplo, você pode especificar que o cache de cadeia de caracteres de consulta seja executado somente em determinados tipos de arquivos ou locais.

Para duplicar o comportamento de cache da cadeia de caracteres de consulta conhecido como "no-cache" na página de Cache de Query-String, você precisará criar uma regra que contenha uma condição de correspondência de Curinga de Consulta de URL e um recurso de Desvio de Cache. A condição de correspondência de Curinga de Consulta de URL deve ser definida como um asterisco (*).

#### <a name="sample-scenarios"></a>Cenários de Exemplo

O seguinte exemplo de uso para esse recurso fornece um exemplo de solicitação e a chave de cache padrão:

- **Solicitação de exemplo:** http://wpc.0001.&lt;Domain&gt;/800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01
- **Default cache-key:** /800001/Origin/folder/asset.htm

##### <a name="include"></a>Incluir

Configuração de exemplo:

- **Tipo:** Incluir
- **Parâmetro(s):** idioma

Este tipo de configuração geraria a seguinte chave de cache de parâmetro de cadeia de caracteres de consulta:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Incluir Todos

Configuração de exemplo:

- **Tipo:** Incluir Todos

Este tipo de configuração geraria a seguinte chave de cache de parâmetro de cadeia de caracteres de consulta:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Excluir

Configuração de exemplo:

- **Tipo:** Excluir
- **Parâmetros:** sessionid userid

Este tipo de configuração geraria a seguinte chave de cache de parâmetro de cadeia de caracteres de consulta:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Excluir Todos

Configuração de exemplo:

- **Tipo:** Excluir Todos

Este tipo de configuração geraria a seguinte chave de cache de parâmetro de cadeia de caracteres de consulta:

    /800001/Origin/folder/asset.htm

###<a name="cache-key-rewrite"></a>Regravação da Chave de Cache
**Finalidade:** regrava a chave de cache associada a uma solicitação.

Um cache-key é o caminho relativo que identifica um ativo para fins de cache. Em outras palavras, nossos servidores verificarão se há uma versão em cache de um ativo de acordo com seu caminho, conforme definido pelo cache-key.

Configure esse recurso definindo as duas seguintes opções:

Opção|Descrição
--|--
Caminho Original| Defina o caminho relativo para os tipos de solicitações cujo cache-key será regravado. Um caminho relativo pode ser definido selecionando um caminho de origem de base e, em seguida, definindo uma expressão regular padrão.
Novo Caminho|Defina o caminho relativo para o novo cache-key. Um caminho relativo pode ser definido selecionando um caminho de origem de base e, em seguida, definindo uma expressão regular padrão. Esse caminho relativo pode ser construído dinamicamente com o uso de variáveis HTTP
**Comportamento padrão:** o cache-key da solicitação é determinado pelo URI de solicitação.

###<a name="complete-cache-fill"></a>Concluir o Preenchimento do Cache
**Finalidade:** determina o que acontece quando uma solicitação resulta em uma perda no cache parcial em um servidor de borda.

Um erro de cache parcial descreve o status de cache para um ativo que não foi completamente baixado para um servidor de borda. Se um ativo estiver apenas parcialmente armazenado em cache em um servidor de borda, a próxima solicitação para esse ativo será encaminhada novamente ao servidor de origem.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.
--->
Um erro de cache parcial normalmente ocorre depois que um usuário anula um download ou para ativos que são solicitados somente usando solicitações de intervalo HTTP. Esse recurso é mais útil para ativos grandes em que os usuários normalmente não os baixarão do início ao fim (por exemplo, vídeos). Como resultado, esse recurso é habilitado por padrão na plataforma Grande HTTP. Isso é desabilitado em todas as outras plataformas.

É recomendável manter a configuração padrão para a plataforma HTTP Grande, pois isso reduzirá a carga no servidor de origem do cliente e aumentará a velocidade com a qual os clientes baixam o conteúdo.

Devido à maneira como as configurações de cache são acompanhadas, esse recurso não pode ser associado às seguintes condições de correspondência: Cname de Borda, Literal de Cabeçalho de Solicitação, Curinga de Cabeçalho de Solicitação, URL de Consulta Literal e Curinga de Consulta de URL.

Valor|Result
--|--
Habilitado|Restaura o comportamento padrão. O comportamento padrão é forçar o servidor de borda a iniciar uma busca em segundo plano do ativo do servidor de origem. Depois disso, o ativo estará no cache local do servidor de borda.
Desabilitado|Impede que um servidor de borda realize uma busca em segundo plano para o ativo. Isso significa que a próxima solicitação para esse ativo dessa região fará com que um servidor de borda solicite-o do servidor de origem do cliente.

**Comportamento padrão:** habilitado.

###<a name="compress-file-types"></a>Compactar Tipos de Arquivo
**Finalidade:** define os formatos de arquivo que serão compactados no servidor.

Um formato de arquivo pode ser especificado usando o tipo de mídia da Internet (por exemplo, Content-Type). O tipo de mídia da Internet são metadados independentes de plataforma que permitem que nossos servidores identifiquem o formato de arquivo de determinado ativo. Uma lista dos tipos comuns de mídia da Internet é fornecida abaixo.

Tipo de Mídia da Internet|Descrição
--|--
texto/sem formatação|Arquivos de texto sem formatação
texto/html| Arquivos HTML
texto/css|Folhas de Estilo em Cascata (CSS)
aplicativo/x-javascript|JavaScript
aplicativo/javascript|JavaScript
Informações de chave:

- Especifique vários tipos de mídia da Internet delimitando cada um deles com um único espaço. 
- Esse recurso compactará apenas ativos cujo tamanho seja menor que 1 MB. Ativos maiores não serão compactados por nossos servidores.
- Determinados tipos de conteúdo, como ativos de mídia de imagem, de vídeo e de áudio (por exemplo, JPG, MP3, MP4, etc.) já estão compactados. A compactação adicional nesses tipos de ativos não reduzirá significativamente o tamanho do arquivo. Portanto, é recomendável que você não habilite a compactação nesses tipos de ativos.
- Não há suporte para caracteres curinga, como asteriscos.
- Antes de adicionar esse recurso a uma regra, certifique-se de ter configurado a opção Compactação desabilitada na página Compactação para a plataforma à qual essa regra será aplicada.

###<a name="default-internal-max-age"></a>Idade Máxima Interna Padrão
**Finalidade:** determina o intervalo de idade máxima padrão para a revalidação de cache do servidor de borda para o servidor de origem. Em outras palavras, o tempo que decorrerá até que um servidor de borda verifique se um ativo em cache corresponde ao ativo armazenado no servidor de origem.

Informações de chave:

- Esta ação ocorrerá somente para respostas de um servidor de origem que não atribuiu uma indicação de max-age no cabeçalho Cache-Control ou Expires.
- Essa ação não ocorrerá para ativos que não são considerados armazenáveis em cache.
- Essa ação não afeta o navegador para revalidações de cache de servidor de borda. Esses tipos de revalidações são determinados pelos cabeçalhos Cache-Control ou Expires enviados ao navegador, que podem ser personalizados com o recurso Max-Age Externo.
- Os resultados dessa ação não têm um efeito observável nos cabeçalhos de resposta e no conteúdo retornado de servidores de borda para seu conteúdo, mas podem ter efeito na quantidade de tráfego de revalidação enviada por servidores de borda ao servidor de origem.
- Configure este recurso da seguinte forma:
    - Selecionando o código de status ao qual um padrão de max-age interno pode ser aplicado.
    - Especificando um valor inteiro e, em seguida, selecionando a unidade de tempo desejada (por exemplo, segundos, minutos, horas etc.). Esse valor define o intervalo de max-age interno padrão.

- Definir a unidade de tempo como "Desativado" atribuirá um intervalo de max-age interno padrão de sete dias para solicitações que não tenham uma indicação de max-age atribuída em seu cabeçalho Cache-Control ou Expires.
- Devido à maneira como as configurações de cache são acompanhadas, esse recurso não pode ser associado às seguintes condições de correspondência: 
    - Edge 
    - Cname
    - Literal de Cabeçalho de Solicitação
    - Curinga de Cabeçalho de Solicitação
    - Método de Solicitação
    - Literal da Consulta da URL
    - Curinga da consulta da URL

**Valor Padrão:** 7 dias

###<a name="expires-header-treatment"></a>Tratamento do Cabeçalho Expira
**Finalidade:** controla a geração de cabeçalhos Expira por um servidor de borda quando o recurso Idade Máxima Externa está ativo.

A maneira mais fácil de obter esse tipo de configuração é colocar os recursos de Tratamento de Cabeçalho Max-Age Externos e Expires na mesma instrução.

Valor|Result
--|--
Substituir|Garante que as seguintes ações ocorrerão:<br/>- Substitui o cabeçalho Expires gerado pelo servidor de origem.<br/>- Adiciona o cabeçalho Expires produzido pelo recurso externo Max-Age à resposta.
Passagem|Garante que o cabeçalho Expires produzido pelo recurso externo Max-Age nunca seja adicionado à resposta. <br/> Se o servidor de origem produzir um cabeçalho Expires, ele será passado para o usuário final. <br/>Se o servidor de origem não produzir um cabeçalho Expires, essa opção poderá fazer com que o cabeçalho de resposta não contenha um cabeçalho Expires.
Adicionar se Ausente| Se um cabeçalho Expires não foi recebido do servidor de origem, essa opção adiciona o cabeçalho Expires produzido pelo recurso Max-Age Externo. Essa opção é útil para garantir que um cabeçalho Expires seja atribuído a todos os ativos.
Remover| Garante que um cabeçalho Expires não seja incluído na resposta do cabeçalho. Se um cabeçalho Expires já tiver sido atribuído, será removido da resposta de cabeçalho.

**Comportamento Padrão:** Substituir

###<a name="external-max-age"></a>Idade Máxima Externa
**Finalidade:** determina o intervalo de idade máxima para a revalidação de cache do navegador para o servidor de borda. Em outras palavras, o tempo que decorrerá até que um navegador possa procurar uma nova versão de um ativo de um servidor de borda.

Habilitar esse recurso gerará cabeçalhos Cache-Control: max-age e Expire de nossos servidores de borda e os enviará ao cliente HTTP. Por padrão, esses cabeçalhos substituirão os criados pelo servidor de origem. No entanto, os recursos de tratamento de Cabeçalho Cache-Control e Cabeçalho Expires podem ser usados para alterar esse comportamento.

Informações de chave:

- Essa ação não afeta o servidor de borda para revalidações de cache de servidor de origem. Esses tipos de revalidações são determinados pelos cabeçalhos Cache-Control/Expires recebidos do servidor de origem e podem ser personalizados com os recursos Max-Age e Force Internal Max-Age padrão.
- Configure esse recurso especificando um valor inteiro e selecionando a unidade de tempo desejada (por exemplo, segundos, minutos, horas, etc.).
- Definir esse recurso como um valor negativo faz com que nossos servidores de borda enviem um Cache-Control:no-cache e um tempo Expires que é definido no passado com cada resposta ao navegador. Embora um cliente HTTP não armazene em cache a resposta, essa configuração não afetará a capacidade de nossos servidores de borda de armazenar em cache a resposta do servidor de origem.
- Definir a unidade de tempo como "Desativado" desabilitará esse recurso. Os cabeçalhos Cache-Control/Expires em cache com a resposta do servidor de origem serão passados para o navegador.

**Comportamento padrão:** desativado

###<a name="force-internal-max-age"></a>Forçar Idade Máxima Interna
**Finalidade:** determina o intervalo de idade máxima para a revalidação de cache do servidor de borda para o servidor de origem. Em outras palavras, o tempo que decorrerá até que um servidor de borda possa verificar se um ativo em cache corresponde ao ativo armazenado no servidor de origem.

Informações de chave:

- Esse recurso substituirá o intervalo de max-age definido em cabeçalhos Cache-Control ou Expires gerados com base em um servidor de origem.
- Esse recurso não afeta o navegador para revalidações exame de cache de servidor de borda. Esses tipos de revalidações são determinados pelos cabeçalhos Cache-Control ou Expires enviados ao navegador.
- Esse recurso não tem efeito observável na resposta fornecida por um servidor de borda para o solicitante. No entanto, isso pode afetar a quantidade de tráfego de revalidação enviado de nossos servidores de borda para o servidor de origem.
- Configure este recurso da seguinte forma:
    - Selecionando o código de status ao qual um max-age interno será aplicado.
    - Especificando um valor inteiro e selecionando a unidade de tempo desejada (por exemplo, segundos, minutos, horas, etc.). Esse valor define o intervalo de max-age da solicitação.

- Definir a unidade de tempo como "Desativado" desabilita a esse recurso. Um intervalo max-age interno não será atribuído aos ativos solicitados. Se o cabeçalho original não contiver instruções de cache, o ativo será armazenado de acordo com a configuração ativa no recurso Max-Age Interno Padrão.
- Devido à maneira como as configurações de cache são acompanhadas, esse recurso não pode ser associado às seguintes condições de correspondência: 
    - Edge 
    - Cname
    - Literal de Cabeçalho de Solicitação
    - Curinga de Cabeçalho de Solicitação
    - Método de Solicitação
    - Literal da Consulta da URL
    - Curinga da consulta da URL

**Comportamento padrão:** desativado

###<a name="h264-support-http-progressive-download"></a>Suporte a H.264 (Download Progressivo de HTTP)
**Finalidade:** determina os tipos de formatos de arquivo H.264 que podem ser usados para transmitir conteúdo.

Informações de chave:

- Defina um conjunto de extensões de nome de arquivo H.264 permitidas delimitadas por espaço na opção de Extensões de Arquivo. A opção de Extensões de Arquivo substituirá o comportamento padrão. Mantenha o suporte a MP4 e F4V incluindo as extensões de nome de arquivo ao definir esta opção. 
- Certifique-se de incluir um ponto ao especificar cada extensão de nome de arquivo (por exemplo, .mp4 .f4v).

**Comportamento padrão:** o Download Progressivo de HTTP dá suporte à mídia MP4 e F4V por padrão.

###<a name="honor-no-cache-request"></a>Respeitar solicitação no-cache
**Finalidade:** determina se solicitações sem cache de um cliente HTTP serão encaminhadas para o servidor de origem.

Uma solicitação no-cache ocorre quando o cliente HTTP envia um Cache-Control: no-cache e/ou cabeçalho Pragma:no-cache na solicitação HTTP.

Valor|Result
--|--
Habilitado|Permite que solicitações no-cache de um cliente HTTP sejam encaminhadas ao servidor de origem, e o servidor de origem retornará os cabeçalhos de resposta e o corpo por meio do servidor de borda para o cliente HTTP.
Desabilitado|Restaura o comportamento padrão. O comportamento padrão é impedir que solicitações de cache sejam encaminhadas para o servidor de origem.

Para todo o tráfego de produção, é altamente recomendável deixar esse recurso em seu estado padrão desabilitado. Caso contrário, os servidores de origem não serão protegidos de usuários finais que podem disparar inadvertidamente muitas solicitações no-cache durante a atualização de páginas da Web ou de muitos players de mídia mais populares que são codificados para enviar um cabeçalho no-cache com cada solicitação de vídeo. No entanto, esse recurso pode ser útil para ser aplicado a determinados diretórios de teste que não sejam de produção, para permitir que o conteúdo novo seja obtido sob demanda do servidor de origem.

O status de cache que será relatado a uma solicitação que pode ser encaminhada para um servidor de origem devido a esse recurso é TCP_Client_Refresh_Miss. O relatório de Status do Cache, que está disponível no módulo de relatório de Núcleo, fornece informações estatísticas por status de cache. Isso permite controlar o número e a porcentagem de solicitações que são encaminhadas para um servidor de origem devido a esse recurso.

**Comportamento padrão:** desabilitado.

###<a name="ignore-origin-no-cache"></a>Ignorar no-cache de origem
**Finalidade:** determina se nossa CDN ignorará as seguintes diretivas fornecidas de um servidor de origem:

- Cache-Control: privado
- Cache-Control: no-store
- Cache-Control: no-cache
- Pragma: no-cache

Informações de chave:

- Configure esse recurso definindo uma lista delimitada por espaços de códigos de status para os quais as diretivas acima serão ignoradas.
- O conjunto de códigos de status válidos para esse recurso é: 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 e 505.
- Desabilite esse recurso definindo-o como um valor em branco.
- Devido à maneira como as configurações de cache são acompanhadas, esse recurso não pode ser associado às seguintes condições de correspondência: 
    - Edge 
    - Cname
    - Literal de Cabeçalho de Solicitação
    - Curinga de Cabeçalho de Solicitação
    - Método de Solicitação
    - Literal da Consulta da URL
    - Curinga da consulta da URL

**Comportamento padrão:** o comportamento padrão é obedecer às diretivas acima.

###<a name="ignore-unsatisfiable-ranges"></a>Ignorar Intervalos Insatisfatórios 
**Finalidade:** determina a resposta que será retornada aos clientes quando uma solicitação gerar um código de status 416 Intervalo Solicitado Insatisfatório.

Por padrão, esse código de status é retornado quando a solicitação de intervalo de bytes especificado não pode ser satisfeita por um servidor de borda e um campo de cabeçalho de solicitação If-Range não foi especificado.

Valor|Result
-|-
Habilitado|Impede que nossos servidores de borda respondam a uma solicitação inválida de intervalo de bytes com um código de status 416 Intervalo Solicitado Insatisfatório. Em vez disso, nossos servidores distribuirão o ativo solicitado e retornarão uma resposta 200 OK ao cliente.
Desabilitado|Restaura o comportamento padrão. O comportamento padrão é cumprir o código de status 416 Intervalo Solicitado Insatisfatório.

**Comportamento padrão:** desabilitado.

###<a name="internal-max-stale"></a>Máximo de Estado Obsoleto Interno
**Finalidade:** controla quanto tempo após o tempo de expiração normal um ativo em cache pode ser atendido por um servidor de borda quando este não puder revalidar o ativo em cache no servidor de origem.

Normalmente, quando o tempo de max-age de um ativo expira, o servidor de borda envia uma solicitação de revalidação ao servidor de origem. O servidor de origem responderá com um 304 Não Modificado para dar ao servidor de borda de uma nova concessão para o cache ativo ou com um 200 OK para fornecer ao servidor de borda uma versão atualizada do cache ativo.

Se o servidor de borda não puder estabelecer uma conexão com o servidor de origem durante a tentativa de revalidação, esse recurso Max-Stale Interno controlará se e por quanto tempo o servidor de borda poderá continuar a fornecer o ativo agora obsoleto.

Observe que esse intervalo de tempo se inicia quando max-age do ativo expira, não quando a revalidação com falha ocorre. Portanto, o período máximo durante o qual um ativo pode ser fornecido sem revalidação bem-sucedida é o tempo especificado pela combinação de max-age e max-stale. Por exemplo, se um ativo foi armazenado em cache às 9:00, com max-age de 30 minutos e max-stale de 15 minutos, uma tentativa de revalidação com falha às 9:44 faria com que um usuário final recebesse o ativo em cache obsoleto, enquanto uma tentativa de revalidação com falha às 9:46 faria com que o usuário final recebesse um 504 Tempo Limite do Gateway.

Qualquer valor configurado para esse recurso é substituído por cabeçalhos Cache-Control:must-revalidate ou Cache-Control:proxy-revalidate recebidos do servidor de origem. Se um desses cabeçalhos for recebido do servidor de origem quando um ativo for inicialmente armazenado em cache, o servidor de borda não fornecerá um ativo em cache obsoleto. Nesse caso, se o servidor de borda não puder fazer a revalidação com a origem quando o intervalo de duração máxima do ativo expirar, o servidor de borda retornará um 504 Tempo Limite do Gateway.

Informações de chave:

- Configure este recurso da seguinte forma:
    - Selecionando o código de status ao qual um max-stale será aplicado.
    - Especificando um valor inteiro e, em seguida, selecionando a unidade de tempo desejada (por exemplo, segundos, minutos, horas etc.). Esse valor define o max-stale interno que será aplicado.

- Definir a unidade de tempo como "Desativado" desabilitará esse recurso. Um ativo em cache não será servido além de seu tempo de expiração normal.
- Devido à maneira como as configurações de cache são acompanhadas, esse recurso não pode ser associado às seguintes condições de correspondência: 
    - Edge 
    - Cname
    - Literal de Cabeçalho de Solicitação
    - Curinga de Cabeçalho de Solicitação
    - Método de Solicitação
    - Literal da Consulta da URL
    - Curinga da consulta da URL

**Comportamento padrão:** dois minutos

###<a name="partial-cache-sharing"></a>Compartilhamento de Cache Parcial
**Finalidade:** determina se uma solicitação pode gerar conteúdo parcialmente em cache.

Esse cache parcial pode ser usado para atender a novas solicitações desse conteúdo até que o conteúdo solicitado seja totalmente armazenado em cache.

Valor|Result
-|-
Habilitado|As solicitações podem gerar conteúdo parcialmente em cache.
Desabilitado|As solicitações só podem gerar uma versão totalmente em cache do conteúdo solicitado.

**Comportamento padrão:** desabilitado.

###<a name="prevalidate-cached-content"></a>Pré-validar Conteúdo Armazenado em Cache
**Finalidade:** determina se o conteúdo armazenado em cache será qualificado para revalidação antecipada antes que o TTL expire.

Defina o período de tempo antes da expiração do tempo de vida do conteúdo solicitado durante o qual ele estará qualificado para revalidação inicial.

Informações de chave:

- Selecionar "Desativado" como a unidade de tempo requer que a revalidação ocorra após o TTL do conteúdo em cache expirar. O tempo não deve ser especificado e será ignorado.

**Comportamento padrão:** desativado. A revalidação só pode ocorrer após a expiração do TTL do conteúdo armazenado em cache.

###<a name="refresh-zero-byte-cache-files"></a>Atualizar Arquivos de Cache de Zero Byte
**Finalidade:** determina como a solicitação de cliente HTTP para um ativo de cache de zero byte é tratado por nossos servidores de borda.

Os valores válidos são:

Valor|Result
--|--
habilitado|Faz o servidor de borda buscar novamente o ativo do servidor de origem.
Desabilitado|Restaura o comportamento padrão. O comportamento padrão é fornecer ativos de cache válidos mediante solicitação.
Este recurso não é necessário para o armazenamento em cache e o fornecimento de conteúdo corretos, mas pode ser útil como solução alternativa. Por exemplo, geradores de conteúdo dinâmicos em servidores de origem inadvertidamente podem fazer com que respostas de 0 byte sejam enviadas aos servidores de borda. Esses tipos de respostas normalmente são armazenados em cache por nossos servidores de borda. Se você souber que uma resposta de 0 byte nunca é uma resposta válida 

para tal conteúdo, esse recurso pode impedir que esses tipos de recursos sejam servidos para seus clientes.

**Comportamento padrão:** desabilitado.

###<a name="set-cacheable-status-codes"></a>Definir Códigos de Status Armazenáveis em Cache
**Finalidade:** define o conjunto de códigos de status que pode resultar em conteúdo armazenado em cache.

Por padrão, o cache é habilitado apenas para respostas 200 OK.

Defina um conjunto delimitado por espaços dos códigos de status desejados.

Informações de chave:

- Habilite o recurso Ignorar no-cache de origem. Se esse recurso não estiver habilitado, as respostas diferentes de 200 OK não poderão ser armazenadas em cache.
- O conjunto de códigos de status válidos para esse recurso é: 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 e 505.
- Esse recurso não pode ser usado para desabilitar o cache para respostas que geram um código de status 200 OK.

**Comportamento padrão:** o cache está habilitado apenas para respostas que geram um código de status 200 OK.
###<a name="stale-content-delivery-on-error"></a>Distribuição de Conteúdo Obsoleta em Erro
**Finalidade:** 

Determina se o conteúdo armazenado em cache expirado será entregue quando ocorre um erro durante a revalidação do cache ou ao recuperar o conteúdo solicitado do servidor de origem do cliente.

Valor|Result
-|-
Habilitado|Conteúdo obsoleto será fornecido ao solicitante quando ocorrer um erro durante a conexão com um servidor de origem.
Desabilitado|O erro do servidor de origem será encaminhado ao solicitante.

**Comportamento Padrão:** Desabilitado

###<a name="stale-while-revalidate"></a>Obsoleto Ao Revalidar
**Finalidade:** melhora o desempenho permitindo que os nossos servidores de borda atendam clientes obsoletos para o solicitante enquanto ocorre a revalidação.

Informações de chave:

- O comportamento desse recurso varia de acordo com a unidade de tempo selecionada.
    - **Unidade de tempo:** especifique um período de tempo e selecione uma unidade de tempo (por exemplo, segundos, minutos, horas, etc.) para permitir a entrega de conteúdo obsoleto. Esse tipo de configuração permite que a CDN estenda o período de tempo em que pode entregar conteúdo antes de solicitar a validação, de acordo com a seguinte fórmula:**TTL** + **Tempo Obsoleto ao Revalidar** 
    - **Desativado:** selecione "Desativado" para exigir revalidação para que uma solicitação de conteúdo obsoleto possa ser atendida.
        - Não especifique um período de tempo, pois ele é inaplicável e será ignorado.

**Comportamento padrão:** desativado. A revalidação deve ocorrer para que o conteúdo solicitado possa ser fornecido.

###<a name="comment"></a>Comentário
**Finalidade:** permite que uma anotação seja adicionada a uma regra.

Um uso para esse recurso é fornecer informações adicionais sobre a finalidade geral de uma regra ou indicar por que determinado recurso ou condição de correspondência foi adicionado à regra.

Informações de chave:

- Um máximo de 150 caracteres pode ser especificado.
- Use apenas caracteres alfanuméricos.
- Esse recurso não afeta o comportamento da regra. Ele simplesmente serve para fornecer uma área em que você pode fornecer informações para referência futura ou que podem ajudar na solução de problemas da regra.
 
## <a name="headers"></a>headers

Esses recursos são projetados para adicionar, modificar ou excluir os cabeçalhos da solicitação ou da resposta.

Nome | Finalidade
-----|--------
Cabeçalho de Resposta de Idade | Determina se um cabeçalho de resposta de idade será incluído na resposta enviada ao solicitante.
Depurar Cabeçalhos de Resposta do Cache | Determina se uma resposta pode incluir o cabeçalho de resposta X-EC-Debug, que fornece informações sobre a política de cache para o recurso solicitado.
Modificar Cabeçalho de Solicitação do Cliente | Substitui, acrescenta ou exclui um cabeçalho de uma solicitação.
Modificar Cabeçalho de Resposta do Cliente | Substitui, acrescenta ou exclui um cabeçalho de uma resposta.
Definir Cabeçalho Personalizado da IP do Cliente | Permite que o endereço IP do cliente solicitante seja adicionado à solicitação como um cabeçalho de solicitação personalizado.

###<a name="age-response-header"></a>Cabeçalho de Resposta de Idade
**Finalidade**: determina se um cabeçalho de resposta de idade será incluído na resposta enviada ao solicitante.
Valor|Result
--|--
Habilitado | O cabeçalho de resposta Age será incluído na resposta enviada ao solicitante.
Desabilitado | O cabeçalho de resposta Age será excluído da resposta enviada ao solicitante.

**Comportamento Padrão**: Desabilitado.

###<a name="debug-cache-response-headers"></a>Depurar Cabeçalhos de Resposta do Cache
**Finalidade:** determina se uma resposta pode incluir o cabeçalho de resposta X-EC-Debug, que fornece informações sobre a política de cache para o recurso solicitado.

Os cabeçalhos de resposta de cache de depuração serão incluídos na resposta quando ambos os seguintes itens forem verdadeiros:

- O Recurso de Cabeçalhos de Resposta de Cache de Depuração foi habilitado na solicitação desejada.
- A solicitação acima define o conjunto de cabeçalhos de resposta do cache de depuração que serão incluídos na resposta.

Cabeçalhos de resposta de cache de depuração podem ser solicitados incluindo-se o seguinte cabeçalho e as diretivas desejadas na solicitação:

X-EC-Debug: _Directive1_,_Directive2_,_DirectiveN_

**Exemplo:**

X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

Valor|Result
-|-
Habilitado|Solicitações para cabeçalhos de resposta do cache de depuração retornarão uma resposta que inclui o cabeçalho X-EC-Debug.
Desabilitado|O cabeçalho de resposta X-EC-Debug será excluído da resposta.

**Comportamento padrão:** desabilitado.

###<a name="modify-client-response-header"></a>Modificar Cabeçalho de Resposta do Cliente
**Finalidade:** cada solicitação contém um conjunto de [cabeçalhos de solicitação]() que a descrevem. Este recurso pode:

- Acrescente ou substitua o valor atribuído a um cabeçalho de solicitação. Se o cabeçalho de solicitação especificado não existir, esse recurso o adicionará à solicitação.
- Exclua um cabeçalho de solicitação da solicitação.

As solicitações encaminhadas para um servidor de origem refletirão as alterações feitas por esse recurso.

Uma das seguintes ações pode ser realizada em um cabeçalho de solicitação:

Opção|Descrição|Exemplo
-|-|-
Acrescentar|O valor especificado será adicionado ao fim do valor de cabeçalho de solicitação existente.|**Valor de cabeçalho de solicitação (cliente):**Valor1 <br/> **Valor de cabeçalho de solicitação (Mecanismo de Regras de HTTP):** Valor2 <br/>**Novo valor de cabeçalho de solicitação:** Value1Value2
Substituir|O valor de cabeçalho de solicitação será definido com o valor especificado.|**Valor de cabeçalho de solicitação (cliente):**Valor1 <br/>**Valor de cabeçalho de solicitação (Mecanismo de Regras de HTTP):** Valor2 <br/>**Novo valor de cabeçalho de solicitação:** Value2 <br/>
Excluir|Exclui o cabeçalho de solicitação especificado.|**Valor de cabeçalho de solicitação (cliente):**Valor1 <br/> **Modificar a configuração de Cabeçalho de Solicitação de Cliente:** exclua o cabeçalho de solicitação em questão. <br/>**Resultado:** o cabeçalho de solicitação especificado não será encaminhado ao servidor de origem.

Informações de chave:

- Certifique-se de que o valor especificado na opção Nome é uma correspondência exata para o cabeçalho de solicitação desejado.
- As maiúsculas e minúsculas não são levadas em conta para fins de identificação de um cabeçalho. Por exemplo, qualquer uma das seguintes variações do nome do cabeçalho Cache-Control pode ser usada para identificá-lo:
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- Ao especificar um nome de cabeçalho, use apenas caracteres alfanuméricos, traços ou sublinhados.
- Excluir um cabeçalho impedirá que ele seja encaminhado para um servidor de origem por nossos servidores de borda.
- Os seguintes cabeçalhos são reservados e não podem ser modificados por este recurso:
    - encaminhado
    - host
    - via
    - Aviso
    - x-forwarded-for
    - Todos os nomes de cabeçalho que começam com "x-ec" são reservados.

###<a name="modify-client-response-header"></a>Modificar Cabeçalho de Resposta do Cliente
Cada resposta contém um conjunto de [cabeçalhos de resposta]() que o descrevem. Este recurso pode:

- Acrescente ou substitua o valor atribuído a um cabeçalho de resposta. Se o cabeçalho de solicitação especificado não existir, esse recurso o adicionará à resposta.
- Exclua um cabeçalho de resposta da resposta.

Por padrão, os valores de cabeçalho de resposta são definidos por um servidor de origem e por nossos servidores de borda.

Uma das seguintes ações pode ser realizada em um cabeçalho de resposta:

Opção|Descrição|Exemplo
-|-|-
Acrescentar|O valor especificado será adicionado ao fim do valor de cabeçalho de solicitação existente.|**Valor de cabeçalho de resposta (Cliente):**Valor1 <br/> **Valor de cabeçalho de resposta (Mecanismo de Regras de HTTP):** Valor2 <br/>**Novo valor de cabeçalho de resposta:** Value1Value2
Substituir|O valor de cabeçalho de solicitação será definido com o valor especificado.|**Valor de cabeçalho de resposta (Cliente):**Valor1 <br/>**Valor de cabeçalho de resposta (Mecanismo de Regras de HTTP):** Valor2 <br/>**Novo valor de cabeçalho de resposta:** Value2 <br/>
Excluir|Exclui o cabeçalho de solicitação especificado.|**Valor de cabeçalho (cliente) da solicitação:** Value1 <br/> **Modificar a configuração de Cabeçalho de Solicitação de Cliente:** exclua o cabeçalho de resposta em questão. <br/>**Resultado:** o cabeçalho de resposta especificado não será encaminhado ao solicitante.

Informações de chave:

- Certifique-se de que o valor especificado na opção Nome é uma correspondência exata para o cabeçalho de resposta desejado. 
- As maiúsculas e minúsculas não são levadas em conta para fins de identificação de um cabeçalho. Por exemplo, qualquer uma das seguintes variações do nome do cabeçalho Cache-Control pode ser usada para identificá-lo:
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- Excluir um cabeçalho impedirá que ele seja encaminhado para o solicitante.
- Os seguintes cabeçalhos são reservados e não podem ser modificados por este recurso:
    - accept-encoding
    - age
    - connection
    - content-encoding
    - content-length
    - content-range
    - data
    - server
    - trailer
    - transfer-encoding
    - atualizar
    - vary
    - via
    - Aviso
    - Todos os nomes de cabeçalho que começam com "x-ec" são reservados.

###<a name="set-client-ip-custom-header"></a>Definir Cabeçalho Personalizado da IP do Cliente
**Finalidade:** adiciona um cabeçalho personalizado que identifica o cliente solicitante pelo endereço IP para a solicitação.

A opção de nome de Cabeçalho define o nome do cabeçalho de solicitação personalizado em que o endereço IP do cliente será armazenado.

Esse recurso permite que um servidor de origem de cliente obtenha endereços IP de cliente por meio de um cabeçalho de solicitação personalizado. Se a solicitação for atendida do cache, o servidor de origem não será informado do endereço IP do cliente. Portanto, é recomendável que esse recurso seja usado com ADN ou ativos que não serão ser armazenado em cache.

Certifique-se de que o nome do cabeçalho especificado não corresponde a nenhum um dos seguintes nomes:

- Nomes de cabeçalho de solicitação padrão. Uma lista de nomes de cabeçalho padrão pode ser encontrada em [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Nomes de cabeçalho reservados:
    - forwarded-for
    - host
    - vary
    - via
    - Aviso
    - x-forwarded-for
    - Todos os nomes de cabeçalho que começam com "x-ec" são reservados.
 
## <a name="logs"></a>Logs

Esses recursos são projetados para personalizar os dados armazenados em arquivos de log brutos.

Nome | Finalidade
-----|--------
Campo de Log Personalizado 1 | Determina o formato e o conteúdo que será atribuído ao campo de log personalizado em um arquivo de log bruto.
Cadeia de Caracteres de Consulta de Log | Determina se uma cadeia de caracteres de consulta será armazenada juntamente com a URL nos logs de acesso.

###<a name="custom-log-field-1"></a>Campo de Log Personalizado 1
**Finalidade:** determina o formato e o conteúdo que será atribuído ao campo de log personalizado em um arquivo de log bruto.

O objetivo principal do campo personalizado é permitir que você determine quais valores de cabeçalho de solicitação e resposta serão armazenados nos arquivos de log.

Por padrão, o campo de log personalizado é chamado de "x-ec_custom-1". No entanto, o nome desse campo pode ser personalizado na [página de Configurações de Log Bruto]().

A formatação que você deve usar para especificar cabeçalhos de solicitação e de resposta é definida abaixo.

Tipo de Cabeçalho|Formatar|Exemplos
-|-|-
Cabeçalho da Solicitação|%{[RequestHeader]()}[i]() | %{Accept-Encoding}i <br/> {Referer}i <br/> %{Authorization}i
Cabeçalho de Resposta|%{[ResponseHeader]()}[o]()| %{Age}o <br/> %{Content-Type}o <br/> %{Cookie}o

Informações de chave:

- Um campo de log personalizado pode conter qualquer combinação de campos de cabeçalho e texto sem formatação.
- Os caracteres válidos para esse campo incluem: alfanuméricos (0-9, a-z e A-Z), traços, dois-pontos, ponto e vírgula, apóstrofos, vírgulas, pontos, sublinhados, sinais de igual, parênteses, colchetes e espaços. O símbolo de porcentagem e as chaves são permitidos apenas quando usados para especificar um campo de cabeçalho.
- A ortografia de cada campo de cabeçalho especificado deve corresponder ao nome de cabeçalho de solicitação/resposta desejado.
- Para especificar vários cabeçalhos, é recomendável que você use um separador para indicar cada cabeçalho. Por exemplo, você poderia usar uma abreviação para cada cabeçalho. A sintaxe de exemplo é fornecida abaixo.
    - AE: %{Accept-Encoding}i A: %{Authorization}i CT: %{Content-Type}o 

**Valor Padrão:** -

###<a name="log-query-string"></a>Cadeia de Caracteres de Consulta de Log
**Finalidade:** determina se uma cadeia de caracteres de consulta será armazenada juntamente com a URL nos logs de acesso.

Valor|Result
-|-
Habilitado|Permite o armazenamento de cadeias de caracteres de consulta durante a gravação de URLs em um log de acesso. Se uma URL não contiver uma cadeia de caracteres de consulta, essa opção não terá efeito.
Desabilitado|Restaura o comportamento padrão. O comportamento padrão é ignorar as cadeias de caracteres de consulta durante a gravação de URLs em um log de acesso.

**Comportamento padrão:** desabilitado.

<!---
## Optimize

These features determine whether a request will undergo the optimizations provided by Edge Optimizer.

Name | Purpose
-----|--------
Edge Optimizer | Determines whether Edge Optimizer can be applied to a request.
Edge Optimizer – Instantiate Configuration | Instantiates or activates the Edge Optimizer configuration associated with a site.

###Edge Optimizer
**Purpose:** Determines whether Edge Optimizer can be applied to a request.

If this feature has been enabled, then the following criteria must also be met before the request will be processed by Edge Optimizer:

- The requested content must use an edge CNAME URL.
- The edge CNAME referenced in the URL must correspond to a site whose configuration has been activated in a rule.

This feature requires the ADN platform and the Edge Optimizer feature.

Value|Result
-|-
Enabled|Indicates that the request is eligible for Edge Optimizer processing.
Disabled|Restores the default behavior. The default behavior is to deliver content over the ADN platform without any additional processing.

**Default Behavior:** Disabled
 

###Edge Optimizer - Instantiate Configuration
**Purpose:** Instantiates or activates the Edge Optimizer configuration associated with a site.

This feature requires the ADN platform and the Edge Optimizer feature.

Key information:

- Instantiation of a site configuration is required before requests to the corresponding edge CNAME can be processed by Edge Optimizer.
- This instantiation only needs to be performed a single time per site configuration. A site configuration that has been instantiated will remain in that state until the Edge Optimizer – Instantiate Configuration feature that references it is removed from the rule.
- The instantiation of a site configuration does not mean that all requests to the corresponding edge CNAME will automatically be processed by Edge Optimizer. The Edge Optimizer feature determines whether an individual request will be processed.

If the desired site does not appear in the list, then you should edit its configuration and verify that the Active option has been marked.

**Default Behavior:** Site configurations are inactive by default.
--->

## <a name="origin"></a>Origem

Esses recursos são criados para controlar como a CDN se comunica com um servidor de origem.

Nome | Finalidade
-----|--------
Máximo de Solicitações Keep-Alive | Define o número máximo de solicitações para uma conexão Keep-Alive antes de ser fechado.
Cabeçalhos Especiais de Proxy | Define o conjunto de cabeçalhos de solicitação CDN específicos que será encaminhado de um servidor de borda para um servidor de origem.


###<a name="maximum-keep-alive-requests"></a>Máximo de Solicitações Keep-Alive
**Finalidade:** define o número máximo de solicitações para uma conexão Keep-Alive antes de ser fechado.

Definir o número máximo de solicitações com um valor baixo é altamente desaconselhável e pode resultar em degradação de desempenho.

Informações de chave:

- Especifique esse valor como um inteiro.
- Não inclua vírgulas nem pontos no valor especificado.

**Valor padrão:** 10.000 solicitações

###<a name="proxy-special-headers"></a>Cabeçalhos Especiais de Proxy
**Finalidade:** define o conjunto de [cabeçalhos de solicitação CDN específicos]() que será encaminhado de um servidor de borda para um servidor de origem.

Informações de chave:

- Cada cabeçalho de solicitação específico da CDN definido neste recurso será encaminhado para um servidor de origem.
- Impeça que um cabeçalho de solicitação específico da CDN seja encaminhado a um servidor de origem removendo-o da lista.

**Comportamento padrão:** todos os [cabeçalhos de solicitação específicos da CDN]() serão encaminhados ao servidor de origem.

## <a name="specialty"></a>Especialidade

Esses recursos fornecem funcionalidade avançada que deve ser usada somente por usuários avançados.

Nome | Finalidade
-----|--------
Métodos HTTP Armazenáveis em Cache | Determina o conjunto de métodos HTTP adicionais que podem ser armazenados em cache em nossa rede.
Tamanho do Corpo da Solicitação Armazenável em Cache | Define o limite para determinar se uma resposta POST pode ser armazenada em cache.

###<a name="cacheable-http-methods"></a>Métodos HTTP Armazenáveis em Cache
**Finalidade:** determina o conjunto de métodos HTTP adicionais que podem ser armazenados em cache em nossa rede.

Informações de chave:

- Esse recurso pressupõe que respostas GET devam ser sempre armazenadas em cache. Como resultado, o método HTTP GET não deve ser incluído ao ser definido esse recurso.
- Esse recurso dá suporte apenas ao método HTTP POST. Habilite o cache de resposta POST definindo esse recurso para: POST 
- Por padrão, somente as solicitações cujo corpo for menor do que 14 Kb serão armazenadas. Use o Recurso de Tamanho de Corpo de Solicitação Armazenável em Cache para definir o tamanho máximo do corpo da solicitação.

**Comportamento padrão:** apenas respostas GET serão armazenadas em cache.

###<a name="cacheable-request-body-size"></a>Tamanho do Corpo da Solicitação Armazenável em Cache

**Finalidade:** define o limite para determinar se uma resposta POST pode ser armazenada em cache.

Esse limite é determinado pela especificação de um tamanho máximo de corpo de solicitação. Solicitações que contiverem um corpo de solicitação maior não serão armazenadas.

Informações de chave:

- Esse recurso só é aplicável quando respostas POST são qualificadas para o cache. Use o Recurso de Métodos HTTP Armazenáveis em Cache para habilitar o cache de solicitação POST.
- O corpo da solicitação é levado em consideração para:
    - x-www-form-urlencoded values
    - Garantindo um cache-key exclusivo
- Definir um tamanho de corpo de solicitação máximo grande pode afetar o desempenho de entrega de dados.
    - **Valor Recomendado:** 14 Kb
    - **Valor Mínimo:** 1 Kb

**Comportamento Padrão:** 14 Kb
 
## <a name="url"></a>URL

Esses recursos permitem que uma solicitação seja redirecionada ou reescrita em uma URL diferente.

Nome | Finalidade
-----|--------
Seguir Redirecionamentos | Determina se as solicitações podem ser redirecionadas para o nome do host definido no cabeçalho Local retornado por um servidor de origem do cliente.
Redirecionamento de URL | Redireciona as solicitações por meio do cabeçalho Local.
Regravação de URL  | Regrava a URL da solicitação.

###<a name="follow-redirects"></a>Seguir Redirecionamentos
**Finalidade:** determina se as solicitações podem ser redirecionadas para o nome do host definido no cabeçalho Local retornado por um servidor de origem do cliente.

Informações de chave:

- As solicitações só podem ser redirecionadas para CNAMEs de borda que correspondam à mesma plataforma.

Valor|Result
-|-
Habilitado|As solicitações podem ser redirecionadas.
Desabilitado|As solicitações não serão redirecionadas.

**Comportamento padrão:** desabilitado.
###<a name="url-redirect"></a>Redirecionamento de URL
**Finalidade:** redireciona as solicitações por meio do cabeçalho Local.

A configuração deste recurso requer a definição das seguintes opções:

Opção|Descrição
-|-
Código|Selecione o código de resposta que será retornado ao solicitante.
Origem e Padrão| Essas configurações definem um padrão de URI de solicitação que identifica o tipo de solicitações que podem ser redirecionadas. Serão redirecionadas somente as solicitações cuja URL atender aos seguintes critérios: <br/> <br/> **Origem (ou ponto de acesso de conteúdo):** selecione um caminho relativo que identifica um servidor de origem. Esta é a seção "/XXXX/" e o nome do ponto de extremidade. <br/> **Origem (padrão):** deve ser definido um padrão que identifica solicitações pelo caminho relativo. Este padrão de expressão regular deve definir um caminho que é iniciado diretamente após o ponto de acesso a conteúdo selecionado anteriormente (veja acima). <br/> – Certifique-se de que os critérios de URI da solicitação (ou seja, Origem e padrão) definidos anteriormente não entram em conflito com as condições de correspondência definidas para esse recurso. <br/> – Especificar um padrão; se você usar um valor em branco como o padrão, todas as cadeias de caracteres serão correspondidas.
Destino| Defina a URL para a qual as solicitações acima serão redirecionadas. <br/> Construa dinamicamente esta URL usando: <br/> - Um padrão de expressão regular <br/>- Variáveis HTTP <br/> Substitua os valores capturados no padrão de origem no padrão de destino usando $_n_, em que _n_ identifica um valor pela ordem na qual ele foi capturado. Por exemplo, $1 representa o primeiro valor capturado no padrão de origem, enquanto $2 representa o segundo valor. <br/> 
É altamente recomendável usar uma URL absoluta. O uso de uma URL relativa pode redirecionar URLs da CDN para um caminho inválido.

**Cenário de exemplo**

Neste exemplo, demonstraremos como redirecionar uma URL de CNAME de borda que aponta para esta URL base da CDN: http://marketing.azureedge.net/brochures

As solicitações serão redirecionadas para esta URL CNAME de borda base: http://cdn.mydomain.com/resources

Esse redirecionamento de URL pode ser obtido por meio da seguinte configuração:![](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Pontos principais:**

- O recurso de Redirecionamento de URL define as URLs de solicitação que serão redirecionadas. Como resultado, as condições de correspondência adicionais não são necessárias. Embora a condição de correspondência tenha sido definida como "Sempre", somente as solicitações que apontarem para a pasta "folhetos" na origem do cliente de "marketing" serão redirecionadas. 
- Todas as solicitações correspondentes serão redirecionadas para a URL CNAME de borda definida na opção Destination. 
    - Cenário de exemplo 1: 
        - Solicitação de exemplo (URL da CDN): http://marketing.azureedge.net/brochures/widgets.pdf 
        - URL da solicitação (depois do redirecionamento): http://cdn.mydomain.com/resources/widgets.pdf  
    - Cenário de exemplo 2: 
        - Exemplo de solicitação (URL de CNAME de borda): http://marketing.mydomain.com/brochures/widgets.pdf 
        - URL da solicitação (depois do redirecionamento): http://cdn.mydomain.com/resources/widgets.pdf Cenário de exemplo
    - Cenário de exemplo 3: 
        - Solicitação de Exemplo (URL de CNAME de borda): http://brochures.mydomain.com/campaignA/final/productC.ppt 
        - URL da solicitação (depois do redirecionamento): http://cdn.mydomain.com/resources/campaignA/final/productC.ppt  
- A variável de Esquema de Solicitação (%{scheme}) foi utilizada na opção de Destino. Isso garante que o esquema da solicitação permaneça inalterado após o redirecionamento.
- Os segmentos da URL que foram capturados da solicitação são acrescentados à nova URL por meio de "$1".
 
###<a name="url-rewrite"></a>Regravação de URL
**Finalidade:** regrava a URL da solicitação.

Informações de chave:

- A configuração deste recurso requer a definição das seguintes opções:

Opção|Descrição
-|-
 Origem e Padrão | Essas configurações definem um padrão de URI de solicitação que identifica o tipo de solicitações que podem ser reconfiguradas. Serão regravadas somente as solicitações cuja URL atender aos seguintes critérios: <br/>     - **Origem (ou ponto de acesso de conteúdo):** selecione um caminho relativo que identifique um servidor de origem. Esta é a seção "/XXXX/" e o nome do ponto de extremidade. <br/> - **Origem (padrão):** deve ser definido um padrão que identifica solicitações pelo caminho relativo. Este padrão de expressão regular deve definir um caminho que é iniciado diretamente após o ponto de acesso a conteúdo selecionado anteriormente (veja acima). <br/> Certifique-se de que os critérios de URI da solicitação (ou seja, Origem e padrão) definidos anteriormente não entram em conflito com as condições de correspondência definidas para esse recurso. Especificar um padrão; se você usar um valor em branco como o padrão, todas as cadeias de caracteres serão correspondidas. 
 Destino  |Defina a URL relativa na qual as solicitações acima serão regravadas: <br/>    1. Selecionando um ponto de acesso ao conteúdo que identifica um servidor de origem. <br/>    2. Definindo um caminho relativo usando: <br/>        - Um padrão de expressão regular <br/>        - Variáveis HTTP <br/> <br/> Substitua os valores capturados no padrão de origem no padrão de destino usando $_n_, em que _n_ identifica um valor pela ordem na qual ele foi capturado. Por exemplo, $1 representa o primeiro valor capturado no padrão de origem, enquanto $2 representa o segundo valor. 
 Esse recurso permite que nossos servidores de borda regravem a URL sem executar um redirecionamento tradicional. Isso significa que o solicitante receberá o mesmo código de resposta que obteria se a URL regravada tivesse sido solicitada.

**Cenário de exemplo 1**

Neste exemplo, demonstraremos como redirecionar uma URL de CNAME de borda que aponta para esta URL base da CDN: http://marketing.azureedge.net/brochures/

As solicitações serão redirecionadas para esta URL CNAME de borda base: http://MyOrigin.azureedge.net/resources/

Esse redirecionamento de URL pode ser obtido por meio da seguinte configuração:![](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Cenário de exemplo 2**

Neste exemplo, demonstraremos como redirecionar uma borda CNAME URL de MAIÚSCULAS para minúsculas usando expressões regulares.

Esse redirecionamento de URL pode ser obtido por meio da seguinte configuração:![](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)


**Pontos principais:**

- O recurso de Reescrita de URL define as URLs de solicitação que serão reescritas. Como resultado, as condições de correspondência adicionais não são necessárias. Embora a condição de correspondência tenha sido definida como "Sempre", somente as solicitações que apontarem para a pasta "folhetos" na origem do cliente de "marketing" serão reescritas.

- Os segmentos da URL que foram capturados da solicitação são acrescentados à nova URL por meio de "$1".



###<a name="compatibility"></a>Compatibilidade

Esse recurso inclui critérios de correspondência que devem ser atendidos para que ele possa ser aplicado a uma solicitação. Para evitar a configuração de critérios de correspondência conflitantes, esse recurso é incompatível com as seguintes condições de correspondência:

- Número AS
- Origem CDN
- Endereço IP do Cliente
- Origem do Cliente
- Esquema de Solicitação
- Diretório do Caminho da URL
- Extensão do Caminho da URL
- Nome do Arquivo do Caminho da URL
- Literal do Caminho da URL
- Regex do Caminho da URL
- Curinga do Caminho da URL
- Literal da Consulta da URL
- Parâmetro da Consulta da URL
- Regex da consulta da URL
- Curinga da consulta da URL


## <a name="next-steps"></a>Próximas etapas
* [Referência do Mecanismo de Regras](cdn-rules-engine-reference.md)
* [Expressões Condicionais do Mecanismo de Regras](cdn-rules-engine-reference-conditional-expressions.md)
* [Condições de Correspondência do Mecanismo de Regras](cdn-rules-engine-reference-match-conditions.md)
* [Substituindo o comportamento HTTP padrão usando o mecanismo de regras](cdn-rules-engine.md)
* [Visão geral da CDN do Azure](cdn-overview.md)
