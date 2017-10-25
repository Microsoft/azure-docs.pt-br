---
title: "CLI do Azure Service Fabric - composição sfctl | Microsoft Docs"
description: "Descreve os comandos de composição do sfctl da CLI do Service Fabric."
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: 3010c298cf227c761288365e3663ffe3fb67d863
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-compose"></a>composição sfctl
Criar, excluir e gerenciar implantações do Docker Compose.

## <a name="commands"></a>Comandos

|Command|Descrição|
| --- | --- |
|    create| Implante um aplicativo do Service Fabric de um arquivo de Composição.|
|    list  | Obtém a lista de implantação de composição criada no cluster do Service Fabric.|
|   remove| Exclui uma implantação de composição existente do Service Fabric do cluster.|
|   status| Obtém informações sobre um aplicativo de implantação do Service Fabric.|
|atualizar       | Começa a atualização de uma implantação de composição no cluster do Service Fabric.|
|    upgrade-status| Obtém detalhes para a atualização mais recente realizada na implantação da composição do Service Fabric.|


## <a name="sfctl-compose-create"></a>criar composição sfctl
Cria uma implantação de composição do Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --caminho de arquivo [obrigatório]| Caminho para o arquivo de destino do Docker Compose.|
 |   --deployment-name [Obrigatório]| O nome da implantação.|
|    -senha criptografada             | Em vez de solicitar uma senha de registro de contêiner, use uma frase secreta já criptografada.|
|    --has-pass                   | Solicita uma senha para o registro de contêiner.|
|    --timeout -t                 | Tempo limite do servidor em segundos.  Padrão: 60.|
 |   --user                       | Nome de usuário para conectar o registro de contêiner.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug                 | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h               | Mostrar esta mensagem de ajuda e sair.|
| --output -o             | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query                 | Cadeia de caracteres de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose               | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-compose-list"></a>lista de composição sfctl
Obtém a lista de implantação de composição criada no cluster do Service Fabric.

Obtém o status sobre as implantações de composição que foram criadas ou estão sendo criadas no cluster do Service Fabric. A resposta inclui o nome, o status e outros detalhes sobre a implantação de composição. Se as implantações não couberem em uma página, uma página de resultados retornará junto com um token de continuação, que poderá ser usado para acessar a próxima página.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --continuation-token| O parâmetro do token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio será incluso na resposta da API quando os resultados do sistema não couberem em uma única resposta.      Quando esse valor for passado para a próxima chamada de API, a API retornará o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL.|
| --max-results    | O número máximo de resultados a serem retornados como parte das consultas paginadas.      Esse parâmetro define o limite superior no número de resultados retornados.      Se não couberem na mensagem, de acordo com as restrições de tamanho máximo de mensagem definidas na configuração, os resultados retornados podem ser inferiores aos resultados máximos especificados. Se esse parâmetro for zero, ou não for especificado, a consulta paginada incluirá o máximo de resultados possível na mensagem de retorno.|
| --timeout -t     | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug          | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h        | Mostrar esta mensagem de ajuda e sair.|
| --output -o      | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query          | Cadeia de caracteres de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose        | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-compose-remove"></a>remoção de composição sfctl
Exclui uma implantação de composição existente do Service Fabric do cluster.

Exclui uma implantação de composição existente do Service Fabric. 

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome de implantação [obrigatório]| A identidade da implantação. Normalmente, é o nome completo do aplicativo sem o esquema de URI "fabric:".|
| --timeout -t            | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug                 | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h               | Mostrar esta mensagem de ajuda e sair.|
| --output -o             | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query                 | Cadeia de caracteres de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose               | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-compose-status"></a>status de composição sfctl
Obtém informações sobre um aplicativo de implantação do Service Fabric.

Retorna o statu de implantação de composição criado ou que está sendo criado no cluster do Service Fabric, e cujo nome corresponde àquele especificado como o parâmetro. A resposta inclui o nome, status e outros detalhes sobre o aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome de implantação [obrigatório]| A identidade da implantação. |
| --timeout -t            | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug                 | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h               | Mostrar esta mensagem de ajuda e sair.|
| --output -o             | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query                 | Cadeia de caracteres de consulta JMESPath. Para saber mais e obter exemplos, consulte http://jmespath.org/.|
| --verbose               | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-compose-upgrade"></a>upgrade de composição sfctl
Começa a atualização de uma implantação de composição no cluster do Service Fabric.

Valida os parâmetros de upgrade fornecidos e inicia a atualização da implantação.

### <a name="arguments"></a>Argumentos
|Argumento|Descrição|
| --- | --- |
|    --caminho do arquivo [obrigatório]| Caminho para o arquivo de destino do Docker Compose.|
|    --deployment-name [Obrigatório]| O nome da implantação.|
|    --falha-svc-tipo-integridade-mapa| Dicionário codificado JSON que descreve a política de integridade usada para avaliar a integridade dos serviços.|
|    -senha criptografada             | Em vez de solicitar uma senha de registro de contêiner, use uma frase secreta já criptografada.|
 |   --ação de falha             | Os valores possíveis incluem: 'Inválido', 'Reversão', 'Manual'.|
|    -força-reinicialização              | Força reinicialização.|
 |   --has-pass                   | Solicita uma senha para o registro de contêiner.|
|    --nova tentativa de verificação da integridade         | Tempo limite de repetição de verificação de integridade medido em milissegundos.|
|    --estabilidade de verificação de integridade        | Verificação da integridade de duração estável, medida em milissegundos.|
|    --espera de verificação de integridade          | Duração da espera da verificação da integridade, medida em milissegundos.|
|    --verificação do conjunto de réplicas          | Upgrade do tempo limite de verificação do conjunto de réplicas medido em segundos.|
|    --mapa de integridade do tipo de svc        | Lista codificada JSON de objetos que descrevem as políticas de integridade usadas para avaliar a integridade de diferentes tipos de serviços.|
|    --timeout -t                 | Tempo limite do servidor em segundos.  Padrão: 60.|
|    --aplicativo prejudicial              | A porcentagem máxima permitida de aplicativos prejudiciais antes de relatar um erro.        Por exemplo, para permitir que 10% dos aplicativos sejam problemáticos, esse valor seria 10. A porcentagem representa o máximo tolerado de aplicativos que podem estar não íntegros antes de o cluster ser considerado com erro. Se a porcentagem for respeitada, mas houver pelo menos um aplicativo não íntegro, a integridade será avaliada como ‘Aviso’. Este percentual é calculado pela divisão do número de aplicativos não íntegros pelo número total de instâncias de aplicativos no cluster.|
|    -tempo limite ao domínio de upgrade     | Tempo limite do domínio de upgrade medido em milissegundos.|
|    --tipo de upgrade               | Padrão: sem interrupção.|
|    --modo de upgrade               | Os valores possíveis incluem: 'Inválido', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitorado'.  Padrão: UnmonitoredAuto.|
|    -tempo limite de upgrade            | Tempo limite do upgrade medido em milissegundos.|
|    --user                       | Nome de usuário para conectar o registro de contêiner.|
|    --aviso-como-erro           | Os avisos são tratados com a mesma gravidade dos erros.|

### <a name="global-arguments"></a>Argumentos globais
 |Argumento|Descrição|
| --- | --- |
|   --debug                      | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
|    --help -h                    | Mostrar esta mensagem de ajuda e sair.|
 |   --output -o                  | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.
                                   Padrão: json.|
 |   --query                      | Cadeia de caracteres de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
 |   --verbose                    | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="next-steps"></a>Próximas etapas
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).