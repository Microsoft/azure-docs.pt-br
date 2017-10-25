---
title: "Exibir o SAML retornado pelo Serviço de Controle de Acesso (Java)"
description: "Saiba como exibir o SAML retornado pelo serviço de controle de acesso em aplicativos Java hospedados no Azure."
services: active-directory
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 6cd216f9-eb43-46b4-b30d-f194d0ae2d48
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.custom: aaddev
ms.openlocfilehash: 1552e624a4703138ab82f7133ceaec3dbd04e1db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-view-saml-returned-by-the-azure-access-control-service"></a>Como exibir o SAML retornado pelo Access Control Service do Azure
Este guia mostrará como exibir o SAML adjacente retornado a seu aplicativo pelo ACS (Serviço de Controle de Acesso) do Azure. O guia baseia-se no tópico [Como autenticar usuários da Web com o Serviço de Controle de Acesso do Azure usando o Eclipse](active-directory-java-authenticate-users-access-control-eclipse.md), fornecendo um código que exibe as informações de SAML. O aplicativo concluído será semelhante ao seguinte.

![Saída do SAML de exemplo][saml_output]

Para obter mais informações sobre o ACS, consulte a seção [Próximas etapas](#next_steps) .

> [!NOTE]
> O Filtro de Controle dos Serviços de Acesso do Azure é uma visualização de tecnologia da comunidade. Como software de pré-lançamento, a Microsoft não dá suporte formal a ele.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Para executar as tarefas deste guia, conclua o exemplo em [Como autenticar usuários da Web com o Serviço de Controle de Acesso do Azure usando o Eclipse](active-directory-java-authenticate-users-access-control-eclipse.md) e use-o como ponto de partida deste tutorial.

## <a name="add-the-jspwriter-library-to-your-build-path-and-deployment-assembly"></a>Adicionar a biblioteca JspWriter a seu caminho de compilação e assembly de implantação
Adicione a biblioteca que contém a classe **javax.servlet.jsp.JspWriter** ao caminho de compilação e ao assembly de implantação. Se você estiver usando o Tomcat, a biblioteca será **jsp-api.jar**, localizada na pasta **lib** do Apache.

1. No Explorador de Projeto do Eclipse, clique com o botão direito em **MyACSHelloWorld**, clique em **Caminho de Compilação**, clique em **Configurar Caminho de Compilação**, clique na guia **Bibliotecas** e, em seguida, clique em **Adicionar JARs Externos**.
2. No diálogo **Seleção de JAR**, navegue até o JAR necessário, selecione-o e, em seguida, clique em **Abrir**.
3. Com o diálogo **Propriedades de MyACSHelloWorld** ainda aberto, clique em **Assembly de Implantação**.
4. Na caixa de diálogo **Assembly de Implantação Web**, clique em **Adicionar**.
5. No diálogo **Nova Diretiva de Assembly**, clique em **Entradas do Caminho de Compilação Java** e, em seguida, clique em **Avançar**.
6. Selecione a biblioteca apropriada e clique em **Concluir**.
7. Clique em **OK** para fechar o diálogo **Propriedades de MyACSHelloWorld**.

## <a name="modify-the-jsp-file-to-display-saml"></a>Modificar o arquivo JSP para exibir o XAML
Modifique o **index.jsp** para usar o código a seguir.

    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
        <%@ page import="javax.xml.parsers.*"
                 import="javax.xml.transform.*"
                 import="org.w3c.dom.*"
                 import="java.io.*"
                 import="javax.xml.transform.stream.*"
                 import="javax.xml.transform.dom.*"
                 import="javax.xml.xpath.*"
                 import="javax.servlet.jsp.JspWriter" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Sample ACS Filter</title>
    </head>
    <body>
        <h3>SAML information from sample ACS program</h3>
        <%!
        void displaySAMLInfo(Node node, String parent, JspWriter out)
        {

            try
            {
                String nodeName;
                int nChild, i;

                nodeName = node.getNodeName();
                out.println("<br>");
                out.println("<u>Examining <b>" + parent + nodeName + "</b></u><br>");

                   // Attributes.
                   NamedNodeMap attribsMap = node.getAttributes();
                   if (null != attribsMap)
                   {
                         for (i=0; i < attribsMap.getLength(); i++)
                         {
                                Node attrib = attribsMap.item(i);
                                out.println("Attribute: <b>" + attrib.getNodeName() + "</b>: " + attrib.getNodeValue()  + "<br>");
                         }
                   }

                   // Child nodes.
                   NodeList list = node.getChildNodes();
                   if (null != list)
                    {
                          nChild = list.getLength();
                          if (nChild > 0)
                          {                    

                                 // If it is a text node, just print the text.
                                 if (list.item(0).getNodeName() == "#text")
                                 {
                                     out.println("Text value: <b>" + list.item(0).getTextContent() + "</b><br>");
                                 }
                                 else
                                 {
                                     // Print out the child node names.
                                     out.print("Contains " + nChild + " child node(s): ");   
                                        for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);

                                        out.print("<b>" + temp.getNodeName() + "</b>");
                                        if (i < nChild - 1)
                                        {
                                            // Separate the names.
                                            out.print(", ");
                                        }
                                        else
                                        {
                                            // Finish the sentence.
                                            out.print(".");
                                        }

                                     }
                                     out.println("<br>");

                                     // Process the child nodes.
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        displaySAMLInfo(temp, parent + nodeName + "\\", out);
                                     }
                               }
                          }
                      }
                  }
                catch (Exception e)
                {
                    System.out.println("Exception encountered.");
                    e.printStackTrace();            
                }
            }
        %>

        <%
        try
        {
            String data  = (String) request.getAttribute("ACSSAML");

            DocumentBuilder docBuilder;
            Document doc = null;
            DocumentBuilderFactory docBuilderFactory = DocumentBuilderFactory.newInstance();
            docBuilderFactory.setIgnoringElementContentWhitespace(true);
            docBuilder = docBuilderFactory.newDocumentBuilder();
            byte[] xmlDATA = data.getBytes();

            ByteArrayInputStream in = new ByteArrayInputStream(xmlDATA);
            doc = docBuilder.parse(in);
            doc.getDocumentElement().normalize();

            // Iterate the child nodes of the doc.
            NodeList list = doc.getChildNodes();

            for (int i=0; i < list.getLength(); i++)
            {
                displaySAMLInfo(list.item(i), "", out);
            }
        }
        catch (Exception e)
        {
            out.println("Exception encountered.");
            e.printStackTrace();
        }

        %>
    </body>
    </html>

## <a name="run-the-application"></a>Executar o aplicativo
1. Execute o aplicativo no emulador do computador ou implante no Azure, usando as etapas documentadas em [Como autenticar usuários da Web com o Serviço de Controle de Acesso do Azure usando o Eclipse](active-directory-java-authenticate-users-access-control-eclipse.md).
2. Inicie um navegador e abra o aplicativo web. Depois de fazer logon em seu aplicativo, você verá informações de SAML, incluindo a declaração de segurança fornecida pelo provedor de identidade.

## <a name="next-steps"></a>Próximas etapas
Para explorar melhor a funcionalidade do ACS e experimentar mais cenários sofisticados, consulte [Serviço de Controle de Acesso 2.0][Access Control Service 2.0].

[Prerequisites]: #pre
[Modify the JSP file to display SAML]: #modify_jsp
[Add the JspWriter library to your build path and deployment assembly]: #add_library
[Run the application]: #run_application
[Next steps]: #next_steps
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[How to Authenticate Web Users with Azure Access Control Service Using Eclipse]: active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png
