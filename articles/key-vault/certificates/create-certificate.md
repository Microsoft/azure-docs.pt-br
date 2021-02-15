---
title: Métodos de criação de certificado
description: Saiba mais sobre opções diferentes para criar ou importar um certificado de Key Vault no Azure Key Vault. Há várias maneiras de criar um certificado de Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: a9545c040809331a5556b11f6cc7536931e2d421
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289567"
---
# <a name="certificate-creation-methods"></a>Métodos de criação de certificado

 Um certificado do Key Vault (KV) pode ser criado ou importado em um cofre de chaves. Quando um certificado do KV é criado, a chave privada é criada no cofre de chaves e nunca exposta ao proprietário do certificado. Abaixo são apresentadas maneiras de criar um certificado no Key Vault:  

-   **Criar um certificado autoassinado:** isso criará um par de chaves públicas-privadas e associá-lo a um certificado. O certificado será assinado por sua própria chave.  

-    **Criar um novo certificado manualmente:** isso criará um par de chaves públicas-privadas e gerará uma solicitação de assinatura de certificado x.509. A solicitação de assinatura pode ser assinada por sua autoridade de registro ou a autoridade de certificação. O certificado X.509 assinado pode ser mesclado com o par de chaves pendente para concluir o certificado do KV no Key Vault. Embora esse método requeira mais etapas, ele oferece maior segurança porque a chave privada é criada no Key Vault e restrita a ele. Isso é explicado no diagrama a seguir.  

![Criar um certificado com sua própria autoridade de certificação](../media/certificate-authority-1.png)  

As descrições a seguir correspondem às etapas indicadas em verde no diagrama anterior.

1. No diagrama acima, o aplicativo está criando um certificado que internamente começa com a criação de uma chave no cofre de chaves.
2. O Key Vault devolve ao seu aplicativo uma Solicitação de Assinatura de Certificado (CSR)
3. O aplicativo passa a CSR à sua AC escolhida.
4. A AC escolhida responde com um certificado X509.
5. Seu aplicativo conclui a criação do novo certificado com uma fusão do Certificado X509 da autoridade de certificação.

-   **Criar um certificado com um provedor de emissor conhecido:** esse método requer que você faça uma tarefa única de criação de um objeto de emissor. Depois que um objeto de emissor é criado no cofre de chaves, seu nome pode ser referenciado na política do certificado do KV. Uma solicitação para criar um certificado do KV criará um par de chaves no cofre e se comunicará com o serviço do provedor de emissor usando as informações no objeto de emissor referenciado para obter um certificado x509. O certificado x509 é recuperado do serviço de emissor e é mesclado com o par de chaves para concluir a criação do certificado do KV.  

![Criar um certificado com uma autoridade de certificação de uma parceria do Key Vault](../media/certificate-authority-2.png)  

As descrições a seguir correspondem às etapas indicadas em verde no diagrama anterior.

1. No diagrama acima, o aplicativo está criando um certificado que internamente começa com a criação de uma chave no cofre de chaves.
2. Key Vault envia uma solicitação de certificado TLS/SSL para a autoridade de certificação.
3. O aplicativo faz a sondagem, em um processo de loop e espera, no Key Vault para a conclusão do certificado. A criação do certificado é concluída quando o Key Vault recebe a resposta da autoridade de certificação com o certificado x509.
4. A autoridade de certificação responde à solicitação de certificado TLS/SSL de Key Vault com um certificado TLS/SSL X. 509.
5. A nova criação de certificado é concluída com a fusão do certificado TLS/SSL X. 509 para a autoridade de certificação.

## <a name="asynchronous-process"></a>Processamento assíncrono
A criação do certificado do KV é um processo assíncrono. Esta operação criará uma solicitação de certificado do KV e retornará um código de status http 202 (Aceito). O status da solicitação pode ser acompanhado consultando o objeto pendente criado por essa operação. O URI completo do objeto pendente é retornado no cabeçalho de LOCALIZAÇÃO.  

Quando uma solicitação para criar um certificado do KV for concluída, o status do objeto pendente será alterado de "em andamento" para "concluído" e uma nova versão do certificado do KV será criada. Ela se tornará a versão atual.  

## <a name="first-creation"></a>Primeira criação
 Quando um certificado do KV é criado pela primeira vez, uma chave endereçável e um segredo também são criados com o mesmo nome que o certificado. Se o nome já estiver em uso, a operação falhará com um código de status http 409 (conflito).
A chave endereçável e o segredo obtêm seus atributos dos atributos do certificado do KV. A chave endereçável e o segredo criados dessa maneira são marcados como chaves e segredos gerenciados, cujo tempo de vida é gerenciado pelo Key Vault. As chaves e os segredos são somente leitura. Observação: se um certificado do KV expirar ou for desabilitado, a chave e o segredo correspondentes se tornarão inoperantes.  

 Se essa for a primeira operação para criar um certificado do KV, uma política será necessária.  Uma política também pode ser fornecida com sucessivas operações de criação para substituir o recurso de política. Se uma política não for fornecida, o recurso de política do serviço é usado para criar uma versão posterior do certificado KV. Observe que enquanto uma solicitação para criar a próxima versão estiver em andamento, o certificado KV atual e a chave endereçável e o segredo correspondentes, permanecem inalterados.  

## <a name="self-issued-certificate"></a>Certificado auto-emitido
 Para criar um certificado autoemitido, defina o nome do emissor como "Auto" na política do certificado, conforme mostrado no snippet a seguir da política de certificado.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Se o nome do emissor não for especificado, o nome do emissor é definido como "Desconhecido". Quando o emissor é "Desconhecido", o proprietário do certificado terá que obter manualmente o certificado x509 do emissor de sua escolha e, em seguida, mesclar o certificado público x509 com o objeto pendente do certificado do Key Vault para concluir a criação do certificado.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Provedores de autoridade de certificação parceiros
A criação de certificado pode ser concluída manualmente ou usando um emissor próprio (“Auto”). O Key Vault também possui parceria com determinados provedores de emissor para simplificar a criação de certificados. Os seguintes tipos de certificados podem ser solicitados ao cofre de chaves com esses provedores de emissor parceiros.  

|Provedor|Tipo de certificado|Configuração  
|--------------|----------------------|------------------|  
|DigiCert|O Key Vault oferece certificados SSL OV ou EV com DigiCert| [Guia de integração](./how-to-integrate-certificate-authority.md)
|GlobalSign|O Key Vault oferece certificados SSL OV ou EV com GlobalSign| [Guia de integração](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

 Um emissor do certificado é uma entidade representada no Azure Key Vault (KV) como um recurso CertificateIssuer. Ele é usado para fornecer informações sobre a origem de um certificado KV; nome do emissor, provedor, credenciais e outros detalhes administrativos.

Observe que quando um pedido é feito com o provedor do emissor, ele pode aceitar ou substituir as extensões de certificado x509 e o período de validade do certificado com base no tipo de certificado.  

 Autorização: Requer a permissão certificados/criar.

## <a name="see-also"></a>Consulte Também

 - [Monitorar e gerenciar a criação de certificados](create-certificate-scenarios.md)