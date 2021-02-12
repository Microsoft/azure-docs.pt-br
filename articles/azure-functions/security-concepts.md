---
title: Proteger o Azure Functions
description: Saiba mais sobre como tornar o código de função em execução no modo seguro do Azure contra ataques comuns.
ms.date: 4/13/2020
ms.topic: conceptual
ms.openlocfilehash: ee54ff8c1efaee00999888891e6de255060aa416
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491317"
---
# <a name="securing-azure-functions"></a>Proteger o Azure Functions

De muitas maneiras, planejar o desenvolvimento, a implantação e a operação seguros de funções sem servidor é muito parecido com o de qualquer aplicativo Web ou hospedado na nuvem. O [Serviço de Aplicativo do Azure](../app-service/index.yml) fornece a infraestrutura de hospedagem para seus aplicativos de funções. Este artigo fornece estratégias de segurança para executar o código de função e como o Serviço de Aplicativo pode ajudá-lo a proteger suas funções. 

[!INCLUDE [app-service-security-intro](../../includes/app-service-security-intro.md)]

Para obter um conjunto de recomendações de segurança que seguem a [Referência de Segurança do Azure](../security/benchmarks/overview.md), consulte a [Linha de Base de Segurança do Azure para Azure Functions](security-baseline.md).

## <a name="secure-operation"></a>Operação segura 

Esta seção orienta você sobre como configurar e executar seu aplicativo de funções da maneira mais segura possível. 

### <a name="security-center"></a>Central de Segurança

A Central de Segurança integra-se ao seu aplicativo de funções no portal. Ele fornece, gratuitamente, uma avaliação rápida de possíveis vulnerabilidades de segurança relacionadas à configuração. Os aplicativos de funções em execução em um plano dedicado também podem usar os recursos de segurança em tempo real da Central de Segurança para obter um custo adicional. Para saber mais, confira [Proteger seus aplicativos Web e APIs do Azure App Service](../security-center/defender-for-app-service-introduction.md). 

### <a name="log-and-monitor"></a>Registrar e monitorar

Uma maneira de detectar ataques é por meio da atividade de monitoramento de atividades e da análise de log. As funções integram-se ao Application Insights para coletar dados de log, de desempenho e de erro para seu aplicativo de funções. O Application Insights detecta automaticamente anomalias de desempenho e inclui ferramentas de análise avançadas para ajudar a diagnosticar problemas e entender como suas funções são usadas. Para saber mais, consulte [Monitorar Azure Functions](functions-monitoring.md).

As funções também se integram aos logs do Azure Monitor para permitir que você consolide logs do aplicativo de funções com eventos do sistema para uma análise mais fácil. Você pode usar as configurações de diagnóstico para definir a exportação de streaming de logs e métricas de plataforma das suas funções para o destino de sua escolha, como um espaço de trabalho do Log Analytics. Para saber mais, confira [Monitoramento do Azure Functions com Logs do Azure Monitor](functions-monitor-log-analytics.md). 

Para detecção de ameaças de nível empresarial e automação de resposta, transmita seus logs e eventos para um espaço de trabalho do Log Analytics. Em seguida, você pode conectar o Azure Sentinel a este espaço de trabalho. Para saber mais, confira [O que é o Azure Sentinel?](../sentinel/overview.md).  

Para obter mais recomendações de segurança para a observação, consulte a [Linha de base de segurança do Azure para Azure Functions](security-baseline.md#logging-and-monitoring). 

### <a name="require-https"></a>Solicitar HTTPS

Por padrão, os clientes podem se conectar a pontos de extremidade de função usando HTTP ou HTTPS. Você deve redirecionar HTTP para HTTPs, pois HTTPS usa o protocolo SSL/TLS para fornecer uma conexão segura, que é criptografada e autenticada. Para saber como, consulte [Impor HTTPS](../app-service/configure-ssl-bindings.md#enforce-https).

Quando você precisar de HTTPS, também deverá exigir a versão mais recente do TLS. Para saber como, consulte [Impor versões de TLS](../app-service/configure-ssl-bindings.md#enforce-tls-versions).

Para obter mais informações, consulte [Conexões seguras (TSL) ](../app-service/overview-security.md#https-and-certificates).

### <a name="function-access-keys"></a>Chaves de acesso de função

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

#### <a name="system-key"></a>Chave do sistema 

Extensões específicas podem exigir uma chave gerenciada pelo sistema para acessar pontos de extremidade de webhook. As chaves do sistema são projetadas para pontos de extremidade de função específicos da extensão que são chamados por componentes internos. Por exemplo, o [gatilho de Grade de Eventos](functions-bindings-event-grid-trigger.md) requer que a assinatura use uma chave do sistema ao chamar o ponto de extremidade do gatilho. O Durable Functions também usa as chaves do sistema para chamar [APIs de extensão de tarefa durável](durable/durable-functions-http-api.md). 

O escopo das chaves do sistema é determinado pela extensão, mas geralmente se aplica a todo o aplicativo de funções. As chaves do sistema só podem ser criadas por extensões específicas e você não pode definir seus valores explicitamente. Assim como outras chaves, você pode gerar um novo valor para a chave do portal ou usar as APIs de chave.

#### <a name="keys-comparison"></a>Comparação de chaves

A tabela a seguir compara os usos para vários tipos de chaves de acesso:

| Ação                                        | Escopo                    | Chaves válidas         |
|-----------------------------------------------|--------------------------|--------------------|
| Executar uma função                            | Função específica        | Função           |
| Executar uma função                            | Qualquer função             | Função ou host   |
| Chamar um ponto de extremidade de administrador                        | Aplicativo de funções             | Host (somente mestre) |
| Chamar APIs de extensão de tarefa durável              | Aplicativo de funções<sup>1</sup> | Sistema<sup>2</sup> |
| Chamar um webhook específico da extensão (interno) | Aplicativo de funções<sup>1</sup> | sistema<sup>2</sup> |

<sup>1</sup>Escopo determinado pela extensão.  
<sup>2</sup>Nomes específicos definidos pela extensão.

Para saber mais sobre as chaves de acesso, consulte o [Artigo de associação de gatilho HTTP](functions-bindings-http-webhook-trigger.md#obtaining-keys).


#### <a name="secret-repositories"></a>Repositórios secretos

Por padrão, as chaves são armazenadas em um contêiner de armazenamento de BLOBs na conta fornecida pela `AzureWebJobsStorage` configuração. Você pode usar configurações de aplicativo específicas para substituir esse comportamento e armazenar as chaves em um local diferente.

|Location  |Configuração | Valor | Descrição  |
|---------|---------|---------|---------|
|Conta de armazenamento diferente     |  `AzureWebJobsSecretStorageSas`       | `<BLOB_SAS_URL` | Armazena chaves no armazenamento de blobs de uma segunda conta de armazenamento, com base na URL da SAS fornecida. As chaves são criptografadas antes de serem armazenadas usando um segredo exclusivo para seu aplicativo de funções. |
|Sistema de arquivos   | `AzureWebJobsSecretStorageType`   |  `files`       | As chaves são mantidas no sistema de arquivos, criptografadas antes do armazenamento usando um segredo exclusivo para seu aplicativo de funções. |
|Cofre de Chave do Azure  | `AzureWebJobsSecretStorageType`<br/>`AzureWebJobsSecretStorageKeyVaultName` | `keyvault`<br/>`<VAULT_NAME>` | O cofre deve ter uma política de acesso correspondente à identidade gerenciada atribuída pelo sistema do recurso de hospedagem. A política de acesso deve conceder à identidade as seguintes permissões de segredo: `Get` , `Set` , `List` e `Delete` . <br/>Ao ser executado localmente, a identidade do desenvolvedor é usada e as configurações devem estar na [local.settings.jsno arquivo](functions-run-local.md#local-settings-file). | 
|Segredos do Kubernetes  |`AzureWebJobsSecretStorageType`<br/>`AzureWebJobsKubernetesSecretName` (opcional) | `kubernetes`<br/>`<SECRETS_RESOURCE>` | Com suporte apenas ao executar o tempo de execução do Functions em kubernetes. Quando `AzureWebJobsKubernetesSecretName` não estiver definido, o repositório será considerado somente leitura. Nesse caso, os valores devem ser gerados antes da implantação. O Azure Functions Core Tools gera os valores automaticamente ao implantar no kubernetes.|

### <a name="authenticationauthorization"></a>Autenticação/autorização

Embora as teclas de função possam fornecer alguma mitigação para acesso indesejado, a única maneira de realmente proteger os pontos de extremidade de função é implementar a autenticação positiva de clientes que acessam suas funções. Em seguida, você pode tomar decisões de autorização com base na identidade.  

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

### <a name="permissions"></a>Permissões

Assim como ocorre com qualquer aplicativo ou serviço, a meta é executar seu aplicativo de funções com as permissões mais baixas possíveis. 

#### <a name="user-management-permissions"></a>Permissões de gerenciamento do usuário

O Functions dá suporte ao Azure [RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md)interno do Azure. As funções do Azure com suporte das funções são [colaborador](../role-based-access-control/built-in-roles.md#contributor), [proprietário](../role-based-access-control/built-in-roles.md#owner)e [leitor](../role-based-access-control/built-in-roles.md#owner). 

As permissões são efetivas no nível do aplicativo de funções. A função de Colaborador é necessária para executar a maioria das tarefas de nível de aplicativo de função. Somente a função de Proprietário pode excluir um aplicativo de funções. 

#### <a name="organize-functions-by-privilege"></a>Organizar funções por privilégio 

As cadeias de conexão e outras credenciais armazenadas nas configurações do aplicativo dão a todas as funções no aplicativo de funções o mesmo conjunto de permissões no recurso associado. Considere minimizar o número de funções com acesso a credenciais específicas movendo funções que não usam essas credenciais para um aplicativo de funções separado. Você sempre pode usar técnicas como [encadeamento de funções](/learn/modules/chain-azure-functions-data-using-bindings/) para passar dados entre funções em diferentes aplicativos de funções.  

#### <a name="managed-identities"></a>Identidades gerenciadas

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

Para obter mais informações, consulte [Como usar identidades gerenciadas para o Serviço de Aplicativo e o Azure Functions](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json).

#### <a name="restrict-cors-access"></a>Restringir o acesso CORS

O [CORS (compartilhamento de recursos entre origens)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) é uma maneira de permitir que aplicativos Web em execução em outro domínio façam solicitações para seus pontos de extremidade de gatilho HTTP. O Serviço de Aplicativo fornece suporte interno para a entrega dos cabeçalhos CORS necessários em solicitações HTTP. As regras de CORS são definidas em um nível de aplicativo de funções.  

Embora seja tentador usar um curinga que permita que todos os sites acessem seu ponto de extremidade. Porém, isso anula a finalidade do CORS, que é para ajudar a evitar ataques de script entre sites. Em vez disso, adicione uma entrada CORS separada para o domínio de cada aplicativo Web que deve acessar seu ponto de extremidade. 

### <a name="managing-secrets"></a>Gerenciamento de segredos 

Para se conectar aos vários serviços e recursos que precisam executar seu código, os aplicativos de funções precisam ser capazes de acessar segredos, como cadeias de conexão e chaves de serviço. Esta seção descreve como armazenar segredos exigidos por suas funções.

Nunca armazene segredos no seu código de função. 

#### <a name="application-settings"></a>Configurações do aplicativo

Por padrão, armazene cadeias de conexão e segredos usados pelo seu aplicativo de funções e associações como configurações de aplicativo. Isso disponibiliza essas credenciais tanto para o código de função quanto para as várias associações usadas pela função. O nome da configuração do aplicativo (chave) é usado para recuperar o valor real, que é o segredo. 

Por exemplo, cada aplicativo de funções requer uma conta de armazenamento associada, que é usada pelo tempo de execução. Por padrão, a conexão com essa conta de armazenamento é armazenada em uma configuração de aplicativo chamada `AzureWebJobsStorage`.

As configurações do aplicativo e cadeias de conexão são armazenadas e criptografadas no Azure. Elas são descriptografadas somente antes de serem injetadas na memória do processo do aplicativo quando o aplicativo é iniciado. As chaves de criptografia são giradas regularmente. Se você preferir gerenciar o armazenamento seguro de seus segredos, a configuração do aplicativo deverá, em vez disso, fazer referência a Azure Key Vault. 

Você também pode criptografar as configurações por padrão na local.settings.jsno arquivo ao desenvolver funções em seu computador local. Para saber mais, consulte a `IsEncrypted` propriedade no [arquivo de configurações local](functions-run-local.md#local-settings-file).  

#### <a name="key-vault-references"></a>Referências de Key Vault

Embora as configurações do aplicativo sejam suficientes para a maioria das funções, talvez você queira compartilhar os mesmos segredos em vários serviços. Nesse caso, o armazenamento redundante de segredos resulta em mais vulnerabilidades potenciais. Uma abordagem mais segura é para um serviço de armazenamento de segredo central e usar referências a esse serviço em vez dos próprios segredos.      

[Azure Key Vault](../key-vault/general/overview.md) é um serviço que fornece gerenciamento centralizado de segredos, com controle total sobre políticas de acesso e histórico de auditoria. Você pode usar uma referência de Key Vault no lugar de uma cadeia de conexão ou chave em suas configurações de aplicativo. Para saber mais, consulte [Usar as referências do Key Vault para o Serviço de Aplicativo e o Azure Functions](../app-service/app-service-key-vault-references.md?toc=%2fazure%2fazure-functions%2ftoc.json).

### <a name="set-usage-quotas"></a>Definir cotas de uso

Considere definir uma cota de uso em funções em execução em um plano de consumo. Quando você define um limite diário de GB por segundo na soma total de execução de funções em seu aplicativo de funções, a execução é interrompida quando o limite é atingido. Isso poderia ajudar a reduzir o código mal-intencionado que executa suas funções. Para saber como estimar o consumo para suas funções, consulte [Estimar os custos do plano de consumo](functions-consumption-costs.md). 

### <a name="data-validation"></a>Validação de dados

Os gatilhos e as associações usadas pelas suas funções não fornecem validação de dados adicional. Seu código deve validar todos os dados recebidos de um gatilho ou uma associação de entrada. Caso um serviço de upstream esteja comprometido, você não quer que entradas não validadas fluam por meio de suas funções. Por exemplo, se sua função armazena dados de uma fila de Armazenamento do Azure em um banco de dados relacional, você deve validar os dados e parametrizar seus comandos para evitar ataques de injeção de SQL. 

Não presuma que os dados que chegam à sua função já tenham sido validados ou corrigidos. Também é uma boa ideia verificar se os dados que estão sendo gravados em associações de saída são válidos. 

### <a name="handle-errors"></a>Tratar erros

Embora pareça básico, é importante escrever um bom tratamento de erro em suas funções. Erros não tratados se acumulam para o host e são manipulados pelo tempo de execução. Associações diferentes tratam do processamento de erros de forma diferente. Para saber mais, consulte [Tratamento de erro do Azure Functions](functions-bindings-error-pages.md).

### <a name="disable-remote-debugging"></a>Desabilitar depuração remota

Verifique se a depuração remota está desabilitada, exceto quando você estiver depurando ativamente suas funções. Você pode desabilitar a depuração remota na guia **Configurações gerais** do seu aplicativo de funções de **Configuração** no portal. 

### <a name="restrict-cors-access"></a>Restringir o acesso CORS

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

Não use caracteres curinga em sua lista de origens permitidas. Em vez disso, liste os domínios específicos dos quais você espera obter solicitações.

### <a name="store-data-encrypted"></a>Armazenar dados criptografados

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

## <a name="secure-deployment"></a>Implantação segura

Agregar ferramentas do Azure Functions para uma integração facilitam a publicação do código do projeto de função local no Azure. É importante entender como a implantação funciona ao considerar a segurança de uma topologia de Azure Functions.   

### <a name="deployment-credentials"></a>Credenciais de implantação

As implantações do Serviço de Aplicativo exigem um conjunto de credenciais de implantação. Essas credenciais de implantação são usadas para proteger suas implantações de aplicativo de funções. As credenciais de implantação são gerenciadas pela plataforma do Serviço de Aplicativo e são criptografadas em repouso. 

Há dois tipos de credenciais de implantação:

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

Neste momento, não há suporte para Key Vault para credenciais de implantação. Para obter mais informações sobre como configurar as credenciais de implantação, consulte [Configurar as credenciais de implantação para o Serviço de Aplicativo do Azure](../app-service/deploy-configure-credentials.md).

### <a name="disable-ftp"></a>Desabilitar FTP

Por padrão, cada aplicativo de funções tem um ponto de extremidade de FTP habilitado. O ponto de extremidade de FTP é acessado usando credenciais de implantação. 

O FTP não é recomendado para implantação de código de função. As implantações de FTP são manuais e exigem a sincronização de gatilhos. Para saber mais, confira [Implantação de FTP](functions-deployment-technologies.md#ftp). 

Quando não estiver planejando usar o FTP, você deverá desabilitá-lo no portal. Se você optar por usar o FTP, deverá [impor FTPS](../app-service/deploy-ftp.md#enforce-ftps).

### <a name="secure-the-scm-endpoint"></a>Proteger o ponto de extremidade do scm

Cada aplicativo de funções tem um ponto de extremidade de serviço `scm` correspondente que é usado pelo serviço ferramentas avançadas (Kudu) em implantações e outras [extensões de site](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) do Serviço de Aplicativo. O ponto de extremidade SCM para um aplicativo de funções é sempre uma URL no formulário `https://<FUNCTION_APP_NAME.scm.azurewebsites.net>`. Ao usar o isolamento de rede para proteger suas funções, você também deve considerar esse ponto de extremidade. 

Tendo um ponto de extremidade SCM separado, você pode controlar as implantações e outras funcionalidades de ferramentas avançadas para o aplicativo de funções que estão isoladas ou em execução em uma rede virtual. O ponto de extremidade do SCM dá suporte à autenticação básica (usando credenciais de implantação) e ao logon único com suas credenciais de portal do Azure. Para saber mais, confira [Acessar o serviço Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service). 

### <a name="continuous-security-validation"></a>Avaliação contínua de segurança

Como a segurança precisa ser considerada a cada etapa do processo de desenvolvimento, faz sentido implementar também validações de segurança em um ambiente de implantação contínua. Isso às vezes é chamado de DevSecOps. Ao usar o Azure DevOps para seu pipeline de implantação, você integrará a validação ao processo de implantação. Para obter mais informações, consulte [Saiba como adicionar validação de segurança contínua ao seu pipeline de CI/CD](/azure/devops/migrate/security-validation-cicd-pipeline).  

## <a name="network-security"></a>Segurança de rede

Restringir o acesso à rede para seu aplicativo de funções permite controlar quem pode acessar seus pontos de extremidade de funções. As funções aproveitam a infraestrutura do Serviço de Aplicativo para permitir que suas funções acessem recursos sem usar endereços roteáveis pela Internet ou para restringir o acesso à Internet a um ponto de extremidade de função. Para saber mais sobre essas opções de rede, confira [Opções de rede do Azure Functions](functions-networking-options.md).

### <a name="set-access-restrictions"></a>Definir restrições de acesso

As restrições de acesso permitem definir listas de regras de permissão/negação para controlar o tráfego para seu aplicativo. As regras são avaliadas em ordem de prioridade. Se não houver nenhuma regra definida, seu aplicativo aceitará o tráfego de qualquer endereço. Para saber mais, confira [Restrições de acesso ao serviço do Serviço de Aplicativo do Azure #](../app-service/app-service-ip-restrictions.md?toc=%2fazure%2fazure-functions%2ftoc.json).

### <a name="private-site-access"></a>Acesso a site particular

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

### <a name="deploy-your-function-app-in-isolation"></a>Implante o aplicativo de funções isoladamente

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

### <a name="use-a-gateway-service"></a>Use um serviço do gateway

Os serviços de gateway, como [Gateway de Aplicativo do Azure](../application-gateway/overview.md) e [Azure Front Door](../frontdoor/front-door-overview.md), permitem configurar um WAF (firewall do aplicativo Web). As regras WAF são usadas para monitorar ou bloquear ataques detectados, que fornecem uma camada extra de proteção para suas funções. Para configurar um WAF, seu aplicativo de funções precisa estar em execução em um ASE ou usando pontos de extremidade privados (versão prévia). Para saber mais, confira [Usar pontos de extremidade privados](../app-service/networking/private-endpoint.md).    

## <a name="next-steps"></a>Próximas etapas

+ [Linha de base de segurança do Azure para o Azure Functions](security-baseline.md)
+ [Diagnóstico do Azure Functions](functions-diagnostics.md)
