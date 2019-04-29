---
title: Autorização - Ferramenta de Modelagem de Ameaças da Microsoft - Azure | Microsoft Docs
description: atenuações de ameaças expostas na ferramenta de modelagem de ameaças
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 0803ade7613480621a0cd87f9944ee5f55bf432c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586298"
---
# <a name="security-frame-input-validation--mitigations"></a>Quadro de segurança: Validação de entrada | Atenuações 
| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Aplicativo Web** | <ul><li>[Desabilite o script XSLT para todas as transformações com uso de folhas de estilo não confiáveis](#disable-xslt)</li><li>[Verifique se cada página que pode ter conteúdo controlável pelo usuário recusa a detecção automática de MIME](#out-sniffing)</li><li>[Proteger ou desabilitar a resolução de entidade XML](#xml-resolution)</li><li>[Aplicativos que usam http.sys executam a verificação de conversão em formato canônico de URL](#app-verification)</li><li>[Verifique se os controles adequados estão em vigor ao aceitar arquivos de usuários](#controls-users)</li><li>[Verifique se os parâmetros de tipo seguro são usados no aplicativo Web para acesso a dados](#typesafe)</li><li>[Use classes de model binding separadas ou listas de filtro de associação para evitar a vulnerabilidade de atribuição em massa do MVC](#binding-mvc)</li><li>[Codifique a saída da Web não confiável antes da renderização](#rendering)</li><li>[Execute a validação de entrada e a filtragem em todos os tipos de cadeia de caracteres de propriedades do modelo](#typemodel)</li><li>[A limpeza deve ser aplicada em campos de formulário que aceitam todos os caracteres, como o editor de rich text](#richtext)</li><li>[Não atribua elementos DOM a coletores que não têm codificação embutida](#inbuilt-encode)</li><li>[Valide se todos os redirecionamentos dentro do aplicativo são fechados ou foram feitos com segurança](#redirect-safe)</li><li>[Implemente a validação de entrada em todos os parâmetros de tipo de cadeia de caracteres aceitos por métodos do Controlador](#string-method)</li><li>[Definir o tempo limite superior para o processamento de expressão regular para evitar DoS devido a expressões regulares incorretas](#dos-expression)</li><li>[Evite usar Html.Raw nos modos de exibição do Razor](#html-razor)</li></ul> | 
| **Banco de dados** | <ul><li>[Não use consultas dinâmicas em procedimentos armazenados](#stored-proc)</li></ul> |
| **API da Web** | <ul><li>[Verifique se a validação do modelo é feita nos métodos de API Web](#validation-api)</li><li>[Implemente a validação de entrada em todos os parâmetros de tipo de cadeia de caracteres aceitos pelos métodos de API Web](#string-api)</li><li>[Verifique se os parâmetros de tipo seguro são usados na API Web para acesso a dados](#typesafe-api)</li></ul> | 
| **Azure DocumentDB** | <ul><li>[Usar consultas SQL parametrizadas no Azure Cosmos DB](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[Validação de entrada de WCF por meio de associação de esquema](#schema-binding)</li><li>[Validação de entrada de WCF por meio de Inspetores de Parâmetro](#parameters)</li></ul> |

## <a id="disable-xslt"></a>Desabilite os scripts XSLT para todas as transformações usando folhas de estilo não confiáveis

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Segurança do XSLT](https://msdn.microsoft.com/library/ms763800(v=vs.85).aspx), [Propriedade XsltSettings](https://msdn.microsoft.com/library/system.xml.xsl.xsltsettings.enablescript.aspx) |
| **Etapas** | O XSLT dá suporte a scripts em folhas de estilos usando o elemento `<msxml:script>`. Isso permite que funções personalizadas sejam usadas em uma transformação XSLT. O script é executado no contexto do processo que está realizando a transformação. O script XSLT deve ser desabilitado quando em um ambiente não confiável, para evitar a execução de código não confiável. *Se estiver usando o .NET:* os scripts XSLT são desabilitados por padrão. No entanto, você deve garantir que eles não sejam explicitamente habilitados por meio da propriedade `XsltSettings.EnableScript`.|

### <a name="example"></a>Exemplo 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Exemplo
Se você estiver usando o MSXML 6.0, o script XSLT será desabilitado por padrão; no entanto, você deve garantir que ele não tenha sido explicitamente habilitado por meio da propriedade de objeto XML DOM AllowXsltScript. 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Exemplo
Se você estiver usando MSXML 5 ou anterior, o script XSLT será habilitado por padrão, e você deverá desabilitá-lo explicitamente. Defina a propriedade de objeto XML DOM AllowXsltScript como false. 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a id="out-sniffing"></a>Verifique se cada página que pode ter conteúdo controlável pelo usuário recusa a detecção automática de MIME

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Segurança do IE8 parte V - proteção abrangente](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx)  |
| **Etapas** | <p>Para cada página que pode conter conteúdo controlável do usuário, você deve usar o cabeçalho HTTP `X-Content-Type-Options:nosniff`. Para cumprir esse requisito, você pode definir o cabeçalho necessário página por página somente para as páginas com conteúdo controlável pelo usuário ou pode defini-lo globalmente para todas as páginas no aplicativo.</p><p>Cada tipo de arquivo entregue de um servidor Web tem um [tipo MIME](https://en.wikipedia.org/wiki/Mime_type) (também chamado de *tipo de conteúdo*) associado que descreve a natureza do conteúdo (ou seja, imagem, texto, aplicativo etc.)</p><p>O cabeçalho X-Content-Type-Options é um cabeçalho HTTP que permite aos desenvolvedores especificar que seus conteúdos não devem ter o MIME detectado. Esse cabeçalho foi criado para reduzir os ataques de detecção de MIME. Foi adicionado suporte para esse cabeçalho no Internet Explorer 8 (IE8)</p><p>Somente os usuários do Internet Explorer 8 (IE8) se beneficiarão de Content-Type-Options. Versões anteriores do Internet Explorer atualmente não respeitam o cabeçalho X-Content-Type-Options</p><p>O Internet Explorer 8 (e posterior) é o único navegador importante a implementar um recurso de recusa de detecção de MIME. Se e quando is principais navegadores (Firefox, Safari, Chrome) implementarem recursos semelhantes, essa recomendação será atualizada para incluir também a sintaxe para esses navegadores</p>|

### <a name="example"></a>Exemplo
Para habilitar globalmente o cabeçalho necessário para todas as páginas do aplicativo, você tem as seguintes opções: 

* Adicionar o cabeçalho no arquivo web.config se o aplicativo estiver hospedado no Internet Information Services (IIS) 7 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* Adicionar o cabeçalho usando a Aplication\_BeginRequest global 

``` 
void Application_BeginRequest(object sender, EventArgs e)
{
  this.Response.Headers[""X-Content-Type-Options""] = ""nosniff"";
} 
```

* Implementar o módulo HTTP personalizado 

``` 
public class XContentTypeOptionsModule : IHttpModule 
  {
    #region IHttpModule Members 
    public void Dispose() 
    { 

    } 
    public void Init(HttpApplication context)
    { 
      context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders); 
    } 
    #endregion 
    void context_PreSendRequestHeaders(object sender, EventArgs e) 
      { 
        HttpApplication application = sender as HttpApplication; 
        if (application == null) 
          return; 
        if (application.Response.Headers[""X-Content-Type-Options ""] != null) 
          return; 
        application.Response.Headers.Add(""X-Content-Type-Options "", ""nosniff""); 
      } 
  } 

``` 

* Você pode habilitar o cabeçalho necessário apenas para páginas específicas adicionando-o a respostas individuais: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a id="xml-resolution"></a>Proteger ou desabilitar a resolução de entidade XML

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Expansão de Entidade XML](https://capec.mitre.org/data/definitions/197.html), [Ataques de negação de serviço de XML e defesas](https://msdn.microsoft.com/magazine/ee335713.aspx), [Visão geral de segurança do MSXML](https://msdn.microsoft.com/library/ms754611(v=VS.85).aspx), [Práticas recomendadas para proteger o código do MSXML](https://msdn.microsoft.com/library/ms759188(VS.85).aspx), [Referência de protocolo NSXMLParserDelegate](https://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html), [Resolvendo referências externas](https://msdn.microsoft.com/library/5fcwybb2.aspx) |
| **Etapas**| <p>Embora não seja amplamente usado, há um recurso XML que permite que o analisador XML expanda entidades de macro com valores definidos no próprio documento ou de fontes externas. Por exemplo, o documento pode definir uma entidade "companyname" com o valor de "Microsoft". Assim, sempre que o texto "&companyname;" aparecer no documento, ele será substituído automaticamente pelo texto Microsoft. Ou então, o documento pode definir uma entidade "MSFTStock" que faz referência a um serviço Web externo para buscar o valor atual de ações da Microsoft.</p><p>Em seguida, sempre que "&MSFTStock;" aparecer no documento, será substituído automaticamente pelo preço da ação atual. No entanto, essa funcionalidade pode ser usada para criar condições de DoS (negação de serviço). Um invasor pode aninhar várias entidades para criar uma bomba de XML de expansão exponencial que consome toda a memória disponível no sistema. </p><p>Como alternativa, ele pode criar uma referência externa que transmita de volta uma quantidade infinita de dados ou que simplesmente interrompa o thread. Como resultado, todas as equipes deverão desabilitar a resolução de entidade XML interna e/ou externa totalmente se o aplicativo não a usar ou limitar manualmente a quantidade de memória e o tempo que o aplicativo pode consumir para resolução de entidade, se essa funcionalidade for absolutamente necessária. Se a resolução de entidade não for exigida pelo aplicativo, desabilite-a. </p>|

### <a name="example"></a>Exemplo
Para código do .NET Framework, você pode usar as seguintes abordagens:

```csharp
XmlTextReader reader = new XmlTextReader(stream);
reader.ProhibitDtd = true;

XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);

// for .NET 4
XmlReaderSettings settings = new XmlReaderSettings();
settings.DtdProcessing = DtdProcessing.Prohibit;
XmlReader reader = XmlReader.Create(stream, settings);
```
Observe que o valor padrão de `ProhibitDtd` em `XmlReaderSettings` é true, mas é false em `XmlTextReader`. Se estiver usando XmlReaderSettings, você não precisará definir ProhibitDtd como true explicitamente, mas isso é recomendável por razões de segurança. Observe também que a classe XmlDocument permite a resolução de entidade por padrão. 

### <a name="example"></a>Exemplo
Para desabilitar a resolução de entidade para XmlDocuments, use o a sobrecarga `XmlDocument.Load(XmlReader)` do método Load e defina as propriedades adequadas no argumento XmlReader para desabilitar a resolução, conforme ilustrado no seguinte código: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Exemplo
Se não for possível para o aplicativo desabilitar a resolução de entidade, defina a propriedade XmlReaderSettings.MaxCharactersFromEntities com um valor razoável, de acordo com as necessidades do aplicativo. Isso limitará o impacto de possíveis ataques de DoS de expansão exponencial. O código a seguir fornece um exemplo dessa abordagem: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Exemplo
Se precisar resolver entidades embutidas, mas não precisar resolver entidades externas, defina a propriedade XmlReaderSettings.XmlResolver como nulo. Por exemplo:  

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Observe que, no MSXML6, ProhibitDTD está definido como true (desabilitando o processamento de DTD) por padrão. Para código do Apple OSX/iOS, há dois analisadores XML que você pode usar: NSXMLParser e libXML2. 

## <a id="app-verification"></a>Os aplicativos que usam http.sys executam a verificação de conversão em formato canônico de URL

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | <p>Qualquer aplicativo que usa http.sys deve seguir estas diretrizes:</p><ul><li>Limite o comprimento da URL para no máximo 16.384 caracteres (ASCII ou Unicode). Esse é o comprimento máximo absoluto da URL com base na configuração padrão do IIS (Serviços de Informações da Internet) 6. Os sites devem se esforçar para ter um comprimento menor do que isso, se possível</li><li>Use as classes de E/S de arquivo padrão do .NET Framework (como FileStream), pois elas tirarão proveito das regras de conversão em formato canônico no .NET FX</li><li>Criar explicitamente uma lista de permissões de nomes de arquivo conhecidos</li><li>Rejeite explicitamente tipos de arquivo conhecidos que você não fornecerá a rejeições de UrlScan: exe, bat, cmd, com, htw, ida, idq, htr, idc, shtm[l], stm, printer, ini, pol, arquivos files</li><li>Utilize as seguintes exceções:<ul><li>System.ArgumentException (para nomes de dispositivo)</li><li>System.NotSupportedException (para fluxos de dados)</li><li>System.IO.FileNotFoundException (para nomes com escape inválidos)</li><li>System.IO.DirectoryNotFoundException (para diretórios com escape inválidos)</li></ul></li><li>*Não* chamar APIs de E/S de arquivo do Win32. Em uma URL inválida, normalmente é retornado um erro 400 para o usuário e o erro real é registrado em log.</li></ul>|

## <a id="controls-users"></a>Verifique se os controles adequados estão em vigor ao aceitar arquivos de usuários

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Upload de arquivo irrestrito](https://www.owasp.org/index.php/Unrestricted_File_Upload), [Tabela de assinatura de arquivo](https://www.garykessler.net/library/file_sigs.html) |
| **Etapas** | <p>Os arquivos carregados representam um risco significativo para os aplicativos.</p><p>A primeira etapa em muitos ataques é obter algum código do sistema a ser atacado. Assim, o ataque só precisa encontrar uma maneira de fazer com que o código seja executado. Usar um upload de arquivo ajuda o invasor a realizar a primeira etapa. As consequências do upload de arquivos irrestrito podem variar, incluindo a tomada de controle completa do sistema, um sistema de arquivos ou banco de dados sobrecarregado, encaminhamento de ataques aos sistemas de back-end e desfiguração simples.</p><p>Depende de que o aplicativo faz com o arquivo carregado e, especialmente, onde ele é armazenado. A validação do lado do servidor de uploads de arquivo está ausente. Os controles de segurança a seguir devem ser implementados para a funcionalidade de Upload de Arquivo:</p><ul><li>Verificação de extensão de arquivo (somente um conjunto válido de tipos de arquivo permitidos deve ser aceito)</li><li>Limite de tamanho máximo de arquivo</li><li>O arquivo não deve ser carregado para webroot; o local deve ser um diretório em uma unidade que não seja do sistema</li><li>A convenção de nomenclatura deve ser seguida, de forma que o nome do arquivo carregado seja aleatório até certo ponto, para impedir que o arquivo seja substituído</li><li>Os arquivos devem ser verificados com o antivírus antes de serem gravados no disco</li><li>Verifique se o nome do arquivo e outros metadados (por exemplo, o caminho do arquivo) são validados para caracteres mal-intencionados</li><li>A assinatura do formato de arquivo deve ser verificada para impedir que um usuário carregue um arquivo mascarado (por exemplo, carregando um arquivo exe alterando a extensão para txt)</li></ul>| 

### <a name="example"></a>Exemplo
Para o último ponto sobre validação de assinatura de formato de arquivo, confira a classe abaixo para obter detalhes: 

```csharp
        private static Dictionary<string, List<byte[]>> fileSignature = new Dictionary<string, List<byte[]>>
                    {
                    { ".DOC", new List<byte[]> { new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 } } },
                    { ".DOCX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".PDF", new List<byte[]> { new byte[] { 0x25, 0x50, 0x44, 0x46 } } },
                    { ".ZIP", new List<byte[]> 
                                            {
                                              new byte[] { 0x50, 0x4B, 0x03, 0x04 },
                                              new byte[] { 0x50, 0x4B, 0x4C, 0x49, 0x54, 0x55 },
                                              new byte[] { 0x50, 0x4B, 0x53, 0x70, 0x58 },
                                              new byte[] { 0x50, 0x4B, 0x05, 0x06 },
                                              new byte[] { 0x50, 0x4B, 0x07, 0x08 },
                                              new byte[] { 0x57, 0x69, 0x6E, 0x5A, 0x69, 0x70 }
                                                }
                                            },
                    { ".PNG", new List<byte[]> { new byte[] { 0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A } } },
                    { ".JPG", new List<byte[]>
                                    {
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE1 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE8 }
                                    }
                                    },
                    { ".JPEG", new List<byte[]>
                                        { 
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 }
                                        }
                                        },
                    { ".XLS", new List<byte[]>
                                            {
                                              new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 },
                                              new byte[] { 0x09, 0x08, 0x10, 0x00, 0x00, 0x06, 0x05, 0x00 },
                                              new byte[] { 0xFD, 0xFF, 0xFF, 0xFF }
                                            }
                                            },
                    { ".XLSX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".GIF", new List<byte[]> { new byte[] { 0x47, 0x49, 0x46, 0x38 } } }
                };

        public static bool IsValidFileExtension(string fileName, byte[] fileData, byte[] allowedChars)
        {
            if (string.IsNullOrEmpty(fileName) || fileData == null || fileData.Length == 0)
            {
                return false;
            }

            bool flag = false;
            string ext = Path.GetExtension(fileName);
            if (string.IsNullOrEmpty(ext))
            {
                return false;
            }

            ext = ext.ToUpperInvariant();

            if (ext.Equals(".TXT") || ext.Equals(".CSV") || ext.Equals(".PRN"))
            {
                foreach (byte b in fileData)
                {
                    if (b > 0x7F)
                    {
                        if (allowedChars != null)
                        {
                            if (!allowedChars.Contains(b))
                            {
                                return false;
                            }
                        }
                        else
                        {
                            return false;
                        }
                    }
                }

                return true;
            }

            if (!fileSignature.ContainsKey(ext))
            {
                return true;
            }

            List<byte[]> sig = fileSignature[ext];
            foreach (byte[] b in sig)
            {
                var curFileSig = new byte[b.Length];
                Array.Copy(fileData, curFileSig, b.Length);
                if (curFileSig.SequenceEqual(b))
                {
                    flag = true;
                    break;
                }
            }

            return flag;
        }
```

## <a id="typesafe"></a>Verifique se os parâmetros de tipo seguro são usados no aplicativo Web para acesso a dados

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | <p>Se você usar a coleção de Parâmetros, o SQL tratará a entrada como um valor literal em vez de código executável. A coleção de Parâmetros pode ser usada para impor restrições de comprimento e tipo aos dados de entrada. Valores fora do intervalo disparam uma exceção. Se não forem usados parâmetros SQL de tipo seguro, invasores poderão executar ataques de injeção que são inseridos na entrada não filtrada.</p><p>Use parâmetros de tipo seguro ao construir consultas SQL para evitar possíveis ataques de injeção de SQL que podem ocorrer com a entrada não filtrada. Você pode usar parâmetros de tipo seguro com procedimentos armazenados e instruções SQL dinâmicas. Os parâmetros são tratados como valores literais no banco de dados, não como código executável. Os parâmetros também são verificados quanto ao tipo e ao tamanho.</p>|

### <a name="example"></a>Exemplo 
O código a seguir mostra como usar parâmetros de tipo seguro com SqlParameterCollection ao chamar um procedimento armazenado. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
No exemplo de código anterior, o valor de entrada não pode ter mais de 11 caracteres. Se os dados não estão de acordo com o tipo ou o comprimento definido pelo parâmetro, a classe SqlParameter lança uma exceção. 

## <a id="binding-mvc"></a>Use classes de model binding separadas ou listas de filtro de associação para evitar a vulnerabilidade de atribuição em massa do MVC

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Atributos de metadados](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.metadatatypeattribute), [Vulnerabilidade de segurança de chave pública e atenuação](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation), [Guia completo para atribuição em massa no ASP.NET MVC](https://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [Introdução ao EF usando MVC](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Etapas** | <ul><li>**Quando devo procurar vulnerabilidades de overposting? -** As vulnerabilidades de overposting podem ocorrer em qualquer lugar em que você associar classes de modelo da entrada do usuário. Estruturas como MVC podem representar dados de usuário em classes personalizadas do .NET, incluindo POCOs (Plain Old CLR Objects). O MVC popula automaticamente essas classes de modelo com os dados da solicitação, fornecendo uma representação conveniente para lidar com a entrada do usuário. Quando essas classes incluem propriedades que não devem ser definidas pelo usuário, o aplicativo pode ser vulnerável a ataques de overposting, que permitem o controle pelo usuário de dados que o aplicativo nunca deveria permitir. Assim como o model binding MVC, tecnologias de acesso a bancos de dados, como mapeadores de objeto/relacionais como Entity Framework, geralmente também dão suporte ao uso de objetos POCO para representar dados do banco de dados. Essas classes de modelo de dados fornecem a mesma conveniência ao lidar com dados do banco de dados que o MVC oferece ao lidar com a entrada do usuário. Como o MVC e o banco de dados dão suporte a modelos semelhantes, como objetos POCO, parece fácil reutilizar as mesmas classes para as duas finalidades. Essa prática não preservar a separação de questões e é uma área comum em que propriedades não intencionais são expostas ao model binding, habilitando ataques de excesso de postagem.</li><li>**Por que não deve usar classes de modelo de banco de dados não filtradas como parâmetros para as ações MVC? -** Porque o model binding MVC associará qualquer item nessa classe. Mesmo se os dados não aparecerem no modo de exibição, um usuário mal-intencionado poderá enviar uma solicitação HTTP com esses dados incluídos, e o MVC os associará, pois a ação diz que a classe de banco de dados é a forma de dados que ele deve aceitar para a entrada do usuário.</li><li>**Por que devo me preocupar com a forma usada para model binding? -** O uso do model binding com o ASP.NET MVC com modelos demasiadamente amplos expõe um aplicativo a ataques de overposting. O excesso de postagem pode permitir que usuários mal-intencionados alterem dados de aplicativos além do que o desenvolvedor desejava, como substituir o preço de um item ou os privilégios de segurança de uma conta. Os aplicativos devem usar modelos de associação específicos de ação (ou listas de filtros de propriedade permitidos específicos) para fornecer um contrato explícito para quais entradas não confiáveis devem ser permitidas por meio de model binding.</li><li>**Ter modelos de associação separados apenas duplica o código? -** Não, é uma questão de separação de questões. Se reutilizar os modelos de banco de dados nos métodos de ação, você está indicando que qualquer propriedade (ou subpropriedade) nessa classe poderá ser definida pelo usuário em uma solicitação HTTP. Se isso não for o que você deseja que o MVC faça, você precisará de uma lista de filtros ou uma forma de classe separada para mostrar ao MVC quais dados podem vir da entrada de usuário.</li><li>**Se eu tiver modelos de associação separados para entrada do usuário, terei que duplicar todos os atributos de anotação de dados? -** Não necessariamente. Você pode usar MetadataTypeAttribute na classe de modelo de banco de dados para vincular aos metadados em uma classe de model binding. Apenas observe que o tipo referenciado por MetadataTypeAttribute deve ser um subconjunto do tipo de referência (ele pode ter menos propriedades, mas não mais).</li><li>**A movimentação de dados entre modelos de entrada de usuário e modelos de banco de dados é entediante. Posso simplesmente copiar todas as propriedades usando a reflexão? -** Sim. As únicas propriedades que aparecem nos modelos de associação são aquelas que você determinou como seguras para a entrada do usuário. Não há motivo de segurança que impeça o uso de reflexão para copiar todas as propriedades que existem em comum entre esses dois modelos.</li><li>**Que tal [Bind(Exclude ="â€¦")]. Posso usar isso em vez de ter modelos de associação separados? -** Essa abordagem não é recomendada. Usar [Bind(Exclude ="â€¦")] significa que qualquer nova propriedade pode ser associada por padrão. Quando uma nova propriedade é adicionada, há uma etapa extra para se lembrar de manter tudo seguro, em vez de ter o design seguro por padrão. É arriscado depender da verificação dessa lista pelo desenvolvedor sempre que uma propriedade for adicionada.</li><li>**[Bind(Include ="â€¦")] é útil para operações de Editar? -** Não. [Bind(Include ="â€¦")] é adequado apenas para operações do estilo INSERT (adicionar novos dados). Para operações de estilo UPDATE (examinar dados existentes), use outra abordagem, como ter modelos de associação separados ou passar uma lista explícita de propriedades permitidas para UpdateModel ou TryUpdateModel. Adicionar um atributo [Bind(Include ="â€¦")] em uma operação de Editar significa que o MVC criará uma instância do objeto e definirá apenas as propriedades listadas, deixando todos os outros valores padrão. Quando os dados forem persistidos, isso substituirá totalmente a entidade existente, redefinindo os valores de qualquer propriedade omitida para seus padrões. Por exemplo, se IsAdmin tiver sido omitido de um atributo [Bind(Include ="â€¦")] em uma operação de Editar, qualquer usuário cujo nome tiver sido editado por meio dessa ação seria redefinido como IsAdmin = false (qualquer usuário editado perderia status de administrador). Se quiser impedir atualizações em determinadas propriedades, use um dos outros métodos acima. Observe que algumas versões das ferramentas do MVC geram classes de controlador com [Bind(Include ="â€¦")] em ações de Editar e implicam que remover uma propriedade da lista impedirá os ataques de excesso de postagem. No entanto, conforme descrito acima, essa abordagem não funciona conforme o esperado e, em vez disso, redefinirá todos os dados nas propriedades omitidos para seus valores padrão.</li><li>**Para operações de Criar, há alguma restrição a usar [Bind(Include ="â€¦")] em vez de modelos de associação separados? -** Sim. Primeiro, essa abordagem não funciona para cenários de Edição, que exigem a manutenção de duas abordagens separadas para atenuar todas as vulnerabilidades de excesso de postagem. Em segundo lugar, modelos de associação separados impõem a separação de problemas entre a forma usada para a entrada do usuário e a forma usada para persistência, algo que [Bind(Include ="â€¦")] não faz. Em terceiro lugar, observe que [Bind(Include ="â€¦")] pode lidar apenas com propriedades de nível superior; você não pode permitir somente partes de subpropriedades (por exemplo, "Details.Name") no atributo. Por fim, e talvez mais importante, usar [Bind(Include ="â€¦")] adiciona uma etapa extra que deve ser lembrada sempre que a classe é usada para model binding. Se um novo método de ação for associado à classe de dados diretamente e esquecer de incluir um atributo [Bind(Include ="â€¦")], ele poderá ficar vulnerável a ataques de excesso de postagem, de forma que a abordagem com [Bind(Include ="â€¦")] é um pouco menos segura por padrão. Caso você use [Bind(Include ="â€¦")], tome cuidado para sempre se lembrar de especificá-lo toda vez que suas classes de dados forem exibidas como parâmetros de método de ação.</li><li>**Para operações de Criar, é possível colocar o atributo [Bind(Include ="â€¦")] na própria classe do modelo? Essa abordagem não evita a necessidade de lembrar de colocar o atributo em cada método de ação? -** Essa abordagem funciona em alguns casos. Usar [Bind(Include ="â€¦")] no próprio tipo de modelo (e não nos parâmetros de ação que usam essa classe) evita a necessidade de se lembrar de incluir o atributo [Bind(Include ="â€¦")] em cada método de ação. Usar o atributo diretamente na classe efetivamente cria uma área de superfície separada dessa classe para fins de model binding. No entanto, essa abordagem só permite uma forma de model binding por classe de modelo. Se um método de ação precisar permitir que o model binding de um campo (por exemplo, uma ação somente de administrador que atualiza funções de usuário) e outras ações precisarem impedir o model binding desse campo, essa abordagem não funcionará. Cada classe pode ter apenas uma forma de model binding. Se ações diferentes precisarem de formas de model binding diferentes, elas precisarão representar essas formas separadas usando classes de model binding separadas ou atributos [Bind(Include ="â€¦")] separados nos métodos de ação.</li><li>**O que são modelos de associação? Eles são o mesmo que os modelos de exibição? -** São dois conceitos relacionados. O modelo de associação de termo se refere a um modelo de classe usado em uma ação e é a lista de parâmetros (a forma passada do model binding MVC para o método de ação). O modelo de exibição do termo se refere a uma classe de modelo passada de um método de ação para um modo de exibição. Usar um modelo específico de modo de exibição é uma abordagem comum para transmitir dados de um método de ação para um modo de exibição. Muitas vezes, essa forma também é adequada para associação de modelos, e o modelo de exibição de termo pode ser usado para referenciar o mesmo modelo usado em ambos os locais. Em termos mais precisos, esse procedimento aborda especificamente a associação de modelos, concentrando-se na forma passada para a ação, o que importa para fins de atribuição em massa.</li></ul>| 

## <a id="rendering"></a>Codifique a saída da Web não confiável antes da renderização

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, Formulários da Web, MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Como evitar scripts entre sites no ASP.NET](https://msdn.microsoft.com/library/ms998274.aspx), [Scripts entre sites](https://cwe.mitre.org/data/definitions/79.html), [Folha de consulta de prevenção de XSS (Cross Site Scripting)](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet) |
| **Etapas** | Scripts entre sites (frequentemente abreviados como XSS) são um vetor de ataque para serviços online ou qualquer aplicativo/componente que consuma a entrada da Web. As vulnerabilidades de XSS podem permitir que um invasor execute o script no computador de outro usuário por meio de um aplicativo Web vulnerável. Scripts mal-intencionados podem ser usados para roubar cookies e adulterar máquina da vítima por meio de JavaScript. O XSS é impedido pela validação da entrada do usuário, garantindo que ele seja bem-formado e codificado antes de ser renderizado em uma página da Web. A validação de entrada e a codificação de saída podem ser feitas usando a Web Protection Library. Para código gerenciado (C\#, VB.NET, etc.), use um ou mais métodos de codificação da biblioteca de proteção da Web (Anti-XSS), dependendo do contexto em que a entrada do usuário é manifestada apropriados:| 

### <a name="example"></a>Exemplo

```csharp
* Encoder.HtmlEncode 
* Encoder.HtmlAttributeEncode 
* Encoder.JavaScriptEncode 
* Encoder.UrlEncode
* Encoder.VisualBasicScriptEncode 
* Encoder.XmlEncode 
* Encoder.XmlAttributeEncode 
* Encoder.CssEncode 
* Encoder.LdapEncode 
```

## <a id="typemodel"></a>Execute a validação de entrada e a filtragem em todos os tipos de cadeia de caracteres de propriedades do modelo

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Adicionando Validação](https://www.asp.net/mvc/overview/getting-started/introduction/adding-validation), [Validando Dados de Modelo em um Aplicativo MVC](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [Princípios Básicos para Aplicativos ASP.NET MVC](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Etapas** | <p>Todos os parâmetros de entrada devem ser validados antes de serem usados no aplicativo, para garantir que o aplicativo esteja protegido contra entradas de usuários mal-intencionados. Valide os valores de entrada usando validações de expressão regular no lado do servidor com uma estratégia de validação de lista de permissões. Entradas do usuário/parâmetros não corrigidos passados para os métodos podem causar vulnerabilidades de injeção de código.</p><p>Para aplicativos Web, os pontos de entrada também podem incluir campos de formulário, QueryStrings, cookies, cabeçalhos HTTP e parâmetros de serviço Web.</p><p>As seguintes verificações de validação de entrada devem ser executadas após o model binding:</p><ul><li>As propriedades do modelo devem ser anotadas com a anotação RegularExpression, para aceitar os caracteres e o comprimento máximo permitidos</li><li>Os métodos do controlador devem executar a validade ModelState</li></ul>|

## <a id="richtext"></a>A limpeza deve ser aplicada em campos de formulário que aceitam todos os caracteres, como o editor de rich text

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Codificar a entrada não segura](https://msdn.microsoft.com/library/ff647397.aspx#paght000003_step3), [HTML Sanitizer](https://github.com/mganss/HtmlSanitizer) |
| **Etapas** | <p>Identifica todas as marcas de marcação estática que você deseja usar. Uma prática comum é restringir a formatação a elementos HTML seguros, como `<b>` (negrito) e `<i>` (itálico).</p><p>Antes de gravar os dados, codifique-os em HTML. Isso torna qualquer script mal-intencionado seguro, fazendo com que ele deva ser tratado como texto, não como código executável.</p><ol><li>Desabilite a validação de solicitação do ASP.NET adicionando o atributo ValidateRequest="false" à política Page do \@</li><li>Codificar a entrada de cadeia de caracteres com o método HtmlEncode</li><li>Use StringBuilder e chame seu método Replace para remover seletivamente a codificação nos elementos HTML que você deseja permitir</li></ol><p>A página nas referências desabilita a validação de solicitação ASP.NET definindo `ValidateRequest="false"`. Ele codifica a entrada com HTML e permite seletivamente `<b>` e `<i>`. Como alternativa, uma biblioteca .NET para a limpeza de HTML também pode ser usada.</p><p>HtmlSanitizer é uma biblioteca .NET para limpeza de fragmentos HTML e documentos para impedir constructos que possam levar a ataques de XSS. Ele usa AngleSharp para analisar, manipular e renderizar HTML e CSS. HtmlSanitizer pode ser instalado como um pacote do NuGet, e a entrada do usuário pode ser passada por métodos de limpeza HTML ou CSS relevantes, conforme aplicável, no lado do servidor. Observe que a limpeza como um controle de segurança deve ser considerada apenas como último recurso.</p><p>A validação de entrada e a codificação de saída são consideradas controles de segurança melhores.</p> |

## <a id="inbuilt-encode"></a>Não atribua elementos DOM a coletores que não tenham a codificação embutida

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Muitas funções de javascript não fazem a codificação por padrão. Ao atribuir entradas não confiáveis para elementos DOM por meio de funções, isso pode resultar em execuções de scripts entre sites (XSS).| 

### <a name="example"></a>Exemplo
A seguir estão exemplos não seguros: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Não use `innerHtml`; em vez disso, use `innerText`. Da mesma forma, em vez de `$("#elm").html()`, use `$("#elm").text()` 

## <a id="redirect-safe"></a>Valide se todos os redirecionamentos dentro do aplicativo são fechados ou foram feitos com segurança

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Estrutura de autorização OAuth 2.0 - redirecionadores abertos](https://tools.ietf.org/html/rfc6749#section-10.15) |
| **Etapas** | <p>O design do aplicativo que exige o redirecionamento para um local fornecido pelo usuário deve restringir os destinos possíveis de redirecionamento para uma lista predefinida "segura" de sites ou domínios. Todos os redirecionamentos do aplicativo devem ser fechados/seguros.</p><p>Para fazer isso:</p><ul><li>Identificar todos os redirecionamentos</li><li>Implemente uma atenuação apropriada para cada tipo de redirecionamento. Atenuações apropriadas incluem a confirmação de usuário ou a lista de permissões de redirecionamento. Se um site ou serviço com uma vulnerabilidade de redirecionamento aberto usar provedores de identidade Facebook/OAuth/OpenID, um invasor poderá roubar o token de logon do usuário e representar o usuário. Esse é um risco inerente ao usar OAuth, o que está documentado em RFC 6749 "A Estrutura de Autorização OAuth 2.0", Seção 10.15, "Redirecionamentos Abertos". Da mesma forma, as credenciais de usuário podem ser comprometidas por ataques de spear phishing usando redirecionamentos abertos</li></ul>|

## <a id="string-method"></a>Implemente a validação de entrada em todos os parâmetros de tipo de cadeia de caracteres aceitos por métodos do Controlador

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Validando dados de modelo em um aplicativo MVC](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [Princípios básicos para aplicativos ASP.NET MVC](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Etapas** | Para métodos que aceitam apenas o tipo de dados primitivo e não modelos como argumento, a validação de entrada deve ser feita usando a Expressão Regular. Aqui, Regex.IsMatch deve ser usado com um padrão de regex válido. Se a entrada não corresponder à Expressão Regular especificada, o controle não deverá prosseguir, e deverá ser exibido um aviso adequado sobre a falha de validação.| 

## <a id="dos-expression"></a>Defina o tempo limite superior para o processamento de expressão regular para evitar DoS devido a expressões regulares incorretas

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, Formulários da Web, MVC5, MVC6  |
| **Atributos**              | N/D  |
| **Referências**              | [Propriedade DefaultRegexMatchTimeout](https://msdn.microsoft.com/library/system.web.configuration.httpruntimesection.defaultregexmatchtimeout.aspx) |
| **Etapas** | Para impedir ataques de negação de serviço contra expressões regulares criadas incorretamente, o que causa muito retrocesso, defina o tempo limite padrão global. Se o tempo de processamento for mais longo do que o limite superior definido, ela lançará uma exceção de tempo limite. Se nada estiver configurado, o tempo limite seria infinito.| 

### <a name="example"></a>Exemplo
Por exemplo, a configuração a seguir lançará RegexMatchTimeoutException se o processamento levar mais de cinco segundos: 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a id="html-razor"></a>Evite usar Html.Raw nos modos de exibição do Razor

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Etapa | Páginas da Web do ASP.NET (Razor) executar a codificação HTML automática. Todas as cadeias de caracteres impressas por nuggets de código inserido (blocos @) são codificadas em HTML automaticamente. No entanto, quando o método `HtmlHelper.Raw` é invocado, ele retorna uma marcação que não é codificada em HTML. Se o método auxiliar `Html.Raw()` for usado, ele ignorará a proteção de codificação automática que o Razor fornece.|

### <a name="example"></a>Exemplo
A seguir está um exemplo não seguro: 

```csharp
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
Não use `Html.Raw()`, a menos que você precise exibir a marcação. Esse método não executa implicitamente a codificação de saída. Use outros auxiliares do ASP.NET; por exemplo, `@Html.DisplayFor()` 

## <a id="stored-proc"></a>Não use consultas dinâmicas em procedimentos armazenados

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Banco de dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | <p>Um ataque de injeção de SQL explora vulnerabilidades na validação de entrada para executar comandos arbitrários no banco de dados. Isso pode ocorrer quando o aplicativo usa a entrada para construir instruções SQL dinâmicas para acessar o banco de dados. Isso também poderá ocorrer se o código usar procedimentos armazenados que sejam cadeias de caracteres passadas que contenham entrada bruta do usuário. Usando o ataque de injeção de SQL, o invasor pode executar comandos arbitrários no banco de dados. Todas as instruções SQL (incluindo as instruções SQL em procedimentos armazenados) devem ser parametrizadas. Instruções SQL parametrizadas aceitam caracteres que têm significado especial para o SQL (como aspas simples) sem problemas, pois eles são fortemente tipados. |

### <a name="example"></a>Exemplo
A seguir está um exemplo de Procedimento Armazenado dinâmico não seguro: 

```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteria]
(
  @productName nvarchar(200) = NULL,
  @startPrice float = NULL,
  @endPrice float = NULL
)
AS
 BEGIN
  DECLARE @sql nvarchar(max)
  SELECT @sql = ' SELECT ProductID, ProductName, Description, UnitPrice, ImagePath' +
       ' FROM dbo.Products WHERE 1 = 1 '
       PRINT @sql
  IF @productName IS NOT NULL
     SELECT @sql = @sql + ' AND ProductName LIKE ''%' + @productName + '%'''
  IF @startPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice > ''' + CONVERT(VARCHAR(10),@startPrice) + ''''
  IF @endPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice < ''' + CONVERT(VARCHAR(10),@endPrice) + ''''

  PRINT @sql
  EXEC(@sql)
 END
```

### <a name="example"></a>Exemplo
A seguir está o mesmo procedimento armazenado implementado com segurança: 
```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteriaSecure]
(
             @productName nvarchar(200) = NULL,
             @startPrice float = NULL,
             @endPrice float = NULL
)
AS
       BEGIN
             SELECT ProductID, ProductName, Description, UnitPrice, ImagePath
             FROM dbo.Products where
             (@productName IS NULL or ProductName like '%'+ @productName +'%')
             AND
             (@startPrice IS NULL or UnitPrice > @startPrice)
             AND
             (@endPrice IS NULL or UnitPrice < @endPrice)         
       END
```

## <a id="validation-api"></a>Verifique se a validação do modelo é feita nos métodos de API Web

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Validação do modelo na API Web ASP.NET](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Etapas** | Quando um cliente envia dados a uma API Web, é obrigatório validar os dados antes de executar qualquer processamento. Para APIs Web do ASP.NET que aceitam modelos como entrada, use anotações de dados em modelos para definir regras de validação nas propriedades do modelo.|

### <a name="example"></a>Exemplo
O código a seguir demonstra o mesmo: 

```csharp
using System.ComponentModel.DataAnnotations;

namespace MyApi.Models
{
    public class Product
    {
        public int Id { get; set; }
        [Required]
        [RegularExpression(@"^[a-zA-Z0-9]*$", ErrorMessage="Only alphanumeric characters are allowed.")]
        public string Name { get; set; }
        public decimal Price { get; set; }
        [Range(0, 999)]
        public double Weight { get; set; }
    }
}
```

### <a name="example"></a>Exemplo
No método de ação dos controladores de API, a validade do modelo deve ser explicitamente verificada, conforme mostrado abaixo: 

```csharp
namespace MyApi.Controllers
{
    public class ProductsController : ApiController
    {
        public HttpResponseMessage Post(Product product)
        {
            if (ModelState.IsValid)
            {
                // Do something with the product (not shown).

                return new HttpResponseMessage(HttpStatusCode.OK);
            }
            else
            {
                return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
            }
        }
    }
}
```

## <a id="string-api"></a>Implemente a validação de entrada em todos os parâmetros de tipo de cadeia de caracteres aceitos pelos métodos de API Web

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, MVC 5, MVC 6 |
| **Atributos**              | N/D  |
| **Referências**              | [Validando dados de modelo em um aplicativo MVC](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [Princípios básicos para aplicativos ASP.NET MVC](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Etapas** | Para métodos que aceitam apenas o tipo de dados primitivo e não modelos como argumento, a validação de entrada deve ser feita usando a Expressão Regular. Aqui, Regex.IsMatch deve ser usado com um padrão de regex válido. Se a entrada não corresponder à Expressão Regular especificada, o controle não deverá prosseguir, e deverá ser exibido um aviso adequado sobre a falha de validação.|

## <a id="typesafe-api"></a>Verifique se os parâmetros de tipo seguro são usados na API Web para acesso a dados

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | <p>Se você usar a coleção de Parâmetros, o SQL tratará a entrada como um valor literal em vez de código executável. A coleção de Parâmetros pode ser usada para impor restrições de comprimento e tipo aos dados de entrada. Valores fora do intervalo disparam uma exceção. Se não forem usados parâmetros SQL de tipo seguro, invasores poderão executar ataques de injeção que são inseridos na entrada não filtrada.</p><p>Use parâmetros de tipo seguro ao construir consultas SQL para evitar possíveis ataques de injeção de SQL que podem ocorrer com a entrada não filtrada. Você pode usar parâmetros de tipo seguro com procedimentos armazenados e instruções SQL dinâmicas. Os parâmetros são tratados como valores literais no banco de dados, não como código executável. Os parâmetros também são verificados quanto ao tipo e ao tamanho.</p>|

### <a name="example"></a>Exemplo
O código a seguir mostra como usar parâmetros de tipo seguro com SqlParameterCollection ao chamar um procedimento armazenado. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
No exemplo de código anterior, o valor de entrada não pode ter mais de 11 caracteres. Se os dados não estão de acordo com o tipo ou o comprimento definido pelo parâmetro, a classe SqlParameter lança uma exceção. 

## <a id="sql-docdb"></a>Usar consultas SQL parametrizadas no Cosmos DB

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure Document DB | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Anunciando a parametrização de SQL no Azure Cosmos DB](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Etapas** | Embora o Azure Cosmos DB dê suporte apenas a consultas somente leitura, a injeção de SQL ainda será possível se as consultas forem construídas concatenando com a entrada do usuário. É possível que um usuário obtenha acesso a dados que não deveria acessar na mesma coleção criando consultas SQL mal-intencionadas. Use consultas SQL parametrizadas se as consultas forem construídas com base na entrada do usuário. |

## <a id="schema-binding"></a>Validação de entrada de WCF por meio de associação de esquema

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, NET Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff647820.aspx) |
| **Etapas** | <p>A ausência de validação leva a ataques de injeção de tipo diferente.</p><p>A validação de mensagem representa uma linha de defesa na proteção do aplicativo WCF. Com essa abordagem, você deve validar mensagens usando esquemas para proteger as operações de serviço WCF contra um ataque de um cliente mal-intencionado. Valide todas as mensagens recebidas pelo cliente para protegê-lo contra ataques de um serviço mal-intencionado. A validação de mensagem torna possível validar mensagens quando operações consomem contratos de mensagem ou de dados, o que não pode ser feito usando a validação de parâmetro. A validação de mensagem permite que você crie a lógica de validação dentro de esquemas, fornecendo assim mais flexibilidade e reduzindo o tempo de desenvolvimento. Esquemas podem ser reutilizados entre diferentes aplicativos na organização, criando padrões para representação de dados. Além disso, a validação de mensagem permite proteger operações quando elas consomem tipos de dados mais complexos que envolvem contratos que representam a lógica de negócios.</p><p>Para executar a validação de mensagem, primeiro crie um esquema para representar as operações do serviço e os tipos de dados consumidos por essas operações. Você cria então uma classe .NET que implementa um inspetor de mensagens personalizado do cliente e o inspetor de mensagens personalizado do dispatcher para validar as mensagens enviadas/recebidas /do serviço. Em seguida, você pode implementar um comportamento de ponto de extremidade personalizado para habilitar a validação de mensagem no cliente e no serviço. Finalmente, você implementa um elemento de configuração personalizado na classe que permite expor o comportamento de ponto de extremidade personalizado estendido no arquivo de configuração do serviço ou cliente"</p>|

## <a id="parameters"></a>Validação de entrada de WCF por meio de Inspetores de Parâmetro

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, NET Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff647875.aspx) |
| **Etapas** | <p>A validação de dados e entrada representa uma linha de defesa importante na proteção do aplicativo WCF. Você deve validar todos os parâmetros expostos em operações de serviço WCF para proteger o serviço contra ataques de um cliente mal-intencionado. Por outro lado, você também deve validar todos os valores de retorno recebidos pelo cliente para proteg&e-lo contra ataques de um serviço mal-intencionado</p><p>O WCF fornece pontos de extensibilidade diferentes que permitem que você personalize o comportamento de tempo de execução do WCF criando extensões personalizadas. Inspetores de mensagem e inspetores de parâmetro são dois mecanismos de extensibilidade usados para obter maior controle sobre os dados que passam entre um cliente e um serviço. Você deve usar inspetores de parâmetro para validação de entrada e usar inspetores de mensagem somente quando precisar inspecionar a mensagem inteira que entra e sai de um serviço.</p><p>Para executar a validação de entrada, você criará uma classe .NET e implementará um inspetor de parâmetro personalizado para validar parâmetros nas operações no serviço. Em seguida, você implementará um comportamento de ponto de extremidade personalizado para habilitar a validação no cliente e no serviço. Finalmente, você implementará um elemento de configuração personalizado na classe que permite expor o comportamento de ponto de extremidade personalizado estendido no arquivo de configuração do serviço ou cliente</p>|
