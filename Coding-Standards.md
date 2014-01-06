# **Language C++**
## **Indentation**
Use tabs over spaces/

## **Braces**
Braces should be on the same line as the the statement that starts it. E.g. a function, if else, switch, for loop, or class.

If..else statements need braces even if they are a 1 liner.

Use braces to create local scope where possible to reduce redefinition errors. 

Use the following as a guide for braces:

    switch () {
    case:
        
    break;
    default;
        
    }
    
    class CClass {    
    public:
    
    protected:
    
    private:
    };
    
    void function() {
    
    }

    if () {
    
    }
    else {
    
    }

    for ( ; ; ) {
    
    }
    
    struct SStruct {
    
    };
## **Namespace**
**DO NOT USE** "using namespace std;"! "using std::cout;" may be used for example, to qualify a part of the standard library in an effort to reduce verbosity.

Do place elements that are part of core Sigma inside the sigma namespace.

Namespaces should be one word and lowercase, with the exception of the core Sigma namespace.

## **Variables**
**DON'T USE GLOBALS!** If you have to, use uppercase letters with an _ to separate words. 
Local scope variables should have a short names as their use should be easy to discern. E.g. In a for loop using i, n, x etc 

Member variables should have a descriptive name that allows someone viewing the class header to discern its use. They shouldn't be decorated with m, m_ or other similar naming. Use lowercase with camel case to separate words.

## **Functions and Members**
Functions should do one logical task. Use an uppercase name with camel case  to separate words. Use an _ to separate groups of words. E.g. MeshGroup_ElementCount() or InitializeBuffers(). Try to keep function names descriptive but not verbose. 

Class methods should follow the guides for functions as well. When naming getters/setters exclude get/set as they are implied.

## **Classes and Structs**
Names should begin with a capital letter and be 1 or 2 words. Classes names should describe what the goal of the class is. E.g. Vertex would store data about a Vertex, and ResourceLoader loads resources. Struct names should convey what the data structure holds.

If a class is designed to work on a specific type of data structure (class or struct) try to make it a 2 word name that describes how it works on that class. E.g. ResourceLoader works on resource.
Prefix interfaces with I. And mark members as pure virtual. Think of interfaces as a contract.