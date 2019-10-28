### drools kmodule.xml 配置

kmodule.xml 需要放在工程的resouces/META-INF下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kmodule xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://www.drools.org/xsd/kmodule">
    <configuration>
    </configuration>
    <kbase name="KBase" default="true" eventProcessingMode="cloud" equalsBehavior="equality" declarativeAgenda="enabled" >
        <ksession name="KSession" type="stateful" default="true"/>
        <ksession name="KSession2_less" type="stateless" default="true"/>
    </kbase>
</kmodule>

```
kbase 的package 包名，需要与drl的存放的包路径一致，否则无法执行到其中的规则

### drools 初始化

```java
KieServices kieServices = KieServices.Factory.get();
kContainer = kieServices.getKieClasspathContainer();
// 根据xml配置的base名取得kieBase
kieBase = kContainer.getKieBase("KBase");

// 获取默认的session 其中在kmodule.xml中的ksession default配置
KieSession session = kieBase.newKieSession();
```

### drools drl 语法规则

要使用java工具类，需要将 __dialect  "java"__ 或者不设置，否则会报 __unable to resolve method using strict-mode__

#### extends
```drl
rule "parent"
    when
    then
end

rule "child" extends "parent"
    when
    then
end
```

#### 变量声明 patternBinding

只能在相应的patternType中进行声明其属性的变量，不能属性的属性的变量
特别是Map套Map的树状json数据结构，只能使用from 在外面去声明变量取值，
同样在paternBinding的也不能使用java工具类取值进行变量声明赋值

![Image of Drools Conditinal element](https://docs.jboss.org/drools/release/6.5.0.Final/drools-docs/html_single/images/LanguageReference/pattern.png)

```java
class A {
    String a;
}
class B {
    A a;
    String b;
}
```

```drl
rule "binding"
    when
        B(
            // 可以声明b
            $b := b,
            //但声明不了a
            $a := b.a
        )
        // 需要另起一个patternBinding
        A(
            $a := a
        ) from $b.a
    then
        System.out.println();
end
```

#### drl map 取值示例

```drl
rule "mapEg"
    when 
        Map(
            $a : this["a"]
        ) 
    then
        System.out.println($a)
end
```

#### drl collect 示例

```drl
rule "collectEg"
    when 
        Map(
            $list : this["list"]
        ) 
        $aList : ArrayList(size > 0) from collect ( Map( this["b"] == "b") from $list)
    then
        System.out.println($aList)
end
```

#### drl then 可调用的api

* The call __drools.halt()__ terminates rule execution immediately. This is required for returning control to the point whence the current session was put to work with __fireUntilHalt()__.
* Methods __insert(Object o), update(Object o) and delete(Object o)__ can be called on drools as well, but due to their frequent use they can be called without the object reference.
* __drools.getWorkingMemory()__ returns the WorkingMemory object.
* __drools.setFocus( String s)__ sets the focus to the specified agenda group.
* __drools.getRule().getName()__, called from a rule's RHS, returns the name of the rule.
* __drools.getTuple()__ returns the Tuple that matches the currently executing rule, and drools.getActivation() delivers the corresponding Activation. (These calls are useful for logging and debugging purposes.)