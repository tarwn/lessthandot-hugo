---
title: The mystery of Clay and Anonymous types in VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2011-05-08T04:38:00+00:00
ID: 1163
excerpt: |
  Introduction
  
  Yesterday I made a post about using Clay with VB.Net. Clay works but not all functionality we have in C# work in VB.Net, which is mildly frustrating. To see if I could find a solution I posted a question on StackOverflow. But no solution&hellip;
url: /index.php/desktopdev/mstech/the-mystery-of-clay-and/
views:
  - 6408
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - clay
  - vb.net

---
## Introduction

Yesterday I made a post about using [Clay with VB.Net][1]. Clay works but not all functionality we have in C# work in VB.Net, which is mildly frustrating. To see if I could find a solution I [posted a question on StackOverflow][2]. But no solution yet. 

## The problem

This works in C#

```text
dynamic c = new ClayFactory();
        var plant = c.Plant(new {LatinName = "test"});
        Console.WriteLine(plant.LatinName);
        Console.ReadLine();```
but this does not work in VB.Net

```vbnet
Dim c As Object = New ClayFactory
        Dim plant = c.Plant(New With {.LatinName = "test"})
        Console.WriteLine(plant.LatinName)
        Console.ReadLine()```
I get this error message in VB.Net:

> Cannot close over byref parameter
  
> &#8216;$arg1&#8217; referenced in lambda &#8221;

I&#8217;m not 100% sure how to solve this if I can even solve it. I&#8217;m guessing the VB.Net implementation of anonymous types is slightly different.

I get the error on this line:

```vbnet
Dim plant = c.Plant(New With {.LatinName = "test"})```
I would appreciate if someone could explain this to me.

The IL seems to be quit different.

For VB the private field Latinname is this:

> .field private initonly !T0 $LatinName

For C# it is:

> <code class="codespan">.field private initonly !'&lt;latinname&gt;j__TPar' '&lt;latinname&gt;i__Field'&lt;br />
.custom instance void [mscorlib]System.Diagnostics.DebuggerBrowsableAttribute::.ctor(valuetype [mscorlib]System.Diagnostics.DebuggerBrowsableState) = ( 01 00 00 00 00 00 00 00 ) &lt;/latinname&gt;&lt;/latinname&gt;</code>

And the public method Get_LatinName is this.

VB:

> <code class="codespan">ce !T0  get_LatinName() cil managed&lt;br />
{&lt;br />
  .custom instance void [mscorlib]System.Diagnostics.DebuggerNonUserCodeAttribute::.ctor() = ( 01 00 00 00 )&lt;br />
  // Code size       11 (0xb)&lt;br />
  .maxstack  1&lt;br />
  .locals init (!T0 V_0)&lt;br />
  IL_0000:  ldarg.0&lt;br />
  IL_0001:  ldfld      !0 class VB$AnonymousType_0`1::$LatinName&lt;br />
  IL_0006:  stloc.0&lt;br />
  IL_0007:  br.s       IL_0009&lt;br />
  IL_0009:  ldloc.0&lt;br />
  IL_000a:  ret&lt;br />
} // end of method VB$AnonymousType_0`1::get_LatinName</code>

C#:

>     <code class="codespan">.method public hidebysig specialname instance !'j__TPar'&lt;br />
        get_LatinName() cil managed&lt;br />
{&lt;br />
  // Code size       11 (0xb)&lt;br />
  .maxstack  1&lt;br />
  .locals init (!'j__TPar' V_0)&lt;br />
  IL_0000:  ldarg.0&lt;br />
  IL_0001:  ldfld      !0 class 'f__AnonymousType0`1'j__TPar'&gt;::'i__Field'&lt;br />
  IL_0006:  stloc.0&lt;br />
  IL_0007:  br.s       IL_0009&lt;br />
  IL_0009:  ldloc.0&lt;br />
  IL_000a:  ret&lt;br />
} // end of method 'f__AnonymousType0`1'::get_LatinName</code>

And these are the main methods:

VB:

>     <code class="codespan">.method public static void  Main() cil managed&lt;br />
{&lt;br />
  .entrypoint&lt;br />
  .custom instance void [mscorlib]System.STAThreadAttribute::.ctor() = ( 01 00 00 00 )&lt;br />
  // Code size       90 (0x5a)&lt;br />
  .maxstack  7&lt;br />
  .locals init ([0] object c,&lt;br />
           [1] object plant,&lt;br />
           [2] class VB$AnonymousType_0`1 VB$t_ref$S0,&lt;br />
           [3] object[] VB$t_array$S0)&lt;br />
  IL_0000:  nop&lt;br />
  IL_0001:  newobj     instance void [ClaySharp]ClaySharp.ClayFactory::.ctor()&lt;br />
  IL_0006:  stloc.0&lt;br />
  IL_0007:  ldloc.0&lt;br />
  IL_0008:  ldnull&lt;br />
  IL_0009:  ldstr      "Plant"&lt;br />
  IL_000e:  ldc.i4.1&lt;br />
  IL_000f:  newarr     [mscorlib]System.Object&lt;br />
  IL_0014:  stloc.3&lt;br />
  IL_0015:  ldloc.3&lt;br />
  IL_0016:  ldc.i4.0&lt;br />
  IL_0017:  ldstr      "test"&lt;br />
  IL_001c:  newobj     instance void class VB$AnonymousType_0`1::.ctor(!0)&lt;br />
  IL_0021:  stelem.ref&lt;br />
  IL_0022:  nop&lt;br />
  IL_0023:  ldloc.3&lt;br />
  IL_0024:  ldnull&lt;br />
  IL_0025:  ldnull&lt;br />
  IL_0026:  ldnull&lt;br />
  IL_0027:  call       object [Microsoft.VisualBasic]Microsoft.VisualBasic.CompilerServices.NewLateBinding::LateGet(object,&lt;br />
                                                                                                                    class [mscorlib]System.Type,&lt;br />
                                                                                                                    string,&lt;br />
                                                                                                                    object[],&lt;br />
                                                                                                                    string[],&lt;br />
                                                                                                                    class [mscorlib]System.Type[],&lt;br />
                                                                                                                    bool[])&lt;br />
  IL_002c:  call       object [mscorlib]System.Runtime.CompilerServices.RuntimeHelpers::GetObjectValue(object)&lt;br />
  IL_0031:  stloc.1&lt;br />
  IL_0032:  ldloc.1&lt;br />
  IL_0033:  ldnull&lt;br />
  IL_0034:  ldstr      "LatinName"&lt;br />
  IL_0039:  ldc.i4.0&lt;br />
  IL_003a:  newarr     [mscorlib]System.Object&lt;br />
  IL_003f:  ldnull&lt;br />
  IL_0040:  ldnull&lt;br />
  IL_0041:  ldnull&lt;br />
  IL_0042:  call       object [Microsoft.VisualBasic]Microsoft.VisualBasic.CompilerServices.NewLateBinding::LateGet(object,&lt;br />
                                                                                                                    class [mscorlib]System.Type,&lt;br />
                                                                                                                    string,&lt;br />
                                                                                                                    object[],&lt;br />
                                                                                                                    string[],&lt;br />
                                                                                                                    class [mscorlib]System.Type[],&lt;br />
                                                                                                                    bool[])&lt;br />
  IL_0047:  call       object [mscorlib]System.Runtime.CompilerServices.RuntimeHelpers::GetObjectValue(object)&lt;br />
  IL_004c:  call       void [mscorlib]System.Console::WriteLine(object)&lt;br />
  IL_0051:  nop&lt;br />
  IL_0052:  call       string [mscorlib]System.Console::ReadLine()&lt;br />
  IL_0057:  pop&lt;br />
  IL_0058:  nop&lt;br />
  IL_0059:  ret&lt;br />
} // end of method Module1::Main</code>

C#:

>     <code class="codespan">.method private hidebysig static void  Main(string[] args) cil managed&lt;br />
{&lt;br />
  .entrypoint&lt;br />
  // Code size       299 (0x12b)&lt;br />
  .maxstack  10&lt;br />
  .locals init ([0] object c,&lt;br />
           [1] object plant,&lt;br />
           [2] class [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo[] CS$0$0000)&lt;br />
  IL_0000:  nop&lt;br />
  IL_0001:  newobj     instance void [ClaySharp]ClaySharp.ClayFactory::.ctor()&lt;br />
  IL_0006:  stloc.0&lt;br />
  IL_0007:  ldsfld     class [System.Core]System.Runtime.CompilerServices.CallSite`1f__AnonymousType0`1',object&gt;&gt; ConsoleApplication2.Program/'o__SiteContainer0'::'p__Site1'&lt;br />
  IL_000c:  brtrue.s   IL_004c&lt;br />
  IL_000e:  ldc.i4.0&lt;br />
  IL_000f:  ldstr      "Plant"&lt;br />
  IL_0014:  ldnull&lt;br />
  IL_0015:  ldtoken    ConsoleApplication2.Program&lt;br />
  IL_001a:  call       class [mscorlib]System.Type [mscorlib]System.Type::GetTypeFromHandle(valuetype [mscorlib]System.RuntimeTypeHandle)&lt;br />
  IL_001f:  ldc.i4.2&lt;br />
  IL_0020:  newarr     [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo&lt;br />
  IL_0025:  stloc.2&lt;br />
  IL_0026:  ldloc.2&lt;br />
  IL_0027:  ldc.i4.0&lt;br />
  IL_0028:  ldc.i4.0&lt;br />
  IL_0029:  ldnull&lt;br />
  IL_002a:  call       class [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo::Create(valuetype [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfoFlags,&lt;br />
                                                                                                                                                                             string)&lt;br />
  IL_002f:  stelem.ref&lt;br />
  IL_0030:  ldloc.2&lt;br />
  IL_0031:  ldc.i4.1&lt;br />
  IL_0032:  ldc.i4.1&lt;br />
  IL_0033:  ldnull&lt;br />
  IL_0034:  call       class [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo::Create(valuetype [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfoFlags,&lt;br />
                                                                                                                                                                             string)&lt;br />
  IL_0039:  stelem.ref&lt;br />
  IL_003a:  ldloc.2&lt;br />
  IL_003b:  call       class [System.Core]System.Runtime.CompilerServices.CallSiteBinder [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.Binder::InvokeMember(valuetype [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpBinderFlags,&lt;br />
                                                                                                                                                               string,&lt;br />
                                                                                                                                                               class [mscorlib]System.Collections.Generic.IEnumerable`1,&lt;br />
                                                                                                                                                               class [mscorlib]System.Type,&lt;br />
                                                                                                                                                               class [mscorlib]System.Collections.Generic.IEnumerable`1)&lt;br />
  IL_0040:  call       class [System.Core]System.Runtime.CompilerServices.CallSite`1 class [System.Core]System.Runtime.CompilerServices.CallSite`1f__AnonymousType0`1',object&gt;&gt;::Create(class [System.Core]System.Runtime.CompilerServices.CallSiteBinder)&lt;br />
  IL_0045:  stsfld     class [System.Core]System.Runtime.CompilerServices.CallSite`1f__AnonymousType0`1',object&gt;&gt; ConsoleApplication2.Program/'o__SiteContainer0'::'p__Site1'&lt;br />
  IL_004a:  br.s       IL_004c&lt;br />
  IL_004c:  ldsfld     class [System.Core]System.Runtime.CompilerServices.CallSite`1f__AnonymousType0`1',object&gt;&gt; ConsoleApplication2.Program/'o__SiteContainer0'::'p__Site1'&lt;br />
  IL_0051:  ldfld      !0 class [System.Core]System.Runtime.CompilerServices.CallSite`1f__AnonymousType0`1',object&gt;&gt;::Target&lt;br />
  IL_0056:  ldsfld     class [System.Core]System.Runtime.CompilerServices.CallSite`1f__AnonymousType0`1',object&gt;&gt; ConsoleApplication2.Program/'o__SiteContainer0'::'p__Site1'&lt;br />
  IL_005b:  ldloc.0&lt;br />
  IL_005c:  ldstr      "test"&lt;br />
  IL_0061:  newobj     instance void class 'f__AnonymousType0`1'::.ctor(!0)&lt;br />
  IL_0066:  callvirt   instance !3 class [mscorlib]System.Func`4f__AnonymousType0`1',object&gt;::Invoke(!0,&lt;br />
                                                                                                                                                                                          !1,&lt;br />
                                                                                                                                                                                          !2)&lt;br />
  IL_006b:  stloc.1&lt;br />
  IL_006c:  ldsfld     class [System.Core]System.Runtime.CompilerServices.CallSite`1&gt; ConsoleApplication2.Program/'o__SiteContainer0'::'p__Site2'&lt;br />
  IL_0071:  brtrue.s   IL_00b6&lt;br />
  IL_0073:  ldc.i4     0x100&lt;br />
  IL_0078:  ldstr      "WriteLine"&lt;br />
  IL_007d:  ldnull&lt;br />
  IL_007e:  ldtoken    ConsoleApplication2.Program&lt;br />
  IL_0083:  call       class [mscorlib]System.Type [mscorlib]System.Type::GetTypeFromHandle(valuetype [mscorlib]System.RuntimeTypeHandle)&lt;br />
  IL_0088:  ldc.i4.2&lt;br />
  IL_0089:  newarr     [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo&lt;br />
  IL_008e:  stloc.2&lt;br />
  IL_008f:  ldloc.2&lt;br />
  IL_0090:  ldc.i4.0&lt;br />
  IL_0091:  ldc.i4.s   33&lt;br />
  IL_0093:  ldnull&lt;br />
  IL_0094:  call       class [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo::Create(valuetype [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfoFlags,&lt;br />
                                                                                                                                                                             string)&lt;br />
  IL_0099:  stelem.ref&lt;br />
  IL_009a:  ldloc.2&lt;br />
  IL_009b:  ldc.i4.1&lt;br />
  IL_009c:  ldc.i4.0&lt;br />
  IL_009d:  ldnull&lt;br />
  IL_009e:  call       class [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo::Create(valuetype [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfoFlags,&lt;br />
                                                                                                                                                                             string)&lt;br />
  IL_00a3:  stelem.ref&lt;br />
  IL_00a4:  ldloc.2&lt;br />
  IL_00a5:  call       class [System.Core]System.Runtime.CompilerServices.CallSiteBinder [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.Binder::InvokeMember(valuetype [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpBinderFlags,&lt;br />
                                                                                                                                                               string,&lt;br />
                                                                                                                                                               class [mscorlib]System.Collections.Generic.IEnumerable`1,&lt;br />
                                                                                                                                                               class [mscorlib]System.Type,&lt;br />
                                                                                                                                                               class [mscorlib]System.Collections.Generic.IEnumerable`1)&lt;br />
  IL_00aa:  call       class [System.Core]System.Runtime.CompilerServices.CallSite`1 class [System.Core]System.Runtime.CompilerServices.CallSite`1&gt;::Create(class [System.Core]System.Runtime.CompilerServices.CallSiteBinder)&lt;br />
  IL_00af:  stsfld     class [System.Core]System.Runtime.CompilerServices.CallSite`1&gt; ConsoleApplication2.Program/'o__SiteContainer0'::'p__Site2'&lt;br />
  IL_00b4:  br.s       IL_00b6&lt;br />
  IL_00b6:  ldsfld     class [System.Core]System.Runtime.CompilerServices.CallSite`1&gt; ConsoleApplication2.Program/'o__SiteContainer0'::'p__Site2'&lt;br />
  IL_00bb:  ldfld      !0 class [System.Core]System.Runtime.CompilerServices.CallSite`1&gt;::Target&lt;br />
  IL_00c0:  ldsfld     class [System.Core]System.Runtime.CompilerServices.CallSite`1&gt; ConsoleApplication2.Program/'o__SiteContainer0'::'p__Site2'&lt;br />
  IL_00c5:  ldtoken    [mscorlib]System.Console&lt;br />
  IL_00ca:  call       class [mscorlib]System.Type [mscorlib]System.Type::GetTypeFromHandle(valuetype [mscorlib]System.RuntimeTypeHandle)&lt;br />
  IL_00cf:  ldsfld     class [System.Core]System.Runtime.CompilerServices.CallSite`1&gt; ConsoleApplication2.Program/'o__SiteContainer0'::'p__Site3'&lt;br />
  IL_00d4:  brtrue.s   IL_0109&lt;br />
  IL_00d6:  ldc.i4.0&lt;br />
  IL_00d7:  ldstr      "LatinName"&lt;br />
  IL_00dc:  ldtoken    ConsoleApplication2.Program&lt;br />
  IL_00e1:  call       class [mscorlib]System.Type [mscorlib]System.Type::GetTypeFromHandle(valuetype [mscorlib]System.RuntimeTypeHandle)&lt;br />
  IL_00e6:  ldc.i4.1&lt;br />
  IL_00e7:  newarr     [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo&lt;br />
  IL_00ec:  stloc.2&lt;br />
  IL_00ed:  ldloc.2&lt;br />
  IL_00ee:  ldc.i4.0&lt;br />
  IL_00ef:  ldc.i4.0&lt;br />
  IL_00f0:  ldnull&lt;br />
  IL_00f1:  call       class [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfo::Create(valuetype [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpArgumentInfoFlags,&lt;br />
                                                                                                                                                                             string)&lt;br />
  IL_00f6:  stelem.ref&lt;br />
  IL_00f7:  ldloc.2&lt;br />
  IL_00f8:  call       class [System.Core]System.Runtime.CompilerServices.CallSiteBinder [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.Binder::GetMember(valuetype [Microsoft.CSharp]Microsoft.CSharp.RuntimeBinder.CSharpBinderFlags,&lt;br />
                                                                                                                                                            string,&lt;br />
                                                                                                                                                            class [mscorlib]System.Type,&lt;br />
                                                                                                                                                            class [mscorlib]System.Collections.Generic.IEnumerable`1)&lt;br />
  IL_00fd:  call       class [System.Core]System.Runtime.CompilerServices.CallSite`1 class [System.Core]System.Runtime.CompilerServices.CallSite`1&gt;::Create(class [System.Core]System.Runtime.CompilerServices.CallSiteBinder)&lt;br />
  IL_0102:  stsfld     class [System.Core]System.Runtime.CompilerServices.CallSite`1&gt; ConsoleApplication2.Program/'o__SiteContainer0'::'p__Site3'&lt;br />
  IL_0107:  br.s       IL_0109&lt;br />
  IL_0109:  ldsfld     class [System.Core]System.Runtime.CompilerServices.CallSite`1&gt; ConsoleApplication2.Program/'o__SiteContainer0'::'p__Site3'&lt;br />
  IL_010e:  ldfld      !0 class [System.Core]System.Runtime.CompilerServices.CallSite`1&gt;::Target&lt;br />
  IL_0113:  ldsfld     class [System.Core]System.Runtime.CompilerServices.CallSite`1&gt; ConsoleApplication2.Program/'o__SiteContainer0'::'p__Site3'&lt;br />
  IL_0118:  ldloc.1&lt;br />
  IL_0119:  callvirt   instance !2 class [mscorlib]System.Func`3::Invoke(!0,&lt;br />
                                                                                                                                                    !1)&lt;br />
  IL_011e:  callvirt   instance void class [mscorlib]System.Action`3::Invoke(!0,&lt;br />
                                                                                                                                                                             !1,&lt;br />
                                                                                                                                                                             !2)&lt;br />
  IL_0123:  nop&lt;br />
  IL_0124:  call       string [mscorlib]System.Console::ReadLine()&lt;br />
  IL_0129:  pop&lt;br />
  IL_012a:  ret&lt;br />
} // end of method Program::Main</code>

The C# and VB version are nothing alike. Seems like the C# version does a lot more. So there goes the theory C# and VB.Net compile to the same IL.

I also tried debugging Clay to see which line was causing the problem on their side.

The problem is that it is building a whole expressiontree and that it is difficult to say where it actually fails.

```csharp
public override DynamicMetaObject BindInvokeMember(InvokeMemberBinder binder, DynamicMetaObject[] args) {
            Logger.Log(LogLevel.Debug, null, "BindInvokeMember");

            var argValues = Expression.NewArrayInit(typeof(object), args.Select(x =&gt; Expression.Convert(x.Expression, typeof(Object))));
            var argNames = Expression.Constant(binder.CallInfo.ArgumentNames, typeof(IEnumerable&lt;string&gt;));
            var argNamedEnumerable = Expression.Call(typeof(Arguments).GetMethod("From"), argValues, argNames);

            var binderDefault = binder.FallbackInvokeMember(this, args);

            var missingLambda = Expression.Lambda(Expression.Call(
                GetClayBehavior(),
                IClayBehavior_InvokeMemberMissing,
                Expression.Lambda(binderDefault.Expression),
                GetLimitedSelf(),
                Expression.Constant(binder.Name, typeof(string)),
                argNamedEnumerable));

            var call = Expression.Call(
                GetClayBehavior(),
                IClayBehavior_InvokeMember,
                missingLambda,
                GetLimitedSelf(),
                Expression.Constant(binder.Name, typeof(string)),
                argNamedEnumerable);

            var dynamicSuggestion = new DynamicMetaObject(
                call, BindingRestrictions.GetTypeRestriction(Expression, LimitType).Merge(binderDefault.Restrictions));

            return binder.FallbackInvokeMember(this, args, dynamicSuggestion);
        }```
I think it bombs out on the binder.FallBackInvokeMember. And that just invokes the expressiontree.

But nothing on [MSDN seems to suggest][3] this is not compatible with VB.Net or that they have to be carefull about something.

## Conclusion

For the moment I am at a lose if you know someone that can help then please do so.

 [1]: /index.php/DesktopDev/MSTech/using-clay-in-vb-net
 [2]: http://stackoverflow.com/questions/5922964/why-do-my-anonymous-types-not-work-in-clay-when-using-vb-net-but-do-work-in-c
 [3]: http://msdn.microsoft.com/en-us/library/dd466445.aspx