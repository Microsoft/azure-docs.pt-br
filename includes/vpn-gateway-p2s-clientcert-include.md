Cada computador cliente que se conecta a uma rede virtual usando ponto a site deve ter um certificado de cliente instalado. O certificado de cliente é gerado a partir do certificado raiz e instalado em cada computador cliente. Se um certificado de cliente válido não for instalado, e o cliente tentar se conectar à rede virtual, a autenticação falhará.

Você pode gerar um certificado exclusivo para cada cliente ou pode usar o mesmo certificado para vários clientes. A vantagem da geração de certificados de cliente exclusivos é a capacidade de revogar um único certificado. Caso contrário, se vários clientes estiverem usando o mesmo certificado de cliente e for necessário revogá-lo, você precisará gerar e instalar novos certificados para todos os clientes que usam aquele certificado para autenticação.

Você pode gerar certificados de cliente usando os seguintes métodos:

- **Certificado corporativo:**

  - Se você estiver usando uma solução de certificado corporativo, gere um certificado de cliente com o formato de valor de nome comum 'name@yourdomain.com', em vez do formato 'nome do domínio\nomedeusuário'.
  - Verifique se o certificado do cliente baseia-se no modelo de certificado 'Usuário' que tem 'Autenticação de cliente' como o primeiro item na lista de uso, em vez de Logon de Cartão Inteligente, etc. Você pode verificar o certificado clicando duas vezes no certificado do cliente e exibindo **Detalhes > Uso Avançado de Chave**.

- **Certificado raiz autoassinado:** é importante que você execute as etapas em um dos artigos de certificado de P2S abaixo. Caso contrário, os certificados de cliente criados não serão compatíveis com conexões P2S, e os clientes receberão um erro ao tentar se conectar. As etapas em qualquer um dos artigos a seguir geram um certificado do cliente compatível: 

  * [Instruções do Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): essas instruções exigem o Windows 10 e o PowerShell para gerar certificados. Os certificados gerados podem ser instalados em qualquer cliente de P2S com suporte.
  * [Instruções de MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): use MakeCert se você não tiver acesso a um computador com Windows 10 para gerar certificados. MakeCert foi preterido, mas você ainda pode usar o MakeCert para gerar certificados. Os certificados gerados podem ser instalados em qualquer cliente de P2S com suporte.

  Ao gerar um certificado do cliente de um certificado raiz autoassinado usando as instruções anteriores, ele é instalado automaticamente no computador que você usou para gerá-lo. Se você quiser instalar um certificado de cliente em outro computador cliente, será necessário exportá-lo como .pfx e junto com toda a cadeia de certificado. Isso cria um arquivo .pfx que contém as informações do certificado raiz necessárias para o cliente autenticar com sucesso. Para obter as etapas de exportação de um certificado, consulte [Certificados - exportar um certificado do cliente](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).