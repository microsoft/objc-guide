## Convention
- Start the name with a lowercase letter and capitalize the first letter of embedded words. Don’t use prefixes. For names composed of multiple words, do not use punctuation marks as parts of names or as separators. See [Typographic Conventions](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingBasics.html#//apple_ref/doc/uid/20001281-1002931). There are two specific exceptions to these guidelines. You may begin a method name with a well-known acronym in uppercase (such as TIFF or PDF)), and you may use prefixes to group and identify private methods (see Private Methods).
- For methods that represent actions an object takes, start the name with a verb.
- If the method returns an attribute of the receiver, name the method after the attribute. The use of “get” is unnecessary, unless one or more values are returned indirectly.
- Use keywords before all arguments.
- Make the word before the argument describe the argument.
- Add new keywords to the end of an existing method when you create a method that is more specific than the inherited one.
- Don’t use “and” to link keywords that are attributes of the receiver
- If the method describes two separate actions, use “and” to link them.

For Apple guidelines, please refer [Naming Methods](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-BCIGIJJF)