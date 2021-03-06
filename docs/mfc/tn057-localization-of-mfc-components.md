---
title: 'TN057: Localization of MFC Components | Microsoft Docs'
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology:
- devlang-cpp
ms.tgt_pltfrm: 
ms.topic: article
f1_keywords:
- vc.mfc.components
dev_langs:
- C++
helpviewer_keywords:
- components [MFC], localizing
- TN057
- resources [MFC], localization
- localization [C++], MFC resources
- localization [C++], MFC components
- MFC DLLs [C++], localizing
- DLLs [C++], localizing MFC
- localization [C++], resources
ms.assetid: 5376d329-bd45-41bd-97f5-3d895a9a0af5
caps.latest.revision: 10
author: mikeblome
ms.author: mblome
manager: ghogen
translation.priority.ht:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- ru-ru
- zh-cn
- zh-tw
translation.priority.mt:
- cs-cz
- pl-pl
- pt-br
- tr-tr
translationtype: Machine Translation
ms.sourcegitcommit: 3168772cbb7e8127523bc2fc2da5cc9b4f59beb8
ms.openlocfilehash: 26abc7cd68a262402bb347f2053a65b3c61da572

---
# TN057: Localization of MFC Components
> [!NOTE]
>  The following technical note has not been updated since it was first included in the online documentation. As a result, some procedures and topics might be out of date or incorrect. For the latest information, it is recommended that you search for the topic of interest in the online documentation index.  
  
 This note describes some of the designs and procedures you can use to localize your component, if it an application or an OLE control or a DLL that uses MFC.  
  
## Overview  
 There are really two issues to resolve when localizing a component that uses MFC. First, you must localize your own resources — strings, dialogs, and other resources that are specific to your component. Most components built using MFC also include and use a number of resources that are defined by MFC. You must provide localized MFC resources as well. Fortunately, several languages are already provided by MFC itself.  
  
 In addition, your component should be prepared to run in its target environment (European or DBCS-enabled environment). For the most part, this depends on your application treating characters with the high bit set correctly and handling strings with double byte characters. MFC is enabled, by default, for both of these environments, such that it is possible to have a single worldwide binary that is used on all platforms with just different resources plugged in at setup time.  
  
## Localizing your Component's Resources  
 Localizing your application or DLL should involve simply replacing the resources with resources that match the target language. For your own resources, this is relatively simple: edit the resources in the resource editor and build your application. If your code is written properly there will be no strings or text that you wish to localize hard-coded into your C++ source code – all localization can be done by simply modifying resources. In fact, you can implement your component such that all providing a localized version does not even involve a build of the original code. This is more complex, but is well worth it and is the mechanism chosen for MFC itself. It is also possible to localize an application by loading the EXE or DLL file into the resource editor and editing the resources directly. While possible, it requires reapplication of those changes each time you build a new version of your application.  
  
 One way to avoid that is to locate all resources in a separate DLL, sometimes called a satellite DLL. This DLL is then loaded dynamically at runtime and the resources are loaded from that DLL instead of from the main module with all your code. MFC directly supports this approach. Consider an application called MYAPP.EXE; it could have all of its resources located in a DLL called MYRES.DLL. In the application's `InitInstance` it would perform the following to load that DLL and cause MFC to load resources from that location:  
  
```  
CMyApp::InitInstance()  
{ *// one of the first things in the init code  
    HINSTANCE hInst = LoadLibrary("myres.dll");

    if (hInst != NULL)  
    AfxSetResourceHandle(hInst);

 *// other initialization code would follow  
 .  
 .  
 .  
}  
```  
  
 From then on, MFC will load resources from that DLL instead of from myapp.exe. All resources, however, must be present in that DLL; MFC will not search the application's instance in search of a given resource. This technique applies equally well to regular DLLs as well as OLE Controls. Your setup program would copy the appropriate version of MYRES.DLL depending upon which resource locale the user would like.  
  
 It is relatively easy to create a resource only DLL. You create a DLL project, add your .RC file to it, and add the necessary resources. If you have an existing project that does not use this technique, you can copy the resources from that project. After adding the resource file to the project, you are almost ready to build the project. The only thing you must do is set the linker options to include **/NOENTRY**. This tells the linker that the DLL has no entry point – since it has no code, it has no entry point.  
  
> [!NOTE]
>  The resource editor in Visual C++ 4.0 and later supports multiple languages per .RC file. This can make it very easy to manage your localization in a single project. The resources for each language are controlled by preprocessor directives generated by the resource editor.  
  
## Using the Provided MFC Localized Resources  
 Any MFC application that you build reuses two things from MFC: code and resources. That is, MFC has various error messages, built-in dialogs, and other resources that are used by the MFC classes. In order to completely localize your application, you need to localize not only your application's resources, but also the resources that come directly from MFC. MFC provides a number of different language resource files automatically, so that if the language you are targeting is one of the languages MFC already supports, you just need to make sure you use those localized resources.  
  
 As of this writing, MFC supports Chinese, German, Spanish, French, Italian, Japanese, and Korean. The files which contain these localized versions are in the MFC\INCLUDE\L.* (the 'L' stands for localized) directories. The German files are in MFC\INCLUDE\L.DEU, for example. To cause your application to use these RC files instead of the files located in MFC\INCLUDE, add a `/IC:\PROGRAM FILES\MICROSOFT VISUAL STUDIO .NET 2003\VC7\MFC\INCLUDE\L.DEU` to your RC command line (this is just an example; you would need to substitute your locale of choice as well as the directory into which you installed Visual C++).  
  
 The above instructions will work if your application links statically with MFC. Most applications link dynamically (because that is the AppWizard default). In this scenario, not only the code is dynamically linked – so are the resources. As a result, you can localize your resources in your application, but the MFC implementation resources will still be loaded from the MFC7x.DLL (or a later version) or from MFC7xLOC.DLL if it exists. You can approach this from two different angles.  
  
 The more complex approach is to ship one of the localized MFC7xLOC.DLLs (such as MFC7xDEU, for German, MFC7xESP.DLL for Spanish, etc.), or a later version, and install the appropriate MFC7xLOC.DLL into the system directory when the user installs your application. This can be very complex for both the developer and the end user and as such is not recommended. See [Technical Note 56](../mfc/tn056-installation-of-localized-mfc-components.md) for more information on this technique and its caveats.  
  
 The simplest and safest approach is to include the localized MFC resources in your application or DLL itself (or its satellite DLL if you are using one). This avoids the problems of installing MFC7xLOC.DLL properly. To do so, you follow the same instructions for the static case given above (setting the RC command line properly to point to the localized resources), except that you must also remove the `/D_AFXDLL` define that was added by AppWizard. When `/D_AFXDLL` is defined, AFXRES.H (and the other MFC RC files) do not actually define any resources (because they will be pulled from the MFC DLLs instead).  
  
## See Also  
 [Technical Notes by Number](../mfc/technical-notes-by-number.md)   
 [Technical Notes by Category](../mfc/technical-notes-by-category.md)




<!--HONumber=Jan17_HO2-->


