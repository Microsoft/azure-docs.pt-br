---
title: O que há de novo no cache do Azure para Redis
description: Atualizações recentes para o cache do Azure para Redis
author: yegu-ms
ms.service: cache
ms.topic: reference
ms.date: 09/28/2020
ms.author: yegu
ms.openlocfilehash: b30e83b89b25e6400b8c7e0419406631fa1edcd0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91492371"
---
# <a name="whats-new-in-azure-cache-for-redis"></a>O que há de novo no cache do Azure para Redis

## <a name="azure-tls-certificate-change"></a>Alteração do certificado TLS do Azure

A Microsoft está atualizando os serviços do Azure para usar certificados de servidor TLS de um conjunto diferente de CAs (autoridades de certificação). Essa alteração é distribuída em fases de 13 de agosto de 2020 para 26 de outubro de 2020 (estimado). O Azure está fazendo essa alteração porque [os certificados de autoridade de certificação atuais não estão em conformidade com um dos requisitos de linha de base do fórum de CA/navegador](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951). O problema foi relatado em 1º de julho de 2020 e se aplica a vários provedores de PKI (infraestrutura de chave pública) populares em todo o mundo. A maioria dos certificados TLS usados pelos serviços do Azure hoje vem da PKI *raiz Baltimore Cybertrust* . O cache do Azure para o serviço Redis continuará sendo encadeado à raiz Baltimore CyberTrust. Seus certificados de servidor TLS, no entanto, serão emitidos por novas ICAs (autoridades de certificação intermediárias) a partir de 12 de outubro de 2020.

> [!NOTE]
> Essa alteração é limitada aos serviços em [regiões públicas do Azure](https://azure.microsoft.com/global-infrastructure/geographies/). Ele exclui soberanas (por exemplo, China) ou nuvens governamentais.
>
>

### <a name="does-this-change-affect-me"></a>Esta alteração me afeta?

Esperamos que a maioria dos clientes do cache do Azure para Redis não seja afetada pela alteração. Seu aplicativo poderá ser afetado se especificar explicitamente uma lista de certificados aceitáveis, uma prática conhecida como "fixação de certificado". Se ele estiver fixado em um certificado de folha ou intermediário em vez da raiz Baltimore CyberTrust, você deverá **executar ações imediatas** para alterar a configuração do certificado.

A tabela a seguir fornece informações sobre os certificados que estão sendo revertidos. Dependendo do certificado usado pelo seu aplicativo, talvez seja necessário atualizá-lo para evitar a perda de conectividade com o cache do Azure para a instância Redis.

| Tipo de AC | Current | Após a reversão (12 de outubro de 2020) | Ação |
| ----- | ----- | ----- | ----- |
| Root | Impressão digital: d4de20d05e66fc53fe1a50882c78db2852cae474<br><br> Expiração: segunda-feira, 12 de maio de 2025, 4:59:00 PM<br><br> Nome da entidade:<br> CN = Baltimore CyberTrust root<br> OU = CyberTrust<br> O = Baltimore<br> C = IE | Não alterando | Nenhum |
| Intermediário | Impressões digitais<br> CN = Microsoft IT TLS CA 1<br> Impressão digital: 417e225037fbfaa4f95761d5ae729e1aea7e3a42<br><br> CN = Microsoft TLS AC CA 2<br> Impressão digital: 54d9d20239080c32316ed9ff980a48988f4adf2d<br><br> CN = Microsoft TLS CA AC 4<br> Impressão digital: 8a38755d0996823fe8fa3116a277ce446eac4e99<br><br> CN = Microsoft IT TLS CA 5<br> Impressão digital: Ad898ac73df333eb60ac1f5fc6c4b2219ddb79b7<br><br> Expiração: sexta-feira, 20 de maio de 2024 5:52:38<br><br> Nome da entidade:<br> OU = ti da Microsoft<br> O = Microsoft Corporation<br> L = Redmond<br> S = Washington<br> C = US<br> | Impressões digitais<br> CN = Microsoft RSA TLS CA 01<br> Impressão digital: 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a<br><br> CN = Microsoft RSA TLS CA 02<br> Impressão digital: b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75<br><br> Expiração: terça-feira, 8 de outubro de 2024 12:00:00 AM;<br><br> Nome da entidade:<br> O = Microsoft Corporation<br> C = US<br> | Obrigatório |

### <a name="what-actions-should-i-take"></a>Quais ações devo tomar?

Se seu aplicativo usa o repositório de certificados do sistema operacional ou fixa a raiz Baltimore entre outros, nenhuma ação é necessária. Por outro lado, se o seu aplicativo fixa qualquer certificado TLS intermediário ou de folha, recomendamos que você fixe as seguintes raízes:

| Certificado | Impressão digital |
| ----- | ----- |
| [CA raiz do Baltimore](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 |
| [DigiCert de raiz global G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |

> [!TIP]
> Espera-se que os certificados intermediários e folha sejam alterados com frequência. É recomendável não assumir uma dependência delas. Em vez disso, fixe seu aplicativo a um certificado raiz, uma vez que ele é revertido com menos frequência.
>
>

Para continuar a fixar certificados intermediários, adicione o seguinte à lista de certificados intermediários fixados, que inclui alguns outros adicionais para minimizar as alterações futuras:

| Nome comum da AC | Impressão digital |
| ----- | ----- |
| [Microsoft RSA TLS CA 01](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt) | 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a |
| [Microsoft RSA TLS CA 02](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt) | b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75 |
| [AC emissora de Microsoft Azure TLS 01](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.cer) | 2f2877c5d778c31e0f29c7e371df5471bd673173 |
| [CA de emissão de Microsoft Azure TLS 02](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.cer) | e7eea674ca718e3befd90858e09f8372ad0ae2aa |
| [Microsoft Azure a CA de emissão TLS 05](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.cer) | 6c3af02e7f269aa73afd0eff2a88a4a1f04ed1e5 |
| [Microsoft Azure a AC emissora do TLS 06](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer) | 30e01761ab97e59a06b41ef20af6f2de7ef4f7b0 |

Se seu aplicativo validar o certificado no código, você precisará modificá-lo para reconhecer as propriedades (por exemplo, emissores, impressão digital) dos certificados recentemente afixados. Essa verificação extra deve abranger todos os certificados fixados para serem mais reprovados no futuro.

## <a name="next-steps"></a>Próximas etapas

Se você tiver outras dúvidas, entre em contato conosco por meio de [suporte](https://azure.microsoft.com/support/options/).  
