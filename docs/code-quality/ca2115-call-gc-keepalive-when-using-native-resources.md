---
title: "CA2115: Call GC.KeepAlive when using native resources | Microsoft Docs"
ms.custom: ""
ms.date: "11/04/2016"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "vs-devops-test"
ms.tgt_pltfrm: ""
ms.topic: "article"
f1_keywords: 
  - "CallGCKeepAliveWhenUsingNativeResources"
  - "CA2115"
helpviewer_keywords: 
  - "CA2115"
  - "CallGCKeepAliveWhenUsingNativeResources"
ms.assetid: f00a59a7-2c6a-4bbe-a1b3-7bf77d366f34
caps.latest.revision: 18
author: "gewarren"
ms.author: "gewarren"
manager: "ghogen"
translation.priority.ht: 
  - "de-de"
  - "es-es"
  - "fr-fr"
  - "it-it"
  - "ja-jp"
  - "ko-kr"
  - "ru-ru"
  - "zh-cn"
  - "zh-tw"
translation.priority.mt: 
  - "cs-cz"
  - "pl-pl"
  - "pt-br"
  - "tr-tr"
---
# CA2115: Call GC.KeepAlive when using native resources
|||  
|-|-|  
|TypeName|CallGCKeepAliveWhenUsingNativeResources|  
|CheckId|CA2115|  
|Category|Microsoft.Security|  
|Breaking Change|Non Breaking|  
  
## Cause  
 A method declared in a type with a finalizer references a <xref:System.IntPtr?displayProperty=fullName> or <xref:System.UIntPtr?displayProperty=fullName> field, but does not call <xref:System.GC.KeepAlive%2A?displayProperty=fullName>.  
  
## Rule Description  
 Garbage collection finalizes an object if there are no more references to it in managed code. Unmanaged references to objects do not prevent garbage collection. This rule detects errors that might occur because an unmanaged resource is being finalized while it is still being used in unmanaged code.  
  
 This rule assumes that <xref:System.IntPtr> and <xref:System.UIntPtr> fields store pointers to unmanaged resources. Because the purpose of a finalizer is to free unmanaged resources, the rule assumes that the finalizer will free the unmanaged resource pointed to by the pointer fields. This rule also assumes that the method is referencing the pointer field to pass the unmanaged resource to unmanaged code.  
  
## How to Fix Violations  
 To fix a violation of this rule, add a call to <xref:System.GC.KeepAlive%2A> to the method, passing the current instance (`this` in C# and C++) as the argument. Position the call after the last line of code where the object must be protected from garbage collection. Immediately after the call to <xref:System.GC.KeepAlive%2A>, the object is again considered ready for garbage collection assuming that there are no managed references to it.  
  
## When to Suppress Warnings  
 This rule makes some assumptions that can lead to false positives. You can safely suppress a warning from this rule if:  
  
-   The finalizer does not free the contents of the <xref:System.IntPtr> or <xref:System.UIntPtr> field referenced by the method.  
  
-   The method does not pass the <xref:System.IntPtr> or <xref:System.UIntPtr> field to unmanaged code.  
  
 Carefully review other messages before excluding them. This rule detects errors that are difficult to reproduce and debug.  
  
## Example  
 In the following example, `BadMethod` does not include a call to `GC.KeepAlive` and therefore violates the rule. `GoodMethod` contains the corrected code.  
  
> [!NOTE]
>  This example is pseudo-code Although the code compiles and runs, the warning is not fired because an unmanaged resource is not created or freed.  
  
 [!code-csharp[FxCop.Security.IntptrAndFinalize#1](../code-quality/codesnippet/CSharp/ca2115-call-gc-keepalive-when-using-native-resources_1.cs)]  
  
## See Also  
 <xref:System.GC.KeepAlive%2A?displayProperty=fullName>   
 <xref:System.IntPtr?displayProperty=fullName>   
 <xref:System.Object.Finalize%2A?displayProperty=fullName>   
 <xref:System.UIntPtr?displayProperty=fullName>   
 [Dispose Pattern](/dotnet/standard/design-guidelines/dispose-pattern)