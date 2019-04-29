---
title: Introdução aos certificados com Key Vault
description: Os cenários a seguir descrevem vários dos principais usos do serviço de gerenciamento de certificado do Key Vault incluindo as etapas adicionais necessárias para criar seu primeiro certificado em seu Key Vault.
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 20c05bddddce4c7748e29551fe78d3e5609b2fa5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60641034"
---
# <a name="get-started-with-key-vault-certificates"></a>Introdução aos certificados com Key Vault
Os cenários a seguir descrevem vários dos principais usos do serviço de gerenciamento de certificado do Key Vault incluindo as etapas adicionais necessárias para criar seu primeiro certificado em seu Key Vault.

A seguir descreve-se como:
- Criar seu primeiro certificado do Key Vault
- Criar um certificado com uma autoridade de certificação parceira do Key Vault
- Criar um certificado com uma autoridade de certificação que não é parceira do Key Vault
- Importar um certificado

## <a name="certificates-are-complex-objects"></a>Os certificados são objetos complexos
Certificados são compostos de três recursos inter-relacionados vinculados como um certificado do Key Vault; metadados do certificado, uma chave e um segredo.


![Certificados são complexos](media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Criar seu primeiro certificado do Key Vault  
 Antes que um certificado possa ser criado em Key Vault (KV), as etapas 1 e 2 de pré-requisitos devem ser concluídas com êxito e um Key Vault deve existir para esse usuário / organização.  

**Etapa 1** - Provedores de Autoridade de Certificação (CA)  
-   Entrada como o administrador de TI, administradores de PKI ou qualquer pessoa gerenciando contas com autoridades de certificação, para uma determinada empresa (por exemplo, Contoso) é um pré-requisito para usar certificados de Key Vault.  
    As CAs a seguir são os provedores parceiros atuais do Key Vault:  
    -   DigiCert - O Key Vault oferece certificados SSL OV ou EV com DigiCert.  
    -   GlobalSign - O Key Vault oferece certificados SSL OV com GlobalSign  
    -   WoSign - O Key Vault oferece certificados SSL OV ou SSL EV com WoSign com base na configuração do cliente em sua conta WoSign no portal WoSign.  

**Etapa 2** - Um administrador de conta para um provedor de autoridade de certificação cria as credenciais a serem usadas pelo Key Vault para registrar, renovar e usar certificados SSL por meio do Key Vault.

**Etapa 3** - Um administrador de Contoso, junto com um funcionário da Contoso (usuário do Key Vault) que possui certificados, dependendo da autoridade de certificação, pode obter um certificado com o administrador ou diretamente a partir da conta com a autoridade de certificação.  

- Inicie uma operação de inclusão de credenciais em um cofre de chaves, definindo [um recurso de emissor de certificado](/rest/api/keyvault/setcertificateissuer/setcertificateissuer). Um emissor do certificado é uma entidade representada no Azure Key Vault (KV) como um recurso CertificateIssuer. Ele é usado para fornecer informações sobre a origem de um certificado KV; nome do emissor, provedor, credenciais e outros detalhes administrativos.
  - Ex.: MyDigiCertIssuer  
    -   Provedor  
    -   Credenciais – Credenciais da conta da autoridade de certificação. Cada autoridade de certificação tem seus próprios dados específicos.  

    Para obter mais informações sobre como criar contas com provedores de autoridade de certificação, consulte a postagem relacionada no [blog do Key Vault](https://aka.ms/kvcertsblog).  

**Etapa 3.1** – Configurar [contatos do certificado](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts) para notificações. Este é o contato para o usuário do Key Vault. O Key Vault não impõe essa etapa.  

Observação - Esse processo, por meio de etapa 3.1, é uma operação única.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Criar um certificado com uma autoridade de certificação parceira do Key Vault

![Criar um certificado com uma autoridade de certificação de uma parceria do Key Vault](media/certificate-authority-2.png)

**Etapa 4** - As descrições a seguir correspondem às etapas numeradas em verde no diagrama anterior.  
  (1) - No diagrama acima, o aplicativo está criando um certificado que internamente começa com a criação de uma chave em seu cofre de chaves.  
  (2) - O Key Vault envia uma Solicitação de Certificado SSL para a autoridade de certificação.  
  (3) - Seu aplicativo faz a apuração, em um processo de loop e espera, para seu Key Vault para a conclusão do certificado. A criação do certificado é concluída quando o Key Vault recebe a resposta da autoridade de certificação com o certificado x509.  
  (4) - A autoridade de certificação responde à Solicitação de Certificado SSL do Key Vault com um Certificado SSL X509.  
  (5) - A criação do novo certificado é concluído com a fusão do Certificado X509 para a autoridade de certificação.  

  Usuário do Key Vault – cria um certificado com a especificação de uma política

  -   Repita conforme necessário  
  -   Restrições de política  
      -   Propriedades X509  
      -   Propriedades da chave  
      -   Referência do provedor - > por exemplo, MyDigiCertIssure  
      -   Informações sobre a renovação - > por exemplo, 90 dias antes de expirar  

  - Um processo de criação de certificado é geralmente um processo assíncrono e envolve a sondagem do seu Key Vault para saber o estado da operação de criação de certificado.  
[Operação de obtenção de certificado](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   Status: concluída, falha com informações de erro ou, cancelada  
      -   Devido ao atraso da criação, uma operação de cancelamento pode começar. O cancelamento pode ou não ser eficaz.  

## <a name="import-a-certificate"></a>Importar um certificado  
 Como alternativa – um certificado pode ser importado para o Key Vault – PFX ou PEM.  

 Para obter mais informações sobre o formato PEM, consulte a seção de certificados [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md).  

 Importar o certificado – requer um PEM ou PFX no disco e possui uma chave privada. 
-   Você deve especificar: o nome do Key Vault e o nome do certificado (política é opcional)

-   Os arquivos PEM / PFX contém atributos que o Key Vault pode analisar e usar para popular a política de certificação. Se uma política de certificação já tiver sido especificada, o Key Vault tentará fazer a correspondência de dados do arquivo PFX / PEM.  

-   Depois que a importação for concluída, as operações posteriores usarão a nova política (novas versões).  

-   Se não houver nenhuma outra operação, a primeira coisa que o Key Vault é enviar uma notificação de expiração. 

-   Além disso, o usuário pode editar a política, que está funcionando no momento da importação, mas contém padrões onde nenhuma informação foi especificada durante a importação. Ex.: sem informações do emissor  

### <a name="formats-of-import-we-support"></a>Formatos de importação, damos suporte a
Oferecemos suporte o seguinte tipo de importação para o formato de arquivo PEM. Um único certificado PEM codificado, juntamente com um PKCS n º 8 codificados, chave não criptografada que tem as seguintes

---BEGIN CERTIFICATE------END CERTIFICATE--

---BEGIN PRIVATE KEY------END PRIVATE KEY--

Na mesclagem de certificado damos suporte a formatos PEM com base 2. Você também pode mesclar um único certificado codificado de PKCS n º 8 um codificado por base64 ou arquivo P7B. ---BEGIN CERTIFICATE------END CERTIFICATE--

Atualmente não damos suporte a chaves EC no formato PEM.

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Criar um certificado com uma autoridade de certificação não parceira do Key Vault  
 Esse método permite trabalhar com outras autoridades de certificação diferentes dos provedores parceiros do Key Vault, o que significa que sua organização pode trabalhar com uma autoridade de certificação de sua escolha.  

![Criar um certificado com sua própria autoridade de certificação](media/certificate-authority-1.png)  

 As descrições da etapa a seguir correspondem às etapas indicadas em verde no diagrama anterior.  

  (1) - No diagrama acima, o aplicativo está criando um certificado que internamente começa com a criação de uma chave em seu cofre de chaves.  

  (2) - O Key Vault devolve ao seu aplicativo uma Solicitação de Assinatura de Certificado (CSR).  

  (3) - O aplicativo passa a CSR à sua autoridade de certificação escolhida.  

  (4) - A autoridade de certificação escolhida responde com um certificado X509.  

  (5) - Seu aplicativo conclui a criação do novo certificado com uma fusão do Certificado X509 da autoridade de certificação.

## <a name="see-also"></a>Veja também

- [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
