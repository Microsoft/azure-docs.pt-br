---
title: Autorizar o acesso com uma assinatura de acesso compartilhado nos hubs de eventos do Azure
description: Este artigo fornece informações sobre como autorizar o acesso aos recursos dos hubs de eventos do Azure usando SAS (assinaturas de acesso compartilhado).
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 6a2d7385f82864e8d378055333377fb9c3f73c19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85323128"
---
# <a name="authorizing-access-to-event-hubs-resources-using-shared-access-signatures"></a>Autorizando o acesso aos recursos de hubs de eventos usando assinaturas de acesso compartilhado
Uma SAS (assinatura de acesso compartilhado) fornece uma maneira de conceder acesso limitado a recursos em seu namespace de hubs de eventos. A SAS protege o acesso a recursos de hubs de eventos com base em regras de autorização. Essas regras são configuradas em um namespace ou em uma entidade (Hub de eventos ou tópico). Este artigo fornece uma visão geral do modelo SAS e revisa as práticas recomendadas de SAS.

## <a name="what-are-shared-access-signatures"></a>O que são assinaturas de acesso compartilhado?
Uma SAS (assinatura de acesso compartilhado) fornece acesso delegado a recursos de hubs de eventos com base em regras de autorização. Uma regra de autorização tem nome, está associada a direitos específicos e executa um par de chaves de criptografia. Você usa o nome e a chave da regra por meio dos clientes dos hubs de eventos ou em seu próprio código para gerar tokens SAS. Um cliente pode passar o token para os hubs de eventos para provar a autorização para a operação solicitada.

A SAS é um mecanismo de autorização baseado em declarações usando tokens simples. Usando SAS, chaves nunca são passadas na conexão. As chaves são usadas para assinar criptograficamente informações que posteriormente podem ser verificadas pelo serviço. A SAS pode ser usada como esquema de nome de usuário e senha no qual o cliente está em posse imediata de um nome de regra de autorização e uma chave correspondente. A SAS pode ser usada de forma semelhante a um modelo de segurança federado, em que o cliente recebe um token de acesso assinado e limitado de um serviço de token de segurança sem nunca receber a posse da chave de assinatura.

> [!NOTE]
> Os hubs de eventos do Azure dão suporte à autorização para recursos de hubs de eventos usando o Azure Active Directory (Azure AD). A autorização de usuários ou aplicativos usando o token 2,0 do OAuth retornado pelo Azure AD fornece segurança superior e facilidade de uso sobre SAS (assinaturas de acesso compartilhado). Com o Azure AD, não é necessário armazenar os tokens no código e arriscar as vulnerabilidades de segurança potenciais.
>
> A Microsoft recomenda usar o Azure AD com seus aplicativos de hubs de eventos do Azure quando possível. Para obter mais informações, consulte [autorizar o acesso ao recurso de hubs de eventos do Azure usando Azure Active Directory](authorize-access-azure-active-directory.md).

> [!IMPORTANT]
> Tokens SAS (assinaturas de acesso compartilhado) são essenciais para proteger seus recursos. Embora forneça granularidade, a SAS concede aos clientes acesso aos seus recursos de hubs de eventos. Eles não devem ser compartilhados publicamente. Ao compartilhar, se necessário para os motivos da solução de problemas, considere usar uma versão reduzida de quaisquer arquivos de log ou excluir os tokens SAS (se houver) dos arquivos de log e verifique se as capturas de tela não contêm as informações de SAS.

## <a name="shared-access-authorization-policies"></a>Políticas de autorização de acesso compartilhado
Cada namespace de hubs de eventos e cada entidade de hubs de eventos (uma instância de Hub de eventos ou um tópico Kafka) tem uma política de autorização de acesso compartilhado composta por regras. A política no nível de namespace se aplica a todas as entidades dentro do namespace, independentemente de suas configurações de política individuais.
Para cada regra de política de autorização, você toma decisões quanto a três informações: nome, escopo e direitos. O nome é um nome exclusivo nesse escopo. O escopo é o URI do recurso em questão. Para um namespace de hubs de eventos, o escopo é o FQDN (nome de domínio totalmente qualificado), como `https://<yournamespace>.servicebus.windows.net/` .

Os direitos fornecidos pela regra de política podem ser uma combinação de:
- **Enviar** – dá o direito de enviar mensagens para a entidade
- **Escutar** – dá o direito de escutar ou receber a entidade
- **Gerenciar** – dá o direito de gerenciar a topologia do namespace, incluindo a criação e a exclusão de entidades

> [!NOTE]
> O direito **gerenciar** inclui os direitos **Enviar** e **escutar** .

Uma política de namespace ou de entidade pode conter até 12 regras de autorização de acesso compartilhado, fornecendo espaço para os três conjuntos de regras, cada um cobrindo os direitos básicos e a combinação de enviar e escutar. Esse limite sublinha que o repositório de políticas SAS não pretende ser um armazenamento de conta de serviço ou usuário. Se seu aplicativo precisar conceder acesso a recursos de hubs de eventos com base em identidades de usuário ou de serviço, ele deverá implementar um serviço de token de segurança que emite tokens SAS após uma verificação de autenticação e acesso.

Uma regra de autorização recebe uma **chave primária** e uma **chave secundária**. Essas chaves são chaves criptograficamente fortes. Não perca esses itens nem os vazamentos. Eles estarão sempre disponíveis no portal do Azure. Você pode usar qualquer uma das chaves geradas e pode gerá-las novamente a qualquer momento. Se você gerar novamente ou alterar uma chave na política, todos os tokens emitidos anteriormente com base na chave tornam-se inválidos instantaneamente. No entanto, conexões contínuas criadas com base em tais tokens continuarão funcionando até o token expirar.

Quando você cria um namespace de hubs de eventos, uma regra de política chamada **RootManageSharedAccessKey** é criada automaticamente para o namespace. Esta política tem permissões de **Gerenciamento** para o namespace inteiro. É recomendável que você trate essa regra como uma conta raiz administrativa e não a use em seu aplicativo. Você pode criar regras de política adicionais na guia **Configurar** para o namespace no portal, por meio do PowerShell ou CLI do Azure.

## <a name="best-practices-when-using-sas"></a>Práticas recomendadas ao usar SAS
Ao usar assinaturas de acesso compartilhado nos seus aplicativos, você precisará estar ciente de dois riscos possíveis:

- Se uma SAS for vazada, ela poderá ser usada por qualquer pessoa que a obtenha, o que pode comprometer seus recursos de hubs de eventos.
- Se uma SAS fornecida a um aplicativo cliente expirar e o aplicativo não puder recuperar uma nova SAS do seu serviço, a funcionalidade do aplicativo poderá ser prejudicada.

As recomendações a seguir para usar assinaturas de acesso compartilhado podem ajudar a atenuar esses riscos:

- **Fazer com que os clientes renovem automaticamente a SAS, se necessário**: os clientes devem renovar a SAS bem antes da expiração, para permitir tempo para novas tentativas se o serviço que fornece a SAS não estiver disponível. Se a sua SAS for destinada a ser usada para um pequeno número de operações imediatas e de curta duração que devem ser concluídas dentro do período de expiração, pode ser desnecessária, pois a SAS não deve ser renovada. No entanto, se você tiver um cliente que está sempre fazendo solicitações via SAS, surge a possibilidade de expiração. A principal consideração é equilibrar a necessidade de que a SAS seja de curta duração (como mencionado anteriormente) com a necessidade de garantir que o cliente esteja solicitando a renovação cedo o suficiente (para evitar a interrupção devido à expiração da SAS antes de uma renovação bem-sucedida).
- **Tenha cuidado com a hora de início da SAS**: se você definir a hora de início de SAS como **agora**, devido à distorção do relógio (diferenças na hora atual de acordo com computadores diferentes), as falhas poderão ser observadas intermitentemente pelos primeiros minutos. Em geral, defina a hora de início para pelo menos 15 minutos no passado. Ou então, não o defina, o que o tornará válido imediatamente em todos os casos. O mesmo geralmente se aplica ao tempo de expiração também. Lembre-se de que você pode fazer um observador de até 15 minutos de distorção de relógio em qualquer direção em qualquer solicitação. 
- **Seja específico com o recurso a ser acessado**: uma prática recomendada de segurança é fornecer ao usuário os privilégios mínimos necessários. Se um usuário precisar apenas de acesso de leitura a uma única entidade, conceda-lhe acesso de leitura a essa única entidade, e não acesso de leitura/gravação/exclusão a todas as entidades. Também ajudará a diminuir o dano se uma SAS for comprometida porque a SAS tem menos poder nas mãos de um invasor.
- **Nem sempre use SAS**: às vezes, os riscos associados a uma operação específica em relação aos seus hubs de eventos superam os benefícios da SAS. Para essas operações, crie um serviço de camada intermediária que grave em seus hubs de eventos após a validação, a autenticação e a auditoria da regra de negócio.
- **Sempre usar HTTPS**: sempre use HTTPS para criar ou distribuir uma SAS. Se uma SAS for passada por HTTP e interceptada, um invasor executando um anexo Man-in-the-Middle será capaz de ler a SAS e, em seguida, usá-la da mesma forma que o usuário pretendido pode ter, potencialmente comprometendo dados confidenciais ou permitindo corrupção de dados pelo usuário mal-intencionado.

## <a name="conclusion"></a>Conclusão
As assinaturas de acesso de compartilhamento são úteis para fornecer permissões limitadas aos recursos de hubs de eventos para seus clientes. Eles são parte vital do modelo de segurança para qualquer aplicativo usando os hubs de eventos do Azure. Se você seguir as práticas recomendadas listadas neste artigo, poderá usar SAS para fornecer maior flexibilidade de acesso aos seus recursos, sem comprometer a segurança do seu aplicativo.

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos relacionados: 

- [Autenticar solicitações para hubs de eventos do Azure por meio de um aplicativo usando Azure Active Directory](authenticate-application.md)
- [Autenticar uma identidade gerenciada com Azure Active Directory para acessar recursos de hubs de eventos](authenticate-managed-identity.md)
- [Autenticar solicitações para hubs de eventos do Azure usando assinaturas de acesso compartilhado](authenticate-shared-access-signature.md)
- [Autorizar o acesso aos recursos de hubs de eventos usando Azure Active Directory](authorize-access-azure-active-directory.md)


