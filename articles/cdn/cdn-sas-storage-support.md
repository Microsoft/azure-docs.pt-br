---
title: Usando a CDN do Azure com SAS | Microsoft Docs
description: CDN do Azure suporta o uso de Assinatura de Acesso Compartilhado (SAS) para garantir acesso limitado aos contêineres de armazenamento privado.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: magattus
ms.openlocfilehash: 999bffe9650f3d2f2a04dba728a9aa41fa46a6b0
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593402"
---
# <a name="using-azure-cdn-with-sas"></a>Usando a CDN do Azure com SAS

Ao configurar uma conta de armazenamento para a Rede de Distribuição de Conteúdo do Azure (CDN) para usar em cache o conteúdo, por padrão qualquer pessoa que souber as URLs para os contêineres de armazenamento pode acessar os arquivos que você carregou. Para proteger os arquivos na sua conta de armazenamento, você pode definir o acesso de seus contêineres de armazenamento de pública para privada. No entanto, se você fizer isso, ninguém poderá acessar seus arquivos. 

Se você deseja conceder acesso limitado aos contêineres de armazenamento privado, você pode usar o recurso de Assinatura de Acesso Compartilhado (SAS) de sua conta de armazenamento do Azure. Uma SAS é um URI que concede direitos de acesso restrito aos seus recursos do armazenamento do Azure sem expor sua chave de conta. Você pode fornecer uma SAS para clientes aos quais não confia sua chave de conta de armazenamento, mas para os quais deseja delegar acesso a determinados recursos da conta de armazenamento. Ao distribuir um URI de assinatura de acesso compartilhado para esses clientes, você concede a eles acesso a um recurso por um período especificado.
 
Com uma SAS, você pode definir vários parâmetros de acesso para um blob, como horários de início e vencimento, permissões (leitura/gravação) e intervalos de IP. Este artigo descreve como usar a SAS em conjunto com a CDN do Azure. Para obter mais informações sobre a SAS, incluindo como criá-la e suas opções de parâmetro, consulte [Usando assinaturas de acesso compartilhado (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Configurando a CDN do Azure para trabalhar com SAS no armazenamento
As três opções a seguir são recomendadas para usar a SAS com a CDN do Azure. Todas as opções pressupõem que você já tenha criado uma SAS que está em funcionamento (consulte os pré-requisitos). 
 
### <a name="prerequisites"></a>pré-requisitos
Para começar, crie uma conta de armazenamento e gere uma SAS para seu ativo. Você pode gerar dois tipos de assinaturas de acesso compartilhado: uma SAS de serviço ou uma SAS de conta. Para obter mais informações, confira [Tipos de assinatura de acesso compartilhado](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures).

Depois de gerar um token SAS, você pode acessar o arquivo de armazenamento de blob, acrescentando `?sv=<SAS token>` em sua URL. Esta URL tem o seguinte formato: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Por exemplo:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Para obter mais informações sobre a configuração dos parâmetros, consulte [Considerações sobre parâmetros de SAS](#sas-parameter-considerations) e [Parâmetros de assinatura de acesso compartilhado](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#shared-access-signature-parameters).

![Configurações de SAS da CDN](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>Opção 1: Usar a SAS com passagem para o armazenamento de BLOBs da CDN do Azure

Esta opção é a mais simples e usa apenas um token de SAS, que é passado da CDN do Azure para o servidor de origem.
 
1. Selecione um ponto de extremidade, selecione em **Regras de cache** e, em seguida, selecione **Armazenar em cache todas as URLs exclusivas** na lista **Cache da cadeia de caracteres de consulta**.

    ![Regras de cache da CDN](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Depois de configurar SAS em sua conta de armazenamento, você deverá usar o token de SAS com as URLs de ponto de extremidade de CDN e de servidor de origem para acessar o arquivo. 
   
   A URL de ponto de extremidade de CDN tem o seguinte formato: `https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Por exemplo:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Ajuste a duração do cache usando regras de cache ou adicionando cabeçalhos `Cache-Control` no servidor de origem. Como a CDN do Azure trata o token de SAS como uma cadeia de caracteres de consulta simples, como uma prática recomendada, você deve definir a uma duração do cache que expira na mesma hora que a SAS ou antes. Caso contrário, se um arquivo for armazenado em cache por mais tempo que a SAS está ativa, o arquivo poderá ficar acessível no servidor de origem da CDN do Azure depois de decorrido o tempo de expiração da SAS. Se esta situação ocorrer e você desejar tornar o arquivo armazenado em cache inacessível, execute uma operação de limpeza no arquivo para limpá-lo do cache. Para obter informações sobre como definir a duração do cache na CDN do Azure, consulte [Controlar o comportamento de CDN do Azure com regras de cache](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>Opção 2: Token de SAS CDN oculto usando uma regra de regravação
 
Essa opção só está disponível para perfis da **CDN Premium do Azure da Verizon**. Com essa opção, você pode proteger o armazenamento de blob no servidor de origem. Talvez você queira usar esta opção se não precisar de restrições de acesso específicas para o arquivo, mas desejar impedir que usuários acessem a origem do armazenamento diretamente para melhorar o tempo de descarregamento da CDN do Azure. O token SAS, que é desconhecido para o usuário, é necessário para que qualquer pessoa que acesse os arquivos no contêiner especificado do servidor de origem. No entanto, devido à regra de Regravação de URL, o token SAS não é necessário no ponto de extremidade de CDN.
 
1. Use o [mecanismo de regras](cdn-rules-engine.md) para criar uma regra de regravação de URL. A propagação das novas regras pode demorar até 4 horas.

   ![Botão Gerenciar CDN](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![Botão do mecanismo de regras da CDN](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   A seguinte regra de Regravação de URL de exemplo usa um padrão de expressão regular com um grupo de captura e um ponto de extremidade chamado *sasstoragedemo*:
   
   Origem:   
   `(container1\/.*)`
   
   Destino:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![Regra de regravação da URL da CDN – esquerda](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![Regra de regravação da URL da CDN - direita](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

2. Depois que a nova regra se torna ativa, qualquer pessoa pode acessar arquivos no contêiner especificado no ponto de extremidade de CDN, independentemente de a pessoa usar ou não um token SAS na URL. Este é o formato: `https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Por exemplo:   
   `https://sasstoragedemo.azureedge.net/container1/demo.jpg`
       

3. Ajuste a duração do cache usando regras de cache ou adicionando cabeçalhos `Cache-Control` no servidor de origem. Como a CDN do Azure trata o token de SAS como uma cadeia de caracteres de consulta simples, como uma prática recomendada, você deve definir a uma duração do cache que expira na mesma hora que a SAS ou antes. Caso contrário, se um arquivo for armazenado em cache por mais tempo que a SAS está ativa, o arquivo poderá ficar acessível no servidor de origem da CDN do Azure depois de decorrido o tempo de expiração da SAS. Se esta situação ocorrer e você desejar tornar o arquivo armazenado em cache inacessível, execute uma operação de limpeza no arquivo para limpá-lo do cache. Para obter informações sobre como definir a duração do cache na CDN do Azure, consulte [Controlar o comportamento de CDN do Azure com regras de cache](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Opção 3: Usando a autenticação de token de segurança da CDN com uma regra de regravação

Para usar a autenticação de token de segurança da CDN do Azure, você precisa ter um perfil da **CDN Premium do Azure da Verizon**. Essa opção é a mais segura e personalizável. O acesso do cliente é baseado nos parâmetros de segurança que você define no token de segurança da CDN. Depois que você criar e configurar o token de segurança, ele será necessário em todas as URLs de ponto de extremidade de CDN. No entanto, devido à regra de Regravação de URL, o token SAS não é necessário no ponto de extremidade de CDN. No entanto, se o token de SAS depois se tornar inválida, a CDN do Azure não conseguirá mais revalidar o conteúdo do servidor de origem.

1. [Crie um token de segurança da CDN do Azure](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) e ative-o usando o mecanismo de regras para o ponto de extremidade e o caminho da CDN na qual os usuários podem acessar o arquivo.

   Uma URL de ponto de extremidade de token de segurança tem o seguinte formato:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Por exemplo:   
   ```
   https://sasstoragedemo.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   As opções de parâmetro para uma autenticação de token de segurança são diferentes das opções de parâmetro para um token da SAS. Se optar por usar uma hora de expiração quando criar um token de segurança, você deverá defini-la com o mesmo valor que a hora de expiração do token de SAS. Isso garante que a hora de expiração seja previsível. 
 
2. Use o [mecanismo de regras](cdn-rules-engine.md) para criar uma regra de regravação de URL para permitir acesso com token SAS a todos os blobs no contêiner. A propagação das novas regras pode demorar até 4 horas.

   A seguinte regra de Regravação de URL de exemplo usa um padrão de expressão regular com um grupo de captura e um ponto de extremidade chamado *sasstoragedemo*:
   
   Origem:   
   `(container1\/.*)`
   
   Destino:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![Regra de regravação da URL da CDN – esquerda](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![Regra de regravação da URL da CDN - direita](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

3. Se renovar a SAS, atualize a regra de regravação da URL para usar com o novo token de SAS. 

## <a name="sas-parameter-considerations"></a>Considerações sobre parâmetros da SAS

Como parâmetros da SAS não são visíveis para a CDN do Azure, a CDN do Azure não pode alterar seu comportamento de entrega com base neles. As restrições de parâmetro definidas se aplicam apenas a solicitações que a CDN do Azure faz para o servidor de origem, não para solicitações do cliente à CDN do Azure. Essa distinção é importante de considerar quando você define os parâmetros de SAS. Se esses recursos avançados forem necessários e você estiver usando a [Opção 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule), defina as restrições apropriadas no token de segurança da CDN do Azure.

| Nome do parâmetro da SAS | DESCRIÇÃO |
| --- | --- |
| Início | A hora em que a CDN do Azure pode começar a acessar o arquivo de blob. Devido à distorção do relógio (quando um sinal de relógio chega em momentos diferentes para diferentes componentes), se quiser disponibilizar o ativo imediatamente, escolha um horário 15 minutos antes. |
| End | A hora após a qual a CDN do Azure não poderá mais acessar o arquivo de blob. Arquivos armazenados em cache anteriormente na CDN do Azure ainda são acessíveis. Para controlar a hora de expiração do arquivo, defina a hora de expiração apropriada no token de segurança da CDN do Azure ou limpe o ativo. |
| Endereços IP permitidos | Opcional. Se estiver usando a **CDN do Azure da Verizon**, você poderá definir esse parâmetro para os intervalos definidos em [Azure CDN from Verizon Edge Server IP Ranges](/azure/cdn/cdn-pop-list-api) (CDN do Azure de Intervalos de IP do Servidor de Borda Verizon). Se estiver usando a **CDN do Azure da Akamai**, você não poderá definir o parâmetro de intervalos de IP porque os endereços de IP não são estáticos.|
| Protocolos permitidos | Os protocolos permitidos para uma solicitação feita com a conta de SAS. A configuração HTTPS é recomendada.|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre SAS, veja os seguintes artigos:
- [Como usar SAS (assinaturas de acesso compartilhado)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Assinatura de Acesso Compartilhado, Parte 2: Criar e usar um SAS com o Armazenamento de Blobs](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)

Para saber mais sobre como configurar a autenticação de token, confira [Protegendo ativos da Rede de Distribuição de Conteúdo do Azure com autenticação de token](https://docs.microsoft.com/azure/cdn/cdn-token-auth).
