---
title: Setting Properties in Your Provider | Microsoft Docs
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology:
- devlang-cpp
ms.tgt_pltfrm: 
ms.topic: article
dev_langs:
- C++
helpviewer_keywords:
- OLE DB providers, properties
- properties [C++], OLE DB provider
ms.assetid: 26a8b493-7ec4-4686-96d0-9ad5d2bca5ac
caps.latest.revision: 7
author: mikeblome
ms.author: mblome
manager: ghogen
translation.priority.ht:
- cs-cz
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pl-pl
- pt-br
- ru-ru
- tr-tr
- zh-cn
- zh-tw
translationtype: Human Translation
ms.sourcegitcommit: 3168772cbb7e8127523bc2fc2da5cc9b4f59beb8
ms.openlocfilehash: 86c14f98c30b251a2d18a53dba5401e6c1023dfd

---
# Setting Properties in Your Provider
Find the property group and property ID for the property you want. For more information, see [OLE DB Properties](https://msdn.microsoft.com/en-us/library/ms722734.aspx) in the *OLE DB Programmer's Reference*.  
  
 In the provider code generated by the wizard, find the property map corresponding to the property group. The name of the property group usually corresponds to the name of the object. Command and rowset properties can be found in the command or rowset; data source and initialization properties can be found in the data source object.  
  
 In the property map, add a [PROPERTY_INFO_ENTRY_EX](../../data/oledb/property-info-entry-ex.md) macro. PROPERTY_INFO_ENTRY_EX takes four parameters:  
  
-   The property ID corresponding to your property. You must remove the first seven characters ("DBPROP_") from the front of the property name. For example, if you want to add **DBPROP_MAXROWS**, pass `MAXROWS` as the first element. If this is a custom property, pass the full GUID name (for example, `DBMYPROP_MYPROPERTY`).  
  
-   The variant type of the property (in [OLE DB Properties](https://msdn.microsoft.com/en-us/library/ms722734.aspx) in the *OLE DB Programmer's Reference*). Enter the **VT_** type (such as `VT_BOOL` or `VT_I2`) corresponding to the data type.  
  
-   Flags to indicate whether the property is readable and writable and the group to which it belongs. For example, the following code indicates a read/write property belonging to the rowset group:  
  
    ```  
    DBPROPFLAGS_ROWSET | DBPROPFLAGS_READ | DBPROPFLAGS_WRITE  
    ```  
  
-   The base value of the property. This might be **VARIANT_FALSE** for a Boolean type or zero for an integer type, for example. The property has this value unless it is changed.  
  
    > [!NOTE]
    >  Some properties are connected or chained to other properties, such as bookmarks or updating. When a consumer sets one property to true, another property might also be set. The OLE DB provider templates support this through the method [CUtlProps::OnPropertyChanged](../../data/oledb/cutlprops-onpropertychanged.md).  
  
## Properties Ignored by Microsoft OLE DB Providers  
 The Microsoft OLE DB Providers ignore the following OLE DB properties:  
  
-   **DBPROP_MAXROWS** only works for read-only providers (that is, where DBPROP_IRowsetChange and DBPROP_IRowsetUpdate are false); otherwise this property is not supported.  
  
-   **DBPROP_MAXPENDINGROWS** is ignored; the provider specifies its own limit.  
  
-   **DBPROP_MAXOPENROWS** is ignored; the provider specifies its own limit.  
  
-   **DBPROP_CANHOLDROWS** is ignored; the provider specifies its own limit.  
  
## See Also  
 [Working with OLE DB Provider Templates](../../data/oledb/working-with-ole-db-provider-templates.md)


<!--HONumber=Jan17_HO2-->


