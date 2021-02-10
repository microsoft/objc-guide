# Introduction

The Objective-C coding conventions guide documents many best practices learned from over a decade of experience developing software on Apple platforms at Microsoft, such as [Office for Mac and iOS](https://products.office.com/mac). This is not a style guide (use [clang-format](https://clang.llvm.org/docs/ClangFormat.html) for that), but guidelines that affect implementation.

This document is organized by language area. We have documented many conventions we regularly use by language area, which are accompanied with rationale, examples, and a history if applicable.

Not all teams use every convention documented here and others may be tweaked to be more amendable to team preferences. The intent is to facilitate developing maintainable and robust code. The conventions evolve with the Objective-C language, frameworks, and experience. Feedback and discussion is welcome and encouraged. For details on how to contribute, see the [contribution guidelines](CONTRIBUTING.md).

{% include search-lunr.html %}

# Table of Contents

* [Categories](Categories.md)
  * [Alternative Designs](Categories/AlternativeDesigns.md)
  * [Naming](Categories/Naming.md)
* [Classes](Classes.md)
  * [Naming](Classes/Naming.md)
* [Documentation](Documentation.md)
  * [Patterns](Documentation/Patterns.md)
* [Exceptions](Exceptions.md)
* [Headers](Headers.md)
  * [Factoring](Headers/Factoring.md)
  * [Import and Include](Headers/ImportAndInclude.md)
  * [Naming](Headers/Naming.md)
* [Instance Variables](InstanceVariables.md)
  * [Naming](InstanceVariables/Naming.md)
  * [Patterns](InstanceVariables/Patterns.md)
* [Methods and Implementations](MethodsAndImplementations.md)
  * [Automatic Reference Counting](MethodsAndImplementations/AutomaticReferenceCounting.md)
  * [Early Returns](MethodsAndImplementations/EarlyReturns.md)
  * [Implicit Boolean Conversion](MethodsAndImplementations/ImplicitBooleanConversion.md)
  * [Redundant Method Invocations](MethodsAndImplementations/RedundantMethodInvocations.md)
* [Object Creation](ObjectCreation.md)
* [Objective-C++](ObjectiveCpp.md)
* [PreprocessorDirectives.md](PreprocessorDirectives.md)
* [Properties](Properties.md)
* [Protocols](Protocols.md)
  * [Delegates and Data Sources](Protocols/DelegatesAndDataSources.md)
  * [Naming](Protocols/Naming.md)
  * [Patterns](Protocols/Patterns.md)
* [Syntax](Syntax.md)
  * [Dot Notation](Syntax/DotNotation.md)
  * [Generics](Syntax/Generics.md)
  * [Scope](Syntax/Scope.md)

# Legal Notices

Microsoft and any contributors grant you a license to the Microsoft documentation and other content
in this repository under the [Creative Commons Attribution 4.0 International Public License](https://creativecommons.org/licenses/by/4.0/legalcode),
see the [LICENSE](LICENSE) file, and grant you a license to any code in the repository under the [MIT License](https://opensource.org/licenses/MIT), see the
[LICENSE-CODE](LICENSE-CODE) file.

Microsoft, Windows, Microsoft Azure and/or other Microsoft products and services referenced in the documentation
may be either trademarks or registered trademarks of Microsoft in the United States and/or other countries.
The licenses for this project do not grant you rights to use any Microsoft names, logos, or trademarks.
Microsoft's general trademark guidelines can be found at http://go.microsoft.com/fwlink/?LinkID=254653.

Privacy information can be found at https://privacy.microsoft.com/en-us/

Microsoft and any contributors reserve all other rights, whether under their respective copyrights, patents,
or trademarks, whether by implication, estoppel or otherwise.
