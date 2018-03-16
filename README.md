# implementations
Java annotation processor to create default implementations for interfaces or abstract classes

## Motivation
Reduce boilerplate code (and modification effort) by automatically generating default implementations of interfaces or abstract classes with lots of methods (often required when using listener interfaces). Modern IDEs can generate these implementations, but it is still necessary to manage and, if required, to modify the occuring code files manually. Also, these files won't have any direct benefit beside saving boilerplate code at some other location, so why not just save that boilerplate code, too?

## Usage

Simply add the `Implementation` annotation on an interface or abstract class. It is possible to use the annotation multiple times on the same element. The annotation processor will create a new class for each provided annotation. Each new class will implement the annotated interface (or extend the annotated abstract class) by implementing each unimplemented method:

    @Implementation
    public interface Listener {
        boolean isActive();
        void onShow();
        void onHide();
    }
    
Generated code:

    public class DefaultListener implements Listener {
        public boolean isActive() {
            return false;
        }
        public void onShow() {
            return;
        }
        public void onHide{
            return;
        }
    }
    
The annotation provides the following parameters to configure the resulting implementation class (see below for details on their impact):

* **name** - a `String`, defaults to an empty string
* **nameFormat** - on from the `NameFormat` enum, defaults to `NameFormat.DEFAULT`
* **nameParam** - a `String`, defaults to an empty string
* **packageName** - a `String`, defaults to `"%s"`
* **allMethods** - one from the `Statement` enum, defaults to `Statement.RETURN`
* **voidMethods** - one from the `Statement` enum, defaults to `Statement.DEFAULT`
* **valueMethods** - one from the `Statement` enum, defaults to `Statement.DEFAULT`
* **objectMethods** - one from the `Statement` enum, defaults to `Statement.DEFAULT`

### Class name

The first three parameters **name**, **nameFormat** and **nameParam** form the name of the generated implementation class.
You can use **name** to directly specify a name:

    @Implementation(name="MyInterfaceImplementation")
    public interface MyInterface { }
    
However, you may want to generate the class name dynamically based on the name of the annotated interface or class. Just use the **nameFormat** parameter to choose how the name should be generated. For specific choices of the **nameFormat** parameter, the **nameParam** parameter may provide additional flexibility. The different choices for the **nameFormat** parameter (from the `NameFormat` enum) are explained below:

#### DEFAULT (the default option)

Adds the prefix `Default` to the name of the annotated element:

    @Implementation(nameFormat=NameFormat.DEFAULT) // or simply @Implementation
    public interface Listener { }

The implementation class will be named `DefaultListener`.

#### ADAPTER

Adds the suffix `Adapter` to the name of the annotated element:

    @Implementation(nameFormat=NameFormat.ADAPTER)
    public interface Listener { }

The implementation class will be named `ListenerAdapter`.

#### IMPL

Adds the suffix `Impl` to the name of the annotated element:

    @Implementation(nameFormat=NameFormat.IMPL)
    public interface Listener { }

The implementation class will be named `ListenerImpl`.

#### ABSTRACT

If the name of the annotated element starts with the prefix `Abstract`, the prefix will be removed from the name. Otherwise, the prefix `Abstract` will be added to the name of the annotated element:

    @Implementation(nameFormat=NameFormat.ABSTRACT)
    public interface Listener { }

The implementation class will be named `AbstractListener`.

    @Implementation(nameFormat=NameFormat.ABSTRACT)
    public interface AbstractListener { }

The implementation class will be named `Listener`.

#### ADD_PREFIX

Adds the **nameParam** parameter as prefix to the name of the annotated element:

    @Implementation(nameFormat=NameFormat.ADD_PREFIX, nameParam="My")
    public interface Listener { }

The implementation class will be named `MyListener`. If no **nameParam** parameter is provided, the prefix will default to `Default` (equal to the **DEFAULT** name format).

#### ADD_SUFFIX

Adds the **nameParam** parameter as prefix to the name of the annotated element:

    @Implementation(nameFormat=NameFormat.ADD_SUFFIX, nameParam="Class")
    public interface Listener { }

The implementation class will be named `ListenerClass`. If no **nameParam** parameter is provided, the suffix will default to `Adapter` (equal to the **ADAPTER** name format).

#### REMOVE_PREFIX

Removes the **nameParam** parameter as prefix from the name of the annotated element:

    @Implementation(nameFormat=NameFormat.REMOVE_PREFIX, nameParam="I")
    public interface IListener { }

The implementation class will be named `Listener`. If no **nameParam** parameter is provided, the prefix will default to `Abstract` (equal to the **ABSTRACT** name format, but removal only).

#### CUSTOM

Uses the **nameParam** parameter as format for the `String.format` method. The name of the annotated element will be passed as single argument:

    @Implementation(nameFormat=NameFormat.CUSTOM, nameParam="Default%sAdapter")
    public interface Listener { }
    
The implementation class will be named `DefaultListenerAdapter`. If no **nameParam** parameter is provided, the format will default to `Default%s` (equal to the **DEFAULT** name format).

## Package name

