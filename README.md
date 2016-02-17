<a href="http://www.boost.org/LICENSE_1_0.txt" target="_blank">![Boost Licence](http://img.shields.io/badge/license-boost-blue.svg)</a>
<a href="https://github.com/boost-experimental/di/releases" target="_blank">![Version](https://badge.fury.io/gh/boost-experimental%2Fdi.svg)</a>
<a href="https://travis-ci.org/boost-experimental/di" target="_blank">![Build Status](https://img.shields.io/travis/boost-experimental/di/cpp14.svg?label=linux/osx)</a>
<a href="https://ci.appveyor.com/project/boost-experimental/di" target="_blank">![Build Status](https://img.shields.io/appveyor/ci/boost-experimental/di/cpp14.svg?label=windows)</a>
<a href="https://coveralls.io/r/boost-experimental/di?branch=cpp14" target="_blank">![Coveralls](http://img.shields.io/coveralls/boost-experimental/di/cpp14.svg)</a>
<a href="http://github.com/boost-experimental/di/issues" target="_blank">![Github Issues](https://img.shields.io/github/issues/boost-experimental/di.svg)</a>

---------------------------------------

#Experimental Boost.DI

Your C++14 header only dependency injection library with no dependencies ([__Try it online!__](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/examples/index.html#hello-world))

```cpp
+---------------------------------------------------+
|$CXX -std=c++14 -fno-exceptions -O2 hello_world.cpp|
|#Compiles in 0.2s!                                 |
+---------------------------------------------------+
 
                                  #include <boost/di.hpp> +-----------+
 +-----------------------------+                                      |
 |                             |  namespace di = boost::di;     +-----+--------------------------------+
 |                             +-+                              |One header (3k lines, no dependencies)|
 |  +-----------------------+    +struct uniform {              +--------------------------------------+
 |  |Automatic conversion   |       bool &b;
 |  |between std::shared_ptr+------+boost::shared_ptr<interface> sp;
 |  |and boost::shared_ptr  |     };
 |  +-----------------------+  
 |                             +-+class direct {
 |                          +--+   public:                                 +----------------------------+
 |                          |       direct(const uniform &uniform          |ASM x86-64 == `make_unique` |
 |               +----------+            , std::shared_ptr<interface> sp)  +----------------------------+
 |               |                    : uniform_(uniform)                  |push   %rax                 |
 |               |                  , sp_(sp)                              |mov    $0x8,%edi            |
 |               |                  {}                                     |callq  0x4007b0 <_Znwm@plt> |
 |               |                                                         |movq   $0x400a10,(%rax)     |
 |               |                  const uniform &uniform_;               |mov    $0x8,%esi            |
 | +-------------+----------+       std::shared_ptr<interface> sp_;        |mov    %rax,%rdi            |
 | |using T{...} or T(...)  |     };                                       |callq  0x400960 <_ZdlPvm>   |
 +-+without REFLECTION or   |                                +-------------+mov    $0x1,%eax            |
   |any registration/changes+---+ class hello_world {        |             |pop    %rdx                 |
   |in the code!            |      public:                   +             |retq                        |
   +------------------------+       hello_world(std::unique_ptr<direct> d  +-------------------------+--+
                                   +--------+ , interface &ref                                       |
                                   |          , int i)+-------------------------------------------+  +-+
                                   |  : i_(i) {                                                   |    |
                                   |  assert(false == d->uniform_.b);                             |    |
                     +-------------+  assert(d->sp_.get() == d->uniform_.sp.get());               |    |
                     |                assert(&ref == d->sp_.get());     +                         |    |
    +----------------+---------+    }                         +         |                         |    |
    |Deduce scope based on     |                              |         |                         |    |
    |constructor parameter type|    auto run() const {        +---------+ +--------------------+  |    |
    |T -> unique               |      return i_ == 42;                  +-+The same shared_ptr,|  |    |
    |T& -> singleton           |    }                                     |reference provided  |  |    |
    |shared_ptr -> singleton   |                                          +--------------------+  |    |
    |unique_ptr |> unique      |   private:                                                       |    |
    +--------------------------+    int i_ = 0;                                                +--+    |
                                  };                                                           |       |
                                                                                               |       |
                                  int main() {                          +----------------------+--+    |
                                    auto runtime_value = false;         |ASM x86-64 == 'return 42'|    |
                                                                        +-------------------------+    |
                    +-------------+ auto module = [&] {                 |mov $0x2a,%eax           |    |
            +-------+-----------+     return di::make_injector(         |retq                     |    |
            |Split configuration|       di::bind<>().to(runtime_value)  +----+--------------------+    |
            |into modules       |     );                                     |                         |
            +-------+-----------+   };                                       |                         |
                    |         +----------------------------------------------+                         |
                    |         |     auto injector = di::make_injector(                                 |
                    |         |       di::bind<interface>().to<implementation>()+----------------------+
                    |         +---+ , di::bind<>().to(42)
                    +--------------+, module()                                     +---------------------+
                                    );                                  +----------+Compile time creation|
                                                                        +          |guarantee!           |
                                    auto object = injector.create<hello_world>();  +---------------------+
                                    assert(object.run());  +
                                  }                        |
                                                           |  +----------------------------------------+
                                                           +--+Short compile time error messages!      |
                                                              |For example:                            |
                                                              |`abstract_type<interface>::is_not_bound`|
                                                              +----------------------------------------+
```

---------------------------------------

[](GENERATE_TOC_BEGIN)

* [Introduction](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/index.html)
    * [Do I need Dependency Injection?](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/index.html#do-i-need-dependency-injection)
    * [Real Life examples?](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/index.html#real-life-examples)
    * [Why Dependency Injection?](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/index.html#why-dependency-injection)
    * [Why Boost.DI?](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/index.html#why-boostdi)
    * [Acknowledgements](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/index.html#acknowledgements)
* [Overview](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/overview/index.html)
    * [Quick Start](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/overview/index.html#quick-start)
    * [Dependencies](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/overview/index.html#dependencies)
    * [Supported/tested compilers](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/overview/index.html#supportedtested-compilers)
    * [Configuration](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/overview/index.html#configuration)
    * [Other Libraries](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/overview/index.html#other-libraries)
    * [Performance](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/overview/index.html#performance)
    * [Error messages](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/overview/index.html#error-messages)
* [Tutorial](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/tutorial/index.html)
    * [1. [Basic] Create objects tree](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/tutorial/index.html#1-basic-create-objects-tree)
    * [2. [Basic] First steps with bindings](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/tutorial/index.html#2-basic-first-steps-with-bindings)
    * [3. [Basic] Decide the life-times](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/tutorial/index.html#3-basic-decide-the-life-times)
    * [4. [Basic] Annotations to the rescue](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/tutorial/index.html#4-basic-annotations-to-the-rescue)
    * [5. [Basic] Split your configuration](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/tutorial/index.html#5-basic-split-your-configuration)
    * [6. [Advanced] Dump/Limit your types](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/tutorial/index.html#6-advanced-dumplimit-your-types)
    * [7. [Advanced] Customize it](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/tutorial/index.html#7-advanced-customize-it)
    * [8. [Advanced] Extend it](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/tutorial/index.html#8-advanced-extend-it)
* [Cheatsheet](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/cheatsheet/index.html)
* [Quick User Guide](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/quick_user_guide/index.html)
* [User Guide](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/user_guide/index.html)
    * [Injector](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/user_guide/index.html#injector)
    * [Bindings](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/user_guide/index.html#bindings)
    * [Injections](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/user_guide/index.html#injections)
    * [Annotations](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/user_guide/index.html#annotations)
    * [Scopes](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/user_guide/index.html#scopes)
    * [Modules](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/user_guide/index.html#modules)
    * [Providers](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/user_guide/index.html#providers)
    * [Policies](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/user_guide/index.html#policies)
    * [Concepts](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/user_guide/index.html#concepts)
    * [Configuration](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/user_guide/index.html#configuration)
* [Examples](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/examples/index.html)
    * [Hello World](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/examples/index.html#hello-world)
    * [Bindings](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/examples/index.html#bindings)
    * [Dynamic Bindings](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/examples/index.html#dynamic-bindings)
    * [Forward Bindings](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/examples/index.html#forward-bindings)
    * [Multiple Bindings](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/examples/index.html#multiple-bindings)
    * [Binding Non-owning Pointer](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/examples/index.html#binding-non-owning-pointer)
    * [Automatic Injection](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/examples/index.html#automatic-injection)
    * [Constructor Signature](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/examples/index.html#constructor-signature)
    * [Constructor Injection](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/examples/index.html#constructor-injection)
    * [Multiple Interface](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/examples/index.html#multiple-interface)
    * [Annotations](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/examples/index.html#annotations)
    * [Deduce Scope](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/examples/index.html#deduce-scope)
    * [Custom Scope](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/examples/index.html#custom-scope)
    * [Eager Singletons](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/examples/index.html#eager-singletons)
    * [Modules](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/examples/index.html#modules)
    * [Modules (hpp/cpp)](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/examples/index.html#modules-hppcpp)
    * [Custom Policy](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/examples/index.html#custom-policy)
    * [Custom Provider](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/examples/index.html#custom-provider)
    * [Pool Provider](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/examples/index.html#pool-provider)
    * [Configuration](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/examples/index.html#configuration)
* [Extensions](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/extensions/index.html)
    * [Constructor Bindings](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/extensions/index.html#constructor-bindings)
    * [Assisted Injection](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/extensions/index.html#assisted-injection)
    * [Concepts](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/extensions/index.html#concepts)
    * [Lazy](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/extensions/index.html#lazy)
    * [Named Parameters](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/extensions/index.html#named-parameters)
    * [XML Injection](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/extensions/index.html#xml-injection)
    * [Types Dumper](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/extensions/index.html#types-dumper)
    * [UML Dumper](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/extensions/index.html#uml-dumper)
    * [Mocks Provider](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/extensions/index.html#mocks-provider)
    * [Scoped Scope](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/extensions/index.html#scoped-scope)
    * [Session Scope](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/extensions/index.html#session-scope)
    * [Shared Scope](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/extensions/index.html#shared-scope)
* [Projects](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/projects/index.html)
    * [1. [Experimental Boost.MSM-lite](http://boost-experimental.github.io/msm-lite/)](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/projects/index.html#1-experimental-boostmsm-litehttpboost-experimentalgithubiomsm-lite)
    * [2. [Automatic Mocks Injector](https://github.com/krzysztof-jusiak/mocks_injector)](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/projects/index.html#2-automatic-mocks-injectorhttpsgithubcomkrzysztof-jusiakmocks_injector)
    * [3. [Match-3 Game](https://github.com/krzysztof-jusiak/match3)](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/projects/index.html#3-match-3-gamehttpsgithubcomkrzysztof-jusiakmatch3)
* [CHANGELOG](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/CHANGELOG/index.html)
    * [ [1.0.0] - TBD](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/CHANGELOG/index.html#-100-tbd)
    * [ [0.5.0] - 2015-01-12](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/CHANGELOG/index.html#-050-2015-01-12)
    * [ [0.1.0] - 2014-08-15](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/CHANGELOG/index.html#-010-2014-08-15)
* [TODO](http://boost-experimental.github.io/di/cpp14/boost/libs/di/doc/html/TODO/index.html)

[](GENERATE_TOC_END)

---

**Disclaimer** `Boost.DI` is not an official Boost library.
