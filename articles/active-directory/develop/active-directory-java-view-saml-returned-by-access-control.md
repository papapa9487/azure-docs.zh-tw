---
title: "檢視存取控制服務 (Java) 所傳回的 SAML"
description: "了解如何在裝載於 Azure 上的 Java 應用程式中，檢視存取控制服務所傳回的 SAML。"
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
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-view-saml-returned-by-the-azure-access-control-service"></a>如何檢視 Azure 存取控制服務傳回的 SAML
本指南說明如何檢視 Azure 存取控制服務 (ACS) 傳回給應用程式的基本安全性聲明標記語言 (SAML)。 本指南是以[如何使用 Eclipse 搭配 Azure 存取控制服務來驗證 Web 使用者](active-directory-java-authenticate-users-access-control-eclipse.md)主題為基礎，提供可顯示 SAML 資訊的程式碼。 完成後的應用程式如下所示：

![Example SAML output][saml_output]

如需 ACS 的詳細資訊，請參閱 [後續步驟](#next_steps) 一節。

> [!NOTE]
> Azure Access Services Control Filter 是社群技術預覽。 由於是發行前軟體，Microsoft 尚未提供正式支援。
> 
> 

## <a name="prerequisites"></a>必要條件
若要完成本指南中的工作，請完成[如何使用 Eclipse 搭配 Azure 存取控制服務來驗證 Web 使用者](active-directory-java-authenticate-users-access-control-eclipse.md)中的範例，並使用它作為本教學課程的起點。

## <a name="add-the-jspwriter-library-to-your-build-path-and-deployment-assembly"></a>將 JspWriter 程式庫加入至組建路徑和部署組件
將含有 **javax.servlet.jsp.JspWriter** 類別的程式庫加入至組建路徑和部署組件。 如果使用的是 Tomcat，則程式庫會是位於 Apache [lib] 資料夾中的 **jsp-api.jar**。

1. 在 Eclipse 的 [Project Explorer] \(專案總管) 中，於 [MyACSHelloWorld] 上按一下滑鼠右鍵，然後依序按一下 [Build Path] \(組建路徑)、[Configure Build Path] \(設定組建路徑)、[Libraries] \(程式庫) 索引標籤，以及 [Add External JARs] \(新增外部 JAR)。
2. 在 JAR Selection \(JAR 選擇) 對話方塊中，瀏覽至所需的 JAR 並選取它，然後按一下Open \(開啟)。
3. 在 [Properties for MyACSHelloWorld] \(MyACSHelloWorld 的屬性) 對話方塊仍開啟的情況下，按一下 [Deployment Assembly] \(部署組件)。
4. 在 [Web Deployment Assembly] \(Web 部署組件) 對話方塊中，按一下 [Add] \(加入)。
5. 在 [New Assembly Directive] \(新增組件指示詞) 對話方塊中，按一下 [Java Build Path Entries] \(Java 組建路徑項目)，然後按 [Next] \(下一步)。
6. 選取適當的程式庫，然後按一下 [完成] 。
7. 按一下 [OK] \(確定) 以關閉 [Properties for MyACSHelloWorld] \(MyACSHelloWorld 的屬性) 對話方塊。

## <a name="modify-the-jsp-file-to-display-saml"></a>修改 JSP 檔案來顯示 SAML
修改 **index.jsp** 來使用下列程式碼。

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

## <a name="run-the-application"></a>執行應用程式
1. 使用[如何使用 Eclipse 搭配 Azure 存取控制服務來驗證 Web 使用者](active-directory-java-authenticate-users-access-control-eclipse.md)中記載的步驟，在電腦模擬器中執行您的應用程式，或部署到 Azure。
2. 啟動瀏覽器並開啟您的 Web 應用程式。 登入應用程式之後，您會看到 SAML 資訊，包括身分識別提供者所提供的安全性聲明。

## <a name="next-steps"></a>後續步驟
若要進一步探索 ACS 功能及試試其他更精緻的案例，請參閱 [存取控制服務 2.0][Access Control Service 2.0]。

[Prerequisites]: #pre
[Modify the JSP file to display SAML]: #modify_jsp
[Add the JspWriter library to your build path and deployment assembly]: #add_library
[Run the application]: #run_application
[Next steps]: #next_steps
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[How to Authenticate Web Users with Azure Access Control Service Using Eclipse]: active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png
